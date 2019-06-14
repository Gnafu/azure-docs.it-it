---
title: Gateway dati locale
description: Un gateway locale è necessario se il server Analysis Services di Azure si connette a origini dati locali.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 190394481f17310784f87c9e2f642eeea0b2597f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062228"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Connessione a origini dati locali con gateway dati locale
Il gateway dati locale garantisce il trasferimento sicuro dei dati tra le origini dati locali e i server Azure Analysis Services nel cloud. Oltre a lavorare con più server Azure Analysis Services nella stessa area, la versione più recente del gateway funziona anche con app per la logica di Azure, Power BI, PowerApps e Microsoft Flow. È possibile associare più servizi nella stessa sottoscrizione e nella stessa area con un singolo gateway. 

La configurazione iniziale del gateway è un processo in quattro fasi:

- **Scaricare ed eseguire il programma di installazione**: questo passaggio consente di installare un servizio gateway in un computer dell'organizzazione. È anche possibile accedere ad Azure usando un account in Azure AD del [tenant](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Gli account Azure B2B (guest) non sono supportati.

- **Registrare il gateway**: in questo passaggio vengono specificati un nome e una chiave di ripristino per il gateway, si seleziona un'area e si registra il gateway con il servizio cloud Gateway. La risorsa gateway può essere registrata in qualsiasi area, ma è consigliabile registrarla nella stessa area dei server Analysis Services. 

- **Creare una risorsa per il gateway in Azure**: in questo passaggio, si crea una risorsa per il gateway nella sottoscrizione di Azure.

- **Connettere i server per la risorsa per il gateway**: dopo aver creato una risorsa per il gateway nella sottoscrizione, è possibile iniziare a connettere i server. È possibile connettere più server e altre risorse purché si trovino nella stessa sottoscrizione e nella stessa area.

