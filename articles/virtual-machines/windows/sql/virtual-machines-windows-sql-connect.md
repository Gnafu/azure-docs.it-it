---
title: Connettersi a una macchina virtuale di SQL Server (Gestione risorse) | Documentazione Microsoft
description: Informazioni su come connettersi a SQL Server in esecuzione su una macchina virtuale di Azure. In questo argomento viene usato il modello di distribuzione classica. Gli scenari variano a seconda della configurazione di rete e della posizione del client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ae5c4cdd76f164d13da349c355a30d8b6dc83058
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102094"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Connettersi a una macchina virtuale SQL Server in Azure

## <a name="overview"></a>Panoramica

Questo argomento descrive la modalità di connessione all'istanza di SQL Server in esecuzione su una macchina virtuale di Azure. Presenta alcuni [scenari di connettività generali](#connection-scenarios) e quindi descrive i [passaggi per la modifica delle impostazioni di connettività da eseguire nel portale](#change). Se è necessario risolvere problemi o configurare la connettività esternamente al portale, vedere la sezione relativa alla [configurazione manuale](#manual) alla fine di questo argomento. 

Per una procedura dettagliata completa del provisioning e della connettività, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scenari di connessione

La modalità di connessione di un client a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e della configurazione di rete.

Se si esegue il provisioning di una VM di SQL Server nel portale di Azure, è possibile specificare il tipo di **connettività SQL**.

