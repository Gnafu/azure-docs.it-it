---
title: Distribuire rapidamente un'app esistente in un cluster di Azure Service Fabric
description: Usare un cluster di Azure Service Fabric per ospitare un'applicazione Node.js esistente con Visual Studio.
services: service-fabric
documentationcenter: nodejs
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: atsenthi
ms.openlocfilehash: 6cf9594e6e1db3e163d25843b1fec0c0ff98c250
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592460"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Ospitare un'applicazione Node.js in Azure Service Fabric

Questa guida introduttiva illustra come distribuire un'applicazione esistente, in questo esempio Node.js, in un cluster di Service Fabric in esecuzione in Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo](service-fabric-get-started.md). Che include l'installazione di Service Fabric SDK e Visual Studio 2019 o 2015.

È anche necessaria un'applicazione Node.js esistente da distribuire. Questa Guida introduttiva usa un sito Web Node. js semplice che può essere scaricato [qui][download-sample]. Estrarre il file nella cartella `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` dopo aver creato il progetto nel passaggio successivo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][create-account].

## <a name="create-the-service"></a>Creare il servizio

Avviare Visual Studio come **amministratore**.

Creare un progetto con `CTRL`+`SHIFT`+`N`.

Nella finestra di dialogo **Nuovo progetto** scegliere **Cloud > Applicazione di Service Fabric**.

Assegnare all'applicazione il nome **MyGuestApp** e fare clic su **OK**.

>[!IMPORTANT]
>Node.js può facilmente superare il limite di 260 caratteri per i percorsi presente in Windows. Usare per il progetto un percorso breve, ad esempio **c:\code\svc1**. È anche possibile seguire **[queste istruzioni](https://stackoverflow.com/a/41687101/1664231)** per abilitare i percorsi di file lunghi in Windows 10.
   
![Finestra di dialogo Nuovo progetto in Visual Studio][new-project]

Nella finestra di dialogo successiva è possibile creare qualsiasi tipo di servizio di Service Fabric. Per questa guida introduttiva scegliere **Eseguibile guest**.

Assegnare al servizio il nome **MyGuestService** e impostare le opzioni a destra sui valori seguenti:

| Impostazione                   | Value |
| ------------------------- | ------ |
| Cartella del pacchetto di codice       | _&lt;cartella con l'app Node.js&gt;_ |
| Comportamento del pacchetto di codice     | Copia il contenuto della cartella nel progetto |
| Programma                   | node.exe |
| Argomenti                 | server.js |
| Cartella di lavoro            | CodePackage |

Premere **OK**.

![Finestra di dialogo Nuovo servizio in Visual Studio][new-service]

Visual Studio crea il progetto di applicazione e il progetto di servizio Actor e li visualizza in Esplora soluzioni.

Il progetto dell'applicazione (**MyGuestApp**) non contiene direttamente alcun codice. Il progetto fa riferimento a un set di progetti di servizio. Contiene anche altri tre tipi di contenuto:

* **Profili di pubblicazione**  
Preferenze relative agli strumenti per diversi ambienti.

* **Script**  
Script di PowerShell per distribuire o aggiornare l'applicazione.

* **Definizione di applicazione**  
Include il manifesto dell'applicazione in *ApplicationPackageRoot*. I file dei parametri dell'applicazione associati sono disponibili in *ApplicationParameters*, definiscono l'applicazione e consentono di configurarla appositamente per un ambiente specifico.
    
Per una panoramica del contenuto del progetto di servizio, vedere la [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="set-up-networking"></a>Configurare la rete

L'app Node.js di esempio che viene distribuita usa la porta **80** ed è necessario indicare a Service Fabric che tale porta deve essere esposta.

Aprire il file **ServiceManifest.xml** nel progetto. Nella parte inferiore del manifesto è presente una `<Resources> \ <Endpoints>` con una voce già definita. Modificare la voce aggiungendo `Port`, `Protocol` e `Type`. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Distribuire in Azure

Se si preme **F5** e si esegue il progetto, quest'operazione viene distribuita nel cluster locale. Si eseguirà invece la distribuzione in Azure.

Fare clic con il pulsante destro del mouse sul progetto e scegliere **Pubblica**. Verrà aperta una finestra di dialogo per la pubblicazione in Azure.

![Finestra di dialogo per la pubblicazione in Azure di un servizio di Service Fabric][publish]

Selezionare il profilo di destinazione **PublishProfiles\Cloud.xml**.

Se questa operazione non è stata eseguita in precedenza, scegliere un account Azure in cui effettuare la distribuzione. Se non ne è ancora stato [creato][create-account]uno, iscriversi.

In **Endpoint connessione** selezionare il cluster di Service Fabric in cui eseguire la distribuzione. Se non si dispone di un, selezionare **&lt;Crea nuovo cluster... verrà&gt;** visualizzata la finestra del Web browser per la portale di Azure. Per altre informazioni, vedere [Creare un cluster nel portale](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal). 

Quando si crea il cluster di Service Fabric, assicurarsi di impostare **Endpoint personalizzati** su **80**.

![Configurazione del tipo di nodo di Service Fabric con endpoint personalizzato][custom-endpoint]

Il completamento della creazione di un nuovo cluster di Service Fabric richiede tempo. Al termine, tornare alla finestra di dialogo di pubblicazione e selezionare **&lt;Aggiorna&gt;** . Il nuovo cluster sarà incluso nella casella di riepilogo a discesa. Selezionare il cluster.

Fare clic su **Pubblica** e attendere il completamento della distribuzione.

L'operazione potrebbe richiedere alcuni minuti. Al termine, potrebbero trascorrere ancora alcuni minuti prima che l'applicazione sia completamente disponibile.

## <a name="test-the-website"></a>Testare il sito Web

Dopo che è stato pubblicato, testare il servizio in un Web browser. 

Prima di tutto, aprire il portale di Azure e trovare il servizio di Service Fabric.

Controllare l'indirizzo del servizio nel pannello di panoramica. Usare il nome di dominio della proprietà _Endpoint di connessione client_. Ad esempio `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Pannello di panoramica di Service Fabric nel portale di Azure][overview]

Passare a questo indirizzo in cui verrà visualizzata la `HELLO WORLD` risposta.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Non dimenticare di eliminare tutte le risorse create per questa Guida introduttiva, perché vengono addebitate le risorse.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sugli [eseguibili guest](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