Per iniziare subito, vedere [Installare e configurare il gateway dati locale](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Funzionamento
Il gateway installato su un computer dell'organizzazione viene eseguito come servizio Windows, **Gateway dati locale**. Il servizio locale è registrato con il servizio Cloud Gateway tramite il bus di servizio di Azure. È quindi possibile creare una servizio Cloud della risorsa per il gateway per la sottoscrizione di Azure. I server di Azure Analysis Services quindi sono connessi alla risorsa per il gateway. Quando i modelli nel server devono connettersi ai dati locali di origine per le query o l'elaborazione, un flusso di dati e query attraversa la risorsa del gateway, il bus di servizio di Azure, il servizio gateway dati locale e le origini dati. 

![Funzionamento](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query e flusso di dati:

1. Il servizio cloud crea una query con le credenziali crittografate per l'origine dati locale. La query viene inviata a una coda per l'elaborazione da parte del gateway.
2. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel [bus di servizio di Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.
4. Il gateway riceve la query, decrittografa le credenziali e si connette alle origini dati usando tali credenziali.
5. Invia quindi la query all'origine dati per l'esecuzione.
6. I risultati vengono inviati dall'origine dati al gateway e quindi al servizio cloud e al server.

## <a name="windows-service-account"> </a>Account del servizio Windows
Il gateway dati locale è configurato per usare *NT SERVICE\PBIEgwService* come credenziale di accesso al servizio Windows. Per impostazione predefinita, dispone del diritto di accesso come servizio nel contesto del computer in cui viene installato il gateway. Questa credenziale non coincide con l'account usato per la connessione alle origini dati locali o con l'account Azure.  

Se si verificano problemi di autenticazione con il server proxy, può essere necessario trasformare l'account del servizio Windows nell'account di un utente del dominio o nell'account di un servizio gestito.

## <a name="ports"> </a>Porte
Il gateway crea una connessione in uscita al bus di servizio di Azure. La comunicazione avviene sulle porte in uscita: TCP 443 (impostazione predefinita), 5671, 5672, 9350 attraverso 9354.  Non sono richieste porte in ingresso.

È consigliabile inserire nell'elenco degli elementi consentiti gli indirizzi IP per l'area dati del firewall. È possibile scaricare l'[elenco degli indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'elenco viene aggiornato ogni settimana.

> [!NOTE]
> Gli indirizzi IP inclusi nell'elenco degli indirizzi IP dei data center di Azure sono nella notazione CIDR. Per altre informazioni, vedere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Di seguito sono indicati i nomi di dominio completi usati dal gateway.

| Nomi di dominio | Porte in uscita | Descrizione |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usato per scaricare il programma di installazione. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.microsoftonline-p.com |443 |Usato per l'autenticazione, a seconda della configurazione. |

### <a name="force-https"></a>Forzare la comunicazione HTTPS con il bus di servizio di Azure
È possibile forzare il gateway a comunicare con il bus di servizio di Azure usando HTTPS anziché TCP diretto. Questa operazione, tuttavia, può ridurre le prestazioni in misura significativa. È necessario modificare il file *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* modificando il valore da `AutoDetect` in `Https`. Questo file si trova solitamente in *C:\Programmi\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Amministrazione a livello di tenant 

Attualmente non esiste un'unica posizione in cui gli amministratori tenant possono gestire tutti i gateway che altri utenti hanno installato e configurato.  Se l'utente è un amministratore tenant, è consigliabile chiedere agli utenti dell'organizzazione di aggiungere l'utente come amministratore a ogni gateway che installano. In questo modo è possibile gestire tutti i gateway all'interno dell'organizzazione tramite la pagina delle impostazioni del gateway oppure tramite i [comandi di PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Domande frequenti

### <a name="general"></a>Generale

**D**: È necessario un gateway per le origini dati nel cloud, ad esempio il database SQL di Azure? <br/>
**R**:  No. È necessario un gateway solo per la connessione alle origini dati locali.

**D**: Il gateway deve essere installato nello stesso computer dell'origine dati? <br/>
**R**:  No. Il gateway deve solo potersi connettere al server, in genere nella stessa rete.

<a name="why-azure-work-school-account"></a>

**D**: Perché è necessario usare un account aziendale o dell'istituto di istruzione per accedere? <br/>
**R**: È possibile usare solo un account aziendale o dell'istituto di istruzione quando si installa il gateway dati locale. E l'account deve essere dello stesso tenant della sottoscrizione in cui si sta configurando la risorsa per il gateway. L'account di accesso viene archiviato in un tenant gestito da Azure Active Directory (Azure AD). Il nome dell'entità utente (UPN) dell'account di Azure AD in genere corrisponde all'indirizzo di posta elettronica.

**D**: Dove sono archiviate le credenziali? <br/>
**R**: Le credenziali immesse per un'origine dati vengono crittografate e archiviate nel servizio cloud gateway. Le credenziali vengono quindi decrittografate nel gateway dati locale.

**D**: Sono previsti requisiti per la larghezza di banda della rete? <br/>
**R**: È consigliabile che la connessione di rete abbia una buona velocità effettiva. Ogni ambiente è diverso e la quantità di dati inviati influisce sui risultati. L'uso di ExpressRoute può contribuire a garantire un livello di velocità effettiva tra i data center di Azure e quelli locali.
Lo strumenti di terze parti Azure Speed Test può aiutare a valutare la velocità effettiva.

**D**: Qual è la latenza per l'esecuzione di query a un'origine dati dal gateway? Qual è l'architettura ottimale? <br/>
**R**: Per ridurre la latenza di rete, installare il gateway il più vicino possibile all'origine dati. Installando il gateway nell'origine dati effettiva, la latenza introdotta risulterà ridotta al minimo grazie a questa prossimità. Si considerino anche i data center. Se, ad esempio, il servizio usa il data center degli Stati Uniti occidentali e SQL Server è ospitato in una macchina virtuale di Azure, è opportuno che anche la macchina virtuale di Azure sia ubicata negli Stati Uniti occidentali. Grazie a questa prossimità la latenza è ridotta al minimo e si evitano addebiti relativi ai dati in uscita sulla macchina virtuale di Azure.

**D**: In che modo i risultati vengono inviati al cloud? <br/>
**R**: I risultati vengono inviati tramite il bus di servizio di Azure.

**D**: Esistono connessioni in ingresso al gateway dal cloud? <br/>
**R**:  No. Il gateway usa le connessioni in uscita al bus di servizio di Azure.

**D**: Cosa accade se si bloccano le connessioni in uscita? Cosa fare per sbloccarle? <br/>
**R**: Vedere le porte e gli host usati dal gateway.

**D**: Come viene chiamato il servizio Windows effettivo?<br/>
**R**: In Servizi il gateway è denominato servizio gateway dati locale.

**D**: Il servizio gateway di Windows può essere eseguito con un account Azure Active Directory? <br/>
**R**:  No. Il servizio Windows deve disporre di un account Windows valido. Per impostazione predefinita, il sevizio viene eseguito con il SID servizio NT SERVICE\PBIEgwService.

**D**: Come si acquisisce la proprietà di un gateway? <br/>
**R**: Per acquisire la proprietà di un gateway (selezionando Installa/Cambia in Pannello di controllo > Programmi), è necessario essere un proprietario della risorsa gateway in Azure e avere la chiave di ripristino. I proprietari delle risorse gateway sono configurabili nel controllo di accesso.

### <a name="high-availability"></a>Disponibilità elevata e ripristino di emergenza

**D**: Com'è possibile sfruttare la disponibilità elevata?  
**R**: È possibile installare un gateway in un altro computer per creare un cluster. Per altre informazioni, vedere [Cluster a disponibilità elevata per il gateway dati locale](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters) nella documentazione di Power BI Gateway.

**D**: Quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**R**: È possibile usare la chiave di ripristino per ripristinare o spostare un gateway. La chiave di ripristino viene specificata al momento dell'installazione del gateway.

**D**: Quale vantaggio offre la chiave di ripristino? <br/>
**R**: La chiave di ripristino consente di eseguire la migrazione o di ripristinare le impostazioni del gateway in caso di emergenza.

## <a name="troubleshooting"> </a>Risoluzione dei problemi

**D**: Perché il gateway non compare nell'elenco di istanze del gateway quando si tenta di creare la risorsa del gateway in Azure? <br/>
**R**: Le ragioni possono essere due. La prima è che per il gateway è già stata creata una risorsa nella sottoscrizione corrente o in un'altra sottoscrizione. Per eliminare tale possibilità, enumerare le risorse del tipo **Gateway dati locali** dal portale. Assicurarsi di selezionare tutte le sottoscrizioni durante l'enumerazione di tutte le risorse. Una volta creata la risorsa, il gateway non verrà visualizzato nell'elenco di istanze del gateway nell'esperienza di creazione di una risorsa gateway del portale. La seconda possibilità è che l'identità di Azure AD dell'utente che ha installato il gateway sia diversa da quella dell'utente connesso al portale di Azure. Per risolvere questo problema, accedere al portale con lo stesso account dell'utente che ha installato il gateway.

**D**: Come è possibile visualizzare le query inviate all'origine dati locale? <br/>
**R**: È possibile abilitare la funzione di tracciamento delle query, che include le query inviate. Dopo aver risolto il problema, ripristinare il valore originale per il tracciamento delle query. Se il tracciamento delle query non viene disabilitato, si creeranno dei log più grandi.

È anche possibile usare gli strumenti per il tracciamento delle query di cui è dotata l'origine dati. Ad esempio, è possibile usare Eventi estesi o SQL Profiler per SQL Server e Analysis Services.

**D**: Dove si trovano i log del gateway? <br/>
**R**: Vedere la sezione Log più avanti in questo articolo.

### <a name="update"></a>Aggiornare alla versione più recente

Quando la versione del gateway non è aggiornata, possono emergere numerosi problemi. Come buona pratica, assicurarsi di usare la versione più recente. Se il gateway non è stato aggiornato per un mese o più è consigliabile installarne la versione più recente e verificare se è possibile riprodurre il problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Errore: Impossibile aggiungere l'utente al gruppo. (Utenti log delle prestazioni -2147463168 PBIEgwService)

Questo errore viene visualizzato se si sta tentando di installare il gateway in un controller di dominio, un'operazione non consentita. Assicurarsi di distribuire il gateway in un computer che non sia un controller di dominio.

## <a name="logs"></a>Log

I file di log sono di fondamentale importanza per la risoluzione dei problemi.

#### <a name="enterprise-gateway-service-logs"></a>Log del servizio gateway Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Log di configurazione

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`

#### <a name="event-logs"></a>Log eventi

I log di Gateway di gestione dati e PowerBIGateway sono reperibili in **Registri applicazioni e servizi**.

## <a name="next-steps"></a>Passaggi successivi
* [Installare e configurare un gateway dati locale](analysis-services-gateway-install.md).   
* [Gestire Analysis Services](analysis-services-manage.md)
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)
