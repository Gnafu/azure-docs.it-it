---
title: Presentazione dell'interfaccia utente di Azure IoT Central | Microsoft Docs
description: I generatori devono acquisire familiarità con le aree principali dell'interfaccia utente di Azure IoT Central usata per creare una soluzione IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c4eba3e08e403243e76ac891d3bfcb59f12cad85
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211916"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Presentazione dell'interfaccia utente di Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

In questo articolo viene presentata l'interfaccia utente di Microsoft Azure IoT Central. È possibile usare l'interfaccia utente per creare, gestire e usare una soluzione Azure IoT Central e i relativi dispositivi connessi.

I _generatori_ usano l'interfaccia utente di Azure IoT Central per definire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

- Definire i tipi di dispositivo che si connettono alla soluzione.
- Configurare le regole e le azioni per i dispositivi.
- Personalizzare l'interfaccia utente per un _operatore_ che usa la soluzione.

Gli _operatori_ usano l'interfaccia utente di Azure IoT Central per gestire la soluzione Azure IoT Central. È possibile usare l'interfaccia utente per:

- Eseguire il monitoraggio dei dispositivi.
- Configurare i dispositivi.
- Risolvere e correggere i problemi relativi ai dispositivi.
- Eseguire il provisioning di nuovi dispositivi.

## <a name="use-the-left-navigation-menu"></a>Usare il menu di spostamento a sinistra

Usare il menu di spostamento a sinistra per accedere alle diverse aree dell'applicazione. È possibile espandere o comprimere la barra di spostamento selezionando **<** o **>** :

:::row:::
  :::column span="":::
      ![Left navigation menu](media/overview-iot-central-tour/navigationbar.png)
  :::column-end:::
  :::column span="2":::

      **Dashboard** displays your application dashboard. As a builder, you can customize the dashboard for your operators. Users can also create their own  dashboards.
    
      **Device Explorer** lists the simulated and real devices associated with each device template in the application. As an operator, you use the **Device Explorer** to manage your connected devices.
    
      **Device Sets** enables you to view and create device sets. As an operator, you can create device sets as a logical collection of devices specified by a query.
    
      **Analytics** shows analytics derived from device telemetry for devices and device sets. As an operator, you can create custom views on top of device data to derive insights from your application.
    
      **Jobs** enables bulk device management by having you create and run jobs to perform updates at scale.
    
      **Device Templates** shows the tools a builder uses to create and manage device templates.
    
      **Continuous Data Export** enables an administrator to configure a continuous export to other Azure services such as storage and queues.
    
      **Administration** shows the application administration pages where an administrator can manage application settings, users, and roles.
   :::column-end:::
:::row-end:::

## <a name="search-help-and-support"></a>Ricerca, guida e supporto tecnico

Il menu superiore viene visualizzato in ogni pagina:

![Barra degli strumenti](media/overview-iot-central-tour/toolbar.png)

- Per cercare modelli di dispositivo e dispositivi, immettere un valore in **Search** (Cerca).
- Per cambiare la lingua o il tema dell'interfaccia utente, scegliere l'icona **Settings** (Impostazioni).
- Per disconnettersi dall'applicazione, scegliere l'icona **Account**.
- Per accedere alla guida e al supporto tecnico, fare clic sul menu a discesa **Help** (Guida) per visualizzare un elenco di risorse. In una versione di valutazione dell'applicazione le risorse di supporto includono l'accesso alla [live chat](howto-show-hide-chat.md).

È possibile scegliere tra un tema scuro o un tema chiaro per l'interfaccia utente:

