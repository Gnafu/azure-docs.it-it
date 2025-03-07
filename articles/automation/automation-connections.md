---
title: Asset di connessione in Automazione di Azure
description: Gli asset di connessione di Automazione di Azure contengono le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una connessione DSC. Questo articolo illustra nel dettaglio le connessioni e spiega come usarle nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6b1f9cbececcf02962cdde9741764999a920abf8
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478668"
---
# <a name="connection-assets-in-azure-automation"></a>Asset di connessione in Automazione di Azure

Un asset di connessione di Automazione contiene le informazioni necessarie per la connessione a un servizio esterno o a un'applicazione da un Runbook o una configurazione DSC. ad esempio le informazioni necessarie per l'autenticazione, quali nome utente e password, oltre alle informazioni di connessione quali un URL o una porta. Il valore di una connessione consiste nel mantenere tutte le proprietà per la connessione a un'applicazione specifica in un singolo asset, invece di creare più variabili. L'utente può modificare i valori per una connessione in un'unica posizione e può passare il nome di una connessione a un Runbook o a una configurazione DSC in un singolo parametro. È possibile accedere alle proprietà per una connessione nel Runbook o nella configurazione DSC con l'attività **Get-AutomationConnection** . 

Quando si crea una connessione, è necessario specificare un *tipo di connessione*. Il tipo di connessione è un modello che definisce un set di proprietà. La connessione definisce i valori per ogni proprietà definita nel rispettivo tipo di connessione. I tipi di connessione vengono aggiunti ad Automazione di Azure nei moduli di integrazione oppure creati con l'[API di Automazione di Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) se il modulo di integrazione include un tipo di connessione e viene importato nell'account di Automazione. In caso contrario, sarà necessario creare un file di metadati per specificare un tipo di connessione di Automazione.  Per ulteriori informazioni, vedere [Moduli di integrazione](automation-integration-modules.md).  

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave è archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito dall'Automazione di Azure.

## <a name="connection-types"></a>Tipi di connessione

Sono disponibili tre tipi di connessioni predefiniti in Automazione di Azure:

* **Azure** - questa connessione può essere usata per gestire le risorse classiche.
* **AzureClassicCertificate** - questa connessione è usata dall'account **AzureClassicRunAs**.
* **AzureServicePrincipal** - questa connessione è usata dall'account **AzureRunAs**.

