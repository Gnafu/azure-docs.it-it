---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db05ce33a9fb602d6d48d1a1606f48a7fbde246e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889108"
---
| Identificatore limite | Limite | Commenti |
| --- | --- | --- |
| Numero massimo di credenziali dell'account di archiviazione |64 | |
| Numero massimo di contenitori di volumi |64 | |
| Numero massimo di volumi |255 | |
| Numero massimo di pianificazioni per ogni modello di larghezza di banda |168 |Una pianificazione per ogni ora, ogni giorno della settimana (24*7). |
| Dimensioni massime di un volume a livelli nei dispositivi fisici |64 TB per 8100 e 8600 |8100 e 8600 sono dispositivi fisici. |
| Dimensioni massime di un volume a livelli su dispositivi virtuali di Azure |30 TB per 8010  <br></br>  64 TB per 8020 |8010 e 8020 sono dispositivi virtuali di Azure che usano rispettivamente archiviazione Standard e Premium. |
| Dimensioni massime di un volume aggiunto in locale nei dispositivi fisici |9 TB per 8100  <br></br>  24 TB per 8600 |8100 e 8600 sono dispositivi fisici. |
| Numero massimo di connessioni iSCSI |512 | |
| Numero massimo di connessioni iSCSI dagli iniziatori |512 | |
| Numero massimo di record di controllo di accesso per dispositivo |64 | |
| Numero massimo di volumi per criteri di backup |24 | |
| Numero massimo di backup mantenuti per criteri di backup |64 | |
| Numero massimo di pianificazioni per criteri di backup |10 | |
| Numero massimo di snapshot di qualsiasi tipo che è possibile mantenere per volume |256 |Sono inclusi gli snapshot locali e quelli cloud. |
| Numero massimo di snapshot che possono essere presenti in qualsiasi dispositivo |10,000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per backup, ripristino o clonazione |16 |<ul><li>Se sono presenti più di 16 volumi, questi vengono elaborati in sequenza man mano che gli slot di elaborazione si rendono disponibili.</li><li>Non è possibile eseguire nuovi backup di un volume a livelli clonato o ripristinato fino a quando l'operazione non è completata. Tuttavia, per un volume locale, i backup sono consentiti dopo che il volume è online.</li></ul> |
| Ripristino e clonazione del tempo di ripristino per volumi a livelli |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino o clonazione, indipendentemente dalla dimensione del volume.</li><li>È possibile che inizialmente le prestazioni del volume siano più lente del normale, poiché la maggior parte dei dati e dei metadati si trova ancora nel cloud. Le prestazioni possono migliorare con il flusso dei dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. La velocità può dipendere dalla larghezza di banda di Internet nel cloud.</li><li>L'operazione di ripristino o clonazione è completa quando tutti i metadati sono sul dispositivo.</li><li>Non è possibile eseguire le operazioni di backup fino a quando l'operazione di ripristino o clonazione non è stata completata. |
| Ripristinare il tempo di ripristino per i volumi aggiunti in locale |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino, indipendentemente dalla dimensione del volume.</li><li>È possibile che inizialmente le prestazioni del volume siano più lente del normale, poiché la maggior parte dei dati e dei metadati si trova ancora nel cloud. Le prestazioni possono migliorare con il flusso dei dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. La velocità può dipendere dalla larghezza di banda di Internet nel cloud.</li><li>A differenza dei volumi a livelli, in caso di volumi aggiunti in locale, i dati del volume vengono anche scaricati localmente nel dispositivo. L'operazione di ripristino è completa quando tutti i dati del volume sono stati portati al dispositivo.</li><li>Le operazioni di ripristino possono essere lunghe e il tempo totale necessario per completare il ripristino dipende dalle dimensioni del volume locale con provisioning, la larghezza di banda di Internet e i dati esistenti nel dispositivo. Sono consentite operazioni di backup nel volume aggiunto in locale  mentre è in corso l'operazione di ripristino. |
| Disponibilità del ripristino di tipo thin |Ultimo failover | |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello SSD)* |920/720 MB/s con una singola interfaccia di rete 10GbE |Fino a 2x con MPIO e due interfacce di rete. |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello HDD)* |120/250 MB/s | |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello Cloud)* |11/41 MB/s |La velocità effettiva di lettura dipende dai client che generano e gestiscono una profondità della coda I/O sufficiente. |

&#42; La velocità effettiva massima per ciascun tipo di I/O è stata misurata con scenari di scrittura al 100% e scenari di lettura al 100%. La velocità effettiva potrebbe essere inferiore a seconda delle condizioni della rete e della combinazione I/O.

