---
title: 'Esercitazione: Integrazione di Azure Active Directory con Clarizen | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88f42a849d5c483feb9325072fd65769839672e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105372"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Esercitazione: Integrazione di Azure Active Directory con Clarizen

Questa esercitazione descrive come integrare Clarizen con Azure Active Directory (Azure AD).
L'integrazione di Clarizen con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Clarizen.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Clarizen con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Clarizen, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Clarizen abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Clarizen supporta l'accesso SSO avviato da **IDP**

## <a name="adding-clarizen-from-the-gallery"></a>Aggiunta di Clarizen dalla raccolta

Per configurare l'integrazione di Clarizen in Azure AD, è necessario aggiungere Clarizen dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Clarizen dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Clarizen**, selezionare **Clarizen** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Clarizen nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Clarizen con un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Clarizen.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Clarizen, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Clarizen](#configure-clarizen-single-sign-on)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Clarizen](#create-clarizen-test-user)** : per avere una controparte di Britta Simon in Clarizen collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Clarizen, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Clarizen** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella pagina **Configura l'accesso Single Sign-On con SAML** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Clarizen](common/idp-intiated.png)

    a. Nella casella di testo **Identificatore** digitare un valore: `Clarizen`

    b. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Questi non sono i valori reali. È necessario usare l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare come identificatore il valore univoco di una stringa. Per ottenere i valori effettivi, contattare il [team di supporto Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Set up Clarizen** (Configura Clarizen) copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-clarizen-single-sign-on"></a>Configurare l'accesso Single Sign-On per Clarizen

1. In un'altra finestra del Web browser accedere al sito aziendale di Clarizen come amministratore.

1. Fare clic sul nome utente e quindi su **Settings** (Impostazioni).

    ![Clic su "Settings" (Impostazioni) sotto il nome utente](./media/clarizen-tutorial/tutorial_clarizen_001.png "Settings (Impostazioni)")

1. Fare clic sulla scheda **Global Settings** (Impostazioni globali), quindi accanto a **Federated Authentication** (Autenticazione federata) fare clic su **edit** (modifica).

    ![Scheda "Global Settings" (Impostazioni globali)](./media/clarizen-tutorial/tutorial_clarizen_002.png "Global Settings (Impostazioni globali)")

1. Nella finestra di dialogo **Federated Authentication** (Autenticazione federata) seguire questa procedura:

    ![Finestra di dialogo "Federated Authentication"(Autenticazione federata)](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication (Autenticazione federata)")

    a. Selezionare **Enable Federated Authentication** (Abilita autenticazione federata).

    b. Per caricare il certificato scaricato, fare clic su **Carica** .

    c. Nella casella **Sign-in URL** (URL di accesso) immettere il valore di **Login URL** (URL di accesso) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Nella casella **Sign-out URL** (URL di disconnessione) immettere il valore di **Logout URL** (URL di disconnessione) dalla finestra di configurazione dell'applicazione di Azure AD.

    e. Selezionare **Utilizza POST**.

    f. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon\@dominioaziendale.estensione**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Clarizen.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Clarizen**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni, selezionare **Clarizen**.

    ![Collegamento di Clarizen nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-clarizen-test-user"></a>Creare l'utente di test di Clarizen

Questa sezione descrive come creare un utente chiamato Britta Simon in Clarizen.

**Per creare un utente manualmente, seguire questa procedura:**

Per consentire agli utenti di Azure AD di accedere a Clarizen, è necessario effettuare il provisioning degli account utente. Nel caso di Clarizen, il provisioning è un'attività manuale.

1. Accedere al sito aziendale di Clarizen come amministratore.

2. Fare clic su **Persone**.

    ![Clic su "People" (Persone)](./media/clarizen-tutorial/create_aaduser_001.png "People (Persone)")

3. Fare clic su **Invite User**.

    ![Pulsante "Invite User" (Invita l'utente)](./media/clarizen-tutorial/create_aaduser_002.png "Invitare utenti")

1. Nella finestra di dialogo **Invite People** (Invita persone) seguire questa procedura:

    ![Finestra di dialogo "Invite People" (Invita persone)](./media/clarizen-tutorial/create_aaduser_003.png "Invite People (Invita persone)")

    a. Nella casella **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    b. Fare clic su **Invita**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.


### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Clarizen nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Clarizen per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
