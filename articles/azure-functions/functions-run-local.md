---
title: Sviluppare ed eseguire localmente le funzioni di Azure | Documentazione Microsoft
description: Informazioni su come scrivere codice per le funzioni di Azure e testarle nel computer locale prima di eseguirle in Funzioni di Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 523ef25fe0d3227d526acbdee2c7cf2660fc4f25
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Scrivere codici per Funzioni di Azure e testarle in locale

Sebbene il [portale di Azure] offra un set di strumenti completo per lo sviluppo e il test delle funzioni di Azure, molti sviluppatori preferiscono un'esperienza di sviluppo locale. Funzioni di Azure semplifica l'uso dell'editor di codice e degli strumenti di sviluppo locale preferiti per sviluppare e testare le funzioni nel computer locale. Le funzioni possono attivare gli eventi in Azure ed è possibile eseguire il debug delle funzioni JavaScript e C# nel computer locale. 

Se si sviluppatori di Visual Studio C#, Funzioni di Azure [si integra anche con Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> Non combinare lo sviluppo locale con lo sviluppo del portale nella stessa app per le funzioni. Quando si creano e si pubblicano le funzioni da un progetto locale, non tentare di gestire o modificare il codice di progetto nel portale.

## <a name="install-the-azure-functions-core-tools"></a>Installare gli strumenti di base per Funzioni di Azure

[Strumenti di base di Funzioni di Azure] è una versione locale del runtime di Funzioni di Azure che è possibile eseguire nel computer di sviluppo locale. Non è un emulatore o simulatore. Si tratta dello stesso runtime presente in Funzioni di Azure. Sono disponibili due versioni degli strumenti di base di Funzioni di Azure:

