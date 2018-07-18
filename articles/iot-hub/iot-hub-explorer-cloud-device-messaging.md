---
title: Gestire la messaggistica dei dispositivi cloud nell'hub IoT di Azure con iothub-explorer | Microsoft Docs
description: Informazioni su come usare lo strumento iothub-explorer dell'interfaccia della riga di comando per monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo nell'hub IoT di Azure.
author: rangv
manager: ''
keywords: iothub explorer, messaggistica dei dispositivi cloud, hub iot da cloud a dispositivo, messaggistica da cloud a dispositivo
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: cd8456efb4d8e0b84695f97d379a1ea83e19aba2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634234"
---
# <a name="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Usare iothub-explorer per inviare e ricevere messaggi tra il dispositivo e l'hub IoT

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) include una gamma di comandi che semplificano la gestione dell'hub IoT. Questa esercitazione approfondisce l'uso di iothub-explorer per inviare e ricevere messaggi tra il dispositivo e l'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Concetti legati all'esercitazione

Informazioni su come usare iothub-explorer per monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo. I messaggi da dispositivo a cloud possono includere dati di sensori raccolti dal dispositivo e inviati all'hub IoT. I messaggi da cloud a dispositivo possono includere comandi inviati dall'hub IoT al dispositivo per far lampeggiare un LED connesso a quest'ultimo.

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione

- Usare iothub-explorer per monitorare i messaggi da dispositivo a cloud.
- Usare iothub-explorer per inviare messaggi da cloud a dispositivo.

## <a name="what-you-need"></a>Elementi necessari

- Completare l'esercitazione [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) che prevede i requisiti seguenti:
  - Una sottoscrizione di Azure attiva.
  - Un hub IoT di Azure nella sottoscrizione.
  - Un'applicazione client che invia messaggi ad Azure IoT hub.
- iothub-explorer. ([Installare iothub-explorer](https://github.com/azure/iothub-explorer))

## <a name="monitor-device-to-cloud-messages"></a>Monitorare i messaggi da dispositivo a cloud

Per monitorare i messaggi inviati dal dispositivo all'hub IoT, seguire questi passaggi:

1. Aprire una finestra della console.
1. Eseguire il comando seguente:

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Ottenere `<device-id>` e `<IoTHubConnectionString>` dall'hub IoT. Assicurarsi di aver terminato l'esercitazione precedente. In alternativa, è possibile provare a usare `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` se sono presenti `HostName`, `SharedAccessKeyName` e `SharedAccessKey`.

## <a name="send-cloud-to-device-messages"></a>Inviare messaggi da cloud a dispositivo

Per inviare un messaggio dall'hub IoT al dispositivo, seguire questi passaggi:

1. Aprire una finestra della console.
1. Avviare una sessione nell'hub IoT eseguendo il comando seguente:

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Inviare un messaggio al dispositivo eseguendo il comando seguente:

   ```bash
   iothub-explorer send <device-id> <message>
   ```

Il comando fa lampeggiare il LED di connessione al dispositivo e invia il messaggio a quest'ultimo.

> [!Note]
> Non è necessario che il dispositivo invii un comando ack separato in risposta all'hub IoT una volta ricevuto il messaggio.

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
