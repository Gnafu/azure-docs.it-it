---
title: Schema NetworkConfiguration dei Servizi cloud di Microsoft Azure| Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: c5a6501c71420ca5b43c4c08c2a29ae5f27b4af5
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945920"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Schema Config NetworkConfiguration dei Servizi cloud di Microsoft Azure

L'elemento `NetworkConfiguration` del file di configurazione del servizio specifica i valori Rete virtuale e DNS. Queste impostazioni sono facoltative per i servizi cloud.

Per altre informazioni sulle reti virtuali e sugli schemi associati, è possibile usare la risorsa seguente:

- [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Schema di configurazione di Servizi cloud - Versione classica)
- [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica)
- [Creare una Rete virtuale - Versione classica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
L'esempio seguente illustra l'elemento `NetworkConfiguration` e i relativi elementi figlio.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

La tabella seguente descrive gli elementi figlio dell'elemento `NetworkConfiguration`.

| Elemento       | Descrizione |
| ------------- | ----------- |
| AccessControl | facoltativo. Specifica le regole di accesso agli endpoint in un servizio cloud. Il nome del controllo di accesso è definito da una stringa per l'attributo `name`. L'elemento `AccessControl` contiene uno o più elementi `Rule`. È possibile definire più di un elemento `AccessControl`.|
| Regola | facoltativo. Specifica l'azione che deve essere eseguita per un intervallo di subnet specifico di indirizzi IP. L'ordine della regola è definito da un valore stringa per l'attributo `order`. Più è basso il numero della regola, maggiore sarà la priorità. Ad esempio, le regole possono essere specificate con numeri di ordine pari a 100, 200 e 300. La regola che ha il numero di ordine pari a 100 ha la precedenza sulla regola che ha un ordine pari a 200.<br /><br /> L'azione per la regola è definita da una stringa per l'attributo `action`. I valori possibili sono:<br /><br /> -   `permit`: specifica che solo i pacchetti dell'intervallo di subnet specificato possono comunicare con l'endpoint.<br />-   `deny`: specifica che l'accesso è negato agli endpoint nell'intervallo di subnet indicato.<br /><br /> L'intervallo di subnet degli indirizzi IP interessati dalla regola è definito da una stringa per l'attributo `remoteSubnet`. La descrizione per la regola è definita da una stringa per l'attributo `description`.|
| EndpointAcl | facoltativo. Specifica l'assegnazione delle regole di controllo di accesso a un endpoint. Il nome del ruolo che contiene l'endpoint è definito da una stringa per l'attributo `role`. Il nome dell'endpoint è definito da una stringa per l'attributo `endpoint`. Il nome del set di regole `AccessControl` che devono essere applicate all'endpoint è definito da una stringa per l'attributo `accessControl`. È possibile definire più di un elemento `EndpointAcl`.|
| DnsServer | facoltativo. Specifica le impostazioni per un server DNS. È possibile specificare le impostazioni per i server DNS senza una Rete virtuale. Il nome del server DNS è definito da una stringa per l'attributo `name`. L'indirizzo IP del server DNS è definito da una stringa per l'attributo `IPAddress`. L'indirizzo IP deve essere un indirizzo IPv4 valido.|
| VirtualNetworkSite | facoltativo. Specifica il nome del sito della Rete virtuale in cui si desidera implementare il servizio cloud. Questa impostazione non comporta la creazione di un sito della Rete virtuale. Fa riferimento a un sito che è già stato definito nel file di rete per la Rete virtuale. Un servizio cloud può essere solo un membro di una Rete virtuale. Se non si specifica questa impostazione, il servizio cloud non verrà implementato in una Rete virtuale. Il nome del sito della Rete virtuale è definito da una stringa per l'attributo `name`.|
| InstanceAddress | facoltativo. Specifica l'associazione di un ruolo a una subnet o a un set di subnet nella Rete virtuale. Quando si associa un nome di ruolo all'indirizzo di un'istanza, è possibile specificare le subnet a cui si desidera associare questo ruolo. `InstanceAddress` contiene un elemento Subnet. Il nome del ruolo associato a una o più subnet è definito da una stringa per l'attributo `roleName`.|
| Subnet | facoltativo. Specifica la subnet che corrisponde al nome della subnet nel file di configurazione di rete. Il nome della subnet è definito da una stringa per l'attributo `name`.|
| ReservedIP | facoltativo. Specifica l'indirizzo IP riservato che deve essere associato alla distribuzione. È necessario usare Crea indirizzo IP riservato per creare l'indirizzo IP riservato. Ogni distribuzione in un servizio cloud può essere associata a un indirizzo IP riservato. Il nome dell'indirizzo IP riservato è definito da una stringa per l'attributo `name`.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Schema di configurazione di Servizi cloud - Versione classica)
