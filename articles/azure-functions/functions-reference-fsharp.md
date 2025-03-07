---
title: Guida di riferimento per gli sviluppatori di Funzioni di Azure in F# | Documentazione Microsoft
description: Informazioni su come sviluppare Funzioni di Azure usando lo script F#.
services: functions
documentationcenter: fsharp
author: sylvanc
manager: jbronsk
keywords: Funzioni di Azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server, F#
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.service: azure-functions
ms.devlang: fsharp
ms.topic: reference
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: 23e9ffa5c86674cb34951f29573e033b4a904941
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442237"
---
# <a name="azure-functions-f-developer-reference"></a>Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#

F# per Funzioni di Azure è una soluzione che consente di eseguire facilmente piccole parti di codice, o "funzioni", nel cloud. I dati vengono trasmessi alla funzione F# tramite argomenti della funzione. I nomi di argomento sono specificati in `function.json`e sono disponibili nomi predefiniti per l'accesso a elementi quali il logger delle funzioni e i token di annullamento. 

>[!IMPORTANT]
>Lo script F# (.fsx) è supportato solo dalla [versione 1.x](functions-versions.md#creating-1x-apps) del runtime di Funzioni di Azure. Se si desidera usare F# con la versione 2.x del runtime, è necessario usare un progetto della libreria di classi F# (.fs) precompilato. Creare, gestire e pubblicare un progetto della libreria di classi F# con Visual Studio, come si farebbe con un [progetto della libreria di classi C#](functions-dotnet-class-library.md). Per altre informazioni sulle versioni di Funzioni, vedere [Panoramica delle versioni del runtime per Funzioni di Azure](functions-versions.md).

Questo articolo presuppone che l'utente abbia già letto [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md).

## <a name="how-fsx-works"></a>Funzionamento del file con estensione fsx
Un file `.fsx` è uno script F#. Può essere considerato un progetto F# contenuto in un singolo file. Il file contiene il codice per il programma (in questo caso la funzione di Azure) e direttive per la gestione delle dipendenze.

Quando si usa un file `.fsx` per una funzione di Azure, gli assembly normalmente necessari sono inclusi automaticamente, consentendo di concentrarsi sulla funzione anziché sul codice "boilerplate".

## <a name="folder-structure"></a>Struttura di cartelle

La struttura di cartelle per un progetto di script F# ha un aspetto simile al seguente:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

È presente un file [host.json](functions-host-json.md) condiviso che può essere usato per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione fsx) e il file di configurazione delle associazioni (function.json).

