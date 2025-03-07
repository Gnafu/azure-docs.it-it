---
title: Rete per i set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Configurazione delle proprietà della rete per i set di scalabilità di macchine virtuali di Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: manayar
ms.openlocfilehash: a9141adfb1dd05efd73061379be89ddf27ab3832
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803055"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Rete per i set di scalabilità di macchine virtuali di Azure

Quando si distribuisce un set di scalabilità di macchine virtuali di Azure tramite il portale, determinate proprietà della rete sono predefinite, ad esempio un'istanza di Azure Load Balancer con regole NAT in ingresso. Questo articolo descrive come usare alcune delle funzionalità più avanzate della rete che è possibile configurare con i set di scalabilità.

È possibile configurare tutte le funzionalità illustrate in questo articolo usando i modelli di Azure Resource Manager. Sono inclusi anche esempi dell'interfaccia della riga di comando di Azure e di PowerShell per le funzionalità selezionate.

## <a name="accelerated-networking"></a>Rete accelerata
La rete accelerata di Azure migliora le prestazioni di rete abilitando Single-Root I/O Virtualization (SR-IOV) per le macchine virtuali. Per altre informazioni sull'uso della rete accelerata, vedere Rete accelerata per macchine virtuali [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) o [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Per usare la rete accelerata con i set di scalabilità, impostare enableAcceleratedNetworking su **true** nelle impostazioni networkInterfaceConfigurations del set di scalabilità, Ad esempio:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Creare un set di scalabilità che faccia riferimento a un'istanza di Azure Load Balancer esistente
Quando viene creato un set di scalabilità usando il portale di Azure, viene creato un nuovo servizio di bilanciamento del carico per la maggior parte delle opzioni di configurazione. Se si crea un set di scalabilità, che deve fare riferimento a un servizio di bilanciamento del carico esistente, è possibile eseguire questa operazione tramite l'interfaccia della riga di comando. Lo script di esempio seguente crea un servizio di bilanciamento del carico e quindi crea un set di scalabilità che vi fa riferimento:
```bash
az network lb create \
    -g lbtest \
    -n mylb \
    --vnet-name myvnet \
    --subnet mysubnet \
    --public-ip-address-allocation Static \
    --backend-pool-name mybackendpool

az vmss create \
    -g lbtest \
    -n myvmss \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username negat \
    --ssh-key-value /home/myuser/.ssh/id_rsa.pub \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myvnet \
    --subnet mysubnet \
    --lb mylb \
    --backend-pool-name mybackendpool
```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Creare un set di scalabilità che fa riferimento a un gateway applicazione
Per creare un set di scalabilità che usa un gateway applicazione, fare riferimento al pool di indirizzi back-end del gateway applicazione nella sezione Configurazioni IP del set di scalabilità, come in questa configurazione ARM modello:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Si noti che il gateway applicazione deve trovarsi nella stessa rete virtuale del set di scalabilità, ma in una subnet diversa.


## <a name="configurable-dns-settings"></a>Impostazioni DNS configurabili
Per impostazione predefinita, ai set di scalabilità vengono applicate le impostazioni DNS specifiche della rete virtuale e della subnet in cui sono state create. È tuttavia possibile configurare direttamente le impostazioni DNS per un set di scalabilità.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Creazione di un set di scalabilità con server DNS configurabili
Per creare un set di scalabilità con una configurazione DNS personalizzata usando l'interfaccia della riga di comando di Azure, aggiungere l'argomento **--dns-servers** al comando **vmss create**, facendo seguire l'argomento dagli indirizzi IP dei server separati da spazi. Ad esempio:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Per configurare server DNS personalizzati in un modello di Azure, aggiungere una proprietà dnsSettings alla sezione networkInterfaceConfigurations del set di scalabilità, Ad esempio:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Creazione di un set di scalabilità con nomi di dominio di macchine virtuali configurabili
Per creare un set di scalabilità con un nome DNS personalizzato per le macchine virtuali usando l'interfaccia della riga di comando di Azure, aggiungere l'argomento **--vm-domain-name** al comando di **creazione di set di scalabilità di macchine virtuali** , facendo seguire l'argomento da una stringa che rappresenta il nome di dominio.

Per impostare il nome di dominio in un modello di Azure, aggiungere una proprietà **dnsSettings** alla sezione **networkInterfaceConfigurations** del set di scalabilità, Ad esempio:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

L'output, per un singolo nome DNS di macchina virtuale, avrà il formato seguente: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 pubblico per macchina virtuale
Per le macchine virtuali di un set di scalabilità di Azure in genere non sono necessari indirizzi IP pubblici specifici. Per la maggior parte degli scenari, risulta più economico e sicuro associare un indirizzo IP pubblico a un servizio di bilanciamento del carico o a una singola macchina virtuale (jumpbox), che quindi instrada le connessioni in ingresso alle macchine virtuali del set di scalabilità in base alle esigenze (ad esempio, tramite regole NAT in ingresso).

Alcuni scenari tuttavia richiedono che le macchine virtuali del set di scalabilità abbiano i propri indirizzi IP pubblici, ad esempio i giochi, in cui una console deve stabilire una connessione diretta a una macchina virtuale cloud, che esegue l'elaborazione fisica del gioco. Un altro esempio è quello in cui le macchine virtuali devono stabilire connessioni esterne reciproche tra aree in un database distribuito.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Creazione di un set di scalabilità con un IP pubblico per ogni macchina virtuale
Per creare un set di scalabilità che assegni un indirizzo IP pubblico a ogni macchina virtuale con l'interfaccia della riga di comando di Azure, aggiungere il parametro **--public-ip-per-vm** al comando **vmss create**. 

