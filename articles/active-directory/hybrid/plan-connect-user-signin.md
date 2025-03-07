---
title: 'Azure AD Connect: accesso utente | Microsoft Docs'
description: Accesso utente Azure Connect AD per le impostazioni personalizzate.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbcc05093d801261493745c61dc5f68878d338b0
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607675"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Opzioni di accesso utente di Azure AD Connect
Azure Active Directory (Azure AD) Connect consente agli utenti di accedere alle risorse cloud e locali usando le stesse password. Questo articolo descrive i concetti chiave di ciascun modello di identità per facilitare la scelta dell'identità da usare per l'accesso ad Azure AD.

Se si è già acquisita familiarità con il modello di identità di Azure AD e si desiderano altre informazioni su un metodo specifico, fare clic sul collegamento appropriato:

* [Sincronizzazione dell'hash delle password](#password-hash-synchronization) con [accesso Single Sign-On facile](how-to-connect-sso.md)
* [Autenticazione pass-through](how-to-connect-pta.md) con [accesso Single Sign-On facile](how-to-connect-sso.md)
* [SSO federato (con Active Directory Federation Services, AD FS)](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federazione con PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> È importante ricordare che per configurare la federazione per Azure AD, si stabilisce una relazione di attendibilità tra il tenant di Azure AD e i domini federati. Con questo dominio federato attendibile gli utenti avranno accesso alle risorse cloud di Azure Active Directory nel tenant.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Scelta di un metodo di accesso utente per l'organizzazione
La prima decisione di implementazione di Azure AD Connect è la scelta del metodo di autenticazione che gli utenti useranno per accedere. È importante assicurarsi di scegliere il metodo corretto che soddisfi i requisiti di sicurezza e avanzati della propria organizzazione. L'autenticazione è fondamentale, perché convalida le identità degli utenti per accedere alle applicazioni e ai dati nel cloud. Per scegliere il metodo di autenticazione corretto è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo.

Azure Active Directory supporta i metodi di autenticazione descritti di seguito: 

* **Autenticazione cloud** - Quando si sceglie questo metodo di autenticazione, Azure Active Directory gestisce il processo di autenticazione per l'accesso degli utenti. Con l'autenticazione cloud è possibile scegliere tra due opzioni: 
   * **Sincronizzazione dell'hash delle password (PHS)** - La sincronizzazione dell'hash delle password consente agli utenti di usare gli stessi nome utente e password usati in locale senza la necessità di implementare infrastrutture aggiuntive oltre a Azure AD Connect. 
   * **Autenticazione pass-through (PTA)** -Questa opzione è simile alla sincronizzazione dell'hash delle password, ma fornisce una semplice convalida delle password mediante agenti software locali per organizzazioni con criteri di conformità e sicurezza avanzati.
* **Autenticazione federata** - Se si sceglie questo metodo di autenticazione, Azure AD trasferisce il processo di autenticazione a un sistema di autenticazione attendibile separato, ad esempio AD FS o un sistema di federazione di terze parti, per convalidare l'accesso dell'utente. 

Per la maggior parte delle organizzazioni che vogliono semplicemente abilitare l'accesso utente a Office 365, alle applicazioni SaaS e ad altre risorse basate su Azure AD, è consigliabile l'opzione di sincronizzazione dell'hash delle password predefinita.
 
Per informazioni dettagliate sulla scelta di un metodo di autenticazione, vedere [Scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida di Azure Active Directory](../../security/fundamentals/choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Sincronizzazione dell'hash delle password
Con la sincronizzazione dell'hash delle password, gli hash delle password degli utenti vengono sincronizzati da Active Directory locale ad Azure AD. Quando le password vengono modificate o reimpostate localmente, i nuovi hash delle password vengono sincronizzati immediatamente in Azure AD in modo che gli utenti possano usare sempre la stessa password per le risorse cloud, come avviene localmente. Le password non vengono mai inviate ad Azure AD né archiviate in Azure AD in testo non crittografato. La sincronizzazione dell'hash delle password può essere usata insieme al writeback delle password per consentire la reimpostazione autonoma delle password del servizio in Azure AD.

È possibile abilitare l'[accesso Single Sign-On facile](how-to-connect-sso.md) anche per gli utenti di computer aggiunti al dominio presenti nella rete aziendale. Con l'accesso Single Sign-On, gli utenti abilitati possono accedere in modo sicuro alle risorse cloud immettendo solo un nome utente.

![Sincronizzazione dell'hash delle password](./media/plan-connect-user-signin/passwordhash.png)

Per altre informazioni, vedere l'articolo sulla [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md).

### <a name="pass-through-authentication"></a>Autenticazione pass-through
Con l'autenticazione pass-through, la password dell'utente viene convalidata rispetto al controller di Active Directory locale e non è necessario che sia presente in Azure AD in alcuna forma. In questo modo, durante l'autenticazione nei servizi cloud possono essere valutati criteri locali come le restrizioni relative all'ora di accesso.

L'autenticazione pass-through usa un agente semplice in un computer aggiunto al dominio di Windows Server 2012 R2 nell'ambiente locale, che resta in attesa di richieste di convalida delle password. L'agente non richiede alcuna porta in ingresso aperta a Internet.

È possibile abilitare l'accesso Single Sign-On anche per utenti di computer aggiunti al dominio presenti nella rete aziendale. Con l'accesso Single Sign-On, gli utenti abilitati possono accedere in modo sicuro alle risorse cloud immettendo solo un nome utente.
![Autenticazione pass-through](./media/plan-connect-user-signin/pta.png)

Per altre informazioni, vedere:
- [Autenticazione pass-through](how-to-connect-pta.md)
- [Single Sign-On](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federazione che usa una farm nuova o esistente con AD FS in Windows Server 2012 R2
Con l'accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le proprie password locali. Inoltre, fino a quando si trovano nella rete aziendale, non hanno bisogno di immetterle. Usando l'opzione di federazione con AD FS è possibile distribuire una farm nuova o esistente con AD FS in Windows Server 2012 R2. Se si sceglie di specificare una farm esistente, Azure AD Connect configurerà l’attendibilità tra la farm e Azure AD in modo che gli utenti possano effettuare l’accesso.

<center>

![Federazione con AD FS in Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Distribuire la federazione con AD FS in Windows Server 2012 R2

Se si distribuisce una nuova farm, è necessario quanto segue:

* Un server Windows Server 2012 R2 per il server federativo.
* Un server Windows Server 2012 R2 per il ruolo Proxy applicazione Web.
* Un file PFX con un certificato SSL per il nome del servizio federativo desiderato, ad esempio fs.contoso.com.

Se si distribuisce una nuova farm o si usa una farm esistente, è necessario quanto segue:

* Credenziali di amministratore locale nei server federativi in uso.
* Credenziali di amministratore locale in qualsiasi server di gruppi di lavoro (non aggiunto a un dominio) in cui si intende distribuire il ruolo Proxy applicazione Web.
* Il computer in cui viene eseguita la procedura guidata deve essere in grado di connettersi a qualsiasi altro computer sul quale si desidera installare AD FS o il Proxy applicazione Web tramite Gestione remota Windows.

Per altre informazioni, vedere [Configurazione di SSO con AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federazione con PingFederate
Con l'accesso federato, gli utenti possono accedere ai servizi basati su Azure AD con le proprie password locali. Inoltre, fino a quando si trovano nella rete aziendale, non hanno bisogno di immetterle.

Per altre informazioni sulla configurazione di PingFederate per l'uso con Azure Active Directory, vedere [PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect) (Integrazione di PingFederate con Azure Active Directory e Office 365)

Per informazioni su come configurare Azure AD Connect usando PingFederate, vedere [Installazione personalizzata di Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Accedere usando una versione precedente di AD FS o una soluzione di terze parti
Se è stato già configurato l’accesso al cloud tramite una versione precedente di ADFS (ad esempio AD FS 2.0) o un provider di federazione di terze parti, è possibile ignorare la configurazione dell’accesso utenti tramite Azure AD Connect. In tal modo sarà possibile ottenere la sincronizzazione più recente e altre funzionalità di Azure AD Connect continuando comunque a utilizzare la soluzione esistente per l'accesso.

Per altre informazioni, vedere l'[Elenco di compatibilità di federazione di terze parti di Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Accesso utente e nome dell'entità utente
### <a name="understanding-user-principal-name"></a>Informazioni sul nome dell'entità utente
In Active Directory il nome dell'entità utente (UPN) predefinito è il nome DNS del dominio in cui è stato creato l'account utente. Nella maggior parte dei casi si tratta del nome di dominio registrato come dominio dell'organizzazione su Internet. Tuttavia, è possibile aggiungere altri suffissi UPN usando Domini e trust di Active Directory.

L'UPN dell'utente è in formato username@domain. Ad esempio, per un dominio di Active Directory denominato "contoso.com", un utente denominato John potrebbe avere il nome UPN "john@contoso.com". L'UPN dell'utente è basato su RFC 822. Anche se UPN e posta elettronica condividono lo stesso formato, il valore UPN per un utente non è necessariamente uguale all'indirizzo di posta elettronica dell'utente.

### <a name="user-principal-name-in-azure-ad"></a>Nome dell'entità utente in Azure AD
La procedura guidata di Azure AD Connect usa l'attributo userPrincipalName o, nelle installazioni personalizzate, consente di specificare l'attributo da usare in locale come nome dell'entità utente in Azure AD. Questo è il valore che verrà usato per l'accesso ad Azure AD. Se il valore dell'attributo del nome dell'entità utente non corrisponde a un dominio verificato in Azure AD, Azure AD lo sostituisce con un valore .onmicrosoft.com predefinito.

Ogni directory in Azure Active Directory include un nome di dominio predefinito nel formato contoso.onmicrosoft.com che consente di iniziare a usare Azure o altri servizi Microsoft. È possibile migliorare e semplificare l'esperienza di accesso usando i domini personalizzati. Per informazioni sui nomi di dominio personalizzato in Azure AD e sulla verifica dei domini, vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Configurazione dell'accesso ad Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Configurazione dell'accesso ad Azure AD con Azure AD Connect
L'esperienza di accesso ad Azure AD dipende dalla possibilità per Azure AD di associare il suffisso del nome dell'entità utente dell'utente da sincronizzare a uno dei domini personalizzati verificati nella directory di Azure AD. Azure AD Connect offre una guida alla configurazione delle impostazioni di accesso in Azure AD, in modo che per l'utente l'esperienza di accesso nel cloud sia simile all'esperienza locale.

Azure AD Connect elenca i suffissi UPN definiti per i domini e tenta di associarli a un dominio personalizzato in Azure AD. A quel punto, offre assistenza per eseguire gli interventi appropriati.
Nella pagina di accesso di Azure AD sono riportati i suffissi UPN definiti per l'istanza locale di Active Directory ed è visualizzato lo stato corrispondente per ogni suffisso. I valori dello stato possono essere i seguenti:

| Stato | DESCRIZIONE | Azione necessaria |
|:--- |:--- |:--- |
| Verificato |Azure AD Connect ha rilevato un dominio verificato in Azure AD. Tutti gli utenti di questo dominio possono accedere usando le credenziali locali. |Non è necessaria alcuna azione. |
| Non verificato |Azure AD Connect ha rilevato un dominio corrispondente in Azure AD ma tale dominio non è verificato. Se il dominio non è verificato, dopo la sincronizzazione il suffisso UPN degli utenti di questo dominio verrà modificato nel prefisso predefinito .onmicrosoft.com. | [Verificare il dominio personalizzato in Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Non aggiunto |Azure AD Connect non ha rilevato un dominio personalizzato corrispondente al suffisso UPN. Se il dominio non è aggiunto e verificato in Azure, il suffisso UPN degli utenti di questo dominio verrà modificato nel prefisso predefinito .onmicrosoft.com. | [Aggiungere e verificare un dominio personalizzato corrispondente al suffisso UPN.](../fundamentals/add-custom-domain.md) |

Nella pagina di accesso di Azure AD sono elencati i suffissi UPN definiti per il servizio Active Directory locale e il dominio personalizzato corrispondente in Azure AD con lo stato attuale della verifica. Nell'installazione personalizzata, è ora possibile selezionare l'attributo per il nome dell'entità utente nella pagina di **accesso ad Azure AD**.

![Pagina di accesso di Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

È possibile fare clic sul pulsante di aggiornamento per recuperare nuovamente lo stato più recente dei domini personalizzati da Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Selezione dell'attributo per il nome dell'entità utente in Azure AD
L'attributo userPrincipalName viene usato dagli utenti per l'accesso ad Azure AD e Office 365. I domini utilizzati, noti anche come suffisso UPN, devono essere verificati in Azure AD prima che gli utenti vengano sincronizzati.

Si consiglia di mantenere l'attributo userPrincipalName predefinito. Se questo attributo non è instradabile e non può essere verificato, è possibile selezionarne un altro, ad esempio posta elettronica, come attributo contenente l'ID di accesso. Questo attributo è noto come ID alternativo. Il valore dell'attributo ID alternativo deve essere conforme allo standard RFC 822. È possibile usare un ID alternativo sia con una soluzione di accesso Single Sign-On tramite password che Single Sign-On federativo.

> [!NOTE]
> L'uso di un ID alternativo non è compatibile con tutti i carichi di lavoro di Office 365. Per altre informazioni, vedere [Configurazione dell'ID di accesso alternativo](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Stati diversi del dominio personalizzato ed effetto sull'esperienza di accesso ad Azure
È molto importante comprendere la relazione tra gli stati dei domini personalizzati nella directory di Azure AD e i suffissi UPN definiti a livello locale. Di seguito sono illustrate le diverse esperienze di accesso possibili in Azure quando si imposta la sincronizzazione usando AD Azure Connect.

Per le informazioni seguenti, si supponga di usare il suffisso UPN contoso.com nella directory locale come parte dell'UPN, ad esempio user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Impostazioni rapide/Sincronizzazione dell'hash delle password

| Stato | Effetto sull'esperienza di accesso degli utenti in Azure |
|:---:|:--- |
| Non aggiunto |In questo caso non è stato aggiunto alcun dominio personalizzato per contoso.com nella directory di Azure AD. Gli utenti che usano UPN locali con suffisso @contoso.com non potranno usare il proprio UPN locale per effettuare l'accesso ad Azure. Dovranno invece usare un nuovo UPN disponibile in Azure AD aggiungendo il suffisso per la directory predefinita di Azure AD. Ad esempio, se si sincronizzano gli utenti con la directory di Azure AD azurecontoso.onmicrosoft.com, all'utente locale user@contoso.com verrà assegnato l'UPN user@azurecontoso.onmicrosoft.com. |
| Non verificato |In questo caso il dominio personalizzato contoso.com viene aggiunto nella directory di Azure AD, ma non è ancora verificato. Se si procede con la sincronizzazione degli utenti senza verificare il dominio, agli utenti verrà assegnato un nuovo UPN da Azure AD esattamente come accade nello scenario "Non aggiunto". |
| Verificato |In questo caso il dominio personalizzato contoso.com è già stato aggiunto e verificato in Azure AD per il suffisso UPN. Gli utenti potranno usare i propri nomi di entità utente locali, ad esempio user@contoso.com, per accedere ad Azure dopo la sincronizzazione con Azure AD. |

###### <a name="ad-fs-federation"></a>Federazione AD FS
Non è possibile creare una federazione con il dominio predefinito .onmicrosoft.com in Azure AD o un dominio personalizzato non verificato in Azure AD. Quando si esegue la procedura guidata di Azure AD Connect, se si seleziona un dominio non verificato per creare una federazione con Azure AD Connect verrà richiesto di creare i record necessari dove è ospitato il servizio DNS per il dominio. Per altre informazioni, vedere [Verificare il dominio di Azure AD selezionato per la federazione](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se si seleziona l'opzione di accesso utente come **Federazione con AD FS**, è necessario avere un dominio personalizzato per continuare con la creazione di una federazione in Azure AD. Ai fini di questa discussione, significa che si dovrà aggiungere un dominio personalizzato contoso.com nella directory di Azure AD.

| Stato | Effetto sull'esperienza di accesso degli utenti in Azure |
|:---:|:--- |
| Non aggiunto |In questo caso Azure AD Connect non ha rilevato un dominio personalizzato corrispondente per il suffisso UPN contoso.com nella directory di Azure AD. È necessario aggiungere un dominio personalizzato contoso.com se è necessario che gli utenti accedano usando AD FS con il proprio UPN locale, ad esempio user@contoso.com. |
| Non verificato |In questo caso Azure AD Connect richiederà i dettagli appropriati sul modo in cui si potrà verificare il dominio in una fase successiva. |
| Verified |In questo caso è possibile procedere con la configurazione senza ulteriori azioni. |

## <a name="changing-the-user-sign-in-method"></a>Modifica del metodo di accesso utente
È possibile modificare il metodo di accesso utente da Federazione a Sincronizzazione dell'hash delle password o Autenticazione pass-through usando le attività disponibili in Azure AD Connect dopo la configurazione iniziale di Azure AD Connect con la procedura guidata. Eseguire nuovamente la procedura guidata di Azure AD Connect; verrà visualizzato un elenco di attività che è possibile eseguire. Selezionare **Cambia l'accesso utente** dall'elenco di attività.

![Cambia l'accesso utente](./media/plan-connect-user-signin/changeusersignin.png)

Nella pagina successiva viene richiesto di inserire le credenziali per Azure AD.

![Connessione ad Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Nella pagina **Accesso utente** selezionare il tipo di accesso utente desiderato.

![Connessione ad Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Se si passa solo temporaneamente alla sincronizzazione dell'hash delle password, selezionare l'opzione **Non convertire gli account utente**. Se l'opzione non viene selezionata, ogni utente verrà convertito in federato, operazione che potrà richiedere diverse ore.
>
>

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'[integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
- Per altre informazioni, vedere [Concetti relativi alla progettazione per Azure AD Connect](plan-connect-design-concepts.md).
