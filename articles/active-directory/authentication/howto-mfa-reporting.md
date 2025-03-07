---
title: Report di accesso e utilizzo per l'autenticazione a più fattori di Azure-Azure Active Directory
description: Viene descritto come usare la funzionalità dei report di Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25cd36b6b3e8a6974618189985152e55c2676999
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874290"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Report in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication offre diversi report che possono essere usati dall'utente e dall'organizzazione e ai quali è possibile accedere tramite il portale di Azure. La tabella seguente elenca i report disponibili:

| Report | Location | DESCRIZIONE |
|:--- |:--- |:--- |
| Cronologia utenti bloccati | Azure AD > Server MFA > Blocca/Sblocca utenti | Consente di visualizzare la cronologia delle richieste di blocco o sblocco degli utenti. |
| Avvisi di illecito e utilizzo | Azure AD > Accessi | Fornisce informazioni su utilizzo complessivo, riepilogo utenti e dettagli utente; nonché una cronologia degli avvisi di illecito inviati durante l'intervallo di date specificato. |
| Utilizzo di componenti locali | Azure AD > Server MFA > Report attività | Fornisce informazioni sull'utilizzo complessivo di Multi-Factor Authentication tramite l'estensione del server dei criteri di rete, ADFS e il server MFA. |
| Cronologia utenti bypass | Azure AD > Server MFA > Bypass monouso | Fornisce una cronologia delle richieste di bypass di Multi-Factor Authentication per un utente. |
| Stato del server | Azure AD > Server MFA > Stato del server | Consente di visualizzare lo stato dei server di Multi-Factor Authentication associati all'account. |

## <a name="view-mfa-reports"></a>Visualizzare i report MFA