Per creare un set di scalabilità usando un modello di Azure, verificare che la versione API della risorsa Microsoft.Compute/virtualMachineScaleSets sia almeno **2017-03-30** e aggiungere una proprietà JSON **publicIpAddressConfiguration** alla sezione ipConfigurations del set di scalabilità, Ad esempio:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Modello di esempio: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Query degli indirizzi IP pubblici delle macchine virtuali in un set di scalabilità
Per elencare gli indirizzi IP pubblici assegnati alle macchine virtuali del set di scalabilità usando l'interfaccia della riga di comando di Azure, eseguire il comando **az vmss list-instance-public-ips**.

Per ottenere un elenco degli indirizzi IP pubblici del set di scalabilità con PowerShell, usare il comando _Get-AzPublicIpAddress_. Ad esempio:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

È anche possibile eseguire una query sugli indirizzi IP pubblici facendo direttamente riferimento all'ID risorsa della configurazione degli indirizzi IP pubblici, Ad esempio:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

È anche possibile visualizzare gli indirizzi IP pubblici assegnati alle macchine virtuali del set di scalabilità eseguendo una query di [Azure Resource Explorer](https://resources.azure.com) o dell'API REST di Azure con versione **2017-03-30** o successiva.

Per eseguire una query di [Azure Resource Explorer](https://resources.azure.com):

1. Aprire [Azure Resource Explorer](https://resources.azure.com) in un Web browser.
1. Per espandere *Sottoscrizioni* sul lato sinistro, fare clic su *+* . Se in *Sottoscrizioni* è elencato un solo elemento, potrebbe essere già espanso.
1. Espandere la sottoscrizione.
1. Espandere il gruppo di risorse.
1. Espandere i *provider*.
1. Espandere *Microsoft.Compute*.
1. Espandere *virtualMachineScaleSets*.
1. Espandere il set di scalabilità.
1. Fare clic su *publicipaddresses*.

Per eseguire una query nell'API REST di Azure:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Esempio di output da [Azure Resource Explorer](https://resources.azure.com) e dall'API REST di Azure:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Più indirizzi IP per ogni scheda di interfaccia di rete
Ogni scheda di interfaccia di rete collegata a una macchina virtuale in un set di scalabilità può avere una o più configurazioni IP associate. A ogni configurazione viene assegnato un indirizzo IP privato. Ogni configurazione può anche avere una risorsa di indirizzo IP pubblico associata. Per sapere quanti indirizzi IP possono essere assegnati a una scheda di interfaccia di rete e quanti indirizzi IP pubblici è possibile usare in una sottoscrizione di Azure, vedere [Limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Più schede di interfaccia di rete per ogni macchina virtuale
È possibile avere fino a 8 schede di interfaccia di rete per ogni macchina virtuale, a seconda delle dimensioni del computer. Il numero massimo di schede di interfaccia di rete per computer è disponibile nell'[articolo sulle dimensioni per le VM](../virtual-machines/windows/sizes.md). Tutte le schede di interfaccia di rete connesse a un'istanza di macchina virtuale devono connettersi alla stessa rete virtuale. Le schede di interfaccia di rete possono connettersi a subnet diverse, ma tutte le subnet devono far parte della stessa rete virtuale.

L'esempio seguente è un profilo di rete del set di scalabilità che mostra più voci di schede di interfaccia di rete e più IP pubblici per ogni macchina virtuale:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Gruppi di sicurezza di rete e gruppi di sicurezza delle applicazioni per set di scalabilità
I [gruppi di sicurezza di rete](../virtual-network/security-overview.md) permettono di filtrare il traffico da e verso risorse di Azure in una rete virtuale di Azure usando le regole di sicurezza. I [gruppi di sicurezza delle applicazioni](../virtual-network/security-overview.md#application-security-groups) consentono di gestire la sicurezza di rete delle risorse di Azure e raggrupparle come estensione della struttura dell'applicazione.

I gruppi di sicurezza di rete possono essere applicati direttamente a un set di scalabilità, aggiungendo un riferimento alla sezione della configurazione dell'interfaccia di rete delle proprietà delle macchine virtuali del set di scalabilità.

I gruppi di sicurezza delle applicazioni possono essere specificati anche direttamente in un set di scalabilità, aggiungendo un riferimento alla sezione delle configurazioni IP dell'interfaccia di rete delle proprietà delle macchine virtuali del set di scalabilità.

Ad esempio: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Per verificare che il gruppo di sicurezza di rete sia associato al set di scalabilità, usare il comando `az vmss show`. L'esempio seguente usa `--query` per filtrare i risultati e mostra solo la relativa sezione dell'output.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Per verificare che il gruppo di sicurezza dell'applicazione sia associato al set di scalabilità, usare il comando `az vmss show`. L'esempio seguente usa `--query` per filtrare i risultati e mostra solo la relativa sezione dell'output.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle reti virtuali di Azure, vedere la [panoramica delle reti virtuali di Azure](../virtual-network/virtual-networks-overview.md).
