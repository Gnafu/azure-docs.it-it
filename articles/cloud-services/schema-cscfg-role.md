---
title: Schema del ruolo di Servizi cloud di Azure | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 2f5c657bb80ad0788bcc3dd19d962b3f21afa4a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359000"
---
# <a name="azure-cloud-services-config-role-schema"></a>Schema del ruolo di configurazione di Servizi cloud di Azure

L'elemento `Role` del file di configurazione specifica il numero di istanze del ruolo da distribuire per ogni ruolo nel servizio, i valori delle impostazioni di configurazione e le identificazioni personali per i certificati associati a un ruolo.

Per altre informazioni sullo schema di configurazione di Servizi cloud di Azure, vedere [Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Schema di configurazione di Servizi cloud - Versione classica). Per altre informazioni sullo schema di definizione di Servizi cloud di Azure, vedere [Cloud Service (classic) Definition Schema](schema-csdef-file.md) (Schema di definizione di Servizi cloud - Versione classica).

##  <a name="Role"></a>Elemento Role
L'esempio seguente illustra l'elemento `Role` e i relativi elementi figlio.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

La tabella seguente descrive gli attributi dell'elemento `Role`.

| Attributo | DESCRIZIONE |
| --------- | ----------- |
| name   | Richiesto. Specifica il nome del ruolo. Il nome deve corrispondere al nome specificato per il ruolo nel file di definizione del servizio.|
| vmName | facoltativo. Specifica il nome DNS per una macchina virtuale. Il nome non può contenere più di 10 caratteri.|

La tabella seguente descrive gli elementi figlio dell'elemento `Role`.

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| Istanze | Richiesto. Specifica il numero di istanze da distribuire per il ruolo. Il numero di istanze è definito da un numero intero per l'attributo `count`.|
| Impostazione   | facoltativo. Specifica un nome e un valore per l'impostazione in una raccolta di impostazioni per un ruolo. Il nome dell'impostazione è definito da una stringa per l'attributo `name` e il valore dell'impostazione è definito da una stringa per l'attributo `value`.|
| Certificate | facoltativo. Specifica il nome, l'identificazione personale e l'algoritmo del certificato di servizio che deve essere associato al ruolo. Il nome del certificato è definito da una stringa per l'attributo `name`. L'identificazione personale del certificato è definita da una stringa di numeri esadecimali senza spazi per l'attributo `thumbprint`. I numeri esadecimali devono essere rappresentati usando cifre e caratteri alfabetici maiuscoli. L'algoritmo del certificato è definito da una stringa per l'attributo `thumbprintAlgorithm`.|

## <a name="see-also"></a>Vedere anche
[Cloud Service (classic) Configuration Schema](schema-cscfg-file.md) (Schema di configurazione di Servizi cloud - Versione classica)