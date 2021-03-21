---
title: Så här monterar du Azure Blob Storage som ett fil system i Linux | Microsoft Docs
description: Lär dig hur du monterar en Azure Blob Storage-behållare med blobfuse, en virtuell fil system driv rutin på Linux.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 002e8650a5555b70caf09179e03ce1bad1acdef5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737548"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Montera Blob Storage som ett fil system med blobfuse

## <a name="overview"></a>Översikt
[Blobfuse](https://github.com/Azure/azure-storage-fuse) är en virtuell filsystemsdrivrutin för Azure-blobblagring. Med Blobfuse kan du komma åt dina befintliga block BLOB-data i ditt lagrings konto via Linux-filsystemet. Blobfuse använder det virtuella katalog schemat med Forward-snedstrecket "/" som avgränsare.  

Den här guiden visar hur du använder blobfuse och monterar en Blob Storage-behållare på Linux och åtkomst till data. Läs mer om blobfuse i informationen i [blobfuse-lagringsplatsen](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse garanterar inte 100% POSIX-kompatibilitet eftersom det bara översätter begär anden till [BLOB REST-API: er](/rest/api/storageservices/blob-service-rest-api). Till exempel är Rename-åtgärder atomiska i POSIX, men inte i blobfuse.
> En fullständig lista över skillnaderna mellan ett ursprungligt fil system och blobfuse finns [i käll kods lagret för blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installera blobfuse på Linux
Blobfuse-binärfiler finns tillgängliga i [Microsofts program varu databaser för Linux](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) för Ubuntu, Debian, SUSE, CentoOS, Oracle Linux och RHEL-distributioner. Om du vill installera blobfuse på dessa distributioner konfigurerar du en av databaserna från listan. Du kan också bygga binärfilerna från käll koden efter [Azure Storage installations steg](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) om det inte finns några binärfiler tillgängliga för din distribution.

Blobfuse stöder installation på Ubuntu-versioner: 16,04, 18,04 och 20,04, RHELversions: 7,5, 7,8, 8,0, 8,1, 8,2, CentOS-versioner: 7,0, 8,0, Debian-versioner: 9,0, 10,0, SUSE version: 15, OracleLinux 8,1. Kör det här kommandot för att kontrol lera att du har någon av dessa versioner distribuerade:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurera Microsoft Package-lagringsplatsen
Konfigurera [Linux-paketets lagrings plats för Microsoft-produkter](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Som exempel i en Enterprise Linux 8-distribution:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
```

På samma sätt kan du ändra URL: en så att den `.../rhel/7/...` pekar på en Enterprise Linux 7-distribution.

Ett annat exempel på en Ubuntu 20,04-distribution:
```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

På samma sätt kan du ändra URL: en till `.../ubuntu/16.04/...` eller `.../ubuntu/18.04/...` till referens till en annan Ubuntu-version.

### <a name="install-blobfuse"></a>Installera blobfuse

På en Ubuntu-/Debian-distribution:
```bash
sudo apt-get install blobfuse
```

I en Enterprise Linux-distribution:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Förbered för montering
Blobfuse tillhandahåller inbyggd prestanda genom att kräva att en tillfällig sökväg i fil systemet buffrar och cachelagrar alla öppna filer. För den här tillfälliga sökvägen väljer du den mest presterande disken eller använder en RAMDISK för bästa prestanda. 

> [!NOTE]
> Blobfuse lagrar alla öppna fil innehåll i den tillfälliga sökvägen. Se till att det finns tillräckligt med utrymme för alla öppna filer. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Valfritt Använd en RAMDISK för den tillfälliga sökvägen
I följande exempel skapas en RAMDISK med 16 GB och en katalog för blobfuse. Välj storlek utifrån dina behov. Med den här RAMDISK-filen kan blobfuse öppna filer upp till 16 GB i storlek. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Använd en SSD som tillfällig sökväg
I Azure kan du använda de tillfälliga diskar (SSD) som finns tillgängliga på dina virtuella datorer för att tillhandahålla en buffert med låg latens för blobfuse. I Ubuntu-distributioner monteras den här tillfälliga disken på "/mnt". I Red Hat och CentOS-distributioner är disken monterad på "/mnt/Resource/".

Se till att användaren har åtkomst till den tillfälliga sökvägen:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurera autentiseringsuppgifterna för ditt lagrings konto
Blobfuse kräver att dina autentiseringsuppgifter lagras i en textfil i följande format: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName`Är prefixet för ditt lagrings konto – inte den fullständiga URL: en.

Skapa den här filen med:

```
touch ~/fuse_connection.cfg
```

När du har skapat och redigerat den här filen, se till att begränsa åtkomsten så att inga andra användare kan läsa den.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Om du har skapat konfigurations filen i Windows, se till att köra `dos2unix` för att rensa filen till UNIX-format. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Skapa en tom katalog för montering
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Montera

> [!NOTE]
> För en fullständig lista över monterings alternativ kontrollerar [du blobfuse-lagringsplatsen](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Om du vill montera blobfuse kör du följande kommando med användaren. Det här kommandot monterar den behållare som anges i "/path/to/fuse_connection. cfg" på platsen "/mycontainer".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Du bör nu ha åtkomst till dina block-blobar via de vanliga API: erna för fil system. Den användare som monterar katalogen är den enda person som har åtkomst till den, som standard, vilket skyddar åtkomsten. Om du vill tillåta åtkomst till alla användare kan du montera via alternativet ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Nästa steg

* [Start sida för Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Rapportera blobfuse-problem](https://github.com/Azure/azure-storage-fuse/issues)