Nella maggior parte dei casi non è necessario creare una risorsa di connessione poiché viene creata quando si crea un [account RunAs](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Cmdlet di Windows PowerShell

I cmdlet della tabella seguente vengono usati per creare e gestire connessioni di automazione con Windows PowerShell. Sono inclusi nel [modulo Azure PowerShell](/powershell/azure/overview) , disponibile per l'uso nei runbook di Automazione e nelle configurazioni DSC.

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Recupera una connessione. Include una tabella hash con i valori dei campi della connessione.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Crea una nuova connessione.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Rimuove una connessione esistente.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Imposta il valore di un campo specifico per una connessione esistente.|

## <a name="activities"></a>Attività

Le attività incluse nella tabella seguente vengono usate per accedere alle connessioni in un Runbook o configurazione DSC.

|Attività|Descrizione|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Ottiene una connessione da usare. Restituisce una tabella hash con le proprietà della connessione.|

>[!NOTE] 
>È consigliabile evitare di usare le variabili con il parametro –Name di **Get- AutomationConnection**, poiché ciò può complicare l'individuazione delle dipendenze tra i Runbook o configurazioni DSC e gli asset di connessione durante la fase di progettazione.

 
## <a name="python2-functions"></a>Funzioni Python2 
La funzione nella tabella seguente viene usata per accedere alle connessioni in un runbook Python2. 

| Funzione | Descrizione | 
|:---|:---| 
| automationassets.get_automation_connection | Recupera una connessione. Restituisce un dizionario con le proprietà della connessione. | 

> [!NOTE] 
> È necessario importare il modulo "automationassets" nella parte superiore del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-connection"></a>Creazione di una nuova connessione

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Per creare una nuova connessione con il portale di Azure

1. Dall'account di automazione fare clic sulla parte **Asset** per aprire il pannello **Asset**.
2. Fare clic sulla parte **Connessioni** per aprire il pannello **Connessioni**.
3. Fare clic su **Aggiungi connessione** nella parte superiore del pannello.
4. Dall'elenco a discesa **Tipo** selezionare il tipo di connessione da creare. Il modulo presenta le proprietà per il tipo di connessione specifico.
5. Completare il modulo e fare clic su **Crea** per salvare la nuova connessione.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Per creare una nuova connessione con Windows PowerShell

Creare una nuova connessione con Windows PowerShell usando il cmdlet [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection). Questo cmdlet ha un parametro denominato **ConnectionFieldValues** che prevede una [tabella hash](https://technet.microsoft.com/library/hh847780.aspx) che definisce i valori per ogni proprietà definita dal tipo di connessione.

Se si ha familiarità con l'[account RunAs](automation-sec-configure-azure-runas-account.md) di Automazione per autenticare i runbook usando l'entità servizio, è possibile usare lo script di PowerShell fornito come alternativa alla creazione dell'account RunAs dal portale per creare un nuovo asset di connessione tramite i semplici comandi seguenti.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

È possibile usare lo script per creare un asset di connessione perché, quando si crea l'account di Automazione, questo include automaticamente e per impostazione predefinita diversi moduli globali insieme al tipo di connessione **AzureServicePrincipal**, per creare l'asset di connessione **AzureRunAsConnection**.  È un fattore importante da tenere presente, poiché se si tenta di creare un nuovo asset di connessione a un servizio o a un'applicazione con un metodo di autenticazione diverso, la creazione avrà esito negativo in quanto il tipo di connessione non è già definito nell'account di Automazione.  Per ulteriori informazioni su come creare un tipo di connessione personalizzata adatta al modulo da [PowerShell Gallery](https://www.powershellgallery.com), vedere [Moduli di integrazione](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Uso di una connessione in un Runbook o in una configurazione DSC

Il cmdlet **Get-AutomationConnection** permette di recuperare una connessione in un Runbook o in una configurazione DSC.  Non è possibile usare l'attività [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection).  Questa attività recupera i valori dei diversi campi nella connessione e li restituisce come [tabella hash](https://go.microsoft.com/fwlink/?LinkID=324844) , che può essere quindi usata con i comandi appropriati nel Runbook o nella configurazione DSC.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

I comandi di esempio seguenti mostrano come usare l'account RunAs menzionato in precedenza per autenticarsi con le risorse di Azure Resource Manager nel proprio Runbook.  Usa l'asset di connessione che rappresenta l'account RunAs, il quale fa riferimento all'entità servizio basata sui certificati, non alle credenziali.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount** è ora un alias per **Connect-AzureRMAccount**. Quando si esegue la ricerca tra gli elementi della libreria, se **Connect-AzureRMAccount** non viene visualizzato, è possibile usare **Add-AzureRmAccount** oppure aggiornare i moduli nell'account di Automazione.

### <a name="graphical-runbook-samples"></a>Esempi di runbook grafici

Per aggiungere un'attività **Get-AutomationConnection** a un Runbook grafico, fare clic con il pulsante destro del mouse sulla connessione nel riquadro della libreria dell'editor grafico e scegliere **Aggiungi a area di disegno**.

![Aggiunta all'area di disegno](media/automation-connections/connection-add-canvas.png)

La figura seguente mostra un esempio dell'uso di una connessione in un Runbook grafico.  Si tratta dello stesso esempio mostrato in precedenza per l'autenticazione usando l'account RunAs con un Runbook testuale.  In questo esempio viene usato il set di dati **Valore costante** per l'attività **Get RunAs Connection** (Ottieni connessione RunAs) che usa un oggetto di connessione per l'autenticazione.  Viene usato un [collegamento pipeline](automation-graphical-authoring-intro.md#links-and-workflow) , poiché il parametro ServicePrincipalCertificate impostato prevede un singolo oggetto.

![Recupero delle connessioni](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Esempio di runbook Python2
L'esempio seguente illustra come eseguire l'autenticazione tramite la connessione Run As in un runbook Python2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets


def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Passaggi successivi

- Consultare la sezione [Collegamenti nella creazione grafica](automation-graphical-authoring-intro.md#links-and-workflow) per informazioni su come dirigere e controllare il flusso di logica nei runbook.  

- Vedere [Moduli di integrazione](automation-integration-modules.md) per ulteriori informazioni sull'uso dei moduli di PowerShell da parte di Automazione di Azure e per conoscere le procedure consigliate per creare i propri moduli di PowerShell come moduli di integrazione in Automazione di Azure.  

