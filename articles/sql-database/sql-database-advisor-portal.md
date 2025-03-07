---
title: Applicare le raccomandazioni per le prestazioni - Database SQL di Azure | Microsoft Docs
description: Usare il portale di Azure per trovare raccomandazioni per le prestazioni che consentono di ottimizzare le prestazioni del database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b6bcb2650cd11ed97b6e81fda6aaafe7fca61202
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569595"
---
# <a name="find-and-apply-performance-recommendations"></a>Trovare e applicare raccomandazioni per le prestazioni

È possibile usare il portale di Azure per trovare raccomandazioni per le prestazioni che consentono di ottimizzare le prestazioni del database SQL di Azure o per correggere eventuali problemi individuati nel carico di lavoro. La pagina **Raccomandazioni per le prestazioni** nel portale di Azure consente di trovare le raccomandazioni principali in base all'impatto potenziale. 

## <a name="viewing-recommendations"></a>Visualizzazione delle raccomandazioni

Per visualizzare e applicare le raccomandazioni, sono necessarie le autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) corrette in Azure. Le autorizzazioni **Lettore** e **Collaboratore Database SQL** sono necessarie per visualizzare le raccomandazioni, mentre le autorizzazioni **Proprietario** e **Collaboratore Database SQL** sono necessarie per eseguire qualsiasi operazione, creare o eliminare indici e annullare la creazione di un indice.

Usare la procedura seguente per trovare raccomandazioni per le prestazioni nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare a **Tutti i servizi** > **Database SQL** e selezionare il database.
3. Fare clic su **Raccomandazione per le prestazioni** per visualizzare le raccomandazioni disponibili per il database selezionato.

Le raccomandazioni per le prestazioni vengono visualizzate in una tabella simile a quella illustrata nella figura seguente:

![Consigli](./media/sql-database-advisor-portal/recommendations.png)

Le raccomandazioni vengono ordinate in base all'impatto potenziale sulle prestazioni nelle categorie seguenti:

| Impatto | DESCRIZIONE |
|:--- |:--- |
| High |Le indicazioni ad alto impatto devono fornire l'impatto più significativo sulle prestazioni. |
| Medi |Le raccomandazioni a impatto medio devono migliorare le prestazioni, ma non sostanzialmente. |
| Basso |Le raccomandazioni a basso impatto devono offrire prestazioni migliori, ma i miglioramenti potrebbero non essere significativi. |


> [!NOTE]
> Il database SQL di Azure deve monitorare le attività almeno per un giorno per poter individuare alcune raccomandazioni. Il database SQL di Azure può ottimizzare più facilmente modelli di query coerenti anziché picchi irregolari casuali di attività. Se non sono disponibili raccomandazioni, nella pagina **Performance recommendation** (Raccomandazione prestazioni) viene visualizzato un messaggio che ne spiega il motivo.
> 

È anche possibile visualizzare lo stato delle ultime operazioni storiche. Selezionare un'indicazione o lo stato per visualizzare altre informazioni.

Di seguito è riportato un esempio della raccomandazione "Crea indice" nel portale di Azure.

