---
title: 'Azure Active Directory Domain Services: Introduzione | Microsoft Docs'
description: Abilitare Azure Active Directory Domain Services tramite il portale di Azure
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: c167f40e09ac877d00d348285383106fc5ba41bc
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503911"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Abilitare Azure Active Directory Domain Services tramite il portale di Azure


## <a name="before-you-begin"></a>Prima di iniziare
Fare riferimento a [Considerazioni sulla rete per Azure Active Directory Domain Services](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Attività 2: configurare le impostazioni di rete
L'attività di configurazione successiva consiste nel creare una rete virtuale di Azure e la relativa subnet dedicata. Abilitare Azure Active Directory Domain Services in questa subnet all'interno della rete virtuale. È anche possibile selezionare una rete virtuale esistente e creare la subnet dedicata all'interno di essa.

1. Fare clic su **Rete virtuale** per selezionare una rete virtuale.
    > [!NOTE]
    > **Le reti virtuali classiche non sono supportate per le nuove distribuzioni.** Le reti virtuali classiche non sono supportate per le nuove distribuzioni. I domini gestiti esistenti distribuiti nelle reti virtuali classiche continuano a essere supportati. Nel prossimo futuro Microsoft offrirà la possibilità di eseguire la migrazione di un dominio gestito esistente da una rete virtuale classica a una rete virtuale di Gestione risorse.
    >

2. Nella pagina **Scegli rete virtuale** vengono visualizzate tutte le reti virtuali esistenti. Vengono visualizzate solamente le reti virtuali che appartengono al gruppo di risorse e la posizione di Azure selezionata nella pagina di procedura guidata **Nozioni di base**.
3. Scegliere la rete virtuale in cui abilitare Azure AD Domain Services. È possibile usare una rete virtuale esistente o crearne una nuova.

  > [!TIP]
  > **Non è possibile spostare il dominio gestito in una rete virtuale diversa dopo l'abilitazione di Azure AD Domain Services.** Selezionare la rete virtuale corretta per abilitare il dominio gestito. Dopo aver creato un dominio gestito, è possibile spostarlo in un'altra rete virtuale, senza eliminare il dominio gestito. Prima di procedere, è consigliabile leggere [Considerazioni sulla rete per Azure Active Directory Domain Services](active-directory-ds-networking.md).  
  >

4. **Creare la rete virtuale:** fare clic su **Crea nuovo** per creare una nuova rete virtuale. Usare una subnet dedicata per Azure AD Domain Services. Ad esempio, se si crea una subnet con il nome 'DomainServices', è più semplice per gli altri amministratori comprendere cosa viene implementato all'interno della subnet. Al termine, fare clic su **OK**.

    ![Selezionare una rete virtuale](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > Assicurarsi di selezionare uno spazio di indirizzi che si trova all'interno di spazio di indirizzi IP privato. Gli indirizzi IP di cui non si è proprietari e che si trovano nello spazio di indirizzi pubblici generano errori all'interno di Azure AD Domain Services.

5. **Rete virtuale esistente:** se si seleziona una rete virtuale esistente, [creare una subnet dedicata usando l'estensione di reti virtuali](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), quindi selezionare la subnet. Fare clic su **Rete virtuale** per selezionare la rete virtuale esistente. Fare clic su **Subnet** per selezionare una subnet dedicata nella rete virtuale esistente, nella quale è possibile attivare il nuovo dominio gestito. Al termine, fare clic su **OK**.

    ![Selezionare una subnet all’interno della rete virtuale](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **Linee guida per la selezione di una subnet**
  > 1. Usare una subnet dedicata per Azure AD Domain Services. Non distribuire eventuali altre macchine virtuali per questa subnet. Questa configurazione consente di configurare gruppi di sicurezza di rete per le macchine virtuali/carichi di lavoro senza interromperne il dominio gestito. Per i dettagli vedere [Considerazioni sulla rete per Azure Active Directory Domain Services](active-directory-ds-networking.md).
  2. Non selezionare la subnet del Gateway per l’implementazione di Azure AD Domain Services poiché non si tratta di una configurazione supportata.
  3. La subnet selezionata deve avere almeno da 3 a 5 indirizzi IP disponibili nel proprio spazio di indirizzi.
  >

6. Al termine, fare clic su **OK** per procedere alla pagina **Gruppo amministratore** della procedura guidata.


## <a name="next-step"></a>Passaggio successivo
[Attività 3: configurare un gruppo amministrativo e abilitare Azure AD Domain Services](active-directory-ds-getting-started-admingroup.md)
