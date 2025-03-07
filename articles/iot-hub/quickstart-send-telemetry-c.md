---
title: Guida introduttiva sull'invio di dati di telemetria all'hub IoT di Azure (C) | Microsoft Docs
description: In questa guida introduttiva si eseguono due applicazioni C di esempio per inviare dati di telemetria simulati a un hub IoT e leggere i dati di telemetria dall'hub IoT per l'elaborazione nel cloud.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 842efca1b40827f63ab23581aeac7e5226d04349
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900271"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Avvio rapido: Inviare dati di telemetria da un dispositivo a un hub IoT e leggere i dati con un'applicazione di back-end (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

L'hub IoT è un servizio di Azure che consente di acquisire volumi elevati di dati di telemetria dai dispositivi IoT nel cloud per l'archiviazione o l'elaborazione. In questa Guida rapida, si inviano dati di telemetria da un'applicazione del dispositivo simulato, tramite l'Hub IoT, a un'applicazione di back-end per l'elaborazione.

La guida introduttiva usa un'applicazione di esempio C da [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md) per inviare dati di telemetria a un hub IoT. Gli Azure IoT SDK per dispositivi sono scritti in [ANSI C (C99)](https://wikipedia.org/wiki/C99) per la portabilità e la compatibilità multipiattaforma. Prima di eseguire il codice di esempio, verrà creato un hub IoT e il dispositivo simulato verrà registrato in tale hub.

