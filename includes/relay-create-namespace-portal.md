---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180375"
---
1. Accedere al [portale di Azure][Azure portal].
2. Nel menu a sinistra selezionare **+ Crea una risorsa**. Quindi, selezionare **Integrazione** > **Inoltro**. Se **Inoltro** non è visibile nell'elenco, selezionare **Visualizza tutto** nell'angolo superiore destro. 
3. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
4. Nella casella **Sottoscrizione** selezionare una sottoscrizione di Azure in cui creare lo spazio dei nomi.
5. Nella casella [Gruppo di risorse](../articles/azure-resource-manager/manage-resource-groups-portal.md) selezionare un gruppo di risorse esistente nel quale inserire lo spazio dei nomi oppure crearne uno nuovo.  
6. In **Località** selezionare il paese o l'area in cui deve essere ospitato lo spazio dei nomi.
   
    ![Crea spazio dei nomi][create-namespace]
7. Selezionare **Create**. Lo spazio dei nomi verrà creato e abilitato nel sistema. Dopo alcuni minuti, il sistema effettua il provisioning delle risorse per l'account.

### <a name="get-management-credentials"></a>Ottenere le credenziali di gestione

1. Selezionare **Tutte le risorse** e quindi selezionare il nome dello spazio dei nomi appena creato.
2. Nello spazio dei nomi dell'inoltro selezionare **Criteri di accesso condiviso**.  
3. In **Criteri di accesso condiviso** selezionare **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. In **Criteri: RootManageSharedAccessKey** selezionare il pulsante **Copia** accanto a **Stringa di connessione - Chiave primaria**. La stringa di connessione viene copiata negli Appunti per un uso successivo. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.
   
    ![connection-string][connection-string]

5. Ripetere il passaggio precedente per copiare e incollare il valore di **Chiave primaria** in un percorso temporaneo per usarlo in seguito.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
