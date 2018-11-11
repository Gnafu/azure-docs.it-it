---
title: 'Primo approccio: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino'
description: Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino. Usare i backup delle VM distribuite con Resource Manager, le VM distribuite con la modalità classica e le VM di Archiviazione Premium, le VM crittografate e le VM in Managed Disks per proteggere i dati. Creare e registrare un insieme di credenziali dei servizi di ripristino. Registrare macchine virtuali, creare criteri e proteggere macchine virtuali in Azure.
services: backup
author: markgalioto
manager: carmonm
keyword: backups; vm backup
ms.service: backup
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
keywords: backup; backup di macchine virtuali
ms.openlocfilehash: daa2355d028af9b61b0b14a453452c1a96487403
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233542"
---
# <a name="back-up-azure-virtual-machines-to-recovery-services-vault"></a>Backup di macchine virtuali di Azure in insiemi di credenziali di Servizi di ripristino

Questo articolo illustra come configurare la protezione per una macchina virtuale dal menu delle operazioni delle macchine virtuali o dall'insieme di credenziali di Servizi di ripristino. Gli insiemi di credenziali di Servizi di ripristino proteggono:

* VM distribuite in Azure Resource Manager
* Macchine virtuali classiche
* Macchine virtuali di Archiviazione Standard
* Macchine virtuali di Archiviazione Premium
* Macchine virtuali in esecuzione su Managed Disks
* VM crittografate usando Crittografia dischi di Azure
* Backup coerente con le applicazioni di macchine virtuali Windows tramite VSS e di macchine virtuali Linux tramite script pre-snapshot e post-snapshot personalizzati

