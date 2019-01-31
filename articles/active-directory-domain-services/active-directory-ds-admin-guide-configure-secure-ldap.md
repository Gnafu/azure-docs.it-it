---
title: Configurare l'accesso LDAP sicuro (LDAPS) in Azure Active Directory Domain Services | Microsoft Docs
description: Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Servizi di dominio Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 66c50d7ec9948b1f2447aadcef4a4a8f321204ef
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177391"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurare l'accesso LDAP sicuro (LDAPS) per un dominio gestito di Azure AD Domain Services
Questo articolo illustra come abilitare l'accesso LDAPS (Secure Lightweight Directory Access Protocol) per il dominio gestito di Servizi di dominio Azure AD. L'accesso LDAP sicuro è noto anche come LDAP (Lightweight Directory Access Protocol) su SSL (Secure Sockets Layer) / TLS (Transport Layer Security).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:

1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Un **certificato da usare per abilitare l'accesso LDAP sicuro**.

   * È **consigliabile** ottenere un certificato da un'autorità di certificazione pubblica attendibile. Questa opzione di configurazione è più sicura.
   * In alternativa, è anche possibile scegliere di [creare un certificato autofirmato](#task-1---obtain-a-certificate-for-secure-ldap) , come illustrato più avanti in questo articolo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisiti per il certificato LDAP sicuro
Acquisire un certificato valido in base alle linee guida riportate di seguito prima di abilitare l'accesso LDAP sicuro. Se si prova ad abilitare l'accesso LDAP sicuro per il dominio gestito con un certificato non valido o non corretto, si verificano errori.

1. **Autorità di certificazione attendibile** : il certificato deve essere emesso da un'autorità ritenuta attendibile dai computer connessi al dominio gestito tramite accesso LDAP sicuro. L'autorità può essere un'autorità di certificazione pubblica o globale (enterprise) considerata attendibile da questi computer.
2. **Durata** : il certificato deve essere valido almeno per i 3-6 mesi successivi. L'accesso LDAP sicuro al dominio gestito viene interrotto allo scadere del certificato.
3. **Nome soggetto** : il nome del soggetto nel certificato deve includere il dominio gestito. Ad esempio, se il dominio è denominato "contoso100.com", il nome del soggetto nel certificato deve essere "contoso100.com". Impostare il nome DNS (nome soggetto alternativo) su un nome con caratteri jolly per il dominio gestito.
4. **Utilizzo chiavi** : il certificato deve essere configurato per l'uso nelle firme digitali e nella crittografia a chiave.
5. **Scopo del certificato** : il certificato deve essere valido per l'autenticazione server SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Attività 1: Ottenere un certificato per l'accesso LDAP sicuro
La prima attività consiste nell'ottenere un certificato da usare per l'accesso LDAP sicuro al dominio gestito. Sono disponibili due opzioni:

* Ottenere un certificato da un' autorità di certificazione pubblica o globale (enterprise).
* Creare un certificato autofirmato.

> [!NOTE]
> I computer client che devono connettersi al dominio gestito tramite l'accesso LDAP sicuro devono considerare attendibile l'autorità emittente del certificato LDAP sicuro.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opzione A (scelta consigliata): ottenere un certificato LDAP sicuro da un'autorità di certificazione
Se l'organizzazione ottiene i propri certificati da un'autorità di certificazione pubblica, è necessario ottenere il certificato LDAP sicuro da quella autorità di certificazione pubblica. Se si distribuisce un'autorità di certificazione globale (enterprise), ottenere il certificato LDAP sicuro da quella autorità di certificazione globale (enterprise).

> [!TIP]
> **Usare i certificati autofirmati per domini gestiti con i suffissi di dominio ".onmicrosoft.com".**
> Se il nome di dominio DNS del dominio gestito termina con ".onmicrosoft.com", è impossibile ottenere un certificato LDAP sicuro da un'autorità di certificazione pubblica. Poiché Microsoft è il proprietario del dominio ".onmicrosoft.com", le autorità di certificazione pubbliche rifiutano di emettere un certificato LDAP sicuro per l'utente per un dominio con questo suffisso. In questo scenario, creare un certificato autofirmato e usarlo per configurare l'accesso LDAP sicuro.
>

Verificare che il certificato ottenuto dall'autorità di certificazione pubblica soddisfi tutti i requisiti descritti in [Requisiti per il certificato LDAP sicuro](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opzione B: creare un certificato autofirmato per l'accesso LDAP sicuro
Se non si prevede di usare un certificato di un'autorità di certificazione pubblica, è possibile scegliere di creare un certificato autofirmato per LDAP sicuro. Selezionare questa opzione se il nome di dominio DNS del dominio gestito termina con ".onmicrosoft.com".

**Creare un certificato autofirmato con PowerShell**

Per creare un nuovo certificato autofirmato in un computer Windows, aprire una nuova finestra di PowerShell come **amministratore** e digitare i comandi seguenti.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

Nell'esempio precedente sostituire "contoso100.com" con il nome di dominio DNS del dominio gestito. Ad esempio, se è stato creato un dominio gestito chiamato "contoso100.onmicrosoft.com", sostituire "contoso100.com" nell'attributo Subject con "contoso100.onmicrosoft.com" e "*.contoso100.com" nell'attributo DnsName con "*.contoso100.onmicrosoft.com").

![Selezionare una directory di Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Il certificato autofirmato appena creato viene inserito nell'archivio certificati del computer locale.


## <a name="next-step"></a>Passaggio successivo
[Attività 2: Esportare il certificato LDAP sicuro in un file PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
