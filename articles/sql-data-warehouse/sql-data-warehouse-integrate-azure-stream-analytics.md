---
title: Usare Analisi di flusso di Azure con SQL Data Warehouse | Documentazione Microsoft
description: Suggerimenti per l'uso di Analisi di flusso di Azure con Azure SQL Data Warehouse per lo sviluppo di soluzioni.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 605598d1470cbb535d626c15a5e8e4e08aa4d571
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883815"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usare Analisi di flusso di Azure con SQL Data Warehouse
Analisi di flusso di Azure è un servizio completamente gestito che consente l'elaborazione di eventi complessi con bassa latenza, elevata disponibilità e scalabilità per lo streaming di dati nel cloud. Per informazioni di base, vedere [Introduzione ad Analisi di flusso di Azure][Introduction to Azure Stream Analytics]. È possibile apprendere come creare una soluzione end-to-end con Analisi di flusso seguendo l'esercitazione [Introduzione all'uso di Analisi di flusso di Azure][Get started using Azure Stream Analytics].

In questo articolo si apprenderà come usare il database di Azure SQL Data Warehouse come un sink di output per i processi di Analisi di flusso.

## <a name="prerequisites"></a>Prerequisiti
Eseguire prima di tutto questa procedura nell'esercitazione [Introduzione all'uso di Analisi di flusso di Azure][Get started using Azure Stream Analytics].  

1. Creare un input dell'hub eventi
2. Configurare e avviare l'applicazione di generazione di eventi
3. Eseguire il provisioning di un processo di Analisi dei flussi
4. Specificare la query e l'input del processo

Creare quindi un database di Azure SQL Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Specificare l'output del processo: Database di Azure SQL Data Warehouse
### <a name="step-1"></a>Passaggio 1
Nel processo di Analisi di flusso fare clic su **OUTPUT** nella parte superiore della pagina e quindi scegliere **AGGIUNGI OUTPUT**.

### <a name="step-2"></a>Passaggio 2
Selezionare il Database SQL e fare clic su Avanti.

![][add-output]

### <a name="step-3"></a>Passaggio 3
Immettere i valori seguenti nella pagina successiva:

* *Alias di output*: immettere un nome descrittivo per l'output del processo.
* *Sottoscrizione*
  * Se il database SQL Data Warehouse esiste nella stessa sottoscrizione del processo di analisi di flusso, selezionare Usare il database SQL dalla sottoscrizione corrente.
  * Se il database è in una sottoscrizione diversa, selezionare Usare il database SQL da un'altra sottoscrizione.
* *Database*: specificare il nome di un database di destinazione.
* *Nome server*: specificare il nome del server per il database specificato. Per trovarlo, è possibile usare il portale di Azure.

![][server-name]

* *Nome utente*: specificare il nome utente di un account con autorizzazioni di scrittura nel database.
* *Password*: fornire la password per l'account utente specificato.
* *Tabella*: specificare il nome della tabella di destinazione nel database.

![][add-database]

### <a name="step-4"></a>Passaggio 4
Fare clic sul pulsante con il segno di spunta per aggiungere l'output del processo e per verificare che Analisi di flusso possa connettersi al database.

![][test-connection]

Quando la connessione al database riesce, verrà visualizzata una notifica nella parte inferiore del portale. È possibile scegliere Test connessione nella parte inferiore per testare la connessione al database.

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica dell'integrazione, vedere [Panoramica dell'integrazione di SQL Data Warehouse][SQL Data Warehouse integration overview].

Per altri suggerimenti sullo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
