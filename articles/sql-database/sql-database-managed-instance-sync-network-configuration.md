---
title: Servizio app di Azure - sincronizzazione della configurazione di rete | Microsoft Docs
description: Questo articolo illustra come sincronizzare la configurazione di rete per il piano di hosting del servizio app di Azure.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257895"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Sincronizzare la configurazione di rete per il piano di hosting del servizio app di Azure

Può succedere che, anche se si [integra l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md), non sia possibile stabilire una connessione all'istanza gestita. È possibile provare ad aggiornare la configurazione di rete per il piano di servizio. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Sincronizzare la configurazione di rete per il piano di hosting del servizio app

A tale scopo, seguire questi passaggi:  

1. Passare al piano del servizio app per le app Web.
 
   ![piano servizio app](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Fare clic su **Rete** e quindi fare clic su **Fare clic qui per la gestione**.
 
   ![gestire il piano del servizio](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Selezionare la **Rete virtuale** e fare clic su **Sincronizza rete**. 
 
   ![sincronizza rete](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Attendere fino a quando la sincronizzazione non viene completata.
  
   ![sincronizzazione completata](./media/sql-database-managed-instance-sync-networking/sync-done.png)

A questo punto si è pronti per provare a ristabilire la connessione all'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione della rete virtuale per l'istanza gestita, vedere la [configurazione della rete virtuale dell'istanza gestita](sql-database-managed-instance-vnet-configuration.md).
