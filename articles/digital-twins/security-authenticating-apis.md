---
title: Informazioni sull'autenticazione delle API di Gemelli digitali di Azure | Microsoft Docs
description: Usare Gemelli digitali di Azure per eseguire la connessione e l'autenticazione alle API
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: lyhughes
ms.openlocfilehash: c0b4b6a13143f613bec64c8507f1726e2450be44
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815560"
---
# <a name="connect-and-authenticate-to-apis"></a>Connettersi alle API ed eseguire l'autenticazione

Gemelli digitali di Azure usa Azure Active Directory (Azure AD) per l'autenticazione degli utenti e la protezione delle applicazioni. Azure AD supporta l'autenticazione per un'ampia gamma di architetture moderne. Tutte le architetture sono basate sui protocolli standard OAuth 2.0 oppure OpenID Connect. Inoltre, gli sviluppatori possono usare Azure AD per compilare applicazioni a tenant singolo e line-of-business (LOB). Gli sviluppatori possono usare Azure AD anche per sviluppare applicazioni multi-tenant.

Per una panoramica di Azure AD, visitare la [pagina relativa ai concetti fondamentali](https://docs.microsoft.com/azure/active-directory/fundamentals/index) per guide dettagliate, informazioni sui concetti e progetti di avvio rapido.

Per integrare un'applicazione o un servizio con Azure AD, uno sviluppatore deve prima di tutto registrare l'applicazione con Azure AD. Per istruzioni dettagliate e gli screenshot, vedere [questo avvio rapido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

Azure AD supporta [cinque scenari applicativi principali](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types):

* Applicazione a pagina singola (SPA): Un utente deve accedere a un'applicazione a pagina singola protetta da Azure AD.
* Web browser all'applicazione Web: Un utente deve accedere a un'applicazione Web protetta da Azure AD.
* Da applicazione nativa ad API Web: Un'applicazione nativa in esecuzione su un telefono, tablet o PC deve autenticare un utente per ottenere risorse da un'API Web protetta da Azure AD.
* Da applicazione Web ad API Web: un'applicazione Web deve ottenere le risorse da un'API Web protetta da Azure AD.
* Da applicazione daemon o server ad API Web: Un'applicazione daemon o un'applicazione server senza interfaccia utente Web deve ottenere risorse da un'API Web protetta da Azure AD.

La libreria di autenticazione di Azure offre numerosi modi per acquisire i token di Active Directory. Per informazioni dettagliate sugli esempi di codice e libreria, vedere [questo articolo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Chiamare Gemelli digitali da un'API Web di livello intermedio

Quando progettano soluzioni per Gemelli digitali, in genere gli sviluppatori creano un'API o un'applicazione di livello intermedio. L'app o l'API chiama quindi l'API di Gemelli digitali downstream. Per supportare questa architettura di soluzione Web standard, assicurarsi che:

1. Gli utenti eseguano l'autenticazione con l'applicazione di livello intermedio

1. Durante l'autenticazione viene acquisito un token On-Behalf-Of di OAuth 2.0

1. Il token acquisito viene quindi usato per chiamare o eseguire l'autenticazione con API più a valle tramite il flusso On-Behalf-Of

Per istruzioni su come orchestrare il flusso on-behalf-of, vedere [Flusso On-Behalf-Of di OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). È anche possibile visualizzare esempi di codice in [Calling a downstream web API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/) (Chiamare un'API Web downstream).

## <a name="next-steps"></a>Passaggi successivi

Per configurare e testare Gemelli digitali di Azure usando il flusso di concessione implicita OAuth 2.0, leggere [Configurare Postman](./how-to-configure-postman.md).

Per informazioni sulla sicurezza di Gemelli digitali di Azure, vedere [Creare e gestire assegnazioni di ruolo](./security-create-manage-role-assignments.md).