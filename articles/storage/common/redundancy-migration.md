---
title: Ändra hur ett lagringskonto replikeras
titleSuffix: Azure Storage
description: Lär dig hur du ändrar hur data i ett befintligt lagrings konto replikeras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/19/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 412e5ac661761d5fda1d375c59511c053a6354a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714790"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Ändra hur ett lagringskonto replikeras

Azure Storage lagrar alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Redundans garanterar att ditt lagrings konto uppfyller [service nivå avtalet (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) även om det är i händelse av problem.

Azure Storage erbjuder följande typer av replikering:

- Lokalt redundant lagring (LRS)
- Zonredundant lagring (ZRS)
- Geo-redundant lagring (GRS) eller Geo-redundant lagring med Läs behörighet (RA-GRS)
- Geo-Zone-redundant lagring (GZRS) eller (Read-Access geo-Zone-redundant lagring) (RA-GZRS)

En översikt över vart och ett av dessa alternativ finns i [Azure Storage redundans](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Växla mellan olika typer av replikering

Du kan byta ett lagrings konto från en typ av replikering till en annan typ, men vissa scenarier är mer enkla än andra. Om du vill lägga till eller ta bort geo-replikering eller Läs åtkomst till den sekundära regionen kan du använda Azure Portal, PowerShell eller Azure CLI för att uppdatera inställningen för replikering. Men om du vill ändra hur data replikeras i den primära regionen, genom att flytta från LRS till ZRS eller vice versa, måste du utföra en manuell migrering.

Följande tabell innehåller en översikt över hur du växlar från varje typ av replikering till en annan:

| Växla | ... till LRS | ... till GRS/RA-GRS | ... till ZRS | ... till GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... från LRS</b> | Ej tillämpligt | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningarna<sup>1, 2</sup> | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Begär en Direktmigrering | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Växla till GRS/RA-GRS först och begär sedan en Direktmigrering<sup>1</sup> |
| <b>... från GRS/RA-GRS</b> | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningarna | Ej tillämpligt | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Växla till LRS först och begär sedan en Direktmigrering | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Begär en Direktmigrering |
| <b>... från ZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Ej tillämpligt | Begär en Direktmigrering |
| <b>... från GZRS/RA-GZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningarna | Ej tillämpligt |

<sup>1</sup> ådrar sig en engångs avgift.<br />
<sup>2</sup> det går inte att MIGRERA från LRS till GRS om lagrings kontot innehåller blobar på Arkiv nivån.<br />
<sup>3</sup> konvertering från ZRS till GZRS/ra-GZRS eller vice versa stöds inte i följande regioner: USA, östra 2, östra USA, västra Europa.

> [!CAUTION]
> Om du utförde ett [konto för redundans](storage-disaster-recovery-guidance.md) för ditt (RA-) GRS-eller (RA-) GZRS-konto är kontot lokalt REDUNDANT (LRS) i den nya primära regionen efter redundansväxlingen. Direktmigrering till ZRS eller GZRS för ett LRS-konto som orsakas av en redundansväxling stöds inte. Detta gäller även i händelse av så kallade failback-åtgärder. Om du till exempel utför ett konto redundansväxling från RA-GZRS till LRS i den sekundära regionen och sedan konfigurerar det igen till RA-GRS och utför ett annat konto redundansväxling till den ursprungliga primära regionen, kan du inte kontakta supporten för den ursprungliga Direktmigrering till RA-GZRS i den primära regionen. I stället måste du utföra en manuell migrering till ZRS eller GZRS.

## <a name="change-the-replication-setting"></a>Ändra replikeringsstatus

Du kan använda Azure Portal, PowerShell eller Azure CLI för att ändra replikeringsinställningarna för ett lagrings konto, förutsatt att du inte ändrar hur data replikeras i den primära regionen. Om du migrerar från LRS i den primära regionen till ZRS i den primära regionen eller vice versa måste du antingen utföra en manuell migrering eller en direktmigrering.

Att ändra hur ditt lagrings konto replikeras resulterar inte i drift tid för dina program.

# <a name="portal"></a>[Portal](#tab/portal)

Följ dessa steg om du vill ändra alternativet för redundans för ditt lagrings konto i Azure Portal:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **konfigurations** inställningen.
1. Uppdatera inställningen för **replikering** .

![Skärm bild som visar hur du ändrar replikeringsalternativ i portalen](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra alternativet för redundans för ditt lagrings konto med PowerShell anropar du kommandot [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger `-SkuName` parametern:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ändra alternativet för redundans för ditt lagrings konto med Azure CLI anropar du kommandot [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) och anger `--sku` parametern:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Utföra en manuell migrering till ZRS, GZRS eller RA-GZRS

Om du vill ändra hur data i ditt lagrings konto replikeras i den primära regionen, genom att flytta från LRS till ZRS eller vice versa, kan du välja att utföra en manuell migrering. Manuell migrering ger större flexibilitet än direktmigrering. Du styr tids inställningen för en manuell migrering, så Använd det här alternativet om du vill att migreringen ska slutföras vid ett visst datum.

När du utför en manuell migrering från LRS till ZRS i den primära regionen eller vice versa, kan mål lagrings kontot vara Geo-redundant och kan också konfigureras för Läs behörighet till den sekundära regionen. Du kan till exempel migrera ett LRS-konto till ett GZRS-eller RA-GZRS-konto i ett enda steg.

En manuell migrering kan resultera i avbrott i programmet. Om ditt program kräver hög tillgänglighet tillhandahåller Microsoft även ett alternativ för direktmigrering. Direktmigrering är migrering på plats utan driftavbrott.

Med en manuell migrering kopierar du data från ditt befintliga lagrings konto till ett nytt lagrings konto som använder ZRS i den primära regionen. Om du vill utföra en manuell migrering kan du använda något av följande alternativ:

- Kopiera data med hjälp av ett befintligt verktyg, till exempel AzCopy, ett av Azure Storage klient bibliotek eller ett tillförlitligt verktyg från tredje part.
- Om du är bekant med Hadoop eller HDInsight kan du koppla både käll lagrings kontot och mål lagrings konto kontot till klustret. Parallellisera sedan data kopierings processen med ett verktyg som DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Begär en Direktmigrering till ZRS, GZRS eller RA-GZRS

Om du behöver migrera ditt lagrings konto från LRS till ZRS i den primära regionen utan avbrott i programmet kan du begära en Direktmigrering från Microsoft. Om du vill migrera från LRS till GZRS eller RA-GZRS måste du först växla till GRS eller RA-GRS och sedan begära en direktmigrering. På samma sätt kan du begära en Direktmigrering från GRS eller RA-GRS till GZRS eller RA-GZRS. Om du vill migrera från GRS eller RA-GRS till ZRS, växlar du först till LRS och begär sedan en direktmigrering.

Under en direktmigrering kan du komma åt data i ditt lagrings konto utan att du förlorar hållbarhet eller tillgänglighet. Service avtalet för Azure Storage underhålls under migreringsprocessen. Ingen data förlust är kopplad till en direktmigrering. Tjänst slut punkter, åtkomst nycklar, signaturer för delad åtkomst och andra konto alternativ förblir oförändrade efter migreringen.

ZRS stöder enbart generella v2-konton, så se till att uppgradera ditt lagrings konto innan du skickar en begäran om Direktmigrering till ZRS. Mer information finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](storage-account-upgrade.md). Ett lagrings konto måste innehålla data som ska migreras via direktmigrering.

Direktmigrering stöds endast för lagrings konton som använder LRS eller GRS-replikering. Om ditt konto använder RA-GRS måste du först ändra ditt kontos replikeringstyp till antingen LRS eller GRS innan du fortsätter. Detta mellanliggande steg tar bort den sekundära skrivskyddade slut punkten som tillhandahålls av RA-GRS innan migreringen.

Microsoft hanterar din begäran om direktmigrering utan dröjsmål, men det finns inte någon garanti om när den slutförs. Om dina data måste migreras till ZRS före ett visst datum rekommenderar Microsoft att du utför en manuell migrering i stället. Ju mer data du har på ditt konto, desto längre tid tar det vanligtvis att migrera dina data.

Du måste utföra en manuell migrering om:

- Du vill migrera dina data till ett ZRS lagrings konto som finns i en annan region än käll kontot.
- Ditt lagrings konto är en Premium Page BLOB-eller Block-Blob-konto.
- Du vill migrera data från ZRS till LRS, GRS eller RA-GRS.
- Ditt lagrings konto innehåller data på Arkiv nivån.

Du kan begära en Direktmigrering via [Azure-support portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Från portalen väljer du det lagrings konto som du vill konvertera till ZRS.

1. Välj **ny supportbegäran**.
2. Slutför **grunderna** baserat på din konto information: 
    - **Typ av problem**: Välj **teknisk**.
    - **Tjänst**: Välj **Mina tjänster** och **lagrings konto hantering**.
    - **Resurs**: Välj den resurs som du vill konvertera till ZRS.
3. Välj **Nästa**.
4. Ange följande värden i avsnittet **problem** :
    - **Allvarlighets grad**: låt standardvärdet vara.
    - **Problem typ**: Välj **datamigrering**.
    - **Kategori**: Välj **migrera till ZRS**.
    - **Title**: Ange en beskrivande rubrik, till exempel **ZRS-kontots migrering**.
    - **Information**: Skriv ytterligare information i **informations** rutan, till exempel om jag vill migrera till ZRS från [LRS, GRS] i \_ \_ regionen.
5. Välj **Nästa**.
6. Kontrol lera att kontakt informationen är korrekt på bladet med **kontakt information** .
7. Välj **Skapa**.

En support person kommer att kontakta dig och tillhandahålla den hjälp du behöver.

> [!NOTE]
> Premium fil resurser (FileStorage-konton) är bara tillgängliga för LRS och ZRS.
>
> GZRS lagrings konton stöder för närvarande inte Arkiv nivån. Mer information finns i [Azure Blob Storage: frekvent åtkomst, låg frekvent åtkomst och Arkiv](../blobs/storage-blob-storage-tiers.md) lag rings nivåer.
>
> Managed disks är bara tillgängliga för LRS och kan inte migreras till ZRS. Du kan lagra ögonblicks bilder och avbildningar för standard-SSD-hanterade diskar på standard-HDD-lagring och [välja mellan alternativen LRS och ZRS](https://azure.microsoft.com/pricing/details/managed-disks/). Information om integrering med tillgänglighets uppsättningar finns i [Introduktion till Azure Managed disks](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Växla från ZRS Classic

> [!IMPORTANT]
> Microsoft kommer att föråldra och migrera ZRS klassiska konton den 31 mars 2021. Mer information kommer att ges till ZRS klassiska kunder före utfasning.
>
> När ZRS blir allmänt tillgängligt i en specifik region kommer kunder inte längre att kunna skapa ZRS klassiska konton från Azure Portal i den regionen. Att använda Microsoft PowerShell och Azure CLI för att skapa ZRS klassiska konton är ett alternativ tills ZRS Classic är föråldrad. Information om var ZRS finns tillgänglig finns i [Azure Storage redundans](storage-redundancy.md).

ZRS klassisk replikerar data mellan data Center i en och två regioner. Replikerade data kanske inte är tillgängliga om inte Microsoft initierar redundans till den sekundära. Ett klassiskt ZRS-konto kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS klassiska konton stöder inte heller mått eller loggning.

ZRS Classic är endast tillgängligt för **block-blobar** i GPv1-lagrings konton (General Purpose v1). Mer information om lagrings konton finns i [Översikt över Azure Storage-konto](storage-account-overview.md).

Om du vill migrera ZRS-Datadata till eller från ett LRS, GRS, RA-GRS eller ZRS klassiska konto använder du något av följande verktyg: AzCopy, Azure Storage Explorer, PowerShell eller Azure CLI. Du kan också bygga en egen lösning för migrering med ett av klient biblioteken för Azure Storage.

Du kan också uppgradera ditt ZRS klassiska lagrings konto till ZRS med hjälp av Azure Portal, PowerShell eller Azure CLI i regioner där ZRS är tillgängligt.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill uppgradera till ZRS i Azure Portal navigerar du till **konfigurations** inställningarna för kontot och väljer **uppgradering**:

![Uppgradera ZRS Classic till ZRS i portalen](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill uppgradera till ZRS med PowerShell anropar du följande kommando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera till ZRS med hjälp av Azure CLI anropar du följande kommando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kostnader för att ändra hur data replikeras

Kostnaderna för att ändra hur data replikeras beror på din konverterings Sök väg. Beställningar från minst till dyra, Azure Storage redundans erbjudanden omfattar LRS, ZRS, GRS, RA-GRS, GZRS och RA-GZRS.

Om du till exempel går *från* LRS till en annan typ av replikering debiteras ytterligare avgifter eftersom du flyttar till en mer avancerad redundans nivå. Migrering *till* GRS eller RA-GRS innebär en utgående bandbredds avgift eftersom dina data (i din primära region) replikeras till din sekundära fjärrregion. Den här avgiften är en engångs kostnad vid första konfigurationen. När data har kopierats sker inga ytterligare migreringar. Mer information om kostnader för bandbredd finns på [sidan Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du migrerar ditt lagrings konto från GRS till LRS finns det ingen ytterligare kostnad, men dina replikerade data tas bort från den sekundära platsen.

> [!IMPORTANT]
> Om du migrerar ditt lagrings konto från RA-GRS till GRS eller LRS debiteras det kontot som RA-GRS under ytterligare 30 dagar efter det datum då det konverterades.

## <a name="see-also"></a>Se även

- [Redundans i Azure Storage](storage-redundancy.md)
- [Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md)
- [Använd GEO-redundans för att skapa program med hög tillgänglighet](geo-redundant-design.md)
