---
title: 'Självstudie: skapa simulerade enhets data Machine Learning på Azure IoT Edge'
description: Självstudie – Skapa virtuella enheter som genererar simulerad telemetri som senare kan användas för att träna en maskin inlärnings modell.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eef5e60b06eedb1fb07c57aa2e369dd3830fcad5
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932412"
---
# <a name="tutorial-generate-simulated-device-data"></a>Självstudie: skapa simulerade enhets data

I den här artikeln använder vi Machine Learning Training-data för att simulera en enhet som skickar telemetri till Azure IoT Hub. Som anges i introduktionen använder den här självstudien [turbofan](https://c3.nasa.gov/dashlink/resources/139/) för att simulera data från en uppsättning flyg Plans motorer för utbildning och testning.

I vårt experiment scenario vet vi att:

* Data består av flera multivarierad tids serier.
* Varje data uppsättning är indelad i utbildning och test del mängder.
* Varje tids serie är från en annan motor.
* Varje motor börjar med olika grader av inledande slitage och tillverknings variation.

I den här självstudien använder vi en delmängd av en enda data uppsättning (FD003) för utbildning.

I verkligheten skulle varje motor vara en oberoende IoT-enhet. Förutsatt att du inte har en samling av turbofan-motorer som är anslutna till Internet, kommer vi att skapa en program vara för dessa enheter.

Simulatorn är ett C#-program som använder IoT Hub-API: er för att program mässigt registrera virtuella enheter med IoT Hub. Vi läser sedan data för varje enhet från NASA data mängd och skickar dem till IoT-hubben med hjälp av en simulerad IoT-enhet. All kod för den här delen av själv studie kursen finns i DeviceHarness-katalogen för lagrings platsen.

DeviceHarness-projektet är ett .NET Core-projekt skrivet i C# som består av fyra klasser:

* **Program:** Start punkten för körning som ansvarar för att hantera användarindata och övergripande samordning.
* **TrainingFileManager:** Ansvarig för läsning och parsning av den valda data filen.
* **CycleData:** Representerar en enskild rad med data i en fil som har konverterats till meddelande format.
* **TurbofanDevice:** Ansvarig för att skapa en IoT-enhet som motsvarar en enskild enhet (Time Series), i data och överföring av data till IoT Hub.

De uppgifter som beskrivs i den här artikeln bör ta ungefär 20 minuter att slutföra.

Den verkliga motsvarigheten till arbetet i det här steget skulle förmodligen utföras av enhets utvecklare och moln utvecklare.

I det här avsnittet av självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Införliva ett externt projekt i din utvecklings miljö.
> * Använd samplet DeviceHarness-projektet för att generera simulerade IoT-enhets data.
> * Visa genererade data i IoT Hub.

## <a name="prerequisites"></a>Krav

Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Varje artikel i serien bygger på arbetet i föregående artikel. Om du har kommit till den här artikeln direkt kan du gå till den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Konfigurera Visual Studio Code och build DeviceHarness Project

1. Öppna en fjärrskrivbordssession till din utvecklings-VM.

1. Öppna mappen i Visual Studio Code `C:\source\IoTEdgeAndMlSample\DeviceHarness` .

1. Eftersom du använder tillägg på den här datorn för första gången, kommer vissa tillägg att uppdatera och installera deras beroenden. Du kan uppmanas att uppdatera tillägget. I så fall, väljer du **Omladdnings fönster**.

   Om OmniSharp-fel visas i fönstret utdata måste du avinstallera C#-tillägget.

1. Du uppmanas att lägga till nödvändiga till gångar för DeviceHarness. Välj **Ja** för att lägga till dem.

   * Det kan ta några sekunder innan meddelandet visas.
   * Om du missade det här meddelandet, se klock ikonen i det nedre högra hörnet.

   ![Meny tillägg för VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Välj **Återställ** för att återställa paket beroenden.

   ![VS Code Restore-prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Om du inte får de här meddelandena stänger du Visual Studio Code, tar bort katalogen bin och OBJ i `C:\source\IoTEdgeAndMlSample\DeviceHarness` , öppnar Visual Studio Code och öppnar mappen DeviceHarness igen.

1. Kontrol lera att din miljö har kon figurer ATS korrekt genom att utlösa en build-, **CTRL**-  +  **Shift**  +  **B**-eller **Terminal**  >  **Run-åtgärd**.

1. Du uppmanas att välja build-uppgiften som ska köras. Välj **build**.

1. Bygget körs och genererar ett meddelande som visar att det lyckades.

   ![Genererat utdata-meddelande](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Du kan göra det här steget för att skapa en standard uppgift genom att välja **Terminal**  >  **Konfigurera standard build-aktivitet...** och välja **skapa** från prompten.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Anslut till IoT Hub och kör DeviceHarness

Nu när vi har skapat projektet ansluter du till din IoT Hub för att få åtkomst till anslutnings strängen och övervakar förloppet för genereringen av data.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Logga in på Azure i Visual Studio Code

1. Logga in på din Azure-prenumeration i Visual Studio Code genom att öppna kommando-paletten `Ctrl + Shift + P` eller **Visa**  >  **kommando paletten**.

1. Sök efter kommandot **Azure: Sign in** .

   Ett webbläsarfönster öppnas och du uppmanas att ange dina autentiseringsuppgifter. När du omdirigeras till en lyckad sida kan du stänga webbläsaren.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Anslut till din IoT-hubb och hämta nav-anslutningssträng

1. I det nedre avsnittet i Visual Studio Code Explorer väljer du den **Azure IoT Hub** -ram som ska expanderas.

1. I den expanderade rutan klickar du på **välj IoT Hub**.

1. När du uppmanas väljer du din Azure-prenumeration och sedan IoT Hub.

1. Klicka på **...** till höger om **Azure IoT Hub** för fler åtgärder. Välj **kopiera IoT Hub anslutnings sträng**.

   ![Kopiera IoT Hub anslutnings sträng](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Kör DeviceHarness-projektet

1. Välj **Visa**  >  **Terminal** för att öppna Visual Studio Code Terminal.

   Om du inte ser någon prompt trycker du på RETUR.

1. Ange `dotnet run` i terminalen.

1. När du uppmanas att ange anslutnings strängen för IoT Hub klistrar du in anslutnings strängen som du kopierade i föregående avsnitt.

1. Klicka på knappen Uppdatera i rutan **Azure IoT Hub-enheter** .

   ![Uppdatera IoT Hub enhets lista](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observera att enheter läggs till IoT Hub och att enheterna visas i grönt för att indikera att data skickas via den enheten. När enheterna har skickat meddelanden till IoT-hubben kopplas de bort och visas som blå.

1. Du kan visa meddelanden som skickas till hubben genom att högerklicka på valfri enhet och välja **starta övervakning inbyggd händelse slut punkt**. Meddelandena visas i fönstret utdata i Visual Studio Code.

1. Stoppa övervakningen genom att klicka i fönstret för **Azure IoT Hub** utdata och välj **stoppa övervakning inbyggd händelse slut punkt**.

1. Låt programmet köras klart, vilket tar några minuter.

## <a name="check-iot-hub-for-activity"></a>Kontrol lera IoT Hub för aktivitet

De data som skickas av DeviceHarness gick till din IoT-hubb, där du kan kontrol lera i Azure Portal.

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till IoT Hub som skapats för den här självstudien.

1. Välj **mått** i den vänstra rutans meny under **övervakning**.

1. Klicka på list rutan **mått** på sidan diagram definition, rulla nedåt i listan och välj **Routing: data som levereras till lagring**. Diagrammet ska Visa insamling av när data dirigerades till lagringen.

   ![Diagrammet visar insamling när data levereras till lagring](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Verifiera data i Azure Storage

De data som vi precis skickade till din IoT Hub dirigerades till lagrings behållaren som vi skapade i föregående artikel. Nu ska vi titta på data i vårt lagrings konto.

1. Navigera till ditt lagringskonto i Azure Portal.

1. Välj **Storage Explorer (för hands version)** i lagrings konto navigeringen.

1. I Storage Explorer väljer du **BLOB-behållare** och sedan `devicedata` .

1. I rutan innehåll klickar du på mappen för namnet på IoT Hub följt av år, månad, dag och timme. Du kommer att se flera mappar som representerar minuterna när data skrevs.

   ![Visa mappar i Blob Storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klicka i någon av dessa mappar för att söka efter datafiler med etiketten **00** och **01** som motsvarar partitionen.

1. Filerna skrivs i [Avro](https://avro.apache.org/) -format. Dubbelklicka på någon av dessa filer för att öppna en annan flik i webbläsaren och återge data delvis. Om du uppmanas att öppna filen i ett program kan du välja VS Code och den kommer att återges på rätt sätt.

1. Du behöver inte försöka att läsa eller tolka data just nu. Vi kommer att göra det i nästa artikel.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi ett .NET Core-projekt för att skapa en uppsättning virtuella IoT-enheter och skicka data till vår IoT-hubb och till en Azure Storage-behållare. Det här projektet simulerar ett verkligt scenario där fysiska IoT-enheter skickar data till en IoT Hub och därefter till ett granskat lagrings utrymme. Dessa data omfattar sensor avläsningar, operativa inställningar, felsignaler och lägen, och så vidare. När tillräckligt med data har samlats in använder vi den för att träna modeller som förutsäger återstående livs längd (RUL) för enheten. Vi demonstrerar den här maskin inlärningen i nästa artikel.

Fortsätt till nästa artikel om du vill träna en maskin inlärnings modell med data.

> [!div class="nextstepaction"]
> [Träna och distribuera en Azure Machine Learning-modell](tutorial-machine-learning-edge-04-train-model.md)
