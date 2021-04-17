---
title: Uppgradera till ett v2-lagringskonto för generell användning
titleSuffix: Azure Storage
description: Uppgradera till V2-lagringskonton för generell användning med Azure Portal, PowerShell eller Azure CLI. Ange en åtkomstnivå för blobdata.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d6d94efea52d920b8bfe69be2ee07c8b829792d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484106"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uppgradera till ett v2-lagringskonto för generell användning

V2-lagringskonton för generell användning stöder de senaste Azure Storage och inkluderar alla funktioner i general-purpose v1- och Blob Storage-konton. Konton för generell användning v2 rekommenderas för de flesta lagringsscenarier. Konton för generell användning v2 ger de lägsta priserna per gigabyte för Azure Storage, samt konkurrenskraftiga transaktionspriser. Konton för generell användning v2 stöder standardåtkomstnivåer för konton med hög eller låg nivå samt blobnivåindelad mellan hot, cool eller archive.

Det är enkelt att uppgradera till ett v2-lagringskonto för generell användning från dina konton för generell användning v1 eller Blob Storage. Du kan uppgradera med Azure Portal, PowerShell eller Azure CLI. Det finns ingen avbrottstid eller risk för dataförlust vid uppgradering till ett v2-lagringskonto för generell användning. Kontouppgraderingen sker via en Azure Resource Manager som ändrar kontotypen.

> [!IMPORTANT]
> Uppgradering av ett general-purpose v1- eller Blob Storage-konto till general-purpose v2 är permanent och kan inte ångras.

> [!NOTE]
> Även om Microsoft rekommenderar konton för generell användning v2 för de flesta scenarier, kommer Microsoft att fortsätta att stödja general-purpose v1-konton för nya och befintliga kunder. Du kan skapa V1-lagringskonton för generell användning i nya regioner när Azure Storage är tillgänglig i dessa regioner. Microsoft har för närvarande inte någon plan för att ta bort stödet för konton för generell användning v1 och kommer att tillhandahålla minst ett års förvarning innan någon av Azure Storage kommer att bli inaktuell. Microsoft kommer att fortsätta att tillhandahålla säkerhetsuppdateringar för konton för generell användning v1, men ingen ny funktionsutveckling förväntas för den här kontotypen.
>
> För nya Azure-regioner som har kommit online efter den 1 oktober 2020 har prissättningen för konton för generell användning v1 ändrats och motsvarar prissättningen för konton för generell användning v2 i dessa regioner. Prissättningen för konton för generell användning v1 i Azure-regioner som fanns före den 1 oktober 2020 har inte ändrats. Prisinformation för general-purpose v1-konton i en viss region finns på Azure Storage prissättningssidan. Välj din region och välj sedan Övrigt bredvid **Prissättningserbjudanden.** 

## <a name="upgrade-an-account"></a>Uppgradera ett konto

