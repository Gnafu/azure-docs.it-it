---
title: Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure
description: La nuova esperienza unificata gli avvisi di Azure consente di creare, visualizzare e gestire la metrica e le regole di avviso del log da un'unica posizione.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a913075c051c6b784495917b7edbd7340254a212
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952631"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Questo articolo illustra come configurare gli avvisi tramite la nuova interfaccia degli avvisi nel portale di Azure. La definizione di una regola di avviso è costituita da tre parti:
- Destinazione: risorsa specifica di Azure da monitorare
- Criteri: condizione specifica o logica che, se rilevata nel segnale, deve attivare l'azione
- Azione: chiamata specifica inviata a un ricevitore di una notifica di posta elettronica, SMS, webhook e così via.

Avvisi di Azure fornisce anche una visualizzazione unificata per tutte le regole di avviso e la possibilità di gestirle in un'unica posizione. È inclusa la visualizzazione di tutti gli avvisi non risolti. Altre informazioni sulle funzionalità sono disponibili in [Avvisi di Azure - Panoramica](monitoring-overview-unified-alerts.md).

In Avvisi il termine **avvisi del log** viene usato per descrivere gli avvisi in cui il segnale è basato su una query personalizzata in [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) o [Application Insights](../application-insights/app-insights-analytics.md). La [nuova funzionalità di avviso della metrica](monitoring-near-real-time-metric-alerts.md) fornisce avvisi relativi alle [metriche multidimensionali](monitoring-metric-charts.md) per specifiche risorse di Azure. Gli avvisi per tali risorse possono usare filtri aggiuntivi sulle dimensioni creando **avvisi di metrica multidimensionale**.


> [!NOTE]
> Anche se Avvisi di Azure offre un'esperienza nuova e migliorata per la creazione di avvisi in Azure, l'esperienza di [avvisi (classica)](monitoring-overview-alerts.md) esistente rimane utilizzabile.
>

