---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 50a5c8d515e27db7c2c65b484cdecad8ff00baf8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164669"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-connect-through-the-serial-console"></a>Per connettersi tramite console seriale
1. Collegare il cavo seriale al dispositivo (direttamente o tramite un adattatore seriale USB).
2. Aprire il **Pannello di controllo** e quindi **Gestione dispositivi**.
3. Identificare la porta COM, come illustrato nella figura seguente.
   
     ![Connessione tramite console seriale](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Avviare PuTTY. 
5. Nel riquadro destro impostare **Tipo di connessione** su **Seriale**.
6. Nel riquadro di destra, digitare la porta COM appropriata. Assicurarsi che i parametri di configurazione seriale siano impostati come indicato di seguito:
   
   * Velocità: 115.200
   * Bit di dati: 8
   * Bit di stop: 1
   * Parità: nessuno
   * Controllo di flusso: nessuno
     
     Queste impostazioni sono mostrate nella figura seguente.
     
     ![Impostazioni puTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Se l'impostazione del controllo di flusso predefinito non funziona, provare a impostare il controllo di flusso su XON/XOFF.
     > 
     > 
7. Fare clic su **Apri** per avviare una sessione seriale.

