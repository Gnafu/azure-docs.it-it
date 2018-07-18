Il tipo di servizio e di sottoscrizione determina il numero di query che è possibile effettuare al secondo (QPS). È consigliabile verificare che l'applicazione includa la logica necessaria per rimanere entro la quota. Se si supera il valore QPS, la richiesta ha esito negativo con codice di stato HTTP 429. La risposta include anche l'intestazione Retry-After, che contiene il numero di secondi di attesa previsti prima dell'invio di un'altra richiesta.  
  
### <a name="denial-of-service-dos-versus-throttling"></a>Denial of Service (DoS) e limitazione

Il servizio distingue un attacco DoS da una violazione del valore QPS. Se il servizio sospetta un attacco Denial of Service, la richiesta ha esito positivo (con codice di stato HTTP 200 OK), ma il corpo della risposta è vuoto.