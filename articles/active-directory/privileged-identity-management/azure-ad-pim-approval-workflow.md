---
title: Approva o rifiuta le richieste per i ruoli di Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come approvare o negare le richieste per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a8d373a7e6edeaefd933e4f8ec8ee11e3c14ee
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804029"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Approva o rifiuta le richieste per i ruoli Azure AD in PIM

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile configurare i ruoli in modo da richiedere l'approvazione per l'attivazione e scegliere uno o più utenti o gruppi come responsabili approvazione delegati. I responsabili approvazione delegati hanno 24 ore di tempo per approvare le richieste. Se una richiesta non viene approvata entro 24 ore, l'utente idoneo deve inviare una nuova richiesta. L'intervallo di tempo di 24 ore per l'approvazione non è configurabile.

Attenersi alla procedura descritta in questo articolo per approvare o negare le richieste di Azure AD ruoli.

## <a name="view-pending-requests"></a>Visualizza richieste in sospeso

In qualità di responsabile approvazione delegata, si riceverà una notifica di posta elettronica quando una richiesta di ruolo Azure AD è in attesa dell'approvazione. È possibile visualizzare queste richieste in sospeso in PIM.

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Approva richieste**.

    ![Ruoli di Azure AD-approva le richieste](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    È possibile visualizzare un elenco delle richieste in attesa di approvazione.

## <a name="approve-requests"></a>Approva richieste

1. Selezionare le richieste da approvare e quindi fare clic su **Approva** per aprire il riquadro Approva le richieste selezionate.

    ![Approva l'elenco di richieste con l'opzione approva evidenziata](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Digitare un motivo nella casella **Motivo dell'approvazione**.

    ![Approva il riquadro delle richieste selezionate con un motivo dell'approvazione](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Fare clic su **Approve** (Approva).

    L'icona di stato verrà aggiornata per indicare l'approvazione.

    ![Approva il riquadro delle richieste selezionate dopo aver fatto clic sul pulsante approva](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Rifiutare le richieste

1. Selezionare le richieste da rifiutare e quindi fare clic su **Nega** per aprire il riquadro Rifiuta le richieste selezionate.

    ![Approva elenco richieste con l'opzione Nega evidenziata](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Digitare un motivo nella casella **Motivo del rifiuto**.

    ![Nega il riquadro delle richieste selezionate con un motivo di negazione](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Fare clic su **Nega**.

    L'icona di stato verrà aggiornata per indicare il rifiuto.

## <a name="next-steps"></a>Passaggi successivi

- [Notifiche tramite posta elettronica in PIM](pim-email-notifications.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
