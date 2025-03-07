---
title: 'Guida introduttiva: Riconoscimento vocale, Unity - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione di riconoscimento vocale con Unity e Speech SDK per Unity (beta). Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/24/2019
ms.author: jhakulin
ms.openlocfilehash: 1b6e60edd86cff2d657b562f05351e20571c0909
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815311"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity-beta"></a>Guida introduttiva: Riconoscimento vocale con Speech SDK per Unity (beta)

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Usare questa guida per creare un'applicazione di riconoscimento vocale con [Unity](https://unity3d.com/) e Speech SDK per Unity (beta).
Al termine, sarà possibile parlare nel dispositivo per trascrivere contenuti vocali in tempo reale.
Se non si ha familiarità con Unity, è consigliabile studiare il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/UnityManual.html) prima di sviluppare l'applicazione.

> [!NOTE]
> Speech SDK per Unity è attualmente in versione beta.
> Supporta Windows Desktop (x86 e x64) o la piattaforma UWP (Universal Windows Platform) (x86, x64, ARM/ARM64) e Android (x86, ARM32/64).

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

- [Unity 2018.3 o versioni successive](https://store.unity.com/) con [Unity 2019.1 che aggiunge il supporto per la piattaforma UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). È accettabile anche Visual Studio 2017 versione 15.9 o successive.
  - Per il supporto di ARM64, installare gli [strumenti di compilazione facoltativi per ARM64 e Windows 10 SDK per ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
- Accesso al microfono del computer

## <a name="create-a-unity-project"></a>Creare un progetto Unity

1. Aprire Unity. Se si usa Unity per la prima volta, viene visualizzata la finestra **Unity Hub** (Hub Unity) *<version number>* . È anche possibile aprire Unity Hub direttamente per accedere a questa finestra.

   [![Finestra Unity Hub](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Selezionare **Nuovo**. Viene visualizzata la finestra **Create a new project with Unity** (Crea un nuovo progetto con Unity) *<version number>* .

   [![Creare un nuovo progetto in Unity Hub](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. In **Project Name** (Nome progetto) immettere **csharp-unity**.
1. In **Templates** (Modelli) selezionare **3D**, se non è già selezionato.
1. In **Location** (Percorso) selezionare o creare una cartella in cui salvare il progetto.
1. Selezionare **Create** (Crea).

Dopo qualche istante verrà visualizzata la finestra dell'editor di Unity.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Per installare Speech SDK per Unity, seguire questa procedura:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Scaricare e aprire [Speech SDK per Unity (beta)](https://aka.ms/csspeech/unitypackage), fornito come pacchetto di asset Unity, con estensione unitypackage. All'apertura del pacchetto di asset, viene visualizzata la finestra di dialogo **Import Unity Package** (Importa pacchetto Unity).

   [![Finestra di dialogo Import Unity Package nell'editor di Unity](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Verificare che tutti i file siano selezionati e quindi selezionare **Import** (Importa). Dopo alcuni istanti, il pacchetto di asset Unity viene importato nel progetto.

Per altre informazioni sull'importazione di pacchetti di asset in Unity, vedere la [documentazione di Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Aggiungere l'interfaccia utente

A questo punto verrà aggiunta un'interfaccia utente minima, costituita da un pulsante per attivare il riconoscimento vocale e un campo di testo per visualizzare il risultato. La [finestra **Hierarchy**](https://docs.unity3d.com/Manual/Hierarchy.html) (Gerarchia) contiene una scena di esempio creata da Unity con il nuovo progetto.

1. Nella parte superiore della finestra **Hierarchy** (Gerarchia) selezionare **Create** > **UI** > **Button** (Crea > Interfaccia utente > Pulsante).

   Questa azione crea tre oggetti di gioco, visualizzati nella finestra **Hierarchy** (Gerarchia): un oggetto **Button**, un oggetto **Canvas** che contiene il pulsante e un oggetto **EventSystem**.

   [![Ambiente dell'editor di Unity](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Spostarsi nella visualizzazione **Scene**](https://docs.unity3d.com/Manual/SceneViewNavigation.html) (Scena), in modo da poter esaminare il canvas e il pulsante nella [visualizzazione **Scene**](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. Nella [finestra **Inspector**](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Controllo), sul lato destro per impostazione predefinita, impostare le proprietà **Pos X** e **Pos Y** su **0**, in modo che il pulsante sia posizionato al centro del canvas.

1. Nella finestra **Hierarchy** (Gerarchia) selezionare **Create** > **UI** > **Text** (Crea > Interfaccia utente > Testo) per creare un oggetto **Text**.

1. Nella finestra **Inspector** (Controllo) impostare le proprietà **Pos X** e **Pos Y** su **0** e **120**, e le proprietà **Width** (Larghezza) e **Height** (Altezza) su **240** e **120**. Questi valori assicurano che il campo di testo e il pulsante non si sovrappongano.

Al termine, la visualizzazione **Scene** dovrebbe essere simile allo screenshot seguente:

[![Visualizzazione Scene nell'editor di Unity](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

Per aggiungere il codice di script di esempio per il progetto Unity, seguire questa procedura:

1. Nella [finestra Project](https://docs.unity3d.com/Manual/ProjectView.html) (Progetto) selezionare **Create** > **C# script** (Crea > Script C#) per aggiungere un nuovo script C#.

   [![Finestra Project nell'editor di Unity](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Assegnare il nome `HelloWorld` allo script.

1. Fare doppio clic su `HelloWorld` per modificare lo script appena creato.

   > [!NOTE]
   > Per configurare l'editor di codice che verrà usato da Unity per la modifica, selezionare **Edit** > **Preferences** (Modifica > Preferenze) e quindi passare alle preferenze per **External Tools** (Strumenti esterni). Per altre informazioni, vedere il [manuale dell'utente di Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Sostituire lo script esistente con il codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Trovare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Trovare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche allo script.

A questo punto, tornare all'editor di Unity e aggiungere lo script come componente a uno degli oggetti di gioco:

1. Nella finestra **Hierarchy** (Gerarchia) selezionare l'oggetto **Canvas**.

1. Nella finestra **Inspector** (Controllo) selezionare il pulsante **Add Component** (Aggiungi componente).

   [![Finestra Inspector nell'editor di Unity](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. Nell'elenco a discesa cercare lo script `HelloWorld` creato in precedenza e aggiungerlo. Nella finestra **Inspector** (Controllo) viene visualizzata una sezione **Hello World (Script)** che contiene due proprietà non inizializzate, **Output Text** (Testo di output) e **Start Reco Button** (Pulsante Avvia registrazione). Queste proprietà dei componenti Unity corrispondono a proprietà pubbliche della classe `HelloWorld`.

1. Selezionare il selettore oggetti della proprietà **Start Reco Button** (icona a forma di piccolo cerchio a destra della proprietà) e scegliere l'oggetto **Button** creato in precedenza.

1. Selezionare il selettore oggetti della proprietà **Output Text** e scegliere l'oggetto **Text** creato in precedenza.

   > [!NOTE]
   > Il pulsante include anche un oggetto testo annidato. Prestare attenzione a non selezionarlo accidentalmente per l'output di testo oppure rinominare uno degli oggetti testo usando il campo **Name** (Nome) nella finestra **Inspector** (Controllo) per evitare di confondersi.

## <a name="run-the-application-in-the-unity-editor"></a>Eseguire l'applicazione nell'editor di Unity

A questo punto è possibile eseguire l'applicazione nell'editor di Unity.

1. Sulla barra degli strumenti dell'editor di Unity, sotto la barra dei menu, selezionare il pulsante **Play** (Riproduci), un triangolo rivolto verso destra.

1. Passare alla [visualizzazione **Game**](https://docs.unity3d.com/Manual/GameView.html) (Gioco) e attendere che l'oggetto **Text** visualizzi **Click button to recognize speech** (Fare clic sul pulsante per riconoscere la voce). L'oggetto visualizza **New Text** (Nuovo testo) quando l'applicazione non è stata avviata o non è pronta per rispondere.

1. Selezionare il pulsante e pronunciare una frase o un'espressione in inglese nel microfono del computer. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo, che compare nella visualizzazione **Game** (Gioco).

   [![Visualizzazione Game nell'editor di Unity](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Verificare la presenza di messaggi di debug nella [finestra **Console**](https://docs.unity3d.com/Manual/Console.html). Se la finestra **Console** non è visualizzata, passare alla barra dei menu e selezionare **Window** > **General** > **Console** (Finestra > Generale > Console) per visualizzarla.

1. Al termine del riconoscimento vocale, selezionare il pulsante **Play** (Riproduci) sulla barra degli strumenti dell'editor di Unity per arrestare l'applicazione.

## <a name="additional-options-to-run-this-application"></a>Altre opzioni per l'esecuzione dell'applicazione

Questa applicazione può essere distribuita anche come app Android, come app autonoma di Windows o come applicazione UWP.
Per altre informazioni, vedere il [repository degli esempi](https://aka.ms/csspeech/samples). La cartella `quickstart/csharp-unity` descrive la configurazione per queste destinazioni aggiuntive.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Eseguire il training di un modello per Riconoscimento vocale personalizzato](how-to-custom-speech-train-model.md)
