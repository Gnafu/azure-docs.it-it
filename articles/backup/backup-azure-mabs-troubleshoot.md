---
title: Risolvere i problemi del server di Backup di Azure
description: Risolvere i problemi di installazione e registrazione del server di Backup di Azure ed eseguire backup e ripristino dei carichi di lavoro delle applicazioni.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: kasinh
ms.openlocfilehash: 22507a1b89c6a7d6867e9b669e1a2e70106a4e41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228286"
---
# <a name="troubleshoot-azure-backup-server"></a>Risolvere i problemi del server di Backup di Azure

Fare riferimento alle informazioni elencate nelle tabelle seguenti possibile risolvere gli errori rilevati durante l'uso di server di Backup di Azure.

## <a name="invalid-vault-credentials-provided"></a>Sono state specificate credenziali dell'insieme di credenziali non valide

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Registrazione in un insieme di credenziali | Sono state specificate credenziali di insieme non valide. Il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. | Azione consigliata: <br> <ul><li> Scaricare il file di credenziali più recente dall'insieme e riprovare. <br>OPPURE</li> <li> Se il problema non viene risolto, provare a scaricare le credenziali in una directory locale diversa o creare un nuovo insieme di credenziali. <br>OPPURE</li> <li> Provare ad aggiornare le impostazioni di data e ora come descritto in [questo blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>OPPURE</li> <li> Controllare se il percorso c:\windows\temp contenga più di 65000 file. Spostare i file non aggiornati in un altro percorso o eliminare gli elementi nella cartella Temp. <br>OPPURE</li> <li> Controllare lo stato dei certificati. <br> a. Aprire **Gestisci i certificati computer** nel Pannello di controllo. <br> b. Espandere il nodo **Personale** e il relativo nodo figlio **Certificati**.<br> c.  Rimuovere il certificato **Strumenti di Windows Azure**. <br> d. Ripetere la registrazione nel client di Backup di Azure. <br> OPPURE </li> <li> Verificare se siano applicati eventuali criteri di gruppo. </li></ul> |

