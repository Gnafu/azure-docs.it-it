---
title: Pubblicare un annuncio per un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere un annuncio a un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Pubblicare un annuncio per un lab in Azure DevTest Labs

Gli amministratori dei lab possono pubblicare un annuncio personalizzato in un lab esistente per notificare agli utenti modifiche o aggiunte recenti nel lab. Ad esempio, gli utenti potrebbero essere informati di:

- Nuove dimensioni disponibili delle VM
- Immagini attualmente inutilizzabili
- Aggiornamenti ai criteri dei lab

L'annuncio, dopo la pubblicazione, viene visualizzato nella pagina Panoramica del lab e l'utente può selezionarlo per informazioni più dettagliate.

La funzionalità degli annunci deve essere usata per le notifiche temporanee.  È possibile disabilitare facilmente un annuncio quando non è più necessario.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Procedura per pubblicare un annuncio in un lab esistente

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Nell'elenco di lab selezionare il lab in cui si vuole pubblicare un annuncio.  
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.  

    ![Pulsante Configurazione e criteri](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A sinistra sotto **IMPOSTAZIONI** selezionare **Annuncio lab**.

    ![Pulsante Annuncio lab](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Per creare un messaggio per gli utenti in questo lab, impostare **Abilitato** su **Sì**.

1. È possibile immettere una **Data di scadenza** per specificare una data e un'ora in seguito alle quali l'annuncio non verrà più visualizzato agli utenti. Se non si immette una data di scadenza, l'annuncio rimane fino alla disabilitazione.

   > [!NOTE]
   > Dopo la scadenza, l'annuncio non verrà più visualizzato agli utenti, ma rimarrà disponibile nel riquadro **Annuncio lab**. È possibile modificarlo e riabilitarlo per renderlo di nuovo attivo.
   >
   >

1. Immettere un **titolo dell'annuncio** e il **testo dell'annuncio**.

   Il titolo può contenere fino a 100 caratteri e viene visualizzato dall'utente nella pagina Panoramica del lab. Se l'utente seleziona il titolo, viene visualizzato il testo dell'annuncio.

   Il testo dell'annuncio accetta la sintassi markdown. Quando si immette il testo dell'annuncio, è possibile visualizzare il messaggio nell'area Anteprima nella parte inferiore della schermata.

    ![Schermata Annuncio lab per creare il messaggio.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Selezionare **Salva** quando l'annuncio è pronto per essere pubblicato.

Quando non si vuole più visualizzare questo annuncio per gli utenti del lab, tornare alla pagina **Annuncio lab** e impostare **Abilitato** su **No**. Se è stata specificata una data di scadenza, l'annuncio viene disabilitato automaticamente in tale data e ora.

## <a name="steps-for-users-to-view-an-announcement"></a>Procedura per consentire agli utenti di visualizzare un annuncio

1. Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) selezionare un lab.

1. Se per il lab è stato pubblicato un annuncio, viene visualizzato un messaggio informativo nella parte superiore della pagina Panoramica del lab. Questo messaggio è il titolo dell'annuncio specificato quando l'annuncio è stato creato.

    ![Annuncio lab nella pagina Panoramica](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. L'utente può selezionare il messaggio per visualizzare l'intero annuncio.

    ![Altre informazioni per l'annuncio lab](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* Se si modificano o impostano i criteri per un lab, è possibile pubblicare un annuncio per informare gli utenti. [Configurare criteri e pianificazioni](devtest-lab-set-lab-policy.md) contiene informazioni sull'applicazione di restrizioni e convenzioni nella sottoscrizione usando criteri personalizzati.
* Esplorare la [raccolta dei modelli di avvio rapido di Azure Resource Manager di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
