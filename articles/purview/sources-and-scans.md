---
title: Datakällor som stöds och filtyper
description: Den här artikeln innehåller konceptuell information om data källor och filtyper som stöds i avdelningens kontroll.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677916"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Data källor och filtyper som stöds i Azure avdelningens kontroll

I den här artikeln beskrivs data källor som stöds, filtyper och skannings koncept i avdelningens kontroll.

## <a name="supported-data-sources"></a>Datakällor som stöds

Azure avdelningens kontroll stöder följande källor:

| Lagrings typ | Autentiseringstyp som stöds | Konfigurera genomsökningar via UX/PowerShell |
| ---------- | ------------------- | ------------------------------ |
| Lokala SQL Server                   | SQL-autentisering                        | UX                                |
| Azure Synapse Analytics (tidigare SQL DW)            | SQL-autentisering, tjänstens huvud namn, MSI               | UX                             |
| Azure SQL Database (DB)                  | SQL-autentisering, tjänstens huvud namn, MSI               | UX |
| Hanterad Azure SQL Database-instans      | SQL-autentisering, tjänstens huvud namn, MSI               | UX    |
| Azure Blob Storage                       | Konto nyckel, tjänstens huvud namn, MSI | UX            |
| Azure-datautforskaren                      | Tjänstens huvudnamn                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Tjänstens huvud namn, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Konto nyckel, tjänstens huvud namn, MSI            | UX            |
| Azure Cosmos DB                          | Konto nyckel                                    | UX            |


> [!Note]
> Azure Data Lake Storage Gen2 är nu allmänt tillgänglig. Vi rekommenderar att du börjar använda den idag. Mer information finns på [produkt sidan](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Filtyper som stöds för genomsökning

Följande filtyper stöds för genomsökning, för schema extrahering och klassificering i tillämpliga fall:

- Strukturerade fil format som stöds av tillägget: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- Dokument fil format som stöds av tillägget: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Avdelningens kontroll stöder också anpassade fil namns tillägg och anpassade tolkare.
 
> [!Note]
> Varje gzip-fil måste mappas till en enda CSV-fil i. Gzip-filer omfattas av system-och anpassade klassificerings regler. Vi stöder för närvarande inte skanning av en GZIP-fil som är mappad till flera filer inom eller andra filtyper än CSV. 

## <a name="sampling-within-a-file"></a>Sampling i en fil

I avdelningens kontroll-terminologi,
- L1-genomsökning: extraherar grundläggande information och metadata som fil namn, storlek och fullständigt kvalificerat namn
- L2-skanning: extraherar schema för strukturerade filtyper och databas tabeller
- L3-genomsökning: extraherar schema där tillämpligt och ämnen av exempel filen till system-och anpassade klassificerings regler

För alla strukturerade fil format, avdelningens kontroll skanner exempel filer på följande sätt:

- För strukturerade filtyper, samplar den 128 rader i varje kolumn eller 1 MB, beroende på vilket som är lägre.
- För dokument fil format är det en exempel på 20 MB av varje fil.
    - Om en dokument fil är större än 20 MB, omfattas den inte av en djup genomsökning (klassificering). I så fall fångar avdelningens kontroll endast grundläggande metadata som fil namn och fullständigt kvalificerat namn.

## <a name="resource-set-file-sampling"></a>Sampling av resurs uppsättnings fil

En mapp eller grupp med diskpartitioner identifieras som en *resurs uppsättning* i avdelningens kontroll, om den matchar en princip för system resurs uppsättning eller en kunddefinierad resurs uppsättnings princip. Om en resurs uppsättning identifieras, kommer avdelningens kontroll att sampla varje mapp som den innehåller. Lär dig mer om resurs uppsättningar [här](concept-resource-sets.md).

Fil sampling för resurs uppsättningar efter filtyper:

- **Avgränsade filer (CSV, PSV, SSV, TSV)** -1 i 100 filer samplas (L3-skanning) i en mapp eller grupp med diskpartitioner som betraktas som en resurs uppsättning
- **Data Lake filtyper (Parquet, Avro, Orc)** -1 i 18446744073709551615 (Long max) samplas (L3-skanning) i en mapp eller en grupp med diskpartitioner som betraktas som en *resurs uppsättning*
- **Andra strukturerade filtyper (JSON, XML, txt)** -1 i 100 filer samplas (L3-skanning) i en mapp eller grupp med diskpartitioner som betraktas som en resurs uppsättning
- **SQL-objekt och CosmosDB-entiteter** – varje fil är L3-genomsökd.
- **Dokument fil typer** – varje fil är L3-genomsökd. Resurs uppsättnings mönster gäller inte för de här fil typerna.

## <a name="scan-regions"></a>Skanna regioner
Följande är en lista över alla Azure Data Source-regioner (Data Center) där avdelningens kontroll-skannern körs. Om din Azure-datakälla finns i en region utanför listan, kommer skannern att köras i regionen för din avdelningens kontroll-instans.
 
### <a name="purview-scanner-regions"></a>Avdelningens kontroll-skanner regioner

- EastUs
- EastUs2 
- Usasödracentrala
- WestUs
- WestUs2
- Sydostasien
- Västeuropa
- Europanorra
- UkSouth
- AustraliaEast
- Indiensödra
- Centrala
- Kanada
- Japanöstra
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>Klassificering

Alla system klassificerings regler för 105 gäller strukturerade fil format. Endast reglerna för MCE-klassificering gäller för dokument fil typer (inte data skannings inbyggda regex-mönster, filter-baserad identifiering av blomma). Mer information om klassificeringar som stöds finns i [klassificeringar som stöds i Azure avdelningens kontroll](supported-classifications.md).

## <a name="next-steps"></a>Nästa steg

- [Självstudie: kör Start paket och Genomsök data](tutorial-scan-data.md)
- [Hantera data källor i Azure avdelningens kontroll (för hands version)](manage-data-sources.md)