![Scegliere un tema per l'interfaccia utente](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> L'opzione per scegliere il tema chiaro o scuro non è disponibile se l'amministratore ha configurato un tema personalizzato per l'applicazione.

## <a name="dashboard"></a>dashboard

![dashboard](media/overview-iot-central-tour/homepage.png)

* Il dashboard è la prima pagina visualizzata quando si accede all'applicazione Azure IoT Central. I creatori possono personalizzare il dashboard dell'applicazione per altri utenti aggiungendo riquadri. Per altre informazioni, vedere l'esercitazione sulla [personalizzazione della visualizzazione operatore di Azure IoT Central](tutorial-customize-operator.md).

* Gli operatori possono creare dashboard personalizzati e spostarsi tra di essi e il dashboard predefinito. Per altre informazioni, vedere [Creare e gestire i dashboard personali](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Esplora dispositivi

![Pagina Explorer](media/overview-iot-central-tour/explorer.png)

La pagina Explorer visualizza i _dispositivi_ presenti nell'applicazione Azure IoT Central raggruppati per _modello di dispositivo_.

* Un modello di dispositivo consente di definire un tipo di dispositivo in grado di connettersi all'applicazione. Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type.md).
* Per dispositivo si intende un dispositivo simulato o reale presente nell'applicazione. Per altre informazioni, vedere l'esercitazione sull'[aggiunta di un nuovo dispositivo all'applicazione Azure IoT Central](tutorial-add-device.md).

## <a name="device-sets"></a>Device Set (Set di dispositivi)

![Pagina Device Sets (Set di dispositivi)](media/overview-iot-central-tour/devicesets.png)

La pagina _Device Sets_ (Set di dispositivi) visualizza i set di dispositivi creati dal generatore. Per set di dispositivi si intende una raccolta di dispositivi correlati. I generatori definiscono una query per identificare i dispositivi inclusi in un set di dispositivi. I set di dispositivi vengono usati durante la personalizzazione dell'analisi nell'applicazione. Per altre informazioni, vedere l'articolo sull'[uso di set di dispositivi nell'applicazione Azure IoT Central](howto-use-device-sets.md).

## <a name="analytics"></a>Analytics

![Pagina Analytics (Analisi)](media/overview-iot-central-tour/analytics.png)

La pagina Analytics (Analisi) visualizza grafici che consentono di comprendere il comportamento dei dispositivi connessi all'applicazione. Gli operatori usano questa pagina per controllare e analizzare i problemi relativi ai dispositivi connessi. I generatori possono definire i grafici visualizzati in questa pagina. Per altre informazioni, vedere l'articolo sulla [creazione di analisi personalizzate per l'applicazione Azure IoT Central](howto-use-device-sets.md).

## <a name="jobs"></a>Processi

![Pagina dei processi](media/overview-iot-central-tour/jobs.png)

La pagina dei processi consente di eseguire operazioni di gestione dei dispositivi in blocco nei propri dispositivi. Il generatore usa questa pagina per aggiornare le proprietà, le impostazioni e i comandi del dispositivo. Per altre informazioni, vedere l'articolo [Eseguire un processo](howto-run-a-job.md).

## <a name="device-templates"></a>Modelli di dispositivo

![Pagina Modelli di dispositivo](media/overview-iot-central-tour/templates.png)

Questa pagina consente a un generatore di creare e gestire i modelli di dispositivo nell'applicazione. Un modello di dispositivo specifica le caratteristiche del dispositivo, come:

- Dati di telemetria, stato e misure di evento.
- Impostazioni e proprietà.
- Comandi.
- Regole basate su eventi o valori di telemetria.

Per altre informazioni, vedere l'esercitazione sulla [definizione di un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type.md).

## <a name="continuous-data-export"></a>Esportazione dati continua

![Pagina Esportazione dati continua](media/overview-iot-central-tour/export.png)

Questa pagina consente a un amministratore di definire come eseguire lo streaming dei dati, ad esempio di telemetria, dall'applicazione. I dati esportati possono essere archiviati in altri servizi o usati per l'analisi. Per altre informazioni, vedere l'articolo [Esportare i dati in Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Administration

![Pagina Administration (Amministrazione)](media/overview-iot-central-tour/administration.png)

La pagina Administration (Amministrazione) contiene i collegamenti agli strumenti usati da un amministratore, ad esempio per definire utenti e ruoli nell'applicazione e personalizzare l'interfaccia utente. Per altre informazioni, vedere l'articolo sull'[amministrazione dell'applicazione Azure IoT Central](howto-administer.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver letto la panoramica di Azure IoT Central e aver acquisito familiarità con il layout dell'interfaccia utente, i passaggi successivi consigliati consistono nel completare la guida introduttiva [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central.md).