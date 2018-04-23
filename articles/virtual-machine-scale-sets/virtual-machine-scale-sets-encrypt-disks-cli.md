---
title: Crittografia dischi di Azure per set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni su come crittografare i dischi collegati in un set di scalabilità di macchine virtuali.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Crittografare il sistema operativo e i dischi dati collegati in un set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) di Azure supportano Crittografia dischi di Azure.  La crittografia del disco di Azure può essere abilitata per i set di scalabilità di macchine virtuali di Windows e Linux per proteggere e tutelare i dati inattivi dei set di scalabilità tramite la tecnologia di crittografia standard di settore. Per altre informazioni, leggere l'articolo Crittografia dischi di Azure per macchine virtuali Windows e Linux.

> [!NOTE]
>  Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è attualmente disponibile come anteprima pubblica in tutte le aree pubbliche di Azure.

Crittografia dischi di Azure è supportata per le risorse seguenti:
- Set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
- Volumi di dati e sistema operativo nei set di scalabilità Windows. La disabilitazione della crittografia è supportata per volumi di dati e sistema operativo nei set di scalabilità Windows.
- Volumi di dati nei set di scalabilità Linux. Nell'anteprima corrente la crittografia del disco del sistema operativo NON è supportata per i set di scalabilità Linux.

Le operazioni di creazione di una nuova immagine e di aggiornamento delle macchine virtuali dei set di scalabilità non sono supportate nell'anteprima corrente. È consigliabile usare l'anteprima di Crittografia dischi di Azure per i set di scalabilità di macchine virtuali solo in ambienti di test. Nell'anteprima, non abilitare la crittografia del disco in ambienti di produzione in cui potrebbe essere necessario eseguire l'aggiornamento di un'immagine del sistema operativo in un set di scalabilità crittografato.

Un esempio di file batch end-to-end per la crittografia del disco dati del set di scalabilità di Linux è disponibile [qui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat).  In questo esempio vengono creati un gruppo di risorse e il set di scalabilità di Linux, viene montato un disco dati di 5 GB e viene crittografato il set di scalabilità di macchine virtuali.

## <a name="prerequisites"></a>prerequisiti
Installare le versioni più recenti di [Azure Powershell](https://github.com/Azure/azure-powershell/releases) o l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), che contiene i comandi di crittografia.

Crittografia dischi di Azure per l'anteprima di set di scalabilità di macchine virtuali richiede di effettuare la registrazione automatica della sottoscrizione tramite i comandi di PowerShell seguenti: 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Attendere circa 10 minuti fino a quando non viene restituito lo stato "Registrato" dal comando seguente: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creare un insieme di credenziali delle chiavi di Azure abilitato per la crittografia dei dischi
Creare un nuovo insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area del set di scalabilità e impostare il criterio di accesso "EnabledForDiskEncryption".

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

In alternativa, abilitare un insieme di credenziali delle chiavi esistente nella stessa sottoscrizione e nella stessa area del set di scalabilità per la crittografia dei dischi.

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Abilitare la crittografia

I comandi seguenti eseguono la crittografia di un disco dati in un set di scalabilità in esecuzione tramite un insieme di credenziali delle chiavi nello stesso gruppo di risorse. Per eseguire la crittografia dei dischi in un [set di scalabilità Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o un [set di scalabilità Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) in esecuzione, è possibile anche usare modelli.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>Verificare lo stato della crittografia

Usare i comandi seguenti per visualizzare lo stato della crittografia del set di scalabilità.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>Disabilitare la crittografia
Disabilitare la crittografia in un set di scalabilità di macchine virtuali in esecuzione usando i comandi seguenti. È possibile anche usare i modelli per disabilitare la crittografia in un [set di scalabilità Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) o un [set di scalabilità Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) in esecuzione.

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

