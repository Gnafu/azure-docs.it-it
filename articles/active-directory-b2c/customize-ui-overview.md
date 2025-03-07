---
title: Informazioni sulla personalizzazione dell'interfaccia utente in Azure Active Directory B2C | Microsoft Docs
description: Come personalizzare l'interfaccia utente per le applicazioni che usano Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ae12742c2ad50d5bf1caaf14ae2f6d34bd6d3a2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880796"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Informazioni sulla personalizzazione dell'interfaccia utente in Azure Active Directory B2C

La possibilità di personalizzare e personalizzare l'interfaccia utente che Azure Active Directory B2C (Azure AD B2C) serve per le applicazioni è importante per offrire un'esperienza ottimale ai clienti. Queste esperienze includono iscrizione, accesso, modifica del profilo e reimpostazione della password. Questo articolo fornisce informazioni su come personalizzare l'interfaccia utente delle applicazioni.

L'interfaccia utente dell'applicazione si personalizza in modo diverso in base alle proprie esigenze rispetto a queste esperienze. Ad esempio:

- Se si usano [flussi utente](active-directory-b2c-reference-policies.md) per fornire esperienze di iscrizione, accesso, reimpostazione della password o modifica del profilo all'interno dell'applicazione, si usa il [portale di Azure per personalizzare l'interfaccia utente](tutorial-customize-ui.md).
- Se si usa un flusso utente v2, è possibile usare un [modello di layout di pagina](#page-layout-templates) per modificare l'aspetto delle pagine del flusso utente senza un'ulteriore personalizzazione. È ad esempio possibile applicare un tema di colore blu oltremare o grigio ardesia a tutte le pagine nel flusso utente.
- Se si forniscono solo l'accesso, la relativa pagina di reimpostazione della password e le e-mail di verifica, si usano gli stessi passaggi di personalizzazione da seguire per una [pagina di accesso di Azure AD](../active-directory/fundamentals/customize-branding.md).
- Se i clienti provano a modificare il proprio profilo prima di effettuare l'accesso, vengono reindirizzati a una pagina che si può personalizzare usando gli stessi passaggi seguiti per personalizzare la pagina di accesso di Azure AD.
- Se si usano [criteri personalizzati](active-directory-b2c-overview-custom.md) per fornire iscrizione, accesso, reimpostazione della password o modifica del profilo all'interno dell'applicazione, si usano i [file dei criteri per personalizzare l'interfaccia utente](active-directory-b2c-ui-customization-custom.md).
- Se è necessario fornire contenuto dinamico in base a decisione del cliente, si usano [criteri personalizzati che consentono di cambiare il contenuto della pagina](active-directory-b2c-ui-customization-custom-dynamic.md) in base a un parametro inviato in una stringa di query. L'immagine di sfondo della pagina di accesso o di iscrizione ad Azure AD B2C viene ad esempio modificata in base a un parametro passato dall'applicazione Web o per dispositivi mobili.
- È possibile abilitare il codice JavaScript lato client nei [flussi utente](user-flow-javascript-overview.md) o nei [criteri personalizzati](page-layout.md) di Azure AD B2C.

Azure AD B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato [Condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/). In fase di esecuzione, il contenuto viene caricato da un URL specificato in un flusso utente o in un criterio. Per pagine diverse si specificano URL diversi. Dopo il caricamento dall'URL, il contenuto viene unito con un frammento HTML inserito da Azure AD B2C e quindi mostrato al cliente.

Quando si usano file HTML e CSS per personalizzare l'interfaccia utente, consultare il materiale sussidiario seguente prima di iniziare:

- Azure AD B2C unisce contenuto HTML alle pagine. Non copiare e provare a modificare il contenuto predefinito incluso da Azure AD B2C. È preferibile creare il contenuto HTML da zero e usare il contenuto predefinito come riferimento.
- JavaScript può ora essere incluso nel contenuto personalizzato.
- Le versioni di browser supportate sono:
    - Internet Explorer 11, 10 e Microsoft Edge
    - Supporto limitato per Internet Explorer 9 e 8
    - Google Chrome 42.0 e versioni successive
    - Mozilla Firefox 38.0 e versioni successive
- Assicurarsi di non includere tag Form nel codice HTML, perché interferirebbero con le operazioni POST generate dal codice HTML inserito da Azure AD B2C.

## <a name="page-layout-templates"></a>Modelli di layout di pagina

Per i flussi utente v2, è possibile scegliere un modello predefinito che offre un aspetto migliore delle pagine predefinite e viene usato come base ottimale per la personalizzazione.

Nel menu a sinistra, in **Personalizza**, selezionare **Layout di pagina**. Selezionare quindi **Modello (anteprima)** .

![Elenco a discesa Selezione modello nella pagina flusso utente di portale di Azure](media/customize-ui-overview/template.png)

Selezionare un modello dall'elenco. Il modello **Blu oltremare**, ad esempio, applica il layout seguente alle pagine del flusso utente:

![Esempio di modello blu oceano di cui è stato eseguito il rendering nella pagina di accesso per l'iscrizione](media/customize-ui-overview/ocean-blue.png)

Quando si sceglie un modello, il layout selezionato viene applicato a tutte le pagine nel flusso utente e l'URI per ogni pagina è visibile nel campo **URI della pagina personalizzata**.

## <a name="where-do-i-store-ui-content"></a>Dove archiviare il contenuto dell'interfaccia utente

Quando si usano i file HTML e CSS per personalizzare l'interfaccia utente, è possibile ospitare il contenuto dell'interfaccia utente ovunque, ad esempio in [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md), server Web, reti CDN, AWS S3 o sistemi di condivisione file. L'aspetto importante è che il contenuto sia ospitato in un endpoint HTTPS disponibile pubblicamente con CORS abilitato. Quando si specifica un URL nel contenuto, è necessario usare un URL assoluto.

## <a name="how-do-i-get-started"></a>Come iniziare?

Per personalizzare l'interfaccia utente occorre procedere come segue:

- Creare contenuto HTML ben formato con un elemento `<div id="api"></div>` vuoto che si trova in un punto all'interno di `<body>`. Questo elemento corrisponde al punto in cui viene inserito il contenuto di Azure AD B2C. L'esempio seguente mostra una pagina minima:

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- Ospitare il contenuto in un endpoint HTTPS in cui è consentita la condivisione CORS. Entrambi i metodi di richiesta GET e OPTIONS devono essere abilitati quando si configura CORS.
- Usare CSS per applicare uno stile agli elementi dell'interfaccia utente inseriti nella pagina da Azure AD B2C. L'esempio seguente illustra un semplice file CSS che include anche le impostazioni per gli elementi HTML inseriti all'iscrizione:

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

- Creare o modificare un criterio per usare il contenuto creato.

La tabella seguente elenca i frammenti HTML che Azure AD B2C unisce nell'elemento `<div id="api"></div>` all'interno del contenuto.

| Pagina inserita | Descrizione del codice HTML |
| ------------- | ------------------- |
| Selezione del provider di identità | Contiene un elenco di pulsanti per i provider di identità che il cliente può scegliere durante la procedura di iscrizione o di accesso. Questi pulsanti includono i provider di identità basati su social network, ad esempio Facebook e Google, o gli account locali (basati su indirizzo di posta elettronica o nome utente). |
| Iscrizione dell'account locale | Contiene un modulo per eseguire l'iscrizione dell'account locale in base a un indirizzo di posta elettronica o a un nome utente. Il modulo può contenere diversi controlli di input, ad esempio caselle per l'immissione di testo, caselle per l'immissione della password, pulsanti di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| Iscrizione dell'account di social networking | Può essere visualizzata quando si effettua l'iscrizione usando un account esistente di un provider di identità basato su social network, ad esempio Facebook o Google. Viene usata quando è necessario raccogliere informazioni aggiuntive dal cliente attraverso un modulo di iscrizione. |
| Unificata per l'iscrizione o l'accesso | Gestisce sia l'iscrizione che l'accesso dei clienti, che possono usare provider di identità basati su social network, come Facebook o Google, o account locali. |
| Autenticazione a più fattori | Gli utenti possono verificare il proprio numero di telefono (tramite SMS o chiamata vocale) durante la procedura di iscrizione o di accesso. |
| Errore | Fornisce informazioni sugli errori al cliente. |


## <a name="how-do-i-localize-content"></a>Come localizzare il contenuto

Si può localizzare il contenuto HTML abilitando la [personalizzazione della lingua](active-directory-b2c-reference-language-customization.md) nel tenant di Azure AD B2C. L'abilitazione di questa funzionalità consente Azure ad B2C di inviare il `ui-locales` parametro OpenID Connect all'endpoint. Il server di contenuti può usare questo parametro per fornire pagine HTML personalizzate specifiche della lingua.

È possibile eseguire il pull del contenuto da posizioni diverse in base alle impostazioni locali in uso. Nell'endpoint abilitato per CORS, configurare una struttura di cartelle in cui ospitare il contenuto per specifiche lingue. Se si inserisce il valore del carattere jolly {Culture:RFC5646} verrà chiamata la lingua corretta. Ad esempio, l'URI della pagina personalizzata potrebbe essere simile a `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html`. È possibile caricare la pagina in francese eseguendo il pull del contenuto da `https://contoso.blob.core.windows.net/fr/myHTML/unified.html`

## <a name="examples"></a>Esempi

Per esempi di personalizzazione, scaricare ed esaminare questi [file modello di esempio](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).

## <a name="next-steps"></a>Passaggi successivi

- Se si usano i **flussi utente**, è possibile iniziare a personalizzare l'interfaccia utente con l'esercitazione:

    [Personalizzare l'interfaccia utente delle applicazioni in Azure Active Directory B2C](tutorial-customize-ui.md).
- Se si usano **criteri personalizzati**, è possibile iniziare a personalizzare l'interfaccia utente con l'articolo:

    [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
