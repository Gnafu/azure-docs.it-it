---
title: 'Esercitazione: Configurare Workday per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come usare Workday come origine dei dati sull'identità per Active Directory locale e Azure Active Directory.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: b632622868480638174b616780441e13c16a52c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Esercitazione: Configurare Workday per il provisioning utenti automatico

L'obiettivo di questa esercitazione è illustrare i passaggi da eseguire per importare utenti da Workday in Active Directory e Azure Active Directory, con il writeback facoltativo di alcuni attributi in Workday. 



## <a name="overview"></a>Panoramica

Il [servizio di provisioning utenti di Azure Active Directory](active-directory-saas-app-provisioning.md) si integra con l'[API Human Resources di Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) per il provisioning degli account utente. Azure AD usa questa connessione per consentire i flussi di lavoro di provisioning utenti seguenti:

* **Provisioning degli utenti in Active Directory**: sincronizzare set selezionati di utenti da Workday in una o più foreste di Active Directory. 

* **Provisioning degli utenti solo cloud in Azure Active Directory**: è possibile eseguire il provisioning in Azure Active Directory degli utenti ibridi presenti sia in Active Directory che in Azure Active Directory usando [AAD Connect](connect/active-directory-aadconnect.md). È tuttavia possibile eseguire il provisioning degli utenti solo cloud direttamente da Workday in Azure Active Directory tramite il servizio di provisioning utenti di Azure AD.

* **Writeback degli indirizzi di posta elettronica in Workday**: il servizio di provisioning utenti di Azure AD può eseguire la scrittura in Workday di attributi utente di Azure AD selezionati, ad esempio l'indirizzo di posta elettronica.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Quali scenari di risorse umane è in grado di gestire?

I flussi di lavoro di provisioning utenti di Workday supportati dal servizio di provisioning utenti di Azure AD consentono l'automazione dei seguenti scenari di gestione delle risorse umane e del ciclo di vita delle identità:

* **Assunzione di nuovi dipendenti**: quando viene aggiunto un nuovo dipendente a Workday, verrà creato automaticamente un account utente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](active-directory-saas-app-provisioning.md), con il writeback dell'indirizzo di posta elettronica in Workday.

* **Aggiornamenti di attributi e profili dei dipendenti**: se il record di un dipendente viene aggiornato in Workday (ad esempio, il nome, il titolo o il manager), il relativo account utente verrà aggiornato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](active-directory-saas-app-provisioning.md).

* **Eliminazione di dipendenti**: quando un dipendente viene eliminato in Workday, il relativo account utente viene disabilitato automaticamente in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](active-directory-saas-app-provisioning.md).

