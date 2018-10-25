---
title: 'Esercitazione: Disponibilità elevata per le macchine virtuali Linux in Azure | Microsoft Docs'
description: In questa esercitazione si apprenderà come usare l'interfaccia della riga di comando di Azure per distribuire macchine virtuali a disponibilità elevata nei set di disponibilità
documentationcenter: ''
services: virtual-machines-linux
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: f7bf5e233307703dca522974d52a86bc193186b8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465832"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Esercitazione: Creare e distribuire macchine virtuali a disponibilità elevata con l'interfaccia della riga di comando di Azure

In questa esercitazione si apprenderà come aumentare la disponibilità e l'affidabilità delle soluzioni delle proprie macchine virtuali in Azure tramite una funzionalità denominata set di disponibilità. I set di disponibilità assicurano che le macchine virtuali distribuite in Azure vengano distribuite tra più cluster hardware isolati. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e che nel complesso la soluzione rimane disponibile e operativa.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

Un set di disponibilità è una funzionalità di raggruppamento logico che è possibile usare in Azure per garantire che le risorse delle macchine virtuali inserite dall'utente siano isolate tra loro quando vengono distribuite all'interno di un data center di Azure. Azure garantisce che le macchine virtuali inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. In caso di guasto hardware o errore software in Azure, viene interessato solo un subset delle macchine virtuali. L'applicazione nel suo complesso rimarrà attiva e disponibile per i clienti. I set di disponibilità sono una funzionalità essenziale da sfruttare quando si vogliono creare soluzioni cloud affidabili.

Si consideri una soluzione tipica basata su macchine virtuali, in cui sono disponibili quattro server Web front-end e vengono usate due macchine virtuali di back-end che ospitano un database. Con Azure è possibile definire due set di disponibilità prima di distribuire le macchine virtuali: un set di disponibilità per il livello "Web" e un set di disponibilità per il livello "database". Quando si crea una nuova macchina virtuale, è quindi possibile specificare il set di disponibilità come parametro per il comando az vm create. Azure garantisce automaticamente che le macchine virtuali create all'interno del set di disponibilità vengano isolate tramite installazione in più risorse hardware fisiche. Se l'hardware fisico in cui è in esecuzione una delle macchine virtuali dei server di database o dei server Web presenta un problema, le altre istanze delle macchine virtuali dei server Web e di database rimangono in esecuzione, perché si trovano all'interno di risorse hardware diverse.

È consigliabile usare i set di disponibilità quando si vogliono distribuire soluzioni affidabili basate su macchine virtuali all'interno di Azure.


## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità usando il comando [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create). In questo esempio il numero di domini di aggiornamento e di errore viene impostato su *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.

Creare prima un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create), quindi creare il set di disponibilità:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

I set di disponibilità consentono di isolare le risorse in domini di errore e in domini di aggiornamento. Un **dominio di errore** rappresenta una raccolta isolata di server + rete + risorse di archiviazione. Nell'esempio precedente il set di disponibilità viene distribuito in almeno due domini di errore quando vengono distribuite le macchine virtuali. Il set di disponibilità viene distribuito anche in due **domini di aggiornamento**. Due domini di aggiornamento assicurano che, quando vengono eseguiti automaticamente gli aggiornamenti software, le risorse delle macchine virtuali siano isolate, impedendo che tutto il software in esecuzione nelle macchine virtuali venga aggiornato contemporaneamente.


## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità

Per garantire la corretta distribuzione delle macchine virtuali in tutto l'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una macchina virtuale esistente a un set di disponibilità dopo la sua creazione.

Quando si crea una macchina virtuale con [az vm create](/cli/azure/vm#az_vm_create), usare il parametro `--availability-set` per specificare il nome del set di disponibilità.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Sono ora disponibili due macchine virtuali all'interno del set di disponibilità. Poiché si trovano nello stesso set di disponibilità, Azure assicura che le macchine virtuali e tutte le relative risorse (inclusi i dischi dati) vengano distribuite in risorse hardware fisiche isolate. Questa distribuzione consente di garantire una disponibilità molto maggiore della soluzione complessiva delle macchine virtuali.

La distribuzione del set di disponibilità può essere esaminata nel portale accedendo a Gruppi di risorse > myResourceGroupAvailability > myAvailabilitySet. Le macchine virtuali vengono distribuite tra i due domini di errore e di aggiornamento, come illustrato nell'esempio seguente:

![Set di disponibilità nel portale](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili

È possibile aggiungere più macchine virtuali al set di disponibilità in un secondo momento, se le dimensioni delle macchine virtuali sono disponibili nell'hardware. Usare il comando [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) per elencare tutte le dimensioni disponibili nel cluster hardware per il set di disponibilità:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)
