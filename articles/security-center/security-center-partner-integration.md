---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 1abf9efb5c0bed205ce5b87b1f055c14a11ce9ec
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245016"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. I vantaggi includono:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per le soluzioni come quelle antimalware e di valutazione delle vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente necessario nelle macchine virtuali, mentre per gli appliance firewall il Centro sicurezza può occuparsi di gran parte delle attività di configurazione di rete necessarie.
- **Rilevamenti integrati**: gli eventi di sicurezza delle soluzioni dei partner vengono raccolti, aggregati e visualizzati automaticamente come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente le soluzioni di sicurezza integrate includono:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) e [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))
- Web application firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) e [gateway applicazione di Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall di nuova generazione ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) e [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Valutazione delle vulnerabilità ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) e [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> Centro sicurezza non installa Microsoft Monitoring Agent nelle appliance virtuali dei partner perché la maggior parte dei fornitori di soluzioni di sicurezza non consente che vengano eseguiti agenti esterni nelle proprie appliance.
>
>


| Endpoint Protection               | Piattaforme                             | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | No, predefinito nel sistema operativo           | Yes                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 | Tramite estensione                | Yes                       |
| Trend Micro – Tutte le versioni         | Famiglia Windows Server                 | No                            | Yes                       |
| Symantec v12.1.1100+              | Famiglia Windows Server                 | No                            | Yes                       |
| McAfee v10+                       | Famiglia Windows Server                 | No                            | Yes                       |
| Kaspersky                         | Famiglia Windows Server                 | No                            | No                         |
| Sophos                            | Famiglia Windows Server                 | No                            | No                         |



## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, tra cui:

- Azure AD Identity Protection
- Computer in esecuzione in locale o in altri cloud
- Soluzione di sicurezza che supporta Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).

2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

3. Selezionare **Soluzioni di sicurezza** nel menu del Centro sicurezza.

  ![Panoramica del Centro sicurezza](./media/security-center-partner-integration/overview.png)

In **Soluzioni di sicurezza** è possibile visualizzare informazioni sull'integrità delle soluzioni di sicurezza integrate di Azure ed eseguire attività di gestione di base. È anche possibile connettere altri tipi di origini dati di sicurezza, ad esempio log dei firewall e avvisi di protezione di Azure Active Directory Identity Protection nel formato CEF (Common Event Format).

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **Soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al Centro sicurezza e informazioni sullo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Per altre informazioni, vedere [Gestione delle soluzioni connesse dei partner](security-center-partner-solutions.md).

### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure che non sono tuttavia connesse al Centro sicurezza e visualizza le soluzioni nella sezione **Soluzioni individuate**. Sono incluse le soluzioni di Azure, ad esempio [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), e le soluzioni dei partner.

> [!NOTE]
> Il livello Standard del Centro sicurezza è necessario a livello di sottoscrizione per la funzionalità di soluzioni individuate. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

Selezionare **CONNETTI** in una soluzione per integrarla con il Centro sicurezza e ricevere avvisi di sicurezza.

![Discovered solutions (Soluzioni individuate)](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Il Centro sicurezza individua anche le soluzioni distribuite nella sottoscrizione che sono in grado di inoltrare log in formato CEF (Common Event Format). Vedere come [connettere al Centro sicurezza una soluzione di sicurezza](quick-security-solutions.md) che usa i log CEF.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center (Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure)](security-center-ata-integration.md)
* [Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure](security-center-aadip-integration.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md). Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del Centro sicurezza.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
