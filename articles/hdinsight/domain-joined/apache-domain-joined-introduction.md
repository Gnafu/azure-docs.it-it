---
title: HDInsight - Cluster HDInsight aggiunti al dominio - Azure
description: Informazioni su...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 3fd3a4b8982fe2170726df03bdc884e658d0b0c2
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019489"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Introduzione alla sicurezza Hadoop con i cluster HDInsight aggiunti al dominio

Fino a questo momento, Azure HDInsight ha supportato solo un unico amministratore locale degli utenti. Questo funzionava bene per i team di applicazioni o i reparti di dimensioni ridotte. Man mano che i carichi di lavoro basati su Hadoop hanno continuato ad affermarsi nel settore aziendale, l'esigenza di funzionalità di livello aziendale come autenticazione basata su Active Directory, supporto multiutente e controllo degli accessi in base al ruolo è diventata sempre più importante. Con l'uso di cluster HDInsight aggiunti al dominio, è possibile creare un cluster HDInsight aggiunto a un dominio di Active Directory e configurare un elenco di dipendenti aziendali in grado di autenticarsi tramite Azure Active Directory per accedere al cluster HDInsight. Nessun utente esterno all'azienda può accedere al cluster HDInsight. L'amministratore può configurare il controllo degli accessi in base al ruolo per la sicurezza di Hive usando [Apache Ranger](http://hortonworks.com/apache/ranger/), limitando così l'accesso ai dati solo ai ruoli interessati. Infine, l'amministratore può controllare l'accesso ai dati da parte dei dipendenti e le eventuali modifiche apportate ai criteri di controllo degli accessi, ottenendo in questo modo un elevato livello di governance delle risorse aziendali.

> [!NOTE]
> Le nuove funzionalità descritte in questo articolo sono disponibili solo in versione di anteprima nei seguenti tipi di cluster: Hadoop, Spark e Interactive Query. Oozie ora è abilitato sui cluster aggiunti al dominio. Per poter accedere all'interfaccia utente Web Oozie, gli utenti devono essere abilitati per le operazioni di [tunneling](../hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="benefits"></a>Vantaggi
La protezione aziendale si basa su quattro importanti pilastri: protezione perimetrale, autenticazione, autorizzazione e crittografia.

![I pilastri dei vantaggi dei cluster HDInsight aggiunti al dominio](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Protezione perimetrale
La protezione perimetrale in HDInsight si raggiunge con l'uso di reti virtuali e servizio gateway. Oggi, un amministratore aziendale può creare un cluster HDInsight all'interno di una rete virtuale e usare gruppi di sicurezza di rete (regole del firewall) per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP definiti nelle regole del firewall in entrata potranno comunicare con il cluster HDInsight, fornendo in tal modo la protezione perimetrale. Un altro livello di protezione perimetrale viene garantito grazie al servizio gateway. Il gateway è il servizio che funge da prima linea di difesa per qualsiasi richiesta in entrata nel cluster HDInsight. Accetta la richiesta, poi la verifica e solo a questo punto consente alla richiesta di passare agli altri nodi nel cluster, offrendo protezione perimetrale ad altri nodi di nomi e dati nel cluster.

### <a name="authentication"></a>Authentication
Un amministratore può creare un cluster HDInsight aggiunto al dominio in una [rete virtuale](https://azure.microsoft.com/services/virtual-network/). I nodi del cluster HDInsight verranno aggiunti al dominio gestito dall'azienda. Questo è possibile grazie all'uso dei [servizi di dominio di Azure Active Directory](../../active-directory-domain-services/active-directory-ds-overview.md). Tutti i nodi del cluster sono aggiunti a un dominio gestito dall'azienda. Con questa configurazione, i dipendenti dell'azienda possono accedere ai nodi del cluster usando le credenziali del dominio. Possono usare anche le credenziali del dominio per eseguire l'autenticazione con altri endpoint approvati, ad esempio visualizzazioni di Ambari, ODBC, JDBC, PowerShell e le API REST per interagire con il cluster. L'amministratore ha il pieno controllo sulla limitazione del numero di utenti che interagiscono con il cluster tramite questi endpoint.

### <a name="authorization"></a>Authorization
Una procedura consigliata seguita dalla maggior parte delle aziende è limitare l'accesso a tutte le risorse aziendali da parte dei dipendenti. In modo analogo, con questa versione l'amministratore può definire i criteri di controllo degli accessi in base al ruolo per le risorse del cluster. Ad esempio, l'amministratore può configurare [Apache Ranger](http://hortonworks.com/apache/ranger/) per impostare criteri di controllo degli accessi per Hive. Questa funzionalità garantisce che i dipendenti saranno in grado di accedere solo ai dati di cui hanno bisogno per svolgere il loro lavoro in modo corretto. L'accesso SSH al cluster è limitato solo all'amministratore.

### <a name="auditing"></a>Controllo
Oltre a proteggere i dati e le risorse del cluster HDInsight da utenti non autorizzati, è necessario controllare tutti gli accessi alle risorse del cluster e ai dati per tenere traccia degli accessi non autorizzati o non intenzionali alle risorse. L'amministratore può visualizzare e segnalare tutti gli accessi ai dati e alle risorse del cluster HDInsight. L'amministratore può anche visualizzare e segnalare tutte le modifiche ai criteri di controllo degli accessi eseguite negli endpoint supportati da Apache Ranger. Un cluster HDInsight aggiunto al dominio usa la già nota interfaccia utente di Apache Ranger per effettuare ricerche nei log di controllo. Ranger usa [Apache Solr](http://hortonworks.com/apache/solr/) per l'archiviazione e la ricerca nei log nel back-end.

### <a name="encryption"></a>Crittografia
La protezione dei dati è importante per soddisfare i requisiti aziendali in termini di protezione e conformità; oltre a limitare l'accesso ai dati da parte di dipendenti non autorizzati, è fondamentale anche proteggerli mediante crittografia. Entrambi gli archivi di dati per i cluster HDInsight, il BLOB di archiviazione di Azure e Azure Data Lake, supportano la [crittografia dei dati](../../storage/common/storage-service-encryption.md) inattivi trasparente lato server. La protezione dei cluster HDInsight sarà perfettamente compatibile con questa crittografia lato server della funzionalità dei dati inattivi.

## <a name="next-steps"></a>Passaggi successivi
* [Pianificare i cluster HDInsight aggiunti al dominio](apache-domain-joined-architecture.md).
* [Configurare i cluster HDInsight aggiunti al dominio](apache-domain-joined-configure.md).
* [Gestire i cluster HDInsight aggiunti al dominio](apache-domain-joined-manage.md).
* [Configurare criteri Hive per i cluster HDInsight aggiunti al dominio](apache-domain-joined-run-hive.md).
* [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

