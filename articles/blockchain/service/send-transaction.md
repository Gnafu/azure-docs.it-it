---
title: Usare i contratti intelligenti nel servizio Azure Blockchain
description: Esercitazione su come usare il servizio Azure Blockchain per distribuire un contratto intelligente ed eseguire una funzione tramite una transazione.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705124"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>Esercitazione: Usare i contratti intelligenti nel servizio Azure Blockchain

In questa esercitazione si userà Azure Blockchain Development Kit per Ethereum per creare e distribuire un contratto intelligente e quindi per eseguire una funzione del contratto intelligente tramite una transazione in una rete blockchain per consorzi.

È possibile usare Azure Blockchain Development Kit per Ethereum per:

> [!div class="checklist"]
> * Connettersi a un membro della blockchain per consorzi del servizio Azure Blockchain
> * Creare un contratto intelligente
> * Distribuire un contratto intelligente
> * Eseguire una funzione del contratto intelligente tramite una transazione
> * Eseguire una query sullo stato del contratto

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Estensione Azure Blockchain Development Kit per Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Aggiungere python.exe al percorso. Python nel percorso è necessario per Azure Blockchain Development Kit.
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Interfaccia della riga di comando di Ganache](https://github.com/trufflesuite/ganache-cli)

### <a name="verify-azure-blockchain-development-kit-environment"></a>Verificare l'ambiente di Azure Blockchain Development Kit

Azure Blockchain Development Kit verifica che siano stati soddisfatti i prerequisiti per l'ambiente di sviluppo. Per verificare l'ambiente di sviluppo:

Dal riquadro comandi di VS Code scegliere **Azure Blockchain: Mostra pagina iniziale**.

Azure Blockchain Development Kit esegue uno script di convalida che richiede circa un minuto per il completamento. È possibile visualizzare l'output selezionando **Terminale > Nuovo terminale**. Nella barra dei menu del terminale selezionare la scheda **Output** e **Azure Blockchain** nell'elenco a discesa. Una convalida con esito positivo ha un aspetto simile all'immagine seguente:

![Ambiente di sviluppo valido](./media/send-transaction/valid-environment.png)

 Se non è presente uno strumento necessario, una nuova scheda denominata **Azure Blockchain Development Kit - Anteprima** elenca le app necessarie da installare e i collegamenti per scaricare gli strumenti.

![App necessarie per il kit di sviluppo](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>Connettersi a un membro del consorzio

È possibile connettersi ai membri del consorzio usando l'estensione di VS Code Azure Blockchain Development Kit. Una volta connessi a un consorzio, è possibile compilare e distribuire contratti intelligenti a un membro del consorzio del servizio Azure Blockchain.

Se non si ha accesso a un membro del consorzio del servizio Azure Blockchain, completare il prerequisito [Avvio rapido: Creare un membro della blockchain con il portale di Azure](create-member.md) o [Avvio rapido: Creare un membro della blockchain del servizio Azure Blockchain usando l'interfaccia della riga di comando di Azure](create-member-cli.md).

1. Nel riquadro Explorer di Visual Studio Code (VS Code) espandere l'estensione **Azure Blockchain**.
1. Selezionare **Connect to Consortium** (Connetti al consorzio).

   ![Connect to consortium (Connetti al consorzio)](./media/send-transaction/connect-consortium.png)

    Se viene richiesta l'autenticazione di Azure, seguire le istruzioni per eseguire l'autenticazione tramite un browser.
1. Scegliere **Connect to Azure Blockchain Service consortium** (Connetti al consorzio del servizio Azure Blockchain) nell'elenco a discesa del riquadro comandi.
1. Scegliere la sottoscrizione e il gruppo di risorse associati al membro del consorzio del servizio Azure Blockchain.
1. Scegliere il consorzio dall'elenco.

I membri del consorzio e della blockchain sono elencati nella barra laterale di Visual Studio Explorer.

![Consorzio visualizzato in Explorer](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>Creare un contratto intelligente

Azure Blockchain Development Kit per Ethereum usa i modelli di progetto e gli strumenti di Truffle per semplificare lo scaffolding, la compilazione e la distribuzione dei contratti.

1. Dal riquadro comandi di VS Code scegliere **Azure Blockchain: New Solidity Project** (Nuovo progetto Solidity).
1. Scegliere **Create basic project** (Crea progetto di base).
1. Creare una nuova cartella denominata `HelloBlockchain` e scegliere **Select new project path** (Selezionare nuovo percorso progetto).

Azure Blockchain Development Kit crea e inizializza automaticamente un nuovo progetto Solidity. Il progetto di base include un contratto intelligente di esempio denominato **HelloBlockchain** e tutti i file necessari per eseguire la compilazione e la distribuzione al membro del consorzio nel servizio Azure Blockchain. La creazione del progetto può richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento nel pannello del terminale di VS Code selezionando l'output per Azure Blockchain.

La struttura del progetto avrà un aspetto simile al seguente:

   ![Progetto Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Compilare un contratto intelligente

I contratti intelligenti si trovano nella directory **contracts** del progetto. È necessario compilare i contratti intelligenti prima della distribuzione a una blockchain. Usare il comando **Build Contracts** (Compila contratti) per compilare tutti i contratti intelligenti nel progetto.

1. Nella barra laterale di Explorer di VS Code espandere la cartella **contracts** nel progetto.
1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Build Contracts** (Compila contratti) dal menu.

    ![Build contracts (Compila contratti)](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit usa Truffle per compilare i contratti intelligenti.

![Output di compilazione](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Distribuire un contratto intelligente

Truffle usa gli script di migrazione per distribuire i contratti a una rete Ethereum. Le migrazioni sono file JavaScript che si trovano nella directory **migrations** del progetto.

1. Per distribuire un contratto intelligente, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Deploy Contracts** (Distribuisci contratti) dal menu.
1. Scegliere la rete per consorzi Azure Blockchain in **From truffle-config.js** (Da truffle-config.js). La rete blockchain per consorzi è stata aggiunta al file di configurazione Truffle del progetto al momento della creazione del progetto.
1. Scegliere **Generate mnemonic** (Genera tasto di scelta). Scegliere un nome file e salvare il file dei tasti di scelta nella cartella del progetto. Ad esempio: `myblockchainmember.env`. Il file dei tasti di scelta viene usato per generare una chiave privata Ethereum per il membro della blockchain.

Azure Blockchain Development Kit usa Truffle per eseguire lo script di migrazione per distribuire i contratti alla blockchain.

![Distribuzione del contratto riuscita](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Chiamare una funzione del contratto

La funzione **SendRequest** del contratto **HelloBlockchain** modifica la variabile di stato **RequestMessage**. La modifica dello stato di una rete blockchain viene eseguita tramite una transazione. È possibile creare uno script per eseguire la funzione **SendRequest** tramite una transazione.

1. Creare un nuovo file nella radice del progetto Truffle e denominarlo `sendrequest.js`. Aggiungere il codice JavaScript Web3 seguente al file.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Quando Azure Blockchain Development Kit crea un progetto, il file di configurazione Truffle viene generato con i dettagli dell'endpoint della rete blockchain per consorzi. Aprire **truffle-config.js** nel progetto. Il file di configurazione elenca due reti: una denominata development e una con lo stesso nome del consorzio.
1. Nel riquadro del terminale di VS Code usare Truffle per eseguire lo script nella rete blockchain per consorzi. Nella barra dei menu del riquadro del terminale selezionare la scheda **Terminale** e **PowerShell** nell'elenco a discesa.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Sostituire \<blockchain network\> con il nome della rete blockchain definita in **truffle-config.js**.

Truffle esegue lo script nella rete blockchain.

![Output dello script](./media/send-transaction/execute-transaction.png)

Quando si esegue una funzione del contratto tramite una transazione, la transazione non viene elaborata finché non viene creato un blocco. Le funzioni destinate a essere eseguite tramite una transazione restituiscono un ID transazione anziché un valore restituito.

## <a name="query-contract-state"></a>Eseguire una query sullo stato del contratto

Le funzioni dei contratti intelligenti possono restituire il valore corrente delle variabili di stato. Aggiungere una funzione per restituire il valore di una variabile di stato.

1. In **HelloBlockchain.sol** aggiungere una funzione **getMessage** al contratto intelligente **HelloBlockchain**.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    La funzione restituisce il messaggio archiviato in una variabile di stato in base allo stato corrente del contratto.

1. Fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Build Contracts** (Compila contratti) dal menu per compilare le modifiche apportate al contratto intelligente.
1. Per eseguire la distribuzione, fare clic con il pulsante destro del mouse su **HelloBlockchain.sol** e scegliere **Deploy Contracts** (Distribuisci contratti) dal menu.
1. Successivamente, creare uno script per chiamare la funzione **getMessage**. Creare un nuovo file nella radice del progetto Truffle e denominarlo `getmessage.js`. Aggiungere il codice JavaScript Web3 seguente al file.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Nel riquadro del terminale di VS Code usare Truffle per eseguire lo script nella rete blockchain. Nella barra dei menu del riquadro del terminale selezionare la scheda **Terminale** e **PowerShell** nell'elenco a discesa.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Sostituire \<blockchain network\> con il nome della rete blockchain definita in **truffle-config.js**.

Lo script esegue una query sul contratto intelligente chiamando la funzione getMessage. Viene restituito il valore corrente della variabile di stato **RequestMessage**.

![Output dello script](./media/send-transaction/execute-get.png)

Si noti che il valore non è **Hello, blockchain!** . Il valore restituito è un segnaposto. Quando si modifica e si distribuisce il contratto, il contratto ottiene un nuovo indirizzo del contratto e alle variabili di stato vengono assegnati i valori nel costruttore del contratto intelligente. Lo script di migrazione di esempio Truffle **2_deploy_contracts.js** distribuisce il contratto intelligente e passa un valore segnaposto come argomento. Il costruttore imposta la variabile di stato **RequestMessage** sul valore segnaposto, ovvero il valore restituito.

1. Per impostare la variabile di stato **RequestMessage** ed eseguire una query sul valore, eseguire di nuovo gli script **sendrequest.js** e **getmessage.js**.

    ![Output dello script](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** imposta la variabile di stato **RequestMessage** su **Hello, blockchain!** e **getmessage.js** esegue una query sul contratto per ottenere il valore della variabile di stato **RequestMessage** e restituisce **Hello, blockchain!** .

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, è possibile eliminare le risorse eliminando il gruppo di risorse `myResourceGroup` creato nell'avvio rapido *Creare un membro della blockchain* eseguito come parte dei prerequisiti.

Per eliminare il gruppo di risorse:

1. Nel portale di Azure passare a **Gruppo di risorse** nel riquadro di spostamento a sinistra e selezionare il gruppo di risorse da eliminare.
1. Selezionare **Elimina gruppo di risorse**. Confermare l'eliminazione immettendo il nome del gruppo di risorse e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un progetto Solidity di esempio usando Azure Blockchain Development Kit. È stato compilato e distribuito un contratto intelligente e quindi è stata chiamata una funzione tramite una transazione in una rete blockchain per consorzi ospitata nel servizio Azure Blockchain.

> [!div class="nextstepaction"]
> [Sviluppo di applicazioni blockchain con il servizio Azure Blockchain](develop.md)
