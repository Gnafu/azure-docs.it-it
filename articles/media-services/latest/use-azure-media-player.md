---
title: Riproduzione con Azure Media Player - Azure | Microsoft Docs
description: Questo argomento fornisce informazioni generali su Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: 6157f59d96770298d947a1cf6ca28aa343e1a0d4
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311829"
---
# <a name="azure-media-player-overview"></a>Panoramica di Azure Media Player

Azure Media Player è un lettore video Web progettato per consentire la riproduzione di contenuti multimediali da Servizi multimediali di Microsoft Azure su una vasta gamma di browser e dispositivi. Azure Media Player usa standard di settore come HTML5, MSE (Media Source Extensions) ed EME (Encrypted Media Extensions) per fornire un'esperienza di streaming adattivo completa. Se questi standard non sono disponibili in un dispositivo o in un browser, Azure Media Player usa una tecnologia di fallback come Flash o Silverlight. Indipendentemente dalla tecnologia di riproduzione usata, quindi, gli sviluppatori disporranno di un'interfaccia JavaScript unificata per accedere alle API. In questo modo, i contenuti distribuiti da Servizi multimediali di Azure potranno essere riprodotti su un'ampia gamma di dispositivi e browser senza alcuna operazione aggiuntiva.

Servizi multimediali di Microsoft Azure consente di distribuire contenuti nei formati di streaming HLS; DASH e Smooth Streaming per consentirne la riproduzione. Azure Media Player prende in considerazione tutti i vari formati e automaticamente riproduce il link migliore in base alle funzionalità della piattaforma o del browser. Servizi multimediali consente inoltre la crittografia dinamica degli asset con la crittografia PlayReady o la crittografia della busta AES a 128 bit. Anche Azure Media Player consente la decrittografia di contenuti crittografati con PlayReady o AES a 128 bit, se correttamente configurati. 

> [!NOTE]
> La riproduzione HTTPS è necessaria per il contenuto crittografato con Widevine.

[Avviare la versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>Usare la pagina demo di Azure Media Player

### <a name="start-using"></a>Iniziare l'utilizzo

È possibile usare la [pagina demo Azure Media Player](https://aka.ms/azuremediaplayer) per riprodurre esempi di Servizi multimediali di Azure nel proprio flusso.  

Per riprodurre un nuovo video, incollare un URL diverso e fare clic su **Update** (Aggiorna).

Per configurare diverse opzioni di riproduzione (ad esempio, tecnologia, lingua o crittografia), selezionare **Advanced Options** (opzioni avanzate).

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorare la diagnostica di un flusso video

È possibile usare la [pagina demo di Azure Media Player](https://aka.ms/azuremediaplayer) per monitorare la diagnostica di un flusso video. 

![Diagnostica di Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Configurare Azure Media Player nel codice HTML

Azure Media Player è semplice da configurare. Bastano pochi istanti per ottenere la riproduzione di base di contenuti multimediali dall'account Servizi multimediali. Vedere la [documentazione di Azure Media Player](https://aka.ms/ampdocs) per informazioni dettagliate su come installare e configurare Azure Media Player. 

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione di Azure Media Player](https://aka.ms/ampdocs)
- [Esempi di Azure Media Player](https://aka.ms/ampsamples)
