---
title: Errori della console seriale di Azure | Microsoft Docs
description: Errori comuni nella console seriale di Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129666"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Errori comuni nella console seriale di Azure
Nella console seriale di Azure è presente un set di errori noti. Si tratta di un elenco di questi errori e dei relativi passaggi di mitigazione.

## <a name="common-errors"></a>Errori comuni

Errore                            |   Attenuazione
:---------------------------------|:--------------------------------------------|
Impossibile recuperare le impostazione di diagnostica di avvio per *&lt;VMNAME&gt;* . Per usare la console seriale, assicurarsi che la diagnostica di avvio sia abilitata per questa macchina virtuale. ![Errore di diagnostica di avvio](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Verificare che la VM o il set di scalabilità di macchine virtuali disponga della [diagnostica di avvio](boot-diagnostics.md) abilitata. Se si usa la console seriale in un'istanza del set di scalabilità di macchine virtuali, assicurarsi che l'istanza disponga del modello più recente.
La macchina virtuale è in uno stato arrestato deallocato. Avviare la VM e provare a stabilire di nuovo la connessione alla console seriale. ![Errore deallocato](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Per accedere alla console seriale, è necessario che l'istanza della macchina virtuale o del set di scalabilità di macchine virtuali sia in stato di avvio. Avviare la VM o l'istanza del set di scalabilità di macchine virtuali e riprovare.
È stata rilevata una risposta "Accesso negato" durante l'accesso all'account di archiviazione di diagnostica di avvio della macchina virtuale. ![Errore del firewall dell'account di archiviazione](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Assicurarsi che la diagnostica di avvio non disponga di un firewall dell'account. Un account di archiviazione di diagnostica di avvio accessibile è necessario per il funzionamento della console seriale.
Non si hanno le autorizzazioni necessarie per usare questa macchina virtuale con la console seriale. Assicurarsi di avere almeno le autorizzazioni del ruolo Collaboratore Macchina virtuale.| L'accesso alla console seriale richiede l'accesso a livello di collaboratore o superiore nella VM o nel set di scalabilità di macchine virtuali. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
L'account di archiviazione '' usato per la diagnostica di avvio in questa macchina virtuale non è stato trovato. Verificare che la diagnostica di avvio sia abilitata per questa macchina virtuale, che l'account di archiviazione non sia stato eliminato e di avere accesso a questo account di archiviazione. | Verificare che non sia stato eliminato l'account di archiviazione della diagnostica di avvio per la macchina virtuale o il set di scalabilità di macchine virtuali
Il provisioning per questa macchina virtuale non è stato ancora completato. Assicurarsi che la macchina virtuale sia completamente distribuita e ripetere la connessione alla console seriale. | È ancora possibile eseguire il provisioning della macchina virtuale o del set di scalabilità di macchine virtuali. Attendere del tempo e riprovare.
Non si dispone delle autorizzazioni necessarie per scrivere nell'account di archiviazione della diagnostica di avvio per questa macchina virtuale. Assicurarsi di avere almeno le autorizzazioni di collaboratore macchina virtuale per ''. | Console seriale l'accesso richiede l'accesso a livello di collaboratore nell'account di archiviazione della diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
Impossibile determinare il gruppo di risorse per l'account di archiviazione della diagnostica di avvio *&lt;STORAGEACCOUNTNAME&gt;* . Verificare che la diagnostica di avvio sia abilitata per questa VM e di avere accesso a questo account di archiviazione. | Console seriale l'accesso richiede l'accesso a livello di collaboratore nell'account di archiviazione della diagnostica di avvio. Per ulteriori informazioni, vedere la [pagina Panoramica](serial-console-overview.md).
Il Web socket è chiuso o non può essere aperto. | Potrebbe essere necessario aggiungere l'accesso al firewall `*.console.azure.com`a. Un approccio più dettagliato ma più lungo consiste nel consentire l'accesso del firewall agli [intervalli IP del data center Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), che cambiano abbastanza regolarmente.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [console seriale di Azure per macchine virtuali Linux](./serial-console-linux.md)
* Altre informazioni sulla [console seriale di Azure per le macchine virtuali Windows](./serial-console-windows.md)