---
title: Configurare un ambiente di sviluppo Windows per i microservizi di Azure | Microsoft Docs
description: Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Windows.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2018
ms.author: ryanwi, mikhegn
ms.openlocfilehash: 8e0898cf8046443728f92a8e05f17e51221fe60a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparare l'ambiente di sviluppo in Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Per compilare ed eseguire [applicazioni di Azure Service Fabric][1] nel computer di sviluppo Windows, installare il runtime di Service Fabric, l'SDK e gli strumenti. È anche necessario [abilitare l'esecuzione di script Windows PowerShell](#enable-powershell-script-execution) inclusi nell'SDK.

## <a name="prerequisites"></a>prerequisiti
### <a name="supported-operating-system-versions"></a>Versioni del sistema operativo supportate
Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Windows 7
* Windows 8 e Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Supporto per Windows 7:
> - Per impostazione predefinita, Windows 7 include solo Windows PowerShell 2.0. I cmdlet di PowerShell per Service Fabric richiedono PowerShell 3.0 o versione successiva. È possibile [scaricare Windows PowerShell 5.0][powershell5-download] dall'Area download Microsoft.
> - Il proxy inverso di Service Fabric non è disponibile in Windows 7.
>

## <a name="install-the-sdk-and-tools"></a>Installare l'SDK e gli strumenti
### <a name="to-use-visual-studio-2017"></a>Per usare Visual Studio 2017
Gli strumenti di Service Fabric fanno parte del carico di lavoro di sviluppo di Azure in Visual Studio 2017. Abilitare questo carico di lavoro durante l'installazione di Visual Studio.
È anche necessario installare Microsoft Azure Service Fabric SDK e il runtime usando Installazione guidata piattaforma Web.

* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Per usare Visual Studio 2015 (è necessario Visual Studio 2015 Update 2 o versioni successive)
Per Visual Studio 2015, gli strumenti di Service Fabric vengono installati con l'SDKe il runtime usando Installazione guidata piattaforma Web:

* [Installare Microsoft Azure Service Fabric SDK e gli strumenti][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Installazione solo dell'SDK
Se è necessario solo l'SDK, è possibile installare questo pacchetto:
* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

Le versioni correnti sono:
* SDK e strumenti di Service Fabric 3.0.467
* Runtime di Service Fabric 6.1.467
* Strumenti di Service Fabric per Visual Studio 2015 2.0.10124.2
* Visual Studio 2017 15.5.6 include Strumenti di Service Fabric per Visual Studio 2.0.20180124.2  

Per un elenco delle versioni supportate, vedere [Service Fabric support](service-fabric-support.md) (Supporto di Service Fabric)

## <a name="enable-powershell-script-execution"></a>Consentire l'esecuzione di script di PowerShell
Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows blocca l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato l'ambiente di sviluppo, iniziare a compilare ed eseguire le app.

* [Creare la prima applicazione Infrastruttura di servizi in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Introduzione alla distribuzione e all'aggiornamento di applicazioni nel cluster locale](service-fabric-get-started-with-a-local-cluster.md)
* [Prepare a Linux development environment on Windows (Preparare un ambiente di sviluppo Linux in Windows)](service-fabric-local-linux-cluster-windows.md)
* [Informazioni sui modelli di programmazione Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Vedere gli esempi di codice di Service Fabric in GitHub](https://aka.ms/servicefabricsamples)
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Seguire il percorso di apprendimento di Service Fabric per un'ampia Introduzione alla piattaforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md)
* [Automatizzare l'applicazione di patch al sistema operativo nel cluster](service-fabric-patch-orchestration-application.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Pagina della campagna di Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Collegamento WebPI VS 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Collegamento WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Collegamento WebPI Core SDK"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
