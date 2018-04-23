---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/03/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 81357bce92bb8bd2f77f7aaabc8e3b1d49047a1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
**Ultimo aggiornamento del documento**: 3 aprile, 15.00 PST.

La divulgazione recente di una [nuova classe di vulnerabilità della CPU](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), definita attacchi del canale laterale per l'esecuzione speculativa, ha suscitato molte domande e richieste di chiarimenti da parte dei clienti.  

Microsoft ha distribuito soluzioni di mitigazione dei rischi in tutti i servizi cloud. L'infrastruttura che esegue Azure e isola i carichi di lavoro dei clienti gli uni da gli altri è protetta.  Gli altri clienti in esecuzione su Azure non possono quindi attaccare l'applicazione di un utente tramite queste vulnerabilità.

Azure sta incrementando l'uso della [manutenzione con mantenimento della memoria](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) quando possibile, sospendendo la VM per un massimo di 30 secondi durante l'aggiornamento dell'host o spostandola in un host già aggiornato.  La manutenzione con mantenimento della memoria riduce ulteriormente l'effetto sugli utenti ed elimina la necessità di un riavvio.  Azure userà questi metodi durante l'esecuzione di aggiornamenti dell'intero sistema nell'host.

> [!NOTE] 
> A fine febbraio 2018 Intel Corporation ha pubblicato la versione aggiornata del documento [Microcode Revision Guidance](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) sullo stato delle versioni del microcodice, che migliorano la stabilità e riducono le vulnerabilità rilevate dal team [Project Zero di Google](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). I metodi di attenuazione dei rischi implementati da Azure il [3 gennaio 2018](https://azure.microsoft.com/en-us/blog/securing-azure-customers-from-cpu-vulnerability/) non sono interessati dall'aggiornamento del microcodice Intel. Microsoft ha già implementato soluzioni complesse di mitigazione dei rischi per proteggere i clienti di Azure dalle altre macchine virtuali di Azure.  
>
> Il microcodice Intel è in grado di gestire la variante 2 della vulnerabilità Spectre ([CVE-2017-5715](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715) o inserimento destinazione diramazione) per proteggere i sistemi da attacchi efficaci solo in caso di esecuzione di carichi di lavoro condivisi o non attendibili all'interno di macchine virtuali in Azure. I tecnici stanno testando la stabilità per ridurre al minimo l'impatto sulle prestazioni del microcodice prima di renderlo disponibile ai clienti di Azure.  Dal momento che pochissimi clienti eseguono carichi di lavoro non affidabili nelle rispettive macchine virtuali, la maggior parte dei clienti non dovrà abilitare questa funzionalità dopo il relativo rilascio. 
>
> Questa pagina verrà aggiornata non appena saranno disponibili altre informazioni.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Mantenere aggiornato il sistema operativo

Benché non sia necessario un aggiornamento del sistema operativo per isolare le applicazioni in esecuzione su Azure da altri clienti in esecuzione su Azure, è sempre consigliabile mantenere aggiornate le versioni del sistema operativo. I [rollup per la sicurezza di Windows](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) di gennaio 2018 e versioni successive contengono soluzioni di mitigazione dei rischi per queste vulnerabilità.

Nelle offerte seguenti sono illustrate le azioni consigliate per aggiornare il sistema operativo: 

<table>
<tr>
<th>Offerta</th> <th>Azione consigliata </th>
</tr>
<tr>
<td>Servizi cloud di Azure </td>  <td>Abilitare l'aggiornamento automatico o assicurarsi che sia in esecuzione il sistema operativo Guest più recente.</td>
</tr>
<tr>
<td>Macchine virtuali Linux in Azure</td> <td>Installare aggiornamenti dal provider del sistema operativo, quando disponibili. </td>
</tr>
<tr>
<td>Macchine virtuali Windows in Azure </td> <td>Verificare che sia in esecuzione un'applicazione antivirus supportata prima di installare aggiornamenti del sistema operativo. Contattare il fornitore di software antivirus per ottenere informazioni sulla compatibilità.<p> Installare il [rollup per la sicurezza di gennaio](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Altri servizi PaaS di Azure</td> <td>Non sono necessarie azioni da parte dei clienti che usano questi servizi. Azure aggiorna automaticamente le versioni del sistema operativo. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Indicazioni aggiuntive in caso di esecuzione di codice non attendibile 

Non sono necessarie azioni aggiuntive da parte dei clienti, a meno che il codice eseguito non sia attendibile. Se si consente codice non attendibile, ad esempio se si permette a un cliente di caricare un file binario o un frammento di codice che viene eseguito sul cloud entro l'applicazione, sarà necessario seguire questa procedura aggiuntiva.  


### <a name="windows"></a>Windows 
Se si usa Windows e si esegue l'hosting di codice non attendibile, è necessario abilitare una funzionalità di Windows definita shadowing dell'indirizzo virtuale del kernel, che offre protezione aggiuntiva dalle vulnerabilità del canale laterale per l'esecuzione speculativa, in particolare la variante 3 Meltdown, [CVE-2017-5754](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5754) (o caricamento nella cache di dati non autorizzati). Questa funzionalità viene disattivata per impostazione predefinita e potrebbe influire sulle prestazioni, se abilitata. Seguire le istruzioni disponibili in [Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) per l'abilitazione delle protezioni sul server. Se si eseguono Servizi cloud di Azure, verificare che sia in esecuzione WA-GUEST-OS-5.15_201801-01 o WA-GUEST-OS-4.50_201801-01 (disponibile a partire dal 10 gennaio 2018) e abilitare la chiave del Registro di sistema tramite un'attività di avvio.


### <a name="linux"></a>Linux
Se si usa Linux e si esegue l'hosting di codice non attendibile, è necessario aggiornare anche Linux a una versione più recente che implementa l'isolamento di tabelle di pagina del kernel che separa le tabelle di pagina usate dal kernel da quelle appartenente allo spazio utente. Queste mitigazioni richiedono un aggiornamento del sistema operativo Linux e possono essere ottenute dal provider di distribuzione, se disponibili. Il provider del sistema operativo può segnalare se le protezioni sono abilitate o disabilitate per impostazione predefinita.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protezione dei clienti di Azure dalla vulnerabilità della CPU).
