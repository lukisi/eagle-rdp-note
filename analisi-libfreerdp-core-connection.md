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
