---
title: Vad är Azure Data Lake Storage Gen1? | Microsoft Docs
description: Översikt över Data Lake Storage Gen1 (tidigare Azure Data Lake Store) och värdet den ger över andra data lager
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: twooley
ms.openlocfilehash: 52c00359fb4e759d9517dee958e31c00feb2b33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149242"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Vad är Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagrings plats för stora data analys arbets belastningar. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

Data Lake Storage Gen1 kan nås från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API: er. Den är utformad för att möjliggöra analyser av lagrade data och är anpassat för prestanda för data analys scenarier. Data Lake Storage Gen1 innehåller alla funktioner i företags klass: säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>De viktigaste funktionerna

Några av de viktigaste funktionerna i Data Lake Storage Gen1 inkluderar följande.

### <a name="built-for-hadoop"></a>Byggt för Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop fil system som är kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-eko systemet. Dina befintliga HDInsight-program eller-tjänster som använder WebHDFS-API: n kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 också att exponera ett WebHDFS REST-gränssnitt för program.

Du kan enkelt analysera data som lagras i Data Lake Storage Gen1 med hjälp av Hadoop-analytiska ramverk som MapReduce eller Hive. Du kan etablera Azure HDInsight-kluster och konfigurera dem att direkt komma åt data som lagras i Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och kan lagra en mängd data för analys. Det innebär inte några begränsningar för konto storlekar, fil storlekar eller mängden data som kan lagras i en data Lake. Enskilda filer kan vara från kilobyte till petabyte i storlek. Data lagras varaktigt genom att göra flera kopior. Det finns ingen gräns för hur länge data kan lagras i data Lake.

### <a name="performance-tuned-for-big-data-analytics"></a>Prestandajusterad för analyser av stordata

Data Lake Storage Gen1 skapas för att köra storskaliga analys system som kräver enorma data flöden för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

### <a name="enterprise-ready-highly-available-and-secure"></a>Företags klart: hög tillgänglighet och säker

Data Lake Storage Gen1 ger bransch standard tillgänglighet och tillförlitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel.

Data Lake Storage Gen1 ger också säkerhet i företags klass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alla data

Data Lake Storage Gen1 kan lagra data i sitt ursprungliga format, utan att det krävs tidigare omvandlingar. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in, lämnar det till det enskilda analys ramverket för att tolka data och definiera ett schema vid tidpunkten för analysen. Möjligheten att lagra filer av godtycklig storlek och format gör det möjligt för Data Lake Storage Gen1 att hantera strukturerade, delvis strukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du arbetar med lagrade data med SDK: er, Azure Portal och Azure PowerShell. Om du lagrar dina data i butiken med hjälp av de här gränssnitten och använder lämpliga behållare, kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på den typ av data som lagras.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Skydda data

Data Lake Storage Gen1 använder Azure Active Directory (Azure AD) för autentisering och åtkomst kontrol listor (ACL: er) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Autentisering |Data Lake Storage Gen1 integreras med Azure AD för identitets-och åtkomst hantering för alla data som lagras i Data Lake Storage Gen1. På grund av integreringen Data Lake Storage Gen1 fördelarna med alla Azure AD-funktioner, till exempel Multi-Factor Authentication, villkorlig åtkomst, Azure rollbaserad åtkomst kontroll, övervakning av program användning, säkerhetsövervakning och avisering och så vidare. Data Lake Storage Gen1 stöder OAuth 2,0-protokollet för autentisering i REST-gränssnittet. Se [data Lake Storage gen1 autentisering](data-lakes-store-authentication-using-azure-active-directory.md).|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomst kontroll genom att stödja POSIX-stil-behörigheter som exponeras av WebHDFS-protokollet. Du kan aktivera ACL: er på rotmappen, i undermappar och på enskilda filer. Mer information om hur ACL: er fungerar i samband med Data Lake Storage Gen1 finns i [åtkomst kontroll i data Lake Storage gen1](data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller även kryptering för data som lagras i kontot. Du anger krypterings inställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att kryptera dina data eller välja ingen kryptering. Mer information finns i [kryptering i data Lake Storage gen1](data-lake-store-encryption.md). Instruktioner för hur du tillhandahåller en krypterings-relaterad konfiguration finns i [Kom igång med data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). |

Instruktioner för hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data i Azure Data Lake Storage gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Programkompatibilitet

Data Lake Storage Gen1 är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet. Den integreras också bra med andra Azure-tjänster. Om du vill veta mer om hur du kan använda Data Lake Storage Gen1 med komponenter med öppen källkod och andra Azure-tjänster använder du följande länkar:

- Se [program och tjänster som är kompatibla med Azure Data Lake Storage gen1](data-lake-store-compatible-oss-other-applications.md) för en lista med program med öppen källkod som är kompatibla med data Lake Storage gen1.
- Se [integrera med andra Azure-tjänster](data-lake-store-integrate-with-other-services.md) för att förstå hur du använder data Lake Storage gen1 med andra Azure-tjänster för att möjliggöra ett bredare utbud av scenarier.
- Se [scenarier för att använda data Lake Storage gen1](data-lake-store-data-scenarios.md) för att lära dig hur du använder data Lake Storage gen1 i scenarier som att mata in data, bearbeta data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 fil system

Data Lake Storage Gen1 kan nås via fil systemet AzureDataLakeFilesystem (adl://) i Hadoop-miljöer (tillgängliga med HDInsight-kluster). Program och tjänster som använder adl://kan dra nytta av ytterligare prestanda optimeringar som inte är tillgängliga i WebHDFS. Det innebär att Data Lake Storage Gen1 ger dig flexibiliteten att antingen använda bästa prestanda med det rekommenderade alternativet att använda adl://eller underhålla befintlig kod genom att fortsätta att använda WebHDFS-API: et direkt. Azure HDInsight utnyttjar fullt ut AzureDataLakeFilesystem för att ge bästa möjliga prestanda på Data Lake Storage Gen1.

Du kan komma åt dina data i Data Lake Storage Gen1 med hjälp av `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` . Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [Visa egenskaper för lagrade data](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Data Lake Storage Gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md)
- [Kom igång med Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)