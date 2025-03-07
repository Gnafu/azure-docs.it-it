---
title: Creare una VM Windows suddivisa in zone con il portale di Azure | Microsoft Docs
description: Creare una VM Windows in una zona di disponibilità con il portale di Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 90c6f7106cbc885c513d47bf80a7b1ec218d8c6c
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168565"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Creare una macchina virtuale Windows in una zona di disponibilità con il portale di Azure

Questo articolo descrive come usare il portale di Azure per creare una macchina virtuale Linux in una zona di disponibilità di Azure. Una [zona di disponibilità](../../availability-zones/az-overview.md) è una zona fisicamente separata in un'area di Azure. Usare le zone di disponibilità per proteggere app e dati da un poco probabile errore o perdita di un intero data center.

Per usare una zona di disponibilità, creare la macchina virtuale in un'[area di Azure supportata](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Accedi ad Azure 

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**. 

3. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](faq.md#what-are-the-password-requirements-when-creating-a-vm). Scegliere una località, ad esempio Stati Uniti orientali 2, che supporti le zone di disponibilità. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base sulla VM nel pannello del portale](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Scegliere una dimensione per la VM. Selezionare una dimensione consigliata o filtrare in base alle funzionalità. Confermare che la dimensione è disponibile nella zona che si vuole usare.

    ![Selezionare una dimensione di VM](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Sotto **Impostazioni** > **Disponibilità elevata** selezionare una delle zone numerate nella casella a discesa **Zona di disponibilità**, mantenere invariate le altre impostazioni predefinite e fare clic su **OK**.

    ![Selezionare una zona di disponibilità](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Nella pagina di riepilogo fare clic su **Crea** per avviare la distribuzione della macchina virtuale.

7. La macchina virtuale verrà aggiunta al dashboard del portale di Azure. Una volta completata la distribuzione verrà automaticamente aperto il riepilogo della macchina virtuale.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confermare la zona per il disco gestito e un indirizzo IP

Se la macchina virtuale è distribuita in una zona di disponibilità, viene creato un disco gestito per la macchina virtuale nella stessa zona di disponibilità. Per impostazione predefinita, in questa zona viene creato anche un indirizzo IP pubblico.

È possibile confermare le impostazioni di zona per queste risorse nel portale.  

1. Fare clic su **Gruppi di risorse** e quindi sul nome del gruppo di risorse per la macchina virtuale, ad esempio *myResourceGroup*.

2. Fare clic sul nome della risorsa disco. La pagina **Panoramica** include informazioni dettagliate sulla località e sulla zona di disponibilità della risorsa.

    ![Zona di disponibilità per il disco gestito](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Fare clic sul nome della risorsa dell'indirizzo IP pubblico. La pagina **Panoramica** include informazioni dettagliate sulla località e sulla zona di disponibilità della risorsa.

    ![Zona di disponibilità per l'indirizzo IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare una macchina virtuale in una zona di disponibilità. Altre informazioni sulla [disponibilità](availability.md) per le macchine virtuali di Azure.
