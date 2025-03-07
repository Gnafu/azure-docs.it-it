---
title: Usare Istanze di Azure Container come agente di compilazione Jenkins
description: Informazioni su come usare Istanze di Azure Container come agente di compilazione Jenkins.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: ed000779940d9af7b1384873bf9fddd1cde79c71
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326027"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Usare Istanze di Azure Container come agente di compilazione Jenkins

Istanze di Azure Container offre un ambiente isolato on demand utilizzabile in modalità burst per eseguire carichi di lavoro in contenitori. Grazie a questi attributi, le istanze di contenitore di Azure sono un'ottima piattaforma per l'esecuzione di processi di compilazione di Jenkins su vasta scala. Questo articolo descrive in modo dettagliato come distribuire e usare un server Jenkins preconfigurato con istanze di contenitore di Azure come destinazione di compilazione.

Per altre informazioni sulle istanze di contenitore di Azure, vedere [informazioni sulle istanze di contenitore di Azure][about-aci].

## <a name="deploy-a-jenkins-server"></a>Distribuire un server Jenkins

1. Nel portale di Azure fare clic su **Crea risorsa** e cercare **Jenkins**. Selezionare l'offerta Jenkins con un server di pubblicazione **Microsoft** e selezionare **Crea**.

2. Immettere le informazioni seguenti nel modulo **di base** e fare clic su **OK**.

   - **Nome**: immettere un nome per la distribuzione Jenkins.
   - **User name** (Nome utente): immettere un nome per l'utente amministratore della macchina virtuale Jenkins.
   - **Tipo di autenticazione**: si consiglia una chiave pubblica SSH per l'autenticazione. Se si seleziona questa opzione, incollare una chiave pubblica SSH da usare per accedere alla macchina virtuale Jenkins.
   - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
   - **Gruppo di risorse**: Creare un gruppo di risorse o selezionarne uno esistente.
   - **Località**: selezionare una località per il server Jenkins.

   ![Impostazioni di base per la distribuzione del portale Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Nel modulo **Impostazioni aggiuntive** completare le impostazioni seguenti:

   - **Dimensione**: selezionare l'opzione appropriata per le dimensioni della macchina virtuale Jenkins.
   - **Tipo di disco VM**: specificare **HDD** (unità disco rigido) o **SSD** (Solid-State Drive) per il server Jenkins.
   - **Rete virtuale**: selezionare la freccia se si desidera modificare le impostazioni predefinite.
   - **Subnet**: selezionare la freccia, verificare le informazioni e quindi selezionare **OK**.
   - **Indirizzo IP pubblico**: selezionare la freccia per assegnare un nome personalizzato all'indirizzo IP pubblico, configurare lo SKU e impostare il metodo di assegnazione.
   - **Etichetta del nome di dominio**: specificare un valore per creare un URL completo della macchina virtuale Jenkins.
   - **Tipo di versione Jenkins**: selezionare il tipo di versione desiderato tra le opzioni seguenti: **LTS**, **build settimanale** o **Azure Verified**.

   ![Impostazioni aggiuntive per la distribuzione del portale Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Per l'integrazione dell'entità servizio, selezionare **auto (MSI)** per fare in modo che le [identità gestite per le risorse di Azure][managed-identities-azure-resources] creino automaticamente un'identità di autenticazione per l'istanza di Jenkins. Selezionare l'opzione **Manuale** per fornire le credenziali della propria entità servizio.

5. Gli agenti cloud configurano una piattaforma basata sul cloud per i processi di compilazione di Jenkins. Ai fini di questo articolo selezionare un'**istanza di contenitore di Azure**. Con l'agente cloud delle istanze di contenitore di Azure, ogni processo di compilazione di Jenkins viene eseguito in un'istanza di contenitore.

   ![Impostazioni di integrazione cloud per la distribuzione di Jenkins dal portale](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Dopo aver specificato le impostazioni di integrazione, fare clic su **OK** e quindi di nuovo su **OK** nel riepilogo della convalida. Fare clic su **Crea** nel riepilogo delle **Condizioni per l'utilizzo**. La distribuzione del server Jenkins richiede pochi minuti.

## <a name="configure-jenkins"></a>Configurare Jenkins

1. Nel portale di Azure passare al gruppo di risorse Jenkins, selezionare la macchina virtuale Jenkins e prendere nota del nome DNS.

   ![Nome DNS nei dettagli sulla macchina virtuale di Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Passare al nome DNS della macchina virtuale Jenkins e copiare la stringa SSH restituita.

   ![Istruzioni di accesso a Jenkins con la stringa SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Aprire una sessione del terminale nel sistema di sviluppo e incollare la stringa SSH dall'ultimo passaggio. Aggiornare `username` in base al nome utente specificato durante la distribuzione del server Jenkins.

4. Quando viene stabilita la connessione eseguire il comando seguente per recuperare la password amministratore iniziale:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Lasciare in esecuzione la sessione SSH e il tunnel e passare a `http://localhost:8080` in un browser. Incollare la password amministratore iniziale nella casella e quindi selezionare **Continue** (Continua).

   ![Schermata "Unlock Jenkins" (Sblocca Jenkins) con la casella per la password amministratore](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selezionare **Install suggested plugins** (Installa plug-in consigliati) per installare tutti i plug-in Jenkins consigliati.

   ![Schermata "Customize Jenkins" (Personalizza Jenkins) con "Install suggested plugins" (Installa i plugin consigliati) selezionata](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Creare un nuovo account utente amministratore. Questo account viene usato per l'accesso e l'uso dell'istanza di Jenkins.

   ![Schermata "Create First Admin User" (Crea primo utente amministratore), con le credenziali inserite](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selezionare **Save and Finish** (Salva e completa) e quindi **Start using Jenkins** (Inizia a usare Jenkins) per completare la configurazione.

Jenkins è ora configurato e pronto per compilare e distribuire codice. Per questo esempio, viene usata una semplice applicazione Java per mostrare una compilazione di Jenkins su Istanze di Azure Container.

## <a name="create-a-build-job"></a>Creare un'attività di compilazione

A questo punto, viene creato un processo di compilazione Jenkins per illustrare le compilazioni Jenkins in un'istanza di contenitore di Azure.

1. Selezionare **New item** (Nuovo elemento), assegnare un nome al progetto di compilazione, ad esempio **aci-demo**, selezionare **Freestyle project** (Progetto Freestyle) e fare clic su **OK**.

   ![Finestra per il nome del processo di compilazione e l'elenco dei tipi di progetto](./media/container-instances-jenkins/jenkins-new-job.png)

2. In **General** (Generale) verificare che sia selezionata l'opzione **Restrict where this project can be run** (Limita i casi in cui eseguire il progetto). Immettere **linux** per Label Expression (Espressione etichetta). Questa configurazione assicura che il processo di compilazione venga eseguito nel cloud delle istanze di contenitore di Azure.

   ![Scheda "General" (Generale) con i dettagli di configurazione](./media/container-instances-jenkins/jenkins-job-01.png)

3. In **Build** (Compilazione) selezionare **Add build step** (Aggiungi passaggio di compilazione) e quindi selezionare **Execute Shell** (Esegui shell). Immettere `echo "aci-demo"` come comando.

   ![Scheda "Build" (Compilazione) con le selezioni per l'istruzione di compilazione](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selezionare **Salva**.

## <a name="run-the-build-job"></a>Eseguire il processo di compilazione

Per testare il processo di compilazione e osservare Istanze di Azure Container come piattaforma di compilazione, avviare manualmente una compilazione.

1. Selezionare **Build Now** (Compila) per avviare un processo di compilazione. Per avviare il processo sono necessari alcuni minuti. Verrà visualizzato uno stato simile all'immagine seguente:

   ![Informazione sulla "Build History" (Cronologia di compilazione) con lo stato del processo](./media/container-instances-jenkins/jenkins-job-status.png)

2. Durante l'esecuzione del processo, aprire il portale di Azure ed esaminare il gruppo di risorse Jenkins. Si noterà che è stata creata un'istanza di contenitore. Il processo Jenkins viene eseguito all'interno di questa istanza.

   ![Istanza di contenitore nel gruppo di risorse](./media/container-instances-jenkins/jenkins-aci.png)

3. Poiché Jenkins esegue più processi rispetto al numero configurato di executor Jenkins (2 per impostazione predefinita), vengono create più istanze di contenitore.

   ![Istanze di contenitore appena create](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Dopo aver completato tutti i processi di compilazione, le istanze di contenitore vengono rimosse.

   ![Gruppo di risorse con istanze di contenitore rimosse](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Risoluzione dei problemi del plug-in Jenkins

Se si rilevano bug con i plug-in Jenkins, segnalare un problema in [Jenkins JIRA](https://issues.jenkins-ci.org/) per il componente specifico.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Jenkins in Azure, vedere [Azure e Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md