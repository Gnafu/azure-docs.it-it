---
title: Creare e gestire connessioni ibride | Microsoft Docs
description: Informazioni su come creare una connessione ibrida, gestire la connessione e installare Hybrid Connection Manager. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 584b9d67808e75222d832f045e5d3f93fbb326ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105091"
---
# <a name="create-and-manage-hybrid-connections"></a>Creare e gestire connessioni ibride

> [!IMPORTANT]
> La funzionalità Connessioni ibride di BizTalk è stata ritirata e sostituita dalla funzionalità Connessioni ibride del Servizio app. Per altre informazioni, ad esempio come gestire le connessioni ibride di BizTalk esistenti, vedere [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md).
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Panoramica dei passaggi
1. Creare una a connessione ibrida immettendo il **host name** oppure **FQDN** of the on-premises resource in your private netwoppurek.
2. Collegare App Web di Azure o App per dispositivi mobili di Azure alla connessione ibrida.
3. Installare Gestione connessioni ibride nella risorsa locale e connettersi a una specifica connessione ibrida. Il portale di Azure consente di eseguire l'installazione e la connessione con un solo clic.
4. Gestire le connessioni ibride e le relative chiavi di connessione.

Questi passaggi sono illustrati in questo argomento. 

> [!IMPORTANT]
> È possibile impostare un endpoint della connessione ibrida su un indirizzo IP. Se si usa un indirizzo IP, la risorsa locale può essere raggiunta o meno, a seconda del client. La connessione ibrida dipende dal client che esegue una ricerca DNS. Nella maggior parte dei casi, il **client** è il codice dell'applicazione. Se il client non esegue una ricerca DNS, ovvero non prova a risolvere l'indirizzo IP come se fosse un nome di dominio (x.x.x. x), il traffico non verrà inviato attraverso la connessione ibrida.
> 
> Ad esempio (pseudocodice), è possibile definire **10.4.5.6** come host locale:
> 
> **Lo scenario seguente funziona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on premises host`
> 
> **Lo scenario seguente non funziona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Creare una connessione ibrida
È possibile creare una connessione ibrida in [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md) **o** tramite le [API REST di Servizi BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Informazioni aggiuntive
* È possibile creare più connessioni ibride. Vedere il [servizi BizTalk: Grafico edizioni](biztalk-editions-feature-chart.md) per il numero di connessioni consentite. 
* Ogni connessione ibrida viene creata con una coppia di stringhe di connessione: Chiavi dell'applicazione che inviano e in locale le chiavi che sono in ascolto. Ogni coppia ha una chiave primaria e una chiave secondaria. 

## <a name="LinkWebSite"></a>Collegare un'app per dispositivi mobili o un'App Web del Servizio app di Azure
Per collegare un'app per dispositivi mobili o un'App Web del Servizio app di Azure a una connessione ibrida esistente, selezionare **Usa connessione ibrida esistente** nel pannello Connessioni ibride. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Installare Gestione connessioni ibride nella risorsa locale
Dopo aver creato una connessione ibrida, installare Gestione connessioni ibride nella risorsa locale, disponibile per il download dalle App Web di Azure o dal servizio BizTalk. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
Anche [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md) è una risorsa utile.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Informazioni aggiuntive
* Gestione connessioni ibride può essere installato nei sistemi operativi seguenti:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + e Windows Management Framework 4.0 + richiesti)
  * Windows Server 2012 (Windows Management Framework 4.0 + richiesto)
  * Windows Server 2012 R2
* Dopo aver installato Gestione connessioni ibride, si verifica quanto segue: 
  
  * La connessione ibrida ospitata in Azure viene configurata automaticamente per usare la stringa di connessione dell'applicazione primaria. 
  * La risorsa locale viene configurata automaticamente per usare la stringa di connessione locale primaria.
* Gestione connessioni ibride deve usare una stringa di connessione locale valida per le autorizzazioni. In App Web o in App per dispositivi mobili di Azure deve essere usata una stringa di connessione dell'applicazione valida per le autorizzazioni.
* È possibile ridimensionare le connessioni ibride installando un'altra istanza di Hybrid Connection Manager in un altro server. Configurare il listener locale per utilizzare lo stesso indirizzo come il primo listener locale. In questo caso, il traffico è (round robin) distribuito in modo casuale tra i listener locale attivo. 

## <a name="ManageHybridConnection"></a>Gestire le connessioni ibride

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

Anche [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md) è una risorsa utile.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copiare/rigenerare le stringhe di connessione ibrida

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

Anche [Connessioni ibride del Servizio app di Azure](../app-service/app-service-hybrid-connections.md) è una risorsa utile.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Usare i Criteri di gruppo per controllare le risorse locali usate da una connessione ibrida
1. Scaricare i [modelli amministrativi di Gestione connessioni ibride](https://www.microsoft.com/download/details.aspx?id=42963).
2. Estrarre i file.
3. Nel computer che modifica i Criteri di gruppo eseguire le operazioni seguenti:  
   
   * Copiare i file .ADMX nella cartella *%WINROOT%\PolicyDefinitions*.
   * Copiare i file .ADML nella cartella *%WINROOT%\PolicyDefinitions\en-us*.

Dopo aver copiato i file, usare l'Editor Criteri di gruppo per cambiare i criteri.

## <a name="next"></a>Avanti
[Panoramica delle connessioni ibride](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Vedere anche
[REST API for Managing BizTalk Services on Microsoft Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx) (API REST per la gestione dei servizi BizTalk in Microsoft Azure)  
[Servizi BizTalk: Grafico edizioni](biztalk-editions-feature-chart.md)  
[Creare un servizio BizTalk](biztalk-provision-services.md)  
[Servizi BizTalk: Schede dashboard, monitoraggio e scalabilità](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
