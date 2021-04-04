---
title: Spara filer i Azure Cloud Shell | Microsoft Docs
description: Genom gång av hur Azure Cloud Shell sparar filer.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152273"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Bevara filer i Azure Cloud Shell
Cloud Shell använder Azure File Storage för att spara filer mellan sessioner. Vid inledande start blir Cloud Shell att du kan associera en ny eller befintlig fil resurs för att spara filer mellan sessioner.

> [!NOTE]
> Bash och PowerShell delar samma fil resurs. Det går bara att koppla en fil resurs till automatisk montering i Cloud Shell.

> [!NOTE]
> Azure Storage-brandväggen stöds inte för moln gränssnitts lagrings konton.

## <a name="create-new-storage"></a>Skapa ny lagring

När du använder grundläggande inställningar och bara väljer en prenumeration skapar Cloud Shell tre resurser för din räkning i den region som stöds, som är närmast dig:
* Resursgrupp: `cloud-shell-storage-<region>`
* Lagrings konto: `cs<uniqueGuid>`
* Fil resurs: `cs-<user>-<domain>-com-<uniqueGuid>`

![Prenumerations inställningen](media/persisting-shell-storage/basic-storage.png)

Fil resursen monteras som `clouddrive` i din `$Home` katalog. Detta är en engångs åtgärd och fil resursen monteras automatiskt i efterföljande sessioner. 

Fil resursen innehåller också en avbildning på 5 GB som skapas för dig och som automatiskt sparar data i din `$Home` katalog. Detta gäller både bash och PowerShell.

## <a name="use-existing-resources"></a>Använd befintliga resurser

Med alternativet Avancerat kan du associera befintliga resurser. När du väljer en Cloud Shell region måste du välja ett lagrings konto som finns i samma region. Om din tilldelade region t. ex. är västra USA måste du associera en fil resurs som finns i västra USA.

När installations programmet för lagring visas väljer du **Visa avancerade inställningar** för att visa fler alternativ. De ifyllda lagrings alternativ filtren för lokalt redundant lagring (LRS), Geo-redundant lagring (GRS) och ZRS-konton (Zone-redundant lagring). 

> [!NOTE]
> Du rekommenderas att använda GRS-eller ZRS-lagrings konton för ytterligare återhämtning för din fil resurs. Vilken typ av redundans beror på dina mål och pris inställningar. [Läs mer om replikeringsalternativ för Azure Storage-konton](../storage/common/storage-redundancy.md).

