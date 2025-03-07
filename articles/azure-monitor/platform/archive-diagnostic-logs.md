---
title: Archiviare i log di diagnostica di Azure
description: Informazioni su come archiviare il log di diagnostica di Azure per la conservazione a lungo termine in un account di archiviazione.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8ab8a0bcf0c2c00515e46f3e2bbdb55b42ff7a2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071545"
---
# <a name="archive-azure-diagnostic-logs"></a>Archiviare i log di diagnostica di Azure

Questo articolo illustra come è possibile usare il portale di Azure, i cmdlet di PowerShell, l'interfaccia della riga di comando o l'API REST per archiviare i [log di diagnostica di Azure](diagnostic-logs-overview.md) in un account di archiviazione. Questa opzione è utile per conservare i log di diagnostica con criteri di conservazione facoltativi per il controllo, l'analisi statica o il backup. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione della risorsa che emette log, purché l'utente che configura l'impostazione abbia un accesso RBAC appropriato a entrambe le sottoscrizioni.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario [creare un account di archiviazione](../../storage/common/storage-quickstart-create-account.md) in cui archiviare i log di diagnostica. È consigliabile non usare un account di archiviazione esistente in cui sono archiviati altri dati non di monitoraggio, per poter controllare meglio l'accesso ai dati di monitoraggio. Tuttavia, se si archiviano anche il log attività e le metriche di diagnostica per un account di archiviazione, si potrebbe avere senso usare tale account di archiviazione per i log di diagnostica per tenere tutti i dati di monitoraggio in una posizione centrale.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica

Per archiviare i log di diagnostica usando uno dei metodi indicati di seguito, definire una **impostazione di diagnostica** per una risorsa specifica. Un'impostazione di diagnostica per una risorsa definisce le categorie di log e dati di metrica inviati a una destinazione (account di archiviazione, lo spazio dei nomi di hub eventi o dell'area di lavoro di Log Analitica). Definisce anche i criteri di conservazione (numero di giorni di conservazione) per gli eventi di ogni categoria di log e dati di metrica archiviati in un account di archiviazione. Se un criterio di conservazione è impostato su zero, gli eventi per tale categoria di log vengono archiviati per un periodo illimitato (per sempre). Criteri di conservazione in caso contrario, possono essere qualsiasi numero di giorni compreso tra 1 e 365. [Altre informazioni sulle impostazioni di diagnostica sono disponibili qui](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings). I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata, i log relativi a tale giornata non rientrano quindi più nei criteri di conservazione e verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente. Il processo di eliminazione inizia a mezzanotte UTC, ma si noti che possono essere necessarie fino a 24 ore per l'eliminazione dei log dall'account di archiviazione. 

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archiviare i log di diagnostica tramite il portale

1. Nel portale passare a Monitoraggio di Azure e fare clic su **Impostazioni di diagnostica**

    ![Sezione relativa al monitoraggio di Monitoraggio di Azure](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Facoltativamente filtrare l'elenco in base al tipo di risorsa o al gruppo di risorse, quindi fare clic sulla risorsa per cui si vuole specificare un'impostazione di diagnostica.

3. Se non esiste un'impostazione sulla risorsa selezionata, viene chiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   Se esistono già impostazioni sulla risorsa, verrò visualizzato un elenco di impostazioni già configurate per questa risorsa. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Assegnare un nome all'impostazione, selezionare la casella per **Esporta in Account di archiviazione**, quindi selezionare un account di archiviazione. Facoltativamente, impostare un numero di giorni per la conservazione di questi log usando i dispositivi di scorrimento **Conservazione (giorni)** . Se il valore di conservazione è zero giorni, i log vengono conservati all'infinito.

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa risorsa e i log di diagnostica vengono archiviati in tale account di archiviazione non appena vengono generati nuovi dati di eventi.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archiviare i log di diagnostica tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Category networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Proprietà | Obbligatorio | DESCRIZIONE |
| --- | --- | --- |
| resourceId |Sì |ID risorsa della risorsa in cui si vuole definire un'impostazione di diagnostica. |
| StorageAccountId |No |ID risorsa dell'account di archiviazione in cui devono essere salvati i log di diagnostica. |
| Category |No |Elenco delimitato da virgole di categorie di log da abilitare. |
| Enabled |Sì |Valore booleano che indica se la diagnostica viene abilitata o disabilitata per questa risorsa. |
| RetentionEnabled |No |Valore booleano che indica se un criterio di conservazione è abilitato per questa risorsa. |
| RetentionInDays |No |Numero di giorni per cui gli eventi devono essere mantenuti compreso tra 1 e 365. Se il valore è zero, i log vengono conservati all'infinito. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Archiviare i log di diagnostica tramite l'interfaccia della riga di comando di Azure

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

È possibile aggiungere altre categorie al log di diagnostica aggiungendo dizionari alla matrice JSON passata come parametro `--logs`.

L'argomento `--resource-group` è obbligatorio solo se `--storage-account` non è un ID oggetto. Per la documentazione completa relativa all'archiviazione dei log di diagnostica nella risorsa di archiviazione, vedere le informazioni di [riferimento sui comandi dell'interfaccia della riga di comando](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archiviare i log di diagnostica tramite l'API REST

[Vedere questo documento](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) per informazioni su come è possibile configurare un'impostazione di diagnostica usando l'API REST di Monitoraggio di Azure.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schema dei log di diagnostica nell'account di archiviazione

Dopo aver configurato l'archiviazione, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento in una delle categorie di log abilitate. I BLOB nel contenitore seguono la stessa convenzione di denominazione tra i log attività e i log di diagnostica come illustrato di seguito:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio, un nome BLOB potrebbe essere:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Ogni BLOB PT1H.json contiene un BLOB JSON di eventi che si sono verificati nell'ora specificata nell'URL BLOB (ad esempio, h=12). Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m=00) è sempre 00, perché gli eventi del log di diagnostica vengono sempre suddivisi in singoli BLOB per ogni ora.

Nel file PT1H.json ogni evento viene archiviato nella matrice "records", con questo formato:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Nome dell'elemento | DESCRIZIONE |
| --- | --- |
| time |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| resourceId |ID risorsa della risorsa interessata. |
| operationName |Nome dell'operazione. |
| category |Categoria di log dell'evento. |
| properties |Set di coppie `<Key, Value>` ad esempio Dictionary, che descrivono i dettagli dell'evento. |

> [!NOTE]
> Le proprietà e l'utilizzo di tali proprietà possono variare a seconda della risorsa.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione all'archivio BLOB di Azure con .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Trasmettere log di diagnostica di Azure a uno spazio di nomi di Hub eventi](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Archiviare i log di Azure Active Directory tramite Monitoraggio di Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Altre informazioni sui log di diagnostica](../../azure-monitor/platform/diagnostic-logs-overview.md)