* **Riassunzioni di dipendenti**: quando un dipendente viene nuovamente aggiunto in Workday, il relativo account utente precedente può essere automaticamente riattivato o sottoposto di nuovo a provisioning (a seconda delle proprie preferenze) in Active Directory, Azure Active Directory e, facoltativamente, in Office 365 e [altre applicazioni SaaS supportate da Azure AD](active-directory-saas-app-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Per chi è più adatta questa soluzione di provisioning utenti?

Questa soluzione di provisioning utenti Workday è attualmente disponibile in anteprima pubblica e idealmente è la più appropriata per:

* Organizzazioni che desiderano una soluzione predefinita basata sul cloud per il provisioning utenti Workday

* Organizzazioni che richiedono il provisioning utenti diretto da Workday ad Active Directory o ad Azure Active Directory

* Organizzazioni che richiedono che il provisioning utenti venga effettuato tramite i dati ottenuti dal modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 

* Organizzazioni che richiedono l'aggiunta o lo spostamento degli utenti o che devono lasciare gli utenti sincronizzati con uno o più domini, foreste o unità organizzative di Active Directory solo in base alle informazioni sulle modifiche rilevate nel modulo Workday HCM. Vedere [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizzazioni che usano Office 365 per la posta elettronica


## <a name="planning-your-solution"></a>Pianificazione della soluzione

Prima di avviare l'integrazione di Workday, verificare i prerequisiti riportati di seguito e leggere le seguenti linee guida su come associare gli attuali requisiti per l'architettura Active Directory e il provisioning utenti con le soluzioni fornite da Azure Active Directory.

### <a name="prerequisites"></a>prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Una sottoscrizione di Azure AD Premium P1 valida con accesso di amministratore globale
* Un tenant per l'implementazione di Workday a scopo di test e integrazione
* Autorizzazioni di amministratore in Workday per creare un utente di integrazione dei sistemi e apportare modifiche ai dati dei dipendenti di prova a scopo di test
* Per il provisioning utenti in Active Directory, è necessario un server appartenente a un dominio con Windows Server 2012 o versione successiva per ospitare l'[agente di sincronizzazione locale](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](connect/active-directory-aadconnect.md) per la sincronizzazione tra Active Directory e Azure AD

### <a name="solution-architecture"></a>Architettura della soluzione

Azure AD offre un ricco set di connettori di provisioning per semplificare l'esecuzione del provisioning e la gestione del ciclo di vita delle identità da Workday ad Active Directory, Azure AD, app SaaS e altro ancora. Le funzionalità da usare e la configurazione della soluzione variano a seconda dell'ambiente e dei requisiti dell'organizzazione. Per iniziare, prendere nota del numero degli elementi seguenti presenti e distribuiti nell'organizzazione:

* Quante foreste di Active Directory sono in uso?
* Quanti domini di Active Directory sono in uso?
* Quante unità organizzative di Active Directory sono in uso?
* Quanti tenant di Azure Active Directory sono in uso?
* Sono presenti utenti di cui deve essere effettuato il provisioning sia in Active Directory che in Azure Active Directory (utenti "ibridi")?
* Sono presenti utenti di cui deve essere effettuato il provisioning in Azure Active Directory, ma non in Active Directory (utenti "solo cloud")?
* Deve essere eseguito il writeback degli indirizzi di posta elettronica degli utenti in Workday?


Dopo aver risposto a queste domande, è possibile pianificare la distribuzione del provisioning di Workday seguendo le linee guida riportate di seguito.

#### <a name="using-provisioning-connector-apps"></a>Uso di app di connettori di provisioning

Azure Active Directory supporta connettori di provisioning preintegrati per Workday e molte altre applicazioni SaaS. 

Un singolo connettore di provisioning si interfaccia con l'API di un singolo sistema di origine e consente di eseguire il provisioning dei dati in un singolo sistema di destinazione. La maggior parte dei connettori di provisioning supportati da Azure AD è destinata a un singolo sistema di origine e di destinazione (ad esempio, da Azure AD a ServiceNow) e può essere configurata aggiungendo l'app in questione dalla raccolta di app di Azure AD (ad esempio, ServiceNow). 

È presente una relazione uno-a-uno tra le istanze dei connettori di provisioning e le istanze delle app in Azure AD:

| Sistema di origine | Sistema di destinazione |
| ---------- | ---------- | 
| Tenant di Azure AD | Applicazione SaaS |


Tuttavia, quando si usano Workday e Active Directory, sono presenti più sistemi di origine e di destinazione che è necessario tenere presente:

| Sistema di origine | Sistema di destinazione | Note |
| ---------- | ---------- | ---------- |
| Workday | Foresta di Active Directory | Ogni foresta viene considerata come un sistema di destinazione distinto |
| Workday | Tenant di Azure AD | Come richiesto per gli utenti solo cloud |
| Foresta di Active Directory | Tenant di Azure AD | Questo flusso è attualmente gestito da AAD Connect |
| Tenant di Azure AD | Workday | Per il writeback degli indirizzi di posta elettronica |

Per semplificare questi flussi di lavoro tra più sistemi di origine e di destinazione, Azure AD fornisce diverse app di connettori di provisioning che è possibile aggiungere dalla raccolta di app di Azure AD:

![Raccolta di app di AAD](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Workday to Active Directory Provisioning** (Provisioning Workday in Active Directory): questa app semplifica il provisioning degli account utente da Workday in una singola foresta di Active Directory. Se sono presenti più foreste, è possibile aggiungere un'istanza di questa app dalla raccolta di app di Azure AD per ogni foresta di Active Directory in cui è necessario effettuare il provisioning.

* **Workday to Azure AD Provisioning** (Provisioning Workday in Azure AD): mentre AAD Connect è lo strumento da usare per sincronizzare gli utenti di Active Directory in Azure Active Directory, questa app può essere usata per semplificare il provisioning degli utenti solo cloud da Workday a un singolo tenant di Azure Active Directory.

* **Workday Writeback** (Writeback Workday): questa app semplifica il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

> [!TIP]
> La normale app "Workday" viene usata per la configurazione del Single Sign-On tra Azure Active Directory e Workday. 

Nelle sezioni rimanenti di questa esercitazione verrà descritto come installare e configurare queste speciali app di connettori di provisioning. La scelta delle app da configurare dipende dai sistemi in cui è necessario effettuare il provisioning e dal numero di foreste di Active Directory e di tenant Azure AD nell'ambiente in uso.

![Panoramica](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>Configurare un utente di integrazione dei sistemi in Workday
Un requisito comune di tutti i connettori di provisioning Workday è che richiedono le credenziali di un account di integrazione dei sistemi Workday per la connessione all'API Human Resources di Workday. In questa sezione viene descritto come creare un account di integrazione dei sistemi in Workday.

> [!NOTE]
> È possibile evitare di eseguire questa procedura usando un account di amministratore globale di Workday come account di integrazione dei sistemi. Questa soluzione è utilizzabile a scopo dimostrativo, ma non è consigliata per le distribuzioni di produzione.

### <a name="create-an-integration-system-user"></a>Creare un utente del sistema di integrazione

**Per creare un utente di sistema di integrazione, seguire questa procedura:**

1. Accedere al tenant di Workday usando un account amministratore. In **Workday Workbench** immettere "create user" nella casella di ricerca e quindi fare clic su **Create Integration System User** (Crea utente del sistema di integrazione). 
   
    ![Creare un utente](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Creare un utente")
2. Completare l'attività **Create Integration System User** specificando un nome utente e una password per un nuovo utente del sistema di integrazione.  
 * Lasciare l'opzione **Require New Password at Next Sign In (Richiedi nuova password al prossimo accesso)** non selezionata, perché l'accesso dell'utente verrà eseguito a livello di codice. 
 * Lasciare l'opzione **Session Timeout Minutes (Minuti di timeout della sessione)** impostata sul valore predefinito 0, in modo da evitare un timeout prematuro delle sessioni dell'utente. 
   
    ![Creare un utente del sistema di integrazione](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Creare un utente del sistema di integrazione")

### <a name="create-a-security-group"></a>Creare un gruppo di sicurezza
È necessario creare un gruppo di sicurezza del sistema di integrazione non vincolato e assegnare l'utente a tale gruppo.

**Per creare un gruppo di sicurezza, seguire questa procedura:**

1. Immettere "create security group" nella casella di ricerca e quindi fare clic su **Create Security Group**(Crea gruppo di sicurezza). 
   
    ![Creare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Creare un gruppo di sicurezza")
2. Completare l'attività **Creare un gruppo di sicurezza**.  
3. Selezionare **Integration System Security Group (Unconstrained)** (Gruppo di sicurezza del sistema di integrazione - Non vincolato) nell'elenco a discesa **Type of Tenanted Security Group** (Tipo di gruppo di sicurezza con tenant).
4. Creare un gruppo di sicurezza a cui i membri verranno aggiunti in modo esplicito. 
   
    ![Creare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Creare un gruppo di sicurezza")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Assegnare l'utente del sistema di integrazione al gruppo di sicurezza

**Per assegnare l'utente di sistema di integrazione, seguire questa procedura:**

1. Immettere "edit security group" nella casella di ricerca e quindi fare clic su **Edit Security Group**(Modifica gruppo di sicurezza). 
   
    ![Modificare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modificare un gruppo di sicurezza")
2. Eseguire la ricerca del nuovo gruppo di sicurezza di integrazione e selezionarlo in base al nome. 
   
    ![Modificare un gruppo di sicurezza](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modificare un gruppo di sicurezza")
3. Aggiungere il nuovo utente del sistema di integrazione al nuovo gruppo di sicurezza. 
   
    ![Gruppo di sicurezza del sistema](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Gruppo di sicurezza del sistema")  

### <a name="configure-security-group-options"></a>Configurare le opzioni del gruppo di sicurezza
In questo passaggio si concedono al gruppo di sicurezza le autorizzazioni dei criteri di sicurezza del dominio per i dati del ruolo di lavoro.

**Per configurare le opzioni del gruppo di sicurezza, seguire questa procedura:**

1. Immettere **Domain Security Policies** (Criteri di sicurezza del dominio) nella casella di ricerca e quindi fare clic sul collegamento **Domain Security Policies for Functional Area** (Criteri di sicurezza del dominio per area funzionale).  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Criteri di sicurezza di dominio")  
2. Cercare system e selezionare **System** come area funzionale.  Fare clic su **OK**.  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Criteri di sicurezza di dominio")  
3. Nell'elenco dei criteri di sicurezza relativi all'area funzionale del sistema espandere **Amministrazione sicurezza** e selezionare i criteri di sicurezza del dominio **Provisioning account esterno**.  
   
    ![Criteri di sicurezza di dominio](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Criteri di sicurezza di dominio")  
4. Fare clic sul pulsante **Edit Permissions** (Modifica autorizzazioni) e nella finestra di dialogo **Edit Permissions** (Modifica autorizzazioni) aggiungere il nuovo gruppo di sicurezza all'elenco dei gruppi di sicurezza con autorizzazioni di integrazione **Get** e **Put**. 
   
    ![Modificare un'autorizzazione](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Modificare un'autorizzazione")  
    
5. Ripetere i passaggi precedenti da 1 a 4 per ognuno di questi criteri di sicurezza rimanenti:

| Operazione | Criteri di sicurezza del dominio |
| ---------- | ---------- | 
| Get e put | Worker Data: Public Worker Reports |
| Get e put | Worker Data: Work Contact Information (Dati ruolo di lavoro: informazioni di contatto ruolo di lavoro) |
| Get | Worker Data: All Positions |
| Get | Worker Data: Current Staffing Information |
| Get | Dati lavoratore - Qualifica riportata sul profilo |
   
    
### <a name="activate-security-policy-changes"></a>Attivare le modifiche apportate ai criteri di sicurezza

**Per attivare le modifiche apportate ai criteri di sicurezza, seguire questa procedura:**

1. Immettere "activate" (attiva) nella casella di ricerca e quindi fare clic sul collegamento **Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza)**. 
   
    ![Attivare](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Attivare") 
2. Avviare l'attività Activate Pending Security Policy Changes (Attiva le modifiche in sospeso ai criteri di sicurezza) immettendo un commento a scopo di controllo e quindi fare clic su **OK**. 
   
    ![Attivare la sicurezza in sospeso](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Attivare la sicurezza in sospeso")   
3. Completare l'attività nella schermata successiva selezionando la casella di controllo **Confirm (Conferma)** e quindi facendo clic su **OK**. 
   
    ![Attivare la sicurezza in sospeso](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Attivare la sicurezza in sospeso")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Configurazione del provisioning utenti da Workday in Active Directory
Seguire queste istruzioni per configurare il provisioning degli account utente da Workday in ogni foresta di Active Directory in cui è necessario effettuare il provisioning.

### <a name="planning"></a>Pianificazione

Prima di configurare il provisioning utenti in una foresta di Active Directory, cercare di rispondere alle domande seguenti. Le risposte a queste domande permettono di determinare come devono essere impostati i filtri di ambito e i mapping degli attributi. 

* **Di quali utenti in Workday è necessario effettuare il provisioning in questa foresta di Active Directory?**

   * *Esempio: utenti il cui attributo "Company" di Workday contiene il valore "Contoso", e l'attributo "Worker_Type" contiene "Regular"*

* **Come vengono instradati gli utenti alle diverse unità organizzative?**

   * *Esempio: gli utenti vengono instradati alle unità organizzative che corrispondono alla posizione di un ufficio, in base a quanto definito negli attributi "Municipality" e "Country_Region_Reference" di Workday*

* **Come devono essere immessi gli attributi seguenti in Active Directory?**

   * Nome comune (cn)
      * *Esempio: usare il valore di User_ID di Workday, impostato dalle risorse umane*
      
   * ID dipendente (employeeId)
      * *Esempio: usare il valore di Worker_ID di Workday*
      
   * Nome dell'account SAM (sAMAccountName)
      * *Esempio: usare il valore di User_ID di Workday, filtrato tramite un'espressione di provisioning di Azure AD per rimuovere i caratteri non validi*
      
   * Nome dell'entità utente (userPrincipalName)
      * *Esempio: usare il valore di User_ID di Workday, con un'espressione di provisioning di Azure AD per aggiungere un nome di dominio*

* **Come devono essere impostate le corrispondenze degli utenti tra Workday e Active Directory?**

  * *Esempio: gli utenti con un valore di "Worker_ID" di Workday specifico vengono associati agli utenti di Active Directory il cui attributo "employeeID" ha lo stesso valore. Se il valore di Worker_ID non viene trovato in Active Directory, creare un nuovo utente.*
  
* **La foresta di Active Directory contiene già gli ID utente necessari per il funzionamento della logica di corrispondenza?**

  * *Esempio: se questa è una nuova distribuzione di Workday, è assolutamente consigliabile fare in modo che Active Directory sia già popolato con i valori di Worker_ID di Workday corretti (o con il valore dell'ID univoco desiderato) per mantenere la logica di corrispondenza il più semplice possibile.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Aggiungere l'app del connettore di provisioning e creare la connessione a Workday

**Per configurare il provisioning da Workday in Active Directory:**

1.  Passare a <https://portal.azure.com>.

2.  Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3.  Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4.  Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5.  Cercare **Workday Provisioning to Active Directory** (Provisioning Workday in Active Directory) e aggiungere tale app dalla raccolta.

6.  Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7.  Impostare **Modalità di** **provisioning** su **Automatico**

8.  Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. **Dovrebbe essere simile a: username@contoso4**

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta.

   * **Foresta Active Directory:**  il "nome" della foresta di Active Directory, come restituito dal cmdlet PowerShell Get-ADForest. In genere si tratta di una stringa simile a: *contoso.com*

   * **Contenitore Active Directory:**  immettere la stringa del contenitore che contiene tutti gli utenti nella foresta di Active Directory. Esempio: *OU=Standard Users,OU=Users,DC=contoso,DC=test*

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che le credenziali per Workday siano valide in Workday. 

![Portale di Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi 

In questa sezione verrà configurato il flusso dei dati utente da Workday in Active Directory.

1.  Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Workday Workers to OnPremises** (Sincronizza lavoratori Workday in locale).

2.  Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Workday inclusi nell'ambito per il provisioning in Active Directory, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: Ambito per gli utenti con ID lavoratore compreso tra 1000000 e 2000000

      * Attributo: WorkerID

      * Operatore: Corrispondenza REGEX

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: Solo i dipendenti, senza i lavoratori occasionali 

      * Attributo: EmployeeID

      * Operatore: NON È NULL

3.  Nel campo **Azioni oggetto di destinazione** è possibile applicare un filtro a livello globale per le azioni di cui è consentita l'esecuzione in Active Directory. **Creazione** e **Aggiornamento** sono le più comuni.

4.  Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

      * **Tipo di mapping**

         * **Diretto**: scrive il valore dell'attributo di Workday nell'attributo di Active Directory, senza modifiche

         * **Costante**: scrive un valore stringa costante statico nell'attributo di Active Directory

         * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

      * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

      * **Attributo di destinazione**: l'attributo utente in Active Directory.

      * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Active Directory. In genere, è impostato sul campo ID lavoratore per Workday, che solitamente è associato a uno degli attributi ID dipendente in Active Directory.

      * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

      * **Applica questo mapping**
       
         * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

         * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

![Portale di Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**Di seguito sono riportati alcuni esempi di mapping degli attributi tra Workday e Active Directory, con alcune espressioni comuni**

-   L'espressione che esegue il mapping all'attributo parentDistinguishedName viene usata per effettuare il provisioning di un utente in diverse unità organizzative in base a uno o più attributi di origine di Workday. L'esempio qui colloca gli utenti in diverse unità organizzative in base alla città in cui si trovano.

-   L'attributo userPrincipalName in Active Directory viene generato tramite la concatenazione dell'ID utente di Workday con un suffisso di dominio

-   [La documentazione sulla scrittura di espressioni è disponibile qui](active-directory-saas-writing-expressions-for-attribute-mappings.md). Sono inclusi alcuni esempi su come rimuovere i caratteri speciali.

  
| ATTRIBUTO DI WORKDAY | ATTRIBUTO DI ACTIVE DIRECTORY |  ID CORRISPONDENTE? | CREAZIONE / AGGIORNAMENTO |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Sì** | Scritto solo al momento della creazione | 
| **UserID**    |  cn    |   |   Scritto solo al momento della creazione |
| **Join("@",[UserID], "contoso.com")**   | userPrincipalName     |     | Scritto solo al momento della creazione 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Scritto solo al momento della creazione |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | Creazione e aggiornamento |
| **FirstName**   | givenName       |     |    Creazione e aggiornamento |
| **LastName**   |   sn   |     |  Creazione e aggiornamento |
| **PreferredNameData**  |  displayName |     |   Creazione e aggiornamento |
| **Company**         | company   |     |  Creazione e aggiornamento |
| **SupervisoryOrganization**  | department  |     |  Creazione e aggiornamento |
| **ManagerReference**   | manager  |     |  Creazione e aggiornamento |
| **BusinessTitle**   |  title     |     |  Creazione e aggiornamento | 
| **AddressLineData**    |  streetAddress  |     |   Creazione e aggiornamento |
| **Municipality**   |   l   |     | Creazione e aggiornamento |
| **CountryReferenceTwoLetter**      |   co |     |   Creazione e aggiornamento |
| **CountryReferenceTwoLetter**    |  c  |     |         Creazione e aggiornamento |
| **CountryRegionReference** |  st     |     | Creazione e aggiornamento |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Creazione e aggiornamento |
| **PostalCode**  |   postalCode  |     | Creazione e aggiornamento |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Creazione e aggiornamento |
| **Fax**      | facsimileTelephoneNumber     |     |    Creazione e aggiornamento |
| **Mobile**  |    mobile       |     |       Creazione e aggiornamento |
| **LocalReference** |  preferredLanguage  |     |  Creazione e aggiornamento |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Creazione e aggiornamento |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>Parte 3: Configurare l'agente di sincronizzazione locale

Per eseguire il provisioning in Active Directory locale, è necessario installare un agente in un server appartenente a un dominio nella foresta di Active Directory desiderata. Per completare la procedura, sono necessarie credenziali di amministratore di dominio (o amministratore dell'organizzazione).

**[È possibile scaricare l'agente di sincronizzazione locale qui](https://go.microsoft.com/fwlink/?linkid=847801)**

Dopo aver installato l'agente, eseguire i comandi di PowerShell riportati di seguito per configurare l'agente per l'ambiente in uso.

**Comando 1**

> cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent" Agent\\Modules\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**Comando 2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Input: per "Nome directory", immettere il nome della foresta di Active Directory, com'è stato immesso nella parte \#2
* Input: nome utente e password di amministratore per la foresta di Active Directory

**Comando 3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Input: nome utente e password di amministratore globale per il tenant di Azure AD

>[!IMPORTANT]
>Attualmente c'è un problema noto riguardante le credenziali di amministratore globale che non funzionano se usano un dominio personalizzato (esempio: admin@contoso.com). Per aggirare il problema, è possibile creare e usare un account amministratore globale con un dominio onmicrosoft.com (esempio: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>Attualmente c'è un problema noto riguardante le credenziali di amministratore globale che non funzionano se è abilitata l'autenticazione a più fattori. Come soluzione alternativa, disabilitare l'autenticazione a più fattori per l'amministratore globale.


**Comando 4**

> Get-AdSyncAgentProvisioningTasks

* Azione: verificare che i dati vengano restituiti. Questo comando individua automaticamente le app di provisioning Workday nel tenant di Azure AD. Output di esempio:

> Name          : My AD Forest
>
> Enabled       : True
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed  : False
>
> Identifier    : WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**Comando 5**

> Start-AdSyncAgentSynchronization -Automatic

**Comando 6**

> net stop aadsyncagent

**Comando 7**

> net start aadsyncagent

>[!TIP]
>Oltre ai comandi "net" in Powershell, il servizio agente di sincronizzazione può essere avviato e arrestato anche usando **Services.msc**. Se si verificano errori durante l'esecuzione dei comandi di Powershell, assicurarsi che il l'**agente di provisioning di Microsoft Azure AD Connect** sia in esecuzione in **Services.msc**.

![Services](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Configurazione aggiuntiva per i clienti dell'Unione Europea**

Se il tenant di Azure Active Directory si trova in uno dei data center in Europa, completare i passaggi aggiuntivi seguenti.

1. Aprire **Services.msc** e arrestare il servizio **agente di provisioning di Microsoft Azure AD Connect**.
2. Passare alla cartella di installazione dell'agente (ad esempio C:\Programmi\Microsoft Azure AD Connect Provisioning Agent).
3. Aprire **SyncAgnt.exe.config** in un editor di testo.
4. Sostituire https://manage.hub.syncfabric.windowsazure.com/Management con **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Sostituire https://provision.hub.syncfabric.windowsazure.com/Provisioning con **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Salvare il file **SyncAgnt.exe.config**.
7. Aprire **Services.msc** e avviare il servizio **agente di provisioning di Microsoft Azure AD Connect**.

**Risoluzione dei problemi dell'agente**

Il [registro eventi di Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) nel computer Windows Server che ospita l'agente contiene gli eventi per tutte le operazioni eseguite dall'agente. Per visualizzare questi eventi:
    
1. Aprire **Eventvwr.msc**.
2. Selezionare **Registri di Windows > Applicazione**.
3. Visualizzare tutti gli eventi registrati sotto **AADSyncAgent**. 
4. Controllare errori e avvisi.

Se si verifica un problema di autorizzazioni con le credenziali di Active Directory o Azure Active Directory fornite nei comandi di Powershell, verrà visualizzato un errore come questo: 
    
![Log eventi](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>Parte 4: Avviare il servizio
Dopo aver completato le parti 1-3, è possibile avviare il servizio di provisioning nel portale di Azure.

1.  Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. In qualsiasi momento è possibile controllare la scheda **Log di controllo** nel portale di Azure per vedere le azioni che sono state eseguite dal servizio di provisioning. I log di controllo elencano tutti i singoli eventi di sincronizzazione eseguiti dal servizio di provisioning, ad esempio quali utenti vengono letti da Workday e successivamente aggiunti o aggiornati in Active Directory. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](active-directory-saas-provisioning-reporting.md)**

5.  Controllare il [registro eventi di Windows](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) nel computer Windows Server che ospita l'agente per eventuali nuovi errori o avvisi. Questi eventi possono essere visualizzati avviando **Eventvwr.msc** sul server e selezionando **Registri di Windows > Applicazione**. Tutti i messaggi correlati al provisioning vengono registrati sotto **AADSyncAgent**. 
    

6. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.

![Portale di Azure](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Configurazione del provisioning utenti in Azure Active Directory
La configurazione del provisioning in Azure Active Directory dipende dei requisiti di provisioning, come descritto nella tabella seguente.

| Scenario | Soluzione |
| -------- | -------- |
| **È necessario eseguire il provisioning degli utenti in Active Directory e Azure AD** | Usare  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **È necessario eseguire il provisioning degli utenti solo in Active Directory** | Usare  **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **È necessario eseguire il provisioning degli utenti solo in Azure AD (solo cloud)** | Usare l'app **Workday to Azure Active Directory provisioning** (Provisioning Workday in Azure Active Directory) nella raccolta di app |

Per istruzioni sulla configurazione di Azure AD Connect, vedere la [documentazione di Azure AD Connect](connect/active-directory-aadconnect.md).

Nelle sezioni seguenti viene descritta la configurazione di una connessione tra Workday e Azure AD per il provisioning degli utenti solo cloud.

> [!IMPORTANT]
> Eseguire la procedura seguente solo se sono presenti utenti solo cloud, di cui è necessario eseguire il provisioning in Azure AD e non in Active Directory locale.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Aggiungere l'app del connettore di provisioning in Azure AD e creare la connessione a Workday

**Per configurare il provisioning da Workday ad Azure Active Directory per gli utenti solo cloud:**

1.  Passare a <https://portal.azure.com>.

2.  Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3.  Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4.  Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5.  Cercare **Workday to Azure AD provisioning** (Provisioning Workday in Azure AD) e aggiungere tale app dalla raccolta.

6.  Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7.  Impostare **Modalità di** **provisioning** su **Automatico**

8.  Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta. Se l'URL non è noto, determinare l'URL corretto da usare insieme al proprio partner di integrazione Workday o al supporto tecnico.

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**.

   * Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.


### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi 

In questa sezione verrà configurato il flusso dei dati utente da Workday in Azure Active Directory per gli utenti solo cloud.

1.  Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Workers to Azure AD** (Sincronizza lavoratori in Azure AD).

2.   Nel campo **Ambito dell'oggetto di origine** è possibile selezionare i set di utenti in Workday inclusi nell'ambito per il provisioning in Azure AD, definendo un set di filtri basati su attributi. L'ambito predefinito è "tutti gli utenti in Workday". Filtri di esempio:

   * Esempio: Ambito per gli utenti con ID lavoratore compreso tra 1000000 e 2000000

      * Attributo: WorkerID

      * Operatore: Corrispondenza REGEX

      * Valore: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Esempio: Solo i lavoratori occasionali, senza i dipendenti

      * Attributo: ContingentID

      * Operatore: NON È NULL

3.  Nel campo **Azioni oggetto di destinazione** è possibile applicare un filtro a livello globale per le azioni di cui è consentita l'esecuzione in Azure AD. **Creazione** e **Aggiornamento** sono le più comuni.

4.  Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory.

5. Fare clic su un mapping di attributi esistente per aggiornarlo oppure fare clic su **Aggiungi nuovo mapping** nella parte inferiore della schermata per aggiungere nuovi mapping. Il mapping di un singolo attributo supporta queste proprietà:

   * **Tipo di mapping**

      * **Diretto**: scrive il valore dell'attributo di Workday nell'attributo di Active Directory, senza modifiche

      * **Costante**: scrive un valore stringa costante statico nell'attributo di Active Directory

      * **Espressione**: consente di scrivere un valore personalizzato per l'attributo di Active Directory, in base a uno o più attributi di Workday. [Per altre informazioni, vedere questo articolo sulle espressioni](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Attributo di origine**: l'attributo utente in Workday. Se l'attributo che si sta cercando non è presente, vedere [Personalizzazione dell'elenco di attributi utente di Workday](#customizing-the-list-of-workday-user-attributes).

   * **Valore predefinito**: facoltativo. Se l'attributo di origine ha un valore vuoto, il mapping eseguirà la scrittura di questo valore.
            Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

   * **Attributo di destinazione**: l'attributo utente in Azure AD.

   * **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping deve essere usato per l'identificazione univoca degli utenti tra Workday e Azure AD. In genere, è impostato sul campo ID lavoratore per Workday, che solitamente è associato all'attributo ID dipendente (nuovo) o a un attributo di estensione in Azure AD.

   * **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

   * **Applica questo mapping**

     * **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente

     * **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente

6. Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

### <a name="part-3-start-the-service"></a>Parte 3: Avviare il servizio
Dopo aver completato le parti 1-2, è possibile avviare il servizio di provisioning.

1.  Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. I singoli eventi di sincronizzazione possono essere visualizzati nella scheda **Log di controllo**. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](active-directory-saas-provisioning-reporting.md)**

5. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Configurazione del writeback degli indirizzi di posta elettronica in Workday
Seguire queste istruzioni per configurare il writeback degli indirizzi di posta elettronica degli utenti da Azure Active Directory in Workday.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Parte 1: Aggiungere l'app del connettore di provisioning e creare la connessione a Workday

**Per configurare il provisioning da Workday in Active Directory:**

1.  Passare a <https://portal.azure.com>.

2.  Sulla barra di spostamento a sinistra selezionare **Azure Active Directory**

3.  Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

4.  Selezionare **Aggiungere un'applicazione** e quindi selezionare la categoria **Tutto**.

5.  Cercare **Workday Writeback** (Writeback Workday) e aggiungere tale applicazione dalla raccolta.

6.  Dopo avere aggiunto l'app e visualizzato la schermata dei dettagli dell'app, selezionare **Provisioning**

7.  Impostare **Modalità di** **provisioning** su **Automatico**

8.  Completare la sezione **Credenziali amministratore** come segue:

   * **Nome utente amministratore**: immettere il nome utente dell'account del sistema di integrazione Workday, aggiungendo il nome di dominio del tenant. Dovrebbe essere simile a: username@contoso4

   * **Password di amministratore:** immettere la password dell'account del sistema di integrazione Workday

   * **URL tenant:** immettere l'URL dell'endpoint dei servizi Web Workday per il tenant. Dovrebbe essere simile a: https://wd3-impl-services1.workday.com/ccx/service/contoso4, dove contoso4 è sostituito dal nome del tenant corretto e wd3-impl è sostituito dalla stringa di ambiente corretta (se necessario).

   * **Indirizzo di posta elettronica per le notifiche:** immettere l'indirizzo di posta elettronica e selezionare la casella di controllo "Invia una notifica di posta elettronica in caso di errore".

   * Fare clic sul pulsante **Test connessione**. Se il test della connessione ha esito positivo, fare clic sul pulsante **Salva** nella parte superiore. In caso contrario, verificare che l'URL e le credenziali per Workday siano validi in Workday.


### <a name="part-2-configure-attribute-mappings"></a>Parte 2: Configurare i mapping degli attributi 


In questa sezione verrà configurato il flusso dei dati utente da Workday in Active Directory.

1.  Nella scheda Provisioning, in **Mapping**, fare clic su **Synchronize Azure AD Users to Workday** (Sincronizza utenti Azure AD in Workday).

2.  Nel campo **Ambito dell'oggetto di origine** è possibile scegliere di filtrare i set di utenti di Azure Active Directory per cui eseguire il writeback degli indirizzi di posta elettronica in Workday. L'ambito predefinito è "tutti gli utenti in Azure AD". 

3.  Nella sezione **Mapping attributi** è possibile definire il mapping dei singoli attributi di Workday agli attributi di Active Directory. Per impostazione predefinita, è presente un mapping per l'indirizzo di posta elettronica. L'ID corrispondente deve tuttavia essere aggiornato in base agli utenti di Azure AD con le voci corrispondenti in Workday. Un metodo comune per garantire la corrispondenza è sincronizzare l'ID lavoratore o l'ID dipendente di Workday in extensionAttribute1-15 in Azure AD e quindi usare questo attributo in Azure AD per associare gli utenti in Workday.

4.  Per salvare i mapping, fare clic su **Salva** nella parte superiore della sezione Mapping attributi.

### <a name="part-3-start-the-service"></a>Parte 3: Avviare il servizio
Dopo aver completato le parti 1-2, è possibile avviare il servizio di provisioning.

1.  Nella scheda **Provisioning** impostare **Stato provisioning** su **Attivato**.

2. Fare clic su **Save**.

3. Verrà avviata la sincronizzazione iniziale, che può richiedere un numero variabile di ore a seconda del numero di utenti in Workday.

4. I singoli eventi di sincronizzazione possono essere visualizzati nella scheda **Log di controllo**. **[Vedere la guida alla creazione di report per il provisioning per istruzioni dettagliate su come leggere i log di controllo](active-directory-saas-provisioning-reporting.md)**

5. Al termine verrà scritto un report di riepilogo di controllo nella scheda **Provisioning**, come illustrato di seguito.


## <a name="customizing-the-list-of-workday-user-attributes"></a>Personalizzazione dell'elenco di attributi utente di Workday
Le app di provisioning di Workday per Active Directory e Azure AD includono entrambe un elenco predefinito di attributi utente di Workday tra cui scegliere. Questi elenchi tuttavia non sono esaustivi. Workday supporta molte centinaia di attributi utente possibili, che possono essere standard o univoci per il tenant di Workday. 

Il servizio di provisioning di Azure AD consente di personalizzare l'elenco o un attributo di Workday per includere tutti gli attributi esposti nell'operazione [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) dell'API Human Resources.

A tale scopo, è necessario usare [Workday Studio](https://community.workday.com/studio-download) per estrarre le espressioni XPath che rappresentano gli attributi che si vuole usare e quindi aggiungerli alla configurazione del provisioning usando l'editor di attributi avanzato nel portale di Azure.

**Per recuperare un'espressione XPath per un attributo utente di Workday:**

1. Scaricare e installare [Workday Studio](https://community.workday.com/studio-download). Per accedere al programma di installazione, è necessario un account della community di Workday.

2. Scaricare il file WDSL Human_Resources Workday dall'URL seguente: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Avviare Workday Studio.

4. Sulla barra dei comandi selezionare l'opzione **Workday > Test Web Service in Tester** (Workday > Testa servizio Web nel tester).

5. Selezionare **External** (Esterno) e quindi selezionare il file Human_Resources WSDL scaricato al passaggio 2.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. Impostare il campo **Location** (Percorso) su `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ma sostituendo "IMPL-CC" con il tipo di istanza effettivo e "TENANT" con il vero nome del tenant.

7. Impostare **Operation** (Operazione) su **Get_Workers**

8.  Fare clic sul piccolo collegamento **configure** (configura) sotto i riquadri relativi a richiesta e risposta per impostare le credenziali di Workday. Selezionare **Authentication** (Autenticazione) e quindi immettere nome utente e password per l'account di sistema di integrazione di Workday. Assicurarsi di formattare il nome utente come name@tenant, lasciando l'opzione **WS-Security UsernameToken** (Oggetto UsernameToken WS-Security) selezionata.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. Selezionare **OK**.

10. Nel riquadro **Request** (Richiesta) incollare il codice XML sottostante e impostare **Employee_ID** sull'ID del dipendente di un utente reale nel tenant di Workday. Selezionare un utente per il quale l'attributo da estrarre sia popolato.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Fare clic su **Send Request** (Invia richiesta) (freccia verde) per eseguire il comando. Se il comando ha esito positivo, la risposta verrà visualizzata nel riquadro **Response** (Risposta). Controllare la risposta per assicurarsi che contenga i dati dell'ID utente immesso e non un errore.

12. In caso di esito positivo, copiare il codice XML dal riquadro **Response** (Risposta) e salvarlo come file XML.

13. Sulla barra dei comandi di Workday Studio selezionare **File > Open File** (File > Apri file) e aprire il file XML appena salvato. Il file verrà aperto nell'editor XML di Workday Studio.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. Nell'albero di file spostarsi a **/env:Envelope > env:Body > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** per trovare i dati dell'utente. 

15. In **wd:Worker** trovare l'attributo da aggiungere e selezionarlo.

16. Copiare l'espressione XPath per l'attributo selezionato dal campo **Document Path** (Percorso documento).

17. Rimuovere il prefisso **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** dall'espressione copiata. 

18. Se l'ultimo elemento dell'espressione copiata è un nodo (ad esempio: "/wd:Birth_Date"), aggiungere **/text()** alla fine dell'espressione. Ciò non è necessario se l'ultimo elemento è un attributo (ad esempio: "/@wd:type").

19. Il risultato dovrebbe essere simile a `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Questo è ciò che verrà copiato nel portale di Azure.


**Per aggiungere l'attributo utente Workday personalizzato alla configurazione del provisioning:**

1. Avviare il [portale di Azure](https://portal.azure.com) e passare alla sezione Provisioning dell'applicazione di provisioning di Workday, come descritto in precedenza in questa esercitazione.

2. Impostare **Stato del provisioning** su **No** e selezionare **Salva**. In questo modo, le modifiche verranno applicate solo quando si è pronti.

3. In **Mapping** selezionare **Synchronize Workers to OnPremises** (Sincronizza ruoli di lavoro con ambiente locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro con Azure AD).

4. Scorrere fino alla parte inferiore della schermata successiva e selezionare **Mostra opzioni avanzate**.

5. Selezionare **Modifica elenco attributi per Workday**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Scorrere fino alla fine dell'elenco di attributi, dove si trovano i campi di input.

7. Per **Nome** immettere un nome visualizzato per l'attributo.

8. Per **Tipo** selezionare il tipo appropriato per l'attributo (il più comune è **String**).

9. Per **Espressione API** immettere l'espressione XPath copiata da Workday Studio. Esempio: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Selezionare **Aggiungi attributo**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Selezionare **Salva** sopra e quindi **Sì** nella finestra di dialogo. Chiudere la schermata Mapping attributi, se è ancora aperta.

12. Tornando nella scheda **Provisioning** selezionare di nuovo **Synchronize Workers to OnPremises** (Sincronizza ruoli di lavoro con ambiente locale) o **Synchronize Workers to Azure AD** (Sincronizza ruoli di lavoro con Azure AD).

13. Selezionare **Aggiungi nuovo mapping**.

14. Il nuovo attributo dovrebbe ora essere visualizzato nell'elenco **Attributo di origine**.

15. Aggiungere un mapping per il nuovo attributo in base alle esigenze.

16. Al termine, ricordarsi di impostare **Stato del provisioning** di nuovo su **Sì** e salvare.


## <a name="known-issues"></a>Problemi noti

* Quando si esegue il comando di Powershell **Add-ADSyncAgentAzureActiveDirectoryConfiguration**, attualmente c'è un problema noto riguardante le credenziali di amministratore globale che non funziona se usano un dominio personalizzato (esempio: admin@contoso.com) . Per aggirare il problema, è possibile creare e usare un account amministratore globale in Azure Active Directory con un dominio onmicrosoft.com (esempio: admin@contoso.onmicrosoft.com).

* È stato risolto un problema precedente relativo ai log di controllo che non compaiono nei tenant di Azure Active Directory che si trovano nell'Unione Europea. Tuttavia è richiesta una configurazione aggiuntiva dell'agente per i tenant di Azure Active Directory che si trovano nell'Unione Europea. Per informazioni dettagliate, vedere [Parte 3: Configurare l'agente di sincronizzazione locale](#Part 3: Configure the on-premises synchronization agent)

## <a name="gdpr-compliance"></a>Conformità al Regolamento generale sulla protezione dei dati (RGPD)

Il [Regolamento generale sulla protezione dei dati (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) è una legge sulla privacy e sulla protezione dei dati dell'Unione europea (UE). L'RGPD impone regole per le aziende, gli enti pubblici, le organizzazioni no profit e altre organizzazioni che offrono beni e servizi a persone nell'UE o che raccolgono e analizzano dati collegati a persone residenti nell'UE. 

Il servizio di provisioning di Azure AD è conforme all'RGPD insieme agli altri servizi e funzionalità Microsoft. Per altre informazioni sulla storia dell'RGPD Microsoft, vedere le [condizioni del servizio](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Tuttavia, poiché la soluzione di provisioning Workday per Active Directory richiede l'installazione di un agente di sincronizzazione in un server aggiunto al dominio, è necessario monitorare alcuni eventi per rimanere conformi all'RGPD.
 
L'agente crea log nel **registro eventi di Windows**, che possono contenere informazioni personali.

Esistono due modi per essere conformi all'RGPD:

1. Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dal registro eventi di Windows. 
2. Conservare i registri eventi di Windows originati dal processo AADSyncAgent da meno di 48 ore

Per informazioni su come configurare la conservazione dei dati per i registri eventi di Windows, vedere [Settings for event logs](https://technet.microsoft.com/library/cc952132.aspx) (Impostazioni dei log eventi). Per informazioni generali sul registro eventi di Windows, vedere [questo articolo](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
* [Informazioni su come configurare l'accesso Single Sign-On tra Workday e Azure Active Directory](active-directory-saas-workday-tutorial.md)
* [Informazioni sull'integrazione di altre applicazioni SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)

