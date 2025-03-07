---
title: 'Azure Active Directory Domain Services: Creare un account del servizio gestito del gruppo | Microsoft Docs'
description: Informazioni su come creare un account del servizio gestito del gruppo (gMSA) per l'uso con Azure Active Directory Domain Services domini gestiti
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: 3742aed7ff39e0a2f6bdf353fb9f261176027422
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612950"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Creare un account del servizio gestito del gruppo in un dominio gestito di Azure AD Domain Services
Questo articolo illustra come creare account del servizio gestiti in un dominio gestito di Azure AD Domain Services.

## <a name="managed-service-accounts"></a>Account del servizio gestiti
Un account del servizio gestito autonomo è un account di dominio gestito la cui password viene gestita automaticamente. Semplifica la gestione del nome dell'entità servizio (SPN) e consente la gestione delegata agli altri amministratori. Questo tipo di account del servizio gestito è stato introdotto in Windows Server 2008 R2 e Windows 7.

L'account del servizio gestito del gruppo (gMSA, group-Managed Service Account) fornisce gli stessi vantaggi per più server del dominio. Tutte le istanze di un servizio ospitato in una server farm devono usare la stessa entità servizio per consentire il funzionamento dei protocolli di autenticazione reciproca. Quando un account del servizio gestito del gruppo viene usato come entità servizio, è il sistema operativo Windows, non l'amministratore, a gestire la password dell'account.

**Altre informazioni:**
- [Panoramica degli account del servizio gestiti del gruppo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Introduzione agli account del servizio gestiti del gruppo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Uso degli account del servizio in Azure AD Domain Services
I domini gestiti di Azure AD Domain Services vengono bloccati e gestiti da Microsoft. È bene fare due considerazioni importanti quando si usano gli account del servizio con Azure AD Domain Services.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>Creare gli account del servizio all'interno di unità organizzative (OU) personalizzate nel dominio gestito
Non è possibile creare un account del servizio nelle unità organizzative "AADDC Users" o "AADDC Computers" predefinite. [Creare un'unità organizzativa personalizzata](create-ou.md) nel dominio gestito e quindi creare gli account del servizio in tale unità organizzativa personalizzata.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>La chiave radice del Servizio distribuzione chiavi (KDS) viene creata anticipatamente
La chiave radice del Servizio di distribuzione chiavi (KDS, Key Distribution Services) viene creata anticipatamente in un dominio gestito di Azure AD Domain Services. Non è necessario creare una chiave radice KDS né avere i privilegi per farlo. Non è neppure possibile visualizzare la chiave radice nel dominio gestito.

## <a name="sample---create-a-gmsa-using-powershell"></a>Esempio: Creare un account del servizio gestito del gruppo con PowerShell
L'esempio seguente illustra come creare un'unità organizzativa personalizzata con PowerShell. È quindi possibile creare un account del servizio gestito del gruppo in tale unità organizzativa usando il parametro ```-Path``` per specificare l'unità organizzativa.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=contoso,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso.com  `
-Path "OU=MYNEWOU,DC=contoso,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
http/WebFarmSvc.contoso.com/contoso,  `
http/WebFarmSvc/contoso.com, http/WebFarmSvc/contoso  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**Documentazione dei cmdlet di PowerShell:**
- [New-ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit) (Cmdlet New-ADOrganizationalUnit)
- [New-ADServiceAccount cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount) (Cmdlet New-ADServiceAccount)


## <a name="next-steps"></a>Passaggi successivi
- [Creare un'unità organizzativa personalizzata in un dominio gestito](create-ou.md)
- [Panoramica degli account del servizio gestiti del gruppo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [Introduzione agli account del servizio gestiti del gruppo](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
