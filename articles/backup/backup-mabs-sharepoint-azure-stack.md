---
title: Eseguire il backup di una farm di SharePoint con Azure Stack
description: Usare il server di Backup di Azure per eseguire il backup e ripristinare i dati di SharePoint in Azure Stack. In questo articolo vengono fornite le informazioni per configurare la farm di SharePoint in modo da archiviare in Azure i dati desiderati. È possibile ripristinare i dati SharePoint protetti dal disco o da Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: 8200bfb4e99d7847dc1661a4258b2171f24a9aed
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210261"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Eseguire il backup di una farm di SharePoint con Azure Stack
Per eseguire il backup di una farm di SharePoint in Azure con Azure Stack usare il server di Backup di Microsoft Azure (MABS) come per eseguire il backup di altre origini dati. Backup di Azure offre flessibilità nella pianificazione di backup per creare punti di backup quotidiani, settimanali, mensili o annuali e offre diverse opzioni in termini di criteri di conservazione per i vari intervalli di backup. Offre inoltre la possibilità di archiviare copie dei dischi locali per obiettivi di tempi di ripristino (RTO) rapidi e di archiviare copie in Azure per una conservazione economicamente conveniente e a lungo termine.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versioni supportate di SharePoint e relativi scenari di protezione
Backup di Azure per MABS supporta gli scenari seguenti:

| Carico di lavoro | Versione | Distribuzione di SharePoint | Protezione e ripristino |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint distribuito come macchina virtuale Azure Stack <br> -------------- <br> SQL AlwaysOn | Opzioni di protezione di ripristino di farm di SharePoint: farm di ripristino, database e file o elemento di elenco dai punti di ripristino del disco.  Farm e ripristino dei database dai punti di ripristino di Azure. |

## <a name="before-you-start"></a>Prima di iniziare
È necessario verificare alcuni aspetti prima di eseguire il backup di una farm di SharePoint in Azure.

### <a name="prerequisites"></a>Prerequisiti
Prima di procedere, assicurarsi di avere [installato e preparato il server di Backup di Azure](backup-mabs-install-azure-stack.md) per proteggere i carichi di lavoro.

