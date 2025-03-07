---
title: Streaming live con Servizi multimediali di Azure per creare flussi a più bitrate | Microsoft Docs
description: 'Questo argomento descrive come configurare un canale che riceve un flusso live a velocità in bit singola da un codificatore locale e quindi esegue la codifica live in un flusso a velocità in bit adattiva con Servizi multimediali. Il flusso può essere quindi distribuito alle applicazioni di riproduzione client tramite uno o più endpoint di streaming, usando uno dei protocolli di flusso adattivo seguenti: HLS, Smooth Streaming, MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: a828d03093c73d5c65a92ccf899fbaa1ef622bd6
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016497"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Streaming live con Servizi multimediali di Azure per creare flussi a più bitrate

> [!NOTE]
> A partire dal 12 maggio 2018 i canali live non supporteranno più il protocollo di inserimento del flusso di trasporto RTP/MPEG-2. Eseguire la migrazione da RTP/MPEG-2 ai protocolli di inserimento RTMP o MP4 frammentato (Smooth Streaming).

## <a name="overview"></a>Panoramica
In Servizi multimediali di Azure (AMS) un **canale** rappresenta una pipeline per l'elaborazione dei contenuti in streaming live. Un **canale** riceve i flussi di input live in uno dei due modi seguenti:

* Un codificatore live locale invia un flusso a bitrate singolo al canale abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei seguenti formati: RTMP o Smooth Streaming (MP4 frammentato). Il canale esegue quindi la codifica live del flusso in ingresso a velocità in bit singola in un flusso video a più velocità in bit (adattivo). Quando richiesto, Servizi multimediali invia il flusso ai clienti.
* Un codificatore live locale invia un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a bitrate multipli a un canale non abilitato per eseguire la codifica live con AMS. I flussi inseriti passano attraverso il **canale**senza altre elaborazioni. Questo metodo viene chiamato **pass-through**. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a bitrate multipli: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco ed Elemental. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek e codificatori Tricaster.  Un codificatore live può anche inviare un flusso a bitrate singolo a un canale non abilitato per la codifica live, ma questa operazione non è consigliata. Quando richiesto, Servizi multimediali invia il flusso ai clienti.

  > [!NOTE]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.
  > 
  > 

A partire dalla versione 2.10 di Servizi multimediali, quando si crea un canale è possibile specificare in che modo il canale riceverà il flusso di input e se eseguirà o meno la codifica live del flusso. Sono disponibili due opzioni:

