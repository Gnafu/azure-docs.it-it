---
title: File di inclusione
description: File di inclusione
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180410"
---
1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota. 

2. In Progettazione app per la logica immettere "github" come filtro. 

3. Selezionare il connettore GitHub e il trigger da usare.

   ![Selezionare il connettore di GitHub e un trigger](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Tutti i flussi di lavoro delle app per la logica devono iniziare con un trigger. È possibile selezionare azioni solo quando il flusso di lavoro della logica inizia già con un trigger. 

4. Se non si è creata una connessione in precedenza, scegliere **Accedi** per poter fornire le credenziali di GitHub quando richiesto.  

   ![Accedere con le credenziali di GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   L'app per la logica usa queste credenziali per autorizzare la connessione e l'accesso ai dati per l'account GitHub. 

5. Fornire il nome utente e la password di GitHub, quindi confermare l'autorizzazione.

   ![Fornire le credenziali e confermare l'autorizzazione](./media/connectors-create-api-github/github-connector-authorize.png)   

   La connessione è stata creata nel portale di Azure ed è pronta per l'uso.

6. Continuare a definire il flusso di lavoro dell'app per la logica.

   ![Aggiungere altre azioni al flusso di lavoro dell'app per la logica](./media/connectors-create-api-github/github-connector-logic-app.png)