Per altre informazioni sulla protezione di macchine virtuali di Archiviazione Premium, vedere l'articolo [Backup e ripristino di macchine virtuali di Archiviazione Premium](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup). Per altre informazioni sul supporto per macchine virtuali con Managed Disks, vedere [Backup e ripristino di macchine virtuali in Managed Disks](backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). Per altre informazioni sul framework di script pre-snapshot e post-snapshot per il backup di macchine virtuali Linux, vedere [Backup di macchine virtuali Linux coerente con le applicazioni tramite script pre-snapshot e post-snapshot](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Per altre informazioni su ciò di cui è possibile/non è possibile eseguire il backup, vedere [Preparare l'ambiente per eseguire il backup di macchine virtuali di Azure](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

> [!NOTE]
> Il servizio di backup crea un gruppo di risorse distinto da quello della macchina virtuale per archiviare la raccolta di punti di ripristino. È consigliabile che i clienti non blocchino il gruppo di risorse creato per l'uso dal servizio di backup.
Il formato di denominazione del gruppo di risorse creato dal servizio di backup è: AzureBackupRG_`<Geo>`_`<number>`
<br>Ad esempio, AzureBackupRG_northeurope_1
>
>

A seconda del numero di macchine virtuali da proteggere, è possibile iniziare da diversi punti. Se si desidera eseguire il backup di più macchine virtuali in un'unica operazione, passare all'insieme di credenziali di Servizi di ripristino e [avviare il processo di backup nel dashboard dell'insieme di credenziali](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-recovery-services-vault). Se si desidera eseguire il backup di una singola macchina virtuale, [avviare il processo dal menu Operazioni macchina virtuale](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).

## <a name="configure-the-backup-job-from-the-vm-operations-menu"></a>Configurare il processo di backup dal menu Operazioni macchina virtuale

Usare i passaggi seguenti per configurare il processo di backup dal menu Operazioni macchina virtuale. La procedura è applicabile solo alle macchine virtuali nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Tutti i servizi** nel menu Hub e digitare **Macchine virtuali** nella finestra di dialogo Filtro. Durante la digitazione verrà filtrato l'elenco di risorse. Dopo averla individuata, selezionare la voce Macchine virtuali.

  ![Screenshot che illustra come passare a Macchine virtuali da Tutti i servizi](./media/backup-azure-vms-first-look-arm/open-vm-from-hub.png)

  Verrà visualizzato l'elenco di macchine virtuali (VM) nella sottoscrizione.

  ![Verrà visualizzato l'elenco di macchine virtuali nella sottoscrizione.](./media/backup-azure-vms-first-look-arm/list-of-vms.png)

3. Dall'elenco selezionare la macchina virtuale di cui eseguire il backup.

  ![Verrà visualizzato l'elenco di macchine virtuali nella sottoscrizione.](./media/backup-azure-vms-first-look-arm/list-of-vms-selected.png)

  Quando si seleziona la macchina virtuale, l'elenco di macchine virtuali si sposta a sinistra e si aprono il menu di gestione e il dashboard della macchina virtuale.

4. Nel menu di gestione della macchina virtuale, nella sezione **Operazioni** fare clic su **Backup**. </br>

  ![Opzione di backup nel menu di gestione della macchina virtuale](./media/backup-azure-vms-first-look-arm/vm-management-menu.png)

  Verrà aperto il menu Abilita backup.

  ![Opzione di backup nel menu di gestione della macchina virtuale](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup.png)

5. Nell'area insieme di credenziali di Servizi di ripristino, fare clic su **Seleziona esistente** e scegliere l'insieme di credenziali dall'elenco a discesa.

  ![Procedura guidata Abilita backup](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

  Se non sono presenti insiemi di credenziali di Servizi di ripristino o se si desidera usare un nuovo insieme, fare clic su **Crea nuovo** e inserire un nome per il nuovo insieme di credenziali. Viene creato un nuovo insieme nello stesso gruppo di risorse e nella stessa area geografica della macchina virtuale. Se si desidera creare un insieme di credenziali di Servizi di ripristino con valori diversi, vedere la sezione su [come creare un insieme di credenziali di Servizi di ripristino](backup-azure-vms-first-look-arm.md#create-a-recovery-services-vault-for-a-vm).

6. Dal menu Scegliere i criteri di backup, selezionare un criterio. I dettagli del criterio selezionato saranno visualizzati sotto il menu a discesa.

  Per creare un nuovo criterio o modificare i criteri esistenti, fare clic su **Crea (o modifica) un nuovo criterio** per aprire l'editor Criteri di backup. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Per salvare le modifiche al criterio di backup e tornare al menu Abilita backup, fare clic su **OK**.

  ![Selezionare il criterio di backup](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Per applicare i criteri di backup e l'insieme di credenziali di servizi di ripristino alla macchina virtuale, fare clic su **Abilita backup** per distribuire il criterio. La distribuzione del criterio consente di associarlo all'insieme di credenziali e alle macchine virtuali.

  ![Pulsante Abilita backup](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. È possibile monitorare l'avanzamento della configurazione tramite le notifiche visualizzate nel portale. L'esempio seguente mostra che la distribuzione ha avuto inizio.

  ![Notifica di Abilita backup](./media/backup-azure-vms-first-look-arm/vm-management-blade-enable-backup-notification.png)

9. Dopo aver completato la configurazione, fare clic su **Backup** nel menu di gestione della macchina virtuale per aprire il menu Backup e visualizzare i dettagli disponibili.

  ![Visualizzazione dell'elemento di backup della macchina virtuale](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

  Fino al completamento del backup iniziale, lo **stato dell'ultimo backup** è **Avviso (backup iniziale in sospeso)**. Per visualizzare la data e l'ora del processo di backup successivo pianificato, fare clic sul nome del criterio in **Riepilogo**. Viene visualizzato il menu Criteri di backup in cui viene indicata l'ora del backup pianificato.

10. Per proteggere la macchina virtuale, fare clic su **Esegui backup ora**.

  ![Fare clic su Esegui backup ora per eseguire il backup iniziale](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

  Si aprirà il menu Esegui backup ora.

  ![mostra il pannello Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

11. Nel menu Esegui backup ora, fare clic sull'icona del calendario, usare il comando del calendario per selezionare l'ultimo giorno di conservazione di tale punto di recupero e fare clic su **OK**.

  ![impostare l'ultimo giorno di conservazione del punto di ripristino di Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Le notifiche sulla distribuzione consentono di sapere che il processo di backup è stato attivato e che è possibile monitorare lo stato di avanzamento del processo nella pagina Processi di backup.

## <a name="configure-the-backup-job-from-the-recovery-services-vault"></a>Configurare il processo di backup dall'insieme di credenziali di Servizi di ripristino
Per configurare il processo di backup, seguire questa procedura.

1. Creare un insieme di credenziali di Servizi di ripristino per una macchina virtuale.
2. Usare il portale di Azure per selezionare uno scenario, impostare criteri di backup e identificare gli elementi da proteggere.
3. Eseguire il backup iniziale.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creare l'insieme di credenziali dei servizi di ripristino per una macchina virtuale
Un insieme di credenziali dei servizi di ripristino è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup applicati alle VM protette.

> [!NOTE]
> Il backup delle VM è un processo locale. Non è possibile eseguire il backup delle VM da un'area geografica a un insieme di credenziali dei servizi di ripristino in un'altra area. Quindi in ogni area geografica di Azure con VM di cui eseguire il backup deve esistere almeno un insieme di credenziali dei servizi di ripristino.
>
>

Per creare un insieme di credenziali dei servizi di ripristino:

1. Se questa operazione non è già stata eseguita, accedere al [portale di Azure](https://portal.azure.com/), usando la sottoscrizione di Azure.
2. Fare clic su **Tutti i servizi** nel menu Hub e digitare **Servizi di ripristino** nella finestra di dialogo Filtro. Durante la digitazione verrà filtrato l'elenco di risorse. Quando viene visualizzato, fare clic su Insiemi di credenziali dei servizi di ripristino nell'elenco.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Se nella sottoscrizione sono disponibili insiemi di credenziali di Servizi di ripristino, verranno elencati.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-azure-vms-first-look-arm/list-of-rs-vault.png)
3. Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    Verrà visualizzato il menu degli insiemi di credenziali di Servizi di ripristino, in cui viene richiesto di specificare **Nome**, **Sottoscrizione**, **Gruppo di risorse** e **Località**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. Nel campo **Nome**digitare un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

5. Nella sezione **Sottoscrizione** usare il menu a discesa per scegliere la sottoscrizione di Azure. Se si usa una sola sottoscrizione, questa verrà visualizzata e sarà possibile andare al passaggio successivo. Se non si è certi di quale sottoscrizione usare, usare la sottoscrizione predefinita (o suggerita). Sono disponibili più scelte solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

6. Nella sezione **Gruppo di risorse**:

    * Selezionare **Crea nuovo** se si vuole creare un gruppo di risorse.
    Oppure
    * Selezionare **Usa esistente** e fare clic sul menu a discesa per visualizzare l'elenco di gruppi di risorse disponibili.

  Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

7. Fare clic su **Località** per selezionare l'area geografica per l'insieme di credenziali. La scelta determina l'area geografica in cui vengono inviati i dati di backup.

  > [!IMPORTANT]
  > Se si non è certi dell'area geografica della macchina virtuale, chiudere la finestra di dialogo di creazione dell'insieme di credenziali e passare all'elenco di macchine virtuali nel portale. Se si hanno macchine virtuali in più aree, creare un insieme di credenziali di Servizi di ripristino in ogni area. Creare l'insieme di credenziali nella prima area geografica prima di passare a quella successiva. Non è necessario specificare gli account di archiviazione usati per archiviare i dati di backup, perché l'insieme di credenziali di Servizi di ripristino e il servizio Backup di Azure gestiscono l'archiviazione automaticamente.
  >

8. Nella parte inferiore del menu Insieme di credenziali di Servizi di ripristino fare clic su **Crea**.

    La creazione dell'insieme di credenziali dei servizi di ripristino può richiedere alcuni minuti. Monitorare le notifiche di stato nell'area superiore destra del portale. L'insieme di credenziali, dopo essere stato creato, viene visualizzato negli insiemi di credenziali di Servizi di ripristino. Se l'insieme di credenziali non viene visualizzato dopo qualche minuto, fare clic su **Aggiorna**.

    ![Fare clic sul pulsante Aggiorna](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Dopo la visualizzazione dell'insieme di credenziali nell'elenco corrispondente per i Servizi di ripristino, è possibile configurare la ridondanza di archiviazione.

Ora che l'insieme di credenziali è stato creato, è possibile apprendere come impostare la replica di archiviazione.

### <a name="set-storage-replication"></a>Impostare la replica di archiviazione
L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se l'insieme di credenziali di Servizi di ripristino è il backup primario, lasciare l'opzione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con [ridondanza geografica](../storage/common/storage-redundancy-grs.md) e con [ridondanza locale](../storage/common/storage-redundancy-lrs.md), vedere la panoramica [Replica di Archiviazione di Azure](../storage/common/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Nel menu **Insieme di credenziali di Servizi di ripristino** selezionare il nuovo insieme di credenziali.

  ![Selezionare il nuovo insieme di credenziali dall'elenco corrispondente per Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

  Quando si seleziona l'insieme di credenziali, si aprono il menu Impostazioni, *con il nome dell'insieme di credenziali nella parte superiore* e il dashboard dell'insieme di credenziali.

  ![Visualizzare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-update.png)

2. Nel menu di gestione del nuovo insieme di credenziali, usare il dispositivo di scorrimento verticale per passare alla sezione Gestisci e fare clic su **Infrastruttura di backup** per aprire il menu corrispondente.

   ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-config-bkup-infra.png)

3. Nel menu Infrastruttura di backup fare clic su **Configurazione backup** per aprire il menu **Configurazione backup**.

    ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-open-infra.png)
4. Scegliere l'opzione di replica di archiviazione appropriata per l'insieme di credenziali.

    ![opzioni di configurazione dell'archiviazione](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'opzione **Con ridondanza geografica**. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/common/storage-redundancy-grs.md) e [con ridondanza locale](../storage/common/storage-redundancy-lrs.md), vedere [Panoramica della ridondanza di archiviazione](../storage/common/storage-redundancy.md).


## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selezionare un obiettivo di backup, impostare i criteri e definire gli elementi da proteggere
Prima di registrare una macchina virtuale in un insieme di credenziali, eseguire il processo di individuazione per verificare che vengano identificate le eventuali nuove macchine virtuali aggiunte alla sottoscrizione. Il processo esegue una query su Azure per ottenere l'elenco delle macchine virtuali disponibili nella sottoscrizione, insieme ad altre informazioni come il nome del servizio cloud e l'area. Nel portale di Azure lo scenario fa riferimento a ciò che si inserirà nell'insieme di credenziali dei servizi di ripristino. I criteri determinano la pianificazione relativa alla frequenza e al momento in cui acquisiti i punti di ripristino. I criteri includono anche il periodo di mantenimento dati per i punti di ripristino.

1. Se è già aperto un insieme di credenziali dei servizi di ripristino, procedere al passaggio 2. In caso contrario, fare clic su **Tutti i servizi**. Digitare **Servizi di ripristino** e fare clic su **Insiemi di credenziali di Servizi di ripristino**.

    ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    Verrà visualizzato l'elenco degli insiemi di credenziali dei servizi di ripristino.

    ![Visualizzazione dell'elenco degli insiemi di credenziali dei Servizi di ripristino](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Nell'elenco degli insiemi di credenziali di Servizi di ripristino selezionare un insieme di credenziali per aprire il relativo dashboard.

     ![Menu dell'insieme di credenziali aperto](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Scegliere **Backup** dal menu del dashboard dell'insieme di credenziali per aprire il menu Backup.

    ![Aprire il menu Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Verranno visualizzati i menu Backup e Obiettivo del backup.

    ![Menu Scenario aperto](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)
3. Nel menu Obiettivo del backup, dal menu a discesa **Posizione di esecuzione del carico di lavoro** selezionare Azure. Dal menu a discesa **What do you want to backup?** (Elementi per il backup), scegliere Macchina virtuale, quindi fare clic su **OK**.

    Queste azioni registrano l'estensione della macchina virtuale nell'insieme di credenziali. Il menu Obiettivo del backup verrà chiuso e verrà aperto il menu **Criteri di backup**.

    ![Menu Scenario aperto](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. Nel menu Criteri di backup selezionare il criterio di backup da applicare all'insieme di credenziali.

    ![Selezionare il criterio di backup](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    I dettagli dei criteri predefiniti vengono elencati nel menu a discesa. Per creare un criterio, selezionare **Crea nuovo** dal menu a discesa. Per istruzioni sulla definizione di un criterio di backup, vedere [Definizione di un criterio di backup](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Fare clic su **OK** per associare il criterio di backup all'insieme di credenziali.

    Il menu Criteri di backup verrà chiuso e verrà visualizzato il menu **Seleziona macchine virtuali**.
5. Nel menu **Seleziona macchine virtuali** scegliere le macchine virtuali da associare al criterio specificato e fare clic su **OK**.

    ![Selezionare il carico di lavoro](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Viene convalidata la macchina virtuale selezionata. Se le macchine virtuali previste non sono visibili, verificare che si trovino nella stessa posizione di Azure dell'insieme di credenziali di Servizi di ripristino e che non siano già protette. La posizione dell'insieme dei credenziali dei Servizi di ripristino viene visualizzata nel dashboard dell'insieme.

6. Dopo avere definito tutte le impostazioni per l'insieme di credenziali, nel menu Backup fare clic su **Abilita backup** per distribuire il criterio nell'insieme di credenziali e nelle macchine virtuali. La distribuzione del criterio di backup non crea il punto di ripristino iniziale per la macchina virtuale.

    ![Abilita backup](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Dopo avere abilitato correttamente il backup, il criterio di backup verrà eseguito come pianificato. Tuttavia, procedere con l'avvio del primo processo di backup.

## <a name="initial-backup"></a>Backup iniziale
Distribuire un criterio di backup nella macchina virtuale non significa eseguire il backup dei dati. Per impostazione predefinita, il primo backup pianificato (definito nel criterio di backup) è il backup iniziale. Fino all'esecuzione del backup iniziale, lo stato dell'ultimo backup nel menu **Processi di backup** è **Avviso (backup iniziale in sospeso)**.

![Backup in sospeso](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A meno che l'avvio del backup iniziale non sia imminente, è consigliabile scegliere l'opzione **Esegui backup ora**.

Per eseguire il processo di backup iniziale:

1. Nel dashboard dell'insieme di credenziali fare clic sul numero sotto **Elementi di backup** oppure fare clic sul riquadro **Elementi di backup**. <br/>
  ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Si aprirà l'elenco **Elementi di backup**.

  ![Elementi di backup](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Nel menu **Elementi di backup** selezionare l'elemento.

  ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Si apre l'elenco **Elementi di backup**. <br/>

  ![Processo di backup attivato](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Nell'elenco **Elementi di backup** fare clic sui puntini di sospensione **...** per aprire il menu di scelta rapida.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Si apre il menu di scelta rapida.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Nel menu di scelta rapida fare clic su **Esegui backup ora**.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Si aprirà il menu Esegui backup ora.

  ![mostra il menu Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Nel menu Esegui backup ora, fare clic sull'icona del calendario, usare il comando del calendario per selezionare l'ultimo giorno di conservazione di tale punto di recupero e fare clic su **Backup**.

  ![impostare l'ultimo giorno di conservazione del punto di ripristino di Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Le notifiche sulla distribuzione consentono di sapere che il processo di backup è stato attivato e che è possibile monitorare lo stato di avanzamento del processo nella pagina Processi di backup. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

  > [!NOTE]
  > Tutti i dati sottoposti a backup tramite Backup di Azure vengono crittografati a riposo mediante la [crittografia del servizio di archiviazione](../storage/common/storage-service-encryption.md).
  >
  >

6. Per visualizzare o tenere traccia dello stato del backup iniziale, nel dashboard dell'insieme di credenziali, nel riquadro **Processi di backup** fare clic sul riquadro **In corso**.

  ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Si aprirà il menu Processi di backup.

  ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Nel menu **Processi di backup** è possibile visualizzare lo stato di tutti i processi. Controllare se il processo di backup per la macchina virtuale è ancora in corso o se è terminato. Al termine del processo di backup, lo stato è *Completato*.

  > [!NOTE]
  > Come parte dell'operazione di backup, il servizio Backup di Azure esegue un comando nell'estensione di backup in ogni VM per scaricare tutte le scritture e creare uno snapshot coerente.
  >
  >


[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installare l'agente di macchine virtuali nella macchina virtuale
Queste informazioni vengono fornite nel caso in cui siano necessarie. Per il funzionamento dell'estensione di backup, l'agente di macchine virtuali deve essere installato nella macchina virtuale di Azure. Se tuttavia la macchina virtuale è stata creata dalla raccolta di Azure, l'agente di macchine virtuali è già installato. Nelle macchine virtuali di cui viene eseguita la migrazione da data center locali l'agente di macchine virtuali non è installato. In tal caso l'agente di macchine virtuali deve essere installato. In caso di problemi con il backup della macchina virtuale di Azure, assicurarsi che l'agente di macchine virtuali di Azure sia installato correttamente nella macchina virtuale (vedere la tabella seguente). Se si crea una VM personalizzata, installare l'agente di macchine virtuali prima del provisioning della macchina virtuale.

Per altre informazioni, vedere gli articoli sull'[agente di macchine virtuale](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e sulla relativa [installazione](../virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

La tabella seguente fornisce informazioni aggiuntive sull'agente di macchine virtuali per macchine virtuali Windows e Linux.

| **operazione** | **Windows** | **Linux** |
| --- | --- | --- |
| Installazione dell'agente di macchine virtuali |<li>Scaricare e installare il file [MSI per l'agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Per completare l'installazione sono necessari privilegi di amministratore. <li>[Aggiornare le proprietà della VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |<li> Installare l' [agente Linux](https://github.com/Azure/WALinuxAgent) più recente da GitHub. Per completare l'installazione sono necessari privilegi di amministratore. <li> [Aggiornare le proprietà della VM](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è stato installato. |
| Aggiornamento dell'agente di VM |L'aggiornamento dell'agente di VM è semplice quanto la reinstallazione dei [file binari dell'agente di VM](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente VM. |Seguire le istruzioni sull'[aggiornamento dell'agente di macchine virtuali Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br>Assicurarsi che non siano in esecuzione operazioni di backup durante l'aggiornamento dell'agente di VM. |
| Convalida dell'installazione dell'agente di macchine virtuali |<li>Passare alla cartella *C:\WindowsAzure\Packages* nella VM di Azure, <li>che dovrebbe includere il file WaAppAgent.exe.<li> Fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e quindi selezionare la scheda **Dettagli**. Il campo Versione prodotto deve essere 2.6.1198.718 o superiore. |N/D |

### <a name="backup-extension"></a>Estensione di backup
Dopo aver installato l'agente di macchine virtuali nella macchina virtuale, il servizio Backup di Azure installa l'estensione di backup nell'agente di macchine virtuali. Il servizio Backup di Azure applica aggiornamenti e patch all'estensione di backup senza ulteriore intervento dell'utente.

Il servizio Backup installa l'estensione di backup anche se la VM non è in esecuzione. Una macchina virtuale in esecuzione consente di ottenere un punto di ripristino coerente con l'applicazione. Il servizio Backup di Azure continua tuttavia a eseguire il backup della macchina virtuale, anche se questa è spenta e non è stato possibile installare l'estensione. Questo tipo di backup è noto come macchina virtuale offline e il punto di ripristino è *coerente con l'arresto anomalo*.

## <a name="troubleshooting-information"></a>Informazioni sulla risoluzione dei problemi
In caso di problemi nell'esecuzione di alcune attività di questo articolo, vedere le [indicazioni per la risoluzione dei problemi](backup-azure-vms-troubleshoot.md).

## <a name="pricing"></a>Prezzi
Il costo del backup delle macchine virtuali di Azure è basato sul numero di istanze protette. Per una definizione di istanza protetta, vedere [Che cos'è un'istanza protetta?](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Per un esempio di calcolo del costo del backup di una macchina virtuale, vedere [Modalità di calcolo delle istanze protette](backup-azure-vms-introduction.md#calculating-the-cost-of-protected-instances). Per informazioni sui [prezzi per Backup](https://azure.microsoft.com/pricing/details/backup/), vedere la pagina relativa ai prezzi di Backup di Azure.

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](https://aka.ms/azurebackup_feedback).
