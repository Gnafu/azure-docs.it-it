---
title: 'Esercitazione: Integrazione di Azure Active Directory con Spacio | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Spacio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9df8d199-b955-483c-aa4e-cabad1a0b9d6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: aa5c91265a832ef8a66948086b407688fdcbbbc2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435475"
---
# <a name="tutorial-azure-active-directory-integration-with-spacio"></a>Esercitazione: Integrazione di Azure Active Directory con Spacio

Questa esercitazione descrive come integrare Spacio con Azure Active Directory (Azure AD).

L'integrazione di Spacio con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Spacio.
- È possibile abilitare l'accesso automatico degli utenti a Spacio (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Spacio, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Spacio abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Spacio dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-spacio-from-the-gallery"></a>Aggiunta di Spacio dalla raccolta
Per configurare l'integrazione di Spacio in Azure AD, è necessario aggiungere Spacio dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Spacio dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

1. Nella casella di ricerca digitare **Spacio**, selezionare **Spacio** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Spacio nell'elenco dei risultati](./media/spacio-tutorial/tutorial_spacio_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Spacio con un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in Spacio. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Spacio.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Spacio, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creare un utente di test per Spacio](#create-a-spacio-test-user)**: per avere una controparte di Britta Simon in Spacio collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione Spacio.

**Per configurare l'accesso Single Sign-On di Azure AD con Spacio, eseguire queste operazioni:**

1. Nella pagina di integrazione dell'applicazione **Spacio** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/spacio-tutorial/tutorial_spacio_samlbase.png)

1. Nella sezione **URL e dominio Spacio** eseguire queste operazioni:

    ![Informazioni su URL e dominio di Spacio per l'accesso Single Sign-On](./media/spacio-tutorial/tutorial_spacio_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://sso.spac.io/<brokerageID>`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://sso.spac.io/<brokerageID>`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Spacio](mailto:support@spac.io). 

1. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/spacio-tutorial/tutorial_general_400.png)

1. Nella sezione **Certificato di firma SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e incollarlo nel Blocco note. 

    ![Collegamento di download del certificato](./media/spacio-tutorial/tutorial_spacio_certificate.png)

1. Per configurare l'accesso Single Sign-On in **Spacio**, è necessario inviare l'**URL dei metadati di federazione dell'app** al [team di supporto di Spacio](mailto:support@spac.io). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/spacio-tutorial/create_aaduser_01.png)

1. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/spacio-tutorial/create_aaduser_02.png)

1. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/spacio-tutorial/create_aaduser_03.png)

1. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/spacio-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Create**(Crea).
 
### <a name="create-a-spacio-test-user"></a>Creare un utente di test per Spacio

In questa sezione viene creato un utente di nome Britta Simon in Spacio. Collaborare con il [team di supporto di Spacio](mailto:support@spac.io) per aggiungere gli utenti alla piattaforma Spacio. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Spacio.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Spacio, eseguire queste operazioni:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Spacio**.

    ![Collegamento Spacio nell'elenco delle applicazioni](./media/spacio-tutorial/tutorial_spacio_app.png)  

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Spacio nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione Spacio.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spacio-tutorial/tutorial_general_01.png
[2]: ./media/spacio-tutorial/tutorial_general_02.png
[3]: ./media/spacio-tutorial/tutorial_general_03.png
[4]: ./media/spacio-tutorial/tutorial_general_04.png

[100]: ./media/spacio-tutorial/tutorial_general_100.png

[200]: ./media/spacio-tutorial/tutorial_general_200.png
[201]: ./media/spacio-tutorial/tutorial_general_201.png
[202]: ./media/spacio-tutorial/tutorial_general_202.png
[203]: ./media/spacio-tutorial/tutorial_general_203.png

