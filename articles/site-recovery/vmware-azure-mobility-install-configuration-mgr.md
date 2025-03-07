---
title: Automatizzare l'installazione del servizio Mobility di Azure Site Recovery per il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure tramite System Center Configuration Manager | Microsoft Docs
description: Questo articolo consente di automatizzare l'installazione del servizio Mobility con System Center Configuration Manager per eseguire il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure usando Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 35c317c4b73e9a22e3b0d6192abcfc2a596066b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60598118"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>Automatizzare l'installazione del servizio Mobility tramite System Center Configuration Manager

Il servizio Mobility viene installato nelle VM VMware e nei server fisici di cui si vuole eseguire la replica in Azure con [Azure Site Recovery](site-recovery-overview.md).

Questo articolo descrive un esempio dell'uso di System Center Configuration Manager per distribuire il servizio Mobility di Azure Site Recovery in una VM VMware. L'uso di uno strumento di distribuzione software come Configuration Manager offre i seguenti vantaggi:

* Pianificare nuove installazioni e aggiornamenti durante la finestra di manutenzione pianificata per gli aggiornamenti software
* Estendere la distribuzione fino a centinaia di server simultaneamente

In questo articolo si usa System Center Configuration Manager 2012 R2 per illustrare l'attività di distribuzione. Si presuppone l'uso del servizio Mobility versione **9.9.4510.1** o successiva.

In alternativa, è possibile automatizzare l'installazione del servizio Mobility con [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Prerequisiti

