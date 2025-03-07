---
title: Foglio informativo su XPath per la configurazione del ruolo di Servizi cloud | Documentazione Microsoft
description: Varie impostazioni di XPath che è possibile usare nella configurazione del ruolo del servizio cloud per esporre le impostazioni come una variabile di ambiente.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: cd2bdc4fc4b2a135907851ca4d3034430618e0cd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359013"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Esporre le impostazioni di configurazione del ruolo come una variabile di ambiente con XPath
Nel file di definizione del servzio del ruolo di lavoro o del ruolo Web del servizio cloud è possibile esporre i valori di configurazione di runtime come variabili di ambiente. Sono supportati i valori XPath seguenti, che corrispondono ai valori di API.

Questi valori XPath sono disponibili anche tramite la libreria [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>App in esecuzione nell'emulatore
Indica che l'app è in esecuzione nell'emulatore.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Codice |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID distribuzione
Recupera l'ID distribuzione per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Codice |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID ruolo
Recupera l'ID del ruolo corrente per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Codice |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Aggiornamento dominio
Recupera il dominio di aggiornamento dell'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Codice |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Dominio di errore
Recupera il dominio di errore dell'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Codice |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome del ruolo
Recupera il nome del ruolo dell'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Codice |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Impostazione di configurazione
Recupera il valore dell'impostazione di configurazione specificata.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Codice |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Percorso di archiviazione locale
Recupera il percorso di archiviazione locale per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Codice |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Dimensioni di archiviazione locale
Recupera le dimensioni di archiviazione locale per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Codice |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocollo di endpoint
Recupera il protocollo di endpoint per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Codice |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>IP dell'endpoint
Ottiene l'indirizzo IP dell'endpoint specificato.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Codice |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta dell'endpoint
Recupera la porta dell'endpoint per l'istanza.

| Type | Esempio |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Codice |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>Esempio
Ecco un esempio di un ruolo di lavoro che crea un'attività di avvio con una variabile di ambiente denominata `TestIsEmulated` impostata sul valore [@emulated xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul file [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Creare un pacchetto [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Abilitare [Desktop remoto](cloud-services-role-enable-remote-desktop-new-portal.md) per un ruolo.

