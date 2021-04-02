---
title: Fråga Avro data genom att använda Azure Data Lake Analytics | Microsoft Docs
description: Använd egenskaper för meddelande text för att dirigera telemetri till Blob Storage och fråga Avro format-data som skrivs till Blob Storage.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 3cfe75edcf338f5248baf396147a5b77803fbfb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655942"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Fråga Avro data med hjälp av Azure Data Lake Analytics

Den här artikeln beskriver hur du frågar Avro-data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster. Med [meddelanderoutning](iot-hub-devguide-messages-d2c.md) kan du filtrera data med hjälp av omfattande frågor baserat på meddelande egenskaper, meddelande text, enhetens dubbla Taggar och enhetens dubbla egenskaper. Mer information om fråge funktionen i meddelanderoutning finns i artikeln om [meddelanderoutning för meddelanderoutning](iot-hub-devguide-routing-query-syntax.md).

Utmaningen har varit att när Azure IoT Hub dirigerar meddelanden till Azure Blob Storage, som standard IoT Hub skriver innehållet i Avro-format, som har både en meddelande text egenskap och en meddelande egenskap. Avro-formatet används inte för andra slut punkter. Även om Avro-formatet är bra för data och meddelande bevarat, är det en utmaning att använda den för att fråga efter data. I jämförelse är JSON-eller CSV-formatet mycket enklare för att fråga efter data. IoT Hub stöder nu skrivning av data till Blob Storage i JSON samt AVRO.

Mer information finns i [använda Azure Storage som en Dirigerings slut punkt](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

Du kan använda många av de stora data mönstren för att hantera data som inte är relationella för stora data och format och lösa detta genom att använda många av de stora data mönstren för båda omvandlings-och skalnings data. Ett av mönstren, "betala per fråga", är Azure Data Lake Analytics, vilket är fokus för den här artikeln. Även om du enkelt kan köra frågan i Hadoop eller andra lösningar är Data Lake Analytics ofta bättre lämpad för den här metoden "betala per fråga".

Det finns en "Extractor" för Avro i U-SQL. Mer information finns i [exempel på U-SQL-Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Fråga och exportera Avro-data till en CSV-fil

I det här avsnittet frågar du Avro-data och exporterar dem till en CSV-fil i Azure Blob Storage, men du kan enkelt placera data i andra databaser eller data lager.

1. Konfigurera Azure IoT Hub för att dirigera data till en Azure Blob Storage-slutpunkt genom att använda en egenskap i meddelande texten för att välja meddelanden.

   ![Avsnittet anpassade slut punkter](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Routningsregler](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Mer information om inställningar för Routning och anpassade slut punkter finns i [meddelanderoutning för en IoT Hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Kontrol lera att enheten har kodning, innehålls typ och nödvändiga data antingen i egenskaperna eller i meddelande texten, som det hänvisas till i produkt dokumentationen. När du visar dessa attribut i Device Explorer, som visas här, kan du kontrol lera att de är korrekt inställda.

   ![Data rutan för Händelsehubben](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Konfigurera en Azure Data Lake Store instans och en Data Lake Analytics instans. Azure IoT Hub dirigerar inte till en Data Lake Store instans, men en Data Lake Analytics instans kräver en.

   ![Data Lake Store och Data Lake Analytics instanser](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. I Data Lake Analytics konfigurerar du Azure Blob Storage som en ytterligare lagring, samma blob-lagring som Azure IoT Hub dirigerar data till.

   ![Fönstret data källor](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Som beskrivs i [U-SQL-Avro-exemplet](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)måste du ha fyra DLL-filer. Överför filerna till en plats i Data Lake Store-instansen.

   ![Fyra uppladdade DLL-filer](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Skapa ett U-SQL-projekt i Visual Studio.

   ![Skapa ett U-SQL-projekt](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Klistra in innehållet i följande skript i den nya filen. Ändra de tre markerade avsnitten: ditt Data Lake Analytics-konto, de tillhör ande Sök vägarna för DLL-filen och rätt sökväg för ditt lagrings konto.

   ![De tre avsnitten som ska ändras](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Det faktiska U-SQL-skriptet för enkel utdata till en CSV-fil:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Det tog Data Lake Analytics fem minuter att köra följande skript, vilket var begränsat till 10 analys enheter och bearbetade 177-filer. Resultatet visas i CSV-filens utdata som visas i följande bild:

    ![Resultat av utdata till CSV-fil](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Utdata konverterad till CSV-fil](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Fortsätt till steg 8 för att parsa JSON.

8. De flesta IoT-meddelanden är i JSON-filformat. Genom att lägga till följande rader kan du parsa meddelandet i en JSON-fil, där du kan lägga till WHERE-satserna och endast mata ut data som behövs.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    Utdata visar en kolumn för varje objekt i `SELECT` kommandot.

    ![Utdata som visar en kolumn för varje objekt](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du frågar Avro data för att effektivt dirigera meddelanden från Azure IoT Hub till Azure-tjänster.

Exempel på kompletta lösningar från slut punkt till slut punkt som använder IoT Hub finns i [dokumentationen för Azure IoT Solution acceleratorer](../iot-accelerators/index.yml).

Mer information om hur du utvecklar lösningar med IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).

Mer information om meddelanderoutning i IoT Hub finns i [skicka och ta emot meddelanden med IoT Hub](iot-hub-devguide-messaging.md).
