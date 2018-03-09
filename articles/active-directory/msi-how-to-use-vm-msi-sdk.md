---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure con Azure SDK"
description: "Esempi di codice per l'uso di Azure SDK con un'identità del servizio gestito di una macchina virtuale di Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: bd2f03f47cebec52aecb84ef2e97a745ede670ff
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Come usare un'identità del servizio gestito di una macchina virtuale di Azure con Azure SDK 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Questo articolo fornisce un elenco di esempi di SDK, che illustrano l'uso del supporto di Azure SDK per l'identità del servizio gestito.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Tutti gli esempi di codice e script in questo articolo presuppongono che il client sia in esecuzione in una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito in una macchina virtuale, vedere [Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo (per PowerShell, interfaccia della riga di comando, un modello o una versione di Azure SDK). 

## <a name="sdk-code-samples"></a>Esempi di codice SDK

| SDK             | Esempio di codice |
| --------------- | ----------- |
| .NET            | [Deploy an Azure Resource Manager template from a Windows VM using Managed Service Identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) (Distribuire un modello di Azure Resource Manager da una macchina virtuale Windows tramite identità del servizio gestito) |
| .NET Core       | [Call Azure services from a Linux VM using Managed Service Identity](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) (Chiamare servizi Azure da una macchina virtuale Linux tramite identità del servizio condiviso) |
| Node.js         | [Manage resources using Managed Service Identity](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) (Gestire le risorse usando l'identità del servizio gestito) |
| Python          | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Usare l'identità del servizio gestito per eseguire in modo semplice l'autenticazione in una macchina virtuale) |
| Ruby            | [Manage resources from an MSI-enabled VM](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) (Gestire le risorse da una macchina virtuale abilitata per l'identità del servizio gestito) |

## <a name="related-content"></a>Contenuti correlati

- Vedere [Azure SDK](https://azure.microsoft.com/downloads/) per l'elenco completo delle risorse di Azure SDK, inclusi i download di librerie, la documentazione e altro ancora.
- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