* **Nessuno** : specificare questo valore se si prevede di usare un codificatore live locale che genera un flusso a più velocità in bit (un flusso pass-through). In questo caso, il flusso in ingresso viene passato all'output senza codifica. Questo è il comportamento di un canale prima della versione 2.10.  Per informazioni più dettagliate sull'uso dei canali di questo tipo, vedere [Streaming live con codificatori locali che creano flussi a bitrate multipli](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** - scegliere questo valore se si prevede di usare Servizi multimediali per codificare il flusso live a velocità in bit singola in un flusso a più velocità in bit. Tenere presente che la codifica live è soggetta a un costo e che se si lascia un canale di codifica live impostato sullo stato "In esecuzione", vengono aggiunti nuovi costi alla fatturazione.  Per evitare costi orari aggiuntivi, quindi, è consigliabile arrestare immediatamente i canali in esecuzione al termine dell'evento in streaming live.

> [!NOTE]
> Questo argomento illustra gli attributi dei canali abilitati per l'esecuzione della codifica live (tipo di codifica**Standard** ). Per informazioni sull'uso dei canali non abilitati all'esecuzione della codifica live, vedere [Live streaming with on-premise encoders that create multi-bitrate streams](media-services-live-streaming-with-onprem-encoders.md)(Streaming live con codificatori locali che creano flussi a velocità in bit multipla).
> 
> Assicurarsi di esaminare la sezione [Considerazioni](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Implicazioni relative alla fatturazione
Un canale di codifica live avvia la fatturazione non appena il suo stato viene impostato su "In esecuzione" tramite l'API.   È possibile visualizzare lo stato del canale nel portale di Azure o nello strumento Explorer di Servizi multimediali di Azure (https://aka.ms/amse).

La tabella seguente illustra il mapping degli stati del canale agli stati di fatturazione nell'API e nel portale di Azure. Gli stati sono leggermente diversi tra l'API e il portale. Non appena un canale viene impostato sullo stato "In esecuzione" tramite l'API o sullo stato "Pronto" o "Streaming" nel portale di Azure, viene attivata la fatturazione.
Per sospendere l'attività di fatturazione del canale, è necessario interrompere il canale tramite l'API o nel portale di Azure.
È l'utente ad essere responsabile dell'interruzione dei canali al termine dell'utilizzo del canale di codifica live.  La mancata interruzione del canale di codifica comporta infatti il proseguimento della fatturazione.

### <a id="states"></a>Stati del canale e relativi metodi di mapping alla modalità di fatturazione
Si tratta dello stato attuale del canale. I valori possibili sono:

* **Arrestato**. Lo stato iniziale del canale dopo la creazione (se nel portale non è stata selezionata l'opzione di avvio automatico.) In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
* **Avvio in corso**. È in corso l'avvio del canale. In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il canale torna allo stato Interrotto.
* **In esecuzione**. Il canale è in grado di elaborare flussi live. La fatturazione è ora attiva. È necessario interrompere il canale per sospendere la fatturazione. 
* **Arresto in corso**. È in corso l'interruzione del canale. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming.
* **Eliminazione in corso**. È in corso l'eliminazione del canale. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming.

La tabella seguente illustra il mapping degli stati del canale alla modalità di fatturazione. 

| Stato del canale | Indicatori dell'interfaccia utente del portale | Fatturazione? |
| --- | --- | --- |
| Avvio in corso |Avvio in corso |No (stato temporaneo) |
| In esecuzione |Pronto (nessun programma in esecuzione)<br/>Oppure<br/>Streaming (almeno un programma in esecuzione) |YES |
| Stopping |Stopping |No (stato temporaneo) |
| Arrestato |Arrestato |No |

### <a name="automatic-shut-off-for-unused-channels"></a>Spegnimento automatico per i canali non usati
A partire dal 25 gennaio 2016, Servizi multimediali ha distribuito un aggiornamento che interrompe automaticamente un canale (con la codifica live abilitata) dopo che è rimasto in esecuzione in stato di mancato utilizzo per un lungo periodo. Questa condizione si applica ai canali che non hanno programmi attivi e che non hanno ricevuto un feed di contributo di input per un lungo periodo di tempo.

La soglia per un periodo di mancato utilizzo nominalmente è 12 ore, ma è soggetta a modifiche.

## <a name="live-encoding-workflow"></a>Flusso di lavoro della codifica live
Il diagramma seguente rappresenta un flusso di lavoro di streaming live in cui un canale riceve un flusso a bitrate singolo in uno dei protocolli seguenti: RTMP o Smooth Streaming. Viene quindi eseguita la codifica de flusso in un flusso a bitrate multipli. 

![Flusso di lavoro live][live-overview]

## <a id="scenario"></a>Scenario comune di streaming live
Di seguito sono descritti i passaggi generali relativi alla creazione di applicazioni comuni di streaming live.

> [!NOTE]
> Attualmente, la durata massima consigliata per un evento live è 8 ore. Se è necessario eseguire un canale per una durata superiore, contattare amshelp@microsoft.com. La codifica live è soggetta a un costo e se si lascia un canale di codifica live impostato sullo stato "In esecuzione", vengono aggiunti nuovi costi alla fatturazione.  Per evitare costi orari aggiuntivi, quindi, è consigliabile arrestare immediatamente i canali in esecuzione al termine dell'evento in streaming live. 

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che può restituire un flusso a bitrate **singolo** in uno dei protocolli seguenti: RTMP o Smooth Streaming. 

    Questa operazione può essere eseguita anche dopo la creazione del canale.
2. Creare e avviare un canale. 
3. Recuperare l'URL di inserimento del canale. 

    L'URL di inserimento viene usato dal codificatore live per inviare il flusso al canale.
4. Recuperare l'URL di anteprima del canale. 

    Usare questo URL per verificare che il canale riceva correttamente il flusso live.
5. Creare un programma. 

    Se si crea un programma tramite il portale di Azure classico, viene creato anche un asset. 

    Se si usa .NET SDK o REST, è necessario creare un asset e specificarne l'uso quando si crea un programma. 
6. Pubblicare l'asset associato al programma.   

    >[!NOTE]
    >Quando l'account AMS viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. L'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**. 

7. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.
8. Facoltativamente, il codificatore live può ricevere il segnale per l'avvio di un annuncio. L'annuncio viene inserito nel flusso di output.
9. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.
10. Eliminare il programma e, facoltativamente, eliminare l'asset.   

> [!NOTE]
> È molto importante non dimenticare di arrestare un canale di codifica Live. La codifica live, infatti, è soggetta a un costo orario e se si lascia un canale di codifica live impostato sullo stato "In esecuzione", vengono aggiunti nuovi costi alla fatturazione.  Per evitare costi orari aggiuntivi, quindi, è consigliabile arrestare immediatamente i canali in esecuzione al termine dell'evento in streaming live. 
> 
> 

## <a id="channel"></a>Configurazioni di input (inserimento) del canale
### <a id="Ingest_Protocols"></a>Protocollo di streaming di inserimento
Se **Tipo di codificatore** è impostato su **Standard**, le opzioni valide sono le seguenti:

* **RTMP**
* **MP4 frammentato** (Smooth Streaming) a velocità in bit singola

#### <a id="single_bitrate_RTMP"></a>RTMP a velocità in bit singola
Considerazioni:

* Il flusso in ingresso non può contenere video a più velocità in bit.
* Il flusso video dovrà avere una velocità in bit media inferiore a 15 Mbps.
* Il flusso audio dovrà avere una velocità in bit media inferiore a 1 Mbps.
* Di seguito sono elencati i codec supportati:
* MPEG-4 AVC/H.264 Video
* Baseline, Main, High Profile (4:2:0 a 8 bit)
* High 10 Profile (4:2:0 a 10 bit)
* High 422 Profile (4:2:2 a 10 bit)
* MPEG-2 AAC-LC Audio
* Mono, Stereo, Surround (5.1, 7.1)
* Frequenza di campionamento a 44.1 kHz
* Stile MPEG-2, pacchetto ADTS
* I codec consigliati includono:
* Telestream Wirecast
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>MP4 frammentato (Smooth Streaming) a velocità in bit singola
Caso di utilizzo tipico:

Usare codificatori live locali di fornitori come Elemental Technologies, Ericsson, Ateme o Envivio per inviare il flusso di input tramite la rete Internet aperta a un data center di Azure nelle vicinanze.

Considerazioni:

Come per [RTMP a velocità in bit singola](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Altre considerazioni
* Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input.
* La risoluzione massima per il flusso video in ingresso è 1920x1080 e al massimo 60 campi/seconde se interlacciato o 30 fotogrammi/secondo se progressivo.

### <a name="ingest-urls-endpoints"></a>URL di inserimento (endpoint)
Un canale fornisce un endpoint di input (URL di inserimento) specificato dall'utente nel codificatore live, in modo che il codificatore possa eseguire il push dei flussi nei canali.

È possibile ottenere gli URL di inserimento dopo aver creato un canale. Per ottenere questi URL, non è necessario che il canale sia nello stato **In esecuzione** . Quando si è pronti per avviare l'esecuzione del push dei dati nel canale, questo dovrà essere nello stato **In esecuzione** . Una volta avviato l'inserimento di dati nel canale, è possibile visualizzare in anteprima il flusso tramite l'URL di anteprima.

È possibile inserire un flusso live MP4 frammentato (Smooth Streaming) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Attualmente AMS non supporta SSL con domini personalizzati.  

### <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti
È possibile definire gli indirizzi IP autorizzati a pubblicare video in questo canale. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo di indirizzi IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo di indirizzi IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').

Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

## <a name="channel-preview"></a>Anteprima del canale
### <a name="preview-urls"></a>URL di anteprima
I canali forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.

È possibile ottenere l'URL di anteprima quando si crea il canale. Per ottenere l'URL, non è necessario che il canale sia nello stato **In esecuzione** .

Una volta che il canale inizia a inserire i dati, è possibile visualizzare in anteprima il flusso.

> [!NOTE]
> Attualmente il flusso di anteprima può essere distribuito solo in formato MP4 frammentato (Smooth Streaming), indipendentemente dal tipo di input specificato.  Per visualizzare il flusso, è possibile usare un lettore ospitato nel portale di Azure.
> 
> 

### <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti
È possibile definire gli indirizzi IP autorizzati a connettersi all'endpoint di anteprima. Se non viene specificato alcun indirizzo IP, sarà consentito qualsiasi indirizzo IP. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo di indirizzi IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo di indirizzi IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio, '10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Impostazioni per la codifica live
Questa sezione descrive come è possibile modificare le impostazioni del codificatore live all'interno del canale, quando **Tipo di codifica** di un canale è impostato su **Standard**.

> [!NOTE]
> Il feed di contributi può contenere solo una singola traccia audio: l'inserimento di più tracce audio non è attualmente supportato. Quando si esegue la codifica live con [codifiche live locali](media-services-live-streaming-with-onprem-encoders.md), è possibile inviare un feed di contributi nel protocollo Smooth Streaming contenente più tracce audio.
> 
> 

### <a name="ad-marker-source"></a>Origine del marcatore di annunci
È possibile specificare l'origine per i segnali dei marcatori di annunci. Il valore predefinito è **API**. Indica che il codificatore live all'interno del canale deve restare in attesa di un'**API Ad Marker** asincrona.

### <a name="cea-708-closed-captions"></a>Sottotitoli codificati CEA-708
Flag facoltativo che indica al codificatore live di ignorare i dati di tutti i sottotitoli codificati CEA 708 incorporati nel video in ingresso. Quando il flag è impostato su false (impostazione predefinita), il codificatore rileva e reinserisce i dati CEA 708 nei flussi video di output.

#### <a name="index"></a>Indice
È consigliabile effettuare l'invio tramite un singolo flusso di trasporto del programma (SPTS). Se il flusso di input contiene più programmi, il codificatore live all'interno del canale analizza la tabella di mappa dei programmi (PMT, Program Map Table) nell'input, individua quindi gli input che presentano un nome del tipo di flusso MPEG-2 AAC ADTS, AC-3 System-A, AC-3 System-B, MPEG-2 Private PES, MPEG-1 Audio o MPEG-2 Audio e li dispone in base all'ordine specificato nella tabella di mappa dei programmi. L'indice in base zero viene quindi usato per selezionare la voce n in tale disposizione.

#### <a name="language"></a>Linguaggio
Identificatore lingua del flusso audio, conforme alla specifica ISO 639-2, ad esempio ITA. Se non è presente, il valore predefinito è UND (undefined).

### <a id="preset"></a>Set di impostazioni del sistema
Specifica il set di impostazioni che dovrà essere usato dal codificatore live all'interno del canale. Attualmente, l'unico valore consentito è **Default720p** (impostazione predefinita).

Si noti che se sono necessari set di impostazioni personalizzati, è amshelp@microsoft.comnecessario contattare.

Con **Default720p** il video sarà codificato nei 6 livelli seguenti.

#### <a name="output-video-stream"></a>Flusso video di output

| Velocità in bit | Larghezza | Altezza | MaxFPS | Profilo | Nome del flusso di output |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |High |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |High |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |High |Video_704x396_1350kbps |
| 850 |512 |288 |30 |High |Video_512x288_850kbps |
| 550 |384 |216 |30 |High |Video_384x216_550kbps |
| 200 |340 |192 |30 |High |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Flusso audio di output

L'audio viene codificato nel formato stereo AAC-LC a 128 kbps, con una frequenza di campionamento di 48 kHz.

## <a name="signaling-advertisements"></a>Segnalazione di annunci
Quando nel canale è abilitata la codifica live, nella pipeline è presente un componente che elabora il flusso video ed è in grado di modificarlo. È possibile impostare il segnale in modo che il canale inserisca slate e/o annunci nel flusso in uscita a velocità in bit adattiva. Gli slate sono immagini fisse che è possibile usare per coprire feed live di input in determinati casi (ad esempio durante un'interruzione pubblicitaria). I segnali pubblicitari sono segnali con sincronizzazione temporale incorporati nel flusso in uscita, per indicare al lettore video di eseguire un'azione speciale, ad esempio passare a un annuncio al momento opportuno. Per una panoramica del meccanismo di segnalazione SCTE-35 usato a questo scopo, vedere questo [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) . Di seguito è illustrato uno scenario tipico che è possibile implementare in un evento live.

1. Fare in modo che gli utenti ottengano un'immagine PRE-EVENTO prima dell'avvio dell'evento.
2. Fare in modo che gli utenti ottengano un'immagine POST-EVENTO al termine dell'evento.
3. Fare in modo che gli utenti ottengano un'immagine EVENTO-DI-ERRORE in caso di errore durante l'evento (ad esempio, interruzione dell'alimentazione allo stadio).
4. Inviare un'immagine INTERRUZIONE-PUBBLICITARIA per nascondere l'evento live durante un'interruzione pubblicitaria.

Di seguito sono elencate le proprietà che è possibile usare quando si inviano segnali pubblicitari. 

### <a name="duration"></a>Duration
Durata dell'interruzione pubblicitaria in secondi. Per avviare l'interruzione pubblicitaria, deve essere un valore positivo diverso da zero. Quando è in corso un'interruzione pubblicitaria e la durata è impostata su zero con ID battuta corrispondente all'interruzione pubblicitaria in corso, l'interruzione viene annullata.

### <a name="cueid"></a>ID battuta
ID univoco dell'interruzione pubblicitaria che dovrà essere usato dall'applicazione downstream per eseguire le operazioni necessarie. Deve essere un intero positivo. È possibile impostare questo valore su qualsiasi intero positivo casuale oppure usare un sistema upstream per tenere traccia degli ID battuta. Assicurarsi di normalizzare tutti gli ID in interi positivi prima dell'invio tramite l'API.

### <a name="show-slate"></a>Visualizza slate
facoltativo. Segnala al codificatore live di passare all'immagine dello [slate predefinito](media-services-manage-live-encoder-enabled-channels.md#default_slate) durante un'interruzione pubblicitaria e nascondere il feed video in ingresso. Durante la visualizzazione dello slate viene disattivato anche l'audio. Il valore predefinito è **false**. 

L'immagine usata sarà quella specificata tramite la proprietà ID asset dello slate predefinito al momento della creazione del canale. Lo slate verrà esteso per adattarsi alle dimensioni dell'immagine visualizzata. 

## <a name="insert-slate--images"></a>Inserire immagini dello slate
È possibile inviare un segnale al codificatore live all'interno del canale per passare a un'immagine dello slate. È anche possibile inviare un segnale perché venga terminato uno slate in corso. 

Il codificatore live può essere configurato per passare a un'immagine dello slate e nascondere il segnale video in ingresso in determinate situazioni, ad esempio, durante un'interruzione pubblicitaria. Se non è configurato uno slate, il video di input non viene mascherato durante l'interruzione pubblicitaria.

### <a name="duration"></a>Duration
Durata dello slate in secondi. Per avviare lo slate, deve essere un valore positivo diverso da zero. Se è in corso uno slate e viene specificata una durata pari a zero, lo slate in corso verrà terminato.

### <a name="insert-slate-on-ad-marker"></a>Inserisci slate su marcatore di annuncio
Quando è impostata su true, questa opzione configura il codificatore live in modo che venga inserita un'immagine dello slate durante un'interruzione pubblicitaria. Il valore predefinito è true. 

### <a id="default_slate"></a>ID asset dello slate predefinito

facoltativo. Specifica l'ID asset di Servizi multimediali che contiene l'immagine dello slate. Il valore predefinito è Null. 


> [!NOTE] 
> Prima di creare il canale, è necessario caricare l'immagine di slate con le limitazioni seguenti come asset dedicato, nel quale non dovranno essere presenti altri file. Questa immagine viene usata solo quando il codificatore live inserisce uno slate a causa di un'interruzione pubblicitaria o è stato segnalato in modo esplicito di inserire uno slate. Non è attualmente possibile usare un'immagine personalizzata quando il codificatore live passa a uno stato di perdita del segnale di input. È possibile votare per l'aggiunta di questa funzionalità [qui](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Risoluzione massima: 1920x1080.
* Dimensione massima: 3 MB.
* Il nome file deve avere una *.jpg extension.
* L'immagine deve essere caricata in un asset come unico AssetFile di tale asset e questo AssetFile deve essere contrassegnato come file primario. L'asset non può essere crittografato per l'archiviazione.

Se non si specifica l'**ID asset dello slate predefinito** e **Inserisci slate su marcatore di annuncio** è impostato su **true**, per nascondere il flusso video di input verrà usata un'immagine predefinita di Servizi multimediali di Azure. Durante la visualizzazione dello slate viene disattivato anche l'audio. 

## <a name="channels-programs"></a>Programmi del canale
Un canale è associato a programmi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il programma impostando il valore **Intervallo di archiviazione** . Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I programmi possono essere eseguiti per la quantità di tempo specificata, ma il contenuto che va oltre la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni programma è associato a un asset che archivia il contenuto trasmesso nel flusso. Un asset viene mappato a un contenitore BLOB in blocchi nell'account di archiviazione di Azure e i file nell'asset vengono archiviati come BLOB in tale contenitore. Per pubblicare il programma in modo che possa essere visualizzato dai clienti, è necessario creare un localizzatore OnDemand per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre programmi in esecuzione simultanea, in modo da poter creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di 6 ore di un programma e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due programmi in esecuzione contemporaneamente. Un programma è impostato per l'archiviazione di 6 ore dell'evento, ma non viene pubblicato. L'altro programma è impostato per l'archiviazione di 10 minuti e viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Creare e avviare invece un nuovo programma per ogni evento, come descritto nella sezione relativa alla programmazione di applicazioni Live Streaming.

Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento. 

Per eliminare il contenuto archiviato, arrestare ed eliminare il programma e quindi eliminare l'asset associato. Un asset non può essere eliminato se è usato da un programma. Per farlo, eliminare prima il programma. 

Anche dopo l'arresto e l'eliminazione del programma, gli utenti possono trasmettere in streaming il contenuto archiviato sotto forma di video on demand, fintanto che non si elimina l'asset.

Se si desidera mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Ottenere un'anteprima di un feed live
Quando è abilitata la codifica live, ora è possibile ottenere un'anteprima del feed live quando raggiunge il canale. Questo strumento può essere utile per verificare se il feed live sta effettivamente per raggiungere il canale. 

## <a id="states"></a>Stati del canale e come vengono mappati alla modalità di fatturazione
Si tratta dello stato attuale del canale. I valori possibili sono:

* **Arrestato**. Lo stato iniziale del canale dopo la creazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
* **Avvio in corso**. È in corso l'avvio del canale. Durante questo stato non sono consentiti aggiornamenti o streaming. Se si verifica un errore, il canale torna allo stato Interrotto.
* **In esecuzione**. Il canale è in grado di elaborare flussi live.
* **Arresto in corso**. È in corso l'interruzione del canale. In questo stato non è consentito alcun aggiornamento o streaming.
* **Eliminazione in corso**. È in corso l'eliminazione del canale. In questo stato non è consentito alcun aggiornamento o streaming.

La tabella seguente illustra il mapping degli stati del canale alla modalità di fatturazione. 

| Stato del canale | Indicatori dell'interfaccia utente del portale | Fatturato? |
| --- | --- | --- |
| Avvio in corso |Avvio in corso |No (stato temporaneo) |
| In esecuzione |Pronto (nessun programma in esecuzione)<br/>Oppure<br/>Streaming (almeno un programma in esecuzione) |SÌ |
| Stopping |Stopping |No (stato temporaneo) |
| Arrestato |Arrestato |No |

> [!NOTE]
> Attualmente, l'avvio del canale richiede in media 2 minuti ma può richiedere anche più di 20 minuti. La reimpostazione del canale può richiedere fino a 5 minuti.
> 
> 

## <a id="Considerations"></a>Considerazioni
* Quando un canale del tipo di codifica **Standard** riscontra una perdita di origine di input/contributo feed, compensa sostituendo l'audio/video di origine con uno slate di errore e inattività. Il canale continua a generare uno slate fino a quando l'input/contributo feed riprende il suo funzionamento. Si consiglia di non lasciare un canale live in tale stato per più di 2 ore. Dopo tale periodo, il comportamento del canale per la riconnessione dell’input non è garantito. Inoltre, il comportamento del canale non è in risposta a un comando di ripristino. È necessario interrompere il canale, eliminarlo e crearne uno nuovo.
* Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input.
* Assicurarsi di chiamare il metodo **Reset** sul canale ogni volta che si riconfigura il codificatore live. Prima di reimpostare il canale, è necessario arrestare il programma. Dopo aver reimpostato il canale, riavviare il programma.
* È possibile interrompere un canale solo quando si trova nello stato In esecuzione e tutti i programmi nel canale sono stati arrestati.
* Per impostazione predefinita, è possibile aggiungere solo cinque canali all'account di Servizi multimediali. Si tratta di una quota flessibile per tutti i nuovi account. Per altre informazioni, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
* Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input.
* Il costo viene addebitato solo quando il canale è nello stato **In esecuzione** . Per altre informazioni, vedere [questa](media-services-manage-live-encoder-enabled-channels.md#states) sezione.
* Attualmente, la durata massima consigliata per un evento live è 8 ore. Se è necessario eseguire un canale per una durata superiore, contattare amshelp@microsoft.com.
* Verificare che lo stato dell'endpoint di streaming da cui si vuole trasmettere il contenuto sia **In esecuzione**.
* Il set di impostazioni di codifica usa la nozione di "frequenza fotogrammi massima" di 30 fps. Pertanto, se l'input è 60 fps/59,94 i, i fotogrammi di input vengono eliminati/de-interlacciati a 30/29,97 fps. Se l'input è 50 fps/50 i, i fotogrammi di input vengono eliminati/de-interlacciati a 25 fps. Se l'input è 25 fps, l'output rimane di 25 fps.
* Non dimenticare di INTERROMPERE I CANALI al termine dell'operazione per evitare il proseguimento della fatturazione.

## <a name="known-issues"></a>Problemi noti
* I tempi di avvio del canale sono stati ridotti a una media di 2 minuti ma, in caso di aumento delle richieste, possono arrivare anche a oltre 20 minuti.
* Le immagini dello slate devono essere conformi alle limitazioni descritte [qui](media-services-manage-live-encoder-enabled-channels.md#default_slate). Se si tenta di creare un canale con uno slate predefinito con una risoluzione superiore a 1920x1080, la richiesta genererà un errore.
* Ancora una volta... per evitare il proseguimento della fatturazione.

## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Argomenti correlati
[Distribuzione di eventi Live Streaming con Servizi multimediali di Azure](media-services-overview.md)

[Creare canali che eseguono la codifica live da flusso a bitrate singolo a flusso a bitrate adattivo con il portale](media-services-portal-creating-live-encoder-enabled-channel.md)

[Creare canali che eseguono la codifica live da flusso a bitrate singolo a flusso a bitrate adattivo con .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Gestire i canali con l'API REST](https://docs.microsoft.com/rest/api/media/operations/channel)

[Concetti su Servizi multimediali di Azure](media-services-concepts.md)

[Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

