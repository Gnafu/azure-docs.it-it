---
title: File di inclusione
description: File di inclusione
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: fa88bf7f8db9b4fea63429dbfd18f84ee84ccda1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012177"
---
Il controllo degli accessi in base al ruolo è una strategia di sicurezza basata sull'ereditarietà per la gestione degli accessi, delle autorizzazioni e dei ruoli. I ruoli discendenti ereditano le autorizzazioni dai ruoli padre. Le autorizzazioni, tuttavia, possono essere assegnate anche se non sono state ereditate da un ruolo padre e possono essere assegnate anche per personalizzare un ruolo in base alle esigenze.

È possibile, ad esempio, che un amministratore dello spazio richieda l'accesso globale per poter eseguire qualsiasi operazione in uno spazio specifico. L'accesso include tutti i nodi sottostanti. Per un installatore di dispositivi possono essere sufficienti le autorizzazioni di *lettura* e *aggiornamento* per i dispositivi e i sensori.

In ogni caso, ai ruoli sono concesse le *autorizzazioni di accesso strettamente necessarie* per completare le attività assegnate in base al principio del privilegio minimo. In base a questo principio, a un'identità è concesso *solamente*:

* Il livello di accesso necessario per completare il processo assegnato.
* Un ruolo appropriato e limitato allo svolgimento del processo assegnato.

>[!IMPORTANT]
> Seguire sempre il principio del privilegio minimo.

Di seguito sono riportate altre due procedure importanti relative al controllo degli accessi in base al ruolo:

> [!div class="checklist"]
> * Controllare periodicamente le assegnazioni di ruolo per verificare che ogni ruolo disponga delle autorizzazioni corrette.
> * Pulire i ruoli e le assegnazioni in caso di variazione dei ruoli o delle assegnazioni degli utenti.