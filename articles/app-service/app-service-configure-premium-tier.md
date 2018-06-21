---
title: Configurare il livello PremiumV2 per il servizio app di Azure | Microsoft Docs
description: Informazioni su migliorare le prestazioni di un'app Web, un'app per dispositivi mobili o un'app per le API nel servizio app di Azure passando al nuovo piano tariffario PremiumV2.
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 4c157ed905b7dc48c886b26987c164ef9a47f3c3
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714562"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurare il livello PremiumV2 per il servizio app di Azure

Il nuovo piano tariffario **PremiumV2** consente di avere processori più veloci e spazio di archiviazione su unità SSD, nonché di raddoppiare il rapporto tra memoria e core rispetto ai piani tariffari esistenti. I vantaggi relativi alle prestazioni che ne derivano consentono di eseguire le app con un minor numero di istanze e garantiscono quindi un risparmio economico. Questo articolo illustra come creare un'app nel livello **PremiumV2** o passare un'app al livello **PremiumV2**.

## <a name="prerequisites"></a>Prerequisiti

Per passare un'app Web al livello **PremiumV2**, è necessario avere un'app Web nel servizio app di Azure eseguita su un livello inferiore rispetto a **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilità di PremiumV2

Il livello PremiumV2 è attualmente disponibile per il servizio app di Azure solo in _Windows_. I contenitori Linux non sono attualmente supportati.

Il livello PremiumV2 è già disponibile nella maggior parte delle aree di Azure ed è in continua espansione. Per vedere se è disponibile nella propria area, eseguire in [Azure Cloud Shell](../cloud-shell/overview.md) il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Se si riceve un errore durante la creazione dell'app o del piano di servizio app, è probabile che **PremiumV2** non sia disponibile nella propria area.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Creare un'app nel livello PremiumV2

Il piano tariffario di un'app del servizio app è definito nel [piano di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md) su cui è in esecuzione. È possibile creare un piano di servizio app in modo indipendente o nell'ambito della creazione di un'app Web.

Durante la configurazione del piano di servizio app nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> selezionare **Piano tariffario**. 

Selezionare **Produzione**, quindi selezionare **P1V2**, **P2V2** o **P3V2** e fare clic su **Applica**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se le opzioni **P1V2**, **P2V2** e **P3V2** non sono visualizzate, è possibile che **PremiumV2** non sia disponibile nell'area scelta o che si stia configurando un piano di servizio app Linux, che non supporta **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Passare un'app esistente al livello PremiumV2

Prima di passare un'app esistente al livello **PremiumV2**, assicurarsi che **PremiumV2** sia disponibile della propria area. Per informazioni, vedere [Disponibilità di PremiumV2](#availability). Se non è disponibile nella propria area, vedere [Passare a un piano superiore da un'area non supportata](#unsupported).

A seconda dell'ambiente di hosting, il passaggio al livello successivo può richiedere operazioni aggiuntive. 

Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> aprire la pagina dell'app del servizio di app.

Nel riquadro di spostamento sinistro della pagina dell'app del servizio app selezionare **Aumenta prestazioni (piano di servizio app)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selezionare **Produzione**, quindi selezionare **P1V2**, **P2V2** o **P3V2** e fare clic su **Applica**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se l'operazione viene completata correttamente, nella pagina di panoramica dell'app appare l'indicazione che l'app è passata al livello **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se si verifica un errore

In alcuni piani di servizio app non è possibile passare al livello PremiumV2. Se l'operazione di scalabilità verticale restituisce un errore, è necessario creare un nuovo piano di servizio app per l'app.

Creare un piano di servizio app _Windows_ nella stessa area e nello stesso gruppo di risorse in cui si trova l'app del servizio app. Seguire i passaggi descritti in [Creare un'app nel livello PremiumV2](#create) per impostarla sul livello **PremiumV2**. Se si vuole, è possibile usare la stessa configurazione di scalabilità orizzontale definita per il piano di servizio app esistente (numero di istanze, scalabilità automatica e così via).

Aprire di nuovo la pagina dell'app del servizio app. Nel riquadro di spostamento sinistro del servizio app, selezionare **Cambia il piano di servizio app**.

![](media/app-service-configure-premium-tier/change-plan.png)

Selezionare il piano di servizio app creato.

![](media/app-service-configure-premium-tier/select-plan.png)

Al termine dell'operazione di modifica, l'app è in esecuzione sul livello **PremiumV2**.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Passare a un piano superiore da un'area non supportata

Se l'app viene eseguita in un'area in cui **PremiumV2** non è ancora disponibile, per poter usufruire del livello **PremiumV2** è possibile spostare l'app in un'area diversa. Sono disponibili due opzioni:

- Creare un'app in un nuovo piano **PremiumV2** e quindi ridistribuire il codice dell'applicazione. Seguire i passaggi descritti in [Creare un'app nel livello PremiumV2](#create) per impostarla sul livello **PremiumV2**. Se necessario, è possibile usare la stessa configurazione di scalabilità orizzontale definita per il piano di servizio app esistente (numero di istanze, scalabilità automatica e così via).
- Se l'app è già in esecuzione in un livello **Premium** esistente, è possibile clonare l'app con tutte le impostazioni dell'app, le stringhe di connessione e la configurazione di distribuzione.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Nella pagina **Clona app** è possibile creare un piano di servizio app nell'area voluta e specificare le impostazioni da clonare.

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la creazione dell'app nel livello **PremiumV2** con script usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il comando seguente crea un piano di servizio app in _P1V2_. È possibile eseguirlo in Cloud Shell. Le opzioni per `--sku` sono P1V2, _P2V2_ e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

Il comando seguente crea un piano di servizio app in _P1V2_. Le opzioni per `-WorkerSize` sono _Small_, _Medium_ e _Large_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Altre risorse

[Aumentare le prestazioni di un'app in Azure](web-sites-scale.md)  
[Scalare il conteggio delle istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md)