---
title: File di inclusione
description: File di inclusione
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 421e88374a4ca03311fa77a6568a676aa9ffafa5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "70919692"
---
Ecco le limitazioni d'utilizzo e altri limiti di servizio per il servizio Azure Active Directory (Azure AD).

| Category | Limiti |
| --- | --- |
| Directory | Un utente singolo può appartenere a un massimo di 500 directory di Azure AD come membro o guest.<br/>Un utente singolo può creare un massimo di 20 directory. |
| Domini | È possibile aggiungere non più di 900 nomi di dominio gestito. Se si configurano tutti i domini per la federazione con l'istanza locale di Active Directory, è possibile aggiungere non più di 450 nomi di dominio in ogni directory. |
| Oggetti |<ul><li>Per impostazione predefinita, gli utenti dell'edizione gratuita di Azure Active Directory possono creare fino a 50.000 oggetti in una singola directory. Se è disponibile almeno un dominio verificato, la quota predefinita del servizio directory in Azure AD viene estesa a 300.000 oggetti. </li><li>Un utente non amministratore può creare al massimo 250 oggetti. I n questa quota vengono conteggiati sia gli oggetti attivi che gli oggetti eliminati disponibili per il ripristino. Solo gli oggetti eliminati da meno di 30 giorni sono disponibili per il ripristino. Gli oggetti eliminati che non sono più disponibili per il ripristino vengono conteggiati in questa quota con un valore di un quarto per 30 giorni. È possibile [assegnare un ruolo di amministratore](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) a utenti senza privilegi di amministratore che possono superare ripetutamente questa quota nel corso delle normali attività.</li></ul> |
| Estensioni dello schema |<ul><li>Le estensioni di tipo stringa possono contenere un massimo di 256 caratteri. </li><li>Le estensioni di tipo binario sono limitate a 256 byte.</li><li>Solo 100 valori di estensione, tra *tutti* i tipi e *tutte* le applicazioni, possono essere scritti in un singolo oggetto.</li><li>Solo le entità User, Group, TenantDetail, Device, Application e ServicePrincipal possono essere estese con gli attributi a valore singolo di tipo stringa o di tipo binario.</li><li>Le estensioni dello schema sono disponibili solo nella versione di anteprima dell'API Graph 1.21. All'applicazione deve essere concesso l'accesso in scrittura per registrare un'estensione.</li></ul> |
| Applicazioni |Al massimo 100 utenti possono essere proprietari di una singola applicazione. |
| Gruppi |<ul><li>Al massimo 100 utenti possono essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membro di un singolo gruppo.</li><li>Un utente può essere un membro di un numero qualsiasi di gruppi.</li><li>Il numero di membri in un gruppo che è possibile sincronizzare tra l'istanza locale di Active Directory e Azure Active Directory usando Azure AD Connect è limitato a 50.000.</li></ul> |
| Proxy dell'applicazione | <ul><li>Un massimo di 500 transazioni al secondo per ogni applicazione proxy app</li><li>Un massimo di 750 transazioni al secondo per il tenant</li></ul><br/>Una transazione viene definita come una singola richiesta e risposta http per una risorsa univoca. Quando la limitazione è limitata, i client riceveranno una risposta 429 (troppe richieste). |
| Pannello di accesso |<ul><li>Non sono previsti limiti al numero di applicazioni che possono essere visualizzate nel Pannello di accesso per ogni utente. Questo vale per gli utenti a cui sono state assegnate licenze per Azure AD Premium o Enterprise Mobility Suite.</li><li>Nel Pannello di accesso è possibile visualizzare un massimo di 10 riquadri di app per ogni utente. Questo limite si applica agli utenti a cui sono assegnate licenze per Azure AD Free piano di licenze. Esempi di riquadri di app includono Box, Salesforce o Dropbox. Questo limite non si applica agli account di amministratore.</li></ul> |
| Report | È possibile visualizzare o scaricare in qualsiasi report un massimo di 1000 righe. Eventuali dati aggiuntivi vengono troncati. |
| Unità amministrative | Un oggetto può appartenere al massimo a 30 unità amministrative. |
| Autorizzazioni e ruoli amministrativi | <ul><li>Un gruppo non può essere aggiunto come [proprietario](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Non è possibile assegnare un gruppo a un [ruolo](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>La capacità degli utenti di leggere le informazioni di directory degli altri utenti non può essere limitata al di fuori del commutere a livello di tenant per disabilitare tutti gli accessi degli utenti non amministratori a tutte le informazioni di directory (scelta non consigliata). Altre informazioni sulle autorizzazioni predefinite sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Potrebbero essere necessari fino a 15 minuti o la disconnessione e l'accesso prima dell'aggiunta e della revoca delle appartenenze ai ruoli amministrativi.</li></ul> |
