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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ebc64215683989ce07f4dd88dc352ecaefe184cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697273"
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

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati
* [Join a VM to an existing Active Directory domain by using a Resource Manager template in DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Aggiungere una macchina virtuale a un dominio di Active Directory esistente usando un modello di Resource Manager in DevTest Labs)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio Git a un lab](devtest-lab-add-artifact-repo.md).