Le estensioni di associazione richieste nella [versione 2.x](functions-versions.md) del runtime di Funzioni sono definite nel file `extensions.csproj`, con gli effettivi file di libreria inclusi nella cartella `bin`. Quando si sviluppa una funzione in locale, è necessario [registrare le estensioni di associazione](./functions-bindings-register.md#extension-bundles). Quando si sviluppano funzioni nel portale di Azure, la registrazione viene eseguita automaticamente.

## <a name="binding-to-arguments"></a>Associazione agli argomenti
Ogni associazione supporta set di argomenti, come descritto nei dettagli in [Guida di riferimento per gli sviluppatori di trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md). Ad esempio, una delle associazioni di argomento supportate da un trigger del BLOB è un oggetto POCO, che può essere espresso con un record F#. Ad esempio:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

La funzione F# di Azure richiederà uno o più argomenti. Per "argomenti di Funzioni di Azure" si intendono argomenti di *input* e argomenti di *output*. Un argomento di input rappresenta un input per la funzione F# di Azure. Un argomento di *output* è costituito da dati modificabili oppure è un argomento `byref<>` usato per passare di nuovo dati *in uscita* dalla funzione.

Nell'esempio precedente, `blob` è un argomento di input, mentre `output` è un argomento di output. Si noti che è stato usato `byref<>` per `output`. Non è necessario aggiungere l'annotazione `[<Out>]`. L'uso di un tipo `byref<>` consente alla funzione di cambiare il record o l'oggetto cui l'argomento fa riferimento.

Quando un record F# viene usato come tipo di input, la definizione del record deve essere contrassegnata con `[<CLIMutable>]` per consentire al framework di Funzioni di Azure di impostare i campi in modo appropriato prima di passare il record alla funzione. `[<CLIMutable>]` genera setter in background per le proprietà del record. Ad esempio:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

Una classe F# può essere usata negli argomenti di input e di output. Per una classe, le proprietà necessitano in genere di getter e setter. Ad esempio:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Registrazione
Per registrare l'output nei [log in streaming](../app-service/troubleshoot-diagnostic-logs.md) in F#, la funzione deve accettare un argomento di tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Per coerenza è consigliabile denominare questo argomento `log`. Ad esempio:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
È possibile usare il flusso di lavoro `async`, ma il risultato deve restituire un oggetto `Task`. Questa operazione può essere eseguita con `Async.StartAsTask`, ad esempio:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token di annullamento
Se la funzione deve gestire l'arresto normale, è possibile assegnarle un argomento [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) . È possibile combinare questo argomento con `async`, ad esempio:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importazione di spazi dei nomi
Gli spazi dei nomi possono essere aperti nel modo consueto:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

I seguenti spazi dei nomi vengono aperti automaticamente:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Riferimento ad assembly esterni
In maniera analoga, i riferimenti per l'assembly del framework possono essere aggiunti con la direttiva `#r "AssemblyName"` .

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Gli assembly seguenti vengono aggiunti automaticamente dall'ambiente di hosting di Funzioni di Azure:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Gli assembly seguenti sono anche casi speciali ai quali è possibile fare riferimento tramite simplename, ad esempio `#r "AssemblyName"`:

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Per fare riferimento a un assembly privato è possibile caricare il file dell'assembly in una cartella `bin` relativa alla funzione e farvi riferimento usando il nome file, ad esempio `#r "MyAssembly.dll"`. Per informazioni su come caricare i file nella cartella della funzione vedere la sezione seguente sulla gestione dei pacchetti.

## <a name="editor-prelude"></a>Codice introduttivo dell'editor
Un editor che supporta i servizi di compilazione F# non è in grado di riconoscere gli spazi dei nomi e gli assembly inclusi automaticamente con Funzioni di Azure. Può quindi essere utile includere un codice introduttivo che consenta all'editor di trovare gli assembly usati e di aprire in modo esplicito gli spazi dei nomi. Ad esempio:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

Quando esegue il codice, Funzioni di Azure elabora l'origine con `COMPILED` definito, quindi il codice introduttivo dell'editor verrà ignorato.

<a name="package"></a>

## <a name="package-management"></a>Gestione dei pacchetti
Per usare i pacchetti NuGet in una funzione F#, aggiungere un file `project.json` nella cartella della funzione nel file system dell'app della funzione. Di seguito è riportato un esempio di file `project.json` che aggiunge un riferimento ai pacchetti NuGet a `Microsoft.ProjectOxford.Face` versione 1.1.0:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

È supportato solo .NET Framework 4.6, quindi verificare che nel file `project.json` sia specificato `net46` come qui illustrato.

Quando si carica un file `project.json` , il runtime ottiene i pacchetti e aggiunge automaticamente riferimenti agli assembly dei pacchetti. Non è necessario aggiungere direttive `#r "AssemblyName"` . È sufficiente aggiungere le istruzioni `open` necessarie al file `.fsx`.

Si consiglia di inserire gli assembly con riferimento automatico nel codice introduttivo dell'editor, per migliorare l'interazione dell'editor con i servizi di compilazione F#.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Come aggiungere un file `project.json` alla funzione di Azure
1. Assicurarsi prima di tutto che l'app di funzione sia in esecuzione aprendo la funzione nel portale di Azure. In questo modo è anche possibile accedere ai log in streaming in cui verrà visualizzato l'output di installazione dei pacchetti.
2. Per caricare un file `project.json` , usare uno dei metodi descritti in [Come aggiornare i file delle app per le funzioni](functions-reference.md#fileupdate). Se si usa la [distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md), è possibile aggiungere un file `project.json` al ramo di staging per eseguire qualche esperimento prima di aggiungerlo al ramo di sviluppo.
3. Dopo l'aggiunta del file `project.json` , l'output visualizzato nel log in streaming della funzione sarà simile all'esempio seguente:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variabili di ambiente
Per ottenere una variabile di ambiente o un valore di impostazione dell'app, usare `System.Environment.GetEnvironmentVariable`, ad esempio:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Riutilizzo del codice del file con estensione fsx
È possibile usare il codice di altri file `.fsx` tramite una direttiva `#load`. Ad esempio:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

I percorsi specificati per la direttiva `#load` sono relativi alla posizione del file `.fsx`.

* `#load "logger.fsx"` carica un file che si trova nella cartella della funzione.
* `#load "package\logger.fsx"` carica un file che si trova nella sottocartella `package` della cartella della funzione.
* `#load "..\shared\mylogger.fsx"` carica un file che si trova nella cartella `shared` allo stesso livello della cartella della funzione, ovvero direttamente in `wwwroot`.

La direttiva `#load` funziona solo con i file `.fsx` (script F# ), non con i file `.fs`.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [F# Guide](/dotnet/articles/fsharp/index) (Guida di F#)
* [Best Practices for Azure Functions](functions-best-practices.md) (Procedure consigliate per Funzioni di Azure)
* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Test di Funzioni di Azure](functions-test-a-function.md)
* [Scalabilità di Funzioni di Azure](functions-scale.md)

