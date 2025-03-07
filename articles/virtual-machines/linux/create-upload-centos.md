---
title: Creare e caricare un VHD Linux basato su CentOS in Azure
description: Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene un sistema operativo Linux basato su CentOS.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: cfbd687e59a9a852fe1bf01386dac221f040d0f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083634"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparare una macchina virtuale basata su CentOS per Azure

Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene un sistema operativo Linux basato su CentOS.

* [Preparare una macchina virtuale CentOS 6.x per Azure](#centos-6x)
* [Preparare una macchina virtuale CentOS 7.0+ per Azure](#centos-70)


## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Linux CentOS (o un sistema derivato simile) in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di CentOS**

* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd. Se si usa VirtualBox, ciò significa che è stato selezionato **Dimensioni fisse** anziché il valore predefinito allocato in modo dinamico durante la creazione del disco.
* Durante l'installazione del sistema Linux è *consigliabile* usare partizioni standard anziché LVM, spesso la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale identica per la risoluzione dei problemi. È possibile usare [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) su dischi di dati.
* Per l'installazione di file system UDF è necessario il supporto di kernel. Al primo avvio in Azure la configurazione del provisioning viene passata alla macchina virtuale Linux tramite supporti di memorizzazione formattati con UDF, collegati al guest. È necessario che l'agente Linux di Azure possa montare il file system UDF per leggere la relativa configurazione ed eseguire il provisioning della macchina virtuale.
* Le versioni di kernel Linux inferiori a 2.6.37 non supportano NUMA su Hyper-V con macchine virtuali di dimensioni maggiori. Questo problema incide principalmente sulle distribuzioni precedenti che usavano il kernel upstream Red Hat 2.6.32. Il problema è stato risolto in RHEL 6.6 (kernel-2.6.32-504). I sistemi che eseguono kernel personalizzati con versione precedente alla versione 2.6.37 o kernel basati su RHEL con versione precedente alla versione 2.6.32-504 devono impostare il parametro di avvio `numa=off` nella riga di comando del kernel in rub.conf. Per altre informazioni, vedere l'articolo Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse.  Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="centos-6x"></a>CentOS 6.x

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3. In CentOS 6, NetworkManager può interferire con l'agente Linux di Azure. Disinstallare il pacchetto eseguendo questo comando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il seguente comando:

    ```bash
    sudo chkconfig network on
    ```

8. Se si vogliono usare i mirror OpenLogic ospitati nei data center di Azure, sostituire il file `/etc/yum.repos.d/CentOS-Base.repo` con i repository seguenti.  Verrà così aggiunto anche il repository **[openlogic]** che include pacchetti aggiuntivi come l'agente Linux di Azure:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > La parte restante di questa guida presuppone che l'utente usi almeno il repository `[openlogic]`, con cui verrà installato l'agente Linux di Azure illustrato di seguito.

9. Aggiungere la seguente riga a /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Eseguire questo comando per cancellare i metadati yum correnti e aggiornare il sistema con i pacchetti più recenti:

    ```bash
    yum clean all
    ```

    A meno che non si crei un'immagine per una versione precedente di CentOS, è consigliabile aggiornare tutti i pacchetti all'ultima versione:

    ```bash
    sudo yum -y update
    ```

    Dopo l'esecuzione di questo comando potrebbe essere necessario un riavvio.

11. (Facoltativo) Installare i driver per Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Questo passaggio è **obbligatorio** per CentOS 6.3 e le versioni precedenti e facoltativo per le versioni successive.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    In alternativa, seguire le istruzioni per l'installazione manuale disponibili nella [pagina di download di LIS](https://go.microsoft.com/fwlink/?linkid=403033) per installare RPM nella VM.

12. Installare l'agente Linux di Azure e le dipendenze. Avviare e abilitare il servizio waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Il pacchetto WALinuxAgent rimuoverà i pacchetti NetworkManager e NetworkManager-gnome, se non sono già stati rimossi come descritto nel passaggio 3.

13. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.

    Inoltre, è consigliabile *rimuovere* i parametri seguenti:

    ```console
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.  L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.

    > [!Important]
    > Per CentOS 6.5 e le versioni precedenti deve essere impostato anche il parametro del kernel `numa=off`. Vedere l'articolo [KB 436883](https://access.redhat.com/solutions/436883) di Red Hat.

14. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.

15. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.



## <a name="centos-70"></a>CentOS 7.0+

**Modifiche in CentOS 7 (e sistemi derivati simili)**

La preparazione di una macchina virtuale CentOS 7 per Azure è molto simile a CentOS 6, tuttavia vi sono alcune importanti differenze da notare:

* Il pacchetto NetworkManager e l'agente Linux di Azure non sono più in conflitto. Questo pacchetto viene installato per impostazione predefinita ed è consigliabile non rimuoverlo.
* GRUB2 viene ora usato come bootloader predefinito, quindi la procedura per la modifica dei parametri kernel è cambiata (vedere di seguito).
* XFS è ora il file system predefinito. Se si vuole, è ancora possibile usare il file system ext4.

**Procedura di configurazione**

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se si vogliono usare i mirror OpenLogic ospitati nei data center di Azure, sostituire il file `/etc/yum.repos.d/CentOS-Base.repo` con i repository seguenti.  Verrà aggiunto anche il repository **[openlogic]** che include i pacchetti per l'agente Linux di Azure:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > La parte restante di questa guida presuppone che l'utente usi almeno il repository `[openlogic]`, con cui verrà installato l'agente Linux di Azure illustrato di seguito.

7. Eseguire il comando seguente per cancellare i metadati yum correnti e installare eventuali aggiornamenti:

    ```bash
    sudo yum clean all
    ```

    A meno che non si crei un'immagine per una versione precedente di CentOS, è consigliabile aggiornare tutti i pacchetti all'ultima versione:

    ```bash
    sudo yum -y update
    ```

    Dopo l'esecuzione di questo comando potrebbe essere necessario un riavvio.

8. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`, ad esempio:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva anche nuove convenzioni di denominazione CentOS 7 per NIC. Inoltre, è consigliabile *rimuovere* i parametri seguenti:

    ```console
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.

9. Dopo aver terminato di modificare `/etc/default/grub` come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Se si compila l'immagine da **VMware, VirtualBox o KVM**, assicurarsi che i driver Hyper-V siano inclusi in initramfs:

    Modificare `/etc/dracut.conf`e aggiungere il contenuto:

    ```console
    add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
    ```

    Ricompilare l’initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Installare l'agente Linux di Azure e le dipendenze:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i seguenti parametri di `/etc/waagent.conf` in modo appropriato:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile usare il disco rigido virtuale CentOS Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
