---
title: Abilitare la sincronizzazione offline per un'app UWP (Universal Windows Platform) con App per dispositivi mobili| Documentazione Microsoft
description: Informazioni su come usare un'app per dispositivi mobili di Azure per memorizzare nella cache e sincronizzare i dati offline nell'app UWP (Universal Windows Platform).
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 4970a80b911a1efbc308d48ac4b8a50f774b4d04
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67551921"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Abilitare la sincronizzazione offline per l'app di Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center sta investendo nei servizi nuovi e integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud. Estrai [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-offline-data) oggi stesso.
>

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere il supporto offline a un'app UWP (Universal Windows Platform) tramite un back-end di App per dispositivi mobili di Azure. La sincronizzazione offline consente agli utenti finali di interagire con un'app per dispositivi mobili, visualizzando, aggiungendo e modificando i dati, anche se non è disponibile una connessione di rete. Le modifiche vengono archiviate in un database locale. Quando il dispositivo torna online, vengono sincronizzate con il back-end remoto.

In questa esercitazione verrà aggiornato il progetto di app UWP creato nell'esercitazione [Creare un'app Windows] in modo da supportare le funzionalità offline di App per dispositivi mobili di Azure. Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere al progetto il pacchetto di estensione per l'accesso ai dati. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all' [utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Per altre informazioni sulla funzionalità di sincronizzazione offline, vedere l'argomento [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="requirements"></a>Requisiti  
Per questa esercitazione sono necessari i prerequisiti seguenti:

* Visual Studio 2013 in esecuzione in Windows 8.1 o versioni successive
* Completamento dell'esercitazione [Creare un'app Windows][Creare un'app Windows].
* [Archivio SQLite di servizi mobili di Azure][sqlite store nuget]
* [SQLite for Universal Windows Platform](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Aggiornare l'app client per supportare le funzionalità offline
Le funzionalità offline delle app per dispositivi mobili di Azure consentono di interagire con un database locale in uno scenario offline. Per usare queste funzionalità nell'app, inizializzare un'interfaccia [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] . SQLite viene usato come archivio locale nel dispositivo.

1. Installare il [runtime di SQLite per la piattaforma UWP (Universal Windows Platform)](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. In Visual Studio aprire Gestione pacchetti NuGet per il progetto di app UWP completato nell'esercitazione [Creare un'app Windows].
    Cercare e installare il pacchetto NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. In Esplora soluzioni fare doppio clic su **Riferimenti** > **Aggiungi riferimento...** >**Universal Windows**>**Estensioni** e quindi abilitare sia **SQLite for Universal Windows Platform** che **Visual C++ 2015 Runtime for Universal Windows Platform apps**.

    ![Aggiungere un riferimento UWP SQLite][1]
4. Aprire il file MainPage.xaml.cs e rimuovere i simboli di commendo dalla definizione `#define OFFLINE_SYNC_ENABLED`.
5. In Visual Studio premere **F5** per ricompilare ed eseguire l'app client. L'app funziona esattamente come prima di attivare la sincronizzazione offline. Tuttavia, il database locale è ora popolato con dati che possono essere usati in uno scenario offline.

## <a name="update-sync"></a>Aggiornare l'app per disconnetterla dal back-end
In questa sezione verrà interrotta la connessione al back-end dell'app per dispositivi mobili per simulare una situazione offline. Quando si aggiungono elementi di dati, il gestore di eccezioni informa che l'app è in modalità offline. In questo stato, nuovi elementi verranno aggiunti all'archivio locale che verranno sincronizzati con il back-end dell'app per dispositivi mobili la prima volta che verrà eseguita un'operazione di push in presenza di uno stato di connessione.

1. Modificare il file App.xaml.cs nel progetto condiviso. Impostare come commento l'inizializzazione di **MobileServiceClient** e aggiungere la riga seguente, che usa un URL dell'app per dispositivi mobili non valido:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    È anche possibile illustrare il comportamento offline disabilitando le reti Wi-Fi e cellulare sul dispositivo o impostando la modalità aereo.
2. Premere **F5** per compilare ed eseguire l'app. Si noti l'errore di sincronizzazione al momento dell'aggiornamento quando l'app viene avviata.
3. Immettere nuovi elementi. Si noti che il push ha esito negativo con stato [CancelledByNetworkError] ogni volta che si fa clic su **Salva**. I nuovi elementi todo, tuttavia, sono presenti nell'archivio locale fino a quando non è possibile effettuarne il push al back-end dell'app per dispositivi mobili.  In un'app di produzione, se si eliminano queste eccezioni, l'app client si comporta come se fosse ancora connessa al back-end dell'app per dispositivi mobili.
4. Chiudere l'app e riavviarla per verificare che i nuovi elementi creati siano salvati in modo permanente nell'archivio locale.
5. (Facoltativo) In Visual Studio aprire **Esplora server**. Passare al database in **Azure**->**Database SQL**. Fare clic con il pulsante destro del mouse sul database e scegliere **Apri in Esplora oggetti di SQL Server**. È ora possibile passare alla tabella di database SQL e al relativo contenuto. Verificare che i dati nel database back-end non siano stati modificati.
6. (Facoltativo) Usare uno strumento REST come Fiddler o Postman per eseguire una query sul back-end mobile, usando una query GET nel formato `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Aggiornare l'app per la riconnessione al back-end dell'app per dispositivi mobili
In questa sezione verrà effettuata la riconnessione dell'app al back-end dell'app per dispositivi mobili. Queste modifiche simulano una riconnessione alla rete nell'app.

Alla prima esecuzione dell'applicazione, il gestore eventi `OnNavigatedTo` chiama `InitLocalStoreAsync`. Questo metodo a sua volta chiama `SyncAsync` per sincronizzare l'archivio locale con il database back-end. L'app prova a eseguire la sincronizzazione all'avvio.

1. Aprire App.xaml.cs nel progetto condiviso e rimuovere il commento dall'inizializzazione precedente di `MobileServiceClient` per l'URL corretto dell'app per dispositivi mobili.
2. Premere **F5** per ricompilare ed eseguire l'app. L'app sincronizza le modifiche locali con il back-end dell'app per dispositivi mobili di Azure usando operazioni push e pull quando viene eseguito il gestore eventi `OnNavigatedTo`.
3. (Facoltativo) Visualizzare i dati aggiornati usando Esplora oggetti di SQL Server o uno strumento REST come Fiddler. Si noti che i dati sono stati sincronizzati tra il database back-end dell'app per dispositivi mobili di Azure e l'archivio locale.
4. Nell'app fare clic sulla casella di controllo accanto ad alcuni elementi da completare nell'archivio locale.

   `UpdateCheckedTodoItem` chiama `SyncAsync` per sincronizzare ogni elemento completato con il back-end dell'app per dispositivi mobili. `SyncAsync` chiama operazioni sia push che pull. Tuttavia, **ogni volta che si effettua il pull su una tabella a cui il client ha apportato modifiche, viene sempre effettuato automaticamente il push**. Questo comportamento assicura che tutte le tabelle nell'archivio locale e le relazioni restino coerenti e può determinare un'operazione push non prevista.  Per altre informazioni su questo comportamento, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure].

## <a name="api-summary"></a>Riepilogo dell'API
Per supportare le funzionalità offline di servizi mobili, è stata usata l'interfaccia [IMobileServiceSyncTable] e è stato inizializzato [MobileServiceClient. SyncContext][synccontext] con un database SQLite locale. In una situazione offline le normali operazioni CRUD per App per dispositivi mobili funzionano come se l'app fosse ancora connessa, mentre tutte le operazioni interessano l'archivio locale. Per sincronizzare l'archivio locale con il server vengono usati i metodi seguenti:

* **[PushAsync]** . Dato che questo metodo è membro di [IMobileServicesSyncContext], viene effettuato il push al back-end delle modifiche in tutte le tabelle. Solo i record con modifiche locali vengono inviati al server.
* **[PullAsync]** . Viene avviato un pull da [IMobileServiceSyncTable]. Se nella tabella sono presenti modifiche di cui si è tenuta traccia, viene eseguito un push implicito per assicurarsi che tutte le tabelle nell'archivio locale e le relazioni corrispondenti siano ancora coerenti. Il parametro *pushOtherTables* determina se viene effettuato il push implicito di altre tabelle nel contesto. Il parametro di *query* accetta [una\<stringa imobileservicetablequery T >][IMobileServiceTableQuery] o una stringa di query OData per filtrare i dati restituiti. Il parametro *queryId* viene usato per definire la sincronizzazione incrementale. Per altre informazioni, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** . L'app deve chiamare periodicamente questo metodo per ripulire l'archivio locale dai dati non aggiornati. Usare il parametro *force* quando è necessario ripulire tutte le modifiche non ancora sincronizzate.

Per altre informazioni su questi concetti, vedere [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Altre informazioni
Gli argomenti seguenti forniscono altre informazioni in background sulla funzionalità di sincronizzazione offline delle app per dispositivi mobili:

* [Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]
* [SDK di .NET per app per dispositivi mobili di Azure][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronizzazione di dati offline nelle app per dispositivi mobili di Azure]: app-service-mobile-offline-data-sync.md
[Creare un'app Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
