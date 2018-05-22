---
title: Registrare Azure Enterprise Agreement con Gestione costi di Azure | Microsoft Docs
description: Usare il contratto Enterprise per registrarsi in Gestione costi di Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: b4848a624425a189b403a64a62e87e87bc9e4b74
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrare un Azure Enterprise Agreement e visualizzare i dati sui costi

Usare il contratto Enterprise di Azure per registrarsi in Gestione costi di Azure. La registrazione consente di accedere al portale di Cloudyn. Questa guida introduttiva illustra nei dettagli il processo di registrazione necessario per creare una sottoscrizione di valutazione di Cloudyn e accedere al suo portale. Descrive anche come iniziare a visualizzare da subito i dati sui costi.

## <a name="log-in-to-azure"></a>Accedere ad Azure

- Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Eseguire la registrazione con Gestione costi di Azure

1. Nel portale di Azure fare clic su **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. In **Panoramica** fare clic su **Gestione dei costi**  
    ![Pagina Gestione costi](./media/quick-register-ea/cost-mgt-billing-service.png)
3. Nella pagina **Gestione dei costi** fare clic su **Go to Cost Management** (Vai a Gestione costi) per aprire la pagina di registrazione di Cloudyn in una nuova finestra.
4. Nella pagina della registrazione di valutazione del portale Cloudyn digitare il nome della società e quindi selezionare **Azure Enterprise Enrollment Administrator** (Amministratore delle registrazioni di Azure Enterprise).  
    ![registrazione di valutazione](./media/quick-register-ea/trial-reg.png)
5. Immettere la chiave API di registrazione di Enterprise Portal. Se non si dispone della chiave necessaria fare clic sul link [Enterprise Portal](https://ea.azure.com) ed eseguire i passaggi seguenti:
  1. Accedere al sito Web di Azure Enterprise e fare clic su **Report**, quindi su **API Access Key** (Chiave di accesso dell'API) e copiare la chiave primaria.  
    ![Chiave API di EA](./media/quick-register-ea/ea-key.png)
  3. Tornare alla pagina di registrazione e copiare la chiave API.
6. Accettare le condizioni d'uso e convalidare la chiave. Fare clic su **Avanti** per autorizzare Cloudyn a raccogliere i dati sulle risorse di Azure. Dalla sottoscrizione vengono raccolti dati riguardanti l'uso, le prestazioni, la fatturazione e dati dei tag.  
    ![convalida della chiave](./media/quick-register-ea/ea-key-validated.png)
7. In **Invite other stakeholder** (Invita altri stakeholder) è possibile aggiungere utenti digitando i loro indirizzi di posta elettronica. Al termine, fare clic su **Avanti**. A seconda delle dimensioni della registrazione di Azure, l'aggiunta dei dati di fatturazione a Cloudyn può richiedere fino a 24 ore.
8. Fare clic su **Go to Cloudyn** (Passa a Cloudyn) per aprire il portale di Cloudyn. Nella pagina **Cloud Accounts Management** (Gestione account cloud) saranno visualizzate le informazioni sugli account EA registrati.

Per un video di esercitazione sulla registrazione del contratto Enterprise, vedere [How to Find Your EA Enrollment ID and API Key for use in Azure Cost Management](https://youtu.be/u_phLs_udig) (Come individuare l'ID di registrazione del contratto Enterprise e la chiave API da usare in Gestione costi di Azure).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida sono state usate le informazioni su Azure Enterprise Agreement per registrarsi in Gestione costi. È stato anche eseguito l'accesso al portale di Cloudyn ed è stata avviata la visualizzazione dei dati sui costi. Per altre informazioni su Gestione costi di Azure, continuare l'esercitazione su Gestione costi.

> [!div class="nextstepaction"]
> [Esaminare l'utilizzo e i costi](./tutorial-review-usage.md)
