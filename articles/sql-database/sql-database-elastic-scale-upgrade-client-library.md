---
title: Eseguire l'aggiornamento alla libreria client dei database elastici | Documentazione Microsoft
description: Usare NuGet per aggiornare la libreria client dei database elastici.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 286fa60fef8de5240fb2ccd0f14ced0c4e38ff73
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981362"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Aggiornare un'app in modo da usare la libreria client dei database elastici più recente

Le nuove versioni della [libreria client dei database elastici](sql-database-elastic-database-client-library.md) sono disponibili tramite NuGet e l'interfaccia di gestione pacchetti NuGet in Visual Studio. Gli aggiornamenti contengono correzioni di bug e supporto delle nuove funzionalità della libreria client.

**Per la versione più recente:** passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Ricompilare l'applicazione con la nuova libreria e modificare i metadati Shard Map Manager esistenti archiviati nei database SQL di Azure in modo che supportino le nuove funzionalità.

Seguendo l'ordine di questi passaggi è possibile essere sicuri che le versioni precedenti della libreria client non siano più presenti nell'ambiente quando gli oggetti dei metadati vengono aggiornati, e che pertanto dopo l'aggiornamento non saranno creati oggetti di metadati di versioni precedenti.

## <a name="upgrade-steps"></a>Passaggi dell'aggiornamento

**1. Aggiornare le applicazioni.** In Visual Studio, scaricare e fare riferimento alla versione più recente della libreria client in tutti i progetti di sviluppo che usano tale libreria; quindi ricompilare e distribuire.

* Nella soluzione Visual Studio in uso selezionare **Strumenti** --> **Gestione pacchetti NuGet** -->  **Gestisci pacchetti NuGet per la soluzione**.
* (Visual Studio 2013) Nel riquadro sinistro selezionare **Aggiornamenti**, quindi il pulsante **Aggiorna** del pacchetto **Libreria client di scalabilità elastica del database SQL di Azure** che viene visualizzato nella finestra.
* (2015 di Visual Studio) Impostare la casella del filtro su **aggiornamento disponibile**. Selezionare il pacchetto da aggiornare, e fare clic sul pulsante **Aggiorna** .
* (Visual Studio 2017) Nella parte superiore della finestra di dialogo selezionare **Aggiornamenti**. Selezionare il pacchetto da aggiornare, e fare clic sul pulsante **Aggiorna** .
* Compilare e distribuire.

**2. Aggiornare gli script.** Se si usano script **PowerShell** per gestire le partizioni, [scaricare la nuova versione della libreria](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copiarla nella directory da cui vengono eseguiti gli script.

**3. Aggiornare il servizio di divisione e unione.** Se si usa il servizio di divisione e unione di scalabilità elastica per riorganizzare i dati delle partizioni, [scaricare e distribuire la versione più recente dello strumento](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). I passaggi dettagliati dell'aggiornamento per il servizio sono disponibili [qui](sql-database-elastic-scale-overview-split-and-merge.md).

**4. Aggiornare i database di Gestore mappe partizioni**. Aggiornare i metadati che supportano le mappe delle partizioni nel database SQL di Azure.  Esistono due modi per eseguire questa operazione, tramite PowerShell o C#. Entrambe le opzioni sono mostrate di seguito.

***Opzione 1: aggiornare i metadati tramite PowerShell***

1. Scaricare l'utility della riga di comando più recente per NuGet da [qui](https://nuget.org/nuget.exe) e salvarla in una cartella.
2. Aprire un prompt dei comandi, passare alla stessa cartella ed eseguire il comando: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Passare alla sottocartella contenente la versione DLL appena scaricata, ad esempio: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Scaricare lo script di aggiornamento del client di database elastico dal [Centro script](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9) e salvarlo nella stessa cartella contenente la DLL.
5. Da quella cartella, eseguire “PowerShell .\upgrade.ps1” dal prompt dei comandi e seguire i prompt.

***Opzione 2: aggiornare i metadati tramite C#***

In alternativa, creare un'applicazione Visual Studio che apre ShardMapManager, scorre tutte le partizioni ed esegue l'aggiornamento dei metadati chiamando i metodi [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) e [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) come in questo esempio:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Tali tecniche di aggiornamento dei metadati possono essere applicate più volte senza creare problemi. Se, ad esempio, una versione client precedente crea inavvertitamente una partizione dopo che è già stato eseguito l'aggiornamento, è possibile eseguire nuovamente l'aggiornamento in tutte le partizioni per garantire che nell'infrastruttura sia presente la versione dei metadati più aggiornata.

**Nota:**  le nuove versioni della libreria client pubblicate fino a oggi continuano a funzionare con le versioni precedenti dei metadati di Gestore mappe partizioni sul database SQL di Azure e viceversa.   Tuttavia, per sfruttare alcune delle nuove funzionalità nel client più recente, è necessario aggiornare i metadati.   Tenere presente che gli aggiornamenti dei metadati non influiscono sui dati utente o sui dati specifici dell'applicazione, solo gli oggetti creati e utilizzati da Gestore mappe partizioni.  E le applicazioni continuano a funzionare nel corso della sequenza di aggiornamento descritta in precedenza.

## <a name="elastic-database-client-version-history"></a>Cronologia delle versioni del client di database elastico

Per la cronologia delle versioni, passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
