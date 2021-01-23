---
title: Använda Azure import/export för att överföra data till och från Azure Storage | Microsoft Docs
description: Lär dig hur du skapar import-och export jobb i Azure Portal för att överföra data till och från Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 6dd2c7fcef6860cb5a2eaf88d141f8567955ed25
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706729"
---
# <a name="what-is-azure-importexport-service"></a>Vad är Azure import/export-tjänsten?

Azure import/export-tjänsten används för att på ett säkert sätt importera stora mängder data till Azure Blob Storage och Azure Files genom att leverera disk enheter till ett Azure-datacenter. Du kan också använda den här tjänsten till att överföra data från Azure Blob Storage till diskenheter som sedan levereras till dig lokalt. Data från en eller flera disk enheter kan importeras antingen till Azure Blob Storage eller Azure Files.

Ange egna disk enheter och överför data med Azure import/export-tjänsten. Du kan också använda disk enheter som tillhandahålls av Microsoft.

Om du vill överföra data med hjälp av disk enheter som tillhandahålls av Microsoft kan du använda [Azure Data Box disk](../databox/data-box-disk-overview.md) för att importera data till Azure. Microsoft levererar upp till 5 krypterade solid-state disk-enheter (SSD) med 40 TB total kapacitet per beställning, till ditt data Center via en regional bärvåg. Du kan snabbt konfigurera disk enheter, kopiera data till disk enheter via en USB 3,0-anslutning och leverera disk enheterna tillbaka till Azure. Mer information finns i [Azure Data Box disk översikt](../databox/data-box-disk-overview.md).

## <a name="azure-importexport-use-cases"></a>Användnings fall för Azure import/export

Överväg att använda Azure import/export-tjänsten när du laddar upp eller laddar ned data över nätverket är för långsamt, eller så är det inte längre att få ytterligare nätverks bandbredd. Använd den här tjänsten i följande scenarier:

* **Migrering av data till molnet**: flytta stora mängder data till Azure snabbt och kostnads effektivt.
* **Innehålls distribution**: skicka data snabbt till dina kund platser.
* **Säkerhets kopiering**: gör säkerhets kopior av dina lokala data för lagring i Azure Storage.
* **Data återställning**: återställa stor mängd data som lagras i lagring och som levereras till din lokala plats.

## <a name="importexport-components"></a>Importera/exportera komponenter

Import/export-tjänsten använder följande komponenter:

* **Import/export-tjänst**: den här tjänsten är tillgänglig i Azure Portal hjälper användaren att skapa och spåra data import (överför) och exportera (Hämta) jobb.  

* **WAImportExport-verktyg**: det här är ett kommando rads verktyg som gör följande:
  * Förbereder disk enheterna som levereras för import.
  * Underlättar kopiering av data till enheten.
  * Krypterar data på enheten med AES 256-bitars BitLocker. Du kan använda ett externt nyckel skydd för att skydda din BitLocker-nyckel.
  * Genererar de enhets Journals-filer som används när importen skapas.
  * Hjälper till att identifiera antalet enheter som krävs för export jobb.

