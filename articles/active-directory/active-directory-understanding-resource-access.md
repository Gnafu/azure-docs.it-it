---
title: Informazioni sull'accesso alle risorse in Azure | Microsoft Docs
description: Questo argomento illustra i concetti relativi all'uso di amministratori della sottoscrizione per controllare l'accesso alle risorse nel portale di Azure completo.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.custom: it-pro;
ms.openlocfilehash: 621ebec898e5b345556832097b12ca9b54506e7c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="understanding-resource-access-in-azure"></a>Informazioni sull'accesso alle risorse in Azure

La fatturazione costituisce il punto di partenza per il controllo di accesso in Azure. Il proprietario di un account Azure, a cui si accede tramite il [Centro account di Azure](https://account.azure.com), è l'amministratore account. Le sottoscrizioni sono contenitori per la fatturazione, ma vengono usate anche come limiti per la sicurezza: ogni sottoscrizione ha un Amministratore del servizio (SA) che può aggiungere, rimuovere e modificare le risorse di Azure nella sottoscrizione tramite il [portale di Azure](https://portal.azure.com/). L'amministratore del servizio predefinito di una nuova sottoscrizione è l'amministratore dell'account, ma quest'ultimo può modificare l'amministratore del servizio nel Centro account di Azure.

<br><br>![Account Azure][1]

Le sottoscrizioni dispongono anche di un'associazione a una directory. Una directory definisce un set di utenti. Possono essere utenti dell'azienda o dell'istituto di istruzione che ha creato la directory o utenti guest esterni. Le sottoscrizioni sono accessibili da un sottogruppo di questi utenti della directory che sono stati nominati amministratori del servizio (SA) o co-amministratori (CA); l'unica eccezione è che, per motivi di compatibilità, gli account Microsoft (precedentemente Windows Live ID) possono essere nominati SA o CA senza essere presenti nella directory.

<br><br>![Controllo di accesso in Azure][2]

Le funzionalità disponibili nel portale di Azure consentono agli amministratori del servizio che hanno eseguito l'accesso tramite un account Microsoft di modificare la directory a cui è associata una sottoscrizione. Questa operazione può influire sul controllo di accesso di quella sottoscrizione.

<br><br>![Flusso di accesso dell'utente semplice][3]

Nel caso più semplice un'organizzazione, ad esempio Contoso, imporrà la fatturazione e il controllo di accesso nello stesso set di sottoscrizioni. Questo significa che la directory è associata alle sottoscrizioni di proprietà di un singolo account Azure. Dopo l'accesso al portale di Azure, gli utenti visualizzeranno due raccolte di risorse, illustrate in arancione nella figura precedente:

* Directory in cui è presente l'account utente (originato o aggiunto come entità esterna). Si noti che la directory usata per l'accesso non è rilevante ai fini di questo calcolo, di conseguenza verranno sempre visualizzate tutte le directory, indipendentemente da quella usata per l'accesso.
* Risorse che fanno parte delle sottoscrizioni associate alla directory usata per l'accesso E a cui l'utente può accedere (perché è amministratore dei servizi o coamministratore).

<br><br>![Utente con più sottoscrizioni e directory][4]

Gli utenti con sottoscrizioni in più directory possono cambiare il contesto corrente del portale di Azure con il filtro della sottoscrizione. Questo comporta un accesso separato a una directory diversa, operazione che viene eseguita automaticamente tramite l'accesso Single Sign-On.

Operazioni quali lo spostamento di risorse tra sottoscrizioni possono risultare più difficoltose a causa della visualizzazione a directory singola delle sottoscrizioni. Per eseguire il trasferimento di risorse, potrebbe essere necessario usare prima il comando **Modifica directory** nella pagina Sottoscrizioni di **Impostazioni** per associare le sottoscrizioni alla stessa directory.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../billing/billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png
