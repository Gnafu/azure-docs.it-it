---
title: Configurare l'account per lo streaming offline di contenuto protetto da Widevine - Azure
description: Questo argomento illustra come configurare l'account di Servizi multimediali di Azure per lo streaming offline di contenuto protetto da Widevine.
services: media-services
keywords: DASH, DRM, modalità offline Widevine, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 9e90951f810c5101a46c29570af8ad71b42be637
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341024"
---
# <a name="offline-widevine-streaming-for-android"></a>Modalità offline dello streaming Widevine per Android

Oltre a proteggere il contenuto per lo streaming online, i servizi di sottoscrizione e noleggio di contenuti multimediali offrono la possibilità di scaricare contenuto utilizzabile anche quando non si è connessi a Internet. Può ad esempio essere necessario scaricare contenuto sullo smartphone o sul tablet per la riproduzione in modalità aereo quando si è disconnessi dalla rete durante il volo. Il download di contenuto può essere utile anche in altri scenari, come nei casi seguenti:

- Alcuni provider di contenuti potrebbero non consentire la distribuzione di licenze DRM oltre i confini di un paese/area geografica. Se un utente vuole consultare il contenuto durante una trasferta all'estero, è necessario il download offline.
- In alcuni paesi/aree geografiche, disponibilità di Internet e/o della larghezza di banda è limitata. Gli utenti possono quindi scegliere di scaricare il contenuto per ottenere una risoluzione sufficientemente elevata a garantire un'esperienza di visualizzazione soddisfacente.

Questo articolo illustra come implementare la riproduzione in modalità offline per contenuto DASH protetto da Widevine su dispositivi Android. Con la tecnologia DRM offline è possibile fornire modelli di sottoscrizione, noleggio e acquisto per i servizi di contenuto offerti in modo da consentire ai clienti di usare il contenuto scaricato anche quando non sono connessi a Internet.

Per creare un'app lettore per Android vengono presentate tre opzioni:

> [!div class="checklist"]
> * L'API Java di ExoPlayer SDK
> * Il binding Xamarin di ExoPlayer SDK
> * Le estensioni EME (Encrypted Media Extension) ed MSE (Media Source Extension) nel browser Chrome per dispositivi mobili versione 62 o successive

L'articolo include inoltre le risposte ad alcune domande frequenti relative allo streaming offline di contenuto protetto da Widevine.

> [!NOTE]
> Tecnologia DRM offline viene fatturato solo per una singola richiesta per ottenere una licenza quando si scarica il contenuto. Tutti gli errori non vengono fatturati.

## <a name="prerequisites"></a>Prerequisiti 

Prima di implementare la tecnologia DRM offline per Widevine su dispositivi Android, è necessario:

- Acquisire familiarità con i concetti relativi alla protezione di contenuto online con Widevine DRM. Questi concetti sono illustrati in dettaglio nei documenti ed esempi seguenti:
    - [Progettazione di un sistema di protezione del contenuto con DRM multiplo e controllo di accesso](design-multi-drm-system-with-access-control.md)
    - [Usare il servizio di crittografia dinamica e di distribuzione di licenze DRM](protect-with-drm.md)
