---
title: Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nel servizio Frontdoor di Azure | Microsoft Docs
description: Se si usa un certificato proprio per abilitare HTTPS in un dominio personalizzato, è necessario usare un'autorità di certificazione (CA) consentita per crearlo.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: de709133099674a0aa0386113b6459f8bc05e378
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046109"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nel servizio Frontdoor di Azure

Per un dominio personalizzato del servizio Frontdoor di Azure, quando si [abilita la funzionalità HTTPS usando un certificato personale](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

Le CA seguenti sono consentite per la creazione di un certificato proprio:

- AddTrust External CA Root
- AP Root CA
- AP Root Certificate Authority 2013
- AP Root Certificate Authority 2014
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority - G2
- GlobalSign
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority - G2
- Microsoft Authenticode(tm) Root Authority
- Microsoft Exchange Services CA 2015
- Microsoft Internal Corporate Root
- Microsoft IT ITO SSL CA 1
- Microsoft IT SSL SHA1
- Microsoft IT SSL SHA2
- Microsoft IT TLS CA 1
- Microsoft IT TLS CA 2
- Microsoft IT TLS CA 4
- Microsoft IT TLS CA 5
- Microsoft Root Authority
- Microsoft Root Certificate Authority
- Microsoft Root Certificate Authority 2010
- Microsoft Root Certificate Authority 2011
- Microsoft Secure Server CA 2011
- Microsoft Services Partner Root
- Microsoft Time Stamping Service Root
- Microsoft Windows Hardware Compatibility
- MSIT CA Z2
- MSIT Enterprise CA 1
- MSIT Enterprise CA 3
- Root Agency
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA - G2
- Thawte Primary Root CA - G3
- Thawte Timestamping CA
- UTN-USERFirst-Object
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
- WMSvc-SHA2-DALEDGE1008
