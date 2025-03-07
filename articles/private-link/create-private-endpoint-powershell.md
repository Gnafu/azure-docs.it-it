---
title: Creare un endpoint privato di Azure usando Azure PowerShell | Microsoft Docs
description: Informazioni sul collegamento privato di Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 569bc021d978714472bf40bcf39f7134fec95970
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130338"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Creare un endpoint privato usando Azure PowerShell
Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse di collegamento privato. 

In questa Guida introduttiva si apprenderà come creare una VM in una rete virtuale di Azure, un server di database SQL con un endpoint privato di Azure usando Azure PowerShell. Quindi, è possibile accedere in modo sicuro al server del database SQL dalla macchina virtuale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare le risorse, è necessario creare un gruppo di risorse che ospiti la rete virtuale e l'endpoint privato con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nella posizione *westus* :

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale
In questa sezione viene creata una rete virtuale e una subnet. Successivamente, associare la subnet alla rete virtuale.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale per l'endpoint privato con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *MyVirtualNetwork*:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Aggiungere una subnet

Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, pertanto è necessario creare una subnet. Creare una *configurazione di subnet denominata* subnet con [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig). Nell'esempio seguente viene creata una subnet *denominata subnet* con il flag di criteri di rete dell'endpoint privato impostato su **disabilitato**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associare la subnet alla rete virtuale

È possibile scrivere la configurazione della subnet nella rete virtuale con [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Questo comando crea la subnet:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM nella rete virtuale con [New-AzVM](/powershell/module/az.compute/new-azvm). Quando si esegue il comandi seguente, viene chiesto di immettere le credenziali. Immettere un nome utente e una password per la macchina virtuale:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

L'opzione `-AsJob` consente di creare la macchina virtuale in background. È possibile continuare con il passaggio successivo.

Quando Azure inizia a creare la macchina virtuale in background, il risultato sarà qualcosa di simile a quanto segue:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>Creare un server di database SQL 

Per creare un server di database SQL, usare il comando New-AzSqlServer. Tenere presente che il nome del server di database SQL deve essere univoco in Azure, quindi sostituire il valore del segnaposto tra parentesi quadre con il proprio valore univoco:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Creare un endpoint privato

Endpoint privato per il server di database SQL nella rete virtuale con [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Configurare la zona di DNS privato 
Creare una zona DNS privata per il dominio del server di database SQL e creare un collegamento di associazione con la rete virtuale: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. Questo esempio restituisce l'indirizzo IP pubblico della macchina virtuale *myVM* :

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Aprire un prompt dei comandi nel computer locale. Eseguire il comando mstsc.  <publicIpAddress>Sostituire con l'indirizzo IP pubblico restituito dall'ultimo passaggio: 


> [!NOTE]
> Se questi comandi sono in esecuzione da un prompt di PowerShell nel computer locale, con il modulo Az PowerShell versione 1.0 o successiva, è possibile continuare in tale interfaccia.
```
mstsc /v:<publicIpAddress>
```

1. Se richiesto, selezionare **Connetti**. 
2. Immettere il nome utente e la password specificati al momento della creazione della VM.
  > [!NOTE]
  > Potrebbe essere necessario selezionare altre opzioni > usare un account diverso per specificare le credenziali immesse durante la creazione della macchina virtuale. 
  
3. Fare clic su **OK**. 
4. Si potrebbe ricevere un avviso del certificato. In tal caso, selezionare **Sì** o **continua**. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>Accedere al server di database SQL privatamente dalla macchina virtuale

1. Nel Desktop remoto di myVM aprire PowerShell.
2. Immettere `nslookup myserver.database.windows.net`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install SQL Server Management Studio
4. In Connect to server, enter or select this information:
    Setting Value
      Server type   Select Database Engine.
      Server name   Select myserver.database.windows.net
      Username  Enter a username provided during creation.
      Password  Enter a password provided during creation.
      Remember password Select Yes.
5. Select Connect.
6. Browse Databases from left menu. 
7. (Optionally) Create or query information from mydatabase
8. Close the remote desktop connection to *myVM*. 

## Clean up resources 
When you're done using the private endpoint, SQL Database server and the VM, use [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) to remove the resource group and all the resources it has:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi
- Scopri di più sul [collegamento privato di Azure](private-link-overview.md)
