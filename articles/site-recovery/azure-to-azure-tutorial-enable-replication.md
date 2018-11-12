---
title: Configurare il ripristino di emergenza per le macchine virtuali di Azure in un'area di Azure secondaria con Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza per macchine virtuali di Azure in un'area di Azure diversa usando il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8dc6b7da77988a789de04578d6653b192f58afa8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261724"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure in un'area di Azure secondaria

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

Questa esercitazione illustra come configurare il ripristino di emergenza in un'area secondaria di Azure per macchine virtuali di Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali di Servizi di ripristino
> * Verificare le impostazioni delle risorse di destinazione
> * Configurare l'accesso in uscita per le macchine virtuali
> * Abilitare la replica per una macchina virtuale

> [!NOTE]
> In questa esercitazione l'utente viene guidato nella procedura di abilitazione della replica, con una personalizzazione minima. Se si desidera approfondire i vari aspetti associati al ripristino di emergenza, incluse considerazioni sulla rete, automazione o risoluzione dei problemi, vedere i documenti relativi alle procedure per le macchine virtuali di Azure.


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](concepts-azure-to-azure-architecture.md).
- Verificare i [requisiti di supporto](site-recovery-support-matrix-azure-to-azure.md) per tutti i componenti.

## <a name="create-a-vault"></a>Creare un insieme di credenziali

Creare l'insieme di credenziali in tutte le aree, ad eccezione dell'area di origine.

