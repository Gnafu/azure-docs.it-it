---
title: Configurare il Server Azure MFA per la disponibilità elevata - Azure Active Directory
description: Distribuire più istanze del server Microsoft Azure Multi-Factor Authentication in configurazioni che offrono disponibilità elevata.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43154e428c3208f5d990688554407777d09f2f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056036"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurare il server Azure MFA per la disponibilità elevata

Per ottenere disponibilità elevata con la distribuzione del server Azure MFA, è necessario distribuire più server MFA. Questa sezione contiene informazioni su una progettazione con bilanciamento del carico per ottenere destinazioni a disponibilità elevata nella distribuzione di server Azure MFS.

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offrirà non è più Server MFA per le nuove distribuzioni. Nuovi clienti che si vuole richiedere l'autenticazione mfa agli utenti devono usare Azure multi-Factor Authentication basato sul cloud. I clienti esistenti che hanno attivato il Server MFA prima del 1 ° luglio sarà in grado di scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="mfa-server-overview"></a>Panoramica del server MFA

L'architettura di servizio del server Azure MFA include diversi componenti, come illustrato nel diagramma seguente:

 ![Componenti dell'architettura di Server MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Un server MFA è un'istanza di Windows Server in cui è installato il software Azure Multi-Factor Authentication. Per funzionare, l'istanza del server MFA deve essere attivata dal servizio MFA in Azure. È possibile installare più di un server MFA locale.

Per impostazione predefinita, la prima istanza del server MFA installata rappresenta il server master MFA al momento dell'attivazione da parte del servizio Azure MFA. L'istanza master del server MFA include una copia scrivibile del database PhoneFactor.pfdata. Le installazioni successive delle istanze del Server MFA sono note come subordinate. Le subordinate di MFA sono una copia replicata di sola lettura del database PhoneFactor.pfdata. I server MFA replicano le informazioni mediante Remote Procedure Call (RPC). Tutti i server MFA devono essere collettivamente aggiunti a un dominio o autonomi per replicare informazioni.

Sia il master MFA che i server MFA subordinati comunicano con il servizio MFA quando è necessaria l'autenticazione a due fattori. Ad esempio, quando un utente tenta di accedere a un'applicazione che richiede l'autenticazione a due fattori, l'utente verrà prima autenticato da un provider di identità, ad esempio Active Directory (AD).

In seguito all'autenticazione con AD, il server MFA comunicherà con il servizio MFA. Il server MFA attende la notifica dal servizio MFA per consentire o negare l'accesso dell'utente all'applicazione.

Se l'istanza master del server MFA è offline, è comunque possibile elaborare le autenticazioni, ma non le operazioni che richiedono modifiche al database MFA. (Alcuni esempi: l'aggiunta di utenti, self-service PIN le modifiche, la modifica delle informazioni utente o l'accesso al portale utenti)

## <a name="deployment"></a>Distribuzione

Considerare gli aspetti fondamentali seguenti per eseguire il bilanciamento del carico del server Azure MFA e dei componenti correlati.

* **Uso dello standard RADIUS per conseguire disponibilità elevata**. Se si usano server Azure MFA come server RADIUS, è possibile configurare un server MFA come destinazione di autenticazione RADIUS primaria e altri server Azure MFA come destinazioni di autenticazione secondarie. Questo metodo per conseguire la disponibilità elevata, tuttavia, potrebbe non risultare pratico, poiché è necessario attendere che si verifichi un timeout quando l'autenticazione ha esito negativo nella destinazione di autenticazione primaria prima che sia possibile eseguire l'autenticazione con la destinazione di autenticazione secondaria. È più efficiente eseguire il bilanciamento del carico del traffico RADIUS tra il client RADIUS e i server RADIUS (in questo caso, le istanze del server Azure MFA che operano come server RADIUS) in modo da poter configurare i client RADIUS con un singolo URL a cui possano puntare.
* **Necessità di alzare manualmente di livello le istanze subordinate di MFA**. Se l'istanza master del server Azure MFA è offline, le istanze secondarie del server Azure MFA continuano a elaborare le richieste MFA. Tuttavia, fino a quando è disponibile un'istanza master del server MFA, gli amministratori non possono aggiungere utenti o modificare le impostazioni di autenticazione a più fattori e gli utenti non possono apportare modifiche tramite il portale per gli utenti. L'innalzamento di livello di un MFA subordinato al ruolo di master è sempre un processo manuale.
* **Separabilità dei componenti**. Il server Azure MFA è costituito da diversi componenti che possono essere installati nella stessa istanza di Windows Server o in istanze diverse. Questi componenti includono il portale per gli utenti, il servizio Web per app per dispositivi mobili e la scheda AD FS (agente). Questa separabilità permette di usare il proxy applicazione Web per pubblicare il portale per gli utenti e il server Web per app per dispositivi mobili dalla rete perimetrale. Questo tipo di configurazione contribuisce alla sicurezza complessiva delle progettazioni, come illustrato nel diagramma seguente. È anche possibile distribuire il portale per gli utenti MFA e il server Web per app per dispositivi mobili in configurazioni con bilanciamento del carico a disponibilità elevata.

   ![Server MFA con una rete perimetrale](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **L'autenticazione One Time Password (OTP) con SMS (nota anche come SMS unidirezionale) richiede l'uso di sessioni permanenti, se il traffico è sottoposto a bilanciamento del carico**. L'opzione di autenticazione con SMS unidirezionale determina l'invio agli utenti di un SMS contenente un messaggio OTP da parte del server MFA. L'utente immette l'OTP in una finestra del prompt dei comandi per completare la richiesta di autenticazione a più fattori. Se si esegue il bilanciamento del carico delle istanze del server Azure MFA, il server che ha gestito la richiesta di autenticazione iniziale deve essere quello che riceve il messaggio OTP dall'utente. Se un'altra istanza del server MFA riceve la risposta OTP, la richiesta di autenticazione ha esito negativo. Per altre informazioni, vedere [One Time Password over SMS Added to Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server) (Aggiunta di One Time Password con SMS al server Azure MFA).
* **Le distribuzioni con bilanciamento del carico del portale per gli utenti e del servizio Web per app per dispositivi mobili richiedono sessioni permanenti**. Se si esegue il bilanciamento del carico del portale per utenti MFA e del servizio Web per app per dispositivi mobili, ogni sessione deve rimanere nello stesso server.

## <a name="high-availability-deployment"></a>Distribuzione a disponibilità elevata

Il diagramma seguente illustra un'implementazione completa con bilanciamento del carico a disponibilità elevata di Azure MFA e dei relativi componenti, oltre ad AD FS per riferimento.

 ![Implementazione a disponibilità elevata del server Azure MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Tenere presente gli elementi seguenti per l'area numerata corrispondente del diagramma precedente.

1. Le due istanze del server Azure MFA (MFA1 e MFA2) sono sottoposte a bilanciamento del carico (mfaapp.contoso.com) e configurate per l'uso di una porta statica (4443) per replicare il database PhoneFactor.pfdata. L'SDK del servizio Web è installato in ogni istanza del server MFA per consentire la comunicazione sulla porta TCP 443 con i server AD FS. Le istanze del server MFA sono distribuite in una configurazione con bilanciamento del carico senza stato. Tuttavia, se si intende usare OTP con SMS, è necessario usare il bilanciamento del carico con stato.
   ![Server Azure MFA - Elevata disponibilità del server app](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Poiché RPC usa porte dinamiche, non è consigliabile aprire i firewall fino all'intervallo di porte dinamiche che RPC può usare. Se è presente un firewall **tra** i server applicativi MFA, è necessario configurare il Server MFA per comunicare su una porta statica per il traffico di replica tra server master e subordinati e aprire la porta nel firewall. È possibile forzare la porta statica creando un valore del Registro di sistema DWORD in ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` denominato ```Pfsvc_ncan_ip_tcp_port``` e impostando il valore su una porta statica disponibile. Le connessioni vengono sempre avviate dai server MFA subordinati al master, la porta statica è necessaria solo sul master, ma poiché è possibile alzare di livello un server subordinato in un master in qualsiasi momento, è consigliabile impostare la porta statica in tutte le istanze del server MFA.

2. I due server del portale per gli utenti e delle app per dispositivi mobili MFA (MFA-UP-MAS1 e MFA-UP-MAS2) sono sottoposti a bilanciamento del carico in una configurazione **con stato** (mfa.contoso.com). Tenere presente che le sessioni permanenti sono un requisito necessario per il bilanciamento del carico del portale per gli utenti MFA e del servizio per app per dispositivi mobili.
   ![Server Azure MFA - Disponibilità elevata per il portale per gli utenti e per il servizio per app per dispositivi mobili](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. La server farm AD FS è sottoposta a bilanciamento del carico e pubblicata in Internet tramite proxy AD FS con bilanciamento del carico nella rete perimetrale. Ogni server AD FS usa l'agente AD FS per comunicare con i server Azure MFA mediante un unico URL con bilanciamento del carico (mfaapp.contoso.com) sulla porta TCP 443.

## <a name="next-steps"></a>Passaggi successivi

* [Installare e configurare il server Azure MFA](howto-mfaserver-deploy.md)