Questo articolo è scritto per Windows, ma è possibile completare questa guida introduttiva anche in Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Installare [Visual Studio 2019](https://www.visualstudio.com/vs/) con il carico di lavoro ['Sviluppo di applicazioni desktop con C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) abilitato.
* Installare la versione più recente di [Git](https://git-scm.com/download/).
* Eseguire il comando seguente per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza di Cloud Shell. L'estensione IoT aggiunge i comandi specifici di hub IoT, IoT Edge e servizio Device Provisioning in hub IoT all'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

Per questo argomento di avvio rapido si userà [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md). 

È possibile usare l'SDK installando i pacchetti e librerie per gli ambienti seguenti:

* **Linux**: sono disponibili pacchetti apt-get per Ubuntu 16.04 e 18.04 con le architetture di CPU seguenti: amd64, arm64, armhf e i386. Per altre informazioni, vedere [Use apt-get to create a C device client project on Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md) (Usare apt-get per creare un progetto client di dispositivo C in Ubuntu).

* **mbed**: per gli sviluppatori che creano applicazioni per dispositivi sulla piattaforma mbed, sono stati pubblicati una libreria ed esempi per iniziare velocemente a usare l'hub IoT di Azure. Per altre informazioni, vedere [Use the mbed library](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed) (Usare la libreria mbed).

* **Arduino**: se si sviluppa in Arduino, è possibile sfruttare la libreria di IoT di Azure disponibile nella gestione librerie dell'IDE di Arduino. Per altre informazioni, vedere [The Azure IoT Hub library for Arduino](https://github.com/azure/azure-iot-arduino) (Libreria di Arduino per l'hub IoT di Azure).

* **iOS**: IoT Hub Device SDK è disponibile come CocoaPods per lo sviluppo di dispositivi iOS e Mac. Per altre informazioni, vedere [iOS Samples for Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient) (Esempi di iOS per Microsoft Azure IoT).

Tuttavia, in questo argomento di avvio rapido si preparerà un ambiente di sviluppo usato per clonare e compilare [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) da GitHub. L'SDK in GitHub include il codice di esempio usato in questa guida introduttiva.

1. Scaricare il [sistema di compilazione CMake](https://cmake.org/download/).

    È importante che i prerequisiti di Visual Studio (Visual Studio e il carico di lavoro "Sviluppo di applicazioni desktop con C++") siano installati nel computer **prima** di avviare l'installazione di `CMake`. Quando i prerequisiti sono pronti e il download è stato verificato, installare il sistema di compilazione CMake.

2. Aprire un prompt dei comandi o la shell Git Bash e passare a una directory di lavoro in cui clonare Azure IoT C SDK. Eseguire il comando seguente per clonare il repository GitHub [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

3. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. Immettere i comandi seguenti dalla directory di lavoro:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Eseguire il comando seguente per compilare una versione dell'SDK specifica per la piattaforma di sviluppo client. Verrà generata una soluzione di Visual Studio per il dispositivo simulato nella directory `cmake`.

    ```cmd
    cmake ..
    ```

    Se `cmake` non trova il compilatore C++, è possibile che si verifichino errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrare un dispositivo

È necessario registrare un dispositivo con l'hub IoT perché questo possa connettersi. In questa sezione si userà Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) per registrare un dispositivo simulato.

1. Eseguire questo comando in Azure Cloud Shell per creare l'identità del dispositivo.

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

   **MyCDevice**: nome specificato per il dispositivo registrato. Usare MyCDevice come illustrato. Se si sceglie un altro nome per il dispositivo, sarà necessario usare tale nome anche nell'ambito di questo articolo e aggiornare il nome del dispositivo nelle applicazioni di esempio prima di eseguirle.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Eseguire i comandi seguenti in Azure Cloud Shell per ottenere la _stringa di connessione del dispositivo_ per il dispositivo registrato:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Annotare la stringa di connessione del dispositivo, che avrà questo aspetto:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Il valore verrà usato più avanti in questa guida introduttiva.

## <a name="send-simulated-telemetry"></a>Inviare dati di telemetria simulati

L'applicazione del dispositivo simulato si connette a un endpoint specifico del dispositivo nell'hub IoT e invia una stringa come dati di telemetria simulati.

1. Usando un editor di testo, aprire il file di origine iothub_convenience_sample.c ed esaminare il codice di esempio per l'invio di dati di telemetria. Il file si trova nel percorso seguente nella directory di lavoro in cui è stato clonato Azure IoT C SDK:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Individuare la dichiarazione della costante `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Sostituire il valore della costante `connectionString` con la stringa di connessione del dispositivo annotata in precedenza. Salvare quindi le modifiche in **iothub_convenience_sample.c**.

3. In una finestra del terminale locale passare alla directory del progetto *iothub_convenience_sample* nella directory CMake creata in Azure IoT C SDK. Immettere il comando seguente dalla directory di lavoro:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Eseguire CMake nella finestra del terminale locale per compilare l'esempio con il valore `connectionString` aggiornato:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Nella finestra del terminale eseguire i comandi seguenti per eseguire l'applicazione del dispositivo simulato:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Lo screenshot seguente mostra l'output mentre l'applicazione del dispositivo simulato invia i dati di telemetria all'hub IoT:

    ![Eseguire il dispositivo simulato](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Leggere i dati di telemetria dell'hub

In questa sezione si userà Azure Cloud Shell con l'[estensione IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) per eseguire il monitoraggio dei messaggi inviati dal dispositivo simulato.

1. Tramite Azure Cloud Shell, eseguire il comando seguente per connettersi e leggere i messaggi dall'hub IoT:

   **YourIoTHubName**: sostituire il segnaposto in basso con il nome scelto per l'hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Leggere i messaggi del dispositivo tramite l'interfaccia della riga di comando di Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato configurato un hub IoT, è stato registrato un dispositivo, sono stati inviati dati di telemetria simulati all'hub usando un'applicazione C e i dati di telemetria sono stati letti dall'hub usando Azure Cloud Shell.

Per altre informazioni sullo sviluppo con l'SDK C per l'hub IoT di Azure, continuare con la guida pratica seguente:

> [!div class="nextstepaction"]
> [Sviluppare tramite l'SDK C per l'hub IoT di Azure](iot-hub-devguide-develop-for-constrained-devices.md)