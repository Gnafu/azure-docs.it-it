---
title: 'Esercitazione: Assegnare un accesso in Gestione costi di Azure | Microsoft Docs'
description: In questa esercitazione si apprenderà come assegnare un accesso ai dati di gestione costi con account utente che definiscono i livelli di accesso alle entità.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: c1be4d649bf4b69a9f749003b5c66142006b78e0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Esercitazione: Assegnare l'accesso ai dati di gestione costi

L'accesso ai dati di Gestione dei costi è fornito per utente o gestione di entità. Gli account utente Cloudyn determinano l'accesso a *entità* e le funzioni amministrative. Esistono due tipi di accesso: amministratore e utente. A meno che non sia modificato per ogni utente, l'accesso amministrativo consente all'utente di usare senza restrizioni tutte le funzioni del portale Cloudyn, tra cui: gestione degli utenti, la gestione degli elenchi di destinatari e accesso all'entità radice per tutti i dati di entità. L'accesso utente consente agli utenti finali di visualizzare e creare report usando l'accesso di cui dispongono ai dati di entità.

Le entità vengono usate in modo da riflettere la struttura gerarchica dell'organizzazione e consentono di identificare reparti, divisioni e team dell'organizzazione in Cloudyn. La gerarchia delle entità consente di verificare in modo accurato la spesa per le entità.

Al momento della registrazione dell'account o del contratto di Azure, in Cloudyn è stato creato un account con autorizzazione di amministratore, in modo da poter eseguire tutti i passaggi in questa esercitazione. Questa esercitazione illustra l'accesso ai dati di gestione dei costi, incluso la gestione utente e la gestione di entità. Si apprenderà come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Creare entità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

- È necessario disporre di un account Azure.
- È necessario avere una registrazione di valutazione o una sottoscrizione a pagamento per Gestione costi di Azure.

## <a name="create-a-user-with-admin-access"></a>Creare un utente con accesso amministrativo

Anche se si dispone già di un accesso amministrativo, lo stesso potrebbe essere necessario anche per i colleghi all'interno dell'organizzazione. Nel portale di Cloudyn, fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Gestione utenti**. Fare clic su **Aggiunta nuovo utente** per aggiungere un nuovo utente.

Immettere le informazioni necessarie relative all'utente. L'ID di accesso deve essere un indirizzo di posta elettronica valido. Scegliere le autorizzazioni per consentire la Gestione utenti in modo che l'utente possa creare e modificare altri utenti. La gestione degli elenchi di destinatari consente all'utente di modificare gli elenchi di destinatari. Selezionando **Notify user by email** (Notifica utente tramite posta elettronica), all'utente verrà inviato tramite posta elettronica da Cloudyn un collegamento con informazioni di accesso. L'utente imposta una password al primo accesso.

In **User has admin access** (L'utente dispone di un accesso amministrativo), viene selezionata l'entità radice dell'organizzazione. Lasciare la radice selezionata e salvare le informazioni utente. La selezione dell'entità radice consente all'utente di disporre dell'autorizzazione di amministratore non solo per l'entità radice nell'albero, ma anche per tutte le entità che si trovano sotto di essa.  
  ![aggiungere un nuovo utente con accesso amministrativo](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Creare un utente con accesso utente
Gli utenti tipici che richiedono l'accesso a dati di gestione dei costi come dashboard e report devono disporre di un accesso utente per visualizzarli. Creare un nuovo utente con accesso utente simile a quello creato con accesso amministrativo, con le differenze seguenti:

- Deselezionare **Allow User Management** (Consenti gestione utente), **Allow Recipient lists Management** (Consenti gestione elenchi di utenti) e deselezionare tutto nell'elenco **User has admin access** (L'utente dispone dell'accesso amministrativo).
- Selezionare le entità a cui l'utente deve accedere nell'elenco **User has user access** (L'utente dispone di un accesso utente).
- È anche possibile consentire all'amministratore di accedere a entità specifiche, in base alle esigenze.

![aggiungere un nuovo utente con accesso utente](.\media\tutorial-user-access\new-user-access.png)

Per guardare un video di esercitazione sull'aggiunta di utenti, vedere [Aggiungere utenti in Gestione costi di Azure](https://youtu.be/Nzn7GLahx30).

## <a name="create-entities"></a>Creare entità

Quando si definisce la gerarchia delle entità di costi, una procedura consigliata consiste nell'identificare la struttura dell'organizzazione.

Mentre si compila la struttura ad albero, prendere in considerazione la modalità preferibile o necessaria per visualizzare i relativi costi isolati per business unit, centri di costo, ambienti e reparti vendite. L'albero delle entità in Cloudyn è flessibile data l'ereditarietà delle entità. Le singole sottoscrizioni per gli account cloud sono collegate a entità specifiche. Le entità sono quindi multi-tenant. Tramite le entità, è possibile assegnare a utenti specifici l'accesso al solo segmento dell'azienda pertinente. In questo modo i dati vengono mantenuti isolati, anche attraverso aree di grandi dimensioni di un'azienda, come le società affiliate. L'isolamento dei dati semplifica anche la governance.  

Al momento della registrazione del contratto o dell'account di Azure con Cloudyn, i dati di risorse di Azure, inclusi l'uso, le prestazioni, la fatturazione e i dati di tag delle sottoscrizioni sono stati copiati nel proprio account Cloudyn. Tuttavia, la struttura ad albero delle entità deve essere creata manualmente. Se non si è eseguito la registrazione in Azure Resource Manager, sul portale di Cloudyn saranno disponibili solo i dati di fatturazione e alcuni report di asset.

Nel portale di Cloudyn fare clic sul simbolo a forma di ingranaggio in alto a destra e selezionare **Cloud Accounts** (Account cloud). Si inizierà con una singola entità (radice), per poi compilare la struttura ad albero a partire da essa. Di seguito è riportato un esempio di una gerarchia di entità che potrebbe somigliare a molte organizzazioni IT dopo avere completato la struttura ad albero:

![struttura ad albero di entità](.\media\tutorial-user-access\entity-tree.png)

Accanto a **Entità**, fare clic su **Aggiungi entità**. Immettere le informazioni sulla persona o sul reparto che si desidera aggiungere. I campi **Nome e cognome** e **Posta elettronica** non devono corrispondere a utenti esistenti. Per visualizzare un elenco di livelli di accesso, cercare *Aggiunta di un'entità* nella Guida.

![aggiungi entità](.\media\tutorial-user-access\add-entity.png)

Al termine, **salvare** l'entità.


Per guardare un video di esercitazione sulla creazione di una gerarchia di entità costo, vedere [Creare una gerarchia di entità costo in Gestione costi di Azure](https://youtu.be/dAd9G7u0FmU).

Per gli utenti di un contratto Enterprise di Azure, è disponibile un video di esercitazione sull'associazione di account e sottoscrizioni a entità nella pagina [Connessione ad Azure Resource Manager con Gestione costi di Azure](https://youtu.be/oCIwvfBB6kk).

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un utente con accesso amministrativo
> * Creare un utente con accesso utente
> * Creare entità

Se non è già stato abilitato l'accesso all'API di Azure Resource Manager per gli account, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Attivare sottoscrizioni e account di Azure](activate-subs-accounts.md)
