---
title: 'Esercitazione: Integrazione di Azure Active Directory con TrackVia | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TrackVia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e7010023-bdda-4a19-a335-19904e75b813
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeedes
ms.openlocfilehash: 54e830f2ead2887c941d81a97ed619ae5cb1c750
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202559"
---
# <a name="tutorial-azure-active-directory-integration-with-trackvia"></a>Esercitazione: Integrazione di Azure Active Directory con TrackVia

Questa esercitazione descrive come integrare TrackVia con Azure Active Directory (Azure AD).

L'integrazione di TrackVia con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TrackVia.
- È possibile abilitare gli utenti per l'accesso automatico a TrackVia (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con TrackVia, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di TrackVia abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di TrackVia dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-trackvia-from-the-gallery"></a>Aggiunta di TrackVia dalla raccolta
Per configurare l'integrazione di TrackVia in Azure AD, è necessario aggiungere TrackVia dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere TrackVia dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **TrackVia**, nel pannello dei risultati selezionare **TrackVia** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![TrackVia nell'elenco risultati](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TrackVia usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di TrackVia corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TrackVia.

Per stabilire la relazione di collegamento, in TrackVia assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con TrackVia, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di TrackVia](#create-a-trackvia-test-user)**: per avere una controparte di Britta Simon in TrackVia collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione TrackVia.

**Per configurare l'accesso Single Sign-On di Azure AD con TrackVia, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TrackVia** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_samlbase.png)

3. Nella sezione **URL e dominio TrackVia** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_url.png)

    Nella casella di testo **Identificatore** digitare il valore: `TrackVia`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://companyname.trackvia.com`
     
    > [!NOTE] 
    > Poiché questo non è un valore reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questi valori, contattare il [team di supporto clienti di TrackVia](mailto:support@trackvia.com).

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-trackvia-tutorial/tutorial_general_400.png)

7. Nella sezione **Configurazione di TrackVia** fare clic su **Configura TrackVia** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di TrackVia](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_configure.png)
    
8. In un'altra finestra del browser, accedere al sito aziendale di TrackVia come amministratore.

9. Fare clic sulle impostazioni dell'**Account personale** di Trackvia e quindi selezionare la scheda **Single Sign-On**, procedere come segue:

    ![Configurazione di TrackVia](./media/active-directory-saas-trackvia-tutorial/configure1.png)

    a. Nella casella di testo **Identity Provider Entity ID** (ID entità provider di identità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    b. Selezione **Scegli file** per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Save**

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-trackvia-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-trackvia-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-trackvia-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-trackvia-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-trackvia-test-user"></a>Creare un utente di test di TrackVia

Questa sezione descrive come creare un utente chiamato Britta Simon in TrackVia. TrackVia supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a TrackVia viene creato un nuovo utente, se non esiste già.
>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di TrackVia](mailto:support@trackvia.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad TrackVia.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a TrackVia, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **TrackVia**.

    ![Collegamento di TrackVia nell'elenco delle applicazioni](./media/active-directory-saas-trackvia-tutorial/tutorial_trackvia_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TrackVia nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TrackVia.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trackvia-tutorial/tutorial_general_203.png
