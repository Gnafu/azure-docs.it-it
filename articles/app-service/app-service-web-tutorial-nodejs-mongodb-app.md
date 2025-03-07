---
title: Node.js (MEAN.js) con MongoDB - Servizio app di Azure | Microsoft Docs
description: Informazioni su come usare un'app Node.js in Azure, con connessione a un database Cosmos DB tramite una stringa di connessione MongoDB. MEAN.js verrà usato nell'esercitazione.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 361e921af65b33ac0a7a8d12e28db1cb305b0fa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138858"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Esercitazione: Creare un'app Node.js e MongoDB in Azure

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Windows. Per la distribuzione nel servizio app in _Linux_, vedere [Creare un'app Node.js e MongoDB nel servizio app di Azure in Linux](./containers/tutorial-nodejs-mongodb-app.md).
>

Il Servizio app di Azure offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. Questa esercitazione illustra come creare un'app Node.js nel servizio app e connetterla a un database MongoDB. Al termine, si avrà un'applicazione MEAN (MongoDB, Express, AngularJS e Node.js) in esecuzione nel [servizio app di Azure](overview.md). Per semplicità, l'applicazione di esempio usa il [framework Web MEAN.js](https://meanjs.org/).

![App MEAN.js in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un database MongoDB in Azure
> * Connettere un'app Node.js a MongoDB
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
2. [Installare Node.js e NPM](https://nodejs.org/)
3. [Installare Bower](https://bower.io/), richiesto da [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started)
4. [Installare Gulp.js](https://gulpjs.com/), richiesto da [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started)
5. [Installare ed eseguire MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>Testare il MongoDB locale

Aprire la finestra del terminale e usare il comando `cd` per passare alla directory `bin` dell'installazione di MongoDB. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

Eseguire `mongo` nel terminale per connettersi al server MongoDB locale.

```bash
mongo
```

Se la connessione ha esito positivo significa che il database MongoDB è già in esecuzione. In caso contrario, assicurarsi che il database MongoDB locale venga avviato seguendo la procedura descritta in [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Installare MongoDB Community Edition). Spesso, anche se MongoDB è stato installato, è necessario avviare il servizio eseguendo `mongod`. 

Dopo avere testato il database MongoDB, digitare `Ctrl+C` nel terminale. 

## <a name="create-local-nodejs-app"></a>Creare l'app Node.js locale

In questo passaggio si imposta il progetto Node.js locale.

### <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.  

Eseguire il comando seguente per clonare l'archivio di esempio. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Questo repository contiene una copia del [repository MEAN.js](https://github.com/meanjs/mean), che è stato modificato per poter essere eseguito nel servizio app. Per altre informazioni, vedere il [file LEGGIMI](https://github.com/Azure-Samples/meanjs/blob/master/README.md) del repository MEAN.js.

### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire i comandi seguenti per installare i pacchetti necessari e avviare l'applicazione.

```bash
cd meanjs
npm install
npm start
```

Al termine del caricamento dell'app viene visualizzato un messaggio simile al seguente:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Andare a `http://localhost:3000` in un browser. Fare clic su **Iscriviti** nel menu in alto e creare un utente test. 

L'applicazione di esempio MEAN.js archivia i dati utente nel database. Dopo aver creato un utente e aver eseguito l'accesso, l'app scriverà i dati nel database MongoDB locale.

![MEAN.js si connette correttamente a MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Selezionare **Admin > Manage Articles** (Amministrazione > Gestione articoli) per aggiungere articoli.

Per arrestare Node.js in qualsiasi momento, premere `Ctrl+C` nel terminale. 

> [!NOTE]
> La [Guida introduttiva di Node.js](app-service-web-get-started-nodejs.md) indica la necessità di un file web.config nella directory radice dell'app. Tuttavia, in questa esercitazione il file web.config verrà generato automaticamente dal servizio app quando si distribuiscono i file usando la [distribuzione con Git locale](deploy-local-git.md) anziché la distribuzione con file ZIP. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Creare MongoDB di produzione

In questo passaggio si crea un database MongoDB in Azure. Quando viene distribuita in Azure, l'app usa questo database basato sul cloud.

Per MongoDB, questa esercitazione usa [Azure Cosmos DB](/azure/documentdb/). COSMOS DB supporta le connessioni client MongoDB.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Creare un account Cosmos DB

> [!NOTE]
> La creazione dei database Azure Cosmos DB in questa esercitazione nell'ambito della sottoscrizione di Azure è a pagamento. Per usare un account Azure Cosmos DB gratuito per sette giorni, è possibile provare l'esperienza disponibile nella pagina [Prova gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Fare clic sul pulsante **Crea** nel riquadro MongoDB per creare un database MongoDB gratuito in Azure. Dopo aver creato il database, passare a **Stringa di connessione** nel portale e recuperare la stringa di connessione di Azure Cosmos DB da usare più avanti in questa esercitazione.
>

In Cloud Shell creare un account Cosmos DB con il comando [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create).

Nel comando seguente sostituire il segnaposto *\<cosmosdb_name>* con un nome Cosmos DB univoco. Poiché questo nome è incluso nell'endpoint Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, è necessario che sia univoco in tutti gli account Cosmos DB in Azure. Il nome deve contenere solo lettere minuscole, numeri e il carattere (-) e deve avere una lunghezza compresa tra 3 e 50 caratteri.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

Il parametro *--kind MongoDB* abilita le connessioni client MongoDB.

Dopo la creazione dell'account Cosmos DB, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Connettere l'app a MongoDB di produzione

In questo passaggio si usa una stringa di connessione MongoDB per connettere l'applicazione di esempio MEAN.js al database Cosmos DB appena creato. 

### <a name="retrieve-the-database-key"></a>Recuperare la chiave del database

Per connettersi al database Cosmos DB, è necessario disporre della chiave database. In Cloud Shell usare il comando [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) per recuperare la chiave primaria.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

L'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copiare il valore di `primaryMasterKey`. Queste informazioni saranno necessarie durante il passaggio successivo.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurare la stringa di connessione nell'applicazione Node.js

Nella cartella _config/env/_ del repository MEAN.js locale creare un file denominato _local-production.js_. Per impostazione predefinita, _.gitignore_ è configurato per escludere questo file dal repository. 

Copiare nel file il codice seguente. Assicurarsi di sostituire i due segnaposto *\<cosmosdb_name>* con il nome del database Cosmos DB e di sostituire il segnaposto *\<primary_master_key>* con la chiave copiata nel passaggio precedente.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

L'opzione `ssl=true` è obbligatoria poiché [Cosmos DB richiede l'SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Salvare le modifiche.

### <a name="test-the-application-in-production-mode"></a>Testare l'applicazione nella modalità di produzione 

Eseguire il comando seguente per minimizzare e aggregare gli script per l'ambiente di produzione. Questo processo genera i file necessari per l'ambiente di produzione.

```bash
gulp prod
```

Eseguire il comando seguente per usare la stringa di connessione configurata in _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` imposta la variabile di ambiente che richiede a Node.js l'esecuzione nell'ambiente di produzione.  `node server.js` avvia il server Node.js con `server.js` nella radice del repository. Questo è il modo in cui l'applicazione Node.js viene caricata in Azure. 

Dopo aver caricato l'app, verificare che sia in esecuzione nell'ambiente di produzione:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Andare a `http://localhost:8443` in un browser. Fare clic su **Iscriviti** nel menu in alto e creare un utente test. Dopo aver creato un utente e aver eseguito l'accesso, l'app scriverà i dati nel database Cosmos DB in Azure. 

Nel terminale arrestare Node.js digitando `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

In questo passaggio si distribuisce l'applicazione Node.js connessa a MongoDB nel Servizio app di Azure.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Creare un'app Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configurare una variabile di ambiente

Per impostazione predefinita, il progetto MEAN.js mantiene _config/env/local-production.js_ fuori dal repository Git. Di conseguenza, per l'app di Azure si usano le impostazioni dell'app per definire la stringa di connessione di MongoDB.

Per configurare le impostazioni dell'app, usare il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. 

L'esempio seguente configura un'impostazione dell'app `MONGODB_URI` nell'app Azure. Sostituire i segnaposto *\<app_name>* , *\<cosmosdb_name>* e *\<primary_master_key>* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

Nel codice Node.js si accede all'impostazione dell'app con `process.env.MONGODB_URI`, esattamente come si accede a una variabile di ambiente qualsiasi. 

Nel repository MEAN.js locale aprire _config/env/production.js_ (non _config/env/local-production.js_), che ha una configurazione specifica dell'ambiente di produzione. L'app MEAN.js predefinita è già configurata per l'uso della variabile di ambiente `MONGODB_URI` creata.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

Il processo di distribuzione esegue [Gulp](https://gulpjs.com/) dopo `npm install`. Il servizio app non esegue attività Gulp o Grunt durante la distribuzione, pertanto questo repository di esempio include due file aggiuntivi nella directory radice per abilitarlo: 

- _.deployment_: questo file indica al servizio app di eseguire `bash deploy.sh` come script di distribuzione personalizzato.
- _deploy.sh_: script di distribuzione personalizzato. Se si esamina il file, si noterà che esegue `gulp prod` dopo `npm install` e `bower install`. 

È possibile usare questo approccio per aggiungere uno o più passaggi alla distribuzione basata su Git. Se si riavvia l'app di Azure in qualsiasi momento, il servizio app non esegue di nuovo queste attività di automazione.

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure 

Passare all'app distribuita usando il Web browser. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Fare clic su **Registrati** nel menu in alto e creare un utente fittizio. 

Se l'operazione ha esito positivo e l'app accede automaticamente all'utente creato, l'app MEAN.js in Azure sarà connessa al database MongoDB, ovvero Cosmos DB. 

![App MEAN.js in esecuzione nel Servizio app di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Selezionare **Admin > Manage Articles** (Amministrazione > Gestione articoli) per aggiungere articoli. 

**Congratulazioni** L'app Node.js basata su dati è in esecuzione nel Servizio app di Azure.

## <a name="update-data-model-and-redeploy"></a>Aggiornare il modello di dati e ridistribuire

In questo passaggio viene modificato il modello di dati `article` e viene pubblicata la modifica in Azure.

### <a name="update-the-data-model"></a>Aggiornare il modello di dati

Aprire _modules/articles/server/models/article.server.model.js_.

In `ArticleSchema` aggiungere un tipo `String` denominato `comment`. Al termine, il codice dello schema dovrebbe avere un aspetto simile al seguente:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Aggiornare il codice di articoli

Aggiornare la parte rimanente del codice `articles` per usare `comment`.

È necessario modificare cinque file: il controller server e le quattro visualizzazioni client. 

Aprire _modules/articles/server/controllers/articles.server.controller.js_.

Nella funzione `update` aggiungere un'assegnazione per `article.comment`. Il codice seguente mostra la funzione `update` completata:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Aprire _modules/articles/client/views/view-article.client.view.html_.

Appena sopra il tag di chiusura `</section>` aggiungere la riga seguente per visualizzare `comment` insieme al resto dei dati dell'articolo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Aprire _modules/articles/client/views/list-articles.client.view.html_.

Appena sopra il tag di chiusura `</a>` aggiungere la riga seguente per visualizzare `comment` insieme al resto dei dati dell'articolo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Aprire _modules/articles/client/views/admin/list-articles.client.view.html_.

Nell'elemento `<div class="list-group">` sopra il tag di chiusura `</a>` aggiungere la riga seguente per visualizzare `comment` con la parte rimanente dei dati dell'articolo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Aprire _modules/articles/client/views/admin/form-article.client.view.html_.

Trovare l'elemento `<div class="form-group">` che contiene un pulsante di invio simile al seguente:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Sopra il tag aggiungere un altro elemento `<div class="form-group">` che consente agli utenti di modificare il campo `comment`. Il nuovo elemento dovrebbe avere un aspetto simile al seguente:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Testare le modifiche in locale

Salvare tutte le modifiche.

Nella finestra del terminale locale testare di nuovo le modifiche in modalità di produzione.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Andare a `http://localhost:8443` in un browser e assicurarsi di avere eseguito l'accesso.

Selezionare **Admin > Manage Articles** (Admin > Gestione articoli), quindi aggiungere un articolo selezionando il pulsante **+** .

La nuova casella di testo `Comment` è ora visibile.

![Campo di commento aggiunto ad Articles (Articoli)](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

Nel terminale arrestare Node.js digitando `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nella finestra del terminale locale eseguire il commit delle modifiche in Git e quindi eseguire il push delle modifiche al codice in Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Dopo aver completato `git push`, passare all'app Azure e provare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Eventuali articoli aggiunti in precedenza sono ancora visibili. I dati esistenti in Cosmos DB non vengono persi. Gli aggiornamenti allo schema di dati inoltre lasciano intatti i dati esistenti.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica 

Mentre l'applicazione Node.js è in esecuzione nel servizio app di Azure, è possibile fare in modo che i log di console siano inviati tramite pipe al terminale. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Per avviare lo streaming dei log, usare il comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) in Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Dopo avere avviato lo streaming del log, aggiornare l'app Azure nel browser per ottenere il traffico Web. I log di console vengono inviati tramite pipe al terminale.

Arrestare il flusso dei log in qualsiasi momento digitando `Ctrl+C`. 

## <a name="manage-your-azure-app"></a>Gestire l'app Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un database MongoDB in Azure
> * Connettere un'app Node.js a MongoDB
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log da Azure al terminale
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"] 
> [Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure](app-service-web-tutorial-custom-domain.md)
