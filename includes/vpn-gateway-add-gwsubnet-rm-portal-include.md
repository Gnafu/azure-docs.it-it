---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673395"
---
1. Nel [portale di Azure](https://portal.azure.com) selezionare la rete virtuale di Resource Manager per cui si vuole creare un gateway di rete virtuale.

2. Nella sezione **Impostazioni** della pagina della rete virtuale selezionare **Subnet** per espandere la pagina **corrispondente**.

3. Nella pagina **Subnet** selezionare **Subnet del gateway** per aprire la pagina **Aggiungi subnet**.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Aggiungere la subnet del gateway")

4. Il **nome** della subnet verrà compilato automaticamente con il valore *GatewaySubnet*. Questo valore è obbligatorio per consentire ad Azure di riconoscere la subnet come subnet del gateway. Modificare i valori di **Intervallo di indirizzi** inseriti automaticamente in modo che corrispondano ai requisiti della configurazione e quindi selezionare **OK** per creare la subnet.

   ![Aggiunta della subnet](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Aggiunta della subnet")