> [!NOTE]
> WAImportExport-verktyget finns i två versioner, version 1 och 2. Vi rekommenderar att du använder:
>
> * Version 1 för import/export till Azure Blob Storage.
> * Version 2 för att importera data till Azure Files.
>
> WAImportExport-verktyget är endast kompatibelt med 64-bitars Windows-operativsystem. För särskilda OS-versioner som stöds, gå till [import/export-krav för Azure](storage-import-export-requirements.md#supported-operating-systems).

* **Disk enheter**: du kan leverera solid state-hårddiskar (SSD) eller hård diskar (HDD) till Azure-datacentret. När du skapar ett import jobb levererar du disk enheter som innehåller dina data. När du skapar ett export jobb levererar du tomma enheter till Azure-datacentret. För vissa disk typer går du till [disk typer som stöds](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hur fungerar Import/Export?

Azure import/export-tjänsten tillåter data överföring till Azure-blobbar och Azure Files genom att skapa jobb. Använd Azure Portal eller Azure Resource Manager REST API för att skapa jobb. Varje jobb är associerat med ett enda lagrings konto.

Jobben kan vara import-eller export jobb. Med ett import jobb kan du importera data till Azure-blobbar eller Azure-filer medan export jobbet tillåter att data exporteras från Azure-blobbar. För ett import jobb levererar du enheter som innehåller dina data. När du skapar ett export jobb levererar du tomma enheter till ett Azure-datacenter. I varje fall kan du leverera upp till 10 disk enheter per jobb.

### <a name="inside-an-import-job"></a>Inuti ett import jobb

På hög nivå omfattar ett import jobb följande steg:

1. Avgör vilka data som ska importeras, hur många enheter du behöver, målets BLOB-plats för dina data i Azure Storage.
2. Använd verktyget WAImportExport för att kopiera data till disk enheter. Kryptera disk enheterna med BitLocker.
3. Skapa ett import jobb på mål lagrings kontot i Azure Portal. Ladda upp filer för enhets journalen.
4. Ange avsändar adressen och transport företagets konto nummer för att leverera enheterna tillbaka till dig.
5. Leverera disk enheterna till leverans adressen som angavs när jobbet skapades.
6. Uppdatera leverans spårnings numret i import jobb informationen och skicka import jobbet.
7. Enheterna tas emot och bearbetas i Azure Data Center.
8. Enheterna levereras med ditt transport konto till avsändar adressen som anges i import jobbet.

> [!NOTE]
> För lokala (inom land/region i Data Center) kan du dela ett inhemskt transport konto.
>
> För utgångs punkt (utanför Data Center land/region) kan du dela ett internationellt transport konto.

 ![Bild 1: importera jobb flöde](./media/storage-import-export-service/importjob.png)

Steg-för-steg-instruktioner om data import finns på:

* [Importera data till Azure-blobbar](storage-import-export-data-to-blobs.md)
* [Importera data till Azure Files](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Inuti ett export jobb

> [!IMPORTANT]
> Tjänsten stöder endast export av Azure-blobbar. Export av Azure-filer stöds inte.

Ett export jobb på hög nivå omfattar följande steg:

1. Bestäm vilka data som ska exporteras, antalet enheter du behöver, käll-blobbar eller container vägar för dina data i Blob Storage.
2. Skapa ett export jobb i ditt käll lagrings konto i Azure Portal.
3. Ange käll-blobbar eller container Sök vägar för de data som ska exporteras.
4. Ange avsändar adressen och transport företagets konto nummer för att leverera enheterna tillbaka till dig.
5. Leverera disk enheterna till leverans adressen som angavs när jobbet skapades.
6. Uppdatera leverans spårnings numret i export jobb informationen och skicka export jobbet.
7. Enheterna tas emot och bearbetas i Azure Data Center.
8. Enheterna är krypterade med BitLocker och nycklarna är tillgängliga via Azure Portal.  
9. Enheterna levereras med ditt transport konto till avsändar adressen som anges i import jobbet.

> [!NOTE]
> För lokala (inom land/region i Data Center) kan du dela ett inhemskt transport konto.
>
> För utgångs punkt (utanför Data Center land/region) kan du dela ett internationellt transport konto.
  
 ![Bild 2: Exportera jobb flöde](./media/storage-import-export-service/exportjob.png)

Steg-för-steg-instruktioner om data export finns i [Exportera data från Azure-blobar](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Regional tillgänglighet

Tjänsten Azure import/export stöder kopiering av data till och från alla Azure Storage-konton. Du kan leverera disk enheter till en av de angivna platserna. Om ditt lagrings konto finns på en Azure-plats som inte anges här, anges en alternativ leverans plats när du skapar jobbet.

### <a name="supported-shipping-locations"></a>Leverans platser som stöds

|Land/region  |Land/region  |Land/region  |Land/region  |
|---------|---------|---------|---------|
|East US    | Europa, norra        | Indien, centrala        |USA Gov, Iowa         |
|USA, västra     |Europa, västra         | Indien, södra        | USA DoD, östra        |
|USA, östra 2    | Asien, östra        |  Indien, västra        | USA DoD, centrala        |
|USA, västra 2     | Sydostasien        | Kanada, centrala        | Kina, östra         |
|Central US     | Australien, östra        | Kanada, östra        | Kina, norra        |
|USA, norra centrala     |  Australien, sydöstra       | Brasilien, södra        | Storbritannien, södra        |
|USA, södra centrala     | Japan, västra        |Sydkorea, centrala         | Tyskland, centrala        |
|USA, västra centrala     |  Japan, östra       | US Gov, Virginia        | Tyskland, nordöstra        |
|Sydafrika, västra   |  Sydafrika, norra |

## <a name="security-considerations"></a>Säkerhetsöverväganden

Data på enheten krypteras med AES 256-bitars BitLocker-diskkryptering. Den här krypteringen skyddar dina data när de överförs.

För import jobb krypteras enheter på två sätt.  

* Ange alternativet när du använder *dataset.csv* -filen när du kör WAImportExport-verktyget när du förbereder enheten.

* Aktivera BitLocker-kryptering manuellt på enheten. Ange krypterings nyckeln i *driveset.csv* när du kör kommando raden för WAImportExport-verktyget under förberedelse av enheten. BitLocker-krypteringsnyckeln kan skyddas ytterligare med hjälp av ett externt nyckel skydd (kallas även för hanterad Microsoft-nyckel) eller en kund hanterad nyckel. Mer information finns i så här [använder du en kundhanterad nyckel för att skydda din BitLocker-nyckel](storage-import-export-encryption-key-portal.md).

För export jobb, när dina data har kopierats till enheterna, krypterar tjänsten enheten med BitLocker innan du levererar tillbaka den till dig. Du får en krypterings nyckel via Azure Portal. Enheten måste låsas upp med WAImporExport-verktyget med hjälp av nyckeln.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Prissättning

**Enhets hanterings avgift**

Det finns en enhets hanterings avgift för varje enhet som bearbetas som en del av ditt import-eller export jobb. Se informationen på priserna för [import/export i Azure](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frakt kostnader**

När du levererar enheter till Azure betalar du leverans kostnaden till transport företaget. När Microsoft returnerar enheterna till dig debiteras leverans kostnaden på det transport företags konto som du angav när jobbet skapades.

**Transaktionskostnader**

[Standard lagrings transaktions avgift](https://azure.microsoft.com/pricing/details/storage/) gäller vid import och export av data. Standard avgifter för utgående trafik kan också användas tillsammans med lagrings transaktions avgifter när data exporteras från Azure Storage. Mer information om utgående kostnader finns i [priser för data överföring.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder import/export-tjänsten för att:

* [Importera data till Azure-blobbar](storage-import-export-data-to-blobs.md)
* [Exportera data från Azure-blobbar](storage-import-export-data-from-blobs.md)
* [Importera data till Azure Files](storage-import-export-data-to-files.md)
