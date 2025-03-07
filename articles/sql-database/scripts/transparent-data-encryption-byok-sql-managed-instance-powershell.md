---
title: 'PowerShell: abilitare TDE per BYOK - Istanza gestita di database SQL di Azure | Microsoft Docs'
description: Informazioni su come configurare un'istanza gestita di SQL di Azure per iniziare a usare Transparent Data Encryption (TDE) per BYOK per la crittografia dei dati inattivi tramite PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569702"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>Gestire Transparent Data Encryption in un Istanza gestita usando la propria chiave da Azure Key Vault (anteprima)

Questo esempio di script di PowerShell configura Transparent Data Encryption (Transparent Data Encryption) in uno scenario di Bring Your Own Key (anteprima) per Istanza gestita SQL di Azure, usando una chiave Azure Key Vault. Per altre informazioni su TDE con il supporto Bring Your Own Key (BYOK), vedere [Bring Your Own Key di TDE per SQL Azure](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Prerequisiti

- Istanza gestita esistente. Vedere [usare PowerShell per creare un'istanza gestita di database SQL di Azure](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

L'uso di PowerShell in locale o con Azure Cloud Shell richiede AZ PowerShell 1.1.1-Preview o una versione di anteprima successiva. Se è necessario eseguire l'aggiornamento, vedere [install Azure PowerShell Module](/powershell/azure/install-az-ps)o eseguire lo script di esempio seguente per installare il modulo.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
