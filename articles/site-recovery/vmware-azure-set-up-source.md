---
title: Configurare l'ambiente di origine per la replica da VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare l'ambiente locale per la replica di macchine virtuali VMware in Azure con Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952655"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurare l'ambiente di origine per la replica da VMware in Azure

Questo articolo descrive come configurare l'ambiente locale di origine per la replica di macchine virtuali VMware in Azure. Include i passaggi per la selezione dello scenario di replica, l'impostazione di un computer locale come server di configurazione di Site Recovery e l'individuazione automatica delle macchine virtuali locali. 

## <a name="prerequisites"></a>prerequisiti

Nell'articolo si presuppone che le seguenti procedure siano già state eseguite:
- [Configurare le risorse ](tutorial-prepare-azure.md) nel [portale di Azure](http://portal.azure.com).
- [Configurare istanze di VMware locali](vmware-azure-tutorial-prepare-on-premises.md), tra cui un account dedicato per l'individuazione automatica.



## <a name="choose-your-protection-goals"></a>Scegliere gli obiettivi della protezione

1. Nel portale di Azure accedere al pannello degli insiemi di credenziali di **Servizi di ripristino** e selezionare l'insieme di credenziali.
2. Nel menu delle risorse dell'insieme di credenziali passare ad **Attività iniziali** > **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.

    ![Scegliere gli obiettivi](./media/vmware-azure-set-up-source/choose-goals.png)
3. In **Obiettivo di protezione** selezionare **In Azure** e scegliere **Sì, con VMware vSphere Hypervisor**. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurare il server di configurazione

È possibile configurare il server di configurazione come una macchina virtuale VMware locale usando un modello OVA (Open Virtualization Application). [Altre informazioni](concepts-vmware-to-azure-architecture.md) sui componenti che verranno installati nella macchina virtuale VMware.

1. Informazioni sui [prerequisiti](vmware-azure-deploy-configuration-server.md#prerequisites) per la distribuzione del server di configurazione.
2. [Controllare i valori di capacità](vmware-azure-deploy-configuration-server.md#capacity-planning) per la distribuzione.
3. [Scaricare](vmware-azure-deploy-configuration-server.md#download-the-template) e [importare](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) il modello OVA per configurare una macchina virtuale VMware locale che esegue il server di configurazione. La licenza fornita con il modello è una licenza di valutazione ed è valida per 180 giorni. Passato questo periodo, il cliente deve attivare le finestre con una licenza acquistata.
4. Attivare la macchina virtuale VMware e [registrarla](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) nell'insieme di credenziali di Servizi di ripristino.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Aggiungere l'account VMware per l'individuazione automatica

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Connettersi al server VMware

Per consentire ad Azure Site Recovery di individuare macchine virtuali in esecuzione nell'ambiente locale, è necessario connettere il server VMware vCenter o gli host vSphere ESXi a Site Recovery.

Selezionare **+vCenter** per avviare la connessione di un server VMware vCenter o di un host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemi comuni
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Passaggi successivi
[Configurare l'ambiente di destinazione](./vmware-azure-set-up-target.md) in Azure.
