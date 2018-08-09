---
title: 'Esercitazione: Integrazione di Azure Active Directory con Slack | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 8f79926d0d4729c6ad939bc604e9eb885dbe9f03
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421256"
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Esercitazione: Integrazione di Azure Active Directory con Slack

Questa esercitazione descrive come integrare Slack con Azure Active Directory (Azure AD).

L'integrazione di Slack con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Slack
- È possibile abilitare gli utenti per l'accesso automatico a Slack (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Slack, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Slack abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Slack dalla raccolta
1. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-slack-from-the-gallery"></a>Aggiunta di Slack dalla raccolta
Per configurare l'integrazione di Slack in Azure AD, è necessario aggiungere Slack dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Slack dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

1. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
1. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

1. Nella casella di ricerca digitare **Slack**.

    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/tutorial_slack_search.png)

1. Nel pannello dei risultati selezionare **Slack** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Slack con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Slack che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Slack.

Per stabilire la relazione di collegamento, in Slack assegnare il valore di **nome utente** in Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Slack, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
1. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
1. **[Creazione di un utente di test di Slack](#creating-a-slack-test-user)**: per avere una controparte di Britta Simon in Slack collegata alla rappresentazione dell'utente in Azure AD.
1. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Slack.

**Per configurare l'accesso Single Sign-On di Azure AD con Slack, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Slack** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

1. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_samlbase.png)

1. Nella sezione **URL e dominio Slack** seguire questa procedura:

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.slack.com`

    b. Nella casella di testo **Identificatore** digitare l'URL: `https://slack.com`

    > [!NOTE] 
    > Poiché non è il valore reale. È necessario aggiornare il valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto di Slack](https://slack.com/help/contact).
     
1. L'applicazione Slack prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Se l'**indirizzo di posta elettronica** di uno o più utenti non è compreso da una licenza di Office 365, l'attestazione **User.Email** non viene visualizzata nel token SAML. In questi casi, è invece consigliabile usare **user.userprincipalname** come valore dell'attributo **User.Email** per eseguire il mapping come **Identificatore univoco**.

1. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** selezionare **user.mail** come **Identificatore utente**. Per ogni riga illustrata nella tabella seguente, seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |  
    | User.Username | user.userprincipalname |

    a. Fare clic su **Attributo** per aprire la finestra di dialogo **Modifica attributo** ed eseguire i passaggi seguenti:

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_attribute1.png)

    a. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    b. Nell'elenco **Valore** selezionare il valore dell'attributo indicato per quella riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Fare clic su **OK**.

1. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_certificate.png)

1. Fare clic sul pulsante **Salva** .

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_general_400.png)

1. Nella sezione **Configurazione di Slack** fare clic su **Configura Slack** per aprire la finestra **Configura accesso**. Copiare **l'ID entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido**.

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_configure.png)

1. In un'altra finestra del browser Web accedere al sito aziendale di Slack come amministratore.

1. Accedere a **Microsoft Azure AD**, quindi andare su **Impostazioni team**.

     ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_001.png)

1. Nella sezione **Impostazioni team** fare clic sulla scheda **Autenticazione**, quindi fare clic su **Cambia impostazioni**.

    ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_002.png)

1. Nella finestra di dialogo **Impostazioni di autenticazione SAML** , eseguire la procedura seguente:

    ![Configurazione accesso Single Sign-On sul lato app](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Nella casella di testo **Endpoint SAML 2.0 (HTTP)** incollare il valore **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) copiato dal portale di Azure.

    b.  Nella casella di testo **Autorità di certificazione del provider di identità** incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    c.  Aprire il certificato scaricato nel Blocco note, copiarne il contenuto negli Appunti e incollarlo nella casella di testo **Certificato pubblico**.

    d. Configurare le tre impostazioni riportate sopra nel modo appropriato per il proprio team Slack. Per altre informazioni sulle impostazioni, vedere la **guida alla configurazione dell'accesso SSO di Slack** disponibile a questo indirizzo: `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Fare clic su **Salva configurazione**.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/create_aaduser_01.png) 

1. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/create_aaduser_02.png) 

1. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.

    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/create_aaduser_03.png)

1. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:

    ![Creazione di un utente test di Azure AD](./media/slack-tutorial/create_aaduser_04.png)

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Create**(Crea).

### <a name="creating-a-slack-test-user"></a>Creazione di un utente test di Slack

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Slack. Slack supporta il provisioning JIT, abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a Slack viene creato un nuovo utente, se non esiste ancora. Slack supporta anche il provisioning utenti automatico; [qui](slack-provisioning-tutorial.md) è possibile trovare altre informazioni su come configurare il provisioning utenti automatico.

> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Slack.

![Assegna utente][200]

**Per assegnare Britta Simon a Slack, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

1. Nell'elenco delle applicazioni selezionare **Slack**.

    ![Configure Single Sign-On](./media/slack-tutorial/tutorial_slack_app.png) 

1. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

1. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

1. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

1. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Slack nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Slack.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
* [Configura provisioning utenti](slack-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/slack-tutorial/tutorial_general_01.png
[2]: ./media/slack-tutorial/tutorial_general_02.png
[3]: ./media/slack-tutorial/tutorial_general_03.png
[4]: ./media/slack-tutorial/tutorial_general_04.png

[100]: ./media/slack-tutorial/tutorial_general_100.png

[200]: ./media/slack-tutorial/tutorial_general_200.png
[201]: ./media/slack-tutorial/tutorial_general_201.png
[202]: ./media/slack-tutorial/tutorial_general_202.png
[203]: ./media/slack-tutorial/tutorial_general_203.png
