---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b044912fd8f52f3f4fdbe1b3a74b64f9b565ddf0
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673400"
---
1. Nel [portale](https://portal.azure.com) passare alla rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Subnet** per espandere la pagina **Subnet**.
3. Nella pagina **Subnet** fare clic su **+Subnet del gateway** per aprire la pagina **Aggiungi subnet**. 

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/addgwsubnet.png "Aggiungere la subnet del gateway")
4. Il **nome** della subnet verrà compilato automaticamente con il valore 'GatewaySubnet'. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** compilati automaticamente in modo che corrispondano ai requisiti di configurazione. Non configurare la tabella di route o gli endpoint di servizio.

   ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-p2s-rm-portal-include/p2sgwsub.png "Aggiunta della subnet")
5. Fare clic su **OK** in basso nella pagina per creare la subnet.
