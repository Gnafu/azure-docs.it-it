---
title: Come creare e distribuire un servizio Cloud | Documentazione Microsoft
description: Informazioni su come creare e distribuire un servizio cloud mediante il portale di Azure.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: gwallace
ms.openlocfilehash: 3d5b3f291eb42edc1f7999f33cf6c0879c33bcf4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359122"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Come creare e distribuire un servizio Cloud
Il portale di Azure offre due modi per creare e distribuire un servizio cloud: *Creazione rapida* e *creazione personalizzata*.

In questo argomento viene descritto come usare il metodo di creazione rapida di un nuovo servizio cloud e come caricare e distribuire un pacchetto del servizio cloud in Azure tramite l'opzione **Carica** . Quando si usa questo metodo, il portale di Azure rende disponibili comodi collegamenti per completare tutti i requisiti man mano che si procede. Se si è pronti per distribuire il servizio cloud durante la creazione, è possibile effettuare contemporaneamente entrambe le operazioni usando Creazione personalizzata.

> [!NOTE]
> Se si prevede di pubblicare il servizio cloud da Azure DevOps, usare Creazione rapida e quindi impostare la pubblicazione di Azure DevOps dall'avvio rapido di Azure o dal dashboard. Per altre informazioni, vedere [recapito continuo in Azure tramite Azure DevOps][TFSTutorialForCloudService]o vedere la guida per la pagina **avvio rapido** .
>
>

## <a name="concepts"></a>Concetti
Per distribuire un'applicazione come servizio cloud in Azure, sono necessari tre componenti:

* **Definizione del servizio**  
  Il file di definizione del servizio cloud (con estensione csdef) definisce il modello di servizio, compreso il numero di ruoli.
* **Configurazione del servizio**  
  l file di configurazione del servizio cloud (con estensione cscfg) specifica le impostazioni di configurazione per il servizio cloud e i singoli ruoli, incluso il numero di istanze del ruolo.
* **Pacchetto del servizio**  
  Il pacchetto del servizio (con estensione cspkg) contiene il codice dell'applicazione, le configurazioni e il file di definizione del servizio.

Per altre informazioni in proposito e su come creare un pacchetto, fare clic [qui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparare l'app
Per poter distribuire un servizio cloud, è necessario creare il pacchetto di servizio cloud (.cspkg) dal codice dell'applicazione e un file di configurazione del servizio cloud (.cscfg). Azure SDK offre strumenti per la preparazione dei file necessari alla distribuzione. È possibile installare l'SDK dalla pagina di [download](https://azure.microsoft.com/downloads/) di Azure, nel linguaggio in cui si preferisce sviluppare il codice dell'applicazione.

Per poter esportare un pacchetto di servizio, è necessario configurare tre funzionalità del servizio cloud:

* Se si vuole distribuire un servizio cloud che usa SSL (Secure Sockets Layer) per la crittografia dei dati, [configurare l'applicazione](cloud-services-configure-ssl-certificate-portal.md#modify) per SSL.
* Se si vogliono configurare connessioni Desktop remoto a istanze del ruolo, [configurare i ruoli](cloud-services-role-enable-remote-desktop-new-portal.md) per Desktop remoto.
* Se si desidera configurare il monitoraggio dettagliato per il servizio cloud, abilitare la Diagnostica Azure per il servizio cloud. *Monitoraggio minimo* (livello di monitoraggio predefinito) ricorre a contatori delle prestazioni raccolti dai sistemi operativi host per istanze del ruolo (macchine virtuali). *Il* monitoraggio dettagliato raccoglie metriche supplementari in base ai dati delle prestazioni all'interno delle istanze del ruolo per consentire un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per scoprire come abilitare la Diagnostica Azure, vedere [Abilitazione della diagnostica in Azure](cloud-services-dotnet-diagnostics.md).

Per creare un servizio cloud con le distribuzioni dei ruoli Web o dei ruoli di lavoro, è necessario [creare il pacchetto del servizio](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Prima di iniziare
* Se Azure SDK non è stato installato, fare clic su **Install Azure SDK** per aprire la pagina di [download](https://azure.microsoft.com/downloads/)di Azure e quindi scaricare l'SDK nel linguaggio in cui si preferisce sviluppare il codice. (È possibile eseguire questa operazione in seguito).
* Se un'istanza del ruolo lo richiede, creare i certificati. I servizi cloud richiedono un file con estensione pfx con una chiave privata. È possibile caricare i certificati in Azure nel corso della creazione e della distribuzione del servizio cloud.

## <a name="create-and-deploy"></a>Creazione e distribuzione
1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Fare clic su **Crea una risorsa > Calcolo**, quindi scorrere verso il basso e fare clic su **Servizio cloud**.

    ![Pubblica il servizio cloud](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Nel nuovo riquadro **Servizio cloud** immettere un valore per il **nome DNS**.
4. Creare un nuovo **Gruppo di risorse** o selezionarne uno esistente.
5. Selezionare un **percorso**.
6. Fare clic su **Pacchetto**. Verrà visualizzato il riquadro **Carica un pacchetto**. Compilare i campi obbligatori. Se sono presenti ruoli contenenti una singola istanza, assicurarsi che l'opzione **Distribuisci anche se uno o più ruoli contengono una singola istanza** sia selezionata.
7. Assicurarsi che l'opzione **Avvia distribuzione** sia selezionata.
8. Fare clic su **OK** per chiudere il riquadro **Carica un pacchetto**.
9. Se non è disponibile un certificato da aggiungere, fare clic su **Crea**.

    ![Pubblica il servizio cloud](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Caricamento di un certificato
Se il pacchetto di distribuzione è stato [configurato per usare i certificati](cloud-services-configure-ssl-certificate-portal.md#modify), a questo punto è possibile caricare il certificato.

1. Selezionare **Certificati** e nel riquadro **Aggiungi certificati** selezionare il file PFX del certificato SSL e fornire la **password** per il certificato.
2. Fare clic su **Collega certificato** e quindi su **OK** nel riquadro **Aggiungi certificati**.
3. Fare clic su **Crea** nel riquadro **Servizio cloud**. Quando la distribuzione ha raggiunto lo stato **Ready** , è possibile procedere con i passaggi successivi.

    ![Pubblica il servizio cloud](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verificare che la distribuzione sia stata completata correttamente
1. Fare clic sull'istanza del servizio cloud.

    Lo stato del servizio dovrebbe ora essere **In esecuzione**.
2. In **Informazioni di base** fare clic su **URL sito** per aprire il servizio cloud in un Web browser.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passaggi successivi
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure-portal.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name-portal.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage-portal.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate-portal.md).
