---
title: Risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali. Comprendere i problemi tipici rilevati e come risolverli.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7d87b72-ee24-4e52-9377-a42f337f76fa
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: windows
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 3308b22606e87853aad7e3d3a3995aab8d1b5401
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803585"
---
# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Risoluzione dei problemi di scalabilità automatica con set di scalabilità di macchine virtuali
**Problema**: è stata creata un'infrastruttura per il ridimensionamento automatico in Azure Resource Manager tramite set di scalabilità di macchine virtuali di Azure, ad esempio distribuendo un modello simile al seguente : https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. Le regole di scalabilità impostate  funzionano ma, indipendentemente dal carico delle VM, il ridimensionamento automatico non funziona.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
Alcuni aspetti da considerare:

* Quanti vCPU sono presenti in ogni macchina virtuale e viene caricato ogni singolo vCPU?
  Il modello di avvio rapido di Azure di esempio riportato sopra ha uno script do_work.php, che carica un singolo vCPU. Se si usa una macchina virtuale di dimensioni superiori a quelle di una macchina virtuale a vCPU singolo come Standard_A1 o D1, è necessario eseguire il carico più volte. Per verificare il numero di vCPU delle macchine virtuali, vedere [Dimensioni delle macchine virtuali in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Quante VM sono presenti nel set di scalabilità di macchine virtuali? Vengono eseguite operazioni su ogni VM?
  
    Un aumento del numero di istanze ha luogo unicamente quando l'uso medio della CPU tra **tutte** le macchine virtuali in un set di scalabilità supera il valore di soglia, in base al tempo definito all'interno delle regole di scalabilità automatica.
* Sono stati persi eventi di scalabilità?
  
    Cercare gli eventi di scalabilità nei log di controllo nel portale di Azure. Potrebbero essersi verificati un aumento e poi una riduzione delle prestazioni non rilevati. È possibile filtrare per "Scalabilità".
  
    ![Log di controllo][audit]
* Le soglie di aumento e riduzione del numero di istanze sono sufficientemente diverse?
  
    Si supponga di impostare una regola per aumentare il numero di istanze quando l'uso medio della CPU è superiore al 50% in cinque minuti e per ridurre il numero di istanze quando l'uso medio della CPU è inferiore al 50%. Questa impostazione può provocare un problema di instabilità quando l'uso della CPU si avvicina a tale soglia e le azioni di ridimensionamento devono aumentare e ridurre costantemente le dimensioni del set. Il servizio di scalabilità automatica prova quindi a risolvere l'instabilità e questo può risolversi in un mancato ridimensionamento. Assicurarsi che le soglie di aumento e riduzione del numero di istanze siano sufficientemente diverse per consentire le azioni di ridimensionamento.
* È stato scritto un modello JSON personalizzato?
  
    È facile commettere errori. È quindi consigliabile partire da un modello funzionante come quello riportato sopra e apportare piccole modifiche incrementali. 
* È possibile eseguire manualmente l'aumento o la riduzione del numero di istanze?
  
    Provare a ridistribuire la risorsa del set di scalabilità di macchine virtuali con un'impostazione di "capacità" diversa, per modificare manualmente il numero di macchine virtuali. Un modello di esempio è il seguente: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing potrebbe essere necessario modificare il modello per assicurarsi che abbia la stessa dimensione della macchina che usa il set di scalabilità. Se è possibile modificare manualmente il numero di macchine virtuali, il problema è limitato alla scalabilità automatica.
* Verificare lo stato delle risorse Microsoft.Compute/virtualMachineScaleSet e Microsoft.Insights in [Esplora risorse di Azure](https://resources.azure.com/)
  
    Esplora risorse di Azure è uno strumento di risoluzione dei problemi indispensabile che mostra lo stato delle risorse di Azure Resource Manager. Fare clic sulla sottoscrizione ed esaminare il gruppo di risorse di cui si sta provando a risolvere i problemi. In Provider di risorse di calcolo esaminare il set di scalabilità di macchine virtuali la relativa visualizzazione delle istanze, che mostra lo stato di una distribuzione. Controllare anche la visualizzazione delle istanze delle macchine virtuali incluse nel set di scalabilità. Accedere quindi al provider di risorse Microsoft.Insights e verificare lo stato delle regole di scalabilità automatica.
* L'estensione della diagnostica funziona e genera dati sulle prestazioni?
  
    **Aggiornamento:** Scalabilità automatica di Azure è stata migliorata per l'uso di una pipeline di metriche basate su host, che non richiede più un'estensione di diagnostica da installare. I paragrafi seguenti non avranno più validità se si crea un'applicazione di scalabilità automatica usando la nuova pipeline. Un esempio di modelli di Azure che sono stati convertiti per l'utilizzo della pipeline su host è disponibile qui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale. 
  
    È consigliabile usare le metriche basate su host per eseguire la scalabilità automatica per i motivi seguenti:
  
  * Meno componenti variabili poiché non è più necessario installare alcuna estensione di diagnostica.
  * Modelli più semplici. È sufficiente aggiungere regole di scalabilità automatica di Insights a un modello del set di scalabilità esistente.
  * Creazione di report più affidabile e avvio più veloce delle nuove macchine virtuali.
    
    Si potrebbe voler usare un'estensione di diagnostica soltanto se ci fosse l'esigenza di scalare/creare report di diagnostica della memoria. Le metriche basate su host non creano report della memoria.
    
    Tenendo questo a mente, continuare a leggere l'articolo solo se si intende usare le estensioni di diagnostica per la scalabilità automatica.
    
    La funzione di scalabilità automatica in Azure Resource Manager può (ma non dovrà più) essere eseguita tramite un'estensione della macchina virtuale denominata estensione Diagnostica. L'estensione invia i dati sulle prestazioni a un account di archiviazione specificato nel modello. I dati vengono quindi aggregati dal servizio Monitoraggio di Azure.
    
    Se il servizio Insights non può leggere i dati delle macchine virtuali, invia un messaggio di posta elettronica. Ad esempio, si riceve un messaggio di posta elettronica se le macchine virtuali non sono attive. Controllare la posta all'indirizzo di posta elettronica specificato al momento della creazione dell'account di Azure.
    
    È anche possibile esaminare i dati personalmente. Esaminare l'account di archiviazione di Azure con uno strumento di esplorazione cloud. Usare ad esempio [Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) per accedere e scegliere la sottoscrizione di Azure usata. Selezionare il nome dell'account di archiviazione di diagnostica a cui si fa riferimento nella definizione dell'estensione della diagnostica nel modello di distribuzione.
    
    ![Cloud Explorer][explorer]
    
    Verrà visualizzata una serie di tabelle in cui vengono archiviati i dati di ogni macchina virtuale. Prendendo ad esempio Linux e la relativa metrica della CPU, esaminare le righe più recenti. Visual Studio Cloud Explorer supporta un linguaggio di query che permette di eseguire una query. Ad esempio, è possibile eseguire una query del tipo "Timestamp gt datetime'2016-02-02T21:20:00Z'" per assicurarsi di ottenere gli eventi più recenti. Il fuso orario è impostato su UTC. I dati visualizzati corrispondono alle regole di scalabilità impostate? Nell'esempio seguente, l'uso della CPU nel computer 20 è aumentato fino al 100% negli ultimi cinque minuti.
    
    ![Tabelle di archiviazione][tables]
    
    Se i dati non sono presenti, il problema riguarda l'estensione della diagnostica in esecuzione nelle macchine virtuali. Se i dati sono presenti, il problema può riguardare le regole di scalabilità oppure il servizio Insights. Verificare lo [Stato di Azure](https://azure.microsoft.com/status/).
    
    Dopo aver seguito questa procedura, qualora ci siano ancora problemi di scalabilità automatica è possibile provare le risorse seguenti: 
    * Leggere i forum in [MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=WAVirtualMachinesforWindows) o [Stack overflow](https://stackoverflow.com/questions/tagged/azure) 
    * Registrare una chiamata del supporto. Sarà necessario, condividere il modello e la visualizzazione dei dati sulle prestazioni.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
