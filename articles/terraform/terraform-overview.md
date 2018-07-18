---
title: Utilizzo di Terraform con Azure
description: Introduzione all'utilizzo di Terraform per il controllo delle versioni e la distribuzione dell'infrastruttura di Azure.
ms.service: virtual-machines-linux
keywords: terraform, devops, panoramica, pianificare, applicare, automatizzare
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5d313bda6a1067e6d023f62fb26704f9aee5c7bf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114473"
---
# <a name="terraform-with-azure"></a>Utilizzo di Terraform con Azure

[Hashicorp Terraform](https://www.terraform.io/) è uno strumento open source per il provisioning e la gestione dell'infrastruttura. Consente di codificare l'infrastruttura nei file di configurazione che descrivono la topologia delle risorse cloud, ad esempio macchine virtuali, account di archiviazione e interfacce di rete. L'interfaccia della riga di comando di Terraform offre un meccanismo semplice per la distribuzione e il controllo della versione dei file di configurazione in Azure o in altri cloud supportati.

Questo articolo descrive i vantaggi dell'utilizzo di Terraform nella gestione dell'infrastruttura di Azure.

## <a name="automate-infrastructure-management"></a>Automatizzare la gestione dell'infrastruttura.

I file di configurazione basati su modello di Terraform consentono di definire, eseguire il provisioning e configurare le risorse di Azure in modo prevedibile e ripetibile. L'automazione dell'infrastruttura ha diversi vantaggi:

- Riduce il rischio di errori umani durante la distribuzione e la gestione dell'infrastruttura.
- Distribuisce lo stesso modello più volte per creare ambienti di sviluppo, test e produzione identici.
- Riduce i costi degli ambienti di sviluppo e test creandoli su richiesta.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Comprendere le modifiche apportate all'infrastruttura prima che vengano applicate 

Quando una topologia di risorsa diventa complessa, comprendere il significato e l'impatto delle modifiche apportate all'infrastruttura può essere difficile.

Terraform offre un'interfaccia della riga di comando che consente agli utenti di convalidare e visualizzare in anteprima le modifiche apportate all'infrastruttura, prima della distribuzione. La visualizzazione in anteprima delle modifiche di infrastruttura in modo sicuro e produttivo presenta diversi vantaggi:
- I membri del team possono collaborare efficacemente conoscendo le modifiche proposte e il relativo impatto.
- È possibile rilevare le modifiche non intenzionali nelle prime fasi del processo di sviluppo.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Distribuire l'infrastruttura su più cloud

Terraform è uno strumento diffuso, usato negli scenari multi-cloud in cui un'infrastruttura simile viene distribuita in Azure e a provider di servizi cloud aggiuntivi o data center locali. Consente agli sviluppatori di usare gli stessi strumenti e file di configurazione per gestire l'infrastruttura su più provider di servizi cloud.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver letto la panoramica su Terraform e i relativi vantaggi, ecco i passaggi successivi suggeriti:

- Per iniziare, [installare Terraform e configurarlo per l'utilizzo in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Creare una macchina virtuale di Azure usando Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Esplorare il [modulo di Azure Resource Manager per Terraform](https://www.terraform.io/docs/providers/azurerm/). 