1. Accedere a [portale di Azure](https://portal.azure.com) > **Servizi di ripristino**.
2. Fare clic su **Crea una risorsa** > **Monitoraggio e gestione** > **Backup e Site Recovery**.
3. In **Nome**specificare un nome descrittivo per identificare l'insieme di credenziali. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
4. Creare un gruppo di risorse o selezionarne uno esistente. Specificare un'area di Azure. Per verificare le aree supportate, vedere la sezione relativa alla disponibilità a livello geografico in [Prezzi di Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Per accedere rapidamente all'insieme di credenziali dal dashboard, fare clic su **Aggiungi al dashboard** e quindi su **Crea**.

   ![Nuovo insieme di credenziali](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Il nuovo insieme di credenziali viene aggiunto al **Dashboard**, nella sezione **Tutte le risorse**, e nella pagina **Insiemi di credenziali dei servizi di ripristino** principale.

## <a name="verify-target-resources"></a>Verificare le risorse di destinazione

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di macchine virtuali nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

2. Assicurarsi che la sottoscrizione disponga di risorse sufficienti per supportare macchine virtuali con dimensioni corrispondenti alle macchine virtuali di origine. Per la macchina virtuale di destinazione, Site Recovery sceglie la stessa dimensione o la dimensione più vicina possibile.

## <a name="configure-outbound-network-connectivity"></a>Configurare la connettività di rete in uscita

Per un corretto funzionamento di Site Recovery, è necessario apportare alcune modifiche alla connettività di rete in uscita dalle macchine virtuali che si vuole replicare.

- Site Recovery non supporta l'uso di un proxy di autenticazione per controllare la connettività di rete.
- Se si usa un proxy di autenticazione, non è possibile abilitare la replica.

### <a name="outbound-connectivity-for-urls"></a>Connettività in uscita per gli URL

Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, consentire l'accesso agli URL seguenti usati da Site Recovery.

| **URL** | **Dettagli** |
| ------- | ----------- |
| *.blob.core.windows.net | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. |
| login.microsoftonline.com | Fornisce l'autenticazione e l'autorizzazione per gli URL del servizio Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Consente alla macchina virtuale di comunicare con il servizio Site Recovery. |
| *.servicebus.windows.net | Consente alla macchina virtuale di scrivere i dati di diagnostica e monitoraggio di Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Connettività in uscita per gli intervalli di indirizzi IP

Se si vuole controllare la connettività in uscita usando indirizzi IP invece di URL, inserire nell'elenco elementi consentiti gli intervalli di data center appropriati, gli indirizzi di Office 365 e gli indirizzi degli endpoint di servizio per firewall basati su IP, proxy o regole dei gruppi di sicurezza di rete.

  - [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Intervalli IP del data center di Microsoft Azure in Germania](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Intervalli IP del data center di Microsoft Azure in Cina](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Indirizzi IP dell'endpoint di servizio di Site Recovery](https://aka.ms/site-recovery-public-ips)

È possibile usare questo [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) per creare le regole NSG necessarie.

## <a name="verify-azure-vm-certificates"></a>Verificare i certificati della macchina virtuale di Azure

Controllare che nella macchina virtuale Windows o Linux siano presenti tutti i certificati radice più recenti da replicare. Se i certificati radice più recenti non sono disponibili, non è possibile registrare la macchina virtuale in Site Recovery per motivi di sicurezza.

- Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.

- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.

## <a name="set-permissions-on-the-account"></a>Impostare le autorizzazioni per l'account

Azure Site Recovery offre tre ruoli predefiniti per controllare le operazioni di gestione di Site Recovery.

- **Collaboratore di Site Recovery** - Questo ruolo ha tutte le autorizzazioni necessarie per gestire le operazioni di Azure Site Recovery in un insieme di credenziali di Servizi di ripristino. Un utente con questo ruolo, tuttavia, non può creare o eliminare un insieme di credenziali di Servizi di ripristino oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per gli amministratori del ripristino di emergenza che possono abilitare e gestire il ripristino di emergenza per le applicazioni o per intere organizzazioni.

- **Operatore di Site Recovery** - Questo ruolo ha le autorizzazioni per eseguire e gestire le operazioni di failover e failback. Un utente con questo ruolo non può abilitare o disabilitare la replica, creare o eliminare insiemi di credenziali, registrare una nuova infrastruttura oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per un operatore di ripristino di emergenza che può eseguire il failover di macchine virtuali o applicazioni quando richiesto dai proprietari delle applicazioni e dagli amministratori IT. In seguito alla risoluzione della situazione di emergenza, l'operatore di ripristino di emergenza può proteggere nuovamente le macchine virtuali ed eseguirne il failback.

- **Lettore di Site Recovery** - Questo ruolo ha le autorizzazioni per visualizzare tutte le operazioni di gestione di Site Recovery. Questo ruolo è ideale per un dirigente del monitoraggio IT che può controllare lo stato corrente di protezione e generare ticket di supporto.

Maggiori informazioni sui [ruoli predefiniti del Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>Abilitare la replica

### <a name="select-the-source"></a>Selezionare l'origine

1. In Insiemi di credenziali dei servizi di ripristino fare clic sul nome dell'insieme di credenziali e quindi selezionare **+Replica**.
2. In **Source** (Origine) selezionare **Azure**.
3. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
4. Selezionare il **modello di distribuzione delle macchine virtuali di Azure**: **Resource Manager** o **Classica**.
5. Selezionare la **sottoscrizione di origine** in cui sono in esecuzione le macchine virtuali. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
6. Selezionare il **Gruppo di risorse di origine** per le macchine virtuali di Resource Manager o il **Servizio cloud** per le macchine virtuali classiche.
7. Fare clic su **OK** per salvare le impostazioni.

### <a name="select-the-vms"></a>Selezionare le macchine virtuali

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse/servizio cloud.

1. In **Macchine virtuali** selezionare le macchine virtuali da replicare.
2. Fare clic su **OK**.

### <a name="configure-replication-settings"></a>Configurare le impostazioni di replica

Site Recovery crea le impostazioni predefinite e i criteri di replica per l'area di destinazione. È possibile modificare le impostazioni in base ai requisiti personali.

1. Fare clic su **Impostazioni** per visualizzare le impostazioni di destinazione e replica.
2. Per eseguire l'override delle impostazioni di destinazione predefinite, fare clic su **Personalizza** accanto a **Gruppo di risorse, rete, archiviazione e set di disponibilità**.

  ![Configurare le impostazioni](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione sarà uguale alla sottoscrizione di origine. Fare clic su "Personalizza" per selezionare una sottoscrizione di destinazione diversa nello stesso tenant di Azure Active Directory.

- **Percorso di destinazione**: area di destinazione usata per il ripristino di emergenza. È consigliabile che il percorso di destinazione corrisponda al percorso dell'insieme di credenziali di Site Recovery.

- **Gruppo di risorse di destinazione**: gruppo di risorse nell'area di destinazione in cui si trovano le macchine virtuali di Azure dopo il failover. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione con suffisso "asr". Il percorso del gruppo di risorse di destinazione può essere qualsiasi area, ad eccezione di quella in cui sono ospitate le macchine virtuali di origine.

- **Rete virtuale di destinazione**: rete nell'area di destinazione in cui si trovano le macchine virtuali dopo il failover.
  Per impostazione predefinita, Site Recovery crea una nuova rete virtuale e subnet nell'area di destinazione con suffisso "asr".

- **Account di archiviazione della cache**: Site Recovery usa un account di archiviazione nell'area di origine. Le modifiche apportate alle macchine virtuali di origine vengono inviate a questo account prima della replica nel percorso di destinazione.

- **Account di archiviazione di destinazione (se la VM di origine non usa i dischi gestiti)**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione nell'area di destinazione per eseguire il mirroring dell'account di archiviazione della macchina virtuale di origine.

- **Dischi gestiti di replica (se la VM di origine usa i dischi gestiti)**: per impostazione predefinita, Site Recovery crea dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine con lo stesso tipo di archiviazione (Standard o Premium) del disco gestito della VM di origine.

- **Set di disponibilità di destinazione**: per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione con il suffisso "asr". I set di disponibilità possono essere aggiunti solo se le VM fanno parte di un set nell'area di origine.

Per eseguire l'override delle impostazioni predefinite per i criteri di replica, fare clic su **Personalizza** accanto a **Criteri di replica**.  

- **Nome del criterio di replica**: nome del criterio.

- **Conservazione del punto di ripristino**: per impostazione predefinita, Site Recovery conserva i punti di ripristino per 24 ore. È possibile configurare un valore compreso tra 1 e 72 ore.

- **Frequenza snapshot coerenti con l'app**: per impostazione predefinita, Site Recovery accetta uno snapshot coerente con l'app ogni 4 ore. È possibile configurare un valore compreso tra 1 e 12 ore. Uno snapshot coerente con l'app è uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Il servizio Copia Shadow del volume assicura che lo stato dell'app nella macchina virtuale sia coerente quando viene creato lo snapshot.

- **Gruppo di replica**: se l'applicazione richiede la coerenza tra le macchine virtuali, è possibile creare un gruppo di replica per tali macchine virtuali. Per impostazione predefinita, le VM selezionate non fanno parte di gruppi di replica.

  Fare clic su **Personalizza** accanto a **Criteri di replica** e quindi selezionare **Sì** per la coerenza tra più VM per rendere le VM parte di un gruppo di replica. È possibile creare un nuovo gruppo di replica o usarne uno esistente. Selezionare le VM che dovranno far parte del gruppo di replica e fare clic su **OK**.

> [!IMPORTANT]
  Tutte le macchine virtuali in un gruppo di replica avranno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. L'abilitazione della coerenza tra più macchine virtuali può influire sulle prestazioni del carico di lavoro e deve essere usata solo se le macchine eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più macchine virtuali.

> [!IMPORTANT]
  Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. Verificare che nessuna appliance firewall blocchi la comunicazione interna tra VM sulla porta 20004. Se le VM Linux devono far parte di un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni della versione Linux specifica.

### <a name="configure-encryption-settings"></a>Configurare le impostazioni di crittografia

Se nella macchina virtuale di origine è abilitata la Crittografia dischi di Azure, verrà visualizzata la sezione seguente relativa alle impostazioni di crittografia.

- **Insiemi di credenziali delle chiavi di crittografia del disco**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia del disco della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato.
- **Insiemi di credenziali delle chiavi di crittografia della chiave**: per impostazione predefinita, Azure Site Recovery crea un nuovo insieme di credenziali delle chiavi nell'area di destinazione il cui nome contiene il suffisso "asr" basato sulle chiavi di crittografia della chiave della macchina virtuale di origine. Nel caso in cui l'insieme di credenziali delle chiavi creato da Azure Site Recovery esista già, verrà riutilizzato.

Fare clic su 'Personalizza' accanto alle impostazioni di crittografia per eseguire l'override delle impostazioni predefinite e selezionare insiemi di credenziali delle chiavi personalizzati.

>[!NOTE]
>Azure Site Recovery supporta attualmente solo macchine virtuali di Azure che eseguono il sistema operativo Windows e che sono [enabled abilitate per la crittografia con l'app Azure AD](https://aka.ms/ade-aad-app).
>

### <a name="track-replication-status"></a>Tenere traccia dello stato della replica

1. In **Impostazioni** fare clic su **Aggiorna** per ottenere lo stato più recente.

2. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**.

3. In **Impostazioni** > **Elementi replicati** è possibile visualizzare lo stato delle VM e lo stato della replica iniziale. Fare clic sulla VM per visualizzare nel dettaglio le rispettive impostazioni.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato il ripristino di emergenza per una macchina virtuale di Azure. Il passaggio successivo consiste nel testare la configurazione avviando un'esercitazione sul ripristino di emergenza.

> [!div class="nextstepaction"]
> [Eseguire un'esercitazione sul ripristino di emergenza](azure-to-azure-tutorial-dr-drill.md)
