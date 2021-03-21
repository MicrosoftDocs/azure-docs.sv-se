---
title: Uppgradera till ett V2-lagringskonto för generell användning
titleSuffix: Azure Storage
description: Uppgradera till General-Purpose v2-lagrings konton med hjälp av Azure Portal, PowerShell eller Azure CLI. Ange en åtkomst nivå för BLOB-data.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 85520032e9bc63b6296e40dd1c1055e36762dcc8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555014"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uppgradera till ett V2-lagringskonto för generell användning

Allmänna-Purpose v2-lagrings konton har stöd för de senaste Azure Storage funktionerna och inkluderar alla funktioner i generella v1-och Blob Storage-konton. Allmänna-Purpose v2-konton rekommenderas för de flesta lagrings scenarier. Allmänna-Purpose v2-konton ger de lägsta priserna per Gigabyte för Azure Storage, samt priser för priser som är konkurrerande för branschen. Allmänna-Purpose v2-konton har stöd för standard konto åtkomst nivåer för frekvent eller låg frekvent lagrings nivå och blobnivå mellan frekvent, låg frekvent eller Arkiv lag ring.

Att uppgradera till ett allmänt-syfte v2-lagrings konto från dina generella v1-eller Blob Storage-konton är enkelt. Du kan uppgradera med hjälp av Azure Portal, PowerShell eller Azure CLI. Det finns ingen stillestånds tid eller risk för data förlust som är kopplad till uppgradering till ett allmänt lagrings konto. Konto uppgraderingen sker via en enkel Azure Resource Manager-åtgärd som ändrar konto typen.

> [!IMPORTANT]
> Att uppgradera ett allmänt v1-eller Blob Storage-konto till General-Purpose v2 är permanent och kan inte ångras.

> [!NOTE]
> Även om Microsoft rekommenderar General-Purpose v2-konton för de flesta scenarier kommer Microsoft att fortsätta att stödja allmänna v1-konton för nya och befintliga kunder. Du kan skapa generella v1-lagrings konton i nya regioner när Azure Storage är tillgängliga i dessa regioner. Microsoft har för närvarande inte en plan för att föråldra support för allmänna v1-konton och ger minst ett års varsel innan det går att använda en Azure Storage-funktion. Microsoft fortsätter att tillhandahålla säkerhets uppdateringar för generella v1-konton, men ingen ny funktions utveckling förväntas för den här konto typen.
>
> Från och med den 1 oktober 2020 är prissättningen för allmänna v1-konton i nya Azure Storage regioner likvärdig med prissättningen för General-Purpose v2-konton i dessa regioner. Priser i befintliga Azure Storage-regioner har inte ändrats. Pris information för allmänna v1-konton i en speciell region finns på sidan med Azure Storage priser. **Välj din** region och välj sedan bredvid **prissättnings erbjudanden**.

## <a name="upgrade-an-account"></a>Uppgradera ett konto

