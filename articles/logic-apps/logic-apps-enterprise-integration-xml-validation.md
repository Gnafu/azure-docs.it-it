---
title: Convalidare XML per l'integrazione aziendale B2B - App per la logica di Azure | Microsoft Docs
description: Convalidare XML con gli schemi per soluzioni B2B in App per la logica di Azure tramite Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: 8db0dbadd944007ff953f9ea69695bf988ffebb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996354"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Convalidare XML per l'integrazione aziendale B2B in App per la logica di Azure con Enterprise Integration Pack

Spesso negli scenari B2B i partner di un contratto devono accertarsi che i messaggi scambiati siano validi prima di avviare l'elaborazione dei dati. In Enterprise Integration Pack, è possibile usare il connettore di convalida XML per convalidare i documenti in base a uno schema predefinito.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Come convalidare un documento con il connettore di convalida XML

1. Creare un'app per la logica e [collegarla all'account di integrazione](../logic-apps/logic-apps-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene lo schema che verrà usato per convalidare i dati XML.

2. Aggiungere un trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Aggiungere l'azione **Convalida XML** selezionando **Aggiungi un'azione**.

4. Immettere *xml* nella casella di ricerca per filtrare tutte le azioni su quella che si desidera. Selezionare **Convalida XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Per specificare il contenuto XML che si desidera convalidare, selezionare **CONTENUTO**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Selezionare il tag del corpo come contenuto che si desidera convalidare.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Per specificare lo schema che desidera usare per convalidare il *contenuto* immesso in precedenza, scegliere **NOME SCHEMA**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Salvare il lavoro  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

A questo punto, la configurazione del connettore di convalida è completa. In un'applicazione reale è possibile archiviare i dati convalidati in un'app line-of-business (LOB), ad esempio SalesForce. Per inviare l'output della convalida a Salesforce, aggiungere un'azione.

Per testare l'azione di convalida, eseguire una richiesta all'endpoint HTTP.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")   

