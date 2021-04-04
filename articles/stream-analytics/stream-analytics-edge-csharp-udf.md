---
title: Självstudie – Skriv C# användardefinierade funktioner för Azure Stream Analytics jobb i Visual Studio (för hands version)
description: Den här självstudien visar hur du skriver c#-användardefinierade funktioner för Stream Analytics jobb i Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020476"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Självstudie: skriva en C#-användardefinierad funktion för Azure Stream Analytics jobb (förhands granskning)

Med användardefinierade funktioner med C# (UDF skapas i Visual Studio) kan du utöka Azure Stream Analytics-frågespråket med dina egna funktioner. Du kan återanvända befintlig kod (inklusive DLL-filer) och använda matematiska eller komplex logik med C#. Det finns tre sätt att implementera UDF:er: CodeBehind-filer i ett Stream Analytics-projekt, UDF:er från ett lokalt C#-projekt eller UDF:er från ett befintligt paket från ett lagringskonto. Den här självstudien använder metoden CodeBehind för att implementera en grundläggande C#-funktion. UDF-funktionen för Stream Analytics jobb är för närvarande en för hands version och bör inte användas i produktions arbets belastningar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en användardefinierad funktion i C# med CodeBehind.
> * Testa ditt Stream Analytics jobb lokalt.
> * Publicera jobbet till Azure.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har slutfört följande krav:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Installera [Stream Analytics-verktygen för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) och arbetsbelastningen **Azure development** (Azure-utveckling) eller **Data Storage and Processing** (Datalagring och bearbetning).
* Ta en titt på den befintliga [Stream Analytics Edge Development guide](stream-analytics-tools-for-visual-studio-edge-jobs.md) om du skapar ett IoT Edge jobb.

## <a name="create-a-container-in-your-azure-storage-account"></a>Skapa en container i ditt Azure Storage-konto

Den behållare som du skapar kommer att användas för att lagra det kompilerade C#-paketet. Om du skapar ett Edge-jobb kommer det här lagrings kontot också att användas för att distribuera paketet till din IoT Edge-enhet. Använd en särskild container för varje Stream Analytics-jobb. Det går inte att använda samma container för flera Stream Analytics Edge-jobb. Om du redan har ett lagringskonto med befintliga containrar kan du använda dem. Annars måste du [skapa en ny container](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Skapa ett Stream Analytics-projekt i Visual Studio

1. Starta Visual Studio.

2. Välj **Arkiv > Nytt > Projekt**.

3. Välj **Stream Analytics** i listan mallar till vänster och välj sedan **Azure Stream Analytics Edge-program** eller **Azure Stream Analytics program**.

4.  Ange projektets **namn**, **plats** och **lösningsnamn**. Välj sedan **OK**.

    ![Skapa ett Azure Stream Analytics Edge-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Konfigurera sökvägen för sammansättningspaketet

1. Öppna Visual Studio och navigera till **Solution Explorer**.

2. Dubbelklicka på jobbets konfigurationsfil, `EdgeJobConfig.json`.

3. Expandera avsnittet **användardefinierade kod Configuration** avsnittet och fyll i konfigurationen med följande föreslagna värden:

   |**Inställning**|**Föreslaget värde**|
   |-------|---------------|
   |Resurs för globala lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagrings inställningar| < prenumerationen >|
   |Lagrings konto för globala lagrings inställningar| < ditt lagrings konto >|
   |Resurs för anpassade kod lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Lagrings konto för anpassade kod lagrings inställningar|< ditt lagrings konto >|
   |Behållare för lagrings inställningar för anpassade koder|< lagrings container >|


## <a name="write-a-c-udf-with-codebehind"></a>Skriv en C#-UDF med CodeBehind
En CodeBehind-fil är en C#-fil som associeras med ett enda ASA-frågeuttryck. Visual Studio-verktyg kommer automatiskt komprimera CodeBehind-filen och överföra den till Azure storage-kontot när den skickas. Alla klasser måste definieras som *offentliga* och alla objekt måste definieras som *statiska offentliga*.

1. I **Solution Explorer**, expandera **Script.asql** för att hitta CodeBehind-filen **Script.asaql.cs**.

2. Ersätt koden med följande exempel:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementera en användardefinerad funktion

1. I **Solution Explorer** öppnar du filen **Script.asaql**.

2. Ersätt den befintliga frågan med följande:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Lokal testning

1. Hämta [exempel data filen för temperatur simulatorn](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. I **Solution Explorer**, expandera **Indata**, högerklicka på **Input.json** och välj **Lägg till lokala indata**.

   ![Lägg till lokala indata till Stream Analytics-jobbet i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Ange den lokala indatafilens sökväg för de exempeldata som du hämtade och **spara**.

    ![Konfiguration för lokala indata till Stream Analytics-jobbet i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Klicka på **Kör lokalt** i Skriptredigeraren. När resultatet har sparats för lokal körning trycker du på valfri tangent för att se resultatet i tabellformat. 

    ![Köra Azure Stream Analytics-jobb lokalt med Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Du kan också välja **Öppna resultatmappen** för att se raw-filer i JSON- och CSV-format.

    ![Visa resultat från lokalt Azure Stream Analytics-jobb med Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Felsöka en UDF
Du kan felsöka dina C#-UDF:er lokalt på samma sätt som du skulle felsöka standard-C#-kod. 

1. Lägga till brytpunkter i en C#-funktion.

    ![Lägga till brytpunkter i en användardefinierad funktion för Stream Analytics i Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Starta felsökningen genom att trycka på **F5**. Programmet kommer att avbrytas vid dina brytpunkter som förväntat.

    ![Visa felsökningsresultat för användardefinierad funktion för Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publicera ditt jobb på Azure
När du har testat din fråga lokalt väljer du **skicka till Azure** i skriptredigeraren för att publicera projektet på Azure.

![Skicka in ditt Stream Analytics Edge-jobb till Azure från Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Distribuera till IoT Edge-enheter
Om du väljer att bygga ett Stream Analytics Edge-jobb kan det nu distribueras som en IoT Edge modul. Följ snabbstarten om [IoT Edge](../iot-edge/quickstart.md) för att skapa en IoT-hubb, registrera en IoT Edge-enhet och installera och starta IoT Edge-körningen på din enhet. Följ sedan självstudien om att [distribuera jobbet](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) om du vill distribuera ditt Stream Analytics-jobb som en IoT Edge-modul. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en enkel C#-användardefinierad funktion med hjälp av CodeBehind, publicerat jobbet till Azure och distribuerat jobbet till Azure eller IoT Edge enhet. 

Om du vill veta mer om de olika sätten att använda C#-användardefinierade funktioner för Stream Analytics jobb kan du fortsätta till den här artikeln:

> [!div class="nextstepaction"]
> [Skriva funktioner i C# för Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)