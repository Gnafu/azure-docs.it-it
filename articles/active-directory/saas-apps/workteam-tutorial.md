---
title: 'Esercitazione: integrazione di Azure Active Directory con Workteam | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898653"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Esercitazione: integrazione di Azure Active Directory con Workteam

Questa esercitazione descrive come integrare Workteam con Azure Active Directory (Azure AD).

L'integrazione di Workteam con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workteam.
- È possibile abilitare gli utenti per l'accesso automatico a Workteam (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workteam, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione Workteam abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Workteam dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-workteam-from-the-gallery"></a>Aggiunta di Workteam dalla raccolta
Per configurare l'integrazione di Workteam in Azure AD, è necessario aggiungere Workteam dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workteam dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Workteam** , selezionare **Workteam**  nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Workteam nell'elenco risultati](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workteam in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Workteam che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workteam.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workteam, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Workteam](#create-a-workteam-test-user)**: per avere una controparte di Britta Simon in Workteam collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Workteam.

**Per configurare Single Sign-On di Azure AD con Workteam, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Workteam** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. Nella sezione **URL e dominio Workteam** l'utente deve eseguire alcuna operazione perché l'applicazione è già preintegrata in Azure.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workteam](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workteam](./media/workteam-tutorial/tutorial_workteam_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL: `https://app.workte.am`
     
5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/workteam-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Configurazione di Workteam** fare clic su **Configura Workteam** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. In un'altra finestra del Web browser accedere a Workteam come Amministratore della sicurezza.

9. Nell'angolo superiore destro fare clic su **logo del profilo** e quindi fare clic su **Impostazioni organizzazione**. 

    ![Impostazioni Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. Sotto **AUTENTICAZIONE** sezione, fare clic su **impostazioni logo**.

     ![Workteam in Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. Nella pagina delle **impostazioni SAML** seguire questa procedura:

     ![Saml per Workteam](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Impostare **Provider di identità SAML** come **Azure AD**.

    b. Nella casella di testo **URL servizio Single Sign-On SAML** incollare il valore dell'**URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    c. Nella casella di testo **ID entità SAML** incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    d. Nel Blocco note, aprire il **certificato con codifica Base 64** scaricato dal portale di Azure, copiarne il contenuto e quindi incollarlo nella casella **Certificato di firma SAML (Base64)** .

    e. Fare clic su **OK**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/workteam-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/workteam-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/workteam-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/workteam-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-workteam-test-user"></a>Creare un utente di test Workteam

Per consentire agli utenti di Azure AD di accedere a Workteam, è necessario effettuarne il provisioning in Workteam. Nel caso di Workteam il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere a Workteam come Amministratore della sicurezza.

2. In alto al centro della pagina **Impostazioni dell'organizzazione**, fare clic su **UTENTI** e quindi fare clic su **NUOVO UTENTE**.

    ![Utente Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Nella pagina **Nuovo dipendente** seguire questa procedura:

    ![Nuovo utente Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Nella casella di testo **Nome** immettere il nome dell'utente, ad esempio **Brittasimon**.

    b. Nelle **messaggio di posta elettronica** testo immettere l'indirizzo di posta elettronica dell'utente, ad esempio **Brittasimon\@contoso.com**.

    c. Fare clic su **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Workteam.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Workteam, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Workteam**.

    ![Collegamento Workteam nell'elenco Applicazioni](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Workteam nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workteam.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

