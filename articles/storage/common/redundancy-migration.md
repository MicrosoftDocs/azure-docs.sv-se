---
title: Ändra hur ett lagringskonto replikeras
titleSuffix: Azure Storage
description: Lär dig hur du ändrar hur data i ett befintligt lagringskonto replikeras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb8bbf852803df53c43cef90bd2229bfcddd60d4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766195"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Ändra hur ett lagringskonto replikeras

Azure Storage alltid flera kopior av dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och enorma naturkatastrofer. Redundans säkerställer att ditt lagringskonto uppfyller serviceavtalet [(SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel.

Azure Storage erbjuder följande typer av replikering:

- Lokalt redundant lagring (LRS)
- Zonredundant lagring (ZRS)
- Geo-redundant lagring (GRS) eller geo-redundant lagring med läsbehörighet (RA-GRS)
- Geo-zonredundant lagring (GZRS) eller geo-zonredundant lagring med läsbehörighet (RA-GZRS)

En översikt över vart och ett av dessa alternativ finns i [Azure Storage redundans](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Växla mellan olika typer av replikering

Du kan växla ett lagringskonto från en typ av replikering till en annan typ, men vissa scenarier är enklare än andra. Om du vill lägga till eller ta bort geo-replikering eller läsåtkomst till den sekundära regionen kan du använda Azure Portal, PowerShell eller Azure CLI för att uppdatera replikeringsinställningen. Men om du vill ändra hur data replikeras i den primära regionen genom att flytta från LRS till ZRS eller vice versa måste du utföra en manuell migrering.

Följande tabell innehåller en översikt över hur du växlar från varje typ av replikering till en annan:

| Växling | ... till LRS | ... till GRS/RA-GRS | ... till ZRS | ... till GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... från LRS</b> | Ej tillämpligt | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningen<sup>1,2</sup> | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Begära en direktmigrering | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Växla till GRS/RA-GRS först och begär sedan en direktmigrering<sup>1</sup> |
| <b>... från GRS/RA-GRS</b> | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningen | Ej tillämpligt | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Växla till LRS först och begär sedan en direktmigrering | Utföra en manuell migrering <br /><br /> ELLER <br /><br /> Begära en direktmigrering |
| <b>... från ZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Ej tillämpligt | Begära en direktmigrering |
| <b>... från GZRS/RA-GZRS</b> | Utföra en manuell migrering | Utföra en manuell migrering | Använd Azure Portal, PowerShell eller CLI för att ändra replikeringsinställningen | Ej tillämpligt |

<sup>1</sup> Debiteras en engångsavgift för utgående.<br />
<sup>2</sup> Migrering från LRS till GRS stöds inte om lagringskontot innehåller blobar på arkivnivån.<br />
<sup>3</sup> Konvertering från ZRS till GZRS/RA-GZRS eller vice versa stöds inte i följande regioner: USA, östra 2, USA, östra, Europa, västra.

> [!CAUTION]
> Om du [](storage-disaster-recovery-guidance.md) har utfört en kontoredundans för ditt (RA-)GRS- eller (RA-)GZRS-konto är kontot lokalt redundant (LRS) i den nya primära regionen efter redundansen. Direktmigrering till ZRS eller GZRS för ett LRS-konto som är resultatet av en redundans stöds inte. Detta gäller även för så kallade återställningsåtgärder. Om du till exempel utför en konto redundans från RA-GZRS till LRS i den sekundära regionen och sedan konfigurerar det igen till RA-GRS och utför en annan konto redundans till den ursprungliga primära regionen, kan du inte kontakta supporten för den ursprungliga direktmigrering till RA-GZRS i den primära regionen. I stället måste du utföra en manuell migrering till ZRS eller GZRS.

## <a name="change-the-replication-setting"></a>Ändra replikeringsinställningen

Du kan använda Azure Portal, PowerShell eller Azure CLI för att ändra replikeringsinställningen för ett lagringskonto, så länge du inte ändrar hur data replikeras i den primära regionen. Om du migrerar från LRS i den primära regionen till ZRS i den primära regionen eller tvärtom, måste du antingen utföra en manuell migrering eller en direktmigrering.

Att ändra hur ditt lagringskonto replikeras leder inte till drifttid för dina program.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill ändra redundansalternativ för ditt lagringskonto i Azure Portal du följande steg:

1. Navigera till ditt lagringskonto i Azure-portalen.
1. Välj **konfigurationsinställningen.**
1. Uppdatera **inställningen** Replikering.

![Skärmbild som visar hur du ändrar replikeringsalternativ i portalen](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill ändra redundansalternativ för ditt lagringskonto med PowerShell anropar du kommandot [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger `-SkuName` parametern:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ändra redundansalternativ för ditt lagringskonto med Azure CLI anropar du [kommandot az storage account update](/cli/azure/storage/account#az_storage_account_update) och anger `--sku` parametern:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Utföra en manuell migrering till ZRS, GZRS eller RA-GZRS

Om du vill ändra hur data i ditt lagringskonto replikeras i den primära regionen genom att flytta från LRS till ZRS eller vice versa, kan du välja att utföra en manuell migrering. Manuell migrering ger större flexibilitet än direktmigrering. Du styr tidpunkten för en manuell migrering, så använd det här alternativet om du behöver slutföra migreringen vid ett visst datum.

När du utför en manuell migrering från LRS till ZRS i den primära regionen eller vice versa, kan mållagringskontot vara geo-redundant och kan också konfigureras för läsåtkomst till den sekundära regionen. Du kan till exempel migrera ett LRS-konto till ett GZRS- eller RA-GZRS-konto i ett enda steg.

En manuell migrering kan leda till avbrott i programmet. Om ditt program kräver hög tillgänglighet tillhandahåller Microsoft även ett alternativ för direktmigrering. Direktmigrering är migrering på plats utan driftavbrott.

Med en manuell migrering kopierar du data från ditt befintliga lagringskonto till ett nytt lagringskonto som använder ZRS i den primära regionen. Om du vill utföra en manuell migrering kan du använda något av följande alternativ:

- Kopiera data med hjälp av ett befintligt verktyg som AzCopy, ett av Azure Storage klientbibliotek eller ett tillförlitligt verktyg från tredje part.
- Om du är bekant med Hadoop eller HDInsight kan du ansluta både källlagringskontot och mållagringskontot till klustret. Parallellisera sedan datakopieringsprocessen med ett verktyg som DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Begära en direktmigrering till ZRS, GZRS eller RA-GZRS

Om du behöver migrera ditt lagringskonto från LRS till ZRS i den primära regionen utan avbrott i programmet kan du begära en direktmigrering från Microsoft. Om du vill migrera från LRS till GZRS eller RA-GZRS måste du först byta till GRS eller RA-GRS och sedan begära en direktmigrering. På samma sätt kan du begära en direktmigrering från GRS eller RA-GRS till GZRS eller RA-GZRS. Om du vill migrera från GRS eller RA-GRS till ZRS växlar du först till LRS och begär sedan en direktmigrering.

Under en direktmigrering kan du komma åt data i ditt lagringskonto utan förlust av hållbarhet eller tillgänglighet. Det Azure Storage serviceavtalet underhålls under migreringsprocessen. Ingen dataförlust är associerad med en direktmigrering. Tjänstslutpunkter, åtkomstnycklar, signaturer för delad åtkomst och andra kontoalternativ förblir oförändrade efter migreringen.

ZRS stöder endast konton för generell användning v2, så se till att uppgradera ditt lagringskonto innan du skickar en begäran om direktmigrering till ZRS. Mer information finns i [Uppgradera till ett v2-lagringskonto för generell användning.](storage-account-upgrade.md) Ett lagringskonto måste innehålla data som ska migreras via direktmigrering.

Direktmigrering stöds endast för lagringskonton som använder LRS- eller GRS-replikering. Om ditt konto använder RA-GRS måste du först ändra kontots replikeringstyp till antingen LRS eller GRS innan du fortsätter. Det här mellanliggande steget tar bort den sekundära skrivskyddade slutpunkten som tillhandahålls av RA-GRS före migreringen.

Microsoft hanterar din begäran om direktmigrering utan dröjsmål, men det finns inte någon garanti om när den slutförs. Om dina data måste migreras till ZRS före ett visst datum rekommenderar Microsoft att du utför en manuell migrering i stället. Ju mer data du har på ditt konto, desto längre tid tar det vanligtvis att migrera dina data.

Du måste utföra en manuell migrering om:

- Du vill migrera dina data till ett ZRS-lagringskonto som finns i en annan region än källkontot.
- Ditt lagringskonto är en premium-sidblob eller ett blockblobkonto.
- Du vill migrera data från ZRS till LRS, GRS eller RA-GRS.
- Ditt lagringskonto innehåller data på arkivnivån.

Du kan begära direktmigrering via [Azure Support portalen.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 

> [!IMPORTANT]
> Om du behöver migrera fler än ett lagringskonto skapar du en enda supportbiljett och anger namnen på de konton som ska konverteras **på fliken** Information.

Följ dessa steg om du vill begära en direktmigrering:

1. I Azure Portal du till ett lagringskonto som du vill migrera.
1. Under **Support + felsökning** väljer du Ny **supportbegäran.**
1. Fyll i **fliken Grundläggande** inställningar baserat på din kontoinformation:
    - **Typ av problem:** Välj **Teknisk**.
    - **Tjänst:** Välj **Mina tjänster** och sedan Hantering **av lagringskonto.**
    - **Resurs:** Välj ett lagringskonto som ska migreras. Om du behöver ange flera lagringskonton kan du göra det i **avsnittet** Information.
    - **Problemtyp:** Välj **Datamigrering.**
    - **Undergrupp av** problem: **Välj Migrera till ZRS, GZRS eller RA-GZRS.**

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="Skärmbild som visar hur du begär en direktmigrering – fliken Grunder":::

1. Välj **Nästa**. På fliken **Lösningar** kan du kontrollera om dina lagringskonton är berättigande till migrering.
1. Välj **Nästa**. Om du har fler än ett lagringskonto att migrera anger du namnet på varje konto på fliken Information, avgränsat med semikolon. 

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="Skärmbild som visar hur du begär en direktmigrering – fliken Information":::

1. Fyll i den ytterligare information som krävs på **fliken Information** och välj sedan Granska + skapa **för att** granska och skicka din supportbiljett. En supportperson kontaktar dig för att ge den hjälp du kan behöva.

> [!NOTE]
> Premium-filresurser (FileStorage-konton) är endast tillgängliga för LRS och ZRS.
>
> GZRS-lagringskonton stöder för närvarande inte arkivnivån. Mer [information finns i Åtkomstnivåer för Azure Blob Storage: hot, cool och archive.](../blobs/storage-blob-storage-tiers.md)
>
> Hanterade diskar är bara tillgängliga för LRS och kan inte migreras till ZRS. Du kan lagra ögonblicksbilder och avbildningar för SSD-hanterade standarddiskar på standard HDD-lagring och [välja mellan LRS- och ZRS-alternativ.](https://azure.microsoft.com/pricing/details/managed-disks/) Information om integrering med tillgänglighetsuppsättningar finns i [Introduktion till Azure Managed Disks](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Växla från ZRS Classic

> [!IMPORTANT]
> Microsoft kommer att inaktuella och migrera klassiska ZRS-konton den 31 mars 2021. Mer information kommer att ges till ZRS Classic-kunder före utfasningen.
>
> När ZRS blir allmänt tillgängligt i en viss region kommer kunderna inte längre att kunna skapa ZRS Classic-konton från Azure Portal i den regionen. Att använda Microsoft PowerShell och Azure CLI för att skapa klassiska ZRS-konton är ett alternativ tills ZRS Classic är inaktuell. Information om var ZRS är tillgängligt finns i [Azure Storage redundans](storage-redundancy.md).

ZRS Classic replikerar asynkront data mellan datacenter inom en till två regioner. Replikerade data kanske inte är tillgängliga om inte Microsoft initierar redundans till den sekundära. Ett ZRS Classic-konto kan inte konverteras till eller från LRS, GRS eller RA-GRS. ZRS Classic-konton stöder inte heller mått eller loggning.

ZRS Classic är endast tillgängligt för **blockblobar i** allmänna V1-lagringskonton (GPv1). Mer information om lagringskonton finns i [Översikt över Azure Storage-konton.](storage-account-overview.md)

Om du vill migrera ZRS-kontodata manuellt till eller från ett LRS-, GRS-, RA-GRS- eller ZRS Classic-konto använder du något av följande verktyg: AzCopy, Azure Storage Explorer, PowerShell eller Azure CLI. Du kan också skapa en egen migreringslösning med ett av Azure Storage klientbiblioteken.

Du kan också uppgradera ditt klassiska ZRS-lagringskonto till ZRS med hjälp av Azure Portal, PowerShell eller Azure CLI i regioner där ZRS är tillgängligt.

# <a name="portal"></a>[Portal](#tab/portal)

Om du vill uppgradera till ZRS i Azure Portal navigerar du **till kontots** konfigurationsinställningar och väljer **Uppgradera:**

![Uppgradera ZRS Classic till ZRS i portalen](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Om du vill uppgradera till ZRS med PowerShell anropar du följande kommando:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera till ZRS med Azure CLI anropar du följande kommando:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kostnader som är kopplade till att ändra hur data replikeras

Kostnaderna för att ändra hur data replikeras beror på din konverteringsväg. Att beställa från minst till de dyraste redundanserbjudandena för Azure Storage är LRS, ZRS, GRS, RA-GRS, GZRS och RA-GZRS.

Om du till exempel *går från* LRS till någon annan typ av replikering tillkommer ytterligare avgifter eftersom du flyttar till en mer avancerad redundansnivå. Om du *migrerar* till GRS eller RA-GRS debiteras en utgående bandbredd eftersom dina data (i din primära region) replikeras till den sekundära fjärrregionen. Den här avgiften är en enda kostnad vid den första installationen. När data har kopierats tillkommer inga ytterligare migreringsavgifter. Mer information om bandbreddsavgifter finns på [Azure Storage prissättningssidan.](https://azure.microsoft.com/pricing/details/storage/blobs/)

Om du migrerar ditt lagringskonto från GRS till LRS tillkommer ingen extra kostnad, men dina replikerade data tas bort från den sekundära platsen.

> [!IMPORTANT]
> Om du migrerar ditt lagringskonto från RA-GRS till GRS eller LRS debiteras kontot som RA-GRS i ytterligare 30 dagar efter det datum då det konverterades.

## <a name="see-also"></a>Se även

- [Redundans i Azure Storage](storage-redundancy.md)
- [Kontrollera egenskapen Last Sync Time för ett lagringskonto](last-sync-time-get.md)
- [Använda geo-redundans för att utforma program med hög tillgänglighet](geo-redundant-design.md)
