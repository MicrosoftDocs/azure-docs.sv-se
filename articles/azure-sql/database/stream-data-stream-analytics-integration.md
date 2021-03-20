---
title: Strömma data med Azure Stream Analytics integration (för hands version)
description: Använd Azure Stream Analytics-integrering för att strömma data till Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454025"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Strömma data till Azure SQL Database med Azure Stream Analytics integration (för hands version)

Användarna kan nu mata in, bearbeta, Visa och analysera real tids strömnings data i en tabell direkt från en databas i Azure SQL Database. De gör det i Azure Portal som använder [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md). Den här upplevelsen möjliggör en mängd olika scenarier, till exempel ansluten bil, fjärrövervakning, bedrägeri identifiering och många fler. I Azure Portal kan du välja en händelse källa (Event Hub/IoT Hub), Visa inkommande real tids händelser och välja en tabell för att lagra händelser. Du kan också skriva Azure Stream Analytics frågor om språk frågor i portalen för att transformera inkommande händelser och lagra dem i den valda tabellen. Den nya start punkten är förutom de skapande-och konfigurations upplevelser som redan finns i Stream Analytics. Den här upplevelsen börjar från databasens kontext, så att du snabbt kan skapa ett Stream Analytics jobb och navigera sömlöst mellan databasen i Azure SQL Database och Stream Analytics upplevelser.

