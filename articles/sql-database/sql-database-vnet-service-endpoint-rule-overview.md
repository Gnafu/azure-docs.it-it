---
title: Endpoint del servizio e regole della rete virtuale per il database SQL di Azure | Microsoft Docs
description: Contrassegnare una subnet come endpoint del servizio Rete virtuale. Contrassegnare quindi l'endpoint come regola della rete virtuale per ACL nel database SQL di Azure. Il database SQL accetta quindi la comunicazione da tutte le macchine virtuali e altri nodi nella subnet.
services: sql-database
ms.service: sql-database
author: MightyPen
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 03/15/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 7622c6e6ffb1410cc2cbd42f6ac3601d281832da
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Usare gli endpoint del servizio Rete virtuale e le regole per il database SQL di Azure

Le *regole della rete virtuale* rappresentano una funzionalità di sicurezza firewall che consente di definire se il server del database SQL di Azure accetta le comunicazioni inviate da subnet specifiche nelle reti virtuali. Questo articolo spiega i motivi per cui la funzione delle regole della rete virtuale rappresenti a volte la scelta ideale per consentire le comunicazioni con il database SQL di Azure in modo sicuro.

Per creare una regola della rete virtuale, deve essere disponibile un [endpoint del servizio Rete virtuale][vm-virtual-network-service-endpoints-overview-649d] al quale la regola possa fare riferimento.


#### <a name="how-to-create-a-virtual-network-rule"></a>Come creare una regola della rete virtuale

