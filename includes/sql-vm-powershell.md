---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165785"
---
## <a name="start-your-powershell-session"></a>Avviare la sessione di PowerShell
 

Eseguire il cmdlet [**Connect-Az Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

```powershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni, usare il cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare la sottoscrizione in uso nella sessione corrente di PowerShell, eseguire [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Per visualizzare tutte le sottoscrizioni, eseguire [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