+ [Versione 1.x](#v1): supporta la versione 1.x del runtime. Questa versione è supportata solo nei computer Windows e viene installata da un [pacchetto npm](https://docs.npmjs.com/getting-started/what-is-npm).
+ [Versione 2.x](#v2): supporta la versione 2.x del runtime. Questa versione supporta [Windows](#windows-npm), [macOS](#brew) e [Linux](#linux). Usa gestori di pacchetti specifici della piattaforma o npm per l'installazione. 

### <a name="v1"></a>Versione 1.x

La versione originale degli strumenti usa il runtime 1.x di Funzioni. Questa versione usa .NET Framework (4.7.1) ed è supportata solo nei computer Windows. Prima di installare la versione 1.x degli strumenti, è necessario [installare NodeJS](https://docs.npmjs.com/getting-started/installing-node) che include npm.

Usare il comando seguente per installare gli strumenti in versione 1.x:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Versione 2.x

>[!NOTE]
> Il runtime 2.0 di Funzioni di Azure è disponibile in anteprima e attualmente non tutte le funzionalità di Funzioni di Azure sono supportate. Per altre informazioni, vedere [Panoramica delle versioni del runtime per Funzioni di Azure](functions-versions.md). 

La versione 2.x degli strumenti usa il runtime di Funzioni di Azure 2.x basata su .NET Core. Questa versione è supportata su tutte le piattaforme supportate da .NET Core 2.x, incluse [Windows](#windows-npm), [macOS](#brew) e [Linux](#linux).

#### <a name="windows-npm"></a>Windows

I passaggi seguenti usano npm per installare gli strumenti di base in Windows. È anche possibile usare [Chocolatey](https://chocolatey.org/). Per altre informazioni, vedere il [file leggimi degli strumenti di base](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installare [.NET Core 2.0 per Windows](https://www.microsoft.com/net/download/windows).

2. Installare [Node.js], che include npm. Per la versione 2.x degli strumenti, sono supportate solo le versioni Node.js 8.5 e successive.

3. Installare il pacchetto degli strumenti di base:

  ```bash
  npm install -g azure-functions-core-tools@core
  ```

#### <a name="brew"></a>MacOS con Homebrew

I passaggi seguenti usano Homebrew per installare gli strumenti di base su macOS.

1. Installare [.NET Core 2.0 per macOS](https://www.microsoft.com/net/download/macos).

1. Installare [Homebrew](https://brew.sh/), se non è già installato.

2. Installare il pacchetto degli strumenti di base:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) con APT

La procedura seguente usa [APT](https://wiki.debian.org/Apt) per installare gli strumenti di base nella distribuzione Ubuntu/Debian Linux. Per altre distribuzioni Linux, vedere il [file leggimi degli strumenti di base](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installare [.NET Core 2.0 per Linux](https://www.microsoft.com/net/download/linux)

1. Registrare il codice Product Key di Microsoft come attendibile:

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
  ```

2.  Configurare il feed del pacchetto, sostituendo `<version>` nel comando seguente con il nome della versione appropriata dalla tabella:

  ```bash
  sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-<version>-prod <version> main" > /etc/apt/sources.list.d/dotnetdev.list'
  sudo apt-get update
  ```

  | Distribuzione Linux | `<version>` |
  | --------------- | ----------- |
  | Ubuntu 17.10    | `artful`    |
  | Ubuntu 17.04    | `zesty`     |
  | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

3. Installare il pacchetto degli strumenti di base:

  ```bash
  sudo apt-get install azure-functions-core-tools
  ```

## <a name="run-azure-functions-core-tools"></a>Eseguire Strumenti di base di Funzioni di Azure
 
Strumenti di base di Funzioni di Azure aggiunge gli alias di comando seguenti:
* **func**
* **azfun**
* **azurefunctions**

Uno di questi alias può essere utilizzato dove `func` è visualizzato negli esempi.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Creare un progetto Funzioni locale

Quando è eseguito in locale, un progetto Funzioni è una directory che presenta i file [host.json](functions-host-json.md) e [local.settings.json](#local-settings-file). Questa directory è l'equivalente di un'app per le funzioni in Azure. Per altre informazioni sulla struttura delle cartelle di Funzioni di Azure, vedere la [Guida per sviluppatori di Funzioni di Azure](functions-reference.md#folder-structure).

Nella finestra del terminale o da un prompt dei comandi, eseguire il comando seguente per creare il progetto e l’archivio Git locale:

```
func init MyFunctionProj
```

L'output ha un aspetto simile all'esempio seguente:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Per creare il progetto senza un archivio Git locale, utilizzare l’opzione `--no-source-control [-n]`.

## <a name="register-extensions"></a>Registrare le estensioni

Nella versione 2.x del runtime di Funzioni di Azure, è necessario registrare esplicitamente le [estensioni delle associazioni](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) usate nell'app per le funzioni. 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Per altre informazioni, vedere [Concetti relativi a trigger e associazioni in Funzioni di Azure](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>File di impostazioni locali

Il file local.settings.json archivia le impostazioni di app, le stringhe di connessione e le impostazioni per Strumenti di base di Funzioni di Azure. Presenta la struttura seguente:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Impostazione      | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Se impostato su **true**, tutti i valori sono crittografati tramite una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è **false**. |
| **Valori** | Raccolta di impostazioni dell'applicazione usate durante l'esecuzione in locale. **AzureWebJobsStorage** e **AzureWebJobsDashboard** sono solo esempi; per un elenco completo vedere il [riferimento sulle impostazioni dell’app](functions-app-settings.md). Molti trigger e associazioni includono una proprietà che fa riferimento a un'impostazione di app, ad esempio **Connection** per il trigger di archiviazione BLOB. Per tali proprietà, è necessaria un'impostazione dell'applicazione definita nella matrice **Values**. Questo vale anche per qualsiasi proprietà di associazione impostata su un nome di impostazione di app racchiudendo il valore tra simboli di percentuale, ad esempio `%AppSettingName%`. |
| **Host** | Le impostazioni in questa sezione consentono di personalizzare il processo host di Funzioni durante l'esecuzione in locale. | 
| **LocalHttpPort** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questo valore. |
| **CORS** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **ConnectionStrings** | Contiene le stringhe di connessione del database per le funzioni. Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di **System.Data.SqlClient**.  | 

Queste impostazioni possono anche essere lette nel codice come variabili di ambiente. Per altre informazioni, vedere la sezione Variabili di ambiente negli argomenti di riferimento specifici del linguaggio seguenti:

+ [C# precompilato](functions-dotnet-class-library.md#environment-variables)
+ [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Le impostazioni nel file local.settings.json vengono usate solo per gli strumenti delle funzioni durante l'esecuzione in locale. Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure. Utilizzare lo switch `--publish-local-settings` [durante la pubblicazione](#publish) per assicurarsi che queste impostazioni vengano aggiunte all'app della funzione in Azure.

Quando non è impostata alcuna stringa di connessione di archiviazione valida per **AzureWebJobsStorage**, viene visualizzato il messaggio di errore seguente:  

>Valore mancante per AzureWebJobsStorage in local.settings.json. È necessario per tutti i trigger diversi da HTTP. È possibile eseguire "func azure functionapp fetch-app-settings <functionAppName>" o specificare una stringa di connessione in local.settings.json.
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configurare le impostazioni applicazione

Per impostare un valore per le stringhe di connessione, è possibile eseguire una delle opzioni seguenti:
* Immettere la stringa di connessione da [Azure Storage Explorer](http://storageexplorer.com/).
* Usare uno di questi comandi:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Per entrambi i comandi è necessario innanzitutto accedere ad Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Creare una funzione

Eseguire il comando seguente per creare una funzione:

```
func new
``` 
`func new` supporta gli argomenti opzionali seguenti:

| Argomento     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Il linguaggio di programmazione del modello, come C#, F# o JavaScript. |
| **`--template -t`** | Il nome del modello. |
| **`--name -n`** | Il nome della funzione. |

Ad esempio, per creare un trigger HTTP JavaScript, eseguire:

```
func new --language JavaScript --template "Http Trigger" --name MyHttpTrigger
```

Per creare una funzione attivata dalla coda, eseguire:

```
func new --language JavaScript --template "Queue Trigger" --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Eseguire funzioni localmente

Per eseguire un progetto Funzioni, eseguire l'host di Funzioni. L'host abilita i trigger per tutte le funzioni del progetto:

```
func host start
```

`func host start` supporta le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | La porta locale su cui ascoltare. Valore predefinito: 7071. |
| **`--debug <type>`** | Le opzioni sono `VSCode` e `VS`. |
| **`--cors`** | Un elenco delimitato dalla virgola di origini CORS, senza spazi. |
| **`--nodeDebugPort -n`** | La porta per il debugger di nodo da usare. Predefinito: un valore di launch.json o 5858. |
| **`--debugLevel -d`** | Il livello di traccia della console (off, verbose, info, warning o error). Predefinito: Info.|
| **`--timeout -t`** | Il timeout per l'host di Funzioni da avviare, in secondi. Impostazione predefinita: 20 secondi.|
| **`--useHttps`** | Eseguire l'associazione a https://localhost:{port} anziché a http://localhost:{port}. Per impostazione predefinita, questa opzione crea un certificato attendibile nel computer in uso.|
| **`--pause-on-error`** | Sospendere per l'input aggiuntivo prima dell'uscita dal processo. Utile quando si avvia Strumenti di base di Funzioni di Azure da un ambiente di sviluppo integrato (IDE).|

Quando viene avviato l'host di Funzioni, restituisce come output l'URL delle funzioni attivate da HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Eseguire il debug in Visual Studio Code o Visual Studio

Per associare un debugger, passare l'argomento `--debug`. Per eseguire il debug di funzioni JavaScript, usare Visual Studio Code. Per le funzioni C#, usare Visual Studio.

Per eseguire il debug di funzioni C#, usare `--debug vs`. È anche possibile usare [Strumenti di Visual Studio 2017 per Funzioni di Azure](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Per avviare l'host e configurare il debug di JavaScript, eseguire:

```
func host start --debug vscode
```

> [!IMPORTANT]
> Per finalità di debug, è supportato solo Node.js 8.x. Node.js 9.x non è supportato. 

Quindi, in Visual Studio Code, nella visualizzazione **Debug**, selezionare **Attach to Azure Functions** (Associa a Funzioni di Azure). È possibile associare punti di interruzione, controllare variabili ed eseguire il codice passo per passo.

![Debug di JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Passaggio di dati di test a una funzione

Per testare le funzioni localmente, [avviare l'host di Funzioni](#start) e chiamare gli endpoint nel server locale usando richieste HTTP. L'endpoint chiamato dipende dal tipo di funzione. 

>[!NOTE]  
> Gli esempi in questo argomento usano lo strumento cURL per inviare richieste HTTP dal terminale o da un prompt dei comandi. È possibile usare lo strumento preferito per inviare richieste HTTP al server locale. Lo strumento cURL è disponibile per impostazione predefinita nei sistemi basati su Linux. In Windows è necessario prima scaricare e installare lo [strumento cURL](https://curl.haxx.se/).

Per informazioni più generali sui test delle funzioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funzioni attivate tramite HTTP e webhook

È possibile chiamare l'endpoint seguente per eseguire localmente funzioni attivate tramite HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Assicurarsi di usare lo stesso nome server e la stessa porta su cui è in ascolto l'host di Funzioni. Questi valori sono visualizzati nell'output generato all'avvio dell'host di Funzioni. È possibile chiamare questo URL usando qualsiasi metodo HTTP supportato dal trigger. 

Il comando cURL seguente attiva la funzione di avvio rapido `MyHttpTrigger` da una richiesta GET con il parametro _name_ passato nella stringa di query. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
L'esempio seguente è la stessa funzione chiamata da una richiesta POST passando _name_ nel corpo della richiesta:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

È possibile effettuare richieste GET da un browser passando dati nella stringa di query. Per tutti gli altri metodi HTTP è necessario usare cURL, Fiddler, Postman o uno strumento analogo per i test HTTP.  

#### <a name="non-http-triggered-functions"></a>Funzione attivate non da HTTP
Per tutti i tipi di funzioni diverse dai trigger HTTP e dai webhook, è possibile testare localmente le funzioni chiamando un endpoint di amministrazione. Chiamando questo endpoint con una richiesta HTTP POST sul server locale si attiva la funzione. È facoltativamente possibile passare dati di test all'esecuzione nel corpo del messaggio della richiesta POST. Questa funzionalità è analoga alla scheda **Test** del portale di Azure.  

Chiamare l'endpoint di amministrazione seguente per attivare funzioni non HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Per passare dati di test all'endpoint di amministrazione di una funzione, è necessario fornire i dati nel corpo di un messaggio di richiesta POST. Il corpo del messaggio deve avere il formato JSON seguente:

```JSON
{
    "input": "<trigger_input>"
}
```` 
Il valore `<trigger_input>` contiene dati nel formato previsto dalla funzione. L'esempio cURL seguente è una richiesta POST per una funzione `QueueTriggerJS`. In questo caso l'input è una stringa che equivale al messaggio previsto nella coda.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Uso del comando `func run` nella versione 1.x

>[!IMPORTANT]  
> Il comando `func run` non è supportato nella versione 2.x degli strumenti. Per altre informazioni, vedere l'argomento [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

È anche possibile richiamare una funzione direttamente tramite `func run <FunctionName>` e offrire dati di input per la funzione. Questo comando è simile all'esecuzione di una funzione con la scheda **Test** nel portale di Azure. 

`func run` supporta le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenuto inline. |
| **`--debug -d`** | Associare un debugger al processo host prima di eseguire la funzione.|
| **`--timeout -t`** | Tempo di attesa (in secondi) fino a quando l'host locale di Funzioni è pronto.|
| **`--file -f`** | Il nome del file da usare come contenuto.|
| **`--no-interactive`** | Non richiede input. Utile per scenari di automazione.|

Ad esempio, per chiamare una funzione attivata da HTTP e passare il corpo del contenuto, eseguire il comando seguente:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Visualizzazione dei file di log in locale

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a> Pubblicazione in Azure

Per pubblicare un progetto Funzioni in un'app per le funzioni in Azure, usare il comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

È possibile usare le opzioni seguenti:

| Opzione     | DESCRIZIONE                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Pubblicare le impostazioni di local.settings.json in Azure, suggerendo di sovrascrivere eventuali impostazioni esistenti.|
| **`--overwrite-settings -y`** | Deve essere usato con `-i`. Sovrascrive AppSettings in Azure con il valore locale se diverso. Viene suggerito il valore predefinito.|

Questo comando consente di pubblicare un'app per le funzioni esistente in Azure. Si verifica un errore se `<FunctionAppName>` non esiste nella propria sottoscrizione. Per informazioni su come creare un'app per le funzioni dal prompt dei comandi o dalla finestra del terminale usando l'interfaccia della riga di comando di Azure, vedere [Creare un'app per le funzioni per l'esecuzione senza server](./scripts/functions-cli-create-serverless.md).

Il comando `publish` carica il contenuto della directory del progetto Funzioni. Se si eliminano i file in locale, il comando `publish` non li elimina da Azure. È possibile eliminare i file in Azure usando lo [strumento Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) nel [portale di Azure].  

>[!IMPORTANT]  
> Quando si crea un'app per le funzioni in Azure, questa utilizza la versione 1.x del runtime di Funzioni per impostazione predefinita. Per fare in modo che l’app per le funzioni utilizzi la versione 2.x del runtime, aggiungere l'impostazione dell'applicazione `FUNCTIONS_EXTENSION_VERSION=beta`.  
Utilizzare il seguente codice dell’interfaccia della riga di comando di Azure per aggiungere questa impostazione all'app per le funzioni: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Passaggi successivi

Strumenti di base di Funzioni di Azure è [open source ed è ospitato su GitHub](https://github.com/azure/azure-functions-cli).  
Per registrare una richiesta per un bug o una funzionalità [aprire un problema di GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Strumenti di base di Funzioni di Azure]: https://www.npmjs.com/package/azure-functions-core-tools
[portale di Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
