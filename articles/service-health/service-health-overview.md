---
title: Panoramica di integrità dei servizi di Azure | Microsoft Docs
description: Informazioni personalizzate su come le app di Azure sono interessate dalla manutenzione e dai problemi attuali e futuri dei servizi di Azure.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: a6f6288aebe599db794a7f15fc733563d26c66cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067104"
---
# <a name="service-health-overview"></a>Panoramica dell'integrità del servizio

L’Integrità del servizio offre un dashboard personalizzabile che registra l'integrità dei servizi di Azure nelle regioni in cui si usano. In questo dashboard è possibile rilevare gli eventi attivi, come i problemi del servizio in corso, le manutenzioni pianificate o gli avvisi di integrità pertinenti. Quando gli eventi diventano inattivi, vengono posizionati nella cronologia di integrità fino a 90 giorni. Infine, è possibile utilizzare il dashboard di integrità dei servizi per creare e gestire gli avvisi di integrità che notificano in modo proattivo quando i problemi del servizio ne compromettono le prestazioni.

## <a name="service-health-events"></a>Eventi di Integrità dei servizi

Integrità dei servizi registra tre tipi di eventi di integrità che possono influire sulle risorse:

1. **Problemi dei servizi**: problemi dei servizi di Azure che influiscono in questo momento. 
2. **Manutenzione pianificata**: manutenzione imminente che può influire sulla disponibilità dei servizi in futuro.  
3. **Avvisi sull'integrità**: modifiche apportate ai servizi di Azure che richiedono attenzione, ad esempio quando le funzionalità di Azure sono deprecate o si supera una quota di utilizzo.

> [!NOTE]
> Per visualizzare gli eventi di integrità dei servizi, gli utenti devono avere il ruolo con autorizzazioni di lettura per una sottoscrizione.

## <a name="get-started-with-service-health"></a>Introduzione a Integrità dei servizi

Per avviare il dashboard di Integrità dei servizi, selezionare il riquadro Integrità dei servizi nel dashboard del portale. Se in precedenza il riquadro è stato rimosso o si una un dashboard personalizzato, cercare il servizio Integrità dei servizi in "Altri servizi" in basso a sinistra nel dashboard.

![Introduzione a Integrità dei servizi](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Vedere i problemi che attualmente influiscono su servizi

La visualizzazione **Problemi del servizio** indica eventuali problemi presenti nei servizi di Azure che influiscono sulle risorse. È possibile capire quando è iniziato il problema e quali sono le aree e i servizi interessati. È anche possibile leggere l'aggiornamento più recente per comprendere che cosa sta facendo Azure per risolvere il problema. 

![Gestire il problema del servizio](./media/service-health-overview/azure-service-health-overview-2.png)

Scegliere la scheda **Impatto potenziale** per visualizzare l'elenco specifico delle risorse di cui si è proprietari che potrebbero essere interessate dal problema. È possibile scaricare un elenco in formato CSV delle risorse per condividerle con il team.

![Gestire il problema del servizio - Impatto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Ottenere i collegamenti e spiegazioni scaricabili 

È possibile ottenere un collegamento relativo al problema e usarlo nel sistema di gestione del problema. È possibile scaricare file PDF e in alcuni casi CSV da condividere con gli utenti che non hanno accesso al portale di Azure.   

![Gestire il problema del servizio - Gestione dei problemi](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Ricevere assistenza da Microsoft

Contattare il supporto tecnico se la risorsa rimane in uno stato insoddisfacente anche se il problema è stato risolto.  Usare i collegamenti del supporto tecnico nella parte destra della pagina.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Aggiungere una mappa dell'integrità personalizzata al dashboard

Filtrare Integrità dei servizi in modo da visualizzare le sottoscrizioni, le aree e i tipi di risorse cruciali per l'azienda. Salvare il filtro e aggiungere al dashboard del portale una mappa del mondo personalizzata per l'integrità. 

![Mappa di integrità con filtro personalizzato](./media/service-health-overview/azure-service-health-overview-6a.png)

![Aggiungere una mappa di integrità personalizzata](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurare gli avvisi di integrità del servizio

L’Integrità del servizio si integra con il Monitoraggio di Azure per l'invio di avvisi via posta elettronica, messaggi di testo e notifiche webhook nel caso in cui siano coinvolte risorse aziendali cruciali. Impostare un avviso di attività di log per l'evento di Integrità dei servizi appropriato. Indirizzare tale avviso alle persone appropriate nell'organizzazione usando i gruppi di azioni. Per altre informazioni, vedere [Configurare gli avvisi per Integrità dei servizi](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Passaggi successivi

Impostare gli avvisi per ricevere notifiche sui problemi di integrità. Per altre informazioni, vedere [procedure consigliate per la configurazione di avvisi sull'integrità del servizio Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