Di seguito è riportata una guida dettagliata per l'uso di Avvisi di Azure.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Creare una regola di avviso con il portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.  
    ![Monitoraggio](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Selezionare il pulsante **Nuova regola di avviso** per creare un nuovo avviso in Azure.
    ![Aggiungi avviso](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. Viene visualizzata la sezione Crea avviso composta da tre parti: *Definire la condizione dell'avviso*, *Definire i dettagli dell'avviso* e *Definire il gruppo di azioni*.

    ![Creare una regola](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definire la condizione dell'avviso tramite il collegamento **Seleziona risorsa** e specificare la destinazione selezionando una risorsa. Filtrare scegliendo la *Sottoscrizione*, il *Tipo di risorsa e infine la *Risorsa* necessaria.

    >[!NOTE]

    > Prima di procedere, verificare i segnali disponibili per la risorsa selezionata.

    ![Selezionare una risorsa](./media/monitor-alerts-unified/Alert-SelectResource.png)

 L'interfaccia utente consente di creare diversi tipi di avvisi da un'unica posizione. In base al tipo di avviso desiderato scegliere il passaggio successivo:

    - Per gli **avvisi metrica** seguire i passaggi da 5 a 7, quindi andare direttamente al passaggio 11
    - Per gli **avvisi del log**, andare al passaggio 8.

    > [!NOTE]

    > Sono supportati anche gli avvisi del log attività, ma sono in versione di anteprima. [Altre informazioni](monitoring-activity-log-alerts-new-experience.md).

5. *Avvisi delle metriche*: verificare che **Tipo di risorsa** sia selezionato con tipo di segnale **Metrica**, quindi, una volta selezionata la **risorsa** appropriata, fare clic sul pulsante *Operazione completata* per tornare alla finestra Crea avviso. Usare quindi il pulsante **Aggiungi criteri** per selezionare il segnale specifico nell'elenco delle opzioni di segnale, il servizio di monitoraggio e il tipo elencato disponibili per la risorsa selezionata in precedenza.

    ![Selezionare una risorsa](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Tutte le risorse che supportano [avvisi near real time](monitoring-near-real-time-metric-alerts.md) sono elencate con il servizio di monitoraggio **Piattaforma** e tipo di segnale **Metrica**

6. *Avvisi metrica*: dopo avere selezionato il segnale, è possibile dichiarare la logica per gli avvisi. I dati cronologici del segnale vengono visualizzati per riferimento con un'opzione per modificare l'intervallo di tempo tramite **Visualizza cronologia**, dalle ultime sei ore fino alla settimana precedente. Con la visualizzazione impostata, è possibile selezionare **Logica avvisi** dalle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Come anteprima della logica impostata, nella visualizzazione vengono mostrate la condizione e la cronologia del segnale, per indicare quando verrebbe attivato l'avviso. Specificare infine per quanto tempo l'avviso deve cercare la condizione specificata scegliendo l'opzione desiderata in **Periodo** e la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.

    ![Configurare la logica del segnale per la metrica](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Avvisi metrica*: se il segnale è una metrica, la finestra degli avvisi può essere filtrata tramite più punti dati o dimensioni per la risorsa di Azure specifica. 

    a. Scegliere una durata dall'elenco a discesa **Mostra cronologia** per visualizzare un periodo temporale diverso. È possibile selezionare le dimensioni per la metrica supportata per filtrare in base a una serie temporale. La scelta delle dimensioni è facoltativa e possono essere usate fino a cinque dimensioni. 

    b. È possibile selezionare **Logica avvisi** dalle opzioni visualizzate: *Condizione*, *Aggregazione e *Soglia*. Come anteprima della logica impostata, nella visualizzazione vengono mostrate la condizione e la cronologia del segnale, per indicare quando l'avviso sarebbe stato attivato in passato. 

    c. Per specificare la durata, scegliere **Periodo** insieme alla frequenza di esecuzione dell'avviso selezionando **Frequenza**.

    ![Configurare la logica del segnale per la metrica multidimensionale](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Avvisi del log*: verificare che **Tipo di risorsa** sia un'origine dati di analisi come *Log Analytics* o *Application Insights* e tipo di segnale **Log** quindi, dopo aver selezionato la **risorsa** appropriata, fare clic su *Operazione completata*. Usare quindi il pulsante **Aggiungi criteri** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa e nell'elenco dei segnali selezionare l'opzione di **ricerca log personalizzata** per il servizio di monitoraggio del log scelto, ad esempio *Log Analytics* o *Application Insights*.

   ![Selezionare una risorsa - ricerca log personalizzata](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Avvisi può importare query di Analytics come tipo di segnale **Log (query salvata)**, come illustrato nella figura precedente. Gli utenti possono quindi perfezionare la query in Analytics e salvarla per uso futuri in Avvisi. Altri dettagli sull'uso del salvataggio di query sono disponibili in [Usare la ricerca log in Log Analytics](../log-analytics/log-analytics-log-searches.md) o in [Query condivisa in Analytics in Application Insights](../log-analytics/log-analytics-overview.md). 

9.  *Avvisi del log*: una volta selezionata, la query per gli avvisi può essere immessa nel campo **Query di ricerca**. Se la sintassi della query non è corretta, nel campo viene visualizzato un errore in ROSSO. Se la sintassi della query è corretta, i dati cronologici della query specificata vengono visualizzati sotto forma di grafico come riferimento con un'opzione per modificare l'intervallo di tempo dalle ultime sei ore alla settimana precedente.

 ![Configurare una regola di avviso](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > La visualizzazione dei dati cronologici è possibile solo se i risultati della query includono dettagli temporali. Se la query restituisce dati di riepilogo o specifici valori di colonna, questi dati vengono visualizzati come tracciato singolo.

    >  Per il tipo Unità di misura della metrica degli avvisi del log con Application insights, è possibile specificare la variabile specifica per raggruppare i dati con l'opzione **Aggrega in base a**, come illustrato di seguito:

    ![opzione aggrega in base a](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Avvisi del log*: con la visualizzazione impostata, è possibile selezionare in **Logica avvisi** una delle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Specificare l'ora in cui valutare la condizione specificata nella logica tramite l'opzione **Periodo**. Specificare la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.
Gli **avvisi del log** possono basarsi su:
   - *Numero di record*: viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
   - *Unità di misura della metrica*: viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* in base al valore scelto. Il numero di violazioni per un avviso è il numero di volte in cui viene superata la soglia nel periodo di tempo scelto. È possibile specificare le violazioni totali per qualsiasi combinazione di violazioni nei set di risultati o le violazioni consecutive necessarie che devono verificarsi in campioni consecutivi. Per altre informazioni, vedere [Avvisi del log e relativi tipi](monitor-alerts-unified-log.md).

    > [!TIP]
    > Al momento in Avvisi gli avvisi di ricerca log possono avere un *periodo* e un valore di *frequenza* in minuti personalizzati. I valori possono variare da 5 a 1440 minuti (24 ore). Pertanto, se si vuole che il periodo dell'avviso sia ad esempio di tre ore, convertirlo in minuti (180 minuti) prima dell'uso

11. Come secondo passaggio, definire un nome per l'avviso nel campo **Nome regola di avviso** insieme a una **Descrizione** con le specifiche per l'avviso e il valore di **Gravità** nelle opzioni disponibili. Questi dettagli vengono riutilizzati in tutti i messaggi di avviso, le notifiche o notifiche push eseguite da Monitoraggio di Azure. Inoltre, l'utente può scegliere di attivare immediatamente la regola di avviso durante la creazione attivando in modo appropriato l'opzione **Abilita regola alla creazione**.

    Solo per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive nei dettagli degli avvisi:

    - **Elimina avvisi**: quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, crea i record di avviso. L'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate.

        ![Elimina avvisi per gli avvisi del log](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Specificare un valore di eliminazione dell'avviso maggiore della frequenza di avviso per garantire che le notifiche vengano arrestate senza sovrapposizione

12. Come terzo e ultimo passaggio, specificare se un **gruppo di azioni** deve essere attivato per la regola di avviso quando viene soddisfatta la condizione dell'avviso. È possibile scegliere qualsiasi gruppo di azioni esistente con un avviso o creare un nuovo gruppo di azioni. In base al gruppo di azioni selezionato, quando viene attivato l'avviso, Azure eseguirà queste operazioni: invio di messaggi di posta elettronica, invio di SMS, chiamata di webhook, correzione dell'uso di runbook di Azure, invio di notifiche push allo strumento Gestione dei servizi IT e così via. Altre informazioni sui [gruppi di azioni](monitoring-action-groups.md).

    Per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive per eseguire l'override di quelle predefinite:

    - **Notifica di posta elettronica**: esegue l'override dell'*oggetto di posta elettronica* nel messaggio di posta elettronica inviato tramite il gruppo di azioni, se esistono una o più azioni di posta elettronica nel gruppo di azioni. Non è possibile modificare il corpo del messaggio e questo campo **non** è destinato agli indirizzi di posta elettronica.
    - **Includi payload JSON personalizzato**: esegue l'override del webhook JSON usato dai gruppi di azioni, se nel gruppo di azioni sono presenti una o più azioni webhook. Un utente può specificare il formato JSON da usare per tutti i webhook configurati nel gruppo di azioni associato. Per altre informazioni sui formati webhook, vedere [Azioni webhook per le regole di avviso relative ai log](monitor-alerts-unified-log-webhook.md). L'opzione Test del webhook viene offerta per controllare il formato e l'elaborazione in base alla destinazione usando il file JSON di esempio e ha unicamente scopo di **test**.

        ![Override dell'azione per gli avvisi del log](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > Per il funzionamento dell'opzione**Test del webhook**, l'endpoint deve supportare il servizio [Cross Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) e l'utente può usare il proxy CORS per evitare problemi dell'intestazione Access-Control-Allow-Origin

13. Se tutti i campi sono validi e hanno un segno di spunta verde, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso in Monitoraggio di Azure - Avvisi. Tutti gli avvisi possono essere visualizzati nella dashboard di Avvisi.

    ![Creazione di regole](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="view-your-alerts-in-azure-portal"></a>Visualizzare gli avvisi nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.  

2. Viene visualizzata la **dashboard di Avvisi**, in cui sono raccolti tutti gli avvisi di Azure visualizzati in un unico riquadro, ![Alert Dashboard](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png) (Dashboard avvisi)
3. Nella parte superiore, da sinistra a destra, la dashboard mostra i seguenti elementi su cui è possibile fare clic per visualizzare un elenco dettagliato:
    - *Avvisi attivati*: numero di avvisi che attualmente hanno soddisfatto la logica e sono stati attivati
    - *Totale regole di avviso*: numero di regole di avviso create nel testo secondario, numero di regole attualmente abilitate 
    

        > [!NOTE]
        > Per garantire la coerenza del dashboard con i dettagli in tutti gli avvisi attivati inclusi avvisi del log per informazioni dettagliate e analisi del log delle applicazioni, usare [Avvisi unificati avanzati (anteprima)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview)
  
  
4. Viene visualizzato un elenco di tutti gli avvisi attivati su cui l'utente può fare clic per visualizzare i dettagli
5. Per cercare avvisi specifici, è possibile usare le opzioni dell'elenco a discesa nella parte superiore della pagina per filtrare *sottoscrizioni, gruppi di risorse e/o risorse* specifiche. Inoltre, per eventuali avvisi non risolti, usare l'opzione *Filtra avviso* per cercare la parola chiave fornita in avvisi specifici corrispondenti per *nome, criteri di avviso, gruppo di risorse e risorsa di destinazione*

## <a name="managing-your-alerts-in-azure-portal"></a>Gestione degli avvisi nel portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.  
2. Selezionare il pulsante **Gestisci regole** nella barra superiore per passare alla sezione di gestione delle regole in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
3. Per individuare le regole di avviso specifiche, è possibile usare i filtri con elenco a discesa nella parte superiore, che consentono di filtrare le regole di avviso per *sottoscrizione, gruppi di risorse e/o risorsa* specifici. In alternativa all'uso del riquadro di ricerca sopra l'elenco di regole di avviso contrassegnato come *Filtra avvisi*, è possibile fornire una parola chiave, che viene confrontata con *nome avviso, condizione e risorsa di destinazione* per visualizzare solo le regole corrispondenti.
   ![Regole di gestione degli avvisi](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Per visualizzare o modificare una regola di avviso specifica, fare clic sul nome visualizzato come un collegamento selezionabile.
5. L'avviso definito viene visualizzato con la struttura di tre parti: 1) condizione dell'avviso 2) dettagli dell'avviso e 3) gruppo di azioni. È possibile selezionare **Criteri di destinazione** per modificare la logica degli avvisi o aggiungere nuovi criteri dopo aver usato l'icona del Cestino per eliminare la logica precedente. Analogamente, nella sezione dei dettagli degli avvisi è possibile modificare **Descrizione** e **Gravità**. È possibile modificare anche il gruppo di azioni o crearne uno nuovo per collegarlo all'avviso tramite il pulsante **Nuovo gruppo di azioni**.

   ![Modificare le regole di avviso](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Tramite il pannello superiore, le modifiche apportate all'avviso possono essere sottoposte a questi comandi: **Salva** per eseguire il commit di tutte le modifiche apportate all'avviso, **Elimina** per tornare alla schermata precedente senza eseguire il commit delle modifiche apportate all'avviso, **Disabilita** per disattivare l'avviso, dopo il quale non viene più eseguita o attivata alcuna azione. Infine, **Elimina** per rimuovere definitivamente l'intera regola di avviso da Azure.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla nuova funzionalità degli [avvisi delle metriche near real time](monitoring-near-real-time-metric-alerts.md)
- Leggere una [panoramica della raccolta di metriche](insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
- Informazioni sugli [avvisi del log in Avvisi di Azure](monitor-alerts-unified-log.md)
- [Informazioni sugli avvisi del log attività nell'esperienza Avvisi (anteprima)](monitoring-activity-log-alerts-new-experience.md)