### <a name="protection-agent"></a>Agente protezione
L'agente di Backup di Azure deve essere installato nel server che esegue SharePoint, nei server che eseguono SQL Server e in tutti gli altri server che fanno parte della farm di SharePoint. Per altre informazioni sull'installazione dell'agente di protezione, vedere l'articolo relativo alla [configurazione dell'agente di protezione](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  L'unica eccezione è che l'agente viene installato in un server Web front-end (WFE) solo. Il server di Backup di Azure richiede che l'agente sia installato in un solo server WFE e agisca come punto di ingresso per la protezione.

### <a name="sharepoint-farm"></a>Una farm di SharePoint
Per ogni 10 milioni di elementi nella farm, deve essere almeno disponibili 2 GB di spazio nel volume in cui si trova la cartella di MABS. Questo spazio è necessario per la generazione del catalogo. Affinché MABS ripristini elementi specifici, ad esempio raccolte siti, siti, elenchi, raccolte documenti, cartelle, singoli documenti ed elementi di elenchi, la generazione del catalogo crea un elenco degli URL inclusi in ogni database del contenuto. È possibile visualizzare l'elenco degli URL nel pannello degli elementi ripristinabili dell'area delle attività di **ripristino** della Console amministrazione MABS.

### <a name="sql-server"></a>SQL Server
Il server di Backup di Azure viene eseguito come account LocalSystem. Per eseguire il backup dei database di SQL Server, MABS deve avere privilegi sysadmin sull'account per il server che esegue SQL Server. Impostare NT AUTHORITY\SYSTEM su *sysadmin* nel server che esegue SQL Server prima di eseguire il backup.

Se la farm di SharePoint ha un database di SQL Server configurato con alias di SQL Server, installare i componenti del client di SQL Server nel server Web front-end da proteggere con MABS.

### <a name="whats-not-supported"></a>Attività non supportate
* MABS protegge una farm di SharePoint, ma non protegge gli indici di ricerca e i database di servizio dell'applicazione. Occorre configurare separatamente la protezione di questi database.
* MABS non esegue il backup dei database di SQL Server SharePoint ospitati in condivisioni di file server di scalabilità orizzontale.

## <a name="configure-sharepoint-protection"></a>Configurare la protezione di SharePoint
Prima di usare MABS per proteggere SharePoint, è necessario configurare il servizio VSS Writer di SharePoint (servizio WSS Writer) tramite **ConfigureSharePoint.exe**.

**ConfigureSharePoint.exe** è disponibile nella cartella [percorso di installazione di MABS]\bin nel server Web front-end. Questo strumento fornisce all'agente protezione le credenziali per la farm di SharePoint. È possibile eseguirlo in un unico server front-end Web. Se si dispone di più server WFE, selezionarne solo uno quando si configura un gruppo di protezione dati.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Per configurare il servizio Writer VSS di SharePoint
1. Nel server WFE, al prompt dei comandi, passare a [percorso di installazione di MABS]\bin\
2. Digitare ConfigureSharePoint -EnableSharePointProtection.
3. Immettere le credenziali di amministratore della farm. Questo account deve essere un membro del gruppo degli amministratori locale nel server front-end Web. Se l'amministratore della farm non è un amministratore locale, concedere le autorizzazioni seguenti sul server front-end Web:
   * Concedere il controllo completo del gruppo WSS_Admin_WPG sulla cartella DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Concedere l'accesso in lettura al gruppo WSS_Admin_WPG per la chiave del Registro di sistema di DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> È necessario eseguire di nuovo ConfigureSharePoint.exe ogni volta che si verifica un cambiamento nelle credenziali di amministratore di farm SharePoint.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Eseguire il backup di una farm di SharePoint tramite MABS
Dopo aver configurato MABS e la farm di SharePoint come descritto in precedenza, è possibile proteggere SharePoint con MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Per proteggere una farm di SharePoint
1. Dalla scheda **Protezione** della Console di amministrazione MABS fare clic su **Nuovo**.
    ![Nuova scheda Protezione](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Nella pagina **Seleziona tipo di gruppo protezione dati** della procedura guidata **Crea nuovo gruppo protezione dati** selezionare **Server** e fare clic su **Avanti**.

    ![Seleziona tipo di gruppo di protezione dati](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Nella schermata **Seleziona membri del gruppo** selezionare la casella di controllo del server di SharePoint che si vuole proteggere e fare clic su **Avanti**.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Con l'agente protezione installato, è possibile visualizzare il server della procedura guidata. MABS mostra anche la propria struttura. Poiché è stato eseguito ConfigureSharePoint.exe, MABS comunica con il servizio VSS Writer di SharePoint e con i relativi database di SQL Server e riconosce la struttura della farm di SharePoint, i database del contenuto associati e tutti gli elementi corrispondenti.
   >
   >
4. Nella pagina **Seleziona metodo protezione dati** digitare il nome del **Gruppo protezione dati** e selezionare i *metodi di protezione* preferiti. Fare clic su **Avanti**.

    ![Seleziona metodo protezione dati](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Il metodo di protezione del disco consente di soddisfare obiettivi di tempi di ripristino brevi.
   >
   >
5. Nella pagina **Specifica obiettivi a breve termine** selezionare l'**intervallo di conservazione** preferito e specificare la frequenza di esecuzione dei backup.

    ![Specifica obiettivi a breve termine](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Poiché è molto spesso richiesto il ripristino di dati con data di creazione inferiore a cinque giorni, in questo esempio è stato selezionato un periodo di conservazione sul disco di cinque giorni ed è stato specificato che il backup non venga eseguito durante orari lavorativi.
   >
   >
6. Esaminare lo spazio su disco del pool di archiviazione allocato per il gruppo protezione dati e fare clic su **Avanti**.
7. Per ogni gruppo protezione dati MABS alloca spazio su disco per archiviare e gestire le repliche. A questo punto, MABS deve creare una copia dei dati selezionati. Selezionare come e quando si vuole che la replica venga creata, quindi fare clic su **Avanti**.

    ![Scelta del metodo per la creazione della replica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Per assicurarsi che il traffico di rete non venga influenzato, selezionare un'ora fuori dell'orario di produzione.
   >
   >
8. MABS garantisce l'integrità dei dati mediante l'esecuzione di verifiche della coerenza sulla replica. Sono disponibili due opzioni: È possibile definire una pianificazione per eseguire verifiche della coerenza oppure DPM può eseguire automaticamente verifiche di coerenza sulla replica quando diventa incoerente. Selezionare l'opzione preferita e fare clic su **Avanti**.

    ![Verifica coerenza](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Nella pagina **Specifica i dati da proteggere online** selezionare la farm di SharePoint che si vuole proteggere e fare clic su **Avanti**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Nella pagina **Specifica la pianificazione dei backup online** selezionare la pianificazione preferita e fare clic su **Avanti**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS fornisce un massimo di due backup giornalieri in Azure dal punto di backup del disco più recente disponibile in quel momento. Backup di Azure può inoltre controllare la quantità di larghezza di banda WAN che può essere usata per i backup in orari normali e di punta tramite la [limitazione della larghezza di bada della rete di Backup di Azure](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. In base alla pianificazione di backup selezionata, nella pagina **Specificare i criteri di mantenimento online** selezionare i criteri di conservazione per i punti di backup giornalieri, settimanali, mensili e annuali.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS usa uno schema di conservazione GFS (Grandfather-Father-Son, nonno-padre-figlio) in cui è possibile scegliere criteri di conservazione diversi per punti di backup diversi.
    >
    >
12. Analogamente al disco, è necessario creare una replica del punto di riferimento iniziale in Azure. Selezionare l'opzione preferita per creare una copia di backup iniziale in Azure e fare clic su **Avanti**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Esaminare le impostazioni selezionate nella pagina di **riepilogo** e fare clic su **Crea gruppo**. Dopo aver creato il gruppo di protezione dati, viene visualizzato un messaggio di corretto completamento.

    ![Riepilogo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Ripristinare un elemento di SharePoint dal disco tramite MABS
Nell'esempio seguente, l' *elemento di SharePoint da ripristinare* è stato accidentalmente eliminato e deve essere ripristinato.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Aprire la **Console amministrazione DPM**. Tutte le farm di SharePoint protette da DPM vengono visualizzate nella scheda **Protezione** .

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Per iniziare a ripristinare l'elemento, selezionare la scheda **Ripristino** .

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. È possibile eseguire ricerche in SharePoint relative all' *elemento di SharePoint da ripristinare* tramite una ricerca con caratteri jolly all'interno di un intervallo di punti di ripristino.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selezionare il punto di ripristino appropriato dai risultati della ricerca, fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina**.
5. È inoltre possibile scorrere i vari punti di ripristino e selezionare un database o un elemento da recuperare. Selezionare **Data > Tempo di ripristino**, quindi selezionare l'elemento corretto da **Database > Farm SharePoint > Punto di ripristino > Elemento**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Fare clic con il pulsante destro del mouse sull'elemento e selezionare **Ripristina** per aprire il **Ripristino guidato**. Fare clic su **Avanti**.

    ![Verifica selezione per ripristino](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selezionare il tipo di ripristino che si vuole eseguire, quindi fare clic su **Avanti**.

    ![Tipo di ripristino](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > In questo esempio, la selezione di **Ripristina originale** ripristina l'elemento nel sito originale di SharePoint.
   >
   >
8. Selezionare il **processo di ripristino** che si vuole usare.

   * Selezionare **Ripristina senza utilizzare una farm di ripristino** se la farm di SharePoint non è stata modificata e corrisponde al punto di ripristino eseguito.
   * Selezionare **Ripristina utilizzando una farm di ripristino** se la farm di SharePoint è stato modificata dopo che il punto di ripristino è stato creato.

     ![processo di ripristino](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Specificare un percorso di istanza di gestione temporanea di SQL Server per ripristinare temporaneamente il database e specificare una condivisione di file di gestione temporanea in MABS e nel server che esegue SharePoint per ripristinare l'elemento.

    ![Staging Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS collega il database del contenuto che ospita l'elemento di SharePoint all'istanza di gestione temporanea di SQL Server. MABS ripristina l'elemento dal database del contenuto e lo aggiunge al percorso di file di gestione temporanea di MABS. L'elemento recuperato che si trova nel percorso di gestione temporanea deve ora essere esportato nel percorso di gestione temporaneo della farm di SharePoint.

    ![Gestione temporanea Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selezionare **Specifica opzioni di ripristino**e applicare le impostazioni di sicurezza per la farm di SharePoint o applicare le impostazioni di sicurezza del punto di ripristino. Fare clic su **Avanti**.

    ![Opzioni di ripristino](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > È possibile scegliere di limitare l'utilizzo della larghezza di banda di rete. Consente di ridurre l'impatto sul server di produzione durante l'orario di produzione.
    >
    >
11. Esaminare le informazioni di riepilogo e fare clic su **Ripristina** per avviare il ripristino del file.

    ![Riepilogo di ripristino](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Selezionare la scheda **Monitoraggio** nella **Console di amministrazione MABS** per visualizzare lo **Stato** del ripristino.

    ![Stato di ripristino](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A questo punto è possibile ripristinare il file. È possibile aggiornare il sito di SharePoint per controllare il file ripristinato.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Ripristinare un database di SharePoint da Azure tramite DPM
1. Per ripristinare un database del contenuto di SharePoint, scorrere i vari punti di ripristino (come illustrato in precedenza) e selezionare il punto di ripristino che si vuole ripristinare.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Fare doppio clic sul punto di ripristino di SharePoint per visualizzare le informazioni del catalogo di SharePoint disponibili.

   > [!NOTE]
   > Poiché la farm di SharePoint è protetta per la conservazione a lungo termine in Azure, non è disponibile nessuna informazione sul catalogo (metadati) in MABS. Di conseguenza, ogni volta che si vuole ripristinare un database del contenuto di SharePoint temporizzato, si deve ricatalogare la farm di SharePoint.
   >
   >
3. Fare clic su **Ricatalogazione**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Viene visualizzata la finestra di stato di **Ricatalogazione cloud** .

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Dopo aver completato la catalogazione, viene visualizzato lo stato *Operazione completata*. Fare clic su **Chiudi**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Fare clic sull'oggetto di SharePoint visualizzato nella scheda **Ripristino** di MABS per ottenere la struttura del database del contenuto. Fare clic con il pulsante destro del mouse sull'elemento e scegliere **Ripristina**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. A questo punto seguire i passaggi di ripristino illustrati precedentemente in questo articolo per ripristinare il database del contenuto di SharePoint dal disco.

## <a name="faqs"></a>Domande frequenti
D: È possibile ripristinare un elemento di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn (con protezione su disco)?<br>
R: Sì, l'elemento può essere ripristinato nel sito originale di SharePoint.

D: È possibile ripristinare un database di SharePoint nel percorso originale se SharePoint è configurato con SQL AlwaysOn?<br>
R: Poiché i database di SharePoint sono configurati in SQL AlwaysOn, non possono essere modificati se non si rimuove il gruppo di disponibilità. Di conseguenza MABS non può ripristinare il database nel percorso originale. È possibile ripristinare un database di SQL Server in un'altra istanza di SQL Server.

## <a name="next-steps"></a>Fasi successive

Vedere l'articolo [Eseguire il backup di file e applicazioni in Azure Stack](backup-mabs-files-applications-azure-stack.md).
Vedere l'articolo [Backup SQL Server on Azure Stack](backup-mabs-sql-azure-stack.md) (Eseguire il backup di SQL Server in Azure Stack).
