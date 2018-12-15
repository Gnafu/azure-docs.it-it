---
title: Sfruttare al meglio le funzionalità di Azure Application Insights | Documentazione Microsoft
description: Dopo l'introduzione a Application Insights, ecco un riepilogo delle funzionalità che è possibile esplorare.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 48232402ee091d6e41381f54cbfa3276cefc47ac
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682232"
---
# <a name="more-telemetry-from-application-insights"></a>Altri dati di telemetria da Application Insights
Dopo aver [aggiunto Application Insights al codice ASP.NET](app-insights-asp-net.md), è possibile eseguire alcune operazioni per ottenere altri dati di telemetria. 

| Azione | Risultato finale|
|---|---|
|(Server IIS) [Installare Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648) in ogni computer server.<br/>(App Web di Azure) Nel pannello di controllo di Azure per l'app Web aprire il pannello di Application Insights.| [**Contatori delle prestazioni**](app-insights-performance-counters.md)<br/>[**Eccezioni**](app-insights-asp-net-exceptions.md): analisi dello stack dettagliate<br/>[**Dipendenze**](app-insights-asp-net-dependencies.md)|
|[Aggiungere il frammento di codice JavaScript alle pagine web](app-insights-javascript.md)|[Prestazioni delle pagine](app-insights-usage-overview.md), eccezioni del browser, prestazioni AJAX. Telemetria personalizzata sul lato client.|
|[Creare test Web di disponibilità](app-insights-monitor-web-app-availability.md)|Ricevere avvisi se il sito diventa non disponibile|
|[Assicurarsi che buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) sia generato da MSBuild|[Creare annotazioni nei grafici di metriche](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Scrivere eventi e metriche personalizzati](app-insights-api-custom-events-metrics.md)|Conteggiare eventi aziendali e metriche, tenere traccia dell'uso dettagliato e altro ancora.|
|[Profilatura del sito live](https://aka.ms/AIProfilerPreview)|Intervalli di funzione dettagliati dall'app Web live|






