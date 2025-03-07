---
title: Usare Application Insights per risolvere i problemi relativi ai criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come configurare Application Insights per tenere traccia dell'esecuzione di criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: df5d710792d8c47e491f5b06d88f4050e8eb4a01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508062"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Raccolta di log

Questo articolo illustra i passaggi per la raccolta di log da Azure AD B2C in modo che sia possibile diagnosticare con criteri personalizzati.

>[!NOTE]
>I log attività dettagliati descritti qui attualmente sono progettati **SOLO** per facilitare lo sviluppo di criteri personalizzati. Non usare la modalità di sviluppo in fase di produzione.  I log raccolgono tutte le attestazioni inviate verso e dai provider di identità durante lo sviluppo.  Se usato in fase di produzione, lo sviluppatore si assume la responsabilità delle informazioni personali raccolte nel log di App Insights di cui è proprietario.  Questi log dettagliati vengono raccolti solo quando il criterio è in **MODALITÀ DI SVILUPPO**.


## <a name="use-application-insights"></a>Usare Application Insights

Azure Active Directory B2C supporta una funzionalità per l'invio di dati ad Application Insights,  servizio che consente di diagnosticare le eccezioni e di visualizzare i problemi di prestazioni delle applicazioni.

### <a name="setup-application-insights"></a>Configurazione di Application Insights

1. Accedere al [portale di Azure](https://portal.azure.com). Verificarsi di trovarsi nel tenant con la propria sottoscrizione di Azure (non il tenant di Azure AD B2C in uso).
1. Fare clic su **+ Nuovo** nel menu di navigazione a sinistra.
1. Cercare e selezionare **Application Insights** e quindi fare clic su **Crea**.
1. Completare il modulo e fare clic su **Crea**. Selezionare **Generale** come valore per **Tipo di applicazione**.
1. Dopo aver creato la risorsa, aprire la risorsa di Application Insights.
1. Fare clic su **Proprietà** nel menu a sinistra.
1. Copiare il valore di **Chiave di strumentazione** e salvarlo per la sezione successiva.

### <a name="set-up-the-custom-policy"></a>Configurare i criteri personalizzati

1. Aprire il file RP, ad esempio SignUpOrSignin.xml.
1. Aggiungere gli attributi seguenti all'elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se non esiste già, aggiungere un nodo figlio `<UserJourneyBehaviors>` al nodo `<RelyingParty>`. Deve trovarsi immediatamente dopo `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`.
2. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`. Assicurarsi di sostituire `{Your Application Insights Key}` con la **chiave di strumentazione** ottenuta da Application Insights nella sezione precedente.

   ```XML
   <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   ```

   * `DeveloperMode="true"` indica ad Application Insights di accelerare la telemetria nella pipeline di elaborazione, valida per lo sviluppo, ma vincolata a volumi elevati.
   * `ClientEnabled="true"` invia lo script di Application Insights lato client per tenere traccia della visualizzazione della pagina e degli errori del client (non necessari).
   * `ServerEnabled="true"` invia l'elemento JSON UserJourneyRecorder esistente come evento personalizzato ad Application Insights.
   Esempio:

   ```XML
   <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
   </TrustFrameworkPolicy>
   ```

3. Caricare i criteri.

### <a name="see-the-logs-in-application-insights"></a>Visualizza i log in Application Insights

>[!NOTE]
> I log di Application Insights possono essere visualizzati dopo un breve ritardo (meno di cinque minuti).

1. Aprire la risorsa di Application Insights creata nel [portale di Azure](https://portal.azure.com).
1. Nel menu **Panoramica** fare clic su **Analytics**.
1. Aprire una nuova scheda in Application Insights.
1. Di seguito viene indicato un elenco di query che è possibile usare per visualizzare i log

| Query | Descrizione |
|---------------------|--------------------|
traces | Consente di visualizzare tutti i log generati da Azure AD B2C |
traces \| where timestamp > ago(1d) | Consente di visualizzare tutti i log generati da Azure AD B2C nell'ultimo giorno

Le voci possono essere lunghe.  Per visualizzarle meglio è possibile esportarle in formato CSV.

Per altre informazioni sullo strumento Analytics, vedere [qui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>La community ha sviluppato un visualizzatore di percorsi utente per supportare gli sviluppatori di identità.  Non è supportato da Microsoft ed reso disponibile esclusivamente così com'è.  Legge l'istanza di Application Insights e restituisce una visualizzazione strutturata degli eventi di percorso utente.  Ottenere il codice sorgente e distribuirlo nella soluzione.

La versione del visualizzatore che legge gli eventi da Application Insights è disponibile [qui](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>I log attività dettagliati descritti qui attualmente sono progettati **SOLO** per facilitare lo sviluppo di criteri personalizzati. Non usare la modalità di sviluppo in fase di produzione.  I log raccolgono tutte le attestazioni inviate verso e dai provider di identità durante lo sviluppo.  Se usato in fase di produzione, lo sviluppatore si assume la responsabilità delle informazioni personali raccolte nel log di App Insights di cui è proprietario.  Questi log dettagliati vengono raccolti solo quando il criterio è in **MODALITÀ DI SVILUPPO**.

[Repository GitHub di esempi di criteri personalizzati non supportati e strumenti correlati](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Fasi successive

Esplorare i dati in Application Insights per capire il funzionamento di B2C sottostante del framework dell'esperienza di gestione delle identità per distribuire le proprie esperienze di gestione delle identità.
