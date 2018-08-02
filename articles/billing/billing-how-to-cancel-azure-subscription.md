---
title: Annullare la sottoscrizione di Azure | Microsoft Docs
description: Descrive come annullare la sottoscrizione di Azure, ad esempio la sottoscrizione di valutazione gratuita
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: genli
ms.openlocfilehash: 0bd0fca54ae5bf4292564a15adf38b7586768450
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145452"
---
# <a name="cancel-your-subscription-for-azure"></a>Annullare la sottoscrizione di Azure

Come [amministratore account](billing-subscription-transfer.md#whoisaa), è possibile annullare la sottoscrizione di Azure. Dopo aver annullato la sottoscrizione, l'accesso alle risorse e ai servizi di Azure non sarà più possibile.

Prima di annullare la sottoscrizione:

* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../azure-resource-manager/resource-group-move-resources.md).

Se si annulla un piano di supporto di Azure a pagamento, verrà comunque emessa una fattura per il resto del periodo di validità della sottoscrizione. Per altre informazioni, vedere [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-using-the-azure-portal"></a>Annullare la sottoscrizione usando il portale di Azure

1. Selezionare la sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione da annullare e fare clic su **Annulla sottoscrizione**.

    ![Screenshot che mostra il pulsante Annulla](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
1. Seguire le istruzioni e completare la procedura di annullamento.

## <a name="what-happens-after-i-cancel-my-subscription"></a>Cosa accade quando si annulla la sottoscrizione?

Nel momento in cui viene annullata, la fatturazione viene interrotta immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti.

Dopo questo intervallo, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura.

Se si annulla la sottoscrizione nel corso di un periodo di fatturazione, la fattura finale verrà inviata alla normale data della fattura al termine del periodo. 

Microsoft attenderà 90 giorni prima di eliminare definitivamente i dati, nel caso in cui fosse necessario accedervi o si cambiasse idea. Non verrà comunque addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="reactivate-subscription"></a>Riattivare la sottoscrizione

Se la sottoscrizione con pagamento in base al consumo viene annullata erroneamente, è possibile [riattivarla nel Centro account](billing-subscription-become-disable.md).

Se la sottoscrizione non è con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivare la sottoscrizione.

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico.

Per eventuali domande, è possibile [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.
