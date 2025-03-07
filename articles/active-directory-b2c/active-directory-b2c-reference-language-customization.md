---
title: Personalizzazione della lingua in Azure AD B2C
description: Informazioni su come personalizzare l'esperienza del linguaggio nei flussi utente.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015572"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalizzazione della lingua in Azure AD B2C

La personalizzazione della lingua in Azure Active Directory B2C (Azure AD B2C) consente al flusso utente di modificare la lingua in base alle esigenze dei clienti. Microsoft fornisce le traduzioni per [36 lingue](#supported-languages), ma è anche possibile inserire le proprie traduzioni per qualsiasi lingua. Anche se l'esperienza è disponibile per una sola lingua, è possibile personalizzare qualsiasi testo nelle pagine.

## <a name="how-language-customization-works"></a>Funzionamento della personalizzazione della lingua

La personalizzazione della lingua consente di selezionare le lingue in cui è disponibile il flusso utente. Dopo aver abilitato la funzionalità, è possibile specificare il parametro della stringa di query, `ui_locales`, dall'applicazione. Quando si esegue la chiamata ad Azure AD B2C, la pagina viene tradotta nelle impostazioni locali indicate. Questo tipo di configurazione garantisce il controllo completo sulle lingue del flusso utente e ignora le impostazioni della lingua del browser del cliente.

Potrebbe non essere necessario un tale livello di controllo sulle lingue visualizzate dal cliente. Se non si specifica un parametro `ui_locales`, l'esperienza del cliente è determinata dalle impostazioni del browser. È comunque possibile controllare le lingue in cui il flusso utente è tradotto aggiungendole come lingue supportate. Se il browser del cliente è impostato su una lingua che non si vuole supportare, viene visualizzata la lingua selezionata come predefinita nelle impostazioni cultura supportate.

* **Lingua specificata tramite ui-locales**: Dopo aver abilitato la personalizzazione della lingua, il flusso utente viene tradotto nella lingua specificata.
* **Lingua richiesta dal browser**: Se non è stato specificato alcun parametro `ui_locales`, il flusso utente viene tradotto nella lingua richiesta dal browser *se è supportata*.
* **Lingua predefinita nei criteri**: Se il browser non specifica alcuna lingua oppure ne specifica una non supportata, il flusso utente viene tradotto nella lingua predefinita del flusso.

> [!NOTE]
> Se si fa uso di attributi utente personalizzati, è necessario fornire le traduzioni. Per altre informazioni, vedere [Personalizzazione delle stringhe](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Supporto delle lingue richieste per ui_locales

Per i criteri creati prima della disponibilità generale della personalizzazione della lingua occorre prima abilitare questa funzionalità. Per i criteri e i flussi utente creati successivamente la personalizzazione della lingua è abilitata per impostazione predefinita.

Quando si abilita la personalizzazione della lingua in un flusso utente, è possibile controllare la lingua del flusso utente aggiungendo il parametro `ui_locales`.

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente da abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare **Abilita personalizzazione della lingua**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selezionare le lingue abilitate per il flusso utente

Abilitare un set di lingue in cui tradurre il flusso utente quando richiesto dal browser senza il parametro `ui_locales`.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare una lingua che si vuole supportare.
1. Nel riquadro delle proprietà impostare **Attivato** su **Sì**.
1. Scegliere **Salva** nella parte superiore del riquadro delle proprietà.

>[!NOTE]
>Se non viene specificato un parametro `ui_locales`, la pagina viene tradotta nella lingua del browser del cliente solo se è abilitata.
>

## <a name="customize-your-strings"></a>Personalizzazione delle stringhe

La personalizzazione della lingua consente di personalizzare qualsiasi stringa nel flusso utente.

1. Assicurarsi che la personalizzazione della lingua sia abilitata nel flusso utente in base alle istruzioni precedenti.
1. Nella pagina **Lingua** del flusso utente selezionare la lingua che si vuole personalizzare.
1. In **File di risorse a livello di pagina** selezionare la pagina che si vuole modificare.
1. Selezionare **Scarica impostazioni predefinite** (o **Scarica override** se in precedenza si è già modificata questa lingua).

Questa procedura permette di ottenere un file JSON con cui iniziare a modificare le stringhe.

### <a name="change-any-string-on-the-page"></a>Modificare stringhe nella pagina

1. In un editor JSON aprire il file JSON scaricato seguendo le istruzioni precedenti.
1. Trovare l'elemento da modificare. È possibile trovare il valore `StringId` della stringa che si sta cercando o cercare l'attributo `Value` che si vuole modificare.
1. Aggiornare l'attributo `Value` con i dati da visualizzare.
1. Per ogni stringa che si vuole modificare impostare `Override` su `true`.
1. Salvare il file e caricare le modifiche. Il controllo di caricamento si trova nella stessa posizione in cui è stato scaricato il file JSON.

> [!IMPORTANT]
> Se è necessario eseguire l'override di una stringa, assicurarsi di impostare il valore `Override` su `true`. Se il valore non viene modificato, la voce viene ignorata.

### <a name="change-extension-attributes"></a>Cambiare gli attributi di estensione

Per modificare la stringa di un attributo utente personalizzato o aggiungerne uno al file JSON, usare il formato seguente:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Sostituire `<ExtensionAttribute>` con il nome dell'attributo utente personalizzato.

Sostituire `<ExtensionAttributeValue>` con la nuova stringa da visualizzare.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornire un elenco di valori usando LocalizedCollections

Se si vuole specificare un elenco preimpostato di valori per le risposte, è necessario creare un attributo `LocalizedCollections`. `LocalizedCollections` è una matrice di coppie `Name`-`Value`. L'ordine degli elementi sarà l'ordine in che cui vengono visualizzati. Per aggiungere `LocalizedCollections`, usare il formato seguente:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` è l'attributo utente per il quale l'attributo `LocalizedCollections` rappresenta una risposta.
* `Name` è il valore che viene visualizzato all'utente.
* `Value` è il valore restituito nell'attestazione quando questa opzione è selezionata.

### <a name="upload-your-changes"></a>Caricamento delle modifiche

1. Dopo aver completato le modifiche al file JSON, tornare al tenant B2C.
1. Selezionare **Flussi utente** e fare clic sul flusso utente che si vuole abilitare per le traduzioni.
1. Selezionare **Lingue**.
1. Selezionare la lingua in cui si vuole tradurre.
1. Selezionare la pagina per cui si vogliono fornire traduzioni.
1. Selezionare l'icona della cartella e selezionare il file JSON da caricare.

Le modifiche vengono salvate automaticamente nel flusso utente.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizzare l'interfaccia utente della pagina usando la personalizzazione della lingua

Esistono due modi per localizzare il contenuto HTML. Un modo consiste nell'attivare la [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md). L'abilitazione di questa funzionalità consente Azure ad B2C di inviare il parametro `ui-locales`OpenID Connect,, all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

In alternativa, è possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS è possibile configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly `{Culture:RFC5646}` verrà chiamata la lingua corretta. Ad esempio, supponiamo che questo sia l'URI della pagina personalizzata:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

È possibile caricare la pagina in `fr`. Quando la pagina esegue il pull del contenuto HTML e CSS, lo fa da:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Aggiungere lingue personalizzate

È anche possibile aggiungere per cui attualmente Microsoft non fornisce traduzioni. Sarà necessario fornire le traduzioni per tutte le stringhe contenute nel flusso utente. I codici della lingua e delle impostazioni internazionali sono limitati a quelli nello standard ISO 639-1.

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
2. Fare clic sul flusso utente in cui si vuole aggiungere le lingue personalizzate e quindi fare clic su **Lingue**.
3. Selezionare **Aggiungi lingua personalizzata** nella parte superiore della pagina.
4. Nel riquadro del contesto che viene visualizzato identificare la lingua per cui si stanno inserendo le traduzioni immettendo un codice delle impostazioni locali valido.
5. Per ogni pagina è possibile scaricare un set di sostituzioni per la lingua inglese e lavorare sulle traduzioni.
6. Una volta terminato con i file JSON, è possibile caricarli per ogni pagina.
7. Selezionare **Abilita**. Il flusso utente può ora visualizzare la lingua per gli utenti.
8. Salvare la lingua.

>[!IMPORTANT]
>È necessario abilitare le lingue personalizzate o caricare gli override per la lingua prima di poterla salvare.

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="page-ui-customization-labels-as-overrides"></a>Etichette di personalizzazione dell'interfaccia utente della pagina come sostituzioni

Quando si abilita la personalizzazione della lingua, le modifiche precedenti apportate alle etichette usando la personalizzazione dell'interfaccia utente della pagina vengono rese persistenti in un file JSON per la lingua Inglese (en). È possibile continuare a modificare le etichette e le altre stringhe caricando le risorse di lingua nella funzionalità di personalizzazione della lingua.

### <a name="up-to-date-translations"></a>Traduzioni aggiornate

Microsoft si impegna a fornire le traduzioni più aggiornate. Continuerà a migliorare le traduzioni e a garantirne la conformità. Verranno identificati i bug e le modifiche alla terminologia globale e verranno eseguiti aggiornamenti compatibili con il flusso utente.

### <a name="support-for-right-to-left-languages"></a>Supporto per lingue da destra a sinistra

Microsoft non fornisce attualmente supporto per le lingue da destra a sinistra. A questo scopo, è possibile usare impostazioni locali personalizzate e modificare la modalità di visualizzazione delle stringhe tramite CSS. Se si ha bisogno di questa funzionalità, votarla in [Commenti e suggerimenti su Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduzioni per provider di identità basati su social network

Microsoft fornisce il parametro OIDC `ui_locales` per gli account di accesso ai social network, che però non viene riconosciuto da alcuni provider di identità basati su social network, tra cui Facebook e Google.

### <a name="browser-behavior"></a>Comportamento dei browser

Chrome e Firefox richiedono entrambi la relativa lingua preimpostata. Se è supportata, tale lingua viene visualizzata prima di quella predefinita. Microsoft Edge attualmente non richiede alcuna lingua e passa direttamente alla lingua predefinita.

## <a name="supported-languages"></a>Lingue supportate

Azure AD B2C include il supporto per le lingue seguenti. Le lingue dei flussi utente sono fornite da Azure AD B2C. I linguaggi di notifica dell'autenticazione a più fattori sono forniti da [Azure](../active-directory/authentication/concept-mfa-howitworks.md)multi-factor authentication.

| Linguaggio              | Codice lingua | Flussi degli utenti         | Notifiche multi-factor authentication  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabo                | ar            | :x:                | :heavy_check_mark: |
| Bulgaro             | BG            | :x:                | :heavy_check_mark: |
| Bengali                | bn            | :heavy_check_mark: | :x:                |
| Catalano               | CA            | :x:                | :heavy_check_mark: |
| Ceco                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| Danese                | da            | :heavy_check_mark: | :heavy_check_mark: |
| Tedesco                | de            | :heavy_check_mark: | :heavy_check_mark: |
| Greco                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| Inglese               | it-IT            | :heavy_check_mark: | :heavy_check_mark: |
| Spagnolo               | es            | :heavy_check_mark: | :heavy_check_mark: |
| Estone              | et            | :x:                | :heavy_check_mark: |
| Basco                | UE            | :x:                | :heavy_check_mark: |
| Finlandese               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| Francese                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| Galiziano              | GL            | :x:                | :heavy_check_mark: |
| Gujarati              | gu            | :heavy_check_mark: | :x:                |
| Ebraico                | he            | :x:                | :heavy_check_mark: |
| Hindi                 | hi            | :heavy_check_mark: | :heavy_check_mark: |
| Croato              | ora            | :heavy_check_mark: | :heavy_check_mark: |
| Ungherese             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| Indonesiano            | id            | :x:                | :heavy_check_mark: |
| Italiano               | it            | :heavy_check_mark: | :heavy_check_mark: |
| Giapponese              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| Kazako                | KK            | :x:                | :heavy_check_mark: |
| Kannada               | kn            | :heavy_check_mark: | :x:                |
| Coreano                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| Lituano            | lt            | :x:                | :heavy_check_mark: |
| Lettone               | LV            | :x:                | :heavy_check_mark: |
| Malayalam             | ml            | :heavy_check_mark: | :x:                |
| Marathi               | mr            | :heavy_check_mark: | :x:                |
| Malese                 | ms            | :heavy_check_mark: | :heavy_check_mark: |
| Norvegese Bokmål      | nb            | :heavy_check_mark: | :x:                |
| Olandese                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| Norvegese             | no            | :x:                | :heavy_check_mark: |
| Punjabi               | pa            | :heavy_check_mark: | :x:                |
| Polacco                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| Portoghese - Brasile   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| Portoghese - Portogallo | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| Rumeno              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| Russo               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| Slovacco                | sk            | :heavy_check_mark: | :heavy_check_mark: |
| Sloveno             | SL            | :x:                | :heavy_check_mark: |
| Serbo-alfabeto cirillico    | SR-Cryl-CS    | :x:                | :heavy_check_mark: |
| Serbo-alfabeto latino       | Sr-Latn-CS    | :x:                | :heavy_check_mark: |
| Svedese               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| Tamil                 | ta            | :heavy_check_mark: | :x:                |
| Telugu                | te            | :heavy_check_mark: | :x:                |
| Thai                  | °            | :heavy_check_mark: | :heavy_check_mark: |
| Turco               | tr            | :heavy_check_mark: | :heavy_check_mark: |
| Ucraino             | Regno Unito            | :x:                | :heavy_check_mark: |
| Vietnamita            | vi            | :x:                | :heavy_check_mark: |
| Cinese (semplificato)  | zh-hans       | :heavy_check_mark: | :heavy_check_mark: |
| Cinese (tradizionale) | zh-hant       | :heavy_check_mark: | :heavy_check_mark: |
