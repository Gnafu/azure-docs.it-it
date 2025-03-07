---
title: 'Esercitazione: Integrazione di Azure Active Directory con Sugar CRM | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Sugar CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 5c46b096b1182b3f2a998f992f72e6127dfe0888
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089550"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Esercitazione: Integrazione di Azure Active Directory con Sugar CRM

Questa esercitazione descrive come integrare Sugar CRM con Azure Active Directory (Azure AD).
L'integrazione di Sugar CRM con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Sugar CRM.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Sugar CRM con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Sugar CRM, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente di Azure AD, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Sugar CRM abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Sugar CRM supporta l'accesso SSO avviato da **SP**

## <a name="adding-sugar-crm-from-the-gallery"></a>Aggiunta di Sugar CRM dalla raccolta

Per configurare l'integrazione di Sugar CRM in Azure AD, è necessario aggiungere Sugar CRM dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Sugar CRM dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Sugar CRM**, selezionare **Sugar CRM** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Sugar CRM nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Sugar CRM usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Sugar CRM.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Sugar CRM, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Sugar CRM](#configure-sugar-crm-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Sugar CRM](#create-sugar-crm-test-user)** : per avere una controparte di Britta Simon in Sugar CRM collegata alla relativa rappresentazione in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Sugar CRM, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/), nella pagina di integrazione dell'applicazione **Sugar CRM**, selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Sugar CRM](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di Sugar CRM](https://support.sugarcrm.com/). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Sugar CRM** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-sugar-crm-single-sign-on"></a>Configurare l'accesso Single Sign-On di Sugar CRM

1. In un'altra finestra del Web browser accedere al sito aziendale di Sugar CRM come amministratore.

1. Passare alla pagina **Admin**.

    ![Amministratore](./media/sugarcrm-tutorial/ic795888.png "Amministratore")

1. Nella sezione **Amministrazione**, fare clic su **Gestione password**.

    ![Amministrazione](./media/sugarcrm-tutorial/ic795889.png "Amministrazione")

1. Selezionare **Abilita autenticazione SAML**.

    ![Amministrazione](./media/sugarcrm-tutorial/ic795890.png "Amministrazione")

1. Nella sezione **SAML Authentication** seguire questa procedura:

    ![Autenticazione SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticazione SAML")  

    a. Nella casella di testo **URL di accesso** incollare il valore di **URL di accesso** copiato dal portale di Azure.
  
    b. Nella casella di testo **URL SLO** incollare il valore di **URL disconnessione** copiato dal portale di Azure.
  
    c. Aprire il certificato con codifica Base 64 nel Blocco note, copiarne il contenuto negli Appunti e incollare l’intero certificato nella casella di testo **Certificate X.509** .
  
    d. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare `brittasimon@yourcompanydomain.extension`.  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Sugar CRM.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Sugar CRM**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Sugar CRM**.

    ![Collegamento a Sugar CRM nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-sugar-crm-test-user"></a>Creare un utente di test di Sugar CRM

Per consentire agli utenti di Azure AD di accedere a Sugar CRM, è necessario effettuarne il provisioning in Sugar CRM. Nel caso di Sugar CRM, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di **Sugar CRM** come amministratore.

1. Passare alla pagina **Admin**.

    ![Amministratore](./media/sugarcrm-tutorial/ic795888.png "Amministratore")

1. Nella sezione **Administration** (Amministrazione) fare clic su **User Management** (Gestione utenti).

    ![Amministrazione](./media/sugarcrm-tutorial/ic795893.png "Amministrazione")

1. Passare a **Users \> Create New User** (Utenti > Crea nuovo utente).

    ![Creazione di un nuovo utente](./media/sugarcrm-tutorial/ic795894.png "Creazione di un nuovo utente")

1. Nella scheda **Profilo utente** eseguire la procedura seguente:

    ![Nuovo utente](./media/sugarcrm-tutorial/ic795895.png "Nuovo utente")

    * Digitare il **nome utente**, il **cognome** e l'**indirizzo e-mail** di un utente di Azure Active Directory valido nelle caselle di testo correlate.
  
1. Per **Status** (Stato) selezionare **Active** (Attivo).

1. Nella scheda Password, eseguire la procedura seguente:

    ![Nuovo utente](./media/sugarcrm-tutorial/ic795896.png "Nuovo utente")

    a. Digitare la password nella casella di controllo correlata.

    b. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento di creazione di account utente Sugar CRM o qualsiasi API fornita da Sugar CRM per eseguire il provisioning degli account utente di Azure AD.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Sugar CRM nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Sugar CRM per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