1. Uno strumento di distribuzione software come Configuration Manager già distribuito nell'ambiente.
2. Creare due [raccolte di dispositivi](https://technet.microsoft.com/library/gg682169.aspx), una per tutti i **server Windows** e una per tutti i **server Linux** da proteggere con Site Recovery.
3. Un server di configurazione già registrato nell'insieme di credenziali di Servizi di ripristino.
4. Una condivisione file di rete sicura (condivisione SMB) che sia accessibile dal computer di Configuration Manager.

## <a name="deploy-on-windows-machines"></a>Distribuire nei computer Windows
> [!NOTE]
> In questo articolo si presuppone che l'indirizzo IP del server di configurazione sia 192.168.3.121 e che la condivisione di file di rete sicura sia \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcWindows**.
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo.
3. Eseguire i comandi seguenti per generare un file passphrase:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiare il file **MobSvc.passphrase** nella cartella **MobSvcWindows** nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando seguente:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copiare il file **Microsoft-ASR\_UA\_*versione*\_Windows\_GA\_*data*\_Release.exe** nella cartella **MobSvcWindows** della condivisione di rete.
7. Copiare il codice seguente e salvarlo come **install.bat** nella cartella **MobSvcWindows**.

   > [!NOTE]
   > Sostituire i segnaposto [CSIP] nello script seguente con i valori effettivi dell'indirizzo IP del server di configurazione.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>Creare un pacchetto

1. Accedere alla console di Configuration Manager.
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**.
3. Fare clic con il pulsante destro del mouse su **Pacchetti** e selezionare **Crea pacchetto**.
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic su **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**.

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Nella pagina **Specificare le informazioni sul programma standard** indicare gli input seguenti e fare clic su **Avanti**. Gli altri input possono mantenere i valori predefiniti.

   | **Nome parametro** | **Valore** |
   |--|--|
   | Name | Installare il servizio Mobility di Microsoft Azure (Windows) |
   | Riga di comando | install.bat |
   | Il programma può essere eseguito | anche se non ci sono utenti connessi |

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Nella pagina successiva selezionare i sistemi operativi di destinazione. Il servizio Mobility può essere installato solo su Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. Fare clic su **Avanti** due volte per completare la procedura guidata.


> [!NOTE]
> Lo script supporta sia le nuove installazioni degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="deploy-the-package"></a>Distribuire il pacchetto
1. Nella console di Configuration Manager fare clic con il pulsante destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**.
   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selezionare i **[punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Completare la procedura guidata. Il pacchetto inizia la replica nei punti di distribuzione specificati.
4. Dopo aver completato la distribuzione del pacchetto, fare clic su quest'ultimo con il pulsante destro del mouse e selezionare **Distribuisci**.
   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi di Windows Server creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

   ![Schermata della Distribuzione guidata del software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**.
7. Nella pagina **Specificare le impostazioni per controllare la modalità di distribuzione del software** verificare che lo scopo sia **Obbligatorio**.

   ![Schermata della Distribuzione guidata del software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Per altre informazioni, vedere la sezione relativa alla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx).
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center. Completare quindi la procedura guidata.

> [!TIP]
> Per evitare riavvii non necessari, pianificare l'installazione del pacchetto durante la finestra di manutenzione mensile o degli aggiornamenti software.

È possibile monitorare l'avanzamento della distribuzione tramite la console di Configuration Manager. Passare a **Monitoraggio** > **Distribuzioni** >  *[nome pacchetto]* .

  ![Schermata dell'opzione di Configuration Manager per monitorare le distribuzioni](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Distribuire in computer Linux
> [!NOTE]
> In questo articolo si presuppone che l'indirizzo IP del server di configurazione sia 192.168.3.121 e che la condivisione di file di rete sicura sia \\\ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcLinux**.
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo.
3. Eseguire i comandi seguenti per generare un file passphrase:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Copiare il file **MobSvc.passphrase** nella cartella **MobSvcLinux** nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Copiare i file seguenti nella cartella **MobSvcLinux** nella condivisione di rete:
   * Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. Copiare il codice seguente e salvarlo come **install_linux.sh** nella cartella **MobSvcLinux**.
   > [!NOTE]
   > Sostituire i segnaposto [CSIP] nello script seguente con i valori effettivi dell'indirizzo IP del server di configurazione.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Creare un pacchetto

1. Accedere alla console di Configuration Manager.
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**.
3. Fare clic con il pulsante destro del mouse su **Pacchetti** e selezionare **Crea pacchetto**.
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic su **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**.

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Nella pagina **Specificare le informazioni sul programma standard** indicare gli input seguenti e fare clic su **Avanti**. Gli altri input possono mantenere i valori predefiniti.

    | **Nome parametro** | **Valore** |
   |--|--|
   | NOME | Installare il servizio Mobility di Microsoft Azure (Linux) |
   | Riga di comando | ./install_linux.sh |
   | Il programma può essere eseguito | anche se non ci sono utenti connessi |

   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Nella pagina successiva selezionare **Questo programma può essere eseguito in qualsiasi piattaforma**.
   ![Schermata di Creazione guidata pacchetto e programma](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. Fare clic su **Avanti** due volte per completare la procedura guidata.

> [!NOTE]
> Lo script supporta sia le nuove installazioni degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="deploy-the-package"></a>Distribuire il pacchetto
1. Nella console di Configuration Manager fare clic con il pulsante destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**.
   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. Selezionare i **[punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Completare la procedura guidata. Il pacchetto inizia la replica nei punti di distribuzione specificati.
4. Dopo aver completato la distribuzione del pacchetto, fare clic su quest'ultimo con il pulsante destro del mouse e selezionare **Distribuisci**.
   ![Schermata della console di Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi server Linux creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

   ![Schermata della Distribuzione guidata del software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**.
7. Nella pagina **Specificare le impostazioni per controllare la modalità di distribuzione del software** verificare che lo scopo sia **Obbligatorio**.

   ![Schermata della Distribuzione guidata del software](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Per altre informazioni, vedere la sezione relativa alla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx).
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center. Completare quindi la procedura guidata.

Il servizio Mobility viene installato nella raccolta di dispositivi server Linux in base alla pianificazione configurata.


## <a name="uninstall-the-mobility-service"></a>Disinstallare il servizio Mobility
È possibile creare pacchetti di Configuration Manager per disinstallare il servizio Mobility. A tale scopo, seguire questa procedura:

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [abilitare la protezione](vmware-azure-enable-replication.md) per le macchine virtuali.
