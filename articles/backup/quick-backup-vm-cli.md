---
title: Avvio rapido di Azure - Eseguire il backup di una macchina virtuale con l'interfaccia della riga di comando di Azure
description: Informazioni su come eseguire il backup delle macchine virtuali con l'interfaccia della riga di comando di Azure
author: dcurwin
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 0a0718387962f677184df85ef95d303a128d9166
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874683"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Eseguire il backup di una macchina virtuale in Azure con l'interfaccia della riga di comando
L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. È possibile proteggere i dati eseguendo backup a intervalli regolari. Backup di Azure crea punti di ripristino che possono essere archiviati in insiemi di credenziali di ripristino con ridondanza geografica. Questo articolo illustra in modo dettagliato come eseguire il backup di una macchina virtuale (VM) in Azure con l'interfaccia della riga di comando di Azure. È anche possibile eseguire questa procedura con [Azure PowerShell](quick-backup-vm-powershell.md) o nel [portale di Azure](quick-backup-vm-portal.md).

Questa guida introduttiva abilita il backup in una VM di Azure esistente. Se necessario, è possibile [creare una VM con l'interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, è necessario eseguire l'interfaccia della riga di comando di Azure 2.0.18 o versioni successive Per determinare la versione dell'interfaccia della riga di comando, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali dei servizi di ripristino
Un insieme di credenziali dei servizi di ripristino è un contenitore logico in cui vengono archiviati i dati di backup per ogni risorsa protetta, ad esempio per le VM di Azure. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Creare un insieme di credenziali dei servizi di ripristino con [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Specificare lo stesso gruppo di risorse e la stessa località della VM da proteggere. Se è stata usata la [guida introduttiva sulle macchine virtuali](../virtual-machines/linux/quick-create-cli.md), sono stati creati gli elementi seguenti:

- Un gruppo di risorse denominato *myResourceGroup*.
- Una macchina virtuale denominata *myVM*.
- Le risorse nella località *Stati Uniti orientali*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Per impostazione predefinita, l'insieme di credenziali di Servizi di ripristino è impostato per l'archiviazione con ridondanza geografica. Con l'archiviazione con ridondanza geografica i dati di backup vengono replicati in un'area di Azure secondaria a centinaia di chilometri di distanza dall'area primaria. Se è necessario modificare l'impostazione della ridondanza dell'archiviazione, usare il cmdlet [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet.

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant" 
```


## <a name="enable-backup-for-an-azure-vm"></a>Abilitare il backup per una VM di Azure
Creare criteri di protezione per definire quando deve essere eseguito un processo di backup e per quanto tempo devono essere archiviati i punti di ripristino. I criteri di protezione predefiniti eseguono un processo di backup ogni giorno e conservano i punti di ripristino per 30 giorni. È possibile usare questi valori dei criteri predefiniti per proteggere rapidamente la VM. Per abilitare la protezione dei backup per una VM, usare [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Specificare il gruppo di risorse e la VM da proteggere e quindi i criteri da usare:

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Se la macchina virtuale non è inclusa nello stesso gruppo di risorse dell'insieme di credenziali, myResourceGroup fa riferimento al gruppo di risorse in cui è stato creato l'insieme di credenziali. Invece del nome della macchina virtuale, specificare l'ID della macchina virtuale come indicato di seguito.

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> Quando si usa l'interfaccia della riga di comando per abilitare il backup simultaneo di più macchine virtuali, assicurarsi che a un singolo criterio non siano associate più di 100 VM. Si tratta di una [procedura consigliata](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy). Attualmente, il client PS non si blocca in modo esplicito se sono presenti più di 100 macchine virtuali, ma per il futuro è pianificata l'aggiunta del controllo.

## <a name="start-a-backup-job"></a>Avviare un processo di backup
Per avviare subito un backup anziché attendere che il processo venga eseguito dai criteri predefiniti all'ora pianificata, usare [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now). Il primo processo di backup crea un punto di ripristino completo. Tutti i processi di backup successivi a questo backup iniziale creano punti di ripristino incrementali. I punti di ripristino incrementali sono veloci ed efficienti in termini di archiviazione, perché trasferiscono solo le modifiche eseguite dopo l'ultimo backup.

Per il backup della VM vengono usati i parametri seguenti:

- `--container-name` è il nome della VM
- `--item-name` è il nome della VM
- Il valore di `--retain-until` deve essere impostato sull'ultima data in cui si vuole che sia disponibile il punto di ripristino, in formato UTC (**gg-mm-aaaa**)

Questo esempio esegue il backup della VM denominata *myVM* e imposta la scadenza del punto di ripristino sul 18 ottobre 2017:

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Monitorare il processo di backup
Per monitorare lo stato dei processi di backup, usare [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

L'output è simile all'esempio seguente, che mostra che il processo di backup è *InProgress*:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando nella colonna *Status* del processo di backup è riportato *Completed*, la VM è protetta con Servizi di ripristino e ha un punto di ripristino completo archiviato.


## <a name="clean-up-deployment"></a>Pulire la distribuzione
Quando non è più necessaria, è possibile disabilitare la protezione per la VM, rimuovere i punti di ripristino e l'insieme di credenziali dei servizi di ripristino e quindi eliminare il gruppo di risorse e le risorse della VM associate. Se è stata usata una VM esistente, si può ignorare il comando [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) finale e mantenere così il gruppo di risorse e la VM.

Se si vuole provare un'esercitazione di Backup che descrive come ripristinare i dati per la macchina virtuale, vedere [Passaggi successivi](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stato creato un insieme di credenziali di Servizi di ripristino, è stata abilitata la protezione per una VM ed è stato creato il punto di ripristino iniziale. Per altre informazioni su Backup e Servizi di ripristino di Azure, proseguire con le esercitazioni.

> [!div class="nextstepaction"]
> [Eseguire il backup di più macchine virtuali di Azure](./tutorial-backup-vm-at-scale.md)