Se si crea solo una regola della rete virtuale, è possibile procedere direttamente ai passaggi e alla spiegazione [più avanti in questo articolo](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologia e descrizione

**Rete virtuale:** è possibile associare reti virtuali alla sottoscrizione di Azure.

**Subnet:** una rete virtuale contiene **subnet**. Le macchine virtuali (VM) di Azure esistenti vengono assegnate a subnet. Una subnet può contenere varie VM o altri nodi di calcolo. I nodi di calcolo esterni alla rete virtuale non possono accedervi, a meno che non si configuri la sicurezza in modo da consentirne l'accesso.

**Endpoint del servizio Rete virtuale:** un [endpoint del servizio Rete virtuale][vm-virtual-network-service-endpoints-overview-649d] è una subnet in cui i valori di proprietà includono uno o più nomi formali di tipi di servizi Azure. Questo articolo è incentrato sul nome del tipo **Microsoft.Sql**, che fa riferimento al servizio Azure denominato Database SQL.

**Regola della rete virtuale:** una regola della rete virtuale per il server di database SQL è una subnet presente nell'elenco di controllo di accesso (ACL) del server di database SQL. Per essere nell'elenco ACL del database SQL, la subnet deve contenere il nome del tipo **Microsoft.Sql**.

Una regola della rete virtuale indica al server di database SQL di accettare le comunicazioni da ogni nodo che si trova nella subnet.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Vantaggi di una regola di rete virtuale

Fino a quando non si interviene, le macchine virtuali nelle subnet non possono comunicare con il database SQL. Un'azione che stabilisce la comunicazione è la creazione di una regola della rete virtuale. La base logica per la scelta dell'approccio delle regole di rete virtuale richiede una discussione di confronto riguardo le opzioni di sicurezza concorrenti offerte dal firewall.

#### <a name="a-allow-access-to-azure-services"></a>R. Possibilità di accedere ai servizi di Azure

Il riquadro del firewall contiene un pulsante **ON/OFF** etichettato **Consenti l'accesso a Servizi di Azure**. L'impostazione **ON** consente le comunicazioni da tutti gli indirizzi IP di Azure e tutte le subnet di Azure. Questi indirizzi IP o subnet di Azure potrebbero non essere di proprietà dell'utente. Questa impostazione **ON** è probabilmente più aperta rispetto al livello desiderato per il database SQL. La funzione delle regole della rete virtuale offre un controllo molto più granulare.

#### <a name="b-ip-rules"></a>B. Regole IP

Il firewall del database SQL consente di specificare gli intervalli di indirizzi IP da cui vengono accettate le comunicazioni nel database SQL. Questo approccio è ideale per gli indirizzi IP stabili esterni alla rete privata di Azure, ma molti nodi all'interno della rete privata di Azure sono configurati con indirizzi IP *dinamici*. Gli indirizzi IP dinamici potrebbero cambiare, ad esempio al riavvio di una macchina virtuale. Sarebbe inutile specificare un indirizzo IP dinamico in una regola del firewall, in un ambiente di produzione.

È possibile recuperare l'opzione IP ottenendo un indirizzo IP *statico* per la macchina virtuale. Per i dettagli, vedere [Configurare indirizzi IP privati per una VM mediante il portale di Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Tuttavia, l'approccio IP statico può diventare difficile da gestire ed è dispendioso a livello di scalabilità. Le regole della rete virtuale sono più semplici da creare e gestire.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Database SQL non ancora disponibile in una subnet

Se il server di database SQL di Azure è un nodo in una subnet nella rete virtuale, tutti i nodi all'interno della rete virtuale possono comunicare con il database SQL. In questo caso, le macchine virtuali possono comunicare con il database SQL senza aver bisogno di regole di rete virtuale o IP.

Tuttavia, a partire da settembre 2017, il database SQL di Azure non è ancora tra i servizi che possono essere assegnati a una subnet.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Dettagli sulle regole della rete virtuale

Questa sezione illustra alcuni dettagli sulle regole della rete virtuale.

#### <a name="only-one-geographic-region"></a>Una sola area geografica

Un endpoint del servizio Rete virtuale si applica a una sola area di Azure. L'endpoint non consente ad altre aree di accettare le comunicazioni dalla subnet.

Ogni regola della rete virtuale è limitata all'area a cui si applica il relativo endpoint sottostante.

#### <a name="server-level-not-database-level"></a>A livello di server, non a livello di database

Ogni regola di rete virtuale si applica all'intero server di database SQL di Azure, non solo a un determinato database nel server. In altre parole, la regola della rete virtuale si applica a livello di server, non a livello di database.

- Al contrario, le regole IP possono essere applicate a qualsiasi livello.

#### <a name="security-administration-roles"></a>Ruoli di amministrazione di sicurezza

I ruoli di sicurezza sono distinti nell'amministrazione degli endpoint del servizio Rete virtuale. Ogni ruolo indicato di seguito deve svolgere determinate azioni:

- **Amministratore di rete:**&nbsp; attivare l'endpoint.
- **Amministratore di database:** &nbsp; aggiornare l'elenco di controllo di accesso (ACL) per aggiungere la subnet specificata al server di database SQL.

*Alternativa del controllo degli accessi in base al ruolo:*

I ruoli di amministratore di rete e amministratore di database hanno più funzionalità di quelle necessarie a gestire le regole della rete virtuale. È necessario solo un subset delle relative funzionalità.

È possibile scegliere di usare il [controllo degli accessi in base al ruolo (RBAC)] [ rbac-what-is-813s] in Azure per creare un singolo ruolo personalizzato che contiene solo il subset necessario di funzionalità. È possibile usare il ruolo personalizzato anziché coinvolgere l'amministratore di rete o di database. La superficie di attacco dell'esposizione a rischi per la sicurezza è ridotta se si aggiunge un utente a un ruolo personalizzato, anziché aggiungere l'utente agli altri due ruoli di amministratore principali.

> [!NOTE]
> In alcuni casi il database SQL di Azure e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi è necessario garantire le configurazioni seguenti:
> - Entrambe le sottoscrizioni devono essere nello stesso tenant di Azure Active Directory.
> - L'utente ha le autorizzazioni necessarie per avviare le operazioni, ad esempio abilitare gli endpoint di servizio e aggiungere una subnet della rete virtuale al server specificato.

## <a name="limitations"></a>Limitazioni

Per il database SQL di Azure, la funzionalità delle regole della rete virtuale presenta le limitazioni seguenti:

- Un'app Web può essere mappata a un indirizzo IP privato in una rete virtuale/subnet. Anche se gli endpoint di servizio sono attivati dalla rete virtuale/subnet specificata, le connessioni dall'app Web al server avranno come origine l'indirizzo IP pubblico di Azure, non la rete virtuale/subnet. Per abilitare la connettività da un'app Web a un server che dispone di regole del firewall della rete virtuale, è necessario **consentire tutti i servizi Azure** sul server.

- Nel firewall per il database SQL ogni regola della rete virtuale fa riferimento a una subnet. Tutte queste subnet cui viene fatto riferimento devono essere ospitate nella stessa area geografica che ospita il database SQL.

- Ogni server di database SQL di Azure può includere fino a 128 voci ACL per qualsiasi rete virtuale specificata.

- Le regole della rete virtuale si applicano solo alle reti virtuali di Azure Resource Manager e non alle reti con un [modello di distribuzione classica][arm-deployment-model-568f].

- L'attivazione degli endpoint di servizio di rete virtuale nel Database SQL di Azure abilita anche gli endpoint per i servizi MySQL e PostGres Azure. Tuttavia, con gli endpoint attivati i tentativi di connessione dagli endpoint alle istanze di MySQL o Postgres avranno esito negativo.
    - La causa principale è che MySQL e PostGres attualmente non supportano ACLing.

- Nel firewall, gli intervalli di indirizzi IP si applicano ai seguenti elementi di rete, ma non le regole della rete virtuale:
    - [VPN (rete privata virtuale) da sito a sito (S2S)][vpn-gateway-indexmd-608y]
    - Ambiente locale tramite [ExpressRoute][expressroute-indexmd-744v]

#### <a name="considerations-when-using-service-endpoints"></a>Considerazioni sull'uso di endpoint del servizio
Quando si usano gli endpoint del servizio per il Database SQL di Azure, rivedere le considerazioni seguenti:

- **In uscita verso gli indirizzi IP pubblici del Database SQL di Azure è necessario che** i gruppi di sicurezza di rete devono essere aperti gli indirizzi IP del Database SQL di Azure per consentire la connettività. È possibile farlo tramite i [Tag dei servizi](../virtual-network/security-overview.md#service-tags) del Gruppo di sicurezza di rete per il Database SQL di Azure.
- **Non sono supportati i Database di Azure per PostgreSQL e MySQL**: gli endpoint del servizio non sono supportati per il Database di Azure per PostgreSQL o MySQL. Abilitando gli endpoint del servizio al Database SQL si interromperà la connettività a questi servizi. È disponibile una mitigazione per questo, contattare *dmalik@microsoft.com*.

#### <a name="expressroute"></a>ExpressRoute

Se la rete è connessa alla rete di Azure con [ExpressRoute][expressroute-indexmd-744v], ogni circuito è configurato con due indirizzi IP pubblici in Microsoft Edge. I due indirizzi IP vengono usati per connettersi ai servizi Microsoft, ad esempio ad Archiviazione di Azure, usando il peering pubblico di Azure.

Per consentire la comunicazione tra il circuito e il database SQL di Azure, è necessario creare regole di rete IP per gli indirizzi IP pubblici dei circuiti. Per trovare gli indirizzi IP pubblici del circuito ExpressRoute, aprire un ticket di supporto in ExpressRoute tramite il portale di Azure.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>Impatto della rimozione di "Allow all Azure Services" (Consenti tutti i servizi di Azure)

Molti utenti vogliono rimuovere **Allow all Azure Services** (Consenti tutti i servizi di Azure) dai server SQL di Azure e sostituirlo con una regola del firewall della rete virtuale.
Questa rimozione tuttavia ha effetto sulle funzionalità del database SQL di Azure seguenti:

#### <a name="import-export-service"></a>Servizio Importazione/Esportazione di Azure
Il servizio Importazione/Esportazione del database SQL di Azure viene eseguito sulle VM in Azure. Queste VM non sono nella rete virtuale e quindi ottengono un IP di Azure quando si connettono al database. Rimuovendo **Allow all Azure Services** (Consenti tutti i servizi di Azure), queste VM non potranno accedere ai database.
È possibile applicare una soluzione alternativa per ovviare al problema. Eseguire l'importazione o l'esportazione BACPAC direttamente nel codice usando l'API DACFx. Assicurarsi che venga distribuita in una VM nella stessa subnet della rete virtuale per cui si è impostata la regola del firewall.

#### <a name="sql-database-query-editor"></a>Editor di query del database SQL
L'editor di query del database SQL di Azure viene distribuito nelle VM in Azure. Queste VM non sono nella rete virtuale. Le VM ottengono quindi un IP di Azure quando si connettono al database. Rimuovendo **Allow all Azure Services** (Consenti tutti i servizi di Azure), queste VM non potranno accedere ai database.

#### <a name="table-auditing"></a>Controllo tabelle
Al momento è possibile abilitare il controllo sul database SQL in due modi. Il controllo tabelle non riesce dopo avere abilitato gli endpoint di servizio nel server di Azure SQL. Per mitigare il problema, in questo caso passare al controllo BLOB.

#### <a name="impact-on-data-sync"></a>Impatto sulla sincronizzazione dati
SQLDB di Azure è dotato della funzionalità di sincronizzazione dei dati che si connette ai database dell'utente tramite gli indirizzi IP di Azure. Quando si usano gli endpoint del servizio, è probabile che si spenga l'accesso **Consenti tutti i servizi Azure** per il server logico. Questa operazione interromperà la funzionalità di sincronizzazione dei dati.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure

Archiviazione di Azure ha implementato la stessa funzionalità che consente di limitare la connettività all'account di archiviazione.
Se si sceglie di usare questa funzionalità con un account di archiviazione usato da un server di Azure SQL, è possibile che si verifichino problemi. Di seguito sono riportati un elenco e una spiegazione delle funzionalità del database SQL di interessate.

#### <a name="azure-sqldw-polybase"></a>PolyBase per Azure SQLDW
PolyBase viene in genere usato per caricare i dati in Azure SQLDW dagli account di archiviazione. Se l'account di archiviazione da cui si caricano i dati limita l'accesso solo a un set di subnet della rete virtuale, la connettività da PolyBase all'account verrà interrotta. È disponibile una mitigazione per questo oggetto; contattare *dmalik@microsoft.com* per altre informazioni.

#### <a name="azure-sqldb-blob-auditing"></a>Controllo BLOB del database SQL di Azure
Il controllo BLOB esegue il push dei log di controllo nell'account di archiviazione. Se questo account di archiviazione usa la funzionalità degli endpoint di servizio di rete virtuale, la connettività dal database SQL di Azure all'account di archiviazione verrà interrotta.


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Aggiunta di una regola del firewall della rete virtuale al server senza attivare gli endpoint di servizio di rete virtuale

In passato, prima che questa funzionalità fosse migliorata, era necessario attivare gli endpoint di servizio di rete virtuale prima di poter implementare una regola di rete virtuale attiva nel firewall. Gli endpoint correlavano una determinata subnet della rete virtuale a un database SQL di Azure. A partire da gennaio 2018 è invece possibile aggirare questo requisito impostando il flag **IgnoreMissingServiceEndpoint**.

La semplice impostazione di una regola del firewall non consente di proteggere il server. Per garantire la sicurezza, è anche necessario attivare gli endpoint di servizio di rete virtuale. Quando si attivano gli endpoint di servizio, la subnet della rete virtuale registra un tempo di inattività fino al termine della transizione dallo stato inattivo a quello attivo. Questo vale soprattutto per le reti virtuali di grandi dimensioni. È possibile usare il flag **IgnoreMissingServiceEndpoint** per ridurre o eliminare il tempo di inattività durante la transizione.

È possibile impostare il flag **IgnoreMissingServiceEndpoint** usando PowerShell. Per informazioni dettagliate, vedere [Usare PowerShell per creare un endpoint del servizio virtuale e una regola per il database SQL di Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].


## <a name="errors-40914-and-40615"></a>Errori 40914 e 40615

L'errore di connessione 40914 è correlato alle *regole della rete virtuale*, come specificato nel riquadro Firewall nel portale di Azure. L'errore 40615 è simile, ma è correlato alle *regole degli indirizzi IP* in Firewall.

#### <a name="error-40914"></a>Errore 40914

*Testo del messaggio:* Impossibile aprire il server '*[nome-server]*' richiesto dall'accesso. Al client non è consentito accedere al server.

*Descrizione dell'errore:* il client si trova in una subnet che include endpoint server di rete virtuale. Tuttavia, il server di database SQL di Azure non è associato ad alcuna regola della rete virtuale che concede alla subnet il diritto di comunicare con il database SQL.

*Risoluzione dell'errore:* nel riquadro Firewall del portale di Azure usare il controllo delle regole della rete virtuale per [aggiungere una regola della rete virtuale](#anchor-how-to-by-using-firewall-portal-59j) per la subnet.

#### <a name="error-40615"></a>Errore 40615

*Testo del messaggio:* Impossibile aprire il server '{0}' richiesto dall'accesso. Non è consentito l'accesso del client con indirizzo IP '{1}' al server.

*Descrizione dell'errore:* il client sta tentando di connettersi da un indirizzo IP che non è autorizzato a connettersi al server di database SQL di Azure. Il firewall del server non ha alcuna regola degli indirizzi IP che consente a un client di comunicare dall'indirizzo IP specifico al database SQL.

*Risoluzione dell'errore:* immettere l'indirizzo IP del client come regola IP. A questo scopo, usare il riquadro Firewall nel portale di Azure.


Un elenco di diversi messaggi di errore del database SQL è disponibile [qui][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Il portale può creare una regola della rete virtuale

Questa sezione illustra come usare il [portale di Azure] [http-azure-portal-link-ref-477t] per creare una *regola della rete virtuale* nel database di SQL Azure. La regola indica al database SQL di accettare le comunicazioni da una subnet specifica contrassegnata come *endpoint del servizio Rete virtuale*.

#### <a name="powershell-alternative"></a>Alternativa PowerShell

Anche uno script di PowerShell può creare regole della rete virtuale. Ad esempio, il cmdlet essenziale **New-AzureRmSqlServerVirtualNetworkRule**. Se interessati, vedere [Usare PowerShell per creare un endpoint del servizio virtuale e una regola per il database SQL di Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="rest-api-alternative"></a>Alternativa API REST

Internamente, i cmdlet di PowerShell per le azioni SQL sulle reti virtuali chiamano API REST. È possibile chiamare direttamente le API REST.

- [Regole della rete virtuale: operazioni][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>prerequisiti

È necessario avere già una subnet contrassegnata con lo specifico *nome del tipo* di endpoint del servizio Rete virtuale pertinente per il database SQL di Azure.

- Il nome del tipo di endpoint pertinente è **Microsoft.Sql**.
- Se la subnet non è contrassegnata con il nome del tipo, vedere [Verificare che la subnet sia un endpoint][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Procedure del portale di Azure

1. Accedere al [Portale di Azure][http-azure-portal-link-ref-477t].

2. Nel portale passare a **SQL Server** &gt; **Firewall/Reti virtuali (anteprima)**.

3. Impostare il controllo **Consenti l'accesso a Servizi di Azure** su OFF.

    > [!IMPORTANT]
    > Se si lascia il controllo impostato su SÌ, il server di database SQL di Azure accetta la comunicazione da qualsiasi subnet. Lasciando il controllo impostato su SÌ, il numero di accessi potrebbe essere eccessivo dal punto di vista della sicurezza. La funzionalità di endpoint del servizio Rete virtuale di Microsoft Azure, in sinergia con la funzionalità delle regole della rete virtuale del database SQL, consente di ridurre la superficie di attacco per la sicurezza.

4. Fare clic sul controllo **+ Aggiungi esistenti** nella sezione **Reti virtuali**.

    ![Fare clic su Aggiungi esistenti (endpoint della subnet, come regola SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. Nel nuovo riquadro **Crea/Aggiorna** compilare i controlli con i nomi delle risorse di Azure.

    > [!TIP]
    > È necessario includere il **prefisso dell'indirizzo** corretto per la subnet. Il valore è disponibile nel portale.
    > Passare a **Tutte le risorse** &gt; **Tutti i tipi** &gt; **Reti virtuali**. Il filtro visualizza le reti virtuali. Fare clic sulla rete virtuale desiderata e quindi su **Subnet**. La colonna **INTERVALLO DI INDIRIZZI** contiene il prefisso dell'indirizzo desiderato.

    ![Compilare i campi per la nuova regola.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Fare clic sul pulsante **OK** nella parte inferiore del riquadro.

7.  Osservare la regola della rete virtuale risultante nel riquadro del firewall.

    ![Osservare la nuova regola nel riquadro del firewall.][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> Le regole presentano gli stati seguenti:
> - **Pronta:** indica che l'operazione avviata è riuscita.
> - **Non riuscita:** indica che l'operazione avviata non è riuscita.
> - **Eliminata:** si applica solo alle operazioni di eliminazione e indica che la regola è stata eliminata e non viene più applicata.
> - **In corso:** indica che l'operazione è in corso. Mentre l'operazione è in questo stato, viene applicata la regola precedente.


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Articoli correlati

- [Endpoint del servizio Rete virtuale di Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regole firewall a livello di server e di database per il database SQL di Azure][sql-db-firewall-rules-config-715d]

La funzionalità delle regole della rete virtuale per il database SQL di Azure è disponibile dalla fine di settembre 2017.

## <a name="next-steps"></a>Passaggi successivi

- [Usare PowerShell per creare un endpoint del servizio virtuale e una regola per il database SQL di Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].
- [Regole della rete virtuale: operazioni][rest-api-virtual-network-rules-operations-862r] con le API REST



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
