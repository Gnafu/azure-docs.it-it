---
title: Installare Office in un'immagine del disco rigido virtuale Master-Azure
description: Come installare e personalizzare Office in un'immagine master di Windows Virtual Desktop Preview in Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 79fe541d1bb3bea8447cf095673111362cec74d2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816440"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installare Office in un'immagine master di disco rigido virtuale

Questo articolo descrive come installare Office 365 ProPlus, OneDrive e altre applicazioni comuni in un'immagine del disco rigido virtuale (VHD) master per il caricamento in Azure. Se gli utenti devono accedere ad alcune applicazioni line-of-business (LOB), è consigliabile installarle dopo aver completato le istruzioni riportate in questo articolo.

Questo articolo presuppone che sia già stata creata una macchina virtuale (VM). In caso contrario, vedere [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md#create-a-vm)

Questo articolo presuppone anche che sia stato eseguito l'accesso con privilegi elevati alla macchina virtuale, indipendentemente dal fatto che venga effettuato il provisioning in Azure o nella console di gestione di Hyper In caso contrario, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e la sottoscrizione di Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows (anteprima) che può essere usata con gli attuali processi dell'organizzazione.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installare Office in modalità di attivazione computer condiviso

L'attivazione del computer condiviso consente di distribuire Office 365 ProPlus in un computer dell'organizzazione a cui si accede da più utenti. Per ulteriori informazioni sull'attivazione dei computer condivisi, vedere [Panoramica dell'attivazione di computer condivisi per Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Usare lo [strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117) per installare Office. Windows 10 Enterprise multisessione supporta solo le seguenti versioni di Office:
- Office 365 ProPlus
- Business Office 365 incluso in una sottoscrizione di Microsoft 365 Business

Per lo strumento di distribuzione di Office è necessario un file XML di configurazione. Per personalizzare l'esempio seguente, vedere le [Opzioni di configurazione per lo strumento di distribuzione di Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Questo codice XML di configurazione di esempio fornito eseguirà le operazioni seguenti:

- Installare Office dal canale Insider e recapitare gli aggiornamenti dal canale Insider quando vengono eseguiti.
- Usare l'architettura x64.
- Disabilitare gli aggiornamenti automatici.
- Installare Visio e Project.
- Rimuovere le installazioni esistenti di Office ed eseguire la migrazione delle impostazioni.
- Abilitare l'attivazione di computer condivisi.

>[!NOTE]
>La funzionalità di ricerca dello stencil in Visio non funziona nel desktop virtuale di Windows durante la configurazione di anteprima.

Ecco il codice XML di configurazione di esempio:

- Installare Skype for business
- Installare OneDrive in modalità per utente. Per altre informazioni, vedere [installare OneDrive in modalità per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>L'attivazione di computer condivisi può essere configurata tramite oggetti Criteri di gruppo (GPO) o impostazioni del registro di sistema. L'oggetto Criteri di gruppo si trova in **\\criteri\\\\di configurazione del computer modelli amministrativi\\Microsoft Office le impostazioni di licenza 2016 (computer)**

Lo strumento di distribuzione di Office contiene Setup. exe. Per installare Office, eseguire il comando seguente in una riga di comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Esempio di Configuration. XML

Nell'esempio XML seguente viene installata la versione Insider.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Il team di Office consiglia di usare l'installazione a 64 bit per il parametro **OfficeClientEdition** .

Dopo l'installazione di Office, è possibile aggiornare il comportamento predefinito di Office. Eseguire i comandi seguenti singolarmente o in un file batch per aggiornare il comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installare OneDrive in modalità per computer

OneDrive viene in genere installato per utente. In questo ambiente deve essere installato per computer.

Di seguito viene illustrato come installare OneDrive in modalità per computer:

1. Per prima cosa, creare un percorso per organizzare il programma di installazione di OneDrive. Una cartella del disco locale o\\un percorso [\\UNC] (file://UNC) è un problema.

2. Scaricare OneDriveSetup. exe nel percorso di gestione temporanea con il collegamento seguente:<https://aka.ms/OneDriveWVD-Installer>

3. Se Office è stato installato con OneDrive omettendo  **\<ExcludeApp ID =\>"OneDrive"/** , disinstallare le installazioni esistenti di OneDrive per utente da un prompt dei comandi con privilegi elevati eseguendo il comando seguente:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Eseguire questo comando da un prompt dei comandi con privilegi elevati per impostare il valore del registro di sistema **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Eseguire questo comando per installare OneDrive in modalità per computer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Eseguire questo comando per configurare OneDrive per avviare l'accesso a tutti gli utenti:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Abilitare l' **account utente per la configurazione invisibile all'utente** eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Reindirizzare e spostare le cartelle note di Windows in OneDrive eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Team e Skype

Desktop virtuale Windows non supporta Skype for business e i team.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato aggiunto Office all'immagine, è possibile continuare a personalizzare l'immagine del disco rigido virtuale master. Vedere [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md).
