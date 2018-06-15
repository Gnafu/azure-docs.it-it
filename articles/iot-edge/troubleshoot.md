---
title: Risoluzione dei problemi di Azure IoT Edge | Microsoft Docs
description: Risolvere problemi comuni e acquisire competenze di risoluzione dei problemi per Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad22b0cd1457c1d4146a75047ff18e916c0c7ccd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633537"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nel proprio ambiente, usare questo articolo come guida per la risoluzione. 

## <a name="standard-diagnostic-steps"></a>Procedura di diagnostica standard 

Quando si verifica un problema, è possibile ottenere informazioni sullo stato del dispositivo IoT Edge esaminando i registri del contenitore e i messaggi in transito da e verso il dispositivo. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni. 

* Esaminare i log dei contenitori Docker per rilevare i problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime di IoT Edge: agente di Edge e hub di Edge. I log dell'agente di Edge forniscono generalmente informazioni sul ciclo di vita di ogni contenitore. I log dell'hub di Edge forniscono informazioni su messaggistica e routing. 

   ```cmd
   docker logs <container name>
   ```

* Visualizzare i messaggi che transitano nell'hub di Edge e raccogliere informazioni dettagliate sugli aggiornamenti delle proprietà dei dispositivi con log dettagliati dei contenitori di runtime.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* Visualizzare i log dettagliati dei comandi iotedgectl:

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* Se si verificano problemi di connettività, esaminare le variabili di ambiente del dispositivo Edge come la stringa di connessione del dispositivo:

   ```cmd
   docker exec edgeAgent printenv
   ```

È anche possibile verificare i messaggi inviati tra l'hub IoT e i dispositivi IoT Edge. Visualizzare questi messaggi tramite l'estensione [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) per Visual Studio Code. Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

Dopo avere esaminato i registri e i messaggi per raccogliere informazioni, è anche possibile provare a riavviare il runtime di Azure IoT Edge:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>L'agente Edge si arresta dopo circa un minuto

L'agente Edge viene avviato ed eseguito correttamente per circa un minuto, quindi si arresta. I log indicano che l'agente di Edge sta provando a connettersi all'hub IoT tramite AMQP e quindi tramite AMQP su WebSocket dopo circa 30 secondi. Se l'operazione non riesce, l'agente si chiude. 

Log di esempio dell'agente di Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa radice
Una configurazione nella rete host impedisce all'agente Edge di raggiungere la rete. L'agente prova prima a connettersi tramite AMQP (porta 5671). Se l'operazione non riesce, prova con WebSocket (porta 443).

Il runtime di IoT Edge configura una rete per ognuno dei moduli usati per la comunicazione. In Linux questa rete è una rete di bridge. In Windows si usa la rete NAT. Questo problema è più comune nei dispositivi Windows con contenitori Windows che usano la rete NAT. 

### <a name="resolution"></a>Risoluzione
Verificare che sia presente un instradamento a Internet per gli indirizzi IP assegnati a questa rete di bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete di IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>L'hub di Edge non si avvia

L'hub di Edge non si avvia e stampa il messaggio seguente nei log: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa radice
Un altro processo nel computer host è associato alla porta 443. L'hub di Edge esegue il mapping delle porte 5671 e 443 per l'uso negli scenari di gateway. Questo mapping delle porte non riesce se un altro processo è già associato a questa porta. 

### <a name="resolution"></a>Risoluzione
Individuare e arrestare il processo che usa la porta 443. Questo processo è in genere un server Web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>L'agente di Edge non può accedere all'immagine di un modulo (403)
Un contenitore non viene eseguito correttamente e i log dell'agente di Edge indicano un errore 403. 

### <a name="root-cause"></a>Causa radice
L'agente di Edge non è autorizzato ad accedere all'immagine del modulo. 

### <a name="resolution"></a>Risoluzione
Provare a eseguire di nuovo il comando `iotedgectl login`.

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl non riesce a trovare Docker

I comandi `iotedgectl setup` o `iotedgectl start` hanno esito negativo e nei log viene stampato il messaggio seguente:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Causa radice
iotedgectl non riesce a trovare Docker, che è un prerequisito.

### <a name="resolution"></a>Risoluzione
Installare Docker, verificare che sia in esecuzione e riprovare.

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>La configurazione di iotedgectl ha esito negativo con un nome host non valido

Il comando `iotedgectl setup` ha esito negativo e viene stampato il messaggio seguente: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Causa radice
Il runtime di IoT Edge può supportare solo nomi host contenenti meno di 64 caratteri. In genere non è un problema per i computer fisici, ma può esserlo quando si configura il runtime in una macchina virtuale. I nomi host generati automaticamente per le macchine virtuali Windows ospitate in Azure, in particolare, tendono a essere lunghi. 

### <a name="resolution"></a>Risoluzione
Quando viene visualizzato questo errore, è possibile risolvere il problema configurando il nome DNS della macchina virtuale e quindi impostando il nome DNS come nome host nel comando di configurazione.

1. Nel portale di Azure passare alla pagina di panoramica della macchina virtuale. 
2. Selezionare **Configura** sotto Nome DNS. Se la macchina virtuale dispone già di un nome DNS configurato, non è necessario configurarne uno nuovo. 

   ![Configurare il nome DNS](./media/troubleshoot/configure-dns.png)

3. Specificare un valore per **Etichetta del nome DNS** e selezionare **Salva**.
4. Copiare il nuovo nome DNS, che deve essere nel formato  **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. All'interno della macchina virtuale, usare il comando seguente per configurare il runtime di IoT Edge con il nome DNS:

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>Passaggi successivi
Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [inviare il problema](https://github.com/Azure/iot-edge/issues) in modo da poter migliorare l'esperienza. 
