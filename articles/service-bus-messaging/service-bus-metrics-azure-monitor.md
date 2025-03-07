---
title: Metriche del Bus di servizio di Azure in Monitoraggio di Azure | Microsoft Docs
description: Usare monitoraggio di Azure per monitorare le entità del Bus di servizio
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 80a4b1e60202b88f6ed3c1574bd4684575a9b153
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538067"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metriche del Bus di servizio di Azure in Monitoraggio di Azure

Le metriche del bus di servizio indicano lo stato delle risorse nella sottoscrizione di Azure. Grazie a un set completo di dati delle metriche è possibile valutare l'integrità generale delle risorse del bus di servizio non solo a livello di spazio dei nomi, ma anche a livello di entità. Queste statistiche possono rivelarsi importanti perché consentono di monitorare lo stato del bus di servizio. Le metriche consentono anche di risolvere i problemi senza dover contattare il supporto di Azure.

Monitoraggio di Azure offre interfacce utente unificate per il monitoraggio di diversi servizi di Azure. Per altre informazioni, vedere [Panoramica sul monitoraggio in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e l'esempio che descrive come [recuperare le metriche di Monitoraggio di Azure con .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) in GitHub.

> [!IMPORTANT]
> Nel caso in cui non si verifichi alcuna interazione con un'entità per 2 ore, fino a quando l'entità resterà inattiva le metriche mostreranno il valore "0".

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedere alle metriche tramite il [portale di Azure](https://portal.azure.com), oppure usare l'API di monitoraggio di Azure (REST e .NET) e dalle soluzioni di analisi come log di monitoraggio di Azure e hub eventi. Per altre informazioni, vedere [le metriche in Monitoraggio di Azure](../azure-monitor/platform/data-platform-metrics.md).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere ai 30 giorni di dati più recenti. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo valore viene configurato nelle [impostazioni di diagnostica ](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) in Monitoraggio di Azure.

## <a name="access-metrics-in-the-portal"></a>Accedere alle metriche nel portale

È possibile monitorare le metriche nel tempo nel [portale di Azure](https://portal.azure.com). L'esempio seguente illustra come visualizzare le richieste completate e le richieste in ingresso a livello di account:

![][1]

È anche possibile accedere alle metriche direttamente tramite lo spazio dei nomi. A tale scopo, selezionare lo spazio dei nomi e quindi fare clic su **metriche**. Per visualizzare le metriche filtrate in base l'ambito dell'entità, selezionare l'entità e quindi fare clic su **metriche**.

![][2]

Per le metriche che supportano le dimensioni, è necessario filtrare specificando il valore di dimensione da usare.

## <a name="billing"></a>Fatturazione

Le metriche e avvisi in Monitoraggio di Azure vengono addebitati in base a ogni avviso. Questi addebiti devono essere disponibili nel portale quando l'avviso è il programma di installazione e prima che venga salvato. 

Ulteriori soluzioni che inseriscono i dati delle metriche vengono fatturate direttamente da tali soluzioni. Ad esempio, la fatturazione viene effettuata da Archiviazione di Azure se i dati relativi alle metriche vengono archiviati in un account di Archiviazione di Azure. La fatturazione viene effettuata da Log Analytics anche se si esegue lo streaming dei dati relativi alle metriche verso Log Analytics per l'esecuzione di analisi avanzate.

Le metriche seguenti offrono una panoramica dell'integrità del servizio. 

> [!NOTE]
> Numerose metriche risultano attualmente deprecate perché hanno assunto un nome diverso. Ciò potrebbe rendere necessario l'aggiornamento dei riferimenti. Le metriche contrassegnate con la parola "deprecata" non saranno supportate in futuro.

Tutti i valori delle metriche vengono inviati a Monitoraggio di Azure ogni minuto. La granularità temporale definisce l'intervallo di tempo per il quale vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche del bus di servizio è 1 minuto.

## <a name="request-metrics"></a>Metriche per le richieste

Conta il numero di richieste di operazioni di dati e gestione.

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
| Richieste in ingresso| Numero di richieste inviate al servizio del bus di servizio in un periodo specificato. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
|Richieste riuscite|Numero di richieste completate inviate al servizio del bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
|Errori server|Numero di richieste non elaborate a causa di un errore nel servizio del bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
|Errori dell'utente (vedere la sottosezione seguente)|Numero di richieste non elaborate a causa di errori utente in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
|Richieste limitate|Numero di richieste che sono state limitate perché è stata superata la soglia di utilizzo.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|

### <a name="user-errors"></a>Errori utente

I due tipi di errori seguenti sono classificati come errori utente:

1. Errori sul lato client (in HTTP sono errori 400).
2. Gli errori che si verificano durante l'elaborazione di messaggi, ad esempio [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriche per i messaggi

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|Messaggi in ingresso|Numero di eventi o messaggi inviati al bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
|Messaggi in uscita|Numero di eventi o messaggi inviati dal bus di servizio in un periodo specificato.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|
| Messaggi| Numero di messaggi contenuti in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Average <br/> Dimensione: EntityName |
| ActiveMessages| Numero di messaggi attivi in una coda o in un argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Average <br/> Dimensione: EntityName |
| Messaggi come non recapitabili| Numero di messaggi come non recapitabili in una coda/argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Average <br/>Dimensione: EntityName |
| Messaggi pianificati| Conteggio dei messaggi pianificati in una coda/argomento. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Average  <br/> Dimensione: EntityName |

## <a name="connection-metrics"></a>Metriche di connessione

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|ActiveConnections|Numero di connessioni attive in uno spazio dei nomi e in un'entità.<br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni: EntityName|

## <a name="resource-usage-metrics"></a>Metriche di utilizzo delle risorse

> [!NOTE] 
> Le metriche seguenti sono disponibili solo con il livello **Premium**. 

| Nome della metrica | Descrizione |
| ------------------- | ----------------- |
|Uso della CPU per spazio dei nomi|Utilizzo percentuale della CPU dello spazio dei nomi.<br/><br/> Unità: Percent <br/> Tipo di aggregazione: Massima <br/> Dimensione: EntityName|
|Uso delle dimensioni della memoria per spazio dei nomi|Utilizzo percentuale della memoria dello spazio dei nomi.<br/><br/> Unità: Percent <br/> Tipo di aggregazione: Massima <br/> Dimensione: EntityName|

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Il bus di servizio di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure. L'aggiunta di dimensioni alle metriche è facoltativa. Se non si aggiungono le dimensioni, le metriche vengono specificate a livello di spazio dei nomi. 

|Nome della dimensione|Descrizione|
| ------------------- | ----------------- |
|EntityName| Il bus di servizio supporta le entità di messaggistica nello spazio dei nomi.|

## <a name="set-up-alerts-on-metrics"></a>Configurazione di avvisi relativi alle metriche

1. Nella scheda **Metriche** della pagina **Spazio dei nomi del bus di servizio** selezionare **Configura avvisi**. 

    ![Pagina Metriche - menu Configura avvisi](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selezionare il **Seleziona destinazione** opzione, ed eseguire le operazioni seguenti sul **selezionare una risorsa** pagina: 
    1. Selezionare **Spazio dei nomi del bus di servizio** per il campo **Filtra per tipo di risorsa**. 
    2. Selezionare la sottoscrizione di Azure per il campo **Filtra per sottoscrizione**.
    3. Selezionare lo **spazio dei nomi del bus di servizio** nell'elenco. 
    4. Selezionare **Operazione completata**. 
    
        ![Selezionare lo spazio dei nomi](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selezionare **Aggiungi criteri** ed eseguire le azioni seguenti nella pagina **Configura logica dei segnali**:
    1. Selezionare **Metriche** per **Tipo segnale**. 
    2. Selezionare un segnale, Ad esempio:  **Errori del servizio**. 

        ![Selezionare gli errori del server](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selezionare **Greater than** (Maggiore di) per **Condizione**.
    2. Selezionare **Totale** per **Aggregazione temporale**. 
    3. Immettere **5** per **Soglia**. 
    4. Selezionare **Operazione completata**.    

        ![Specificare una condizione](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Nella pagina **Crea regola** espandere **Definire i dettagli dell'avviso** ed eseguire queste azioni:
    1. Immettere un **nome** per l'avviso. 
    2. Immettere una **descrizione** per l'avviso.
    3. Selezionare una **gravità** per l'avviso. 

        ![Dettagli dell'avviso](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Nella pagina **Crea regola** espandere **Definire il gruppo di azioni**, selezionare **Nuovo gruppo di azioni** e nella pagina **Aggiungi gruppo di azioni** eseguire le azioni seguenti. 
    1. Immettere un nome per il gruppo di azioni.
    2. Immettere un nome breve per il gruppo di azioni. 
    3. Selezionare la propria sottoscrizione. 
    4. Selezionare un gruppo di risorse. 
    5. Per questa procedura dettagliata immettere **Invia messaggio di posta elettronica** per **NOME AZIONE**.
    6. Selezionare **Email/SMS/Push/Voice** (E-mail/SMS/Push/Voce) per **TIPO DI AZIONE**. 
    7. Selezionare **Modifica dettagli**. 
    8. Nella pagina **Email/SMS/Push/Voice** (E-mail/SMS/Push/Voce) eseguire le azioni seguenti:
        1. Selezionare **E-mail**. 
        2. Digitare l'**indirizzo e-mail**. 
        3. Selezionare **OK**.

            ![Dettagli dell'avviso](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Nella pagina **Aggiungi gruppo di azioni** selezionare **OK**. 
1. Nella pagina **Crea regola** selezionare **Crea regola di avviso**. 

    ![Pulsante Crea regola di avviso](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere le [Panoramica di monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


