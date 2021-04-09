---
title: Översikt över Azure Data Lake Storage Gen1 i HDInsight
description: Översikt över Data Lake Storage Gen1 i HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4ac1a48733d104e7674acbc13bfbb1e7a4cf02b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938882"
---
# <a name="azure-data-lake-storage-gen1-overview-in-hdinsight"></a>Översikt över Azure Data Lake Storage Gen1 i HDInsight

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagrings plats för stor data analys arbets belastningar. Med hjälp av Azure Data Lake kan du samla in data om storlek, typ och inmatnings hastighet. Och på ett och samma ställe för operativa och undersökande analyser.

Åtkomst Data Lake Storage Gen1 från Hadoop (tillgängligt med ett HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API: er. Data Lake Storage Gen1 har utformats för att möjliggöra analyser av lagrade data och är anpassat för prestanda i scenarier med data analyser. Gen1 innehåller de funktioner som är viktiga för verkliga företags användnings fall. Dessa funktioner är bland annat säkerhet, hanterbarhet, pålitlighet och tillgänglighet.

Mer information om Azure Data Lake Storage Gen1 finns i den detaljerade [översikten över Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md).

Viktiga funktioner i Data Lake Storage Gen1 inkluderar följande.

## <a name="compatibility-with-hadoop"></a>Kompatibilitet med Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop fil system som är kompatibelt med HDFS och Hadoop-miljön.  HDInsight-program eller-tjänster som använder WebHDFS-API: n kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 också att exponera ett WebHDFS REST-gränssnitt för program.

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med hjälp av Hadoop analytiska ramverk. Ramverk som MapReduce eller Hive. Azure HDInsight-kluster kan tillhandahållas och konfigureras för direkt åtkomst till data som lagras i Data Lake Storage Gen1.

## <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och är lämplig för att lagra olika typer av data för analys. Den tillåter inte begränsningar av konto storlekar eller fil storlekar. Eller mängden data som kan lagras i ett data Lake. Enskilda filer är i storlek från kilobyte till petabyte, vilket gör Data Lake Storage Gen1 ett bra val för att lagra vilken typ av data som helst. Data lagras varaktigt genom att göra flera kopior. Och det finns ingen gräns för hur länge data kan lagras i data Lake.

## <a name="performance-tuning-for-big-data-analytics"></a>Prestanda justering för Big data Analytics

Data Lake Storage Gen1 är utformad för analys system. System som kräver massivt data flöde för att fråga och analysera stora mängder data. Data Lake sprider delar av en fil över flera enskilda lagrings servrar. När du analyserar data förbättrar den här installationen det lästa data flödet när filen läses parallellt.

## <a name="readiness-for-enterprise-highly-available-and-secure"></a>Beredskap för företag: hög tillgänglig och säker

Data Lake Storage Gen1 ger bransch standard tillgänglighet och tillförlitlighet. Data till gångar lagras varaktigt: redundanta kopior skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av sin befintliga data plattform.

Data Lake Storage Gen1 ger också säkerhet i företags klass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage gen1](#data-security-in-data-lake-storage-gen1).

## <a name="flexible-data-structures"></a>Flexibla data strukturer

Data Lake Storage Gen1 kan lagra data i sitt ursprungliga format, precis som det är, utan föregående omvandling. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in. Det enskilda analys ramverket tolkar data och definierar ett schema vid tidpunkten för analysen. Data Lake Storage Gen1 kan hantera strukturerade data. Och semistrukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du arbetar med lagrade data med hjälp av SDK: er, Azure Portal och Azure PowerShell. Data som lagras i butiken med de här gränssnitten och behållarna kan lagra vilken datatyp som helst. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på typen av data.

## <a name="data-security-in-data-lake-storage-gen1"></a>Data säkerhet i Data Lake Storage Gen1

Data Lake Storage Gen1 använder Azure Active Directory för autentisering och använder åtkomst kontrol listor (ACL: er) för att hantera åtkomst till dina data.

| **Funktion** | **Beskrivning** |
| --- | --- |
| Autentisering |Data Lake Storage Gen1 integreras med Azure Active Directory (Azure AD) för identitets-och åtkomst hantering för alla data som lagras i Data Lake Storage Gen1. På grund av integreringen Data Lake Storage Gen1 fördelarna med alla Azure AD-funktioner. Dessa funktioner är: multifaktorautentisering, villkorlig åtkomst och rollbaserad åtkomst kontroll i Azure. Även program användnings övervakning, säkerhetsövervakning och avisering och så vidare. Data Lake Storage Gen1 stöder OAuth 2,0-protokollet för autentisering i REST-gränssnittet. Se [autentisering i Azure Data Lake Storage gen1 med Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomst kontroll genom att stödja POSIX-format-behörigheter som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Mer information om hur ACL: er fungerar i samband med Data Lake Storage Gen1 finns i [åtkomst kontroll i data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller även kryptering för data som lagras i kontot. Du anger krypterings inställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att kryptera dina data eller välja ingen kryptering. Mer information finns i [kryptering i data Lake Storage gen1](../data-lake-store/data-lake-store-encryption.md). Instruktioner för hur du tillhandahåller en krypterings-relaterad konfiguration finns i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Mer information om hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data som lagras i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1

Data Lake Storage Gen1 är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-miljön. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan om du vill veta mer om hur Data Lake Storage Gen1 kan användas både med komponenter med öppen källkod och andra Azure-tjänster.

* Se [Big data program med öppen källkod som fungerar med Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Se [integrera Azure Data Lake Storage gen1 med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) för att förstå hur du använder data Lake Storage gen1 med andra Azure-tjänster för att möjliggöra ett bredare utbud av scenarier.
* Se [använda Azure Data Lake Storage gen1 för Big data-krav](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fil system (adl://)

I Hadoop-miljöer kan du komma åt Data Lake Storage Gen1 via det nya fil systemet, AzureDataLakeFilesystem (adl://). Prestanda för program och tjänster som använder `adl://` kan optimeras på ett sätt som inte är tillgängligt i WebHDFS. Därför får du flexibiliteten att antingen använda bästa möjliga prestanda genom att använda den rekommenderade adl://. Eller underhåll befintlig kod genom att fortsätta att använda WebHDFS-API: et direkt. Azure HDInsight drar full nytta av AzureDataLakeFilesystem för att ge bästa möjliga prestanda på Data Lake Storage Gen1.

Få åtkomst till dina data i Data Lake Storage Gen1 med hjälp av följande URI:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [åtgärder som är tillgängliga på lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen2-översikt](./overview-data-lake-storage-gen2.md)