## <a name="replica-is-inconsistent"></a>La replica è incoerente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | La replica è incoerente | Verificare che l'opzione relativa alla verifica di coerenza automatica nella procedura guidata Gruppo di protezione sia attivata. Per altre informazioni sulle cause delle incoerenze della replica e i suggerimenti rilevanti, vedere l'articolo di Microsoft TechNet [La replica è incoerente](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> In caso di backup dello stato del sistema o di ripristino bare metal, verificare che Windows Server Backup sia installato nel server protetto.</li><li> Verificare la presenza di problemi di spazio nel pool di archiviazione DPM del server DPM o di Backup di Microsoft Azure e allocare memoria in base alle necessità.</li><li> Controllare lo stato del servizio Copia Shadow del volume nel server protetto. Se è disabilitato, impostarlo per l'avvio manuale. Avviare il servizio sul server. Tornare quindi alla console di DPM o del server di Backup di Microsoft Azure e avviare la sincronizzazione con il processo di verifica della coerenza.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Impossibile creare il punto di ripristino online

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Impossibile creare il punto di ripristino online | **Messaggio di errore**: L'agente di Backup di Microsoft Azure non è riuscito a creare uno snapshot del volume selezionato. <br> **Soluzione alternativa**: provare ad aumentare lo spazio nel volume di replica e del punto di ripristino.<br> <br> **Messaggio di errore**: L'agente di Backup di Microsoft Azure non è riuscito a connettersi al servizio OBEngine. <br> **Soluzione alternativa**: verificare che OBEngine sia presente nell'elenco dei servizi in esecuzione nel computer. Se il servizio OBEngine non è in esecuzione, usare il comando "net start OBEngine" per avviarlo. <br> <br> **Messaggio di errore**: Non è stata impostata la passphrase di crittografia per questo server. Configurare una passphrase di crittografia. <br> **Soluzione alternativa**: provare a configurare una passphrase di crittografia. Se l'operazione non riesce, seguire questa procedura: <br> <ol><li>Verificare che il percorso dei file temporanei esista. Questo è il percorso indicato nel Registro di sistema in **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** con il nome **ScratchLocation**.</li><li> Se il percorso dei file temporanei esiste, provare di nuovo a eseguire la registrazione con la passphrase precedente. *Quando si configura una passphrase di crittografia, salvarla in un luogo sicuro.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>Le credenziali dell'insieme di credenziali fornite non corrispondono all'insieme in cui è registrato il server

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Restore | **Codice errore**: Errore CBPServerRegisteredVaultDontMatchWithCurrent/Credenziali dell'insieme di credenziali: 100110 <br/> <br/>**Messaggio di errore**: Le credenziali dell'insieme di credenziali fornite non corrispondono all'insieme in cui è registrato il server | **Causa**: questo problema si verifica se si cerca di ripristinare file nel server alternativo dal server originale usando l'opzione di ripristino DPM esterno e se il server da ripristinare e il server originale da cui è stato eseguito il backup dei dati non sono associati allo stesso insieme di credenziali di Servizi di ripristino.<br/> <br/>**Soluzione alternativa**: per risolvere questo problema verificare che il server originale e il server alternativo siano registrati nello stesso insieme di credenziali.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>I processi di creazione dei punti di ripristino online per VM VMware non riescono

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | I processi di creazione dei punti di recupero online per VM VMware non riescono. DPM ha riscontrato un errore di VMWare durante il tentativo di ottenere informazioni di rilevamento modifiche. Codice di errore: FileFaultFault (ID 33621) |  <ol><li> Reimpostare il rilevamento modifiche in VMWare per le VM interessate.</li> <li>Controllare che in VMWare non sia presente un disco indipendente.</li> <li>Arrestare la protezione per le VM interessate e riapplicare la protezione con il pulsante **Aggiorna**. </li><li>Eseguire una verifica di coerenza per le VM interessate.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operazione agente non riuscita a causa di un errore di comunicazione con il servizio coordinatore agenti DPM nel server

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Operazione agente non riuscita a causa di un errore di comunicazione con il servizio Coordinatore agenti DPM in \<ServerName>. | **Se l'azione consigliata visualizzata nel prodotto non funziona, seguire questa procedura**: <ul><li> Se si collega un computer da un dominio non trusted, seguire [questa procedura](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> OPPURE </li><li> Se si collega un computer da un dominio trusted, risolvere i problemi mediante i passaggi descritti in [questo blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>OPPURE</li><li> Provare a disabilitare l'antivirus come passaggio per la risoluzione dei problemi. Se il problema viene risolto, modificare le impostazioni dell'antivirus come suggerito in [questo articolo](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Non è stato possibile aggiornare i metadati del Registro di sistema

| Operazione | Dettagli errore | Soluzione alternativa |
|-----------|---------------|------------|
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema ReFS Trimming. | Modificare la chiave del Registro di sistema **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Impostare il valore DWORD su 1. |
|Installazione | Non è stato possibile aggiornare i metadati del Registro di sistema. Questo errore di aggiornamento potrebbe causare un utilizzo eccessivo dell'archiviazione. Per evitare questo problema, aggiornare la voce del Registro di sistema Volume SnapOptimization. | Creare la chiave del Registro di sistema **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** con un valore di stringa vuoto. |

## <a name="registration-and-agent-related-issues"></a>Problemi relativi alla registrazione e all'agente

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Push degli agenti in server protetti | Le credenziali specificate per il server non sono valide. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura**: <br> Provare a installare manualmente l'agente protezione nel server di produzione come specificato in [questo articolo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure (ID: 100050) | L'agente di Backup di Azure non è stato in grado di connettersi al servizio Backup di Azure. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura**: <br>1. Eseguire il comando seguente da un prompt con privilegi elevati: **psexec -i -s "c:\Programmi\Internet Explorer\iexplore.exe**. Verrà aperta la finestra di Internet Explorer. <br/> 2. Passare a **Strumenti** > **Opzioni Internet** > **Connessioni** > **Impostazioni LAN**. <br/> 3. Verificare le impostazioni del proxy per l'account di sistema. Impostare l'IP del Proxy e la porta. <br/> 4. Chiudere Internet Explorer.|
| Installazione dell'agente di Backup di Azure non riuscita | L'installazione di Servizi di ripristino di Microsoft Azure non è riuscita. È stato eseguito il rollback di tutte le modifiche apportate al sistema dall'installazione di Servizi di ripristino di Microsoft Azure. (ID: 4024) | Installare manualmente l'agente di Azure.


