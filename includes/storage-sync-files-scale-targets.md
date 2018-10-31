---
title: File di inclusione
description: File di inclusione
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: a29f1c4a625552dd958884c6a172bee470e61ca6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49312530"
---
| Risorsa | Destinazione | Limite rigido |
|----------|--------------|------------|
| Servizi di sincronizzazione archiviazione per sottoscrizione | 15 servizi di sincronizzazione archiviazione | No  |
| Gruppi di sincronizzazione per servizio di sincronizzazione archiviazione | 100 gruppi di sincronizzazione | Yes |
| Server registrati per servizio di sincronizzazione archiviazione | 99 server | Yes |
| Endpoint cloud per gruppo di sincronizzazione | 1 endpoint cloud | Yes |
| Endpoint server per gruppo di sincronizzazione | 50 endpoint server | No  |
| Endpoint server per server | 33-99 endpoint server | Sì, ma varia in base alla configurazione (CPU, memoria, volumi, varianza del file, numero di file e così via) |
| Dimensioni endpoint | 4 TiB | No  |
| Oggetti file system (directory e file) per gruppo di sincronizzazione | 25 milioni di oggetti | No  |
| Numero massimo di oggetti file system (directory e file) in una directory | 200.000 oggetti | Yes |
| Lunghezza massima del nome dell'oggetto (directory e file) | 255 caratteri | Yes |
| Dimensioni massime del descrittore di protezione (directory e file) dell'oggetto | 4 KiB | Yes |
| Dimensioni complete | 100 GiB | No  |
| Dimensioni minime per un file da rendere a livelli | 64 KiB | Yes |
| Sessioni di sincronizzazione simultanee | 2 sessioni di sincronizzazione attive per ogni processore o fino a 8 sessioni di sincronizzazione attive per ogni server | Yes |
