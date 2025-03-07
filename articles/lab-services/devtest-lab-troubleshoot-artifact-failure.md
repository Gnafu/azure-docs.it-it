---
title: Diagnosticare gli errori degli elementi in una macchina virtuale di Azure DevTest Labs | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi agli elementi in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991373"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticare errori di elementi nel lab 
Dopo aver creato un elemento, è possibile verificare se l'operazione ha avuto esito positivo o negativo. I log degli elementi in Azure DevTest Labs forniscono informazioni che è possibile usare per diagnosticare un errore di un elemento. Ci sono due modi per visualizzare le informazioni del log degli elementi per una macchina virtuale Windows:

* Nel portale di Azure
* Nella macchina virtuale

> [!NOTE]
> Per garantire che gli errori vengano identificati e descritti correttamente, è importante che l'elemento abbia la struttura corretta. Per informazioni su come costruire correttamente un elemento, vedere [Creare elementi personalizzati](devtest-lab-artifact-author.md). Per vedere un esempio di elemento con struttura corretta, vedere l'elemento di [test dei tipi di parametri](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Risolvere i problemi relativi agli elementi usando il portale di Azure

1. Nel portale di Azure, nell'elenco delle risorse, selezionare il proprio lab.
2. Scegliere la macchina virtuale Windows che include l'elemento da analizzare.
3. Nel pannello sinistro sotto **GENERALE** scegliere **Elementi**. Verrà visualizzato un elenco di elementi associati alla macchina virtuale. Sono indicati il nome e lo stato dell'elemento.

   ![Stato dell'elemento](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selezionare un elemento con lo stato **Failed**. L'elemento verrà aperto. Verrà visualizzato un messaggio di estensione che include i dettagli sull'errore dell'elemento.

   ![Messaggio di errore dell'elemento](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Risolvere i problemi degli elementi nella macchina virtuale

1. Accedere alla macchina virtuale che contiene l'elemento da analizzare.
2. Passare a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, dove *1.9* è il numero di versione dell'estensione di script personalizzata di Azure.

   ![File di stato](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Aprire il file di **stato**.

Per istruzioni su come trovare i file di log in una macchina virtuale **Linux** , vedere l'articolo seguente: [Usare l'estensione di script personalizzata di Azure versione 2 con macchine virtuali Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Post di blog correlati
* [Join a VM to an existing Active Directory domain by using a Resource Manager template in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Aggiungere una macchina virtuale a un dominio di Active Directory esistente usando un modello di Resource Manager in DevTest Labs)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio Git a un lab](devtest-lab-add-artifact-repo.md).

