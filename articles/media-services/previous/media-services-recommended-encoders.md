---
title: Informazioni sui codificatori consigliati da Servizi multimediali di Azure | Microsoft Docs
description: Informazioni sui codificatori consigliati dai servizi multimediali
services: media-services
keywords: codifica; codificatori; media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: fc481129e652c6dacd15a5a6d039a9118393e8f1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854253"
---
# <a name="recommended-on-premises-encoders"></a>Codificatori locali consigliati
Quando si esegue lo streaming live con Servizi multimediali di Azure è possibile specificare in che modo si vuole che il canale riceva il flusso di input. Se si sceglie di usare un codificatore locale con un canale di codifica live, il codificatore deve eseguire il push di un flusso a bitrate singolo di alta qualità come output. Se si sceglie di usare un codificatore locale con un canale pass-through, il codificatore deve eseguire il push di un flusso a bitrate multipli come output con tutte le qualità di output desiderate. Per altre informazioni, vedere [streaming live con codificatori locali](media-services-live-streaming-with-onprem-encoders.md).

Servizi multimediali di Azure consiglia di usare uno dei codificatori live seguenti che hanno RTMP come output:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Servizi multimediali di Azure consiglia di usare uno dei codificatori live seguenti che hanno MP4 frammentato (Smooth Streaming) a bitrate multipli come output:
- Media Excel Hero Live e Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Un codificatore live può inviare un flusso a bitrate singolo a un canale pass-through, ma questa configurazione non è consigliata perché non è consentito per lo streaming a bitrate adattivo nel client.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Come diventare un partner del codificatore locale
Come partner del codificatore locale di servizi multimediali di Azure, servizi multimediali promuove il prodotto consigliando il codificatore ai clienti aziendali. Per diventare un partner del codificatore locale, è necessario verificare la compatibilità del codificatore locale con servizi multimediali. A questo scopo, completare le verifiche seguenti:

Verifica del canale pass-through
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale **pass-through**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato
5. Eseguire il codificatore live per circa 10 minuti
6. Arrestare l'evento live
7. Creare, avviare un endpoint di streaming, usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer) per guardare l'asset archiviato e assicurarsi che la riproduzione non presenti anomalie visibili a livello di qualità. In alternativa guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live prima di eseguire il passaggio 6.
8. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live
9. Reimpostare lo stato del canale dopo la creazione di ogni esempio
10. Ripetere i passaggi da 3 a 9 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni annunci/sottotitoli/diverse velocità di codifica)

Verifica del canale di codifica live
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale di **codifica live**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate singolo.
4. Creare un evento live pubblicato
5. Eseguire il codificatore live per circa 10 minuti
6. Arrestare l'evento live
7. Creare, avviare un endpoint di streaming, usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer) per guardare l'asset archiviato e assicurarsi che la riproduzione non presenti anomalie visibili a livello di qualità. In alternativa guardare e convalidare l'asset usando l'URL di anteprima durante la sessione live prima di eseguire il passaggio 6.
8. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live
9. Reimpostare lo stato del canale dopo la creazione di ogni esempio
10. Ripetere i passaggi da 3 a 9 per tutte le configurazioni supportate dal codificatore (con e senza segnalazioni annunci/sottotitoli/diverse velocità di codifica)

Verifica della durata
1. Creare o visitare l'account di Servizi multimediali di Azure
2. Creare e avviare un canale **pass-through**
3. Configurare il codificatore per eseguire il push di un flusso live a bitrate multipli.
4. Creare un evento live pubblicato
5. Eseguire un codificatore live per almeno una settimana
6. Usare un lettore, ad esempio [Azure Media Player](https://aka.ms/azuremediaplayer) per guardare di volta in volta lo streaming live (o l'asset archiviato) e assicurarsi che la riproduzione non presenti anomalie visibili
7. Arrestare l'evento live
8. Registrare l'ID risorsa, l'URL di streaming pubblicato per l'archivio live e le impostazioni e la versione usate dal codificatore live

Infine inviare le impostazioni registrate e i parametri di archiviazione live a Servizi multimediali all'indirizzo e-mail amsstreaming@microsoft.com. Al momento della ricezione, Servizi multimediali esegue dei test di verifica sui campioni dal codificatore live. Per qualsiasi domanda su questo processo, contattare Servizi multimediali.
