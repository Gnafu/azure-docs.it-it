---
title: 'Esercitazione: Integrazione di Azure Active Directory con Rollbar | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Rollbar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ebedddf5e62e41bd0ef2d4c426df448a81a1e2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60349654"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Esercitazione: Integrazione di Azure Active Directory con Rollbar

Questa esercitazione descrive come integrare Rollbar con Azure Active Directory (Azure AD).

L'integrazione di Rollbar con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Rollbar.
- È possibile abilitare gli utenti per l'accesso automatico a Rollbar (Single Sign-On) con i rispettivi account di Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Rollbar, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Rollbar abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Rollbar dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-rollbar-from-the-gallery"></a>Aggiunta di Rollbar dalla raccolta
Per configurare l'integrazione di Rollbar in Azure AD, è necessario aggiungere Rollbar dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Rollbar dalla raccolta, eseguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Rollbar**, nel pannello dei risultati selezionare **Rollbar** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Rollbar nell'elenco risultati](./media/rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Rollbar in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On per un utente di Azure AD, Azure AD deve conoscere l'utente corrispondente in Rollbar. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Rollbar.

Per stabilire la relazione di collegamento, assegnare il valore del **nome utente**in Azure AD al valore del **nome utente** in Rollbar.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Rollbar, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test di Rollbar](#create-a-rollbar-test-user)** per avere una controparte di Britta Simon in Rollbar collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Rollbar.

**Per configurare l'accesso Single Sign-On di Azure AD con Rollbar, eseguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Rollbar** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/rollbar-tutorial/tutorial_rollbar_samlbase.png)

1. Nella sezione **URL e dominio Rollbar**, se si vuole configurare l'applicazione in modalità avviata da **IDP** eseguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Rollbar](./media/rollbar-tutorial/tutorial_rollbar_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://saml.rollbar.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://rollbar.com/<accountname>/saml/sso/azure/`

1. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Rollbar](./media/rollbar-tutorial/tutorial_rollbar_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Rollbar](mailto:support@rollbar.com). 

1. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/rollbar-tutorial/tutorial_rollbar_certificate.png) 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/rollbar-tutorial/tutorial_general_400.png)
    
1. In un'altra finestra del Web browser accedere al sito aziendale Rollbar come amministratore.

1. Fare clic sulle **impostazioni del profilo** nell'angolo superiore destro e quindi fare clic sulle **impostazioni del nome account**.
    
    ![Configurazione](./media/rollbar-tutorial/general.png)

1. Fare clic su **Identity Provider** (Provider di identità) in SECURITY (SICUREZZA).

    ![Configurazione](./media/rollbar-tutorial/configure1.png)

1. Nella sezione **SAML Identity Provider** (Provider di identità SAML) eseguire questa procedura:
    
    ![Configurazione](./media/rollbar-tutorial/configure2.png)

    a. Selezionare **AZURE** dall'elenco a discesa **SAML Identity Provider** (Provider di identità SAML).

    b. Aprire il certificato scaricato in Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **SAML Metadata** (Metadati SAML).

    c. Fare clic su **Save**.

1. Dopo che si è fatto clic su Save (Salva), la schermata avrà l'aspetto seguente:
    
    ![Configurazione](./media/rollbar-tutorial/configure3.png)
    > [!NOTE] 
    > Per completare il passaggio seguente, è necessario prima aggiungere se stessi come utente all'app Rollbar in Azure.
    a. Se si vuole che tutti gli utenti eseguano l'autenticazione tramite Azure, fare clic su **log in via your identity provider** (accedere tramite il provider di identità) per ripetere l'autenticazione tramite Azure.  

    b.  Quando si ritorna alla schermata, selezionare la casella di controllo **Require login via SAML Identity Provider** (Richiedi accesso tramite provider di identità SAML).

    b. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili qui: [Documentazione incorporata di Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/rollbar-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/rollbar-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/rollbar-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/rollbar-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-rollbar-test-user"></a>Creare un utente di test Rollbar

Per consentire agli utenti di Azure AD di accedere a Rollbar, è necessario eseguirne il provisioning in Rollbar. Nel caso di Rollbar, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale Rollbar come amministratore.

1. Fare clic sulle **impostazioni del profilo** nell'angolo superiore destro e quindi fare clic sulle **impostazioni del nome account**.

    ![Utente](./media/rollbar-tutorial/general.png)

1. Fare clic su **Users**.
    
    ![Aggiungere un dipendente](./media/rollbar-tutorial/user1.png)

1. Fare clic su **Invite Team Members** (Invita membri del team).

    ![Invita persone](./media/rollbar-tutorial/user2.png)

1. Nella casella di testo, immettere il nome dell'utente, ad esempio **brittasimon\@contoso.com** e quindi scegliere **Add/Invite**.

    ![Invita persone](./media/rollbar-tutorial/user3.png)

1. L'utente riceve un invito. Dopo che questo è stato accettato, l'utente viene creato nel sistema.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione, Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite concessione dell'accesso a Rollbar.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Rollbar, eseguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Rollbar**.

    ![Collegamento a Rollbar nell'elenco delle applicazioni](./media/rollbar-tutorial/tutorial_rollbar_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Rollbar nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Rollbar.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rollbar-tutorial/tutorial_general_01.png
[2]: ./media/rollbar-tutorial/tutorial_general_02.png
[3]: ./media/rollbar-tutorial/tutorial_general_03.png
[4]: ./media/rollbar-tutorial/tutorial_general_04.png

[100]: ./media/rollbar-tutorial/tutorial_general_100.png

[200]: ./media/rollbar-tutorial/tutorial_general_200.png
[201]: ./media/rollbar-tutorial/tutorial_general_201.png
[202]: ./media/rollbar-tutorial/tutorial_general_202.png
[203]: ./media/rollbar-tutorial/tutorial_general_203.png

