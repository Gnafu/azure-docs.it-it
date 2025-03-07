---
title: 'Esercitazione: Eseguire la distribuzione da GitHub nel servizio Azure Kubernetes (AKS) con Jenkins'
description: Configurare Jenkins per l'integrazione continua (CI) da GitHub e la distribuzione continua (CD) nel servizio Azure Kubernetes (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 7a81f26b4dad5f7257e5c3fd012dffaf06d573bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073790"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Esercitazione: Eseguire la distribuzione da GitHub nel servizio Azure Kubernetes (AKS) con integrazione continua e distribuzione continua di Jenkins

Questa esercitazione distribuisce un'app di esempio da GitHub in un cluster del [servizio Azure Kubernetes (AKS)](/azure/aks/intro-kubernetes) configurando l'integrazione continua (CI) e la distribuzione continua (CD) in Jenkins. In questo modo, quando si aggiorna l'app eseguendo il push dei commit in GitHub, Jenkins esegue automaticamente una nuova compilazione dei contenitori, esegue il push delle immagini dei contenitori in Registro Azure Container e quindi esegue l'app in servizio Azure Kubernetes. 

In questa esercitazione si completeranno le attività seguenti:

> [!div class="checklist"]
> * Distribuire un'app Azure Vote di esempio in un cluster servizio Azure Kubernetes.
> * Creare un progetto di Jenkins di base.
> * Configurare le credenziali per Jenkins per l'interazione con il Registro Azure Container.
> * Creare un processo di compilazione Jenkins e un webhook GitHub per compilazioni automatizzate.
> * Testare la pipeline CI/CD per aggiornare un'applicazione in servizio Azure Kubernetes in base ai commit del codice di GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario quanto segue:

- Conoscenze di base di Kubernetes, Git, CI/CD e delle immagini di contenitore

- Disponibilità di un [cluster servizio Azure Kubernetes][aks-quickstart] e di `kubectl` configurato con le [credenziali del cluster servizio Azure Kubernetes][aks-credentials]

