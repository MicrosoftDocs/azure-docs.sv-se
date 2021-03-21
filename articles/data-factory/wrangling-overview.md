---
title: Data datatransformering i Azure Data Factory
description: En översikt över data Datatransformering i Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738177"
---
# <a name="what-is-data-wrangling"></a>Vad är data datatransformering?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Organisationer behöver kunna utforska sina kritiska affärs data för förberedelse av data och datatransformering för att tillhandahålla en korrekt analys av komplexa data som fortsätter att växa varje dag. Förberedelse av data krävs så att organisationer kan använda data i olika affärs processer och minska tiden till värde.

Data Factory ger dig möjlighet att förbereda kod fri data förberedelse i moln skala upprepade gånger med Power Query. Data Factory integreras med [Power Query online](/power-query/) och gör Power Query M-funktioner tillgängliga som en pipeline-aktivitet.

Data Factory översätter M som genererats av Power Query mashup-redigeraren till Spark-kod för moln skalnings körning genom att översätta M till Azure Data Factory data flöden. Datatransformering-data med Power Query och data flöden är särskilt användbara för data tekniker eller "medborgarnas Data Integrator".

> [!NOTE]
> Power Query aktivitet i Azure Data Factory är för närvarande tillgänglig i offentlig för hands version

## <a name="use-cases"></a>Användningsfall

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snabb interaktiv data utforskning och förberedelse

Flera data tekniker och medborgarnas Data Integrator kan interaktivt utforska och förbereda data uppsättningar i moln skala. Med ökningen av volym, variation och hastighet av data i data sjöar behöver användarna ett effektivt sätt att utforska och förbereda data uppsättningar. Du kan till exempel behöva skapa en data uppsättning som har all kund demografisk information för nya kunder sedan 2017. Du mappar inte till ett känt mål. Du är utforska, datatransformering och förberedelser data uppsättningar för att uppfylla ett krav innan du publicerar det i sjön. Datatransformering används ofta för mindre formella analys scenarier. Beredd-datauppsättningarna kan användas för att utföra omvandlingar och maskin inlärnings åtgärder.

### <a name="code-free-agile-data-preparation"></a>Kod – kostnads fri förberedelse av smidig data behandling

Medborgarnas data integratorer ägnar mer än 60% av tiden att söka efter och förbereda data. De vill göra det med en kod gratis för att förbättra drift produktiviteten. Genom att tillåta att medborgarna integrerar, formar och publicerar data med hjälp av kända verktyg som Power Query online på ett skalbart sätt förbättrar produktiviteten avsevärt. Datatransformering i Azure Data Factory aktiverar den välbekanta Power Query online-redigeraren för att tillåta att data integreras på ett snabbt, standardiserade data och att skapa data med hög kvalitet för att stödja affärs beslut.

### <a name="data-validation-and-exploration"></a>Data verifiering och-utforskning

Genomsök data visuellt i ett kod fritt sätt för att ta bort eventuella avvikare, avvikelser och anpassa dem till en form för snabb analys.

## <a name="supported-sources"></a>Källor som stöds

| Anslutning | Dataformat | Autentiseringstyp |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Konto nyckel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Tjänstens huvudnamn |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Konto nyckel, tjänstens huvud namn |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-autentisering |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-autentisering |

## <a name="the-mashup-editor"></a>Mashup-redigeraren

När du skapar en Power Query-aktivitet blir alla käll data uppsättningar en data uppsättnings fråga och placeras i mappen **ADFResource** . Som standard pekar UserQuery på den första data uppsättnings frågan. Alla transformeringar bör utföras på UserQuery eftersom ändringar av data uppsättnings frågor inte stöds och inte kommer att vara kvar. Det finns för närvarande inte stöd för att byta namn på, lägga till och ta bort frågor.

![Datatransformering](media/wrangling-data-flow/editor.png)

För närvarande stöds inte alla Power Query M-funktioner för data datatransformering trots att de är tillgängliga under redigeringen. När du skapar dina Power Query aktiviteter uppmanas du att ange följande fel meddelande om en funktion inte stöds:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Mer information om omvandlingar som stöds finns i [data datatransformering Functions](wrangling-functions.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en data datatransformering Power Query kombinera](wrangling-tutorial.md).
