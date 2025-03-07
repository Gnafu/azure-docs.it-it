---
title: Panoramica di Azure Resource Graph
description: Informazioni sul servizio Azure Resource Graph, che consente di eseguire query complesse sulle risorse su vasta scala.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 7091e2bd1ee24843299721d3c21a8546197fac6f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207581"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Panoramica del servizio Azure Resource Graph

Azure Resource Graph è un servizio di Azure che consente di estendere la gestione delle risorse di Azure fornendo una funzione di esplorazione delle risorse efficiente e ad alte prestazioni con la possibilità di eseguire query su larga scala in un determinato set di sottoscrizioni in modo da poter controllare l'ambiente efficacemente. Queste query forniscono le funzionalità seguenti:

- Eseguire query sulle risorse con filtri, raggruppamenti e ordinamenti complessi basati sulle proprietà delle risorse.
- Possibilità di esplorare le risorse in modo iterativo in base ai requisiti di governance.
- Valutare l'impatto dell'applicazione di criteri in un ambiente cloud vasto.
- [Descrivere in dettaglio le modifiche apportate alle proprietà delle risorse](./how-to/get-resource-changes.md) (anteprima).

In questa documentazione verrà esaminata ogni funzionalità in modo dettagliato.

> [!NOTE]
> Azure Resource Graph è usato dalla barra di ricerca e dalla nuova esperienza di navigazione "Tutte le risorse" del portale di Azure e dal _diff visivo_ di [Cronologia modifiche](../policy/how-to/determine-non-compliance.md#change-history-preview)
>  di Criteri di Azure. Il suo scopo è aiutare i clienti a gestire ambienti su larga scala.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>In che modo Resource Graph integra Azure Resource Manager

Attualmente, Azure Resource Manager supporta le query su campi di risorse di base, in particolare il nome della risorsa, l'ID, il tipo, il gruppo di risorse, la sottoscrizione e la posizione. Resource Manager fornisce anche funzionalità per la chiamata di provider di risorse individuali per ottenere proprietà dettagliate una risorsa alla volta.

Con Azure Resource Graph, è possibile accedere a queste proprietà restituite dai provider di risorse senza la necessità di effettuare chiamate singole per ogni provider di risorse. Per un elenco dei tipi di risorsa supportati, cercare un **Sì** nella tabella [Risorse per le distribuzioni in modalità completa](../../azure-resource-manager/complete-mode-deletion.md). Un modo alternativo per visualizzare i tipi di risorse supportati è tramite il [browser di schemi di Azure Resource Graph Explorer](./first-query-portal.md#schema-browser).

Con Azure Resource Graph è possibile:

- Accedere alle proprietà restituite dai provider di risorse senza la necessità di effettuare chiamate singole per ogni provider di risorse.
- Visualizzare gli ultimi 14 giorni della cronologia delle modifiche apportate alla risorsa per verificare quali proprietà sono state modificate e quando. (anteprima)

## <a name="how-resource-graph-is-kept-current"></a>Come si tiene aggiornato Resource Graph

Quando si aggiorna una risorsa di Azure, Resource Graph riceve una notifica da Resource Manager relativa alla modifica.
Resource Graph aggiorna quindi il database. Resource Graph esegue inoltre un'_analisi completa_ normale. Questa analisi assicura che i dati di Resource Graph siano aggiornati in caso di mancate notifiche o se una risorsa viene aggiornata al di fuori di Resource Manager.

## <a name="the-query-language"></a>Il linguaggio di query

Ora che si conosce meglio Azure Resource Graph, si passerà alla creazione di query.

È importante sapere che il linguaggio di query di Azure Resource Graph è basato sul [linguaggio di query Kusto](../../data-explorer/data-explorer-overview.md) usato da Esplora dati di Azure.

In primo luogo, per informazioni dettagliate sulle operazioni e le funzioni che possono essere usate con Azure Resource Graph, vedere l'articolo sul [linguaggio di query di Azure Resource Graph](./concepts/query-language.md).
Per esaminare le risorse, vedere l'articolo sull'[esplorazione delle risorse](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Autorizzazioni in Azure Resource Graph

Per usare Resource Graph, è necessario avere i diritti appropriati nel [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) con almeno l'accesso in lettura alle risorse su cui eseguire le query. Senza almeno le autorizzazioni `read` per l'oggetto o il gruppo di oggetti di Azure, non verranno restituiti risultati.

> [!NOTE]
> Resource Graph usa le sottoscrizioni disponibili a un'entità di sicurezza durante l'accesso. Per visualizzare le risorse di una nuova sottoscrizione aggiunta durante una sessione attiva, l'entità deve aggiornare il contesto. Questa azione viene eseguita automaticamente quando ci si disconnette e si accede nuovamente.

L'interfaccia della riga di comando di Azure e Azure PowerShell usano sottoscrizioni accessibili all'utente. Quando si usa direttamente l'API REST, l'elenco delle sottoscrizioni viene fornito dall'utente. Se l'utente ha accesso a una qualsiasi delle sottoscrizioni dell'elenco, i risultati della query vengono restituiti per le sottoscrizioni accessibili all'utente. Questo comportamento è identico a quello riscontrato quando si chiama [Gruppi di risorse - Elenco](/rest/api/resources/resourcegroups/list) e si ottengono i gruppi di risorse cui si ha accesso senza alcuna indicazione del fatto che il risultato potrebbe essere parziale.
Se l'elenco non include presenti sottoscrizioni per cui l'utente dispone dei diritti appropriati, la risposta è _403_ (accesso negato).

## <a name="throttling"></a>Limitazione

Come servizio gratuito, le query a Resource Graph sono limitate per offrire l'esperienza e i tempi di risposta migliori per tutti i clienti. Se l'organizzazione vuole usare l'API Resource Graph per le query su larga scala eseguite di frequente, inviare commenti e suggerimenti dalla [pagina del portale Resource Graph](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Specificare il caso aziendale e selezionare la casella di controllo "Microsoft potrà inviare un messaggio di posta elettronica in merito ai commenti inviati" per essere contattati dal team.

Resource Graph applica limitazioni alle query a livello di utente. La risposta del servizio contiene le intestazioni HTTP seguenti:

- `x-ms-user-quota-remaining` (int): quota di risorse rimanenti per l'utente. Questo valore è associato al conteggio delle query.
- `x-ms-user-quota-resets-after` (hh:mm:ss): durata temporale fino alla reimpostazione del consumo della quota dell'utente

Per altre informazioni, vedere [Guidance for throttled requests](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Esecuzione della prima query

Azure Resource Graph Explorer, parte del portale di Azure, consente l'esecuzione di query di Resource Graph direttamente in portale di Azure. Aggiungere i risultati come grafici dinamici per fornire informazioni dinamiche in tempo reale al flusso di lavoro del portale. Per altre informazioni, vedere [Prima query con Azure Resource Graph Explorer](first-query-portal.md).

Resource Graph supporta l'interfaccia della riga di comando di Azure, Azure PowerShell, Azure SDK per .NET e altro ancora. La query è strutturata nello stesso modo per ogni linguaggio. Informazioni su come abilitare Resource Graph con:

- [Portale di Azure e Resource Graph Explorer](first-query-portal.md) 
- [Interfaccia della riga di comando di Azure](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Passaggi successivi

- Eseguire la prima query con l'[interfaccia della riga di comando di Azure](first-query-azurecli.md).
- Eseguire la prima query con [Azure PowerShell](first-query-powershell.md).
- Iniziare con le [query di base](./samples/starter.md).
- Migliorare la conoscenza delle [query avanzate](./samples/advanced.md).