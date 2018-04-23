---
title: Passare un oggetto JSON a un runbook di Automazione di Azure
description: Come passare i parametri a un runbook come un oggetto JSON
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
keywords: powershell, runbook, json, automazione di azure
ms.openlocfilehash: 43f11b6e89b45fae94d0f7b10f0348468593956e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Passare un oggetto JSON a un runbook di Automazione di Azure

Può essere utile archiviare i dati che si desidera passare a un runbook in un file JSON.
Ad esempio, è possibile creare un file JSON che contiene tutti i parametri da passare a un runbook.
A tale scopo, è necessario convertire il JSON in una stringa e quindi convertire la stringa in un oggetto PowerShell prima di passare il relativo contenuto al runbook.

In questo esempio si creerà uno script di PowerShell che chiama [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) per avviare un runbook PowerShell, passando il contenuto di JSON al runbook.
Il runbook PowerShell avvia una VM di Azure, ottenendo i parametri per la VM dal file JSON che è stato passato.

## <a name="prerequisites"></a>prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure <a href="/pricing/free-account/" target="_blank">[iscriversi per ottenere un account gratuito](https://azure.microsoft.com/free/).
* [Account di Automazione](automation-sec-configure-azure-runas-account.md) che conterrà il runbook ed eseguirà l'autenticazione con le risorse di Azure.  Questo account deve avere l'autorizzazione per avviare e arrestare la macchina virtuale.
* Macchina virtuale di Azure. La macchina virtuale viene arrestata e avviata, quindi non deve essere una macchina virtuale di produzione.
* Azure Powershell installato in un computer locale. Per informazioni sulla configurazione di PowerShell, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0).

## <a name="create-the-json-file"></a>Creare il file JSON

Digitare il seguente test in un file di testo e salvarlo come `test.json` in un punto qualsiasi nel computer locale.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Creare il runbook

Creare un nuovo runbook di PowerShell denominato "Test-Json" in Automazione di Azure.
Per informazioni su come creare un nuovo runbook di PowerShell, vedere [Il primo runbook PowerShell](automation-first-runbook-textual-powershell.md).

Per accettare i dati JSON, il runbook deve accettare un oggetto come parametro di input.

Il runbook può quindi usare le proprietà definite nel file JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Salvare e pubblicare il runbook nell'account di automazione.

## <a name="call-the-runbook-from-powershell"></a>Chiamare il runbook da PowerShell

Ora è possibile chiamare il runbook dal computer locale tramite Azure PowerShell.
Eseguire i comandi di PowerShell seguenti:

1. Accedere ad Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Viene richiesto di immettere le credenziali di Azure.
1. Ottenere il contenuto del file JSON e convertirlo in una stringa:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` è il percorso in cui è stato salvato il file JSON.
1. Convertire i contenuti stringa di `$json` in un oggetto di PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Creare una tabella hash per i parametri per `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Si noti che si imposta il valore di `Parameters` sull'oggetto PowerShell che contiene i valori dal file JSON. 
1. Avviare il runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Il runbook usa i valori del file JSON per avviare una VM.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla modifica di runbook di PowerShell e del flusso di lavoro PowerShell con un editor di testo, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md) 
* Per altre informazioni su come creare o importare un runbook, vedere [Creazione o importazione di un runbook in Automazione di Azure](automation-creating-importing-runbook.md)


