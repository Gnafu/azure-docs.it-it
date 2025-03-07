---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 99a7dec6936b86af4ab9b80d266cd886dae66d12
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381885"
---
Per creare un progetto di Visual Studio per lo sviluppo di applicazioni desktop con C++, è necessario configurare le opzioni di sviluppo di Visual Studio, creare il progetto, selezionare l'architettura di destinazione e installare Speech SDK. 

### <a name="set-up-visual-studio-development-options"></a>Configurare le opzioni di sviluppo di Visual Studio

Per iniziare, verificare di essere configurati correttamente in Visual Studio per lo sviluppo di applicazioni desktop con C++:

1. Aprire Visual Studio 2019 per visualizzare la finestra **iniziale**.

   ![Finestra iniziale - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. Fare clic su **Continua senza codice** per passare all'IDE di Visual Studio.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

   ![Scheda Carichi di lavoro, finestra di dialogo Modifica, Programma di installazione di Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Nella scheda **Carichi di lavoro**, in **Windows**, individuare il carico di lavoro **Sviluppo di applicazioni desktop con C++** . Se la casella di controllo accanto a tale carico di lavoro non è già selezionata, selezionarla.

1. Nella scheda **Singoli componenti** individuare la casella di controllo **Gestione pacchetti NuGet**. Se la casella di controllo non è già selezionata, selezionarla.

1. Nell'angolo fare clic sul pulsante **Chiudi** o **Modifica**. Il nome del pulsante varia a seconda che siano state selezionate o meno alcune funzionalità per l'installazione. Se si fa clic su **Modifica**, inizia l'installazione, che può richiedere alcuni minuti.

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Creare il progetto e selezionare l'architettura di destinazione

Creare quindi il progetto:

1. Dalla barra dei menu di Visual Studio scegliere **File** > **Nuovo** > **Progetto** per visualizzare la finestra **Crea un nuovo progetto**.

   ![Crea un nuovo progetto, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Individuare e selezionare **App console**. Assicurarsi di selezionare la versione C++ (e non C# o Visual Basic) di questo tipo di progetto.

1. Fare clic su **Avanti** per visualizzare la schermata **Configura il nuovo progetto**.

   ![Configura il nuovo progetto, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. In **Nome del progetto** immettere `helloworld`.

1. In **Percorso** individuare e selezionare oppure creare la cartella in cui salvare il progetto.

A questo punto selezionare l'architettura della piattaforma di destinazione. Nella barra degli strumenti di Visual Studio individuare la casella di riepilogo a discesa **Piattaforme soluzione**. Se non è visibile, scegliere **Visualizza** > **Barre degli strumenti** > **Standard** per visualizzare la barra degli strumenti contenente **Piattaforme soluzione**. Se è in esecuzione Windows a 64 bit, selezionare **x64** dalla casella di riepilogo a discesa. Windows a 64 bit può eseguire anche applicazioni a 32 bit, pertanto è possibile selezionare **x86** se si preferisce.

### <a name="install-the-speech-sdk"></a>Installare Speech SDK

Installare infine il [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e fare riferimento a tale SDK nel progetto:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestire pacchetti NuGet per la soluzione** per passare alla finestra **NuGet - Soluzione**.

1. Selezionare **Esplora**.

   ![NuGet - scheda Soluzione, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. In **Origine dei pacchetti** selezionare **nuget.org**.

1. Nella casella **Cerca** immettere `Microsoft.CognitiveServices.Speech` e quindi fare clic su tale pacchetto dopo che viene visualizzato nei risultati della ricerca.

   ![Installazione del pacchetto C++ Microsoft.CognitiveServices.Speech](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Nel riquadro relativo allo stato del pacchetto, accanto ai risultati della ricerca, selezionare il progetto **helloworld**.

1. Selezionare **Installa**.

1. Nella finestra di dialogo **Anteprima modifiche** fare clic su **OK**.

1. Nella finestra di dialogo **Accettazione della licenza** visualizzare la licenza e quindi fare clic su **Accetto**. Inizierà l'installazione del pacchetto e, al termine, nel riquadro **Output** verrà visualizzato un messaggio simile al testo seguente: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.6.0' to helloworld`. 
