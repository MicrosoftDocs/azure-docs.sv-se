---
title: Montera Azure File Storage på virtuella Linux-datorer med SMB
description: Montera Azure File Storage på virtuella Linux-datorer med SMB med Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 45042f474f39b3f64d45913905765016fae3cb26
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551801"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montera Azure File Storage på virtuella Linux-datorer med SMB

Den här artikeln visar hur du använder Azure File Storage-tjänsten på en virtuell Linux-dator med hjälp av en SMB-montering med Azure CLI. Azure File Storage erbjuder fil resurser i molnet med hjälp av standard-SMB-protokollet. 

File Storage erbjuder fil resurser i molnet som använder standard-SMB-protokollet. Du kan montera en fil resurs från alla operativ system som stöder SMB 3,0. När du använder en SMB-montering på Linux får du enkla säkerhets kopieringar till en robust lagrings plats för permanent arkivering som stöds av ett service avtal.

Att flytta filer från en virtuell dator till en SMB-montering som är värd för fil lagring är ett bra sätt att felsöka loggar. Samma SMB-resurs kan monteras lokalt på din Mac-, Linux-eller Windows-arbetsstation. SMB är inte den bästa lösningen för strömmande Linux-eller program loggar i real tid eftersom SMB-protokollet inte har skapats för att hantera sådana tung loggnings uppgifter. Ett dedikerat, enhetligt loggnings lager verktyg som till exempel Fluent är ett bättre alternativ än SMB för att samla in Linux-och program loggnings utdata.

Den här guiden kräver att du kör Azure CLI-versionen 2.0.4 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp med namnet *myResourceGroup* på platsen *USA, östra* .

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett nytt lagrings konto i resurs gruppen som du skapade med [AZ lagrings konto skapa](/cli/azure/storage/account). I det här exemplet skapas ett lagrings konto med namnet *mySTORAGEACCT \<random number>* och namnet på lagrings kontot läggs till i variabeln **STORAGEACCT**. Lagrings konto namn måste vara unika, med `$RANDOM` Lägg till ett nummer i slutet för att göra det unikt.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Hämta lagrings nyckeln

När du skapar ett lagrings konto skapas konto nycklarna i par så att de kan roteras utan avbrott i tjänsten. När du växlar till den andra nyckeln i paret skapar du ett nytt nyckel par. Nya lagrings konto nycklar skapas alltid i par, så du har alltid minst en oanvänd lagrings konto nyckel som är redo att växla till.

Visa lagrings konto nycklarna med [AZ lagrings konto nycklar](/cli/azure/storage/account/keys). I det här exemplet lagras värdet för nyckel 1 i variabeln **STORAGEKEY** .

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Skapa en filresurs

Skapa fil lagrings resursen med hjälp av [AZ Storage Share Create](/cli/azure/storage/share). 

Resurs namn måste vara alla gemener, siffror och enskilda bindestreck, men får inte börja med ett bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata).

I det här exemplet skapas en resurs med namnet *Unshare* med en 10-GIB kvot. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Skapa en monterings punkt

Om du vill montera Azure-filresursen på Linux-datorn måste du se till att du har **CIFS-utils-** paketet installerat. Installations anvisningar finns i [Installera CIFS-utils-paketet för din Linux-distribution](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files använder SMB-protokoll, som kommunicerar via TCP-port 445.  Om du har problem med att montera Azure-filresursen kontrollerar du att brand väggen inte blockerar TCP-port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montera resursen

Montera Azure-filresursen i den lokala katalogen. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Kommandot ovan använder [monterings](https://linux.die.net/man/8/mount) kommandot för att montera Azure-filresursen och alternativ som är speciella för [CIFS](https://linux.die.net/man/8/mount.cifs). Mer specifikt, alternativen file_mode och dir_mode anger filer och kataloger för behörighet `0777` . `0777`Behörigheten ger Läs-, skriv-och körnings behörighet för alla användare. Du kan ändra dessa behörigheter genom att ersätta värdena med andra [chmod-behörigheter](https://en.wikipedia.org/wiki/Chmod). Du kan också använda andra [CIFS](https://linux.die.net/man/8/mount.cifs) -alternativ som GID eller UID. 


## <a name="persist-the-mount"></a>Behåll monteringen

När du startar om den virtuella Linux-datorn demonteras den monterade SMB-resursen under avstängning. För att montera om SMB-resursen vid start lägger du till en rad i Linux-/etc/fstab. Linux använder fstab-filen för att visa de fil system som måste monteras under start processen. Genom att lägga till SMB-resursen ser du till att fil lagrings resursen är ett permanent monterat fil system för den virtuella Linux-datorn. Det går att lägga till File Storage SMB-resursen till en ny virtuell dator när du använder Cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

För ökad säkerhet i produktions miljöer bör du lagra dina autentiseringsuppgifter utanför fstab.

## <a name="next-steps"></a>Nästa steg

- [Använda Cloud-Init för att anpassa en virtuell Linux-dator när den skapas](using-cloud-init.md)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md)
- [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md)
