---
title: Eseguire un'analisi di ripristino di emergenza per computer locali in Azure tramite Azure Site Recovery | Microsoft Docs
description: Informazioni sull'esecuzione di un'analisi di ripristino di emergenza locale in Azure, con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: d1b6dec122672e4f6260105f7b50af2cd7369947
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737107"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Eseguire un'esercitazione sul ripristino di emergenza in Azure

[Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo operative le app aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce e coordina il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e failback.

- Questa è la quarta esercitazione di una serie che illustra come configurare il ripristino di emergenza in Azure per macchine virtuali VMware locali. Si presuppone che siano state completate le prime due esercitazioni:
    - Nella [prima esercitazione](tutorial-prepare-azure.md) sono stati configurati i componenti di Azure necessari per il ripristino di emergenza di VMware.
    - Nella [seconda esercitazione](vmware-azure-tutorial-prepare-on-premises.md) sono stati preparati i componenti locali per il ripristino di emergenza e sono stati esaminati i prerequisiti.
    - Nella [terza esercitazione](vmware-azure-tutorial.md) è stata configurata e abilitata la replica per la macchina virtuale VMware locale.
- Le esercitazioni sono progettate per illustrare il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. 


In questo articolo viene illustrato come eseguire un'analisi del ripristino di emergenza per un computer locale in Azure tramite un failover di test. Un'analisi convalida la strategia di replica senza perdita di dati. È possibile passare agli argomenti seguenti:

> [!div class="checklist"]
> * Configurare una rete isolata per il failover di test
> * Preparare la connessione alla macchina virtuale di Azure dopo il failover
> * Eseguire un failover di test per un singolo computer

Questa esercitazione consente di configurare il ripristino di emergenza di VMware in Azure con le impostazioni più semplici. Per informazioni più dettagliate sui passaggi del failover di test, leggere la [guida alle procedure](site-recovery-test-failover-to-azure.md).

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Prima di eseguire un failover di test, verificare le proprietà della macchina virtuale VMware e assicurarsi che la macchina virtuale Hyper-V [hyper-v-azure-support-matrix.md#replicated-vms], la [macchina virtuale VMware o il server fisico](vmware-physical-azure-support-matrix.md#replicated-machines) sia conforme ai requisiti di Azure.

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il set di disponibilità e le impostazioni del disco gestito.
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-test-failover-for-a-single-vm"></a>Eseguire un failover di test per una singola macchina virtuale

Quando si esegue un failover di test, si verifica quanto segue:

1. Viene eseguito un controllo dei prerequisiti per verificare che tutte le condizioni necessarie per il failover siano in atto.
2. Il failover elabora i dati, in modo che sia possibile creare una macchina virtuale di Azure. Selezionando il punto di recupero più recente, viene creato un punto di recupero dai dati.
3. Una macchina virtuale di Azure viene creata usando i dati elaborati nel passaggio precedente.

Eseguire il failover di test come descritto di seguito:

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **+Failover di test**.
2. Per questa esercitazione, selezionare il punto di ripristino **elaborato più recente**. Verrà così eseguito il failover della VM al punto di ripristino più recente disponibile. Viene visualizzato il timestamp. Con questa opzione, non viene impiegato alcun tempo di elaborazione dati, pertanto viene fornito un RTO (Recovery Time Objective) basso.
3. In **Failover di test** selezionare la rete di Azure di destinazione a cui vengono connesse le macchine virtuali di Azure dopo il failover.
4. Fare clic su **OK** per iniziare il failover. È possibile tenere traccia dell'avanzamento facendo clic sulla macchina virtuale per visualizzarne le proprietà. In alternativa, è possibile fare clic sul processo **Failover di test** nel nome dell'insieme di credenziali, quindi su **Impostazioni** > **Processi** >
   **Site Recovery jobs** (Processi di Site Recovery).
5. Al termine del failover, la macchina virtuale di Azure di replica viene visualizzata nel portale di Azure in **Macchine virtuali**. Verificare che la macchina virtuale sia delle dimensioni appropriate, che sia connessa alla rete corretta e che sia in esecuzione.
6. Sarà ora possibile connettersi alla macchina virtuale replicata in Azure.
7. Per eliminare le macchine virtuali di Azure create durante il failover di test, fare clic su **Pulisci failover di test** nella VM. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test.

In alcuni scenari il failover richiede un'altra elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire failover e failback per macchine virtuali VMware locali](vmware-azure-tutorial-failover-failback.md).