- Clonare https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    È necessario modificare il codice in [Encrypt with DRM using .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) (Eseguire la crittografia con DRM usando .NET) per aggiungere configurazioni Widevine.  
- Acquisire familiarità con Google ExoPlayer SDK per Android, un SDK per lettori video open source in grado di supportare la riproduzione offline con tecnologia Widevine DRM. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [Guida per sviluppatori di ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [Blog per sviluppatori di ExoPlayer](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Configurare la protezione dei contenuti in Servizi multimediali di Azure

Nel metodo [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) eseguire queste operazioni obbligatorie:

1. Specificare in che modo la distribuzione della chiave simmetrica viene autorizzata nel servizio di distribuzione di licenze: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Configurare il modello di licenza Widevine:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Creare ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Abilitare la modalità offline

Per abilitare la modalità **offline** per le licenze Widevine, è necessario configurare il [modello di licenza Widevine](widevine-license-template-overview.md). Nell'oggetto **policy_overrides** impostare la proprietà **can_persist** su **true** come illustrato in [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). Il valore predefinito è false. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Configurazione del lettore Android per la riproduzione offline

Il modo più semplice per sviluppare un'app lettore nativa per dispositivi Android è quello di usare [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), un SDK per lettori video open source. ExoPlayer supporta funzionalità che non sono attualmente supportate dall'API MediaPlayer nativa di Android, inclusi i protocolli di recapito MPEG-DASH e Microsoft Smooth Streaming.

ExoPlayer versione 2.6 e successive include molte classi che supportano la riproduzione offline con Widevine DRM. In particolare, la classe OfflineLicenseHelper fornisce funzioni di utilità per facilitare l'uso di DefaultDrmSessionManager per scaricare, rinnovare e rilasciare licenze offline. Le classi fornite nella cartella "library/core/src/main/java/com/google/android/exoplayer2/offline/" dell'SDK supportano il download di contenuto video offline.

Di seguito sono elencate le classi di ExoPlayer SDK per Android che facilitano la modalità offline:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

Quando creano un'applicazione, gli sviluppatori possono fare riferimento alla [guida per sviluppatori di ExoPlayer](https://google.github.io/ExoPlayer/guide.html) e al [blog](https://medium.com/google-exoplayer) corrispondente. Per il momento Google non ha rilasciato un'implementazione di riferimento o un esempio di codice completamente documentato per il supporto della modalità offline per Widevine nelle app ExoPlayer e pertanto le informazioni sono limitate alla guida e al blog per sviluppatori. 

### <a name="working-with-older-android-devices"></a>Uso con dispositivi Android meno recenti

Per alcuni dispositivi Android meno recenti, è necessario impostare i valori per le proprietà **policy_overrides** seguenti, definite nel [modello di licenza Widevine](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** e **license_duration_seconds**. In alternativa, è possibile impostare queste proprietà su zero, ovvero su una durata infinita o illimitata.  

I valori devono essere impostati per evitare un bug di overflow di numero intero. Per altre informazioni sul problema, vedere https://github.com/google/ExoPlayer/issues/3150 e https://github.com/google/ExoPlayer/issues/3112. <br/>Se non si impostano i valori in modo esplicito, a **PlaybackDurationRemaining** e **LicenseDurationRemaining** verranno assegnati valori molto grandi, ad esempio 9223372036854775807, ovvero il massimo valore positivo per un numero intero a 64 bit. Di conseguenza, la licenza Widevine risulterà scaduta e la decrittografia non verrà eseguita. 

Questo problema non si verifica in Android 5.0 Lollipop o versioni successive perché la 5.0 è la prima versione di Android progettata per il supporto completo di ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) e piattaforme a 64 bit, mentre Android 4.4 KitKat è stato originariamente progettato per il supporto di ARMv7 e piattaforme a 32 bit, come altre versioni di Android precedenti.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Uso di Xamarin per creare un'app Android per la riproduzione di contenuto

È possibile trovare i binding Xamarin per ExoPlayer usando i collegamenti seguenti:

- [Libreria di binding Xamarin per la libreria di Google ExoPlayer](https://github.com/martijn00/ExoPlayerXamarin)
- [Binding Xamarin per il pacchetto NuGet ExoPlayer](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Vedere anche questa conversazione: [Xamarin binding](https://github.com/martijn00/ExoPlayerXamarin/pull/57) (Binding Xamarin). 

## <a name="chrome-player-apps-for-android"></a>App lettore su browser Chrome per Android

A partire da [Chrome per Android versione 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), sono supportate le licenze permanenti in EME. In Chrome per Android è ora supportato anche [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1). È così possibile creare applicazioni per la riproduzione offline in Chrome che gli utenti finali possono usare se dispongono di questa o di una versione successiva del browser. 

Google ha inoltre realizzato un esempio di PWA (Progressive Web App) e lo ha reso disponibile in open source: 

- [Codice sorgente](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Versione ospitata su Google](https://biograf-155113.appspot.com/ttt/episode-2/) (funziona solo in Chrome versione 62 e successive su dispositivi Android)

Se si aggiorna il browser Chrome per dispositivi mobili alla versione 62 o successiva su uno smartphone Android e si testa l'app di esempio ospitata su Google, si noterà che è possibile usare entrambe le modalità di streaming online e riproduzione offline.

Questa app PWA open source è stata creata in Node.js. Se si vuole ospitare una versione personalizzata su un server Ubuntu, tenere presenti i seguenti problemi comunemente riscontrati che possono impedire la riproduzione:

1. Problema di CORS (Cross Origin Resource Sharing): il video di esempio nell'app di esempio è ospitato in https://storage.googleapis.com/biograf-video-files/videos/. Google ha configurato CORS per tutti gli esempi di test ospitati nel bucket di Google Cloud Storage. Questi sono resi disponibili con intestazioni CORS, che specificano in modo esplicito la voce CORS https://biograf-155113.appspot.com (il dominio in cui Google ospita l'esempio), impedendo l'accesso da altri siti. Se si prova a eseguire l'accesso, viene visualizzato l'errore HTTP seguente: Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. (Non è stato possibile eseguire il caricamento di ... non è presente alcuna intestazione 'Access-Control-Allow-Origin' nella risorsa richiesta). Origin ' https:\//13.85.80.81:8080' non è pertanto consentito l'accesso. Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.
2. Problema di certificato: a partire da Chrome versione 58, EME per Widevine richiede HTTPS. È pertanto necessario ospitare l'app di esempio su HTTPS con un certificato X509. Non è possibile usare un normale certificato di test a causa dei requisiti seguenti: È necessario ottenere un certificato che soddisfi i requisiti minimi seguenti:
    - Per Chrome e Firefox, nel certificato deve essere definita l'impostazione relativa al nome alternativo del soggetto (SAN).
    - Il certificato deve avere una CA attendibile. Non è possibile usare un certificato di sviluppo autofirmato.
    - Il certificato deve inoltre avere un nome comune (CN) corrispondente al nome DNS del server Web o del gateway.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="question"></a>Domanda

Come è possibile distribuire licenze permanenti (abilitate per la modalità offline) ad alcuni client o utenti e licenze non permanenti (disabilitate per la modalità offline) ad altri? È necessario duplicare il contenuto e usare chiavi simmetriche distinte?

### <a name="answer"></a>Risposta
Poiché Servizi multimediali versione 3 consente a un'entità Asset di avere più entità StreamingLocators, è possibile avere:

1.  Un'entità ContentKeyPolicy con license_type = "persistent", un'entità ContentKeyPolicyRestriction con attestazione impostata su "persistent" e relativa entità StreamingLocator;
2.  Un'altra entità ContentKeyPolicy con license_type = "nonpersistent", un'entità ContentKeyPolicyRestriction con attestazione impostata su "nonpersistent" e relativa entità StreamingLocator.
3.  Le due entità StreamingLocators hanno diverse entità ContentKey.

In base alla logica di business del servizio token di sicurezza personalizzato, nel token JWT vengono rilasciate diverse attestazioni. Con il token, può essere ottenuta solo la licenza corrispondente e può essere usato solo il relativo URL.

### <a name="question"></a>Domanda

Per Widevine, nella [documentazione di Google introduttiva all'architettura di Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) sono definiti tre diversi livelli di sicurezza, mentre nella [documentazione di Servizi multimediali di Azure relativa al modello di licenza Widevine](widevine-license-template-overview.md) ne vengono presentati cinque. Qual è la relazione o la corrispondenza tra i due diversi set di livelli di sicurezza?

### <a name="answer"></a>Risposta

Nella [documentazione di Google introduttiva all'architettura di Widevine DRM](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) sono definiti i tre livelli di sicurezza seguenti:

1.  Livello di sicurezza 1: tutte le operazioni di elaborazione, crittografia e controllo del contenuto vengono eseguite all'interno dell'ambiente di esecuzione affidabile. In alcuni modelli di implementazione è possibile che l'elaborazione delle informazioni di sicurezza venga eseguita in chip diversi.
2.  Livello di sicurezza 2: la crittografia, ma non l'elaborazione dei dati video, viene eseguita all'interno dell'ambiente di esecuzione affidabile. I buffer decrittografati vengono restituiti al dominio dell'applicazione ed elaborati tramite software o hardware video separato. Al livello 2, le informazioni di crittografia vengono comunque elaborate solo all'interno dell'ambiente di esecuzione affidabile.
3.  Livello di sicurezza 3: non è presente un ambiente di esecuzione affidabile sul dispositivo. È possibile adottare misure appropriate per proteggere le informazioni di crittografia e il contenuto decrittografato nel sistema operativo host. Un'implementazione di livello 3 può includere anche un motore di crittografia hardware, ma solo per migliorare le prestazioni, non la sicurezza.

Parallelamente, nella [documentazione di Servizi multimediali di Azure relativa al modello di licenza Widevine](widevine-license-template-overview.md) la proprietà security_level di content_key_specs può avere i cinque valori seguenti, che definiscono i diversi requisiti di affidabilità client per la riproduzione di contenuto:

1.  È necessaria una soluzione di crittografia white box basata su software.
2.  Sono necessari una soluzione di crittografia software e un decodificatore offuscato.
3.  Il materiale delle chiavi e le operazioni di crittografia devono essere gestiti all'interno di un ambiente di esecuzione affidabile basato su hardware.
4.  Le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.
5.  Le operazioni di crittografia e decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.

Entrambi i set di livelli di sicurezza sono definiti da Google Widevine. La differenza è data dall'uso che ne viene fatto, rispettivamente a livello di architettura o di API. I cinque livelli di sicurezza vengono usati nell'API Widevine. L'oggetto content_key_specs, che contiene security_level, viene deserializzato e passato al servizio di distribuzione globale di Widevine dal servizio licenze Widevine di Servizi multimediali di Azure. La tabella seguente illustra la corrispondenza tra i due set di livelli di sicurezza.

| **Livelli di sicurezza definiti nell'architettura Widevine** |**Livelli di sicurezza usati nell'API Widevine**|
|---|---| 
| **Livello di sicurezza 1**: tutte le operazioni di elaborazione, crittografia e controllo del contenuto vengono eseguite all'interno dell'ambiente di esecuzione affidabile. In alcuni modelli di implementazione è possibile che l'elaborazione delle informazioni di sicurezza venga eseguita in chip diversi.|**security_level=5**: Le operazioni di crittografia e decodifica e l'intera gestione dei file multimediali (con e senza compressione) devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.<br/><br/>**security_level=4**: Le operazioni di crittografia e decodifica del contenuto devono essere eseguite all'interno di un ambiente di esecuzione affidabile basato su hardware.|
**Livello di sicurezza 2**: la crittografia, ma non l'elaborazione dei dati video, viene eseguita all'interno dell'ambiente di esecuzione affidabile. I buffer decrittografati vengono restituiti al dominio dell'applicazione ed elaborati tramite software o hardware video separato. Al livello 2, le informazioni di crittografia vengono comunque elaborate solo all'interno dell'ambiente di esecuzione affidabile.| **security_level=3**: Il materiale delle chiavi e le operazioni di crittografia devono essere gestiti all'interno di un ambiente di esecuzione affidabile basato su hardware. |
| **Livello di sicurezza 3**: nel dispositivo non è presente un ambiente di esecuzione affidabile. È possibile adottare misure appropriate per proteggere le informazioni di crittografia e il contenuto decrittografato nel sistema operativo host. Un'implementazione di livello 3 può includere anche un motore di crittografia hardware, ma solo per migliorare le prestazioni, non la sicurezza. | **security_level=2**: sono necessari una soluzione di crittografia software e un decodificatore offuscato.<br/><br/>**security_level=1**: È necessaria una soluzione di crittografia white box basata su software.|

### <a name="question"></a>Domanda

Perché il download di contenuto richiede molto tempo?

### <a name="answer"></a>Risposta

Per migliorare la velocità di download sono disponibili due diverse strategie:

1.  Abilitare la rete CDN in modo che gli utenti finali raggiungano con maggiore probabilità la rete CDN anziché l'endpoint di origine/streaming per il download di contenuto. Se viene raggiunto l'endpoint di streaming, ogni segmento HLS o frammento DASH viene dinamicamente incluso in un pacchetto e crittografato. Anche se questa latenza è misurabile in millisecondi per singolo segmento o frammento, nel caso di un video della durata di un'ora la latenza accumulata può comportare tempi di download più lunghi.
2.  Offrire agli utenti finali la possibilità di scegliere i livelli di qualità video e le tracce audio da scaricare anziché tutti i contenuti. Per la modalità offline, è inutile scaricare tutti i livelli di qualità. A questo scopo è possibile procedere in due modi:
    1.  Modalità controllata dal client: l'app lettore seleziona automaticamente il livello di qualità video e le tracce audio da scaricare oppure è l'utente stesso a farlo.
    2.  Modalità controllata dal servizio: è possibile usare la funzionalità di manifesto dinamico in Servizi multimediali di Azure per creare un filtro (globale) che limiti la playlist HLS o il file MPD (Media Presentation Description) DASH a un singolo livello di qualità video e a tracce audio selezionate. L'URL di download presentato agli utenti finali includerà pertanto questo filtro.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come implementare la riproduzione in modalità offline per contenuto DASH protetto da Widevine su dispositivi Android.  Sono state inoltre fornite le risposte ad alcune domande frequenti relative allo streaming offline di contenuto protetto da Widevine.