## <a name="configuring-protection-group"></a>Configurazione di un gruppo protezione dati

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Configurazione di gruppi di protezione | DPM non è in grado di enumerare il componente dell'applicazione nel computer protetto (nome computer protetto). | Selezionare **Aggiorna** nella schermata dell'interfaccia utente per la configurazione del gruppo protezione dati al livello di origine dati/componente appropriato. |
| Configurazione di gruppi di protezione | Impossibile configurare la protezione | Se il server protetto è un server SQL, verificare che le autorizzazioni del ruolo sysadmin siano state fornite all'account di sistema (NTAuthority\System) del computer protetto come indicato in [questo articolo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurazione di gruppi di protezione | Non c'è spazio sufficiente nel pool di archiviazione per questo gruppo protezione dati. | I dischi aggiunti al pool di archiviazione [non devono contenere una partizione](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Eliminare gli eventuali volumi presenti sui dischi. Quindi aggiungerli al pool di archiviazione.|
| Modifica dei criteri |Non è possibile modificare i criteri di backup. Errore: Impossibile eseguire l'operazione corrente a causa di un errore di servizio interno [0x29834]. Ripetere l'operazione dopo alcuni minuti. Se il problema persiste, contattare il supporto tecnico Microsoft. | **Causa:**<br/>Questo errore si verifica in tre situazioni: quando sono abilitate le impostazioni di sicurezza, quando si prova a ridurre l'intervallo di conservazione dei dati al di sotto dei valori minimi specificati in precedenza e quando si usa una versione non supportata. Le versioni non supportate sono quelle precedenti al server di Backup di Microsoft Azure 2.0.9052 e server di Backup di Azure aggiornamento 1. <br/>**Azione consigliata:**<br/> per procedere con gli aggiornamenti relativi ai criteri, impostare un periodo di conservazione superiore al periodo di conservazione minimo specificato. Il periodo di conservazione minimo è sette giorni per i backup giornalieri, quattro settimane per quelli settimanali, tre settimane per quelli mensili e un anno per quelli annuali. <br><br>Facoltativamente, per sfruttare tutti gli aggiornamenti della sicurezza, un altro approccio consigliato è aggiornare l'agente di backup e il server di Backup di Azure. |

## <a name="backup"></a>Backup

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Backup | Si è verificato un errore imprevisto durante l'esecuzione del processo. Il dispositivo non è pronto. | **Se l'azione consigliata nel prodotto non funziona, seguire questa procedura:** <br> <ul><li>Impostare lo spazio di archiviazione della copia shadow come illimitato negli elementi del gruppo protezione dati e quindi avviare la verifica coerenza.<br></li> OPPURE <li>Provare a eliminare il gruppo protezione dati esistente e a creare più nuovi gruppi. Ogni nuovo gruppo protezione dati deve contenere un singolo elemento.</li></ul> |
| Backup | Se si sta eseguendo il backup solamente dello stato del sistema, verificare che ci sia spazio libero sufficiente nel computer protetto per archiviarlo. | <ol><li>Verificare che Windows Server Backup sia installato nel computer protetto.</li><li>Verificare che vi sia spazio sufficiente nel computer protetto per lo stato del sistema. Il modo più semplice per verificare questa condizione è passare al computer protetto, aprire Windows Server Backup, fare clic sulle selezioni e quindi selezionare il ripristino bare metal. Nell'interfaccia utente verrà indicato lo spazio necessario. Aprire **WSB** > **Backup locale** > **Pianificazione backup** > **Selezione configurazione di backup** > **Server completo** (vengono visualizzate le dimensioni). Usare queste dimensioni per la verifica.</li></ol>
| Backup | Errore di backup per il ripristino bare metal | Se le dimensioni del ripristino bare metal sono grandi, spostare alcuni file di applicazione nell'unità del sistema operativo e riprovare. |
| Backup | L'opzione per proteggere nuovamente una macchina virtuale VMware in un nuovo server di Backup di Microsoft Azure non viene visualizzata come disponibile per l'aggiunta. | Le proprietà VMware fanno riferimento a un'istanza precedente ritirata del server di Backup di Microsoft Azure. Per risolvere il problema:<br><ol><li>in VCenter (equivalente a SC VMM), passare alla scheda **Summary** e quindi a **Custom Attributes**.</li>  <li>Eliminare il vecchio nome del server di Backup di Microsoft Azure dal valore **DPMServer**.</li>  <li>Tornare al nuovo server di Backup di Microsoft Azure e modificare il PG.  Dopo aver selezionato il pulsante **Refresh**, la macchina virtuale verrà visualizzata con una casella di controllo come disponibile per l'aggiunta alla protezione.</li></ol> |
| Backup | Errore durante l'accesso a file o cartelle condivise | Provare a modificare le impostazioni antivirus come indicato nell'articolo di TechNet [Eseguire software antivirus sul server DPM](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Modificare la passphrase

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Modificare la passphrase |Il PIN di sicurezza immesso non è corretto. Specificare il PIN di sicurezza corretto per completare questa operazione. |**Causa:**<br/> Questo errore si verifica quando si immette un PIN di sicurezza non valido o scaduto durante l'esecuzione di un'operazione critica (ad esempio, se si modifica una passphrase). <br/>**Azione consigliata:**<br/> Per completare l'operazione, è necessario immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a **Impostazioni** > **Proprietà** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |
| Modificare la passphrase |Operazione non riuscita. ID: 120002 |**Causa:**<br/>Questo errore si verifica quando sono abilitate le impostazioni di sicurezza o quando si tenta di modificare la passphrase se si usa una versione non supportata.<br/>**Azione consigliata:**<br/> Per modificare la passphrase, è innanzitutto necessario aggiornare l'agente di backup con la versione minima, 2.0.9052. È anche necessario aggiornare il server di Backup di Azure per con la versione minima dell'aggiornamento 1 e quindi immettere un PIN di sicurezza valido. Per ottenere il PIN, accedere al portale di Azure e passare all'insieme di credenziali dei servizi di ripristino. Passare quindi a **Impostazioni** > **Proprietà** > **Genera PIN di sicurezza**. Usare questo PIN per modificare la passphrase. |


## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

| Operazione | Dettagli errore | Soluzione alternativa |
| --- | --- | --- |
| Impostazione delle notifiche di posta elettronica tramite un account di Office 365 |ID errore: 2013| **Causa:**<br> Tentativo di usare l'account di Office 365 <br>**Azione consigliata:**<ol><li> Verificare prima di tutto che l'inoltro anonimo sia consentito in un connettore di ricezione per il server DPM in Exchange. Per altre informazioni su come configurare questa impostazione, vedere [Autorizzazione per l'inoltro anonimo su un connettore di ricezione](https://technet.microsoft.com/library/bb232021.aspx) su TechNet.</li> <li> Se non è possibile usare un inoltro SMTP interno ed è necessario eseguire la configurazione con il server di Office 365, è possibile configurare IIS come inoltro. Configurare il server DPM per [inoltrare SMTP a Office 365 tramite IIS](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **IMPORTANTE:** Assicurarsi di usare l'utente\@dominio.com formato e *non* dominio\utente.<br><br><li>Impostare DPM in modo da usare il nome del server locale come server SMTP e la porta 587. Quindi impostarlo con l'indirizzo di posta elettronica dell'utente da cui dovrebbero provenire i messaggi.<li> Il nome utente e la password nella pagina di configurazione dell'SMTP per DPM devono corrispondere a un account di dominio nel dominio in cui è attivo DPM. </li><br> **NOTA**: quando si modifica l'indirizzo del server SMTP, assicurarsi di apportare le modifiche alle nuove impostazioni, quindi chiudere la finestra delle impostazioni e riaprirla per verificare che rifletta il nuovo valore.  Se ci si limita ad apportare la modifica ed eseguire il test, è possibile che le nuove impostazioni non vengano applicate, pertanto questo tipo di verifica è la procedura consigliata.<br><br>In qualsiasi momento durante il processo è possibile cancellare queste impostazioni chiudendo la console DPM e modificando le chiavi del Registro di sistema seguenti: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Eliminare le chiavi SMTPPassword e SMTPUserName**. Sarà possibile aggiungerle di nuovo nell'interfaccia utente all'avvio successivo.
