---
title: Creare un'app Web ASP.NET Core in Azure | Microsoft Docs
description: Informazioni su come eseguire app Web nel servizio app di Azure distribuendo l'app Web ASP.NET predefinita.
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/14/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 698f23507da0707a4612f8d33fe7e2995429f361
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Creare un'app Web ASP.NET Core in Azure

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Windows. Per la distribuzione nel servizio app in _Linux_, vedere [Creare un'app Web .NET Core nel servizio app in Linux](./containers/quickstart-dotnetcore.md).
>

Le [app Web di Azure](app-service-web-overview.md) forniscono un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione.  Questa guida introduttiva illustra come distribuire la prima app Web ASP.NET Core in un'app Web di Azure. Al termine della procedura si avrà un gruppo di risorse costituito da un piano di servizio App e da un'app Web di Azure con un'applicazione Web distribuita.

> [!NOTE]
> Per informazioni su come compilare e distribuire un'app Web con il framework ASP.NET, leggere l'articolo disponibile [qui](app-service-web-get-started-dotnet-framework.md). 
>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

* Installare <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> con i carichi di lavoro seguenti:
    - **Sviluppo Web e ASP.NET**
    - **Sviluppo di Azure**

    ![Sviluppo Web e ASP.NET e sviluppo di Azure (in Web e Cloud)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

## <a name="create-an-aspnet-core-web-app"></a>Creare un'app Web ASP.NET Core

In Visual Studio creare un progetto selezionando **File > Nuovo > Progetto**. 

Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Web > Applicazione Web ASP.NET Core**.

Assegnare all'applicazione il nome _myFirstAzureWebApp_ e fare clic su **OK**.
   
![Finestra di dialogo Nuovo progetto](./media/app-service-web-get-started-dotnet/new-project.png)

È possibile distribuire qualsiasi tipo di app Web ASP.NET Core in Azure. Per questa guida introduttiva, selezionare il modello **Applicazione Web** e verificare che l'autenticazione sia impostata su **Nessuna autenticazione**.
      
Selezionare **OK**.

![Finestra di dialogo Nuovo progetto ASP.NET](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Dopo che è stato creato il progetto ASP.NET Core, verrà visualizzata la home page di ASP.NET Core con vari collegamenti alle risorse con informazioni utili per iniziare. 

![Home page](./media/app-service-web-get-started-dotnet/aspnet-core-welcome-page.png)

Nel menu selezionare **Debug > Avvia senza eseguire debug** per eseguire l'app Web in locale.

![Eseguire l'app in locale](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-to-azure"></a>Pubblicazione in Azure

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** e scegliere **Pubblica**.

![Pubblicare da Esplora soluzioni](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Verificare che **Servizio app di Microsoft Azure** sia selezionato e scegliere **Pubblica**.

![Pubblicare dalla pagina di panoramica progetto](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Verrà visualizzata la finestra di dialogo **Crea servizio app**, che consente di creare tutte le risorse di Azure necessarie per eseguire l'app Web ASP.NET Core in Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Nella finestra di dialogo **Crea servizio app** fare clic su **Aggiungi un account** e accedere alla sottoscrizione di Azure. Se è già stato eseguito l'accesso, selezionare l'account contenente la sottoscrizione desiderata dall'elenco a discesa.

> [!NOTE]
> Se si è già connessi, non selezionare ancora l'opzione **Crea**.
>
>
   
![Accedere ad Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Accanto a **Gruppo di risorse** selezionare **Nuovo**.

Assegnare al gruppo di risorse il nome **myResourceGroup** e selezionare **OK**.

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Accanto a **Piano di servizio app** selezionare **Nuovo**. 

Nella finestra di dialogo **Configura piano di servizio app** usare le impostazioni della tabella riportata sotto l'immagine.

![Creare un piano di servizio app](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Impostazione | Valore consigliato | DESCRIZIONE |
|-|-|-|
|Piano di servizio app| myAppServicePlan | Nome del piano di servizio app. |
| Località | Europa occidentale | Data center in cui è ospitata l'app Web. |
| Dimensione | Gratuito | [Piano tariffario](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) che determina le funzionalità di hosting. |

Selezionare **OK**.

## <a name="create-and-publish-the-web-app"></a>Creare e pubblicare l'app Web

In **Nome app Web** immettere un nome univoco dell'app, usando i caratteri validi `a-z`, `0-9` e `-`, o accettare il nome univoco generato automaticamente. L'URL dell'app Web è `http://<app_name>.azurewebsites.net`, dove `<app_name>` è il nome dell'app Web.

Selezionare **Crea** per avviare la creazione delle risorse di Azure.

![Configurare il nome dell'app Web](./media/app-service-web-get-started-dotnet/web-app-name.png)

Al termine della procedura guidata, l'app Web ASP.NET Core viene pubblicata in Azure e avviata nel browser predefinito.

![App Web ASP.NET pubblicata in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Il nome dell'app Web specificato nel passaggio relativo alla [creazione e pubblicazione](#create-and-publish-the-web-app) viene usato come prefisso dell'URL nel formato `http://<app_name>.azurewebsites.net`.

L'app Web ASP.NET Core è ora in esecuzione nel servizio app di Azure.

## <a name="update-the-app-and-redeploy"></a>Aggiornare e ridistribuire l'app

Da **Esplora soluzioni** aprire _Pages/Index.cshtml_.

Trovare il tag HTML `<div id="myCarousel" class="carousel slide" data-ride="carousel" data-interval="6000">` in alto e sostituire l'intero elemento con il codice seguente:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Per la ridistribuzione in Azure, fare clic con il pulsante destro del mouse sul progetto **myFirstAzureWebApp** in **Esplora soluzioni** e selezionare **Pubblica**.

Nella pagina di pubblicazione selezionare **Pubblica**.

Al termine del processo di pubblicazione, Visual Studio avvia un browser sull'URL dell'app Web.

![App Web ASP.NET aggiornata in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per visualizzare l'app Web.

Scegliere **Servizi app** dal menu a sinistra e quindi selezionare il nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-get-started-dotnet/access-portal.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app. 

![Pannello del servizio app nel portale di Azure](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [ASP.NET Core con database SQL](app-service-web-tutorial-dotnetcore-sqldb.md)
