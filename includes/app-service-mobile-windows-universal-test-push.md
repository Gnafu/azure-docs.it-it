---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140445"
---
1. Fare clic con il pulsante destro del mouse sul progetto di Windows Store, selezionare **Imposta come progetto di avvio**e premere il tasto F5 per eseguire l'app di Windows Store.

    All'avvio dell'app, il dispositivo viene registrato per le notifiche push.
2. Arrestare l'app di Windows Store e ripetere i passaggi precedenti per eseguire l'app di Windows Phone Store.

    A questo punto, entrambi i dispositivi sono stati registrati per la ricezione delle notifiche push.

3. Eseguire nuovamente l'app di Windows Store, digitare del testo in **Inserisci TodoItem** e quindi fare clic su **Salva**.

    Si noti che, al termine dell'inserimento, sia l'app di Windows Store che quella di Windows Phone ricevono una notifica push da WNS. La notifica viene visualizzata su Windows Phone anche se l'app non è in esecuzione.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
