---
title: Så här använder du data källor för data profilering i Azure Data Catalog
description: Instruktions artikel som visar hur du lägger till data profiler för tabell-och kolumn nivåer när du registrerar data källor i Azure Data Catalog och hur du använder data profiler för att förstå data källor.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 473ba69fa30542dfa41dfc5f19002ac36b56b482
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674962"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Så här data källor för data källor i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad moln tjänst som fungerar som ett system för registrering och identifierings system för företags data källor. Med andra ord är **Azure Data Catalog** att hjälpa människor att upptäcka, förstå och använda data källor och hjälpa organisationer att få mer värde än befintliga data. När en data källa registreras med **Azure Data Catalog** kopieras och indexeras dess metadata av tjänsten, men artikeln slutar inte där.

**Data profilerings** funktionen i **Azure Data Catalog** undersöker data från data källor som stöds i katalogen och samlar in statistik och information om dessa data. Det är enkelt att inkludera en profil för dina data till gångar. När du registrerar en data till gång väljer du **inkludera data profil** i registrerings verktyget för data källor.

## <a name="what-is-data-profiling"></a>Vad är data profilering

Data profilering undersöker data i data källan som registreras och samlar in statistik och information om dessa data. Under identifieringen av data källor kan dessa statistik hjälpa dig att avgöra lämpligheten hos data för att lösa sitt företags problem.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Följande data källor har stöd för data profilering:

* SQL Server (inklusive Azure SQL DB-och Azure Synapse Analytics)-tabeller och-vyer
* Oracle-tabeller och vyer
* Teradata-tabeller och vyer
* Hive-tabeller

Om du inkluderar data profiler när du registrerar data till gångar kan användarna svara på frågor om data källor, inklusive:

* Kan den användas för att lösa mitt företags problem?
* Överensstämmer informationen med specifika standarder eller mönster?
* Vad är några av avvikelserna i data källan?
* Vilka är möjliga utmaningar med att integrera dessa data i mitt program?

> [!NOTE]
> Du kan också lägga till dokumentation till en till gång för att beskriva hur data kan integreras i ett program. Se [så här dokumenterar du data källor](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Så här lägger du till en data profil när du registrerar en data Källa

Det är enkelt att inkludera en profil för din data källa. När du registrerar en data källa väljer du **inkludera data profil** i panelen **objekt som ska registreras** i registrerings verktyget för data källor.

![Kryss rutan inkludera data profil](media/data-catalog-data-profile/data-catalog-register-profile.png)

Mer information om hur du registrerar data källor finns i [så här registrerar du data källor](data-catalog-how-to-register.md) och [kommer igång med Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrera data till gångar som innehåller data profiler

Om du vill identifiera data till gångar som innehåller en data profil kan du inkludera `has:tableDataProfiles` eller `has:columnsDataProfiles` som en av dina Sök villkor.

> [!NOTE]
> Om du väljer **inkludera data profil** i registrerings verktyget för data källor ingår profil information för både tabell och kolumn nivå. Data Catalogs-API: et gör det dock möjligt för data till gångar att registreras med bara en uppsättning profil uppgifter som ingår.
>

## <a name="viewing-data-profile-information"></a>Visa information om data profiler

När du har hittat en lämplig data källa med en profil kan du Visa information om data profilen. Om du vill visa data profilen väljer du en data till gång och väljer **data profil** i fönstret Data Catalog Portal.

![Fliken Data profil](media/data-catalog-data-profile/data-catalog-view.png)

En data profil i **Azure Data Catalog** visar profil information för tabeller och kolumner, inklusive:

### <a name="object-data-profile"></a>Objekt data profil

* Antal rader
* Tabellstorlek
* När objektet senast uppdaterades

### <a name="column-data-profile"></a>Kolumn data profil

* Kolumn data typ
* Antal distinkta värden
* Antal rader med NULL-värden
* Lägsta, högsta, genomsnittliga och standard avvikelse för kolumn värden

## <a name="summary"></a>Sammanfattning

Med data profilering får du statistik och information om registrerade data till gångar som hjälper dig att avgöra lämpligheten hos data för att lösa affärs problem. Förutom att kommentera och dokumentera data källor kan data profilerna ge användarna en djupare förståelse för dina data.

## <a name="see-also"></a>Se även

* [Så här registrerar du datakällor](data-catalog-how-to-register.md)
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
