---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "58115715"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparare un'installazione push in un server Linux

1. Verificare che tra il computer Linux e il server di elaborazione sia attiva la connettività di rete.
1. Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente **root** nel server Linux di origine. Usarlo solo per l'installazione push e per gli aggiornamenti.
1. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
1. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da replicare.
1. Assicurarsi che SSH (Secure Shell) sia abilitato e in esecuzione sulla porta 22.
1. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd_config. A tale scopo, seguire questa procedura:

    a. Accedere come utente **root**.

    b. Nel file **/etc/ssh/sshd_config** individuare la riga che inizia con **PasswordAuthentication**.

    c. Rimuovere il commento dalla riga e modificare il valore in **yes**.

    d. Individuare la riga che inizia con **Subsystem** e rimuovere il commento.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Riavviare il servizio **sshd**.

1. Aggiungere l'account creato in CSPSConfigtool. A tale scopo, seguire questa procedura:

    a. Accedere al server di configurazione.

    b. Aprire **cspsconfigtool.exe**. È disponibile come collegamento sul desktop e nella cartella %ProgramData%\home\svsystems\bin.

    c. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

    d. Aggiungere l'account che è stato creato.

    d. Immettere le credenziali usate quando si abilita la replica per un computer.
