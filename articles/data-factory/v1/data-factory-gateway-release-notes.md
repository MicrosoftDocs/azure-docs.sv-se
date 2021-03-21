---
title: Viktig information för gateway för datahantering
description: Viktig information om Data Management Gateway Tory
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b5bf0c6d6996a67e8b0fec3ec51e31fef8eedf26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363924"
---
# <a name="release-notes-for-data-management-gateway"></a>Viktig information för gateway för datahantering
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [integration runtime med egen värd i v2](../create-self-hosted-integration-runtime.md).

En av utmaningarna för modern data integrering är att flytta data till och från lokalt till molnet. Data Factory gör denna integrering med Data Management Gateway, som är en agent som du kan installera lokalt för att aktivera hybrid data förflyttning.

Se följande artiklar för detaljerad information om Data Management Gateway och hur du använder det:

*  [Gateway för datahantering](data-factory-data-management-gateway.md)
*  [Flytta data mellan lokalt och molnet med hjälp av Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>AKTUELL VERSION 
Det går inte att uppdatera viktig information här. Hämta senaste versions information [här](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Tidigare versioner
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Funktioner
- Du kan lägga till DNS-poster för att tillåta Service Bus i stället för att tillåta alla Azure IP-adresser från din brand vägg (om det behövs). Du kan hitta respektive DNS-post på Azure Portal (Data Factory-> "författare och distribuera"-> "gateways"-> "serviceUrls" (i JSON)
- HDFS Connector stöder nu självsignerat offentligt certifikat genom att låta dig hoppa över TLS-verifiering.
- Åtgärdat: problem med Gateway offline under uppdateringen (på grund av klock skevning)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Funktioner
-    Du kan lägga till DNS-poster för att tillåta Service Bus snarare än att tillåta alla Azure IP-adresser från din brand vägg (om det behövs). Mer information finns här.
-    Nu kan du kopiera data till/från en enda Block-Blob upp till 4,75 TB, vilket är den högsta tillåtna storleken för Block-Blob. (den tidigare gränsen var 195 GB).
-    Fast: problem med slut på minne när flera små filer har zippats under kopierings aktiviteten.
-    Fixed: index out of Range-problem vid kopiering från dokument databas till SQL Server med idempotens-funktionen.
-    Fast: SQL Cleanup-skriptet fungerar inte med SQL Server från guiden Kopiera.
-    Fast: kolumn namn med blank steg i slutet fungerar inte i kopierings aktiviteten.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Funktioner
- Åtgärdat: problem med autentiseringsuppgifter som saknas vid omstart av gateway-datorn.
- Åtgärdat: problem med registrering under Gateway-återställning med en säkerhets kopia.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Funktioner
- Fast: felaktig läsning av decimalt null-värde från Oracle som källa.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nyheter
- Kunder kan ge feedback om registreringen av gatewayen.
- Stöd för ett nytt komprimerings format: ZIP (DEFLATE)

### <a name="enhancements-"></a>Funktioner
- Prestanda förbättring för Oracle-Sink, HDFS-källa.
- Fel korrigering för automatisk uppdatering av gateway, parallell processor kapacitet för gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Förbättringar 
- Förbättrad och robust Gateway-registrerings upplevelse – nu kan du spåra status status under registreringen av Gateway-registreringen, vilket gör registrerings upplevelsen mer effektiv.
- Förbättrad i Gateway-återställning – du kan fortfarande återställa Gateway även om du inte har säkerhets kopian av Gateway-filen med den här uppdateringen. Detta kräver att du återställer länkade autentiseringsuppgifter för tjänsten i portalen.
- Fel korrigering.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nyheter

- Nu kan du lagra autentiseringsuppgifter för data källa lokalt. Autentiseringsuppgifterna är krypterade. Autentiseringsuppgifterna för data källan kan återställas och återställas med hjälp av den säkerhets kopia som kan exporteras från den befintliga gatewayen, allt lokalt.

### <a name="enhancements-"></a>Funktioner

- Förbättrad och robust Gateway-registrerings upplevelse.
- Stöd för automatisk identifiering av QuoteChar-konfiguration för text format i guiden Kopiera och förbättra den övergripande format identifieringen.

## <a name="2361002"></a>2.3.6100.2

- Stöd för firstRowAsHeader och SkipLineCount automatisk identifiering i kopierings guiden för textfiler i lokalt fil system och HDFS.
- Förbättra stabiliteten hos nätverks anslutningar mellan gateway och Service Bus
- Några fel korrigeringar


## <a name="2260721"></a>2.2.6072.1

*  Har stöd för att ange HTTP-proxy för gatewayen med hjälp av Gateway-Configuration Manager. Om den är konfigurerad, kan Azure-Blob, Azure-tabell, Azure Data Lake och dokument databas nås via HTTP-proxy.
*  Stöder rubrik hantering för text format vid kopiering av data från/till Azure Blob, Azure Data Lake Store, lokalt fil system och lokal HDFS.
*  Stöder kopiering av data från tilläggs-blob och Page BLOB tillsammans med den Block-Blob som redan stöds.
*  Introducerar en ny Gateway-status **online (begränsad)**, som anger att gatewayens huvud funktioner fungerar förutom det interaktiva åtgärds stödet för kopierings guiden.
*  Förbättrar robustheten för gateway-registrering med registrerings nyckeln.

## <a name="216040"></a>2.1.6040.

*  DB2-drivrutinen ingår nu i gatewayens installations paket. Du behöver inte installera det separat.
*  DB2-drivrutinen stöder nu z/OS och DB2 för i (som/400) tillsammans med de plattformar som redan stöds (Linux, UNIX och Windows).
*  Stöder användning av Azure Cosmos DB som källa eller mål för lokala data lager
*  Har stöd för kopiering av data från/till kall/snabb blob-lagring tillsammans med det redan stödda allmänna lagrings kontot.
*  Gör att du kan ansluta till SQL Server via gateway med behörighet för fjärrinloggning.  

## <a name="2060131"></a>2.0.6013.1

*  Du kan välja språk/kultur som ska användas av en gateway under manuell installation.

*  När gatewayen inte fungerar som förväntat, kan du välja att skicka Gateway-loggar med de senaste sju dagarna till Microsoft för att under lätta fel sökningen av problemet. Om gatewayen inte är ansluten till moln tjänsten kan du välja att spara och arkivera Gateway-loggar.  

*  Förbättringar av användar gränssnittet för gateway Configuration Manager:

    *  Gör Gateway-statusen mer synlig på fliken Start.

    *  Organisera om och förenklade kontroller.

    *  Du kan kopiera data från en lagrings plats med [verktyget kod fritt kopiering](data-factory-copy-data-wizard-tutorial.md). Se [mellanlagrad kopia](data-factory-copy-activity-performance.md#staged-copy) för information om den här funktionen i allmänhet.
*  Du kan använda Data Management Gateway för att ingressa data direkt från en SQL Server-databas till Azure Machine Learning.

*  Prestandaförbättringar

    * Förbättra prestanda för visning av schema/för hands version mot SQL Server i kod fritt kopierings verktyg.

## <a name="11259531"></a>1.12.5953.1

*  Felkorrigeringar

## <a name="11159181"></a>1.11.5918.1

*  Den maximala storleken på händelse loggen för gatewayen har ökat från 1 MB till 40 MB.

*  En varnings dialog ruta visas om en omstart krävs vid automatisk uppdatering av gateway. Du kan välja att starta om höger eller senare.

*  Om automatisk uppdatering Miss lyckas försöker Gateway Installer att automatiskt uppdatera tre gånger till maximalt.

*  Prestandaförbättringar

    * Förbättra prestanda för att läsa in stora tabeller från den lokala servern i ett kod fritt kopierings scenario.

*  Felkorrigeringar

## <a name="11058921"></a>1.10.5892.1

*  Prestandaförbättringar

*  Felkorrigeringar

## <a name="1958652"></a>1.9.5865.2

*  Ingen funktion för automatisk uppdatering av touch
*  Ikon för nytt fack med status indikatorer för gateway
*  Möjlighet att "Uppdatera nu" från klienten
*  Möjlighet att ange tid för uppdaterings schema
*  PowerShell-skript för att aktivera/inaktivera automatisk uppdatering
*  Stöd för JSON-format  
*  Prestandaförbättringar
*  Felkorrigeringar

## <a name="1858221"></a>1.8.5822.1

*  Förbättra fel söknings upplevelsen
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757951"></a>1.7.5795.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1757641"></a>1.7.5764.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1657351"></a>1.6.5735.1

*  Stöd för lokal HDFS-källa/-mottagare
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656961"></a>1.6.5696.1

*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656761"></a>1.6.5676.1

*  Stöd för diagnostikverktyg på Configuration Manager
*  Stöd tabell kolumner för tabell data källor för Azure Data Factory
*  Stöd för Azure Synapse Analytics för Azure Data Factory
*  Stöd för reclusive i BlobSource och FileSource för Azure Data Factory
*  Support CopyBehavior – MergeFiles, PreserveHierarchy och FlattenHierarchy i BlobSink och FileSink med binär kopia för Azure Data Factory
*  Stöd för kopierings aktivitets rapporterings förlopp för Azure Data Factory
*  Stöd för anslutnings validering av data källa för Azure Data Factory
*  Felkorrigeringar

### <a name="1656721"></a>1.6.5672.1

*  Support tabell namn för ODBC-datakälla för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1656581"></a>1.6.5658.1

*  Stöd fil mottagare för Azure Data Factory
*  Stöd för att bevara hierarkin i binär kopia för Azure Data Factory
*  Stöd för kopierings aktivitet idempotens för Azure Data Factory
*  Felkorrigeringar

### <a name="1656401"></a>1.6.5640.1

*  Stöd 3 fler data källor för Azure Data Factory (ODBC, OData, HDFS)
*  Support citat tecken i CSV-parser för Azure Data Factory
*  Komprimerings stöd (BZip2)
*  Felkorrigeringar

### <a name="1556121"></a>1.5.5612.1

*  Stöd för fem Relations databaser för Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata och Sybase)
*  Komprimerings stöd (gzip och DEFLATE)
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1455491"></a>1.4.5549.1

*  Lägg till stöd för Oracle-datakälla för Azure Data Factory
*  Prestandaförbättringar
*  Felkorrigeringar

### <a name="1454921"></a>1.4.5492.1

*  Enhetlig binär som stöder både Microsoft Azure Data Factory och Office 365 Power BI-tjänster
*  Förfina konfigurations gränssnittet och registrerings processen
*  Azure Data Factory – stöd för ingångs-och utgångs stöd i Azure för SQL Server data Källa

### <a name="1253031"></a>1.2.5303.1

*  Åtgärda timeout-problem för att stödja mer tids krävande data källans lutningar.

### <a name="1155268"></a>1.1.5526.8

*  Kräver .NET Framework 4.5.1 som ett krav under installationen.

### <a name="1051442"></a>1.0.5144.2

*  Inga ändringar som påverkar Azure Data Factory scenarier.