![Stream Analytics flöde](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>Viktiga fördelar

- Minsta kontext växling: du kan starta från en databas i Azure SQL Database i portalen och börja mata in real tids data i en tabell utan att växla till någon annan tjänst.
- Minskat antal steg: kontexten för din databas och tabell används för att förkonfigurera ett Stream Analytics jobb.
- Enklare att använda med förhands gransknings data: Förhandsgranska inkommande data från händelse källan (Händelsehubben/IoT Hub) i kontexten för den valda tabellen

> [!IMPORTANT]
> Ett Azure Stream Analytics jobb kan skriva ut till Azure SQL Database, Azure SQL-hanterad instans eller Azure Synapse Analytics. Mer information finns i [utdata](../../stream-analytics/stream-analytics-define-outputs.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln behöver du följande resurser:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).
- En databas i Azure SQL Database. Mer information finns i [skapa en enskild databas i Azure SQL Database](single-database-create-quickstart.md).
- En brand Väggs regel som gör att datorn kan ansluta till servern. Mer information finns i [skapa en brand Väggs regel på server nivå](firewall-create-server-level-portal-quickstart.md).

## <a name="configure-stream-analytics-integration"></a>Konfigurera Stream Analytics-integrering

1. Logga in på Azure-portalen.
2. Navigera till databasen där du vill mata in strömmande data. Välj **Stream Analytics (för hands version)**.

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. Om du vill börja mata in dina strömmande data i den här databasen väljer du **skapa** och ger ditt strömnings jobb ett namn och väljer sedan **Nästa: indata**.

    ![Konfigurera grunderna för Stream Analytics jobb](./media/stream-data-stream-analytics-integration/create-job.png)

4. Ange information om händelse källan och välj sedan **Nästa: utdata**.

   - **Indatatyp**: Event Hub/IoT Hub
   - **Indataområde**: Ange ett namn för att identifiera din händelse källa
   - **Prenumeration**: samma som Azure SQL Database prenumeration
   - **Event Hub-namn område**: namn på namn område
   - **Event Hub-namn**: namnet på händelsehubben i det valda namn området
   - **Princip namn för Event Hub** (standard för att skapa ny): Ange ett princip namn
   - **Konsument grupp för Event Hub** (standard för att skapa ny): ge ett konsument grupp namn  

      Vi rekommenderar att du skapar en konsument grupp och en princip för varje nytt Azure Stream Analytics jobb som du skapar härifrån. Konsument grupper tillåter endast fem samtidiga läsare, så att en dedikerad konsument grupp för varje jobb kommer att undvika eventuella fel som kan uppstå från att överskrida den gränsen. Med en dedikerad princip kan du rotera nyckeln eller återkalla behörigheter utan att påverka andra resurser.

     ![Konfigurera Stream Analytics jobbets utdata](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. Välj vilken tabell som du vill mata in strömmande data till. När du är färdig väljer du **skapa**.

   - **Användar namn**, **lösen ord**: Ange dina autentiseringsuppgifter för SQL Server-autentisering. Välj **Verifiera**.
   - **Tabell**: Välj **Skapa ny** eller **Använd befintlig**. I det här flödet väljer vi **skapa**. När du startar Stream Analytics-jobbet skapas en ny tabell.

     ![Skapa Stream Analytics jobb](./media/stream-data-stream-analytics-integration/create.png)

6. En frågenod öppnas med följande information:

   - Din **indata** (källa för indata-händelser) från vilken du matar in data  
   - Din **utmatning** (utdataparameter) som kommer att lagra transformerade data
   - Exempel på [SAQL-fråga](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) med SELECT-instruktion.
   - **Förhands granskning av indata**: visar ögonblicks bilder av senaste inkommande data från källan för indata.
     - Serialiserings typen i dina data identifieras automatiskt (JSON/CSV). Du kan manuellt ändra den och JSON/CSV/AVRO.
     - Du kan förhandsgranska inkommande data i tabell format eller RAW-format.
     - Om data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna.
     - Välj **Select Time-intervall** om du vill testa frågan mot ett speciellt tidsintervall inkommande händelser.
     - Välj **Ladda upp exempel indata** för att testa frågan genom att ladda upp en JSON/CSV-fil. Mer information om hur du testar en SAQL-fråga finns i [testa ett Azure Stream Analytics jobb med exempel data](../../stream-analytics/stream-analytics-test-query.md).

     ![Testa fråga](./media/stream-data-stream-analytics-integration/test-query.png)

   - **Test resultat**: Välj **test fråga** och du kan se resultatet av din strömnings fråga

     ![test resultat](./media/stream-data-stream-analytics-integration/test-results.png)

   - **Schema för test resultat**: visar schemat för resultatet av din strömnings fråga efter testning. Kontrol lera att resultatet av test resultatet är detsamma som i schemat för utdata.

     ![schema för test resultat](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **Utdata schema**: det här innehåller schemat för den tabell som du valde i steg 5 (ny eller befintlig).

      - Skapa nytt: om du har valt det här alternativet i steg 5, ser du inte schemat ännu förrän du startar direkt uppspelnings jobbet. När du skapar en ny tabell väljer du lämpligt tabell index. Mer information om tabell indexering finns i [klustrade och inklustrade index som beskrivs](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/).
      - Använd befintlig: om du har valt det här alternativet i steg 5 visas schemat för den valda tabellen.

7. När du är klar med redigeringen & du testar frågan väljer du **Spara fråga**. Välj **start Stream Analytics jobb** för att börja mata in transformerade data i SQL-tabellen. När du har slutfört följande fält **startar** du jobbet.
   - **Start tid för utdata**: Detta definierar tiden för jobbets första utdata.  
     - Nu: jobbet kommer att startas nu och bearbeta nya inkommande data.
     - Anpassad: jobbet kommer att startas nu men kommer att bearbeta data från en viss tidpunkt (som kan vara i det förflutna eller framtiden). Mer information finns i [så här startar du ett Azure Stream Analytics jobb](../../stream-analytics/start-job.md).
   - **Enheter för strömning**: Azure Stream Analytics priss ätts efter antalet enheter för strömning som krävs för att bearbeta data till tjänsten. Mer information finns i [Azure Stream Analytics prissättning](https://azure.microsoft.com/pricing/details/stream-analytics/).
   - **Fel hantering av utgående data**:  
     - Försök igen: när ett fel inträffar skriver Azure Stream Analytics försök att skriva händelsen oändligt tills skrivningen lyckas. Det finns ingen tids gräns för återförsök. Till sist blockeras alla efterföljande händelser från bearbetningen av händelsen som försöker igen. Det här alternativet är standard principen för fel hantering av utdata.
     - Drop: Azure Stream Analytics ignorerar eventuella utdata-händelser som resulterar i ett data konverterings fel. De ignorerade händelserna kan inte återställas senare. Alla tillfälliga fel (t. ex. nätverks fel) görs nya försök oavsett princip konfigurationen för hantering av utdata.
   - **SQL Database inställningar för utdata**: ett alternativ för att ärva partitionerings schema i föregående fråga-steg för att aktivera helt parallell topologi med flera skrivare till tabellen. Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](../../stream-analytics/stream-analytics-sql-output-perf.md).
   - **Max antal batchar**: den rekommenderade övre gränsen för antalet poster som skickas med varje Mass infognings transaktion.  
    Mer information om fel hantering av utdata finns [i principer för utgående fel i Azure Stream Analytics](../../stream-analytics/stream-analytics-output-error-policy.md).  

     ![starta jobb](./media/stream-data-stream-analytics-integration/start-job.png)

8. När du har startat jobbet visas jobbet som körs i listan och du kan utföra följande åtgärder:
   - **Starta/stoppa jobbet**: om jobbet körs kan du avbryta jobbet. Om jobbet har stoppats kan du starta jobbet.
   - **Redigera jobb**: du kan redigera frågan. Om du vill göra fler ändringar i jobbet kan du lägga till fler indata/utdata och sedan öppna jobbet i Stream Analytics. Redigerings alternativet inaktive ras när jobbet körs.
   - **Förhandsgranska utdata tabell**: du kan förhandsgranska tabellen i SQL-Frågeredigeraren.
   - **Öppna i Stream Analytics**: öppna jobbet i Stream Analytics om du vill visa övervakning, felsöka information om jobbet.

     ![Stream Analytics-jobb](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure Stream Analytics](../../stream-analytics/index.yml)
- [Azure Stream Analytics-lösningsmönster](../../stream-analytics/stream-analytics-solution-patterns.md)
