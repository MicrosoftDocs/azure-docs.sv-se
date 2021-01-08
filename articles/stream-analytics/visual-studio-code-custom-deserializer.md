---
title: Skapa anpassade .NET-deserialiserare för Azure Stream Analytics moln jobb med Visual Studio Code
description: Den här självstudien visar hur du skapar en anpassad .NET-deserialiserare för ett Azure Stream Analytics moln jobb med Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013931"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Skapa anpassade .NET-deserialiserare för Azure Stream Analytics i Visual Studio Code

Azure Stream Analytics har [inbyggt stöd för tre data format](stream-analytics-parsing-json.md): JSON, CSV och Avro. Med anpassade .NET-deserialiserare kan du läsa data från andra format, t. ex. [protokollets buffert](https://developers.google.com/protocol-buffers/), [obligation](https://github.com/Microsoft/bond) och andra användardefinierade format för moln jobb.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Anpassade .NET-deserialiserare i Visual Studio Code

Du kan skapa, testa och felsöka en anpassad .NET-deserialiserare för ett Azure Stream Analytics moln jobb med Visual Studio Code.

### <a name="prerequisites"></a>Förutsättningar

* Installera [.net Core SDK](https://dotnet.microsoft.com/download) och starta om Visual Studio Code.

* Använd den här [snabb](quick-create-visual-studio-code.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Skapa en anpassad deserialiserare

1. Öppna en Terminal och kör följande kommando för att skapa ett .NET-klass bibliotek i Visual Studio Code för din anpassade deserialiserare som kallas **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Gå till **ProtobufDeserializer** -projekt katalogen och installera paketet [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) och [Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet.

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Lägg till [klassen MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) och [MessageBodyDeserializer-klassen](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) i projektet.

4. Bygg **ProtobufDeserializer** -projektet.

### <a name="add-an-azure-stream-analytics-project"></a>Lägg till ett Azure Stream Analytics-projekt

1. Öppna Visual Studio Code och välj **Ctrl + Shift + P** för att öppna kommando paletten. Ange sedan ASA och Select **ASA: skapa nytt projekt**. Ge den namnet **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Konfigurera ett Stream Analytics jobb

1. Dubbelklicka på **JobConfig.jspå**. Använd standard konfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Resurs för globala lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagrings inställningar| < prenumerationen >|
   |Lagrings konto för globala lagrings inställningar| < ditt lagrings konto >|
   |Lagrings konto för CustomCodeStorage-inställningar|< ditt lagrings konto >|
   |Behållare för CustomCodeStorage-inställningar|< lagrings container >|

2. Under **indata** -mappen öppnas **input.jspå**. Välj **Lägg till Live-indata** och Lägg till indata från Azure Data Lake Storage Gen2/Blob Storage, Välj **Välj från din Azure-prenumeration**. Använd standard konfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Namn|Indata|
   |Prenumeration|< prenumerationen >|
   |Lagringskonto|< ditt lagrings konto >|
   |Container|< lagrings container >|
   |Typ av serialisering|Välj **anpassad**|
   |SerializationProjectPath|Välj **Välj biblioteks projekt Sök väg** från CodeLens och välj det **ProtobufDeserializer** biblioteks projekt som skapades i föregående avsnitt. Välj **build-projekt** för att bygga projektet|
   |SerializationClassName|Välj **Välj avserialiserings klass** från CodeLens för att automatiskt fylla i klass namn och DLL-sökväg|
   |Klassnamn|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Lägg till anpassad deserialiserad-indatamängd.":::

3. Lägg till följande fråga i filen **ProtobufCloudDeserializer. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Hämta [exempel filen protobuf indatafilen](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). I mappen **indata** högerklickar du på **input.jspå** och väljer **Lägg till lokal inmatning**. Dubbelklicka sedan på **local_input1.jspå** och Använd standard konfigurationerna, förutom följande inställningar.

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Välj lokal fil Sök väg|Klicka på CodeLens för att välja < fil Sök vägen för den hämtade indatafilen för exempel protobuf>|

### <a name="execute-the-stream-analytics-job"></a>Köra Stream Analytics jobbet

1. Öppna **ProtobufCloudDeserializer. asaql** och välj **Kör lokalt** från CodeLens och välj sedan **Använd lokal indatamängd** i list rutan.

2. Observera fliken resultat på fliken **resultat** i vyn jobb diagram till höger. Du kan också klicka på stegen i jobb diagrammet om du vill visa mellanliggande resultat. Mer information hittar du i [Felsöka lokalt med hjälp av jobb diagram](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Kontrol lera det lokala körnings resultatet.":::

Du har implementerat en anpassad deserialiserare för ditt Stream Analyticss jobb! I den här självstudien testade du den anpassade Avserialiseringen lokalt med lokala indata. Du kan också testa det [med indata från Real tid i molnet](visual-studio-code-local-run-live-input.md). För att köra jobbet i molnet kan du konfigurera indata och utdata på rätt sätt. Skicka sedan jobbet till Azure från Visual Studio Code för att köra jobbet i molnet med hjälp av den anpassade deserialiseraren som du nyss implementerade.

### <a name="debug-your-deserializer"></a>Felsöka deserialiseraren

Du kan felsöka .NET deserialiseraren lokalt på samma sätt som du felsöker standard-.NET-kod. 

1. Lägg till Bryt punkter i .NET-funktionen.

2. Klicka på **Kör** från Visual Studio Code-aktivitets fältet och välj **skapa en launch.jspå filen**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Skapa start fil.":::

   Välj **ProtobufCloudDeserializer** och sedan **Azure Stream Analytics** i list rutan.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Skapa start fil 2.":::

   Redigera **launch.jspå** filen som ska ersättas <ASAScript> . asaql med ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Konfigurera Start fil.":::

3. Starta felsökningen genom att trycka på **F5**. Programmet kommer att avbrytas vid dina brytpunkter som förväntat. Detta fungerar för både lokala indata och indata från Real tid.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Felsök anpassad deserialiserare.":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i självstudien med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du implementerar en anpassad .NET-deserialiserare för protokollets buffert för indataport. Om du vill veta mer om hur du skapar anpassade deserialiserare fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> * [Skapa olika .NET-deserialiserare för Azure Stream Analytics jobb](custom-deserializer-examples.md)
> * [Testa Azure Stream Analytics jobb lokalt med Live-indatamängden med Visual Studio Code](visual-studio-code-local-run-live-input.md)