Om du vill uppgradera ett allmänt v1-eller Blob Storage-konto till ett allmänt-syfte v2-konto, använder du Azure Portal, PowerShell eller Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. I avsnittet **Inställningar** klickar du på **konfiguration**.
4. Klicka på **Uppgradera** under **Typ av konto**.
5. Ange namnet på ditt konto under **Bekräfta uppgradering**.
6. Klicka på **Uppgradera** längst ned på bladet.

    ![Uppgradera konto typ](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill uppgradera ett allmänt v1-konto till ett allmänt-syfte v2-konto med hjälp av PowerShell, måste du först uppdatera PowerShell för att använda den senaste versionen av modulen **AZ. Storage** . Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

Sedan anropar du följande kommando för att uppgradera kontot, vilket ersätter resurs gruppens namn, lagrings kontots namn och önskad konto åtkomst nivå.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill uppgradera ett allmänt v1-konto till ett allmänt-syfte v2-konto med hjälp av Azure CLI installerar du först den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

Sedan anropar du följande kommando för att uppgradera kontot, vilket ersätter resurs gruppens namn, lagrings kontots namn och önskad konto åtkomst nivå.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Ange en åtkomst nivå för BLOB-data

Allmänna-Purpose v2-konton har stöd för alla Azure Storage-tjänster och data objekt, men åtkomst nivåer är bara tillgängliga för att blockera blobbar i Blob Storage. När du uppgraderar till ett allmänt-syfte v2-lagrings konto kan du ange en standard åtkomst nivå för kontot med frekvent eller låg frekvent, vilket anger standard nivån som dina BLOB-data laddas upp som om parametern för den enskilda BLOB-åtkomsten inte har angetts.

Med BLOB Access-nivåer kan du välja den mest kostnads effektiva lagringen utifrån dina förväntade användnings mönster. Block-blobbar kan lagras på frekventa, låg frekventa eller Arkiv lag rings nivåer. Mer information om åtkomst nivåer finns i [Azure Blob Storage: frekvent, låg frekvent och Arkiv lag](../blobs/storage-blob-storage-tiers.md)rings nivåer.

Som standard skapas ett nytt lagrings konto i nivån frekvent åtkomst och ett allmänt v1-lagrings konto kan uppgraderas till antingen frekvent eller låg frekvent konto nivå. Om ingen åtkomst nivå för kontot har angetts vid uppgraderingen, uppgraderas den till aktiv som standard. Om du undersöker vilken åtkomst nivå som ska användas för din uppgradering bör du tänka på ditt nuvarande data användnings scenario. Det finns två vanliga användar scenarier för att migrera till ett allmänt-syfte v2-konto:

* Du har ett befintligt allmänt v1-lagrings konto och vill utvärdera en uppgradering till ett allmänt-syfte v2-lagrings konto med rätt lagrings åtkomst nivå för BLOB-data.
* Du har valt att använda ett allmänt lagrings konto eller har redan ett och vill utvärdera om du ska använda frekvent eller låg frekvent åtkomst nivå för BLOB-data.

I båda fallen är den första prioriteten att uppskatta kostnaden för att lagra, komma åt och arbeta med dina data som lagras i ett allmänt lagrings konto och jämföra det med dina nuvarande kostnader.

## <a name="pricing-and-billing"></a>Priser och fakturering

Att uppgradera ett v1-lagrings konto till ett allmänt-syfte v2-konto är kostnads fritt. Du kan ange önskad konto nivå under uppgraderings processen. Om ingen konto nivå har angetts vid uppgradering blir standard konto nivån för det uppgraderade kontot `Hot` . Men om du ändrar lagrings åtkomst nivån efter uppgraderingen kan det leda till ändringar i fakturan så att du bör ange den nya konto nivån under uppgraderingen.

För alla lagringskonton används en prissättningsmodell för bloblagring som baseras på nivån för varje blob. När du använder ett lagringskonto gäller följande för debitering:

* **Lagrings kostnader**: utöver mängden data som lagras varierar kostnaden för att lagra data beroende på lagrings åtkomst nivå. Kostnaden per gigabyte minskas när nivån blir mer lågfrekvent.

* **Kostnader för dataåtkomst**: Kostnaderna för dataåtkomst ökar när nivån blir mer lågfrekvent. För data i den låg frekventa och Arkiv lag rings nivån debiteras du en åtkomst avgift per Gigabyte för läsningar.

* **Transaktionskostnader**: Du debiteras en kostnad per transaktion för alla nivåer som ökar när nivån blir mer lågfrekvent.

* **Dataöverföringskostnader för geo-replikering**: Den här avgiften gäller endast konton med konfigurerad geo-replikering, inklusive GRS och RA-GRS. Dataöverföring för geo-replikering debiteras per gigabyte.

* **Kostnader för utgående dataöverföring**: Utgående dataöverföringar (data som överförs utanför en Azure-region) debiteras för bandbreddsanvändning per gigabyte, på samma sätt som för allmänna lagringskonton.

* **Ändra lagrings åtkomst nivån**: om du ändrar åtkomst nivån för konto lagring från låg frekvent till frekvent tillkommer en avgift som motsvarar att läsa alla data som finns i lagrings kontot. Att ändra kontots åtkomst nivå från frekvent till låg frekvent innebär dock en avgift som motsvarar skrivning av alla data till den låg frekventa nivån (endast GPv2-konton).

> [!NOTE]
> Mer information om prissättningen för lagringskonton finns på sidan [Pris för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Mer information om kostnaderna för utgående dataöverföring finns på sidan [Prisinformation om Dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Beräkna kostnader för dina aktuella användnings mönster

För att beräkna kostnaden för att lagra och komma åt BLOB-data i ett allmänt lagrings konto i en viss nivå, ska du utvärdera ditt befintliga användnings mönster eller approximera ditt förväntade användnings mönster. Vanligtvis vill du veta:

* Din användning av Blob Storage, i gigabyte, inklusive:
  * Hur mycket data lagras i lagringskontot?
  * Hur ändras datavolymen på månadsbasis; ersätter nya data ständigt gamla data?

* Det primära åtkomst mönstret för dina Blob Storage-data, inklusive:
  * Hur mycket data läses från och skrivs till lagrings kontot?
  * Hur många Läs åtgärder och skriv åtgärder sker på data i lagrings kontot?

För att avgöra den bästa åtkomst nivån för dina behov kan det vara bra att fastställa din BLOB-datakapacitet och hur dessa data används. Detta gör du genom att titta på övervaknings måtten för ditt konto.

### <a name="monitoring-existing-storage-accounts"></a>Övervaka befintliga lagringskonton

Om du vill övervaka dina befintliga lagringskonton och samla in dessa data kan du använda Azure-lagringsanalys som utför loggning och tillhandahåller mätvärden för ett lagringskonto. Azure-lagringsanalys kan lagra mätvärden som innehåller aggregerad transaktionsstatistik och kapacitetsdata om förfrågningar till lagringstjänsten för konton av typerna GPv1, GPv2 och Blob Storage. Dessa data lagras i välkända tabeller i samma lagringskonto.

Mer information finns i [Om mätvärden i Storage Analytics](../blobs/monitor-blob-storage.md) och [Schema över måttabeller i Storage Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)

> [!NOTE]
> Blob Storage-konton exponerar endast tabelltjänstens slutpunkt för att lagra och komma åt mätvärden för det aktuella kontot.

Om du vill övervaka lagringsanvändningen för Blob Storage måste du aktivera kapacitetsmåtten.
Med detta aktiverat registreras kapacitets data dagligen för ett lagrings kontos Blob Service och registreras som en tabell post som skrivs till *$MetricsCapacityBlobs* tabellen i samma lagrings konto.

Om du vill övervaka dataåtkomstmönstren för Blob Storage måste du aktivera transaktionsmått för varje timme från API:et. Med transaktionsmått för varje timme aktiverat aggregeras transaktioner för varje API varje timme, och registreras som en tabellpost som skrivs till tabellen *$MetricsHourPrimaryTransactionsBlob* i samma lagringskonto. Tabellen *$MetricsHourSecondaryTransactionsBlob* registrerar transaktionerna till den sekundära slutpunkten när du använder RA-GRS-lagringskonton.

> [!NOTE]
> Om du har ett allmänt lagringskonto (GPvX) där du har lagrat sidblobar och virtuella datordiskar, eller köer, filer eller tabeller, utöver block- och tilläggsblobdata så gäller inte den här uppskattningsberäkningen. Kapacitetsdata gör ingen åtskillnad mellan blockblobar och andra typer och kapacitetsdata visas inte för andra datatyper. Om du använder dessa typer är en alternativ metod att titta på kvantiteterna på din senaste faktura.

För att få en bra uppskattning av din dataförbrukning och ditt åtkomstmönster rekommenderar vi att du väljer en kvarhållningsperiod för mätvärden som är representativ för din normala användning, och att du utgår därifrån. Ett alternativ är att spara mätvärdena i sju dagar och samla in data varje vecka, för att sedan analysera dessa data i slutet av månaden. Ett annat alternativ är att spara mätvärden i 30 dagar och samla in och analysera dessa data i slutet av 30-dagarsperioden.

Mer information om hur du aktiverar, samlar in och visar mått data finns i [Storage Analytics-mått](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Lagring, åtkomst och hämtning av analysdata debiteras på samma sätt som vanliga användardata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Beräkna kostnader med hjälp av användningsmått

#### <a name="capacity-costs"></a>Kapacitets kostnader

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
> Ett mer detaljerat exempel på hur du beräknar kostnader för att använda frekvent eller låg frekvent åtkomst nivå finns i avsnittet med vanliga frågor och svar *"Vad är frekventa och frekventa åtkomst nivåer och hur ska jag avgöra vilken av dem som ska användas?"* på [Azure Storage-prissidan](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Nästa steg

* [Översikt över lagringskonto](storage-account-overview.md)
* [Skapa ett lagringskonto](storage-account-create.md)
* [Flytta ett Azure Storage-konto till en annan region](storage-account-move.md)
* [Återställa ett borttaget lagringskonto](storage-account-recover.md)