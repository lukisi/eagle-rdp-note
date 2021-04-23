# File `libfreerdp/core/connection.c`

In questo file il commento all'inizio fa riferimento alla
*RDP Connection Sequence* descritta nella sezione
[1.3.1.1](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/023f1e69-cfe8-4ee6-9ee0-7e759fb4e4ee)
delle specifiche del protocollo "`[MS-RDPBCGR]`".

La funzione `rdp_client_connect`
viene chiamata verso l'inizio dell'esecuzione del programma.

Verso la fine di questa funzione abbiamo questo stralcio di codice C.

```c
	/* everything beyond this point is event-driven and non blocking */
	rdp->transport->ReceiveCallback = rdp_recv_callback;
	rdp->transport->ReceiveExtra = rdp;
	transport_set_blocking_mode(rdp->transport, FALSE);

	if (rdp->state != CONNECTION_STATE_NLA)
	{
		if (!mcs_client_begin(rdp->mcs))
			return FALSE;
	}

	for (timeout = 0; timeout < settings->TcpAckTimeout; timeout += 100)
	{
		if (rdp_check_fds(rdp) < 0)
		{
			freerdp_set_last_error_if_not(rdp->context, FREERDP_ERROR_CONNECT_TRANSPORT_FAILED);
			return FALSE;
		}

		if (rdp->state == CONNECTION_STATE_ACTIVE)
			return TRUE;

		Sleep(100);
	}

	WLog_ERR(TAG, "Timeout waiting for activation");
	return FALSE;
```

Il tipo del membro `rdp->transport` è `struct rdp_transport`:

```c
struct rdp_rdp
{
...
	rdpTransport* transport;
...
}

typedef struct rdp_transport rdpTransport;
```

La struttura `rdp_transport` nel file
`libfreerdp/core/transport.h`:

```c
struct rdp_transport
{
	TRANSPORT_LAYER layer;
	BIO* frontBio;
	rdpRdg* rdg;
	rdpTsg* tsg;
	rdpTls* tls;
	rdpContext* context;
	rdpNla* nla;
	rdpSettings* settings;
	void* ReceiveExtra;
	wStream* ReceiveBuffer;
	TransportRecv ReceiveCallback;
	wStreamPool* ReceivePool;
	HANDLE connectedEvent;
	BOOL NlaMode;
	BOOL blocking;
	BOOL GatewayEnabled;
	CRITICAL_SECTION ReadLock;
	CRITICAL_SECTION WriteLock;
	ULONG written;
	HANDLE rereadEvent;
	BOOL haveMoreBytesToRead;
	wLog* log;
	rdpTransportIo io;
};
```

Il prototipo della funzione `TransportRecv ReceiveCallback` che
compare in questa struttura è

```c
typedef int (*TransportRecv)(rdpTransport* transport, wStream* stream, void* extra);
```

Deduco che in un qualche evento (presumo la ricezione di dati dal
server, ma forse anche altri eventi)
viene chiamata la funzione `rdp_recv_callback` nel file
`libfreerdp/core/rdp.c`:

```c
int rdp_recv_callback(rdpTransport* transport, wStream* s, void* extra)
{
	int status = 0;
	rdpRdp* rdp = (rdpRdp*)extra;

	/*
	 * At any point in the connection sequence between when all
	 * MCS channels have been joined and when the RDP connection
	 * enters the active state, an auto-detect PDU can be received
	 * on the MCS message channel.
	 */
	if ((rdp->state > CONNECTION_STATE_MCS_CHANNEL_JOIN) && (rdp->state < CONNECTION_STATE_ACTIVE))
	{
		if (rdp_client_connect_auto_detect(rdp, s))
			return 0;
	}

	switch (rdp->state)
	{
		case CONNECTION_STATE_NLA:
			if (nla_get_state(rdp->nla) < NLA_STATE_AUTH_INFO)
			{
				if (nla_recv_pdu(rdp->nla, s) < 1)
				{
					WLog_ERR(TAG, "%s: %s - nla_recv_pdu() fail", __FUNCTION__,
					         rdp_server_connection_state_string(rdp->state));
					return -1;
				}
			}
			else if (nla_get_state(rdp->nla) == NLA_STATE_POST_NEGO)
			{
				nego_recv(rdp->transport, s, (void*)rdp->nego);

				if (nego_get_state(rdp->nego) != NEGO_STATE_FINAL)
				{
					WLog_ERR(TAG, "%s: %s - nego_recv() fail", __FUNCTION__,
					         rdp_server_connection_state_string(rdp->state));
					return -1;
				}

				if (!nla_set_state(rdp->nla, NLA_STATE_FINAL))
					return -1;
			}

			if (nla_get_state(rdp->nla) == NLA_STATE_AUTH_INFO)
			{
				transport_set_nla_mode(rdp->transport, FALSE);

				if (rdp->settings->VmConnectMode)
				{
					if (!nego_set_state(rdp->nego, NEGO_STATE_NLA))
						return -1;

					if (!nego_set_requested_protocols(rdp->nego, PROTOCOL_HYBRID | PROTOCOL_SSL))
						return -1;

					nego_send_negotiation_request(rdp->nego);

					if (!nla_set_state(rdp->nla, NLA_STATE_POST_NEGO))
						return -1;
				}
				else
				{
					if (!nla_set_state(rdp->nla, NLA_STATE_FINAL))
						return -1;
				}
			}

			if (nla_get_state(rdp->nla) == NLA_STATE_FINAL)
			{
				nla_free(rdp->nla);
				rdp->nla = NULL;

				if (!mcs_client_begin(rdp->mcs))
				{
					WLog_ERR(TAG, "%s: %s - mcs_client_begin() fail", __FUNCTION__,
					         rdp_server_connection_state_string(rdp->state));
					return -1;
				}
			}

			break;

		case CONNECTION_STATE_MCS_CONNECT:
			if (!mcs_recv_connect_response(rdp->mcs, s))
			{
				WLog_ERR(TAG, "mcs_recv_connect_response failure");
				return -1;
			}

			if (!mcs_send_erect_domain_request(rdp->mcs))
			{
				WLog_ERR(TAG, "mcs_send_erect_domain_request failure");
				return -1;
			}

			if (!mcs_send_attach_user_request(rdp->mcs))
			{
				WLog_ERR(TAG, "mcs_send_attach_user_request failure");
				return -1;
			}

			rdp_client_transition_to_state(rdp, CONNECTION_STATE_MCS_ATTACH_USER);
			break;

		case CONNECTION_STATE_MCS_ATTACH_USER:
			if (!mcs_recv_attach_user_confirm(rdp->mcs, s))
			{
				WLog_ERR(TAG, "mcs_recv_attach_user_confirm failure");
				return -1;
			}

			if (!mcs_send_channel_join_request(rdp->mcs, rdp->mcs->userId))
			{
				WLog_ERR(TAG, "mcs_send_channel_join_request failure");
				return -1;
			}

			rdp_client_transition_to_state(rdp, CONNECTION_STATE_MCS_CHANNEL_JOIN);
			break;

		case CONNECTION_STATE_MCS_CHANNEL_JOIN:
			if (!rdp_client_connect_mcs_channel_join_confirm(rdp, s))
			{
				WLog_ERR(TAG,
				         "%s: %s - "
				         "rdp_client_connect_mcs_channel_join_confirm() fail",
				         __FUNCTION__, rdp_server_connection_state_string(rdp->state));
				status = -1;
			}

			break;

		case CONNECTION_STATE_LICENSING:
			status = rdp_client_connect_license(rdp, s);

			if (status < 0)
				WLog_DBG(TAG, "%s: %s - rdp_client_connect_license() - %i", __FUNCTION__,
				         rdp_server_connection_state_string(rdp->state), status);

			break;

		case CONNECTION_STATE_CAPABILITIES_EXCHANGE:
			status = rdp_client_connect_demand_active(rdp, s);

			if (status < 0)
				WLog_DBG(TAG,
				         "%s: %s - "
				         "rdp_client_connect_demand_active() - %i",
				         __FUNCTION__, rdp_server_connection_state_string(rdp->state), status);

			break;

		case CONNECTION_STATE_FINALIZATION:
			status = rdp_recv_pdu(rdp, s);

			if ((status >= 0) && (rdp->finalize_sc_pdus == FINALIZE_SC_COMPLETE))
			{
				rdp_client_transition_to_state(rdp, CONNECTION_STATE_ACTIVE);
				return 2;
			}

			if (status < 0)
				WLog_DBG(TAG, "%s: %s - rdp_recv_pdu() - %i", __FUNCTION__,
				         rdp_server_connection_state_string(rdp->state), status);

			break;

		case CONNECTION_STATE_ACTIVE:
			status = rdp_recv_pdu(rdp, s);

			if (status < 0)
				WLog_DBG(TAG, "%s: %s - rdp_recv_pdu() - %i", __FUNCTION__,
				         rdp_server_connection_state_string(rdp->state), status);

			break;

		default:
			WLog_ERR(TAG, "%s: %s state %d", __FUNCTION__,
			         rdp_server_connection_state_string(rdp->state), rdp->state);
			status = -1;
			break;
	}

	return status;
}
```

Risulta utile alla comprensione la lista dei valori di queste *enum*
riportate nel file `libfreerdp/core/connection.h`:

```c
enum CONNECTION_STATE
{
	CONNECTION_STATE_INITIAL,
	CONNECTION_STATE_NEGO,
	CONNECTION_STATE_NLA,
	CONNECTION_STATE_MCS_CONNECT,
	CONNECTION_STATE_MCS_ERECT_DOMAIN,
	CONNECTION_STATE_MCS_ATTACH_USER,
	CONNECTION_STATE_MCS_CHANNEL_JOIN,
	CONNECTION_STATE_RDP_SECURITY_COMMENCEMENT,
	CONNECTION_STATE_SECURE_SETTINGS_EXCHANGE,
	CONNECTION_STATE_CONNECT_TIME_AUTO_DETECT,
	CONNECTION_STATE_LICENSING,
	CONNECTION_STATE_MULTITRANSPORT_BOOTSTRAPPING,
	CONNECTION_STATE_CAPABILITIES_EXCHANGE,
	CONNECTION_STATE_FINALIZATION,
	CONNECTION_STATE_ACTIVE
};

enum CLIENT_CONNECTION_STATE
{
	CLIENT_STATE_INITIAL,
	CLIENT_STATE_PRECONNECT_PASSED,
	CLIENT_STATE_POSTCONNECT_PASSED
};
```

