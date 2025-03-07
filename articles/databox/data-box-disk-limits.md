---
title: Limiti di Azure Data Box Disk | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 32445e3f6859a6161eb2fae20233c598234f18a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400627"
---
# <a name="azure-data-box-disk-limits"></a>Limiti di Azure Data Box Disk


Quando si distribuisce e si usa la soluzione Microsoft Azure Data Box Disk, tenere presenti i limiti seguenti.

## <a name="data-box-service-limits"></a>Limiti di servizio Data Box

 - Il servizio Data Box è disponibile nelle aree di Azure elencate nelle [aree di disponibilità](data-box-disk-overview.md#region-availability).
 - Con Data Box Disk è supportato un unico account di archiviazione.

## <a name="data-box-disk-performance"></a>Prestazioni di Data Box Disk

Nei test con dischi connessi tramite USB 3.0 sono risultate prestazioni dei dischi fino a 430 MB/s. I valori effettivi variano a seconda delle dimensioni dei file usate. Per i file più piccoli, le prestazioni potrebbero essere inferiori.

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

Questa sezione descrive i limiti relativi al servizio Archiviazione di Azure e le convenzioni di denominazione necessarie per File di Azure, BLOB in blocchi di Azure e BLOB di pagine di Azure, come applicabili al servizio Data Box. Esaminare attentamente i limiti di archiviazione e seguire tutte le raccomandazioni.

Per informazioni aggiornate su limiti del servizio Archiviazione di Azure e le procedure consigliate per la denominazione di condivisioni, contenitori e file, vedere:

- [Naming and referencing containers](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nomi e riferimenti a contenitori)
- [Naming and referencing shares](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) (Assegnazione di nomi e riferimenti a condivisioni)
- [Block blobs and page blob conventions](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) (Convenzioni su BLOB in blocchi e BLOB di pagine)

> [!IMPORTANT]
> Se sono presenti file o directory che superano i limiti del servizio Archiviazione di Azure o che non sono conformi alle convenzioni di denominazione di BLOB o File di Azure, tali file o directory non vengono inseriti in Archiviazione di Azure tramite il servizio Data Box.

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

- Non copiare i dati direttamente nei dischi. Copiare i dati creati in precedenza *BlockBlob*,*PageBlob*, e *AzureFile* cartelle.
- Una cartella in *BlockBlob* e *PageBlob* è un contenitore. I contenitori, ad esempio, vengono creati come *BlockBlob/container* e *PageBlob/container*.
- Se si dispone di un oggetto di Azure esistente (ad esempio, un blob) nel cloud con lo stesso nome dell'oggetto in fase di copia, Data Box Disk verranno rinominare il file come file(1) nel cloud.
- Ogni file scritto nelle condivisioni *BlockBlob* e *PageBlob* viene caricato come BLOB in blocchi e BLOB di pagine rispettivamente.
- Una gerarchia di directory vuota (senza alcun file) creata nelle cartelle *BlockBlob* e *PageBlob* non viene caricata.
- Se si verificano errori durante il caricamento dei dati in Azure, viene creato un log degli errori nell'account di archiviazione di destinazione. Il percorso del log degli errori è disponibile nel portale quando il caricamento è completo ed è possibile esaminare il log per eseguire azioni correttive. Non eliminare i dati dall'origine senza verificare i dati caricati.
- Se si sono specificati i dischi gestiti nell'ordine, esaminare le considerazioni aggiuntive seguenti:

    - In tutte le cartelle create preventivamente e in Data Box Disk può essere presente un solo disco gestito con un determinato nome in un gruppo di risorse. I dischi rigidi virtuali caricati nelle cartelle create preventivamente devono quindi avere nomi univoci. Verificare che il nome assegnato non corrisponda a un disco gestito già esistente in un gruppo di risorse. Se i dischi rigidi virtuali hanno lo stesso nome, un solo disco rigido virtuale verrà convertito in un disco gestito con tale nome. Gli altri dischi rigidi virtuali verranno caricati come BLOB di pagine nell'account di archiviazione di staging.
    - Copiare sempre i dischi rigidi virtuali in una delle cartelle create preventivamente. Se vengono copiati all'esterno di queste cartelle o in una cartella creata dall'utente, i dischi rigidi virtuali verranno caricati nell'account di archiviazione di Azure come BLOB di pagine e non come dischi gestiti.
    - Per la creazione di dischi gestiti possono essere caricati solo dischi rigidi virtuali a dimensione fissa. I dischi rigidi virtuali dinamici o differenze e i file VHDX non sono supportati.

## <a name="azure-storage-account-size-limits"></a>Limiti delle dimensioni dell'account di archiviazione di Azure

Di seguito vengono indicati i limiti sulle dimensioni dei dati copiati nell'account di archiviazione. Verificare che i dati caricati siano conformi a tali limiti. Per informazioni più aggiornate su questi limiti, vedere [Obiettivi di scalabilità dell'archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) e [Obiettivi di scalabilità di File di Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Dimensioni dei dati copiati nell'account di archiviazione di Azure                      | Limite predefinito          |
|---------------------------------------------------------------------|------------------------|
| BLOB di pagine e BLOB in blocchi                                            | 500 TB per account di archiviazione <br> Sono inclusi dati da tutte le origini, ad esempio Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | Circa 4,75 TiB                                                 |
| BLOB di pagine         | 8 TiB <br> (Tutti i file caricati nel formato Blob di pagine deve essere allineati a 512 byte, altrimenti il caricamento ha esito negativo. <br> Il VHD e VHDX sono allineati a 512 byte.) |
|File di Azure        | 1 TiB <br> Max. dimensioni della condivisione sono 5 TiB     |
| Dischi gestiti     |4 TiB <br> Per altre informazioni sulle dimensioni e limiti, vedere: <li>[Obiettivi di scalabilità per i dischi gestiti](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenzioni di denominazione per BLOB in blocchi, BLOB di pagine e file

| Entità                                       | Convenzioni                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomi di contenitori BLOB in blocchi e BLOB di pagine <br> Nomi di condivisione file per file di Azure | Deve essere un nome DNS valido compreso tra 3 e 63 caratteri. <br>  Deve iniziare con una lettera o un numero. <br> Può contenere solo lettere minuscole, numeri e trattini (-). <br> Ogni trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. <br> I trattini consecutivi non sono consentiti nei nomi. |
| Nomi dei file e delle directory per i file di Azure     |<li> Mantengono le maiuscole/minuscole, non fanno distinzione tra maiuscole e minuscole e non devono superare i 255 caratteri. </li><li> Non possono terminare con la barra (/). </li><li>Se inserita, verrà automaticamente rimossa. </li><li> Non sono consentiti i caratteri seguenti: <code>" \\ / : \| < > * ?</code></li><li> I caratteri URL riservati devono essere preceduti da una sequenza di escape. </li><li> Non sono ammessi i caratteri di percorsi URL non validi. Elementi di codice come \\uE000 non sono caratteri Unicode validi. Alcuni caratteri ASCII o Unicode, come caratteri di controllo (da 0x00 a 0x1F, \\u0081, e così via), non sono consentiti. Per le regole che controllano le stringhe Unicode in HTTP/1.1, vedere RFC 2616, sezione 2.2: Basic Rules e RFC 3987. </li><li> I nomi file seguenti non sono consentiti: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, punto (.) e due punti (..).</li>|
| Nomi di BLOB per BLOB in blocchi e BLOB di pagine      | I nomi di BLOB fanno distinzione tra maiuscole e minuscole e possono contenere una qualsiasi combinazione di caratteri. <br> La lunghezza di un nome di BLOB deve essere compresa tra 1 e 1.024 caratteri. <br> I caratteri URL riservati devono essere preceduti da una sequenza di escape. <br>Il numero di segmenti del percorso che includono il nome BLOB non può essere superiore a 254. Un segmento di percorso è la stringa tra caratteri di delimitatore consecutivi, ad esempio, la barra rovesciata '/', che corrisponde al nome di una directory virtuale. |

## <a name="managed-disk-naming-conventions"></a>Convenzioni di denominazione del disco gestito

| Entità | Convenzioni                                             |
|-------------------|-----------------------------------------------------------|
| Nomi dei dischi gestiti       | <li> Il nome deve essere 1 e 80 caratteri. </li><li> Il nome deve iniziare con una lettera o un numero, terminare con una lettera, numero o un carattere di sottolineatura. </li><li> Il nome può contenere solo lettere, numeri, caratteri di sottolineatura, punti o trattini. </li><li>   Il nome non deve contenere spazi o `/`.                                              |

## <a name="next-steps"></a>Passaggi successivi

- Revisione [requisiti di sistema di Data Box Disk](data-box-disk-system-requirements.md)
