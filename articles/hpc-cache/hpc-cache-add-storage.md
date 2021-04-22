---
title: Lägga till lagringsutrymme i en Azure HPC Cache
description: Definiera lagringsmål så att dina Azure HPC Cache kan använda ditt lokala NFS-system eller Azure Blob-containrar för långsiktig fillagring
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862245"
---
# <a name="add-storage-targets"></a>Lägga till lagringsmål

*Lagringsmål* är backend-lagring för filer som nås via en Azure HPC Cache. Du kan lägga till NFS-lagring (till exempel ett lokalt maskinvarusystem) eller lagra data i Azure Blob.

Du kan definiera upp till 20 olika lagringsmål för en cache. Cachen visar alla lagringsmål i ett aggregerat namnområde.

Sökvägarna för namnområdet konfigureras separat när du har lagt till lagringsmålen. I allmänhet kan ett NFS-lagringsmål ha upp till tio namnrymdssökvägar eller mer för vissa stora konfigurationer. Mer [information finns i Sökvägar för NFS-namnrymd.](add-namespace-paths.md#nfs-namespace-paths)

Kom ihåg att lagringsexporterna måste vara tillgängliga från cachens virtuella nätverk. För lokal maskinvarulagring kan du behöva konfigurera en DNS-server som kan matcha värdnamn för NFS-lagringsåtkomst. Läs mer i [DNS-åtkomst.](hpc-cache-prerequisites.md#dns-access)

Lägg till lagringsmål när du har skapat din cache. Följ den här processen:

1. [Skapa cachen](hpc-cache-create.md)
1. Definiera ett lagringsmål (information i den här artikeln)
1. [Skapa klientriktade sökvägar](add-namespace-paths.md) (för det [aggregerade namnområdet](hpc-cache-namespace.md))

Proceduren för att lägga till ett lagringsmål skiljer sig något beroende på vilken typ av lagring som används. Information om var och en finns nedan.

Klicka på bilden nedan för att se [en videodemonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagringsmål från Azure Portal.

[![videominiatyr: Azure HPC Cache: Installation (klicka för att besöka videosidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Lägga till ett nytt Azure Blob Storage-mål

Ett nytt Blob Storage-mål behöver en tom blobcontainer eller en container som fylls med data Azure HPC Cache molnfilsystemformatet. Läs mer om förinläsning av en blobcontainer [i Flytta data till Azure Blob Storage.](hpc-cache-ingest.md)

Sidan Azure Portal **Lägg till lagringsmål** innehåller alternativet att skapa en ny blobcontainer precis innan du lägger till den.

> [!NOTE]
> För NFS-monterad bloblagring använder du [lagringsmåltypen ADLS-NFS.](#)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna cacheinstansen från Azure Portal och klicka på **Lagringsmål** i det vänstra sidofältet.

![skärmbild av inställningarna > lagringsmålsidan, med två befintliga lagringsmål i en tabell och en markering runt knappen + lägg till lagringsmål ovanför tabellen](media/add-storage-target-button.png)

Sidan **Lagringsmål** visar en lista över alla befintliga mål och ger en länk för att lägga till ett nytt.

Klicka på **knappen Lägg till lagringsmål.**

![skärmbild av sidan Lägg till lagringsmål, ifylld med information om ett nytt Azure Blob Storage-mål](media/hpc-cache-add-blob.png)

Ange den här informationen för att definiera en Azure Blob-container.

* **Lagringsmålnamn** – Ange ett namn som identifierar lagringsmålet i Azure HPC Cache.
* **Måltyp** – Välj **Blob**.
* **Lagringskonto** – Välj det konto som du vill använda.

  Du måste ge cacheinstansen åtkomst till lagringskontot enligt beskrivningen i Lägg [till åtkomstroller](#add-the-access-control-roles-to-your-account).

  Information om vilken typ av lagringskonto du kan använda finns i [Krav för Blob Storage.](hpc-cache-prerequisites.md#blob-storage-requirements)

* **Lagringscontainer** – Välj blobcontainern för det här målet eller klicka **på Skapa ny.**

  ![skärmbild av dialogrutan för att ange namn och åtkomstnivå (privat) för den nya containern](media/add-blob-new-container.png)

När du är klar klickar du **på OK** för att lägga till lagringsmålet.

> [!NOTE]
> Om brandväggen för lagringskontot är inställd på att begränsa åtkomsten till endast "valda nätverk" använder du den tillfälliga lösning som beskrivs i Work around Blob storage account firewall settings (Arbeta runt [brandväggsinställningar för Blob Storage-konto).](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>Lägga till åtkomstkontrollroller till ditt konto

Azure HPC Cache använder rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../role-based-access-control/index.yml) för att ge cachetjänsten åtkomst till ditt lagringskonto för Azure Blob Storage-mål.

Lagringskontots ägare måste [](../role-based-access-control/built-in-roles.md#storage-account-contributor) uttryckligen lägga till rollerna Lagringskontodeltagare och [Storage Blob Data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) för användaren "HPC Cache Resursprovider".

Du kan göra detta i förväg eller genom att klicka på en länk på sidan där du lägger till ett Blob Storage-mål. Tänk på att det kan ta upp till fem minuter för rollinställningarna att spridas via Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagringsmål.

Steg för att lägga till Azure-roller:

1. Öppna sidan **Åtkomstkontroll (IAM)** för lagringskontot. (Länken på sidan **Lägg till lagringsmål** öppnar automatiskt den här sidan för det valda kontot.)

1. Klicka på **+** överst på sidan och välj Lägg till en **rolltilldelning.**

1. Välj rollen "Lagringskontodeltagare" i listan.

1. I fältet **Tilldela åtkomst till låter** du standardvärdet vara markerat ("Azure AD-användare, grupp eller tjänstens huvudnamn").  

1. I fältet **Välj** söker du efter "hpc".  Den här strängen ska matcha ett huvudnamn för tjänsten med namnet "HPC Cache Resource Provider". Klicka på huvudnamn för att välja det.

   > [!NOTE]
   > Om en sökning efter "hpc" inte fungerar kan du försöka använda strängen "storagecache" i stället. Användare som har deltagit i förhandsversioner (före GA) kan behöva använda det äldre namnet för tjänstens huvudnamn.

1. Klicka på **knappen** Spara längst ned.

1. Upprepa den här processen för att tilldela rollen "Storage Blob Data-deltagare".  

![skärmbild av lägg till grafiska användargränssnitt för rolltilldelning](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Krav: Åtkomst till lagringskonto

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Innan du lägger till ett bloblagringsmål kontrollerar du att cacheminnet har rätt roller för åtkomst till lagringskontot och att brandväggsinställningarna tillåter att lagringsmålet skapas.

Azure HPC Cache använder rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../role-based-access-control/index.yml) för att ge cachetjänsten åtkomst till ditt lagringskonto för Azure Blob Storage-mål.

Lagringskontots ägare måste [](../role-based-access-control/built-in-roles.md#storage-account-contributor) uttryckligen lägga till rollerna Lagringskontodeltagare och [Storage Blob Data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) för användaren "HPC Cache Resursprovider".

Det går inte att skapa lagringsmålet om cachen inte har dessa roller.

Det kan ta upp till fem minuter för rollinställningarna att spridas via Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagringsmål.

Detaljerade [anvisningar finns i Lägga till eller ta bort Azure-rolltilldelningar](../role-based-access-control/role-assignments-cli.md) med Azure CLI.

Kontrollera även lagringskontots brandväggsinställningar. Om brandväggen är inställd på att begränsa åtkomsten till endast "valda nätverk" kan det hända att skapandet av lagringsmålet misslyckas. Använd den lösning som beskrivs i Work around Blob storage account firewall settings (Arbeta [runt brandväggsinställningar för Blob Storage-konto).](hpc-cache-blob-firewall-fix.md)

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Lägga till ett bloblagringsmål med Azure CLI

Använd gränssnittet [az hpc-cache blob-storage-target add för](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) att definiera ett Azure Blob Storage-mål.

> [!NOTE]
> Azure CLI-kommandona kräver för närvarande att du skapar en sökväg för namnområdet när du lägger till ett lagringsmål. Detta skiljer sig från processen som används med Azure Portal gränssnittet.

Förutom standardparametrarna för resursgrupp och cachenamn måste du ange följande alternativ för lagringsmålet:

* ``--name`` – Ange ett namn som identifierar lagringsmålet i Azure HPC Cache.

* ``--storage-account`` – Kontoidentifieraren, i det här formuläret: /subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Information om vilken typ av lagringskonto du kan använda finns i [Krav för Blob Storage.](hpc-cache-prerequisites.md#blob-storage-requirements)

* ``--container-name`` – Ange namnet på containern som ska användas för det här lagringsmålet.

* ``--virtual-namespace-path`` – Ange sökvägen till den klientriktade filen för det här lagringsmålet. Omge sökvägar inom citattecken. Läs [Planera det aggregerade namnområdet om](hpc-cache-namespace.md) du vill veta mer om funktionen för virtuellt namnområde.

Exempelkommando:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Lägga till ett nytt NFS-lagringsmål

Ett NFS-lagringsmål har andra inställningar än ett Blob Storage-mål. Användningsmodellinställningen hjälper cacheminnet att effektivt cachelagra data från det här lagringssystemet.

![Skärmbild av sidan lägg till lagringsmål med NFS-målet definierat](media/add-nfs-target.png)

> [!NOTE]
> Innan du skapar ett NFS-lagringsmål kontrollerar du att lagringssystemet är åtkomligt från Azure HPC Cache och uppfyller behörighetskraven. Det går inte att skapa lagringsmålet om cachen inte kan komma åt lagringssystemet. Läs [kraven för NFS-lagring](hpc-cache-prerequisites.md#nfs-storage-requirements) [och Felsöka problem med NAS-konfiguration och NFS-lagringsmål](troubleshoot-nas.md) för mer information.

### <a name="choose-a-usage-model"></a>Välj en användningsmodell
<!-- referenced from GUI by aka.ms link -->

När du skapar ett lagringsmål som använder NFS för att nå lagringssystemet måste du välja en användningsmodell för det målet. Den här modellen avgör hur dina data cachelagras.

Läs [Förstå användningsmodeller](cache-usage-models.md) för mer information om alla dessa inställningar.

Med de inbyggda användningsmodellerna kan du välja hur du balanserar snabba svar med risken att få inaktuella data. Om du vill optimera hastigheten för läsning av filer kanske du inte bryr dig om huruvida filerna i cacheminnet kontrolleras mot backend-filerna. Om du å andra sidan vill se till att dina filer alltid är uppdaterade med fjärrlagringen väljer du en modell som kontrollerar ofta.

Dessa tre alternativ omfattar de flesta situationer:

* **Läsa tunga, sfrekventa skrivningar** – Påskyndar läsåtkomsten till filer som är statiska eller sällan har ändrats.

  Det här alternativet cachelagrar filer från klientläsningar, men skickar klient skriver vidare till backend-lagringen omedelbart. Filer som lagras i cachen jämförs inte automatiskt med filerna på NFS-lagringsvolymen.

  Använd inte det här alternativet om det finns en risk att en fil ändras direkt i lagringssystemet utan att först skriva den till cachen. Om det händer är den cachelagrade versionen av filen inte synkroniserad med backend-filen.

* **Mer än 15 % skrivningar** – det här alternativet påskyndar både läs- och skrivprestanda.

  Både klientläsningar och klient skrivningar cachelagras. Filer i cacheminnet antas vara nyare än filer i backend-lagringssystemet. Cachelagrade filer kontrolleras endast automatiskt mot filerna på backend-lagring var åttonde timme. Ändrade filer i cachen skrivs till backend-lagringssystemet efter att de har funnits i cacheminnet i 20 minuter utan ytterligare ändringar.

  Använd inte det här alternativet om några klienter monterar backend-lagringsvolymen direkt, eftersom det finns en risk att den har inaktuella filer.

* **Klienter skriver till NFS-målet och** kringgår cachen – Välj det här alternativet om några klienter i arbetsflödet skriver data direkt till lagringssystemet utan att först skriva till cachen, eller om du vill optimera datakonsekvensen.

  Filer som klienter begär cachelagras, men eventuella ändringar av filerna från klienten skickas till lagringssystemet i backend-systemet omedelbart. Filer i cachen kontrolleras ofta mot backend-versionerna för uppdateringar. Den här verifieringen upprätthåller datakonsekvensen när filer ändras direkt i lagringssystemet i stället för via cachen.

Mer information om de andra alternativen finns i [Förstå användningsmodeller.](cache-usage-models.md)

I den här tabellen sammanfattas skillnaderna mellan alla användningsmodeller:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Värdet **för backend-verifiering** visar när cachen automatiskt jämför sina filer med källfiler i fjärrlagring. Du kan dock utlösa en jämförelse genom att skicka en klientbegäran som innehåller en readdirplus-åtgärd i backend-lagringssystemet. Readdirplus är ett NFS-standard-API (kallas även utökad läsning) som returnerar katalogmetadata, vilket gör att cachen jämför och uppdaterar filer.

### <a name="create-an-nfs-storage-target"></a>Skapa ett NFS-lagringsmål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna cacheinstansen från Azure Portal och klicka på **Lagringsmål** i den vänstra sidopanelen.

![skärmbild av inställningarna > lagringsmålsidan, med två befintliga lagringsmål i en tabell och en markering runt knappen + Lägg till lagringsmål ovanför tabellen](media/add-storage-target-button.png)

Sidan **Lagringsmål** visar en lista över alla befintliga mål och ger en länk för att lägga till ett nytt.

Klicka på **knappen Lägg till lagringsmål.**

![Skärmbild av sidan lägg till lagringsmål där NFS-målet har definierats](media/add-nfs-target.png)

Ange den här informationen för ett NFS-stödt lagringsmål:

* **Lagringsmålnamn** – Ange ett namn som identifierar det här lagringsmålet i Azure HPC Cache.

* **Måltyp** – Välj **NFS.**

* **Värdnamn** – Ange IP-adressen eller det fullständiga domännamnet för ditt NFS-lagringssystem. (Använd bara ett domännamn om ditt cacheminne har åtkomst till en DNS-server som kan matcha namnet.)

* **Användningsmodell** – Välj en av profilerna för cachelagring av data baserat på ditt arbetsflöde, som beskrivs [i Välj en användningsmodell](#choose-a-usage-model) ovan.

När du är klar klickar du **på OK** för att lägga till lagringsmålet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd Azure [CLI-kommandot az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) för att skapa lagringsmålet.

> [!NOTE]
> Azure CLI-kommandona kräver för närvarande att du skapar en sökväg för namnområdet när du lägger till ett lagringsmål. Detta skiljer sig från processen som används med Azure Portal gränssnittet.

Ange dessa värden utöver cachenamnet och cacheresursgruppen:

* ``--name`` – Ange ett namn som identifierar lagringsmålet i Azure HPC Cache.
* ``--nfs3-target`` – IP-adressen för ditt NFS-lagringssystem. (Du kan använda ett fullständigt domännamn här om ditt cacheminne har åtkomst till en DNS-server som kan matcha namnet.)
* ``--nfs3-usage-model`` – En av profilerna för cachelagring av data, som beskrivs [i Choose a usage model (Välj en användningsmodell)](#choose-a-usage-model)ovan.

  Kontrollera namnen på användningsmodellerna med kommandot [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction`` – Parametern för knutpunkter länkar den klientriktade virtuella filsökvägen med en exportsökväg i lagringssystemet.

  Ett NFS-lagringsmål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export eller underkatalog i samma lagringssystem. Skapa alla sökvägar för ett lagringssystem på ett lagringsmål.

  Du kan [lägga till och redigera sökvägar för namnområden](add-namespace-paths.md) på ett lagringsmål när som helst.

  Parametern ``--junction`` använder följande värden:

  * ``namespace-path`` – Den klientriktade virtuella filsökvägen
  * ``nfs-export`` – Lagringssystemet exporteras för att associeras med den klientriktade sökvägen
  * ``target-path`` (valfritt) – En underkatalog för exporten, om det behövs

  Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Läs [Konfigurera aggregerat namnområde om](hpc-cache-namespace.md) du vill veta mer om funktionen för virtuellt namnområde.

Exempelkommando:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Utdata:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Lägga till ett nytt LAGRINGsmål för ADLS-NFS (FÖRHANDSVERSION)

ADLS-NFS-lagringsmål använder Azure Blob-containrar som stöder protokollet Network File System (NFS) 3.0.

> [!NOTE]
> Stöd för NFS 3.0-protokollet för Azure Blob Storage är i offentlig förhandsversion. Tillgängligheten är begränsad och funktionerna kan ändras från och med nu och när funktionen blir allmänt tillgänglig. Använd inte förhandsversionsteknik i produktionssystem.
>
> Den [senaste informationen finns i Protokollstöd för NFS 3.0.](../storage/blobs/network-file-system-protocol-support.md)

ADLS-NFS-lagringsmål har vissa likheter med Blob Storage-mål och vissa med NFS-lagringsmål. Exempel:

* Precis som ett Blob Storage-mål måste du ge Azure HPC Cache behörighet att komma [åt ditt lagringskonto](#add-the-access-control-roles-to-your-account).
* Precis som ett NFS-lagringsmål måste du ange en [cacheanvändningsmodell.](#choose-a-usage-model)
* Eftersom NFS-aktiverade blobcontainrar har en NFS-kompatibel hierarkisk struktur behöver du inte använda cachen för att mata in data, och containrarna kan läsas av andra NFS-system. Du kan läsa in data i förväg i en ADLS-NFS-container och sedan lägga till dem i en HPC Cache som ett lagringsmål och sedan komma åt data senare utanför en HPC Cache. När du använder en standardblobcontainer som ett HPC Cache-lagringsmål skrivs data i ett egenutvecklat format och kan bara nås från andra Azure HPC Cache kompatibla produkter.

Innan du kan skapa ett ADLS-NFS-lagringsmål måste du skapa ett NFS-aktiverat lagringskonto. Följ tipsen i [Krav för att Azure HPC Cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) anvisningarna i Montera Blob Storage med hjälp av [NFS](../storage/blobs/network-file-system-protocol-support-how-to.md). När ditt lagringskonto har ställts in kan du skapa en ny container när du skapar lagringsmålet.

Läs [Använda NFS-monterad bloblagring med Azure HPC Cache](nfs-blob-considerations.md) mer information om den här konfigurationen.

Om du vill skapa ett ADLS-NFS-lagringsmål öppnar **du sidan Lägg till** lagringsmål i Azure Portal. (Ytterligare metoder är under utveckling.)

![Skärmbild av sidan lägg till lagringsmål med ADLS-NFS-målet definierat](media/add-adls-target.png)

Ange den här informationen.

* **Lagringsmålnamn** – Ange ett namn som identifierar det här lagringsmålet i Azure HPC Cache.
* **Måltyp** – Välj **ADLS-NFS.**
* **Lagringskonto** – Välj det konto som du vill använda. Om ditt NFS-aktiverade lagringskonto inte visas i listan kontrollerar du att det uppfyller kraven och att cachen kan komma åt det.

  Du måste ge cacheinstansen åtkomst till lagringskontot enligt beskrivningen i Lägg [till åtkomstroller](#add-the-access-control-roles-to-your-account).

* **Lagringscontainer** – Välj den NFS-aktiverade blobcontainern för det här målet eller klicka **på Skapa ny.**

* **Användningsmodell** – Välj en av profilerna för cachelagring av data baserat på ditt arbetsflöde, vilket beskrivs [i Välj en användningsmodell](#choose-a-usage-model) ovan.

När du är klar klickar du **på OK** för att lägga till lagringsmålet.

## <a name="view-storage-targets"></a>Visa lagringsmål

Du kan använda Azure Portal Azure CLI för att visa de lagringsmål som redan har definierats för din cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Från Azure Portal du cacheinstansen och klickar på **Lagringsmål,** som finns under rubriken Inställningar i det vänstra sidofältet. Sidan lagringsmål visar en lista över alla befintliga mål och kontroller för att lägga till eller ta bort dem.

Klicka på namnet på ett lagringsmål för att öppna sidan med information.

Läs [Redigera lagringsmål om du](hpc-cache-edit-storage.md) vill veta mer.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

Använd alternativet [az hpc-cache storage-target list för](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) att visa de befintliga lagringsmålen för en cache. Ange cachenamnet och resursgruppen (om du inte har angett den globalt).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Använd [az hpc-cache storage-target show för](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) att se information om ett visst lagringsmål. (Ange lagringsmålet efter namn.)

Exempel:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Nästa steg

När du har skapat lagringsmål fortsätter du med dessa uppgifter för att förbereda din cache för användning:

* [Konfigurera det aggregerade namnområdet](add-namespace-paths.md)
* [Montera Azure HPC Cache](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)

Om du behöver uppdatera några inställningar kan du redigera [ett lagringsmål.](hpc-cache-edit-storage.md)