![Opzione di connettività SQL pubblica durante il provisioning](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Le opzioni per la connettività sono:

| Opzione | Descrizione |
|---|---|
| **Pubblica** | Connettersi a SQL Server tramite Internet |
| **Privata** | Connettersi a SQL Server nella stessa rete virtuale |
| **Locale** | Connettersi a SQL Server localmente sulla stessa macchina virtuale | 

Le sezioni seguenti illustrano le opzioni **Pubblica** e **Privata** in modo più dettagliato.

## <a name="connect-to-sql-server-over-the-internet"></a>Connettersi a SQL Server tramite Internet

Se ci si vuole connettere al motore di database di SQL Server da Internet, durante il provisioning selezionare il tipo di **Connettività SQL** **Pubblica** nel portale. Il portale esegue automaticamente la procedura seguente:

* Abilita il protocollo TCP/IP per SQL Server.
* Configura una regola del firewall per l'apertura della porta TCP di SQL Server (valore predefinito 1433).
* Abilita l'autenticazione di SQL Server, necessaria per l'accesso pubblico.
* Configura il gruppo di sicurezza di rete nella VM su tutto il traffico TCP sulla porta di SQL Server.

> [!IMPORTANT]
> Le immagini delle macchine virtuali per SQL Server Express Edition e SQL Server Developer Edition non abilitano automaticamente il protocollo TCP/IP. Per Developer Edition ed Express Edition è necessario usare Gestione configurazione SQL Server per [abilitare manualmente il protocollo TCP/IP](#manualtcp) dopo avere creato la VM.

Tutti i client con accesso a Internet potranno connettersi all'istanza di SQL Server specificando l'indirizzo IP pubblico della macchina virtuale o l'eventuale etichetta DNS assegnata a tale indirizzo IP. Se la porta di SQL Server è 1433, non è necessario specificarla nella stringa di connessione. La stringa di connessione seguente consente la connessione a una VM SQL con etichetta DNS corrispondente a `sqlvmlabel.eastus.cloudapp.azure.com` tramite l'autenticazione SQL (è anche possibile usare l'indirizzo IP pubblico).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Sebbene in questo modo venga abilitata la connettività per i client tramite Internet, ciò non significa che chiunque può connettersi all'istanza di SQL Server. I client esterni dovranno disporre del nome utente e della password corretti. Per maggiore sicurezza, tuttavia, è possibile evitare la porta 1433 nota. Se, ad esempio, SQL Server è configurato per l'ascolto sulla porta 1500 e sono state stabilite regole del gruppo di sicurezza di rete e del firewall corrette, è possibile connettersi aggiungendo il numero di porta al nome del server. L'esempio seguente modifica il precedente tramite l'aggiunta del numero di porta personalizzato **1500** al nome del server:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando si invia una query a SQL Server in una VM via Internet, tutti i dati in uscita dal centro dati di Azure sono soggetti ai normali [prezzi dei trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Connettersi a SQL Server all'interno di una rete virtuale

Se nel portale si sceglie **Privata** in **Connettività SQL**, Azure configura la maggior parte delle impostazioni in modo identico rispetto a **Pubblica**. L'unica differenza è l'assenza di una regola del gruppo di sicurezza di rete che consente il traffico esterno sulla porta di SQL Server (valore predefinito 1433).

> [!IMPORTANT]
> Le immagini delle macchine virtuali per SQL Server Express Edition e SQL Server Developer Edition non abilitano automaticamente il protocollo TCP/IP. Per Developer Edition ed Express Edition è necessario usare Gestione configurazione SQL Server per [abilitare manualmente il protocollo TCP/IP](#manualtcp) dopo avere creato la VM.

La connettività privata viene spesso usata in combinazione con una [rete virtuale](../../../virtual-network/virtual-networks-overview.md), che consente diversi scenari. È possibile connettere le VM nella stessa rete virtuale, anche se si trovano in gruppi di risorse diversi. Con una [VPN da sito a sito](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)è possibile creare un'architettura ibrida che connette le macchine virtuali a computer e reti locali.

Le reti virtuali consentono inoltre di aggiungere le macchine virtuali di Azure a un dominio. Si tratta dell'unico modo per usare l'autenticazione di Windows per SQL Server. Gli altri scenari di connessione richiedono l'autenticazione SQL con nomi utente e password.

Presupponendo che il DNS sia stato configurato nella rete virtuale, è possibile connettersi all'istanza di SQL Server specificando il nome computer della VM di SQL Server nella stringa di connessione. L'esempio seguente presuppone che sia stata configurata anche l'autenticazione di Windows e che all'utente sia stato concesso l'accesso all'istanza di SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>Modificare le impostazioni di connettività SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare le impostazioni di connettività per la macchina virtuale di SQL Server nel portale di Azure.

1. Nella portale di Azure selezionare **macchine virtuali SQL**.

2. Selezionare la VM di SQL Server.

3. In **Impostazioni**selezionare **sicurezza**.

4. Modificare il valore di **Livello di connettività SQL** sull'impostazione necessaria. Facoltativamente, è possibile usare quest'area per modificare la porta di SQL Server o le impostazioni di autenticazione SQL.

   ![Modificare la connettività SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Attendere qualche minuto che l'aggiornamento venga completato.

   ![Notifica dell'aggiornamento della VM SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a>Abilitare TCP/IP per SQL Server Developer Edition ed Express Edition

Quando si modificano le impostazioni di connettività di SQL Server, per SQL Server Developer Edition ed Express Edition Azure non abilita automaticamente il protocollo TCP/IP. La procedura seguente illustra come abilitare manualmente TCP/IP per potersi connettere in remoto in base all'indirizzo IP.

Connettersi prima al computer di SQL Server tramite Desktop remoto.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Abilitare quindi il protocollo TCP/IP con **Gestione configurazione SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Connettersi con SSMS

La procedura seguente illustra come creare un'etichetta DNS facoltativa per la VM di Azure e quindi come connettersi con SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Configurazione manuale e risoluzione dei problemi

Benché il portale offra opzioni per la configurazione automatica della connettività, è utile saper configurare manualmente la connettività. Anche l'identificazione dei requisiti può semplificare la risoluzione dei problemi.

La tabella seguente elenca i requisiti per la connessione a SQL Server in esecuzione in una macchina virtuale di Azure.

| Requisito | Descrizione |
|---|---|
| [Abilitare la modalità di autenticazione di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | L'autenticazione di SQL Server è necessaria per connettersi alla macchina virtuale in remoto, a meno che non sia stato configurato Active Directory in una rete virtuale. |
| [Creare un account di accesso SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Se si usa l'autenticazione SQL, è necessario un account di accesso SQL con un nome utente e una password dotato delle autorizzazioni per il database di destinazione. |
| [Abilitare il protocollo TCP/IP](#manualtcp) | SQL Server deve consentire le connessioni tramite TCP. |
| [Abilitare una regola del firewall per la porta di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Il firewall nella macchina virtuale deve consentire il traffico in ingresso sulla porta di SQL Server (porta predefinita: 1433). |
| [Creare una regola del gruppo di sicurezza di rete per la porta TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | È necessario consentire alla macchina virtuale di ricevere il traffico sulla porta di SQL Server (porta predefinita: 1433) se si vuole connettersi tramite Internet. Le connessioni solo a reti virtuali non richiedono questo passaggio. Questo è l'unico passaggio necessario nel portale di Azure. |

> [!TIP]
> I passaggi indicati nella tabella seguente vengono eseguiti automaticamente durante la configurazione della connettività nel portale. Usare questi passaggi solo per confermare la configurazione o per configurare manualmente la connettività per SQL Server.

## <a name="next-steps"></a>Fasi successive

Per la procedura di configurazione della connettività e del provisioning, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).