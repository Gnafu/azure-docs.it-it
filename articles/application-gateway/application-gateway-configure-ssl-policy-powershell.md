---
title: Configurare i criteri SSL nel gateway applicazione di Azure - PowerShell
description: Questo articolo contiene istruzioni per configurare i criteri SSL nel gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/3/2018
ms.author: victorh
ms.openlocfilehash: a93a3db3010fb9e9d2270414cadeae86b70bb8d0
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157463"
---
# <a name="configure-ssl-policy-versions-and-cipher-suites-on-application-gateway"></a>Configurare i pacchetti di crittografia e le versioni dei criteri SSL nel gateway applicazione

Questo articolo illustra come configurare i pacchetti di crittografia e le versioni dei criteri SSL nel gateway applicazione. È possibile scegliere da un [elenco di criteri predefiniti](#predefined-ssl-policies) contenenti diverse configurazioni dei pacchetti di crittografia abilitati e delle versioni dei criteri SSL. Si possono anche definire [criteri SSL personalizzati](#configure-a-custom-ssl-policy) in base ai propri requisiti.

## <a name="get-available-ssl-options"></a>Recuperare le opzioni SSL disponibili

Il cmdlet `Get-AzureRMApplicationGatewayAvailableSslOptions` offre un elenco dei criteri predefiniti, dei pacchetti di crittografia disponibili e delle versioni del protocollo configurabili. L'esempio seguente illustra un esempio di output ottenuto eseguendo il cmdlet.

```
DefaultPolicy: AppGwSslPolicy20150501
PredefinedPolicies:
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20150501
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401
    /subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups//providers/Microsoft.Network/ApplicationGatewayAvailableSslOptions/default/Applic
ationGatewaySslPredefinedPolicy/AppGwSslPolicy20170401S

AvailableCipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
    TLS_RSA_WITH_AES_128_GCM_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA256
    TLS_RSA_WITH_AES_128_CBC_SHA256
    TLS_RSA_WITH_AES_256_CBC_SHA
    TLS_RSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
    TLS_DHE_DSS_WITH_AES_256_CBC_SHA
    TLS_DHE_DSS_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_3DES_EDE_CBC_SHA
    TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

AvailableProtocols:
    TLSv1_0
    TLSv1_1
    TLSv1_2
```

## <a name="list-pre-defined-ssl-policies"></a>Visualizzare un elenco dei criteri SSL predefiniti

Il gateway applicazione include tre criteri predefiniti utilizzabili. Il cmdlet `Get-AzureRmApplicationGatewaySslPredefinedPolicy` recupera tali criteri. Ognuno include versioni del protocollo e pacchetti di crittografia abilitati diversi. Questi criteri predefiniti possono essere usati per configurare rapidamente i criteri SSL nel gateway applicazione. Per impostazione predefinita, se non vengono definiti criteri SSL specifici è selezionato il criterio **AppGwSslPolicy20150501**.

Di seguito è riportato un esempio di esecuzione di `Get-AzureRmApplicationGatewaySslPredefinedPolicy`.

```
Name: AppGwSslPolicy20150501
MinProtocolVersion: TLSv1_0
CipherSuites:
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
    TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
    TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
    TLS_DHE_RSA_WITH_AES_256_CBC_SHA
    TLS_DHE_RSA_WITH_AES_128_CBC_SHA
    TLS_RSA_WITH_AES_256_GCM_SHA384
 ...
Name: AppGwSslPolicy20170401
MinProtocolVersion: TLSv1_1
CipherSuites:
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
...
```

## <a name="configure-a-custom-ssl-policy"></a>Configurare criteri SSL personalizzati

Quando si configura un criterio SSL personalizzato, si passano i parametri seguenti: PolicyType, MinProtocolVersion, CipherSuite e ApplicationGateway. Se si prova a passare altri parametri, viene visualizzato un errore quando si crea o si aggiorna il gateway applicazione. 

L'esempio seguente imposta criteri SSL personalizzati in un gateway applicazione. Imposta la versione minima del protocollo su `TLSv1_1` e abilita i pacchetti di crittografia seguenti:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

> [!IMPORTANT]
> Quando si configurano criteri SSL personalizzati, è necessario selezionare almeno un pacchetto di crittografia dell'elenco seguente. Il gateway applicazione usa il pacchetto di crittografia RSA SHA256 per la gestione del back-end.
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
> * TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_RSA_WITH_AES_128_GCM_SHA256
> * TLS_RSA_WITH_AES_256_CBC_SHA256
> * TLS_RSA_WITH_AES_128_CBC_SHA256

```powershell
# get an application gateway resource
$gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroup AdatumAppGatewayRG

# set the SSL policy on the application gateway
Set-AzureRmApplicationGatewaySslPolicy -ApplicationGateway $gw -PolicyType Custom -MinProtocolVersion TLSv1_1 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"

# validate the SSL policy locally
Get-AzureRmApplicationGatewaySslPolicy -ApplicationGateway $gw

# update the gateway with validated SSL policy
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

## <a name="create-an-application-gateway-with-a-pre-defined-ssl-policy"></a>Creare un gateway applicazione con criteri SSL personalizzati

Quando si configura un criterio SSL predefinito, si passano i parametri seguenti: PolicyType, PolicyName e ApplicationGateway. Se si prova a passare altri parametri, viene visualizzato un errore quando si crea o si aggiorna il gateway applicazione.

L'esempio seguente crea un nuovo gateway applicazione con criteri SSL predefiniti.

```powershell
# Create a resource group
$rg = New-AzureRmResourceGroup -Name ContosoRG -Location "East US"

# Create a subnet for the application gateway
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network with a 10.0.0.0/16 address space
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for later use
$subnet = $vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location "East US" -AllocationMethod Dynamic

# Create a ip configuration object
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool for backend web servers
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

# Define the backend http settings to be used.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

# Create a new port for SSL
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

# Upload an existing pfx certificate for SSL offload
$password = ConvertTo-SecureString -String "P@ssw0rd" -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile C:\folder\contoso.pfx -Password $password

# Create a frontend IP configuration for the public IP address
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener with the certificate, port, and frontend ip.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a new rule for backend traffic routing
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the size of the application gateway
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Configure the SSL policy to use a different pre-defined policy
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Create the application gateway.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName $rg.ResourceGroupName -Location "East US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

## <a name="update-an-existing-application-gateway-with-a-pre-defined-ssl-policy"></a>Aggiornare un gateway applicazione con criteri SSL predefiniti

Per impostare un criterio SSL personalizzato, passare i parametri seguenti: **PolicyType**, **MinProtocolVersion**, **CipherSuite** e **ApplicationGateway**. Per impostare un criterio SSL predefinito, passare i parametri seguenti: **PolicyType**, **PolicyName** e **ApplicationGateway**. Se si prova a passare altri parametri, viene visualizzato un errore quando si crea o si aggiorna il gateway applicazione.

Nell'esempio seguente sono disponibili esempi di codice sia per il criterio personalizzato che per il criterio predefinito. Rimuovere il commento dal criterio che si vuole usare.

```powershell
# You have to change these parameters to match your environment.
$AppGWname = "YourAppGwName"
$RG = "YourResourceGroupName"

$AppGw = get-azurermapplicationgateway -Name $AppGWname -ResourceGroupName $RG

# Choose either custom policy or predefined policy and uncomment the one you want to use.

# SSL Custom Policy
# Set-AzureRmApplicationGatewaySslPolicy -PolicyType Custom -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_RSA_WITH_AES_128_CBC_SHA256" -ApplicationGateway $AppGw

# SSL Predefined Policy
# Set-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S" -ApplicationGateway $AppGW

# Update AppGW
# The SSL policy options are not validated or updated on the Application Gateway until this cmdlet is executed.
$SetGW = Set-AzureRmApplicationGateway -ApplicationGateway $AppGW
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come reindirizzare il traffico HTTP a un endpoint HTTPS, vedere [Panoramica del reindirizzamento nel gateway applicazione](application-gateway-redirect-overview.md).
