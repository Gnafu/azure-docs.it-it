---
title: Indirizzi IP usati da Application Insights e Log Analytics | Microsoft Docs
description: Eccezioni del firewall del server necessarie per Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: mbullwin
ms.openlocfilehash: d0b3c6124af30cb9ad870ad5f2600495ab872698
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983661"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Indirizzi IP usati da Application Insights e Log Analytics
Il servizio [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) usa diversi indirizzi IP. Potrebbe essere necessario conoscere questi indirizzi se l'app che si sta monitorando è ospitata dietro un firewall.

> [!NOTE]
> Anche se questi indirizzi sono statici, a volte potrebbe essere necessari modificarli. Tutto il traffico di Application Insights rappresenta il traffico in uscita ad eccezione del monitoraggio della disponibilità e dei webhook che richiedono regole firewall in entrata.
> 
> 

> [!TIP]
> Eseguire la sottoscrizione a questa pagina come un RSS feed aggiungendo https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom al lettore RSS/ATOM preferito per ricevere notifiche delle modifiche più recenti.
> 
> 

## <a name="outgoing-ports"></a>Porte in uscita
È necessario aprire alcune porte in uscita nel firewall del server per consentire ad Application Insights SDK e/o a Status Monitor di inviare dati al portale:

| Scopo | URL | IP | Porte |
| --- | --- | --- | --- |
| Telemetria |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.162.117<br/>40.73.171.20<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248 | 443 |
| Flusso metriche attive |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>Monitoraggio stato
Configurazione di Status Monitor: necessaria solo quando si apportano modifiche.

| Scopo | URL | IP | Porte |
| --- | --- | --- | --- |
| Configurazione |`management.core.windows.net` | |`443` |
| Configurazione |`management.azure.com` | |`443` |
| Configurazione |`login.windows.net` | |`443` |
| Configurazione |`login.microsoftonline.com` | |`443` |
| Configurazione |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Configurazione |`auth.gfx.ms` | |`443` |
| Configurazione |`login.live.com` | |`443` |
| Installazione | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`,`dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Test di disponibilità
Questo è l'elenco di indirizzi da cui vengono eseguiti i [test Web della disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) . Se si vogliono eseguire test Web sull'app, ma il server Web è limitato alla fornitura di servizi a client specifici, è necessario consentire il traffico in ingresso dai server di test della disponibilità.

Aprire le porte 80 (http) e 443 (https) per il traffico in ingresso da questi indirizzi (gli indirizzi IP sono raggruppati per posizione):

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>API Application Insights
| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Documentazione API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Estensione delle annotazioni della pipeline di Azure |aigs1.aisvc.visualstudio.com |dynamic|443 |

## <a name="log-analytics-api"></a>API di Log Analytics

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Documentazione API |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Analytics in Application Insights

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Portale di Analisi | analytics.applicationinsights.io | dynamic | 80,443 |
| RETE CDN | applicationanalytics.azureedge.net | dynamic | 80,443 |
| Contenuti multimediali e rete CDN | applicationanalyticsmedia.azureedge.net | dynamic | 80,443 |

Nota: il dominio *.applicationinsights.io è di proprietà del team Application Insights.

## <a name="log-analytics-portal"></a>Portale di Log Analytics

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Portale | portal.loganalytics.io | dynamic | 80,443 |
| RETE CDN | applicationanalytics.azureedge.net | dynamic | 80,443 |

Nota: il dominio *.loganalytics.io è di proprietà del team di Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Estensione del portale di Azure Application Insights

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Estensione Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamic | 80,443 |
| Rete CDN estensione Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamic | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Rete CDN Application Insights JS SDK | az416426.vo.msecnd.net | dynamic | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | dynamic | 80,443 |

## <a name="alert-webhooks"></a>Webhook degli avvisi

| Scopo | IP | Porte
| --- | --- | --- |
| Creazione di avvisi | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profiler

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Agente | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| Portale | gateway.azureserviceprofiler.net | dynamic | 443
| Archiviazione | *.core.windows.net | dynamic | 443

## <a name="snapshot-debugger"></a>Debugger di snapshot

> [!NOTE]
> Profiler e Snapshot Debugger condividono lo stesso set di indirizzi IP.

| Scopo | URI | IP | Porte |
| --- | --- | --- | --- |
| Agente | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| Portale | ppe.gateway.azureserviceprofiler.net | dynamic | 443
| Archiviazione | *.core.windows.net | dynamic | 443
