---
title: Considerazioni sulla piattaforma UWP (Universal Windows Platform) (Microsoft Authentication Library per .NET) | Azure
description: Per informazioni su considerazioni specifiche, vedere l'articolo relativo all'uso di piattaforma UWP (Universal Windows Platform) con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 263264742088a0012ea844946e13cffbab634b29
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532487"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considerazioni specifiche di piattaforma UWP (Universal Windows Platform) con MSAL.NET
In UWP è necessario tenere conto di diverse considerazioni quando si usa MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>Proprietà UseCorporateNetwork
Nella piattaforma WinRT, `PublicClientApplication` presenta la seguente proprietà ``UseCorporateNetwork``booleana. Questa proprietà consente alle applicazioni Win 8.1 e UWP di trarre vantaggio dall'autenticazione integrata di Windows (e pertanto SSO con l'utente connesso con il sistema operativo) se l'utente è connesso con un account in un tenant di Azure AD federato. Quando si imposta questa proprietà, MSAL.NET utilizza WAB (Web Authentication Broker).

> [!IMPORTANT]
> Se si imposta questa proprietà su true, si presuppone che lo sviluppatore dell'applicazione abbia abilitato l'autenticazione integrata di Windows (IWA) nell'applicazione. Per questo:
> - Nella scheda **funzionalità** del perl'applicazioneUWPabilitarelefunzionalitàseguenti:``Package.appxmanifest``
>   - Autenticazione aziendale
>   - Reti private (client e server)
>   - Certificato utente condiviso

L'autenticazione integrata non è abilitata per impostazione predefinita perché le applicazioni che richiedono le funzionalità autenticazione aziendale o certificati utente condivisi richiedono un livello superiore di verifica da accettare in Windows Store e non tutti gli sviluppatori potrebbero desiderare di eseguire le operazioni più elevate livello di verifica.

L'implementazione sottostante sulla piattaforma UWP (WAB) non funziona correttamente negli scenari aziendali in cui è stato abilitato l'accesso condizionale. Il sintomo è che l'utente tenta di accedere con Windows Hello e viene proposto di scegliere un certificato, ma:

- il certificato per il PIN non è stato trovato.
- in alternativa, l'utente lo sceglie, ma non viene mai visualizzata la richiesta del PIN.

Una soluzione alternativa consiste nell'usare un metodo alternativo (nome utente/password e autenticazione telefono), ma l'esperienza non è ottimale.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Alcuni clienti hanno segnalato che in alcuni ambienti aziendali specifici si è verificato il seguente errore di accesso:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

mentre sanno che hanno una connessione a Internet e che funzionano con una rete pubblica.

Una soluzione alternativa consiste nel verificare che WAB (il componente Windows sottostante) consenta la rete privata. È possibile eseguire questa operazione impostando una chiave del registro di sistema:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Per informazioni dettagliate, vedere [Web Authentication broker-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Passaggi successivi
Altri dettagli sono disponibili negli esempi seguenti:

Esempio | Piattaforma | Descrizione 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Un piattaforma UWP (Universal Windows Platform) applicazione client che utilizza msal.net, accedendo al Microsoft Graph per un utente che esegue l'autenticazione con l'endpoint Azure AD v 2.0. <br>![Topologia](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Novell Forms che illustra come usare MSAL per autenticare MSA e Azure AD tramite l'endpoint di AAD v 2.0 e accedere al Microsoft Graph con il token risultante. <br>![Topologia](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