1. Accedere al [portale di Azure](https://portal.azure.com).
2. A sinistra, selezionare **Azure Active Directory** > **Server MFA**.
3. Selezionare il report che si vuole visualizzare.

   ![Rapporto di stato server di autenticazione a più fattori nel portale di Azure](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Report sugli accessi ad Azure AD

Con il **report sulle attività di accesso** nel [portale di Azure](https://portal.azure.com) è possibile ottenere le informazioni necessarie per determinare lo stato e le prestazioni dell'ambiente.

Il report sulle attività di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso, tra cui informazioni sull'utilizzo dell'autenticazione a più fattori. I dati sull'autenticazione a più fattori forniscono informazioni approfondite sul funzionamento di questo tipo di autenticazione nell'organizzazione. Questi dati consentono di rispondere a domande come le seguenti:

- L'accesso è stato verificato con l'autenticazione a più fattori?
- In che modo l'utente ha completato l'autenticazione a più fattori?
- Perché l'utente non è riuscito a completare l'autenticazione a più fattori?
- Quanti utenti vengono verificati tramite l'autenticazione a più fattori?
- Quanti utenti non riescono a completare la verifica tramite l'autenticazione a più fattori?
- Quali sono i problemi comuni relativi all'autenticazione a più fattori riscontrati dagli utenti finali?

Questi dati sono disponibili tramite il [portale di Azure](https://portal.azure.com) e l'[API di creazione report](../reports-monitoring/concept-reporting-api.md).

![Report degli accessi Azure AD nel portale di Azure](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struttura del report sugli accessi

I report delle attività di accesso per l'autenticazione a più fattori permettono di accedere alle informazioni seguenti:

**Autenticazione MFA obbligatoria:** specifica se l'autenticazione a più fattori è obbligatoria o meno per l'accesso. L'autenticazione a più fattori può essere necessaria a causa dell'autenticazione a più fattori per utente, dell'accesso condizionale o di altri motivi. I valori possibili sono **Sì** o **No**.

**Risultato autenticazione MFA:** altre informazioni che specificano se l'autenticazione a più fattori è riuscita o è stata negata.

- Se l'autenticazione a più fattori è riuscita, questa colonna fornisce più informazioni riguardo al modo in cui è stata completata.
   - Azure Multi-Factor Authentication
      - Autenticazione completata nel cloud
      - Autenticazione scaduta a causa dei criteri configurati nel tenant
      - Registrazione richiesta
      - Autenticazione riuscita tramite attestazione nel token
      - Autenticazione riuscita tramite attestazione fornita dal provider esterno
      - Autenticazione riuscita tramite autenticazione avanzata
      - Autenticazione ignorata perché il flusso eseguito è un flusso di accesso del broker Windows
      - Autenticazione ignorata a causa della password di un'app
      - Autenticazione ignorata a causa della posizione
      - Autenticazione ignorata a causa di un dispositivo registrato
      - Autenticazione ignorata a causa di un dispositivo memorizzato
      - Autenticazione completata correttamente
   - Reindirizzamento al provider esterno per l'autenticazione a più fattori

- Se l'autenticazione a più fattori è stata negata, questa colonna fornisce informazioni sul motivo del rifiuto.
   - Autenticazione di Azure Multi-Factor Authentication negata
      - Autenticazione in corso
      - Tentativo di autenticazione duplicato
      - Immissione di un codice non corretto troppe volte
      - Autenticazione non valida
      - Codice di verifica dell'app per dispositivi mobili non valido
      - Errore di configurazione
      - Chiamata telefonica reindirizzata alla segreteria
      - Numero di telefono in formato non valido
      - Errore del servizio
      - Non è stato possibile raggiungere il telefono dell'utente
      - Non è stato possibile inviare la notifica dell'app per dispositivi mobili al dispositivo
      - Non è stato possibile inviare la notifica dell'app per dispositivi mobili
      - L'utente ha rifiutato l'autenticazione
      - L'utente non ha risposto alla notifica dell'app per dispositivi mobili
      - L'utente non ha registrato alcun metodo di verifica
      - L'utente ha immesso un codice non corretto
      - L'utente ha immesso un PIN non corretto
      - L'utente ha chiuso il telefono senza completare l'autenticazione
      - L'utente è bloccato
      - L'utente non ha mai immesso il codice di verifica
      - Utente non trovato
      - Il codice di verifica è già stato usato una volta

**Metodo autenticazione a più fattori:** metodo di autenticazione usato dall'utente per completare l'autenticazione a più fattori. I valori possibili sono:

- SMS
- Notifica dell'app per dispositivi mobili
- Chiamata telefonica (telefono per l'autenticazione)
- Codice di verifica dell'app per dispositivi mobili
- Chiamata telefonica (telefono ufficio)
- Chiamata telefonica (telefono per l'autenticazione alternativo)

**Dettagli autenticazione a più fattori:** versione parzialmente nascosta del numero di telefono, ad esempio +XX XXXXXXXX64.

**Accesso condizionale** Trovare informazioni sui criteri di accesso condizionale che hanno interessato il tentativo di accesso, tra cui:

- Nome criterio
- Concedi controlli
- Controlli di sessione
- Risultato

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Reporting PowerShell per utenti registrati per l'autenticazione a più fattori

Assicurarsi prima di tutto che sia installato il [modulo PowerShell di MSOnline V1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Identificare gli utenti che hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identificare gli utenti che non hanno eseguito la registrazione per MFA usando il codice di PowerShell seguente.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Possibili risultati nei report attività

La tabella seguente può essere usata per risolvere i problemi di autenticazione a più fattori usando la versione scaricata del report attività di autenticazione a più fattori. Non verranno visualizzati direttamente nella portale di Azure.

| Risultato chiamata | DESCRIZIONE | Descrizione generale |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN immesso | L'utente ha immesso un PIN.  Se l'autenticazione ha avuto esito positivo, ha immesso il PIN corretto.  Se l'autenticazione viene negata, è stato immesso un PIN errato o l'utente è impostato sulla modalità standard. |
| SUCCESS_NO_PIN | Solo # immesso | Se l'utente è impostato sulla modalità PIN e l'autenticazione viene negata, significa che l'utente non ha immesso il PIN e ha immesso solo #.  Se l'utente è impostato sulla modalità standard e l'autenticazione ha esito positivo, significa che l'utente ha immesso solo #, operazione corretta da eseguire in modalità standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Non premuto dopo l'immissione | L'utente non ha inviato alcuna cifra DTMF perché # non è stato immesso.  Le altre cifre immesse non vengono inviate a meno che non venga immesso #, a indicare il completamento della voce. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nessun input telefono-timeout | La chiamata è stata risolta, ma non è stata restituita alcuna risposta.  Ciò indica in genere che la chiamata è stata prelevata dalla segreteria telefonica. |
| SUCCESS_PIN_EXPIRED | PIN scaduto e non modificato | Il PIN dell'utente è scaduto ed è stato richiesto di modificarlo, ma la modifica del PIN non è stata completata correttamente. |
| SUCCESS_USED_CACHE | Cache utilizzata | L'autenticazione è riuscita senza una chiamata di autenticazione a più fattori perché si è verificata un'autenticazione precedente riuscita per lo stesso nome utente nell'intervallo di tempo della cache configurato. |
| SUCCESS_BYPASSED_AUTH | Autenticazione ignorata | L'autenticazione ha avuto esito positivo utilizzando un bypass monouso avviato per l'utente.  Per ulteriori informazioni sul bypass, vedere il report Cronologia utenti bypass. |
| SUCCESS_USED_IP_BASED_CACHE | Cache basata su IP utilizzata | L'autenticazione è riuscita senza una chiamata di autenticazione a più fattori, perché una precedente autenticazione riuscita per lo stesso nome utente, tipo di autenticazione, nome dell'applicazione e IP si è verificata entro l'intervallo di tempo della cache configurata. |
| SUCCESS_USED_APP_BASED_CACHE | Cache basata su app usata | L'autenticazione è riuscita senza una chiamata di autenticazione a più fattori, perché una precedente autenticazione riuscita per lo stesso nome utente, tipo di autenticazione e nome dell'applicazione all'interno dell'intervallo di tempo della cache configurata. |
| SUCCESS_INVALID_INPUT | Input telefono non valido | La risposta inviata dal telefono non è valida.  Questo problema può provenire da un computer o un modem fax oppure è possibile che l'utente abbia immesso * come parte del PIN. |
| SUCCESS_USER_BLOCKED | Utente bloccato | Il numero di telefono dell'utente è bloccato.  Un numero bloccato può essere avviato dall'utente durante una chiamata di autenticazione o da un amministratore che usa il portale di Azure. <br> NOTA:   Un numero bloccato è anche un prodotto di un avviso di illecito. |
| SUCCESS_SMS_AUTHENTICATED | SMS autenticato | Per il messaggio di test bidirezionale, l'utente ha risposto correttamente con il proprio codice di accesso monouso (OTP) o OTP + PIN. |
| SUCCESS_SMS_SENT | SMS inviato | Per SMS, è stato inviato il messaggio di testo contenente il codice monouso (OTP).  L'utente immetterà l'OTP o OTP + PIN nell'applicazione per completare l'autenticazione. |
| SUCCESS_PHONE_APP_AUTHENTICATED | App mobile autenticata | L'utente ha eseguito l'autenticazione tramite l'app per dispositivi mobili. |
| SUCCESS_OATH_CODE_PENDING | Codice OATH in sospeso | All'utente è stato richiesto il codice del GIURAmento ma non ha risposto. |
| SUCCESS_OATH_CODE_VERIFIED | Codice OATH verificato | Quando richiesto, l'utente ha immesso un codice di GIURAmento valido. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Codice OATH di fallback verificato | All'utente è stata negata l'autenticazione utilizzando il metodo primario di autenticazione a più fattori, quindi è stato fornito un codice di GIURAmento valido per il fallback. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Domande di sicurezza di fallback risolte | All'utente è stata negata l'autenticazione utilizzando il metodo primario di autenticazione a più fattori e quindi ha risposto correttamente alle domande di sicurezza per il fallback. |
| FAILED_PHONE_BUSY | Autenticazione già in corso | La funzionalità autenticazione a più fattori sta già elaborando un'autenticazione per questo utente.  Questa situazione è spesso causata da client RADIUS che inviano più richieste di autenticazione durante lo stesso accesso. |
| CONFIG_ISSUE | Telefono irraggiungibile | La chiamata è stata tentata, ma non è stato possibile posizionarla o non è stata trovata alcuna risposta.  Sono inclusi il segnale occupato, il segnale di occupato veloce (disconnesso), i tritoni (numero non più nel servizio), il timeout durante la suoneria e così via. |
| FAILED_INVALID_PHONENUMBER | Formato numero di telefono non valido | Il formato del numero di telefono non è valido.  I numeri di telefono devono essere numerici e devono essere costituiti da 10 cifre per il codice paese + 1 (Stati Uniti & Canada). |
| FAILED_USER_HUNGUP_ON_US | Chiamata disconnessa da utente | L'utente ha risposto al telefono, ma si è bloccato senza premere alcun pulsante. |
| FAILED_INVALID_EXTENSION | Interno non valido | L'estensione contiene caratteri non validi.  Sono consentite solo cifre, virgole, * e #.  È possibile utilizzare anche un prefisso @. |
| FAILED_FRAUD_CODE_ENTERED | Codice illecito immesso | L'utente ha scelto di segnalare le illecite durante la chiamata, causando un'autenticazione negata e un numero di telefono bloccato.| 
| FAILED_SERVER_ERROR | Impossibile effettuare chiamate | Il servizio di autenticazione a più fattori non è stato in grado di effettuare la chiamata. |
| FAILED_SMS_NOT_SENT | Impossibile inviare il messaggio di testo | Impossibile inviare il messaggio di testo.  Autenticazione negata. |
| FAILED_SMS_OTP_INCORRECT | OTP SMS non corretta | L'utente ha immesso un codice di accesso monouso (OTP) errato dall'SMS ricevuto.  Autenticazione negata. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN SMS non corretti | L'utente ha immesso un codice di accesso monouso (OTP) errato e/o un PIN utente errato.  Autenticazione negata. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Superato il numero massimo di tentativi di OTP SMS | L'utente ha superato il numero massimo di tentativi di accesso singolo (OTP). |
| FAILED_PHONE_APP_DENIED | App mobile negata | L'utente ha negato l'autenticazione nell'app per dispositivi mobili premendo il pulsante nega. |
| FAILED_PHONE_APP_INVALID_PIN | PIN app mobile non valido | L'utente ha immesso un PIN non valido durante l'autenticazione nell'app per dispositivi mobili. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN app mobile non modificato | L'utente non ha completato correttamente una modifica del PIN richiesta nell'app per dispositivi mobili. |
| FAILED_FRAUD_REPORTED | Illecito segnalato | L'utente ha segnalato una frode nell'app per dispositivi mobili. |
| FAILED_PHONE_APP_NO_RESPONSE | Nessuna risposta da app mobile | L'utente non ha risposto alla richiesta di autenticazione dell'app per dispositivi mobili. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Tutti i dispositivi app mobile bloccati | I dispositivi app per dispositivi mobili per questo utente non rispondono più alle notifiche e sono stati bloccati. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Notifica app mobile non riuscita | Si è verificato un errore durante il tentativo di inviare una notifica all'app per dispositivi mobili sul dispositivo dell'utente. |
| FAILED_PHONE_APP_INVALID_RESULT | Risultato app mobile non valido | L'app per dispositivi mobili ha restituito un risultato non valido. |
| FAILED_OATH_CODE_INCORRECT | Codice OATH errato | L'utente ha immesso un codice di GIURAmento errato.  Autenticazione negata. |
| FAILED_OATH_CODE_PIN_INCORRECT | Codice del GIURAmento + PIN non corretto | L'utente ha immesso un codice di GIURAmento errato e/o un PIN utente errato.  Autenticazione negata. |
| FAILED_OATH_CODE_DUPLICATE | Codice OATH duplicato | L'utente ha immesso un codice di GIURAmento usato in precedenza.  Autenticazione negata. |
| FAILED_OATH_CODE_OLD | Codice OATH non aggiornato | L'utente ha immesso un codice di GIURAmento che precede un codice del GIURAmento usato in precedenza.  Autenticazione negata. |
| FAILED_OATH_TOKEN_TIMEOUT | Timeout risultato codice OATH | L'utente ha impiegato troppo tempo per immettere il codice del GIURAmento ed è già stato raggiunto il timeout del tentativo di autenticazione a più fattori. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Timeout risultato domande di sicurezza | L'utente ha impiegato troppo tempo per immettere risposte alle domande di sicurezza e il tentativo di autenticazione a più fattori si è già verificato. |
| FAILED_AUTH_RESULT_TIMEOUT | Timeout risultato autenticazione | L'utente ha impiegato troppo tempo per completare il tentativo di autenticazione a più fattori. |
| FAILED_AUTHENTICATION_THROTTLED | Autenticazione limitata | Il tentativo di autenticazione a più fattori è stato limitato dal servizio. |

## <a name="next-steps"></a>Passaggi successivi

* [Report sull'utilizzo e informazioni dettagliate su SSPR e multi-factor authentication](howto-authentication-methods-usage-insights.md)
* [Per gli utenti](../user-help/multi-factor-authentication-end-user.md)
* [Dove eseguire la distribuzione](concept-mfa-whichversion.md)
