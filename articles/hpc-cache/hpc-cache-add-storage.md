---
title: Lägga till lagring till en Azure HPC-cache
description: Definiera lagrings mål så att Azure HPC-cachen kan använda ditt lokala NFS-system eller Azure Blob-behållare för långsiktig fil lagring
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: afb896100ea60c21aaf37890d7b520bf38c6ce18
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772730"
---
# <a name="add-storage-targets"></a>Lägga till lagringsmål

*Lagrings målen* är Server dels lagring för filer som nås via en Azure HPC-cache. Du kan lägga till NFS-lagring (t. ex. ett lokalt maskin varu system) eller lagra data i Azure blob.

Du kan definiera upp till 20 olika lagrings mål för ett cacheminne. Cachen visar alla lagrings mål i ett sammanlagt namn område.

Namn rymds Sök vägarna konfigureras separat när du har lagt till lagrings målen. I allmänhet kan ett NFS-lagrings mål ha upp till tio namn rymds Sök vägar eller mer för vissa stora konfigurationer. Mer information finns i [sökvägar till NFS-namnområden](add-namespace-paths.md#nfs-namespace-paths) .

Kom ihåg att lagrings exporten måste vara tillgänglig från cachens virtuella nätverk. För lokal maskin varu lagring kan du behöva konfigurera en DNS-server som kan matcha värdnamn för NFS-lagrings åtkomst. Läs mer i [DNS-åtkomst](hpc-cache-prerequisites.md#dns-access).

Lägg till lagrings mål när du har skapat din cache. Följ den här processen:

1. [Skapa cachen](hpc-cache-create.md)
1. Definiera ett lagrings mål (information i den här artikeln)
1. [Skapa de klientbaserade Sök vägarna](add-namespace-paths.md) (för den [aggregerade namn rymden](hpc-cache-namespace.md))

Proceduren för att lägga till ett lagrings mål skiljer sig något beroende på vilken typ av lagring som används. Information om var och en finns nedan.

Klicka på bilden nedan om du vill titta på en [video demonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagrings mål från Azure Portal.

[![video miniatyr: Azure HPC cache: Setup (Klicka för att besöka video sidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Lägg till ett nytt Azure Blob Storage-mål

Ett nytt Blob Storage-mål måste ha en tom BLOB-behållare eller en behållare som är ifylld med data i Azure HPC-cachens moln fil system format. Läs mer om att för hands läsa in en BLOB-behållare i [Flytta data till Azure Blob Storage](hpc-cache-ingest.md).

Sidan Azure Portal **Lägg till lagrings mål** innehåller alternativet att skapa en ny BLOB-behållare precis innan du lägger till den.

> [!NOTE]
> För NFS-monterad blob-lagring, Använd [måltypen ADLS-NFS-lagring](#) .

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna din cache-instans från Azure Portal och klicka på **lagrings mål** på den vänstra sid panelen.

![skärm bild av sidan Inställningar > lagrings mål, med två befintliga lagrings mål i en tabell och en markering runt knappen + Lägg till lagrings mål ovanför tabellen](media/add-storage-target-button.png)

Sidan **lagrings mål** listar alla befintliga mål och innehåller en länk för att lägga till en ny.

Klicka på knappen **Lägg till lagrings mål** .

![skärm bild av sidan Lägg till lagrings mål, ifylld med information för ett nytt Azure Blob Storage-mål](media/hpc-cache-add-blob.png)

Ange den här informationen för att definiera en Azure Blob-behållare.

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* **Måltyp** – Välj **BLOB**.
* **Lagrings konto** – Välj det konto som du vill använda.

  Du måste auktorisera cache-instansen för att komma åt lagrings kontot enligt beskrivningen i [Lägg till åtkomst roller](#add-the-access-control-roles-to-your-account).

  Information om vilken typ av lagrings konto du kan använda finns i [krav för Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Lagrings behållare** – Välj BLOB-behållaren för målet eller klicka på **Skapa ny**.

  ![skärm bild av dialog rutan för att ange namn och åtkomst nivå (privat) för ny behållare](media/add-blob-new-container.png)

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

> [!NOTE]
> Om ditt lagrings kontos brand vägg är inställt på begränsa åtkomsten till endast "valda nätverk" använder du den tillfälliga lösningen som dokumenteras i [brand Väggs inställningarna för Blob Storage-kontot](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Lägg till roller för åtkomst kontroll i ditt konto

Azure HPC cache använder [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/index.yml) för att ge Cache-tjänsten åtkomst till ditt lagrings konto för Azure Blob Storage-mål.

Lagrings kontots ägare måste uttryckligen lägga till rollerna [lagrings konto deltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor) och [Storage BLOB data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) för användaren "HPC-Provider för HPC-cache".

Du kan göra detta i förväg eller genom att klicka på en länk på sidan där du lägger till ett Blob Storage-mål. Tänk på att det kan ta upp till fem minuter innan roll inställningarna sprids genom Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagrings mål.

Steg för att lägga till Azure-roller:

1. Öppna sidan **åtkomst kontroll (IAM)** för lagrings kontot. (Länken på sidan **Lägg till lagrings mål** öppnar automatiskt den här sidan för det valda kontot.)

1. Klicka på **+** överst på sidan och välj **Lägg till en roll tilldelning**.

1. Välj rollen "lagrings konto deltagare" i listan.

1. I fältet **tilldela åtkomst till** lämnar du standardvärdet markerat ("Azure AD User, Group eller service huvud namn").  

1. I **Välj** -fältet söker du efter "HPC".  Den här strängen ska matcha ett tjänst huvud namn med namnet "HPC-Provider för HPC-cache". Klicka på det primära objektet för att välja det.

   > [!NOTE]
   > Om en sökning efter "HPC" inte fungerar kan du prova att använda strängen "storagecache" i stället. Användare som deltog i för hands versionerna (före GA) kan behöva använda det äldre namnet för tjänstens huvud namn.

1. Klicka på knappen **Spara** längst ned.

1. Upprepa den här processen för att tilldela rollen "Storage BLOB data Contributor".  

![skärm bild av användar gränssnittet för Lägg till roll tilldelning](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Förutsättning: åtkomst till lagrings konto

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Innan du lägger till ett Blob Storage-mål kontrollerar du att cachen har rätt roller för att komma åt lagrings kontot och att brand Väggs inställningarna tillåter skapande av lagrings mål.

Azure HPC cache använder [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/index.yml) för att ge Cache-tjänsten åtkomst till ditt lagrings konto för Azure Blob Storage-mål.

Lagrings kontots ägare måste uttryckligen lägga till rollerna [lagrings konto deltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor) och [Storage BLOB data-deltagare](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) för användaren "HPC-Provider för HPC-cache".

Det gick inte att skapa lagrings mål om cacheminnet inte har dessa roller.

Det kan ta upp till fem minuter innan roll inställningarna sprids genom Azure-miljön, så du bör vänta några minuter efter att du har lagt till rollerna innan du skapar ett lagrings mål.

Läs [Lägg till eller ta bort Azure Role-tilldelningar med hjälp av Azure CLI](../role-based-access-control/role-assignments-cli.md) för detaljerade instruktioner.

Kontrol lera också ditt lagrings kontos brand Väggs inställningar. Om brand väggen är inställd på att begränsa åtkomsten till endast "valda nätverk" kan det hända att det inte går att skapa lagrings målet. Använd den lösning som dokumenteras i [nätverks brand Väggs inställningarna för Blob Storage-kontot](hpc-cache-blob-firewall-fix.md).

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Lägga till ett Blob Storage-mål med Azure CLI

Använd [AZ HPC-cache Blob-Storage – Target Add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add) Interface för att definiera ett Azure Blob Storage-mål.

> [!NOTE]
> Azure CLI-kommandona kräver för närvarande att du skapar en namn områdes Sök väg när du lägger till ett lagrings mål. Detta skiljer sig från den process som används med Azure Portal-gränssnittet.

Förutom standard resurs gruppen och cache namn parametrarna måste du ange följande alternativ för lagrings målet:

* ``--name`` – Ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.

* ``--storage-account`` – Konto identifieraren, i följande format:/Subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Information om vilken typ av lagrings konto du kan använda finns i [krav för Blob Storage](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name`` – Ange namnet på den behållare som ska användas för det här lagrings målet.

* ``--virtual-namespace-path`` -Ange sökvägen till klientens fil för det här lagrings målet. Omge sökvägar med citat tecken. Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

Exempel kommando:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Lägg till ett nytt NFS-lagrings mål

Ett NFS-lagrings mål har olika inställningar från ett Blob Storage-mål. Inställningen användnings modell hjälper cacheminnet att effektivt cachelagra data från det här lagrings systemet.

![Skärm bild av sidan Lägg till lagrings mål med NFS-mål definierat](media/add-nfs-target.png)

> [!NOTE]
> Innan du skapar ett NFS-lagrings mål bör du kontrol lera att lagrings systemet är tillgängligt från Azure HPC cache och uppfyller behörighets kraven. Det gick inte att skapa lagrings mål om cachen inte kan komma åt lagrings systemet. Läs om [lagrings krav för NFS](hpc-cache-prerequisites.md#nfs-storage-requirements) och [FELSÖK problem med NAS-konfiguration och NFS-lagring](troubleshoot-nas.md) för mer information.

### <a name="choose-a-usage-model"></a>Välj en användnings modell
<!-- referenced from GUI by aka.ms link -->

När du skapar ett lagrings mål som använder NFS för att nå sitt lagrings system måste du välja en användnings modell för det målet. Den här modellen avgör hur dina data cachelagras.

Läs [förstå användnings modeller](cache-usage-models.md) för mer information om alla dessa inställningar.

Med de inbyggda användnings modellerna kan du välja hur du ska utjämna snabba svar med risken för att få inaktuella data. Om du vill optimera hastigheten för att läsa filer kanske du inte bryr dig om filerna i cacheminnet kontrol leras mot backend-filerna. Å andra sidan, om du vill vara säker på att filerna alltid är uppdaterade med Fjärrlagring, väljer du en modell som söker ofta.

Dessa tre alternativ behandlar de flesta situationer:

* **Läsning tung, ovanliga skrivningar** – snabbare Läs åtkomst till filer som är statiska eller sällan ändrade.

  Med det här alternativet cachelagras filer från klient läsningar, men klient skrivningar skickas direkt till backend-lagringen. Filer som lagras i cacheminnet jämförs inte automatiskt med filerna på NFS-lagrings volymen.

  Använd inte det här alternativet om det finns en risk att en fil kan ändras direkt på lagrings systemet utan att först skriva den till cacheminnet. Om det händer kommer den cachelagrade versionen av filen inte att synkroniseras med backend-filen.

* **Större än 15% skrivningar** – det här alternativet påskyndar både Läs-och skriv prestanda.

  Klient läsningar och klient skrivningar cachelagras. Filerna i cachen antas vara nyare än filer på Server dels lagrings systemet. Cachelagrade filer kontrol leras bara automatiskt mot filerna på Server sidans lagring var åttonde timme. Ändrade filer i cachen skrivs till Server dels lagrings systemet när de har funnits i cachen i 20 minuter utan ytterligare ändringar.

  Använd inte det här alternativet om några klienter monterar backend-fjärrvolymen direkt, eftersom det finns en risk att den har inaktuella filer.

* **Klienter skriver till NFS-målet, vilket kringgår cachen** – Välj det här alternativet om några klienter i arbets flödet skriver data direkt till lagrings systemet utan att först skriva till cachen eller om du vill optimera data konsekvens.

  Filer som klienten begär cachelagras, men ändringar av filerna från klienten skickas direkt till backend-lagrings systemet. Filerna i cacheminnet kontrol leras ofta mot backend-versionerna för uppdateringar. Den här kontrollen upprätthåller data konsekvens när filer ändras direkt i lagrings systemet i stället för via cachen.

Mer information om de andra alternativen finns i [förstå användnings modeller](cache-usage-models.md).

I den här tabellen sammanfattas skillnaderna mellan alla användnings modeller:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Verifiering svärdet på **Server** sidan visar när cachen automatiskt jämför sina filer med källfiler i Fjärrlagring. Du kan dock utlösa en jämförelse genom att skicka en klientbegäran som innehåller en readdirplus-åtgärd på Server dels lagrings systemet. Readdirplus är ett standard-NFS-API (kallas även utökad läsning) som returnerar katalogens metadata, vilket gör att cachen jämför och uppdaterar filer.

### <a name="create-an-nfs-storage-target"></a>Skapa ett NFS-lagrings mål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna din cache-instans från Azure Portal och klicka på **lagrings mål** på den vänstra sid panelen.

![skärm bild av sidan Inställningar > lagrings mål, med två befintliga lagrings mål i en tabell och en markering runt knappen + Lägg till lagrings mål ovanför tabellen](media/add-storage-target-button.png)

Sidan **lagrings mål** listar alla befintliga mål och innehåller en länk för att lägga till en ny.

Klicka på knappen **Lägg till lagrings mål** .

![Skärm bild av sidan Lägg till lagrings mål med NFS-mål definierat](media/add-nfs-target.png)

Ange den här informationen för ett NFS-baserat lagrings mål:

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.

* **Måltyp** – Välj **NFS**.

* **Hostname** – ange IP-adressen eller det fullständiga domän namnet för NFS-lagrings systemet. (Använd bara ett domän namn om din cache har åtkomst till en DNS-server som kan matcha namnet.)

* **Användnings modell** – Välj en av profilerna för Datacachen baserat på ditt arbets flöde, som beskrivs i [Välj en användnings modell](#choose-a-usage-model) ovan.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd Azure CLI-kommandot [AZ HPC-cache NFS-Storage-Target Add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add) för att skapa lagrings målet.

> [!NOTE]
> Azure CLI-kommandona kräver för närvarande att du skapar en namn områdes Sök väg när du lägger till ett lagrings mål. Detta skiljer sig från den process som används med Azure Portal-gränssnittet.

Ange dessa värden utöver cache-namn och cache-resurs grupp:

* ``--name`` – Ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* ``--nfs3-target`` – IP-adressen för NFS-lagrings systemet. (Du kan använda ett fullständigt kvalificerat domän namn här om din cache har åtkomst till en DNS-server som kan matcha namnet.)
* ``--nfs3-usage-model`` – En av profilerna för datacachelagring, som beskrivs i [Välj en användnings modell](#choose-a-usage-model)ovan.

  Kontrol lera namnen på användnings modellerna med kommandot [AZ HPC-cache Usage-Model List](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction`` – Kopplings parametern länkar den klient-riktade virtuella fil Sök vägen med en export Sök väg på lagrings systemet.

  Ett NFS-lagrings mål kan ha flera virtuella sökvägar, så länge varje sökväg representerar en annan export-eller under katalog på samma lagrings system. Skapa alla sökvägar för ett lagrings system på ett lagrings mål.

  Du kan när som helst [lägga till och redigera namn områdes Sök vägar](add-namespace-paths.md) på ett lagrings mål.

  ``--junction``Parametern använder följande värden:

  * ``namespace-path`` -Klientens virtuella fil Sök väg
  * ``nfs-export`` – Lagrings system exporten som ska associeras med den klient-riktade sökvägen
  * ``target-path`` (valfritt) – en under katalog till exporten, om det behövs

  Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Läs [Konfigurera sammanställd namnrymd](hpc-cache-namespace.md) för att lära dig mer om funktionen för virtuellt namn område.

Exempel kommando:

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

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Lägg till ett nytt ADLS-NFS-lagrings mål (för hands version)

ADLS-NFS-lagrings mål använder Azure Blob-behållare som har stöd för NFS (Network File System) 3,0-protokollet.

> [!NOTE]
> NFS 3,0 protokoll stöd för Azure Blob Storage finns i offentlig för hands version. Tillgänglighet är begränsad och funktioner kan ändras mellan nu och när funktionen blir allmänt tillgänglig. Använd inte för hands versions teknik i produktions system.
>
> Den senaste informationen finns i [stöd för NFS 3,0-protokoll](../storage/blobs/network-file-system-protocol-support.md) .

ADLS-NFS-lagrings mål har en del likheter med Blob Storage-mål och vissa med NFS-lagrings mål. Exempel:

* Precis som med ett Blob Storage-mål måste du ge Azure HPC-behörighet [åtkomst till ditt lagrings konto](#add-the-access-control-roles-to-your-account).
* Precis som ett NFS-lagrings mål måste du ange en [användnings modell](#choose-a-usage-model)för cache.
* Eftersom NFS-aktiverade BLOB-behållare har en NFS-kompatibel hierarkisk struktur behöver du inte använda cacheminnet för att mata in data och behållarna kan läsas av andra NFS-system. Du kan i förväg läsa in data i en ADLS-NFS-behållare, sedan lägga till dem i en HPC-cache som ett lagrings mål och sedan komma åt data senare utanför en HPC-cache. När du använder en standard-BLOB-behållare som ett HPC cache Storage-mål, skrivs data i ett eget format och kan bara nås från andra Azure HPC cache-kompatibla produkter.

Innan du kan skapa ett lagrings mål för ADLS-NFS måste du skapa ett NFS-aktiverat lagrings konto. Följ tipsen i [krav för Azure HPC cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) och instruktionerna i [montera Blob Storage med hjälp av NFS](../storage/blobs/network-file-system-protocol-support-how-to.md). När lagrings kontot har kon figurer ATS kan du skapa en ny behållare när du skapar lagrings målet.

Om du vill skapa ett ADLS-NFS-lagrings mål öppnar du sidan **Lägg till lagrings mål** i Azure Portal. (Ytterligare metoder är under utveckling.)

![Skärm bild av sidan Lägg till lagrings mål med definierat ADLS-NFS-mål](media/add-adls-target.png)

Ange den här informationen.

* **Lagrings mål namn** – ange ett namn som identifierar det här lagrings målet i Azure HPC-cachen.
* **Måltyp** – Välj **ADLS-NFS**.
* **Lagrings konto** – Välj det konto som du vill använda. Om ditt NFS-aktiverade lagrings konto inte visas i listan kontrollerar du att det följer kraven och att cachen har åtkomst till det.

  Du måste auktorisera cache-instansen för att komma åt lagrings kontot enligt beskrivningen i [Lägg till åtkomst roller](#add-the-access-control-roles-to-your-account).

* **Lagrings behållare** – Välj den NFS-aktiverade BLOB-behållaren för målet eller klicka på **Skapa ny**.

* **Användnings modell** – Välj en av profilerna för Datacachen baserat på ditt arbets flöde, som beskrivs i [Välj en användnings modell](#choose-a-usage-model) ovan.

När du är färdig klickar du på **OK** för att lägga till lagrings målet.

<!-- **** -->

## <a name="view-storage-targets"></a>Visa lagrings mål

Du kan använda Azure Portal eller Azure CLI för att visa de lagrings mål som redan har definierats för din cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna din cache-instans från Azure Portal och klicka på **lagrings mål**, som finns under inställnings rubriken på den vänstra sid panelen. Sidan lagrings mål listar alla befintliga mål och kontroller för att lägga till eller ta bort dem.

Klicka på ett lagrings måls namn för att öppna dess informations sida.

Läs [Redigera lagrings mål](hpc-cache-edit-storage.md) för mer information.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

Använd alternativet [AZ HPC-cache Storage-Target List](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) för att visa befintliga lagrings mål för en cache. Ange cache-namn och resurs grupp (om du inte har angett det globalt).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Använd [AZ HPC-cache Storage-mål show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list) för att visa information om ett visst lagrings mål. (Ange lagrings målet efter namn.)

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

När du har skapat lagrings målen fortsätter du med dessa uppgifter för att förbereda cacheminnet för användning:

* [Konfigurera sammanställd namnrymd](add-namespace-paths.md)
* [Montera Azure HPC Cache](hpc-cache-mount.md)
* [Flytta data till Azure Blob Storage](hpc-cache-ingest.md)

Om du behöver uppdatera inställningarna kan du [Redigera ett lagrings mål](hpc-cache-edit-storage.md).
