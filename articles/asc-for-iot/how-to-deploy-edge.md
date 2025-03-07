---
title: Distribuire il Centro sicurezza di Azure per il modulo IoT Edge (anteprima) | Microsoft Docs
description: Informazioni su come distribuire un centro sicurezza di Azure per l'agente di sicurezza Internet in IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4e568d2322088d9f6f6b4f9ad6e4b3cd98f25a47
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376052"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Distribuire un modulo di sicurezza nel dispositivo IoT Edge

> [!IMPORTANT]
> Il Centro sicurezza di Azure per il supporto dei dispositivi IoT Edge è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il modulo **Centro sicurezza di Azure** offre una soluzione di sicurezza completa per i dispositivi IOT Edge.
Il modulo Security raccoglie, aggrega e analizza i dati di sicurezza non elaborati dal sistema operativo e dal sistema del contenitore in consigli e avvisi di sicurezza di utilità pratica.
Per altre informazioni, vedere [modulo di sicurezza per IOT Edge](security-edge-architecture.md).

In questo articolo si apprenderà come distribuire un modulo di sicurezza sul dispositivo IoT Edge.

## <a name="deploy-security-module"></a>Distribuisci modulo di sicurezza

Usare la procedura seguente per distribuire un centro sicurezza di Azure per il modulo di sicurezza Internet per IoT Edge.

### <a name="prerequisites"></a>Prerequisiti

- Nell'hub Internet delle cose assicurarsi che il dispositivo sia [registrato come dispositivo IOT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Il Centro sicurezza di Azure per il modulo IoT Edge richiede l'installazione del [Framework controllato](https://linux.die.net/man/8/auditd) sul dispositivo IOT Edge.

    - Installare il Framework eseguendo il comando seguente sul dispositivo IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Verificare che il controllo sia attivo eseguendo il comando seguente:
   
      `sudo systemctl status auditd`
      
        La risposta prevista è `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Distribuzione con portale di Azure

1. Dal portale di Azure aprire **Marketplace**.

1. Selezionare **Internet delle cose**, quindi cercare il **Centro sicurezza di Azure** e selezionarlo.

   ![Selezionare il Centro sicurezza di Azure per le cose](media/howto/edge-onboarding-8.png)

1. Fare clic su **Crea** per configurare la distribuzione. 

1. Scegliere la **sottoscrizione** di Azure dell'hub Internet delle cose, quindi selezionare l' **Hub**Internet.<br>Selezionare **Distribuisci in un dispositivo** per fare riferimento a un singolo dispositivo o selezionare **Distribuisci su scala** per fare riferimento a più dispositivi, quindi fare clic su **Crea**. Per ulteriori informazioni sulla distribuzione su larga scala, vedere [How to deploy](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Se è stata selezionata l'opzione **Distribuisci su scala**, aggiungere il nome e i dettagli del dispositivo prima di continuare con la scheda **Aggiungi moduli** nelle istruzioni seguenti.     

Sono disponibili tre passaggi per creare una distribuzione IoT Edge per il Centro sicurezza di Azure. illustrati nelle sezioni seguenti. 

#### <a name="step-1-add-modules"></a>Passaggio 1: Aggiungi moduli

1. Dalla scheda **Aggiungi moduli** , area **moduli di distribuzione** , fare clic su **AzureSecurityCenterforIoT**. 
   
1. Modificare il **nome** in **azureiotsecurity**.
1. Modificare l' **URI dell'immagine** in **MCR.Microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Verificare che il valore delle **Opzioni di creazione del contenitore** sia impostato su:      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. Verificare che sia selezionata l'opzione **imposta le proprietà desiderate del modulo gemello** e modificare l'oggetto di configurazione in:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Fare clic su **Save**.
1. Scorrere fino alla fine della scheda e selezionare **Configura Advanced Edge Runtime Settings**.
   
   
1. Modificare l' **immagine** in **Hub Edge** in **MCR.Microsoft.com/ascforiot/edgehub:1.0.9-Preview**.

   >[!Note]
   > Il Centro sicurezza di Azure per il modulo Internet richiede una versione biforcata dell'hub IoT Edge, basata sulla versione 1,20 dell'SDK.
   > Modificando IoT Edge immagine dell'hub, si indica al dispositivo IoT Edge di sostituire la versione stabile più recente con la versione con fork di IoT Edge Hub, che non è ufficialmente supportata dal servizio IoT Edge.

1. Verificare che **Crea opzioni** sia impostato su: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Fare clic su **Save**.
   
1. Fare clic su **Avanti**.

#### <a name="step-2-specify-routes"></a>Passaggio 2: Specifica route 

1. Nella scheda **specifica route** assicurarsi di disporre di una route (esplicita o implicita) che inoltra i messaggi dal modulo **azureiotsecurity** a **$upstream**. 
1. Fare clic su **Avanti**.

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

#### <a name="step-3-review-deployment"></a>Passaggio 3: Rivedi distribuzione

- Nella scheda **Verifica distribuzione** esaminare le informazioni di distribuzione e quindi selezionare **Invia** per completare la distribuzione.

## <a name="diagnostic-steps"></a>Procedure di diagnostica

Se si verifica un problema, i log del contenitore rappresentano il modo migliore per ottenere informazioni sullo stato di un dispositivo IoT Edge Security Module. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Verificare che i contenitori necessari siano installati e funzionino come previsto

1. Eseguire il comando seguente sul dispositivo IoT Edge:
    
     `sudo docker ps`
   
1. Verificare che siano in esecuzione i seguenti contenitori:
   
   | Name | IMMAGINE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Se non sono presenti i contenitori minimi necessari, controllare se il manifesto di distribuzione IoT Edge è allineato con le impostazioni consigliate. Per altre informazioni, vedere [Deploy IOT Edge Module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Esaminare i registri dei moduli per individuare eventuali errori
   
1. Eseguire il comando seguente sul dispositivo IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Per log più dettagliati, aggiungere la variabile di ambiente seguente alla distribuzione del modulo azureiotsecurity `logLevel=Debug`:.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, passare alla Guida alle procedure per la configurazione del modulo. 
> [!div class="nextstepaction"]
> [Guida alle procedure di configurazione del modulo](./how-to-agent-configuration.md)
