---
title: Domande frequenti sul backup di file di Azure
description: Questo articolo fornisce informazioni dettagliate su come proteggere le condivisioni file di Azure.
services: backup
keywords: Non aggiungere o modificare parole chiave senza consultare l'esperto SEO.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 850d4d1e2ef6a13fcd8a072e6da210d558c7769b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Domande sul backup di file di Azure
Questo articolo risponde a domande comuni sul backup di file di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Per analizzare rapidamente le sezioni di questo articolo, usare i collegamenti riportati a destra sotto **In questo articolo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurazione del processo di backup per i file di Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Perché non vengono visualizzati alcuni degli account di archiviazione da proteggere e che contengono condivisioni file di Azure valide? <br/>
Durante l'anteprima, il backup per le condivisioni file di Azure non supporta tutti i tipi di account di archiviazione. Vedere l'elenco di account di archiviazione supportati disponibile [qui](troubleshoot-azure-files.md#preview-boundaries).

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Perché non vengono visualizzate alcune delle condivisioni file di Azure nell'account di archiviazione quando si prova a configurare il backup? <br/>
Verificare se la condivisione file di Azure è già protetta nello stesso insieme di credenziali di Servizi di ripristino oppure se è stata eliminata di recente.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-file-sync-br"></a>Perché non è possibile proteggere condivisioni file connesse a un gruppo di sincronizzazione in Sincronizzazione file di Azure? <br/>
La protezione di condivisioni file di Azure connesse a gruppi di sincronizzazione è disponibile in anteprima limitata. Inviare un messaggio a [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) indicando l'ID della sottoscrizione per richiedere l'accesso. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>In quali aree geografiche è possibile eseguire il backup di condivisioni file di Azure? <br/>
Il backup di condivisioni file di Azure è attualmente disponibile in anteprima e solo nelle aree geografiche seguenti: 
-   Australia sud-orientale (ASE) 
- Brasile meridionale (BRS)
- Canada centrale (CNC)
-   Canada orientale (CE)
-   Stati Uniti centrali (CUS)
-   Asia orientale (EA)
-   Australia orientale (AE) 
-   Stati Uniti orientali (EUS)
-   Stati Uniti orientali 2 (EUS2)
-   India centrale (INC) 
-   Stati Uniti centro-settentrionali (NCUS) 
-   Europa settentrionale (NE) 
-   Stati Uniti a centro-meridionali (SCUS) 
-   Asia sud-orientale (SEA)
-   Regno Unito meridionale (UKS) 
-   Regno Unito occidentale (UKW) 
-   Europa occidentale (OE) 
-   Stati Uniti occidentali (WUS)
-   Stati Uniti centro occidentali (WCUS)
-   Stati Uniti occidentali 2 (WUS 2)

Inviare un messaggio a [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) se è necessario usare la funzionalità in un'area geografica non indicata nell'elenco.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Quante condivisioni file di Azure si possono proteggere in un insieme di credenziali?<br/>
Durante l'anteprima è possibile proteggere condivisioni file di Azure di un massimo di 25 account di archiviazione per ogni insieme di credenziali. È possibile proteggere fino a 200 condivisioni file di Azure in un singolo insieme di credenziali. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Quanti backup su richiesta è possibile eseguire per ogni condivisione file? <br/>
È previsto un limite di 200 snapshot per ogni condivisione file. Il limite include gli snapshot creati da Backup di Azure in base ai criteri definiti. Se si iniziano a rilevare errori con i backup dopo aver raggiunto questo limite, eliminare i punti di ripristino su richiesta per poter completare i backup successivi.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Dopo aver abilitato le reti virtuali nell'account di archiviazione, il backup di condivisioni file nell'account non riesce. Perché?
Il backup di condivisioni file di Azure non supporta attualmente gli account di archiviazione con reti virtuali abilitate. Disabilitare le reti virtuali negli account di archiviazione per consentire il backup. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>È possibile recuperare una condivisione file di Azure eliminata? <br/>
Quando si elimina una condivisione file di Azure, viene visualizzato l'elenco dei backup che verranno eliminati se si procede, con la relativa richiesta di conferma. Non è possibile ripristinare una condivisione file di Azure eliminata.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>È possibile eseguire il ripristino dai backup se è stata interrotta la protezione in una condivisione file di Azure? <br/>
Sì. Se si è scelto **Conserva i dati di backup** quando è stata interrotta la protezione, sarà possibile eseguire il ripristino da tutti i punti di ripristino esistenti.

## <a name="manage-backup"></a>Gestire il backup

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>È possibile accedere agli snapshot creati da backup di Azure e montarli? <br/>
È possibile accedere a tutti gli snapshot creati da Backup di Azure visualizzandoli nel portale, in PowerShell o nell'interfaccia della riga di comando. Per altre informazioni sugli snapshot di condivisione per File di Azure, vedere [Panoramica degli snapshot di condivisione per File di Azure (anteprima)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Qual è il periodo di conservazione massimo configurabile per i backup? <br/>
Il backup di condivisioni file di Azure consente di conservare i backup giornalieri fino a 120 giorni.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Cosa accade quando si modificano i criteri di backup per una condivisione file di Azure? <br/>
Quando vengono applicati nuovi criteri a una o più condivisioni file, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

## <a name="see-also"></a>Vedere anche 
Queste informazioni riguardano solo il backup di file di Azure. Per informazioni su altre aree di Backup di Azure, vedere queste domande frequenti sul backup:
-  [Domande frequenti sull'insieme di credenziali di Servizi di ripristino](backup-azure-backup-faq.md)
-  [Domande frequenti sul backup delle macchine virtuali di Azure](backup-azure-vm-backup-faq.md)
-  [Domande frequenti sull'agente di Backup di Azure](backup-azure-file-folder-backup-faq.md)