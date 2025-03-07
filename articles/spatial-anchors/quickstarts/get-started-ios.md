---
title: Guida introduttiva - Creare un'app iOS usando Ancoraggi nello spazio di Azure | Microsoft Docs
description: In questa guida introduttiva si apprenderà come compilare un'app iOS usando Ancoraggi nello spazio.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56360238db8632e74a95c057a7fe643b5cea3151
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206828"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Guida introduttiva: Creare un'app iOS con Ancoraggi nello spazio di Azure, in Swift o Objective-C

Questa guida introduttiva illustra come creare un'app iOS usando [Ancoraggi nello spazio di Azure](../overview.md) in Swift o Objective-C. Ancoraggi nello spazio di Azure è un servizio per lo sviluppo multipiattaforma che consente di creare esperienze di realtà mista usando oggetti la cui posizione persiste tra dispositivi nel corso del tempo. Al termine, si avrà un'app iOS ARKit in grado di salvare e richiamare un ancoraggio nello spazio.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di Ancoraggi nello spazio
> * Configurare l'identificatore e la chiave dell'account di Ancoraggi nello spazio
> * Distribuire ed eseguire l'app in un dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

- Un computer macOS abilitato per lo sviluppo con <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installati.
- GIT installato tramite HomeBrew. Immettere il comando seguente in una singola riga del terminale: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Quindi eseguire `brew install git`.
- Un dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatibile con ARKit</a> abilitato per lo sviluppo.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

Usare il terminale per le azioni seguenti.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installare i pod necessari usando CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Accedere a `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Accedere a `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Eseguire `pod install --repo-update` per installare CocoaPods per il progetto.

Aprire quindi `.xcworkspace` in Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurare l'identificatore e la chiave dell'account

Il passaggio successivo consiste nel configurare l'app in modo da usare l'identificatore e la chiave dell'account. Questi dati sono stati copiati in un editor di testo durante [la configurazione della risorsa di ancoraggi nello spazio](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Aprire `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Individuare il campo `spatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `spatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Aprire `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Individuare il campo `SpatialAnchorsAccountKey` e sostituire `Set me` con la chiave dell'account.

Individuare il campo `SpatialAnchorsAccountId` e sostituire `Set me` con l'identificatore dell'account.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuire l'app nel dispositivo iOS

Connettere il dispositivo iOS al Mac e impostare lo **schema attivo** sul dispositivo iOS.

![Selezionare il dispositivo](./media/get-started-ios/select-device.png)

Selezionare **Build and then run the current scheme** (Compila e quindi esegui lo schema corrente).

![Compilare ed eseguire](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se viene visualizzato un errore `library not found for -lPods-SampleObjC`, è probabile che sia stato aperto il file `.xcodeproj` invece di `.xcworkspace`. Aprire `.xcworkspace` e riprovare.

In Xcode arrestare l'app premendo **Stop**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Esercitazione: Condividere gli ancoraggi nello spazio tra dispositivi](../tutorials/tutorial-share-anchors-across-devices.md)