- Disponibilità di un [Registro Azure Container][acr-quickstart], del nome del server di accesso del Registro Azure Container e del cluster servizio Azure Kubernetes configurato per [eseguire l'autenticazione con il Registro Azure Container][acr-authentication]

- Interfaccia della riga di comando di Azure versione 2.0.46 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

- [Docker installato][docker-install] nel sistema di distribuzione in uso

- Account GitHub, [token di accesso personale GitHub][git-access-token] e client Git installato nel sistema di distribuzione in uso

- Se si fornisce la propria istanza di Jenkins piuttosto che questo esempio con script per distribuire Jenkins, l'istanza di Jenkins richiede [l'installazione e la configurazione di Docker][docker-install] e [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Preparare l'app

In questo articolo si usa un'applicazione di voto di Azure di esempio che contiene un'interfaccia Web ospitata in uno o più pod e un secondo pod che ospita Redis per l'archiviazione temporanea dei dati. Prima di integrare Jenkins e il servizio Azure Kubernetes per le distribuzioni automatizzate, preparare e distribuire l'applicazione di voto di Azure nel cluster servizio Azure Kubernetes. Questa distribuzione manuale può essere considerata come la prima versione dell'applicazione e consente di visualizzare l'applicazione in azione.

> [!NOTE]
> L'applicazione Azure vote di esempio Usa un pod Linux che viene pianificato l'esecuzione in un nodo Linux. Il flusso descritto in questo articolo funziona anche per un pod di Windows Server pianificato su un nodo Windows Server.

Creare il fork del repository GitHub seguente per l'applicazione esempio - [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Per creare il fork del repository nel proprio account GitHub, selezionare il pulsante **Fork** nell'angolo superiore destro.

Clonare il fork nel proprio sistema di sviluppo. Verificare di usare l'URL del fork durante la clonazione del repository:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Passare alla directory del fork clonato:

```console
cd azure-voting-app-redis
```

Per creare le immagini di contenitore necessarie per l'applicazione di esempio, usare il file *docker-compose.yaml* con `docker-compose`:

```console
docker-compose up -d
```

Viene eseguito il pull delle immagini di base necessarie e vengono compilati i contenitori dell'applicazione. È quindi possibile usare il comando [docker images][docker-images] per visualizzare l'immagine creata. Sono state scaricate o create tre immagini. L'immagine `azure-vote-front` contiene l'applicazione e usa l'immagine `nginx-flask` come base. L'immagine `redis` viene usata per avviare un'istanza di Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Prima di eseguire il push del contenitore *azure-vote-front* in Registro Azure Container, ottenere il server di accesso del servizio contenitori di Azure con il comando [az acr list][az-acr-list]. L'esempio seguente ottiene l'indirizzo del server di accesso di Registro Azure Container in un gruppo di risorse denominato *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare il comando [docker tag][docker-tag] per contrassegnare l'immagine con il nome del server di accesso di Registro Azure Container e con un numero di versione di `v1`. Specificare il nome `<acrLoginServer>` ottenuto nel passaggio precedente:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Eseguire infine il push dell'immagine *azure-vote-front* in Registro Azure Container. Anche in questo caso, sostituire `<acrLoginServer>` con il nome del server di accesso di Registro Azure Container, ad esempio `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Distribuire l'applicazione di esempio nel servizio Azure Kubernetes

Per distribuire l'applicazione di esempio nel cluster servizio Azure Kubernetes, è possibile usare il file manifesto Kubernetes nella radice del repository di voto di Azure. Aprire il file manifesto *azure-vote-all-in-one-redis.yaml* con un editor, ad esempio `vi`. Sostituire `microsoft` con il nome del server di accesso di Registro Azure Container. Tale valore è presente nella riga **47** del file manifesto:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Successivamente, usare il comando [kubectl apply][kubectl-apply] per distribuire l'applicazione nel cluster servizio Azure Kubernetes:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Viene creato un servizio di bilanciamento del carico di Kubernetes per esporre l'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. Per monitorare l'avanzamento della distribuzione del bilanciamento del carico, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`. Dopo il passaggio di *EXTERNAL-IP* da *pending* a un *indirizzo IP*, usare `Control + C` per arrestare il processo kubectl watch.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Per vedere l'applicazione in azione, aprire un Web browser all'indirizzo IP esterno del servizio. Viene visualizzata l'applicazione di voto di Azure, come illustrato nell'esempio seguente:

![Applicazione di voto di Azure di esempio in esecuzione nel servizio Azure Kubernetes](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Distribuire Jenkins in una macchina virtuale di Azure

Per distribuire rapidamente Jenkins per l'uso in questo articolo, è possibile usare lo script seguente per distribuire una macchina virtuale di Azure, configurare l'accesso alla rete e completare un'installazione di base di Jenkins. Per eseguire l'autenticazione tra Jenkins e il cluster servizio Azure Kubernetes e Jenkins, lo script copia il file di configurazione di Kubernetes dal sistema di sviluppo al sistema Jenkins.

> [!WARNING]
> Questo script di esempio è a scopo dimostrativo per effettuare rapidamente il provisioning di un ambiente Jenkins eseguito su una macchina virtuale Azure. Sfrutta l'estensione script personalizzata di Azure per configurare una macchina virtuale e quindi visualizzare le credenziali necessarie. *~/.kube/config* viene copiato sulla macchina virtuale di Jenkins.

Eseguire i comandi seguenti per scaricare ed eseguire lo script. È necessario rivedere il contenuto di tutti gli script prima di eseguirli - [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Sono necessari alcuni minuti per creare la macchina virtuale e distribuire i componenti necessari per Docker e Jenkins. Quando lo script è stato completato, restituisce un indirizzo per il server Jenkins e una chiave per sbloccare il dashboard, come illustrato nell'output di esempio seguente:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Aprire un browser Web all'URL visualizzato e immettere la chiave di sblocco. Seguire le istruzioni visualizzate per completare la configurazione di Jenkins:

- Scegliere **Install suggested plugins** (Installa plug-in consigliati)
- Creare il primo utente amministratore. Immettere un nome utente, ad esempio *azureuser*, quindi specificare una password sicura. Digitare infine un nome completo e un indirizzo di posta elettronica.
- Selezionare **Save and Finish** (Salva e completa).
- Quando Jenkins è pronto, selezionare **Start using Jenkins** (Inizia a usare Jenkins).
    - Se il Web browser visualizza una pagina vuota quando si inizia a usare Jenkins, riavviare il servizio Jenkins. Per riavviare il servizio, eseguire SSH nell'indirizzo IP pubblico dell'istanza di Jenkins e digitare `sudo service jenkins restart`. Dopo il riavvio del servizio, aggiornare il Web browser.
- Accedere a Jenkins con il nome utente e la password create nel processo di installazione.

## <a name="create-a-jenkins-environment-variable"></a>Creare una variabile di ambiente di Jenkins

Per contenere il nome del server di accesso di Registro Azure Container, viene usata una variabile di ambiente di Jenkins. A tale variabile viene fatto riferimento durante il processo di compilazione di Jenkins. Per creare questa variabile di ambiente, completare i passaggi seguenti:

- Nella parte sinistra del portale di Jenkins selezionare **Manage Jenkins** > **Configure System** (Gestisci Jenkins > Configura sistema)
- In **Global Properties** (Proprietà globali) selezionare **Environment variables** (Variabili di ambiente). Aggiungere una variabile con il nome `ACR_LOGINSERVER` e il valore del server di accesso di Registro Azure Container in uso.

    ![Variabili di ambiente di Jenkins](media/aks-jenkins/env-variables.png)

- Al termine, fare clic su **Save** (Salva) nella parte inferiore della pagina di configurazione di Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Creare una credenziale di Jenkins per il Registro Azure Container

Per consentire a Jenkins di compilare e quindi di eseguire il push delle immagini del contenitore aggiornate in Registro Azure Container, è necessario specificare le credenziali per tale registro. Questa autenticazione può usare le entità servizio di Azure Active Directory. Nei prerequisiti è stata configurata l'entità servizio per il cluster servizio Azure Kubernetes in uso con le autorizzazione di *lettore* in Registro Azure Container. Tali autorizzazioni consentono al cluster di servizio Azure Kubernetes di eseguire il *pull* delle immagini da Registro Azure Container. Durante il processo CI/CD, Jenkins compila nuovi immagini del contenitore in base agli aggiornamenti delle applicazioni e deve quindi eseguire il *push* di tali immagini in Registro Azure Container. Per la separazione di ruoli e autorizzazioni, configurare un'entità servizio per Jenkins con le autorizzazioni di *collaboratore* in Registro Azure Container.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Creare un'entità servizio per Jenkins per usare il Registro Azure Container

Creare in primo luogo un'entità servizio usando il comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Annotare i valori di *appId* e *password* visualizzati nell'output. Tali valori vengono usati nella procedura seguente per configurare la risorsa credenziale in Jenkins.

Ottenere l'ID risorsa di Registro Azure Container usando il comando [az acr show][az-acr-show] e archiviarlo come una variabile. Specificare il nome del gruppo di risorse e il nome di Registro Azure Container:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

A questo punto creare un'assegnazione di ruolo per assegnare i diritti dell'entità servizio *collaboratore* al Registro Azure Container. Nell'esempio seguente specificare il proprio *appId* illustrato nell'output di un comando precedente per creare l'entità servizio:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Creare una risorsa credenziale in Jenkins per l'entità servizio del servizio Registro Azure Container

Con l'assegnazione di ruolo creata in Azure, archiviare le credenziali di Registro Azure Container in un oggetto credenziale di Jenkins. Durante il processo di compilazione di Jenkins si farà riferimento a queste credenziali.

Tornare nella parte sinistra del portale di Jenkins, fare clic su **Credentials** > **Jenkins** > **Global credentials (unrestricted)**  > **Add Credentials** (Credenziali > Jenkins > Credenziali globali senza restrizioni > Aggiungi credenziali)

Verificare che il tipo di credenziali sia **Username with password** (Nome utente con password) e immettere gli elementi seguenti:

- **Nome utente** - *appId* dell'entità servizio creata per l'autenticazione con il Registro Azure Container.
- **Password** - *password* dell'entità utente creata per l'autenticazione con il Registro Azure Container.
- **ID** - identificatore di credenziali, ad esempio *acr-credentials*

Al termine, il modulo delle credenziali è analogo all'esempio seguente:

![Creare un oggetto credenziale di Jenkins con le informazioni dell'entità servizio](media/aks-jenkins/acr-credentials.png)

Fare clic su **OK** e tornare al portale di Jenkins.

## <a name="create-a-jenkins-project"></a>Creare un progetto di Jenkins

Nella home page del portale di Jenkins selezionare **New item** (Nuovo elemento) nella parte sinistra:

1. Immettere *azure-vote* come nome del processo. Scegliere **Freestyle project** (Progetto Freestyle) e quindi selezionare **OK**
1. Sotto il **generali** sezione, selezionare **progetto GitHub** e immettere l'URL del repository con fork, ad esempio *https:\//github.com/\<your github account--\>/azure-voting-app-redis*
1. Sotto il **gestione del codice di origine** selezionare **Git**, immettere il repository con fork *GIT* URL, ad esempio *https:\//github.com/\<your github account--\>/azure-voting-app-redis.git*

1. Nella sezione **Build Triggers** (Trigger di compilazione) selezionare **GitHub hook trigger for GITScm polling** (Trigger di hook GitHub per polling GITScm).
1. In **Build Environment** (Ambiente di compilazione) selezionare **Use secret texts or files** (Usa testi o file segreti).
1. In **Bindings** (Associazioni) selezionare **Add** (Aggiungi) > **Username and password (separated)** (Nome utente e password separati).
   - Immettere `ACR_ID` per **Username Variable** (Variabile nome utente) e `ACR_PASSWORD` per **Password Variable** (Variabile password).

     ![Associazioni di Jenkins](media/aks-jenkins/bindings.png)

1. Aggiungere un'**istruzione di compilazione** di tipo **Execute shell** (Esegui shell) e usare il testo seguente. Questo script compila una nuova immagine del contenitore e la inserisce nel Registro Azure Container.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Aggiungere un'altra **istruzione di compilazione** di tipo **Execute shell** (Esegui shell) e usare il testo seguente. Questo script aggiorna la distribuzione dell'applicazione nel servizio Azure Kubernetes con la nuova immagine del contenitore da Registro Azure Container.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Al termine, fare clic su **Save** (Salva).

## <a name="test-the-jenkins-build"></a>Testare la build di Jenkins

Prima di automatizzare il processo in base ai commit di GitHub, testare manualmente la compilazione di Jenkins. Tale compilazione manuale verifica che il processo sia stato configurato correttamente, che il file di autenticazione Kubernetes corretto sia presente e che l'autenticazione con il Registro Azure Container funzioni.

Nel menu a sinistra del progetto selezionare **Build Now** (Compila).

![Build di test di Jenkins](media/aks-jenkins/test-build.png)

La prima compilazione richiede un minuto o due perché i livelli dell'immagine Docker vengono scaricati nel server Jenkins. Le compilazioni successive possono usare i livelli di immagine memorizzati nella cache per migliorare i tempi di compilazione.

Durante questo processo, il repository di GitHub viene clonato nel server di compilazione di Jenkins. Viene compilata una nuova immagine del contenitore, che viene inserita nel Registro Azure Container. Infine l'applicazione di voto di Azure in esecuzione nel cluster di servizio Azure Kubernetes viene aggiornata per usare la nuova immagine. Poiché non sono state apportate modifiche al codice dell'applicazione, quest'ultima non viene modificata se si visualizza l'app di esempio in un Web browser.

Dopo che il processo di compilazione è stato completato, fare clic su **build #1** (compilazione 1) nella cronologia delle compilazioni. Selezionare **Console output** (Output console) e visualizzare l'output del processo di compilazione. La riga finale deve indicare che la build ha avuto esito positivo.

## <a name="create-a-github-webhook"></a>Creare un webhook GitHub

Dopo aver completato la compilazione manuale, integrare GitHub nella compilazione Jenkins. Per eseguire il processo di compilazione di Jenkins ogni volta che viene eseguito un commit di codice in GitHub, è possibile usare un webhook. Per creare il webhook GitHub, completare questi passaggi:

1. Passare al repository GitHub con fork in un Web browser.
1. Selezionare **Impostazioni** e quindi **Webhook** sul lato sinistro.
1. Scegli **Aggiungi webhook**. Come valore per *Payload URL* (URL di payload) immettere `http://<publicIp:8080>/github-webhook/` dove `<publicIp>` è l'indirizzo IP del server di Jenkins. Assicurarsi di includere la barra finale (/). Lasciare le altre impostazioni predefinite per il tipo di contenuto e attivare gli eventi *push*.
1. Selezionare **Aggiungi webhook**.

    ![Creare un webhook GitHub per Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testare la pipeline CI/CD completata

È ora possibile testare l'intera pipeline di integrazione CI/CD. Quando si esegue il push di un commit di codice da GitHub, vengono eseguite queste operazioni:

1. Il webhook GitHub contatta Jenkins.
1. Jenkins avvia il processo di compilazione ed esegue il pull del commit di codice più recente da GitHub.
1. Viene avviata una compilazione di Docker usando il codice aggiornato e la nuova immagine di contenitore è contrassegnata con il numero di build più recente.
1. Viene eseguito il push di tale immagine in Registro Azure Container.
1. L'applicazione viene distribuita negli aggiornamenti del servizio Azure Kubernetes con l'immagine del contenitore più recente di Registro Azure Container.

Nel computer di sviluppo aprire l'applicazione clonata con un editor di codice. Nella directory */azure-vote/azure-vote* aprire il file denominato **config_file.cfg**. Aggiornare i valori di voto in questo file su un valore rilevante, come illustrato nell'esempio seguente:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Al termine dell'aggiornamento, salvare il file, eseguire il commit delle modifiche ed eseguirne il push nel fork del repository di GitHub. Il webhook GitHub attiva un nuovo processo di compilazione in Jenkins. Nel dashboard Web di Jenkins monitorare il processo di compilazione. Sono necessari pochi secondi per eseguire il pull del codice più recente, creare ed eseguire il push dell'immagine aggiornata e distribuire l'applicazione aggiornata nel servizio Azure Kubernetes.

Al termine della compilazione, aggiornare il Web browser dell'applicazione di voto di Azure di esempio. Le modifiche vengono visualizzate, come illustrato nell'esempio seguente:

![Voto di Azure di esempio nel servizio Azure Kubernetes aggiornato dal processo di compilazione di Jenkins](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare Jenkins nell'ambito di una soluzione di integrazione CI/CD. Il servizio Azure Kubernetes può integrarsi con altre soluzioni e strumenti di automazione CI/CD, ad esempio il [progetto Azure DevOps][azure-devops] o la [creazione di un cluster servizio Azure Kubernetes con Ansible][aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
