---
title: Eseguire il backup dell'app - Servizio app di Azure
description: Informazioni sulla creazione dei backup delle app nel Servizio app di Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8784a06306f59015b95293d90ff5509dcfcae045
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057943"
---
# <a name="back-up-your-app-in-azure"></a>Eseguire il backup dell'app in Azure
La funzionalità di backup e ripristino nel [Servizio app di Azure](overview.md) consente di creare facilmente backup di app in modo manuale o pianificato.  È possibile configurare i backup in modo che vengano conservati fino a un periodo di tempo indefinito. È possibile ripristinare l'app a una snapshot di uno stato precedente sovrascrivendo l'applicazione esistente o eseguendo il ripristino in un'altra applicazione.

Per informazioni sul ripristino di un'app dal backup, vedere [Ripristinare un'app nel Servizio app di Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Elementi di cui viene eseguito il backup
Il servizio app può eseguire il backup delle informazioni seguenti in un account di archiviazione di Azure e nel contenitore configurati per essere usati dall'app. 

* Configurazione dell'app
* Contenuto file
* Database connesso all'app

Le soluzioni di database seguenti sono supportate con funzionalità di backup: 
   - [Database SQL](https://azure.microsoft.com/services/sql-database/)
   - [Database di Azure per MySQL](https://azure.microsoft.com/services/mysql)
   - [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL in-app](https://azure.microsoft.com/en-us/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
>  Ciascun backup è una copia offline completa dell'app, non un aggiornamento incrementale.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisiti e restrizioni
* Per usufruire della funzionalità di backup e ripristino è necessario che il piano del Servizio app si trovi al livello **Standard** o al livello **Premium**. Per altre informazioni sul ridimensionamento del piano di servizio app per usare un livello superiore, vedere [Scalare un'app Web in Servizio app di Azure](manage-scale-up.md).  
  Il livello **Premium** consente un maggior numero di backup giornalieri rispetto al livello **Standard**.
* Sono necessari un account di archiviazione e un contenitore di Azure nella stessa sottoscrizione dell'applicazione di cui si desidera eseguire il backup. Per altre informazioni sugli account di archiviazione di Azure, vedere [Panoramica dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* È possibile eseguire il backup di un massimo di 10 GB di contenuto del database e dell'app. Se la dimensione del backup supera questo limite, verrà visualizzato un messaggio di errore.
* Il backup di Database di Azure per MySQL abilitato per SSL non è supportato. Se è configurato un backup, si otterranno i backup non riusciti.
* Il backup di Database di Azure per PostgreSQL abilitato per SSL non è supportato. Se è configurato un backup, si otterranno i backup non riusciti.
* Per i database MySQL in-app viene automaticamente eseguito un backup senza alcuna configurazione. Se si modificano manualmente le impostazioni per i database MySQL in-app, ad esempio si aggiungono stringhe di connessione, è possibile che i backup non vengano eseguiti correttamente.
* L'uso di un account di archiviazione abilitato per un firewall come destinazione per i backup non è supportato. Se è configurato un backup, si otterranno i backup non riusciti.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Creare un backup manuale
1. Nel [portale di Azure](https://portal.azure.com) passare alla pagina dell'app e selezionare **Backup**. Viene visualizzata la pagina **Backup**.
   
    ![Pagina Backups][ChooseBackupsPage]
   
   > [!NOTE]
   > Se viene visualizzato questo messaggio, fare clic per aggiornare il piano di servizio app prima di procedere con i backup.
   > Per altre informazioni, vedere [Aumentare le prestazioni di un'app in Azure](manage-scale-up.md).  
   > ![Scelta dell'account di archiviazione](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Nella pagina **Backup** fare clic su **Configura**
![Fare clic su Configura](./media/web-sites-backup/ClickConfigure1.png)
3. Nella pagina **Configurazione backup** fare clic su **Archiviazione: non configurata** per configurare un account di archiviazione.
   
    ![Scegli account di archiviazione][ChooseStorageAccount]
4. Scegliere la destinazione del backup selezionando un **Account di archiviazione** e un **Contenitore**. L'account di archiviazione deve appartenere alla stessa sottoscrizione dell'app da sottoporre a backup. Se si vuole, è possibile creare un nuovo account di archiviazione o un nuovo contenitore nelle rispettive pagine. Al termine, fare clic su **Seleziona**.
   
    ![Scegli account di archiviazione](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Nella pagina **Configurazione backup** rimasta aperta è possibile configurare **Backup database**, selezionare i database da includere nei backup (database SQL o MySQL), quindi fare clic su **OK**.  
   
    ![Scegli account di archiviazione](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Per visualizzare un database nell'elenco, è necessaria la presenza della relativa stringa di connessione nella sezione **Stringhe di connessione** della pagina **Impostazioni applicazione** per l'app. 
   >
   > Per i database MySQL in-app viene automaticamente eseguito un backup senza alcuna configurazione. Se si modificano manualmente le impostazioni per i database MySQL in-app, ad esempio si aggiungono stringhe di connessione, è possibile che i backup non vengano eseguiti correttamente.
   > 
   > 
6. Nella pagina **Configurazione backup** fare clic su **Salva**.    
7. Nella pagina **Backup** fare clic su **Backup**.
   
    ![Pulsante BackUp Now][BackUpNow]
   
    Durante il processo di backup viene visualizzato un messaggio di stato.

Dopo avere configurato l'account di archiviazione e il contenitore è possibile avviare un backup manuale in qualsiasi momento.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurazione dei backup automatici
1. Nella pagina **Configurazione backup** impostare **Backup pianificato** su **On** (Attivato). 
   
    ![Scegli account di archiviazione](./media/web-sites-backup/05ScheduleBackup1.png)
2. Verranno visualizzate le opzioni di pianificazione backup. Impostare **Backup pianificati** su **Sì**, quindi configurare la pianificazione dei backup come desiderato e fare clic su **OK**.
   
    ![Abilitazione dei backup automatici][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurare backup parziali
In alcuni casi non si vuole eseguire il backup di tutti gli elementi dell'app. Ecco alcuni esempi:

* Si [configurano backup settimanali](#configure-automated-backups) dell'app che contiene contenuto statico che non cambia mai, ad esempio immagini o post di blog precedenti.
* L'app include oltre 10 GB di contenuto, ovvero la quantità massima di cui è possibile eseguire il backup ogni volta.
* Non si vuole eseguire il backup dei file di log.

I backup parziali consentono di scegliere esattamente i file di cui eseguire il backup.

> [!NOTE]
> I singoli database del backup possono avere una dimensione massima di 4 GB, ma la dimensione totale massima del backup è 10 GB

### <a name="exclude-files-from-your-backup"></a>Escludere file dal backup
Si supponga di avere un'app che contiene file di log e immagini statiche di cui è stato eseguito un backup e che nos si intende modificare. In questi casi è possibile escludere le cartelle e i file dall'archiviazione nei backup futuri. Per escludere file e cartelle dai backup, creare un file `_backup.filter` nella cartella `D:\home\site\wwwroot` dell'applicazione. Specificare l'elenco di file e cartelle da escludere in questo file. 

Un modo semplice per accedere ai file consiste nell'usare Kudu. Fare clic su **Strumenti avanzati -> Vai** perché l'app Web acceda a Kudu.

![Uso del portale con Kudu][kudu-portal]

Identificare le cartelle da escludere dai backup.  Ad esempio, si vuole applicare un filtro per la cartella e i file evidenziati.

![Cartella delle immagini][ImagesFolder]

Creare un file denominato `_backup.filter` e inserire l'elenco precedente nel file, ma rimuovere `D:\home`. Elencare una directory o un file per ogni riga. Il contenuto del file dovrebbe essere analogo al seguente:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Caricare il file `_backup.filter` nella directory `D:\home\site\wwwroot\` del sito usando [ftp](deploy-ftp.md) o qualsiasi altro metodo. È anche possibile creare il file direttamente usando il comando `DebugConsole` in Kudu e inserire i contenuti nel file.

Eseguire i backup secondo la procedura consueta, ovvero [manualmente](#create-a-manual-backup) o [automaticamente](#configure-automated-backups). A questo punto, eventuali file e cartelle specificati in `_backup.filter` verranno esclusi dai backup futuri pianificati o avviati manualmente. 

> [!NOTE]
> È possibile ripristinare i backup parziali del sito nello stesso modo in cui si [ripristina un backup regolare](web-sites-restore.md). Il processo di ripristino esegue le operazioni corrette.
> 
> Dopo il ripristino di un backup completo, tutti i contenuti del sito vengono sostituiti dai contenuti del backup. Se un file è presente nel sito ma non nel backup, verrà eliminato. Quando viene ripristinato un backup parziale, tuttavia, eventuali contenuti presenti in una delle directory disattivate o in un file disattivato verranno lasciati invariati.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Modalità di archiviazione dei backup
Dopo l'esecuzione di uno o più backup per l'app, i backup saranno visibili nella pagina **Contenitori** dell'account di archiviazione, così come l'app. Nell'account di archiviazione ogni backup è costituito da un file con estensione `.zip` contenente i dati di backup e un file con estensione `.xml` contenente un manifesto dei contenuti del file con estensione `.zip`. È possibile decomprimere e sfogliare questi file se si vuole accedere ai backup senza eseguire effettivamente un ripristino delle app.

Il backup del database per l'app viene archiviato nella radice del file con estensione zip. Per un database SQL può essere un file BACPAC (nessuna estensione di file) e può essere importato. Per creare un database SQL basato sull'esportazione BACPAC, vedere [Importare un file BACPAC per creare un nuovo database utente](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> La modifica di qualsiasi file nel contenitore **websitebackups** può danneggiare il backup rendendolo non valido e dunque non ripristinabile.
> 
> 

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la gestione backup con gli script, usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

Per qualche esempio vedere:

- [Esempi dell'interfaccia della riga di comando di Azure](samples-cli.md)
- [Esempi di Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Fasi successive
Per informazioni sul ripristino di un'app da un backup, vedere [Ripristinare un'app nel Servizio app di Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