Om du vill uppgradera ett v1- eller Blob Storage-konto för generell användning till ett v2-konto för generell användning använder du Azure Portal, PowerShell eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. I avsnittet **Inställningar** klickar du på **Konfiguration.**
4. Klicka på **Uppgradera** under **Typ av konto**.
5. Ange namnet på ditt konto under **Bekräfta uppgradering**.
6. Klicka **på** Uppgradera längst ned på bladet.

    ![Uppgradera typ av konto](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill uppgradera ett V1-konto för generell användning till ett v2-konto för generell användning med hjälp av PowerShell måste du först uppdatera PowerShell för att använda den senaste versionen av **Az.Storage-modulen.** Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

Anropa sedan följande kommando för att uppgradera kontot och ersätt resursgruppens namn, lagringskontots namn och önskad åtkomstnivå för kontot.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera ett v1-konto för generell användning till ett v2-konto för generell användning med hjälp av Azure CLI måste du först installera den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

Anropa sedan följande kommando för att uppgradera kontot och ersätt resursgruppens namn, lagringskontots namn och önskad åtkomstnivå för kontot.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Ange en åtkomstnivå för blobdata

Konton för generell användning v2 stöder alla Azure-lagringstjänster och dataobjekt, men åtkomstnivåer är bara tillgängliga för blockblobar i Blob Storage. När du uppgraderar till ett v2-lagringskonto för generell användning kan du ange en standardåtkomstnivå för kontot som är het eller kall, vilket anger den standardnivå som dina blobdata laddas upp som om den enskilda blobåtkomstnivåparametern inte har angetts.

Med åtkomstnivåer för blobar kan du välja den mest kostnadseffektiva lagringen baserat på dina förväntade användningsmönster. Blockblobar kan lagras på nivåerna hot, cool eller archive. Mer information om åtkomstnivåer finns i [Lagringsnivåer för Azure Blob Storage: Hot, Cool och Arkiv.](../blobs/storage-blob-storage-tiers.md)

Som standard skapas ett nytt lagringskonto på den heta åtkomstnivån och ett v1-lagringskonto för generell användning kan uppgraderas till antingen den heta eller den coola kontonivån. Om åtkomstnivån för ett konto inte anges vid uppgraderingen uppgraderas den till hot som standard. Om du utforskar vilken åtkomstnivå som ska användas för uppgraderingen bör du överväga ditt aktuella dataanvändningsscenario. Det finns två vanliga användarscenarier för att migrera till ett v2-konto för generell användning:

* Du har ett befintligt v1-lagringskonto för generell användning och vill utvärdera en uppgradering till ett v2-lagringskonto för generell användning med rätt lagringsnivå för blobdata.
* Du har bestämt dig för att använda ett v2-lagringskonto för generell användning eller redan har ett och vill utvärdera om du bör använda åtkomstnivån för blobbdata med hög eller kall lagring.

I båda fallen är den första prioriteten att beräkna kostnaden för att lagra, komma åt och använda dina data som lagras i ett V2-lagringskonto för generell användning och jämföra det med dina aktuella kostnader.

## <a name="pricing-and-billing"></a>Priser och fakturering

Det är kostnadsfritt att uppgradera ett v1-lagringskonto till ett v2-konto för generell användning. Du kan ange önskad kontonivå under uppgraderingsprocessen. Om en kontonivå inte anges vid uppgradering blir standardkontonivån för det uppgraderade kontot `Hot` . Om du ändrar lagringsåtkomstnivå efter uppgraderingen kan det dock leda till ändringar i fakturan, så vi rekommenderar att du anger den nya kontonivån under uppgraderingen.

För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagringskostnader:** Utöver mängden data som lagras varierar kostnaden för att lagra data beroende på lagringsåtkomstnivån. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.

* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data på den coola lagrings- och arkivlagringsnivån debiteras du en dataåtkomstavgift per gigabyte för läsningar.

* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.

* **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.

* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.

* **Om du ändrar lagringsåtkomstnivå: Om du** ändrar åtkomstnivån för lagringskontot från kall till het debiteras en avgift som motsvarar läsningen av alla data som finns i lagringskontot. Men om du ändrar åtkomstnivån för kontot från hot till cool debiteras en avgift som motsvarar skrivning av alla data till den coola nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för lagringskonton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Beräkna kostnader för dina aktuella användningsmönster

Om du vill beräkna kostnaden för att lagra och komma åt blobdata i ett v2-lagringskonto för generell användning på en viss nivå kan du utvärdera ditt befintliga användningsmönster eller göra en uppskattning av det förväntade användningsmönstret. Vanligtvis vill du veta:

* Din bloblagringsförbrukning i gigabyte, inklusive:
  * Hur mycket data lagras i lagringskontot?
  * Hur ändras datavolymen på månadsbasis; ersätter nya data ständigt gamla data?

* Det primära åtkomstmönstret för dina Blob Storage-data, inklusive:
  * Hur mycket data läses från och skrivs till lagringskontot?
  * Hur många läsåtgärder jämfört med skrivåtgärder sker på data i lagringskontot?

När du ska välja den bästa åtkomstnivån för dina behov kan det vara bra att fastställa din blobdatakapacitet och hur dessa data används. Det kan du göra bäst genom att titta på övervakningsmåtten för ditt konto.

### <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](../blobs/monitor-blob-storage.md) och [Schema över måttabeller i Storage Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot.

Om du vill övervaka lagringsanvändningen för Blob Storage måste du aktivera kapacitetsmåtten.
När detta är aktiverat registreras kapacitetsdata dagligen för ett lagringskontos Blob Service och registreras som en tabellpost som skrivs till *$MetricsCapacityBlob-tabellen* inom samma lagringskonto.

Om du vill övervaka dataåtkomstmönstren för Blob Storage måste du aktivera transaktionsmått för varje timme från API:et. Med transaktionsmått för varje timme aktiverat aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) där du har lagrat sidblobar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Kapacitetsdata gör ingen åtskillnad mellan blockblobar och andra typer och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar måttdata finns i [Mått för lagringsanalys.](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

#### <a name="capacity-costs"></a>Kapacitetskostnader

Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'data'* visar lagringskapacitet som förbrukats av användardata. Den senaste posten i kapacitetsmåttstabellen *$MetricsCapacityBlob* med radnyckeln *'analytics'* visar lagringskapaciteten som förbrukats av analysloggarna.

Den här totala kapaciteten som förbrukats av både användardata och analysloggar (om detta aktiverats) kan sedan användas för att beräkna kostnaden för datalagring i lagringskontot. Samma metod kan även användas för att uppskatta lagringskostnader för GPv1-konton.

#### <a name="transaction-costs"></a>Transaktionskostnader

Summan av *'TotalBillableRequests'*, för alla poster för ett API i tabellen över transaktionsmått, visar det totala antalet transaktioner för API:et i fråga. *Till exempel* kan det totala antalet *'GetBlob'*-transaktioner inom en viss period beräknas baserat på summan av totalt antal debiterbara begäranden för alla poster med radnyckeln *'user;GetBlob'*.

För att kunna beräkna transaktionskostnader för Blob Storage-konton måste du dela in transaktionerna i tre grupper eftersom de har olika pris.

* Skrivtransaktioner som *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* och *'CopyBlob'*.
* Borttagningstransaktioner som *'DeleteBlob'* och *'DeleteContainer'*.
* Alla andra transaktioner.

För att kunna beräkna transaktionskostnaderna för GPv1-konton måste du aggregera alla transaktioner oavsett åtgärd/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Dataöverföringskostnader för dataåtkomst och geo-replikering

Lagringsanalyserna visar inte mängden data som har lästs eller skrivits till ett lagringskonto, men du kan göra en ungefärlig uppskattning genom att titta i tabellen över transaktionsmått. Summan av *'TotalIngress'* för alla poster för ett API i tabellen över transaktionsmått anger den totala mängden inkommande data i antal byte för API:et i fråga. På samma sätt anger summan av *'TotalEgress'* den totala mängden utgående data i antal byte.

För att kunna beräkna kostnaderna för dataåtkomst för Blob Storage-konton måste du dela in transaktionerna i två grupper.

* Du kan beräkna mängden data som hämtas från lagringskontot genom att titta på summan av *'TotalEgress'* och särskilt för åtgärderna *'GetBlob'* och *'CopyBlob'*.

* Du kan beräkna mängden data som skrivs till lagringskontot genom att titta på summan av *'TotalIngress'* och särskilt för åtgärderna *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* och *'AppendBlock'*.

När du använder ett GRS- eller RA-GRS-lagringskonto kan kostnaden för dataöverföring med geo-replikering för Blob Storage-konton också beräknas baserat på uppskattningen av mängden data som skrivits.

> [!NOTE]
> Ett mer detaljerat exempel på hur du beräknar kostnaderna för att använda åtkomstnivån för den heta eller coola lagringen finns i Vanliga frågor och svar med namnet "Vad är åtkomstnivåer för varma och kalla och hur ska jag avgöra vilken som ska *användas?"* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Nästa steg

* [Översikt över lagringskonto](storage-account-overview.md)
* [Skapa ett lagringskonto](storage-account-create.md)
* [Flytta ett Azure Storage till en annan region](storage-account-move.md)
* [Återställa ett borttaget lagringskonto](storage-account-recover.md)
