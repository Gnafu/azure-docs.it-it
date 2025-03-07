---
title: Eseguire pacchetti SSIS in Azure Data Factory da SSDT | Microsoft Docs
description: Informazioni su come eseguire pacchetti SSIS in Azure da SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ddf427d7d749dff1af45b3f6f83d20a89e1aae0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678407"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Eseguire pacchetti SSIS in Azure da SSDT
Questo articolo descrive la funzionalità dei progetti SQL Server Integration Services (SSIS) abilitati per Azure in SQL Server Data Tools (SSDT), che consente di eseguire pacchetti in Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF).  È possibile usare questa funzionalità per testare i pacchetti SSIS esistenti prima di eseguire & MAIUSC/migrarli in Azure o per sviluppare nuovi pacchetti SSIS per l'esecuzione in Azure.

Con questa funzionalità è possibile creare un nuovo runtime di integrazione Azure-SSIS o associarne uno esistente ai progetti SSIS, quindi eseguirvi i pacchetti.  Sono supportati i pacchetti in esecuzione da distribuire nel catalogo SSIS (SSISDB) nel modello di distribuzione del progetto e quelli da distribuire in file System/condivisioni file/File di Azure nel modello di distribuzione del pacchetto. 

## <a name="prerequisites"></a>Prerequisiti
Per usare questa funzionalità, scaricare e installare la versione più recente di SSDT con l'estensione di progetti SSIS per Visual Studio da [qui](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) o come programma di installazione autonomo da [qui](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure-abilitare i progetti SSIS
In SSDT è possibile creare nuovi progetti SSIS abilitati per Azure usando il modello di **Integration Services progetto (abilitato per Azure)** .