![Resurs grupps inställningen](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Skydda lagrings åtkomst
För säkerhet bör varje användare etablera sitt eget lagrings konto.  För rollbaserad åtkomst kontroll i Azure (Azure RBAC) måste användarna ha deltagar åtkomst eller högre på lagrings konto nivå.

Cloud Shell använder en Azure-filresurs i ett lagrings konto i en angiven prenumeration. På grund av ärvda behörigheter kommer användare med tillräcklig åtkomst behörighet till prenumerationen att kunna komma åt alla lagrings konton och fil resurser som ingår i prenumerationen.

Användarna bör låsa åtkomsten till sina filer genom att ange behörigheterna på lagrings kontot eller på prenumerations nivån.

## <a name="supported-storage-regions"></a>Lagrings regioner som stöds
För att hitta din aktuella region kan du köra `env` i bash och leta upp variabeln `ACC_LOCATION` eller från PowerShell-körning `$env:ACC_LOCATION` . Fil resurser får en avbildning på 5 GB som skapats så att du kan spara din `$Home` katalog.

Det finns Cloud Shell datorer i följande regioner:

|Område|Region|
|---|---|
|Nord- och Sydamerika|Östra USA, södra centrala USA, västra USA|
|Europa|Europa, norra, Europa, västra|
|Asien och stillahavsområdet|Indien, centrala, Sydostasien|

Kunderna bör välja en primär region, om de inte har ett krav på att deras data i vila ska lagras i en viss region. Om de har sådana krav bör en sekundär lagrings region användas.

### <a name="secondary-storage-regions"></a>Sekundära lagrings regioner
Om en sekundär lagrings region används, finns det associerade Azure Storage-kontot i en annan region som Cloud Shell datorn som du monterar dem på. Jane kan till exempel ställa in sitt lagrings konto så att det finns i Kanada, östra, en sekundär region, men datorn som hon är monterad på finns fortfarande i en primär region. Hennes data i vila finns i Kanada, men bearbetas i USA.

> [!NOTE]
> Om en sekundär region används kan fil åtkomst och start tid för Cloud Shell vara långsammare.

En användare kan köra `(Get-CloudDrive | Get-AzStorageAccount).Location` i PowerShell för att se platsen för fil resursen.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Begränsa resurs skapande med en Azure-resurs princip
De lagrings konton som du skapar i Cloud Shell taggas med `ms-resource-usage:azure-cloud-shell` . Om du inte vill att användare ska kunna skapa lagrings konton i Cloud Shell skapar du en [princip för Azure-resurser för Taggar](../governance/policy/samples/index.md) som utlösts av den här taggen.

## <a name="how-cloud-shell-storage-works"></a>Så här fungerar Cloud Shell Storage 
Cloud Shell sparar filer genom båda följande metoder: 
* Skapa en disk avbildning av `$Home` katalogen för att spara allt innehåll i katalogen. Disk avbildningen sparas i den angivna fil resursen som `acc_<User>.img` på `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` och synkroniserar automatiskt ändringar. 
* Montera din angivna fil resurs som `clouddrive` i din `$Home` katalog för direkt interaktion av fil resurser. `/Home/<User>/clouddrive` är mappad till `fileshare.storage.windows.net/fileshare` .
 
> [!NOTE]
> Alla filer i `$Home` katalogen, till exempel SSH-nycklar, finns kvar i din användar disk avbildning, som lagras i den monterade fil resursen. Använd bästa praxis när du bevarar information i din `$Home` katalog och monterade fil resursen.

## <a name="clouddrive-commands"></a>clouddrive-kommandon

### <a name="use-the-clouddrive-command"></a>Använda `clouddrive` kommandot
I Cloud Shell kan du köra ett kommando som kallas `clouddrive` , vilket gör att du kan uppdatera fil resursen som är monterad till Cloud Shell manuellt.

![Köra kommandot "clouddrive"](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista `clouddrive`
För att identifiera vilken fil resurs som monteras som `clouddrive` kör du `df` kommandot. 

Fil Sök vägen till clouddrive visar ditt lagrings konto namn och fil resurs i URL: en. Till exempel `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Montera en ny clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Krav för manuell montering
Du kan uppdatera fil resursen som är kopplad till Cloud Shell med hjälp av `clouddrive mount` kommandot.

Om du monterar en befintlig fil resurs måste lagrings kontona finnas i din SELECT Cloud Shell-region. Hämta platsen genom att köra `env` och kontrol lera `ACC_LOCATION` .

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount`Kommandot

> [!NOTE]
> Om du monterar en ny fil resurs skapas en ny användar avbildning för din `$Home` katalog. Din föregående `$Home` bild behålls i din tidigare fil resurs.

Kör `clouddrive mount` kommandot med följande parametrar:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Om du vill visa mer information, kör `clouddrive mount -h` , som du ser här:

![Kör clouddrive-mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Demontera clouddrive
Du kan demontera en fil resurs som är monterad till Cloud Shell när som helst. Eftersom Cloud Shell kräver att en monterad fil resurs används uppmanas du att skapa och montera en annan fil resurs i nästa session.

1. Kör `clouddrive unmount`.
2. Bekräfta och bekräfta prompter.

Fil resursen kommer att finnas kvar om du inte tar bort den manuellt. Cloud Shell kommer inte längre att söka efter den här fil resursen på efterföljande sessioner. Om du vill visa mer information, kör `clouddrive unmount -h` , som du ser här:

![Kör clouddrive-unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Även om du kör det här kommandot tar du inte bort några resurser, manuellt tar bort en resurs grupp, ett lagrings konto eller en fil resurs som är mappad till Cloud Shell raderar `$Home` katalog disk avbildningen och eventuella filer i fil resursen. Det går inte att ångra den här åtgärden.
## <a name="powershell-specific-commands"></a>PowerShell-/regionsspecifika kommandon

### <a name="list-clouddrive-azure-file-shares"></a>Visa en lista över `clouddrive` Azure-filresurser
`Get-CloudDrive`Cmdleten hämtar information om Azure-filresurser som för närvarande monteras av `clouddrive` i Cloud Shell. <br>
![Kör get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Demontera `clouddrive`
Du kan demontera en Azure-filresurs som är monterad till Cloud Shell när som helst. Om Azure-filresursen har tagits bort uppmanas du att skapa och montera en ny Azure-fil resurs vid nästa session.

`Dismount-CloudDrive`Cmdleten demonterar en Azure-filresurs från det aktuella lagrings kontot. Demonteringen av `clouddrive` avslutar den aktuella sessionen. Användaren uppmanas att skapa och montera en ny Azure-fil resurs under nästa session.
![Köra dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Obs: om du behöver definiera en funktion i en fil och anropa den från PowerShell-cmdletarna måste punkt operatorn inkluderas. Till exempel:. .\MyFunctions.ps1

## <a name="next-steps"></a>Nästa steg
[Cloud Shell snabb start](quickstart.md) <br>
[Läs mer om lagring av Microsoft Azure filer](../storage/files/storage-files-introduction.md) <br>
[Läs mer om lagrings märken](../azure-resource-manager/management/tag-resources.md) <br>