![Crea indice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Applicazione delle raccomandazioni
Il database SQL di Azure offre il controllo completo sull'attivazione delle raccomandazioni tramite una delle tre opzioni seguenti: 

* Applicare le singole indicazioni una alla volta.
* Abilitare l'ottimizzazione automatica per applicare automaticamente le raccomandazioni.
* Per implementare una raccomandazione manualmente, eseguire lo script T-SQL consigliato nel database.

Selezionare una raccomandazione per visualizzarne i dettagli e quindi fare clic su **Visualizza script** per esaminare in dettaglio come viene creata la raccomandazione.

Il database rimane online mentre viene applicata la raccomandazione. Un database non viene mai portato offline per l'uso di una raccomandazione per le prestazioni o per l'ottimizzazione automatica.

### <a name="apply-an-individual-recommendation"></a>Applicare una singola indicazione
È possibile leggere e accettare le indicazioni una alla volta.

1. Nella pagina **Raccomandazioni** selezionare una raccomandazione.
2. Nella pagina **Dettagli** fare clic sul pulsante **Applica**.
   
    ![Applica suggerimento](./media/sql-database-advisor-portal/apply.png)

La raccomandazione selezionata verrà applicata nel database.

### <a name="removing-recommendations-from-the-list"></a>Rimozione delle raccomandazioni dall'elenco

Se l'elenco di raccomandazioni contiene voci che si vuole rimuovere dall'elenco, ignorare la raccomandazione:

1. Selezionare una raccomandazione nell'elenco **Raccomandazioni** per aprire i dettagli.
2. Fare clic su **Ignora** nella pagina **Dettagli**.

Se si vuole, è possibile aggiungere nuovamente gli elementi ignorati all'elenco **Raccomandazioni** :

1. Nella pagina **Raccomandazioni** fare clic su **Visualizza rimosse**.
2. Selezionare un elemento ignorato nell'elenco per visualizzarne i dettagli.
3. Facoltativamente, fare clic su **Annulla rimozione** per aggiungere nuovamente l'indice all'elenco principale di **Raccomandazioni**.

> [!NOTE]
> Se è abilitata l'[ottimizzazione automatica](sql-database-automatic-tuning.md) del database SQL e se una raccomandazione è stata rimossa manualmente dall'elenco, tale raccomandazione non verrà mai applicata automaticamente. La rimozione di una raccomandazione è un modo pratico per gli utenti di abilitare l'ottimizzazione automatica nei casi che richiedono che non venga applicata una raccomandazione specifica.
> Tale operazione può essere annullata aggiungendo nuovamente le raccomandazioni rimosse all'elenco selezionando l'opzione Annulla rimozione.
> 

### <a name="enable-automatic-tuning"></a>Abilitare l'ottimizzazione automatica
È possibile impostare il database SQL di Azure in modo che implementi automaticamente le raccomandazioni. Man mano che le raccomandazioni vengono rese disponibili, verranno applicate automaticamente. Come per tutte le raccomandazioni gestite dal servizio, se l'impatto sulle prestazioni è negativo le raccomandazioni verranno annullate.

1. Nella pagina **Raccomandazioni** fare clic su **Automatizza**:
   
    ![Impostazioni di Advisor](./media/sql-database-advisor-portal/settings.png)
2. Selezionare le azioni da automatizzare:
   
    ![Indici consigliati](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Si noti che l'opzione **DROP_INDEX** non è attualmente compatibile con le applicazioni che usano cambi di partizione e hint di indice. 
>

Dopo aver selezionato la configurazione desiderata, fare clic su Applica.

### <a name="manually-apply-recommendations-through-t-sql"></a>Applicare manualmente le raccomandazioni tramite T-SQL

Selezionare qualsiasi raccomandazione e quindi fare clic su **Visualizza script**. Eseguire questo script nel database per applicare manualmente l'indicazione.

*Gli indici eseguiti manualmente non vengono monitorati e convalidati per l'impatto sulle prestazioni da parte del servizio* , quindi è consigliabile monitorarli dopo la creazione per verificare che offrano miglioramenti delle prestazioni e modificarli o eliminarli se necessario. Per informazioni dettagliate sulla creazione di indici, vedere [CREAZIONE INDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Inoltre, le raccomandazioni applicate manualmente rimarranno attive e visualizzate nell'elenco di raccomandazioni per 24-48 ore. prima del ritiro automatico del sistema. Se si desidera rimuovere prima una raccomandazione, è possibile eliminarla manualmente.

### <a name="canceling-recommendations"></a>Annullamento delle raccomandazioni

Le raccomandazioni con stato **In sospeso**, **Convalida** oppure **Operazione completata** possono essere annullate. Le raccomandazioni con stato **In esecuzione** non possono essere annullate.

1. Selezionare una raccomandazione nell'area **Cronologia ottimizzazione** per aprire la pagina dei **dettagli della raccomandazione**.
2. Fare clic su **Annulla** per interrompere il processo di applicazione della raccomandazione.

## <a name="monitoring-operations"></a>Monitoraggio delle operazioni

L'applicazione di un'indicazione potrebbe non avvenire in tempo reale. Il portale fornisce dettagli sullo stato della raccomandazione. Di seguito sono indicati gli stati possibili di un indice:

| Stato | DESCRIZIONE |
|:--- |:--- |
| In sospeso |Il comando di applicazione della raccomandazione è stato ricevuto ed è pianificato per l'esecuzione. |
| In esecuzione |La raccomandazione viene applicata. |
| Convalida |La raccomandazione è stata applicata e il servizio sta valutando i vantaggi. |
| Riuscito |La raccomandazione è stata applicata e i vantaggi sono stati misurati. |
| Errore |Si è verificato un errore durante il processo di applicazione della raccomandazione. Può trattarsi di un problema temporaneo o eventualmente di una modifica dello schema della tabella e lo script non è più valido. |
| Ripristino |La raccomandazione è stata applicata, ma è stata considerata non efficiente e verrà ripristinata automaticamente. |
| Ripristinato |La raccomandazione è stata ripristinata. |

Fare clic su una raccomandazione in-process nell'elenco per visualizzare altre informazioni:

![Indici consigliati](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Ripristino di una raccomandazione
Se sono state usate le raccomandazioni per le prestazioni per applicare la raccomandazione, ovvero non è stato eseguito manualmente lo script T-SQL, la raccomandazione annulla automaticamente la modifica se viene rilevato un impatto negativo sulle prestazioni. Se per qualsiasi motivo si vuole semplicemente annullare una raccomandazione, procedere come segue:

1. Selezionare una raccomandazione applicata nell'area **Cronologia ottimizzazione** .
2. Fare clic su **Ripristina** nella pagina dei **dettagli della raccomandazione**.

![Indici consigliati](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitoraggio dell'impatto sulle prestazioni delle indicazioni relative agli indici
Dopo aver implementato correttamente le raccomandazioni, attualmente, solo raccomandazioni relative alle operazioni sugli indici e alle query con parametri, fare clic su **Informazioni dettagliate query** nella pagina dei dettagli delle raccomandazioni per aprire [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) ed esaminare l'impatto sulle prestazioni delle query principali.

![Monitorare l'impatto sulle prestazioni](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Riepilogo
Il database SQL di Azure fornisce raccomandazioni per migliorare le prestazioni del database SQL. Questa funzionalità offre script T-SQL e consente di ottimizzare il database e quindi di migliorare le prestazioni delle query.

## <a name="next-steps"></a>Passaggi successivi
Monitorare le raccomandazioni e continuare ad applicarle in modo da migliorare le prestazioni. I carichi di lavoro dei database sono dinamici e cambiano in modo continuo. Database SQL di Azure continua a monitorare e offrire raccomandazioni potenzialmente utili per migliorare le prestazioni del database. 

* Vedere [Ottimizzazione automatica](sql-database-automatic-tuning.md) per altre informazioni sull'ottimizzazione automatica nel database SQL di Azure.
* Vedere le [Raccomandazioni per le prestazioni](sql-database-advisor.md) per una panoramica delle raccomandazioni per le prestazioni per il database SQL di Azure.
* Vedere l'articolo [Informazioni dettagliate prestazioni query](sql-database-query-performance.md) per informazioni sull'analisi dell'impatto sulle prestazioni delle query principali.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Archivio query](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controllo degli accessi in base al ruolo](../role-based-access-control/overview.md)

