---
title: Självstudie – anpassade .NET-deserialiserare för Azure Stream Analytics moln jobb
description: Den här självstudien visar hur du skapar en anpassad .NET-deserialiserare för ett Azure Stream Analytics moln jobb med Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018215"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Självstudie: anpassade .NET-deserialiserare för Azure Stream Analytics

Azure Stream Analytics har [inbyggt stöd för tre data format](stream-analytics-parsing-json.md): JSON, CSV och Avro. Med anpassade .NET-deserialiserare kan du läsa data från andra format, till exempel [protokollets buffert](https://developers.google.com/protocol-buffers/), [obligations](https://github.com/Microsoft/bond) -och andra användardefinierade format för både moln-och Edge-jobb.

Den här självstudien visar hur du skapar en anpassad .NET-deserialiserare för ett Azure Stream Analytics moln jobb med Visual Studio. Information om hur du skapar .NET-deserialiserare i Visual Studio Code finns i [skapa .net-deserialiserare för Azure Stream Analytics jobb i Visual Studio Code](visual-studio-code-custom-deserializer.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad deserialiserare för en Protocol-buffert.
> * Skapa ett Azure Stream Analytics jobb i Visual Studio.
> * Konfigurera Stream Analytics-jobbet så att det använder den anpassade deserialiseraren.
> * Kör ditt Stream Analytics jobb lokalt för att testa och felsöka den anpassade deserialiseraren.


## <a name="prerequisites"></a>Förutsättningar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Installera [Visual studio 2019 (rekommenderas)](https://www.visualstudio.com/downloads/) eller [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/). Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Express-versionen stöds inte. 

* [Installera Stream Analytics verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) eller uppdatera till den senaste versionen. 

* Öppna **Cloud Explorer** i Visual Studio och logga in på din Azure-prenumeration.

* Skapa en behållare i ditt Azure Storage-konto.
Den behållare som du skapar kommer att användas för att lagra till gångar som är relaterade till ditt Stream Analytics jobb. Om du redan har ett lagringskonto med befintliga containrar kan du använda dem. Annars måste du [skapa en ny container](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Skapa en anpassad deserialiserare

1. Öppna Visual Studio och välj **fil > nytt > projekt**. Sök efter **Stream Analytics** och välj **Azure Stream Analytics anpassat deserialiserat projekt (.net)**. Ge projektet ett namn, t. ex. **protobuf deserialiserare**.

   ![Skapa Visual Studio dotNet standard klass biblioteks projekt](./media/custom-deserializer/create-dotnet-library-project.png)

2. I Solution Explorer högerklickar du på projektet **protobuf deserialiserare** och väljer **Hantera NuGet-paket** på menyn. Installera sedan paketen **Microsoft. Azure. StreamAnalytics** och **Google. protobuf** NuGet.

3. Lägg till [klassen MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) och [MessageBodyDeserializer-klassen](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) i projektet.

4. Bygg **protobuf deserialiserar** -projektet.

## <a name="add-an-azure-stream-analytics-project"></a>Lägg till ett Azure Stream Analytics-projekt

1. I Solution Explorer högerklickar du på lösningen **protobuf deserialiserare** och väljer **Lägg till > nytt projekt**. Under **Azure Stream Analytics > Stream Analytics** väljer du **Azure Stream Analytics program**. Ge den namnet **ProtobufCloudDeserializer** och välj **OK**. 

2. Högerklicka på **referenser** under **ProtobufCloudDeserializer** Azure Stream Analytics-projektet. Lägg till **protobuf deserialiserare** under **projekt**. Den bör fyllas i automatiskt åt dig.

## <a name="configure-a-stream-analytics-job"></a>Konfigurera ett Stream Analytics jobb

1. Dubbelklicka på **JobConfig.jspå**. Använd standard konfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Resurs för globala lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagrings inställningar| < prenumerationen >|
   |Lagrings konto för globala lagrings inställningar| < ditt lagrings konto >|
   |Resurs för anpassade kod lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Lagrings konto för anpassade kod lagrings inställningar|< ditt lagrings konto >|
   |Behållare för lagrings inställningar för anpassade koder|< lagrings container >|

2. Under **indata** dubbelklickar du på **Input.jspå**. Använd standard konfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Källa|Blob Storage|
   |Resurs|Välj datakälla från det aktuella kontot|
   |Prenumeration|< prenumerationen >|
   |Lagringskonto|< ditt lagrings konto >|
   |Container|< lagrings container >|
   |Format för händelse serialisering|Annat (protobuf, XML, patentskyddat...)|
   |Resurs|Läsa in från ASA projekt referens eller CodeBehind|
   |Sammansättnings namn för CSharp|ProtobufDeserializer.dll|
   |Klassnamn|MessageBodyProto.MessageBodyDeserializer|
   |Händelse komprimerings typ|Inget|

3. Lägg till följande fråga i filen **script. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Hämta [exempel filen protobuf indatafilen](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). I mappen **indata** högerklickar du på **Input.jspå** och väljer **Lägg till lokal inmatning**. Dubbelklicka sedan på **local_Input.jspå** och konfigurera följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Indataalias|Indata|
   |Källtyp|Dataström|
   |Format för händelse serialisering|Annat (protobuf, XML, patentskyddat...)|
   |Sammansättnings namn för CSharp|ProtobufDeserializer.dll|
   |Klassnamn|MessageBodyProto.MessageBodyDeserializer|
   |Sökväg till lokal indatafil|< fil Sök vägen för den hämtade indatafilen för protobuf>|

## <a name="execute-the-stream-analytics-job"></a>Köra Stream Analytics jobbet

1. Öppna **script. asaql** och välj **Kör lokalt**.

2. Observera resultaten i **Stream Analytics lokala körnings resultat**.

Du har implementerat en anpassad deserialiserare för ditt Stream Analyticss jobb! I den här självstudien testade du den anpassade Avserialiseringen lokalt. För dina faktiska data konfigurerar du både indata och utdata på rätt sätt. Skicka sedan jobbet till Azure från Visual Studio för att köra jobbet i molnet med hjälp av den anpassade deserialiseraren som du nyss implementerade.

## <a name="debug-your-deserializer"></a>Felsöka deserialiseraren

Du kan felsöka .NET deserialiseraren lokalt på samma sätt som du felsöker standard-.NET-kod.

1. Högerklicka på **ProtobufCloudDeserializer** projekt namn och ange det som start projekt.

2. Lägg till Bryt punkter i din funktion.

3. Starta felsökningen genom att trycka på **F5**. Programmet kommer att avbrytas vid dina brytpunkter som förväntat.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i självstudien med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du implementerar en anpassad .NET-deserialiserare för protokollets buffert för indataport. Om du vill veta mer om hur du skapar anpassade deserialiserare fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Skapa olika .NET-deserialiserare för Azure Stream Analytics jobb](custom-deserializer-examples.md)