---
title: Aggiungere set di dati a una condivisione dati esistente nell'anteprima della condivisione dati di Azure
description: Aggiungere set di dati a una condivisione dati esistente
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: 75ac837491927881fd8b7547b0f49a069c3f2d47
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257193"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Come aggiungere set di dati a una condivisione esistente nell'anteprima della condivisione dati di Azure

Questo articolo illustra come aggiungere set di dati a una condivisione dati preesistente usando l'anteprima di condivisione dati di Azure. In questo modo è possibile condividere più dati con gli stessi destinatari senza dover creare una nuova condivisione.

Per informazioni su come aggiungere set di dati durante la creazione di una condivisione, vedere l'esercitazione su come [condividere i dati](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Passare a una condivisione dati inviata

Nell'anteprima della condivisione dati di Azure passare alla condivisione inviata e selezionare la scheda **set** di dati. Fare clic sul pulsante **+ Aggiungi set di DataSet** per aggiungere altri set di impostazioni.

![Aggiungi set di impostazioni](./media/how-to/how-to-add-datasets/add-datasets.png)

Nel pannello a destra selezionare il tipo di set di dati che si desidera aggiungere e quindi fare clic su **Avanti**. Selezionare la sottoscrizione e il gruppo di risorse dei dati che si desidera aggiungere. Utilizzando le frecce a discesa, trovare e quindi selezionare la casella accanto ai dati da aggiungere.

![Aggiungi set di impostazioni](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Una volta fatto clic su **Aggiungi set di impostazioni**, i set di impostazioni verranno aggiunti alla condivisione. Nota: Uno snapshot deve essere attivato dagli utenti affinché possano visualizzare i nuovi set di impostazioni. Se sono state configurate impostazioni snapshot, i consumer visualizzeranno i nuovi set di impostazioni al termine del successivo snapshot pianificato. Senza configurare le impostazioni dello snapshot, il consumer deve attivare manualmente una copia completa o incrementale dei dati per ricevere gli aggiornamenti. Per ulteriori informazioni sugli snapshot, vedere [snapshot](terminology.md).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [aggiungere i destinatari a una condivisione dati esistente](how-to-add-recipients.md).