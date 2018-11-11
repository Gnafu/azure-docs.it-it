---
title: Configurare le impostazioni dell'app per le funzioni di Azure | Microsoft Docs
description: Informazioni su come configurare le impostazioni dell'app per le funzioni di Azure.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 5e34aae62f871cba2a27a292242cff8e90c67934
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228981"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Come gestire un'app per le funzioni nel portale di Azure 

In Funzioni di Azure un'app per le funzioni fornisce il contesto di esecuzione per le singole funzioni. I comportamenti dell'app per le funzioni si applicano a tutte le funzioni ospitate da un'app per le funzioni specifica. In questo argomento viene descritto come configurare e gestire le app per le funzioni nel portale di Azure.

Innanzitutto passare al [portale di Azure](http://portal.azure.com) e accedere all'account di Azure. Nella barra di ricerca nella parte superiore del portale digitare il nome dell'app per le funzioni e selezionarla dall'elenco. Dopo aver selezionato l'app per le funzioni, viene visualizzata la pagina seguente:

![Panoramica dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funzioni preferite nel portale 

In alcuni casi può essere difficile trovare le risorse nel [portale di Azure]. Per rendere più semplice trovare le app per le funzioni create, aggiungere le app per le funzioni ai Preferiti nel portale. 

1. Accedere al [Portale di Azure].

2. Fare clic sulla freccia in basso a sinistra per espandere tutti i servizi, digitare `Functions` nel campo **Filtro** e quindi fare clic sulla stella accanto a **App per le funzioni**.  
 
    ![Creare un'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    L'icona Funzioni viene aggiunta al menu disponibile nella parte sinistra del portale.

3. Chiudere il menu e scorrere verso il basso fino a trovare l'icona Funzioni. Fare clic sull'icona per visualizzare l'elenco di tutte le app per le funzioni. Fare clic sull'app per le funzioni per eseguire operazioni sulle funzioni all'interno dell'app. 
 
    ![App per le funzioni nei Preferiti](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Portale di Azure]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Scheda delle impostazioni dell'app per le funzioni

![Panoramica dell'app per le funzioni nel portale di Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Nella scheda **Impostazioni** è possibile aggiornare la versione di runtime di Funzioni usata dall'app per le funzioni. È anche possibile gestire le chiavi host usate per limitare l'accesso HTTP a tutte le funzioni ospitate dall'app per le funzioni.

Funzioni supporta sia i piani di hosting a consumo che i piani di hosting del servizio app. Per altre informazioni vedere [Scegliere il piano di servizio corretto per Funzioni di Azure](functions-scale.md). Per una migliore prevedibilità nel piano di consumo, Funzioni consente di limitare l'uso della piattaforma impostando una quota di uso giornaliera, in secondi di gigabyte. Quando la quota di uso giornaliera viene raggiunta, l'app per le funzioni viene arrestata. In questo caso può essere riattivata dallo stesso contesto dell'impostazione della quota di spesa giornaliera. Vedere la [pagina prezzi di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/) per altre informazioni sulla fatturazione.   

## <a name="platform-features-tab"></a>Scheda delle funzionalità della piattaforma

![Scheda delle funzionalità della piattaforma per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Le app per le funzioni vengono eseguite e gestite dalla piattaforma Servizio app di Azure. Di conseguenza, le app per le funzioni hanno accesso alla maggior parte delle funzionalità di piattaforma di hosting Web di base di Azure. Nella scheda **Funzionalità della piattaforma** è possibile accedere a molte funzionalità della piattaforma del servizio app che è possibile usare nelle app per le funzioni. 

> [!NOTE]
> Non tutte le funzionalità del servizio app sono disponibili quando un'app per le funzioni viene eseguita nel piano di hosting a consumo.

La restante parte di questo argomento illustra le seguenti funzionalità di servizio app nel portale di Azure utili per Funzioni:

+ [Editor del servizio app](#editor)
+ [Impostazioni dell'applicazione](#settings) 
+ [Console](#console)
+ [Strumenti avanzati (Kudu)](#kudu)
+ [Opzioni di distribuzione](#deployment)
+ [CORS](#cors)
+ [autenticazione](#auth)
+ [Definizione dell'API](#swagger)

Per altre informazioni su come usare le impostazioni del servizio app, vedere [Configurare le impostazioni di del servizio app di Azure](../app-service/web-sites-configure.md).

### <a name="editor"></a>Editor del servizio app

| | |
|-|-|
| ![Editor del servizio app per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | L'editor del servizio app è un editor avanzato, disponibile nel portale, che si può usare per modificare i file di configurazione JSON e i file del codice nello stesso modo. Quando si sceglie questa opzione, viene aperta una scheda separata del browser con un editor di base. Ciò consente di realizzare l'integrazione con l'archivio Git, eseguire il codice e il relativo debug e modificare le impostazioni dell'app per le funzioni. Questo editor offre un ambiente di sviluppo migliorato per le funzioni confrontato con il pannello dell'app per le funzioni predefinito.    |

![Editor del servizio app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Impostazioni dell'applicazione

| | |
|-|-|
| ![Impostazioni delle applicazioni per l'app per le funzioni.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | Nel pannello **Impostazioni applicazione** del servizio app è possibile configurare e gestire le versioni di framework, il debug remoto, le impostazioni dell'app e le stringhe di connessione. Quando l'app per le funzioni si integra con altri servizi di terze parti e di Azure, qui è possibile modificare tali impostazioni. Per eliminare un'impostazione, scorrere verso destra e quindi selezionare l'icona a forma di **X** all'estremità destra della riga (non visualizzata nella figura seguente).

![Configurare le impostazioni dell'applicazione](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Console dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | La console nel portale è uno strumento ideale per gli sviluppatori quando si desidera interagire con l'app per le funzioni dalla riga di comando. I comandi comuni includono la creazione e lo spostamento di file e directory, nonché l'esecuzione di script e file batch. |

![Console dell'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Strumenti avanzati (Kudu)

| | |
|-|-|
| ![App per le funzioni Kudu nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | Gli strumenti avanzati per il servizio app, noto anche come Kudu, consentono l'accesso alle funzionalità amministrative avanzate dell'app per le funzioni. Dalla Kudu è possibile gestire informazioni di sistema, impostazioni dell'app, variabili di ambiente, estensioni del sito, intestazioni HTTP e variabili del server. È anche possibile avviare **Kudu** selezionando l'endpoint SCM per l'app per le funzioni, ad esempio`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurare Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opzioni di distribuzione

| | |
|-|-|
| ![Opzioni di distribuzione dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funzioni consente di sviluppare il codice della funzione sul computer locale. È quindi possibile caricare il progetto dell'app per le funzioni locale in Azure. Oltre al caricamento FTP tradizionale, Funzioni consente di distribuire app per le funzioni usando le soluzioni di integrazione continua più diffuse, come GitHub, Azure DevOps, Dropbox, Bitbucket e altre. Per altre informazioni, vedere [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md). Per caricare manualmente tramite FTP o Git locale, è necessario anche [configurare le credenziali di distribuzione](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS di app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Per impedire l'esecuzione di codici dannosi nei servizi, il servizio app consente di bloccare le chiamate alle app per le funzioni da origini esterne. Funzioni supporta la condivisione di risorse tra le origini, CORS per consentire la definizione di un "elenco" di origini consentite da cui le funzioni possono accettare le richieste remote.  |

![Configurare CORS per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticazione

| | |
|-|-|
| ![Autenticazione dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando le funzioni usano un trigger HTTP, è possibile richiedere innanzitutto l'autenticazione delle chiamate. Servizio app supporta l'autenticazione di Azure Active Directory e consente di accedere ai provider social, quali Facebook, Microsoft e Twitter. Per informazioni dettagliate sulla configurazione di specifici provider di autenticazione, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md). |

![Configurare l'autenticazione per un'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definizione dell'API

| | |
|-|-|
| ![Definizione dello swagger API dell'app per le funzioni nel portale di Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funzioni supporta Swagger per consentire ai client di usare più facilmente le funzioni attivate da HTTP. Per altre informazioni sulla creazione di definizioni di API con Swagger, vedere [Ospitare un'API RESTful con CORS in Servizio app di Azure](../app-service/app-service-web-tutorial-rest-api.md). È anche possibile usare Proxy di Funzioni per definire una singola superficie API per le funzioni multiple. Per altre informazioni, vedere [Uso di proxy in Funzioni di Azure](functions-proxies.md). |

![Configurare l'API per l'app per le funzioni](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passaggi successivi

+ [Configurare le impostazioni del servizio app di Azure](../app-service/web-sites-configure.md)
+ [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md)



