---
title: 'Esempio di criteri di Gestione API di Azure: inviare errori a Stackify per la registrazione | Microsoft Docs'
description: Esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e47a3736e814229e881a314c7cb05054369db1f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935247"
---
# <a name="send-errors-to-stackify-for-logging"></a>Inviare errori a Stackify per la registrazione

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come aggiungere criteri di registrazione degli errori per inviare gli errori a Stackify per la registrazione. Per impostare o modificare il codice dei criteri, seguire la procedura descritta in [Impostare o modificare un criterio](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-samples.md).

## <a name="policy"></a>Criterio

Incollare il codice nel blocco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-samples.md)