![Nuovo progetto SSIS abilitato per Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

In alternativa, è possibile abilitare Azure per abilitare i progetti SSIS esistenti facendo clic con il pulsante destro del mouse sul nodo del progetto in Esplora soluzioni pannello di SSDT per visualizzare un menu e quindi selezionando la voce di menu abilitata per **Azure** .

![Azure-Abilita progetto SSIS esistente](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Per abilitare Azure per i progetti SSIS esistenti, è necessario impostare la versione del server di destinazione in modo che sia la versione più recente supportata dal runtime di integrazione SSIS di Azure, attualmente **SQL Server 2017**, quindi, se non è già stato fatto, viene visualizzata una finestra di dialogo a tale scopo.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Connettere i progetti abilitati per Azure a SSIS in Azure Data Factory
Connettendo i progetti abilitati per Azure a SSIS in ADF, è possibile caricare i pacchetti in File di Azure ed eseguirli nel runtime di integrazione Azure-SSIS.  A tale scopo, attenersi alla seguente procedura:

1. Fare clic con il pulsante destro del mouse sul nodo del progetto o **delle risorse di Azure collegate** nel pannello Esplora soluzioni di SSDT per visualizzare un menu e selezionare la voce di menu **Connetti a SSIS in Azure Data Factory** per avviare la **creazione guidata connessione SSIS in ADF**.

   ![Connettersi a SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Nella pagina **Introduzione a SSIS in ADF** esaminare l'introduzione e fare clic sul pulsante **Avanti** per continuare.

   ![Introduzione a SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Nella pagina selezionare un runtime di integrazione **SSIS in ADF** selezionare il file ADF e il runtime di integrazione Azure-SSIS esistenti per eseguire i pacchetti o crearne di nuovi se non sono presenti.
   - Per selezionare il runtime di integrazione Azure-SSIS esistente, selezionare prima di tutto la sottoscrizione di Azure pertinente e l'ADF.
   - Se si seleziona il file ADF esistente che non dispone di un runtime di integrazione SSIS di Azure, fare clic sul pulsante creare un runtime di integrazione **SSIS** per crearne uno nuovo nel portale o nell'app ADF.
   - Se si seleziona la sottoscrizione di Azure esistente in cui non è installato alcun ADF, fare clic sul pulsante **Crea IR SSIS** per avviare la **creazione guidata Integration Runtime**, dove è possibile immettere il percorso e il prefisso per creare automaticamente una nuova Azure Gruppo di risorse, Data Factory e runtime di integrazione SSIS per conto dell'utente, denominato nel modello seguente: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Selezionare IR SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Nella pagina **Seleziona archiviazione di Azure** selezionare l'account di archiviazione di Azure esistente per caricare i pacchetti in file di Azure o crearne uno nuovo, se non è disponibile.
   - Per selezionare l'account di archiviazione di Azure esistente, selezionare prima di tutto la sottoscrizione di Azure pertinente.
   - Se si seleziona la stessa sottoscrizione di Azure del runtime di integrazione SSIS di Azure che non dispone di un account di archiviazione di Azure, fare clic sul pulsante **Crea archiviazione di Azure** per crearne automaticamente uno nuovo per conto dell'utente nella stessa posizione del runtime di integrazione Azure-SSIS, denominato da combinazione di un prefisso del nome del runtime di integrazione Azure-SSIS e della relativa data di creazione.
   - Se si seleziona una sottoscrizione di Azure diversa che non dispone di un account di archiviazione di Azure, fare clic sul pulsante **Crea archiviazione di Azure** per crearne uno nuovo in portale di Azure.
   
   ![Selezionare Archiviazione di Azure](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Fare clic sul pulsante **Connetti** per completare la connessione.  Il runtime di integrazione Azure-SSIS selezionato e l'account di archiviazione di Azure vengono visualizzati nel nodo **risorse di Azure collegate** nel pannello Esplora soluzioni di SSDT.  Verrà anche aggiornato lo stato del runtime di integrazione SSIS di Azure, mentre è possibile gestirlo facendo clic con il pulsante destro del mouse sul relativo nodo per visualizzare un menu e quindi scegliendo la voce di menu **Start\Stop\Manage** che consente di visualizzare il portale o l'app di ADF.

## <a name="execute-ssis-packages-in-azure"></a>Esecuzione di pacchetti SSIS in Azure
### <a name="starting-package-executions"></a>Avvio delle esecuzioni di pacchetti
Dopo aver connesso i progetti a SSIS in ADF, è possibile eseguire i pacchetti nel runtime di integrazione Azure-SSIS.  Sono disponibili due opzioni per avviare le esecuzioni di pacchetti:
-  Fare clic sul pulsante **Start** della barra degli strumenti di SSDT per scorrere un menu e selezionare la voce di menu **Esegui in Azure** 

   ![Esegui in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Fare clic con il pulsante destro del mouse sul nodo pacchetto nel pannello Esplora soluzioni di SSDT per visualizzare un menu e selezionare la voce di menu **Esegui pacchetto in Azure** .

   ![Esegui pacchetto in Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Per eseguire i pacchetti in Azure è necessario avere un runtime di integrazione SSIS di Azure in esecuzione. se il runtime di integrazione Azure-SSIS viene arrestato, viene visualizzata una finestra di dialogo per avviarla.  Escludendo qualsiasi tempo di installazione personalizzato, questo processo dovrebbe essere completato entro 5 minuti, ma potrebbe essere necessario circa 20-30 minuti per l'aggiunta di un runtime di integrazione Azure-SSIS a una rete virtuale.  Dopo aver eseguito i pacchetti in Azure, è possibile arrestare il runtime di integrazione Azure-SSIS per gestire il costo di esecuzione facendo clic con il pulsante destro del mouse sul relativo nodo nel pannello Esplora soluzioni di SSDT per visualizzare un menu e quindi selezionare la voce di menu **Start\Stop\Manage** che porta al portale di ADF/ app a tale scopo.

### <a name="checking-package-execution-logs"></a>Verifica dei log di esecuzione dei pacchetti
Quando si avvia l'esecuzione del pacchetto, il relativo log viene formattato e visualizzato nella finestra di stato di SSDT.  Per un pacchetto con esecuzione prolungata, il log viene aggiornato periodicamente in base ai minuti.  È possibile arrestare l'esecuzione del pacchetto facendo clic sul pulsante Interrompi nella barra degli strumenti di SSDT che lo annullerà immediatamente.  È anche possibile trovare temporaneamente i dati di log non elaborati nel percorso Universal Naming Convention (UNC `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`):, ma verrà pulito dopo un giorno.

### <a name="switching-package-protection-level"></a>Cambio del livello di protezione del pacchetto
L'esecuzione di pacchetti SSIS in Azure non supporta i livelli di protezione**EncryptAllWithUserKey** di **EncryptSensitiveWithUserKey**/.  Di conseguenza, se i pacchetti sono configurati con questi, verranno temporaneamente passati in **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, con password generate in modo casuale quando si caricano i pacchetti in File di Azure per l'esecuzione nel runtime di integrazione Azure-SSIS.

> [!NOTE]
> Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti configurati con i livelli di protezione**EncryptAllWithUserKey** di **EncryptSensitiveWithUserKey**/, è necessario riconfigurare manualmente gli altri pacchetti in usare **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, rispettivamente, prima di eseguire i pacchetti.

Se i pacchetti sono già configurati con i livelli di protezione**EncryptAllWithPassword** di **EncryptSensitiveWithPassword**/, verranno mantenuti invariati, ma continueranno a usare password generate in modo casuale quando si caricano i pacchetti in File di Azure per l'esecuzione nel runtime di integrazione Azure-SSIS.

### <a name="using-package-configuration-file"></a>Uso del file di configurazione del pacchetto
Se si usano i file di configurazione del pacchetto nel modello di distribuzione del pacchetto per modificare i valori delle variabili in fase di esecuzione, i file verranno caricati automaticamente con i pacchetti in File di Azure per l'esecuzione nel runtime di integrazione Azure-SSIS.

### <a name="using-execute-package-task"></a>Utilizzo dell'attività Esegui pacchetto
Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti archiviati in file system locali, è necessario eseguire le seguenti configurazioni aggiuntive:

1. Caricare gli altri pacchetti in File di Azure con lo stesso account di archiviazione di Azure connesso ai progetti e ottenere il nuovo percorso UNC, ad esempio`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Sostituire il percorso del file degli altri pacchetti nella gestione connessione file delle attività Esegui pacchetto con il nuovo percorso UNC
   - Se il computer che esegue SSDT non è in grado di accedere al nuovo percorso UNC, è possibile modificare il percorso del file nel pannello Proprietà della gestione connessione file
   - In alternativa, è possibile usare una variabile per il percorso del file per assegnare il valore corretto in fase di esecuzione

Se i pacchetti contengono attività Esegui pacchetto che fanno riferimento ad altri pacchetti nello stesso progetto, non è necessaria alcuna configurazione aggiuntiva.

## <a name="next-steps"></a>Passaggi successivi
Quando si è soddisfatti dell'esecuzione dei pacchetti in Azure da SSDT, è possibile distribuirli ed eseguirli come attività Esegui pacchetto SSIS nelle pipeline di ADF, vedere [eseguire pacchetti SSIS come attività Esegui pacchetto SSIS nelle pipeline di ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
