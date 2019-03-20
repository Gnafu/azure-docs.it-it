---
title: Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure | Microsoft Docs
description: Informazioni sulla panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni di database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 6e26ae7dd8637a71b9f4e2f6ea1ed6063cf0ec40
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181198"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Panoramica dei prerequisiti per usare il Servizio Migrazione del database di Azure
Esistono diversi prerequisiti obbligatori per assicurare che il Servizio Migrazione del database di Azure venga eseguito senza problemi quando si eseguono migrazioni di database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

I prerequisiti associati all'utilizzo del Servizio Migrazione del database di Azure sono elencati nelle sezioni seguenti.

## <a name="prerequisites-common-across-migration-scenarios"></a>Prerequisiti comuni tra gli scenari di migrazione
In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:
- Creare una rete virtuale per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [ VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verificare che le regole dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure non blocchino le porte di comunicazione seguenti: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
- Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Prerequisiti per la migrazione di un database SQL Server a un database SQL di Azure 
Oltre ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione, ci sono anche altri prerequisiti che si applicano specificatamente a uno scenario o all'altro.

Quando si usa il Servizio Migrazione del database di Azure per eseguire le migrazioni di un database SQL Server a un database SQL di Azure, oltre ai prerequisiti comuni a tutti gli scenari di migrazione, è necessario risolvere i seguenti prerequisiti aggiuntivi:

- Creare un'istanza dell'istanza del database SQL di Azure seguendo le istruzioni riportate nell'articolo [Creare un database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 o versione successiva.
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
- Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per il server di database SQL di Azure per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
- Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.

   > [!NOTE]
   > Per l'elenco completo dei prerequisiti richiesti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni da SQL Server al database SQL di Azure, vedere l'esercitazione [Eseguire la migrazione di SQL Server nel database SQL di Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Prerequisiti per la migrazione di SQL Server in Istanza gestita di database SQL di Azure
- Creare un'istanza di Istanza gestita di database SQL di Azure seguendo le istruzioni riportate nell'articolo [Create an Azure SQL Database Managed Instance in the Azure portal](https://aka.ms/sqldbmi) (Creare un'istanza di Istanza gestita di database SQL di Azure nel portale di Azure).
- Aprire i firewall per consentire il traffico SMB sulla porta 445 per l'intervallo di indirizzi IP o di subnet del Servizio Migrazione del database di Azure.
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine, che per impostazione predefinita è la porta TCP 1433.
- Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Verificare che gli accessi usati per connettersi all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo sysadmin del server.
- Creare una condivisione di rete che può essere usata dal servizio Migrazione del database di Azure per il backup del database di origine.
- Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
- Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il Servizio Migrazione del database di Azure rappresenta le credenziali dell'utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
- Creare un contenitore BLOB e recuperare l'URI della firma di accesso condiviso associato usando la procedura descritta nell'articolo [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Selezionare tutte le autorizzazioni (lettura, scrittura, eliminazione, elenco) nella finestra dei criteri durante la creazione dell'URI SAS.

   > [!NOTE]
   > Per l'elenco completo dei prerequisiti richiesti per usare il Servizio Migrazione del database di Azure per eseguire le migrazioni da SQL Server a lstanza gestita di database SQL di Azure, vedere l'esercitazione [Eseguire la migrazione di SQL Server in Istanza gestita di database SQL di Azure](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Passaggi successivi
Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md). 