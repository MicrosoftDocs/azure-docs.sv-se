---
title: Självstudie – Stream Analytics på gränsen med Azure IoT Edge
description: I den här självstudien distribuerar du Azure Stream Analytics som en modul till en IoT Edge enhet
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462056"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Självstudie: Distribuera Azure Stream Analytics som en IoT Edge modul

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Många IoT-lösningar använder analys tjänster för att få insikt om data när de tas emot i molnet från IoT-enheter. Med Azure IoT Edge kan du använda logik från [Azure Stream Analytics](../stream-analytics/index.yml) och flytta den till själva enheten. Du kan minska hur mycket data som överförs och minska tiden det tar att reagera på insikter genom att bearbeta telemetriströmmar på gränsen.

Azure IoT Edge och Azure Stream Analytics är integrerade för att förenkla din arbets belastnings utveckling. Du kan skapa ett Azure Stream Analytics jobb i Azure Portal och sedan distribuera det som en IoT Edge modul utan ytterligare kod.  

Azure Stream Analytics ger en omfattande strukturerad frågesyntax för data analys, både i molnet och på IoT Edge enheter. Mer information finns i [Azure Stream Analytics-dokumentationen](../stream-analytics/stream-analytics-edge.md).

Stream Analytics-modulen i den här självstudien beräknar medeltemperaturen över ett rullande 30-sekundersschema. När medeltemperaturen når 70 grader skickar modulen en varning så att enheten kan vidta en åtgärd. I det här fallet är åtgärden att återställa den simulerade temperatursensorn. Du kan till exempel använda den här funktionen för att stänga av en dator eller vidta förebyggande åtgärder när temperaturen når farliga nivåer i en produktionsmiljö.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Skapa ett Azure Stream Analytics-jobb för bearbetning av data på gränsen.
> * Anslut det nya Azure Stream Analytics-jobbet med andra IoT Edge-moduler.
> * Distribuera Azure Stream Analytics-jobbet till en IoT Edge-enhet från Azure Portal.

<center>

![Diagram – självstudie arkitektur: mellanlagra och distribuera ASA-jobb](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet:

* Du kan använda en virtuell Azure-dator som en IoT Edge enhet genom att följa stegen i snabb starten för [Linux](quickstart-linux.md) -eller [Windows-enheter](quickstart.md).

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Skapa ett Azure Stream Analytics-jobb

I det här avsnittet skapar du ett Azure Stream Analytics jobb som gör följande steg:

* Ta emot data från IoT Edges enheten.
* Fråga telemetridata för värden utanför ett uppsättnings intervall.
* Vidta åtgärder på den IoT Edge enheten baserat på frågeresultaten.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

När du skapar ett Azure Stream Analytics-jobb som ska köras på en IoT Edge-enhet måste det lagras på ett sätt som kan anropas från enheten. Du kan använda ett befintligt Azure Storage konto eller skapa ett nytt nu.

1. I Azure Portal går du till **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**.

1. Skapa lagringskontot genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resurs grupp för alla test resurser för IoT Edge snabb starter och självstudier. Till exempel **IoTEdgeResources**. |
   | Name | Ange ett unikt namn för lagringskontot. |
   | Location | Välj en plats i närheten av dig. |

1. Behåll standardvärdena för de andra fälten och välj **Granska + skapa**.

1. Granska inställningarna och välj sedan **skapa**.

### <a name="create-a-new-job"></a>Skapa ett nytt jobb

1. I Azure Portal går du till **skapa en resurs**  >  **Sakernas Internet**  >  **Stream Analytics jobb**.

1. Skapa jobbet genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Jobbnamn | Ange ett namn för ditt jobb. Till exempel **IoTEdgeJob** |
   | Prenumeration | Välj samma prenumeration som din IoT-hubb. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Location | Välj en plats i närheten av dig. |
   | Värdmiljö | Välj **Gräns**. |

1. Välj **Skapa**.

### <a name="configure-your-job"></a>Konfigurera jobbet

När Stream Analytics-jobbet har skapats på Azure Portal kan du konfigurera det med indata, utdata och en fråga som ska köras mot de data som skickas.

I det här avsnittet används de tre elementen för indata, utdata och fråga för att skapa ett jobb som tar emot temperaturdata från IoT Edge-enheten. Dessa data analyseras i ett rullande 30-sekundersintervall. Om medeltemperaturen i intervallet överstiger 70 grader, skickas en avisering till IoT Edge-enheten. Du anger exakt var dessa data kommer från och vart de skickas i nästa avsnitt när du distribuerar jobbet.  

1. Gå till Stream Analytics-jobbet på Azure Portal.

1. Välj **Indata** under **Jobbtopologi** och välj sedan **Lägg till strömindata**.

   ![Azure Stream Analytics – Lägg till inmatade](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Välj **Edge Hub** i listrutan.

1. Ange **temperatur** som inmatat alias i fönstret **Nya indata**.

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Öppna **Utdata** under **Jobbtopologi** och välj sedan **Lägg till**.

   ![Azure Stream Analytics – Lägg till utdata](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Välj **Edge Hub** i listrutan.

1. I rutan **Nya utdata** anger du **avisering** som utdataalias.

1. Behåll standardvärdena för de andra fälten och välj **Spara**.

1. Under **jobb sto pol Ogin** väljer du **fråga**.

1. Ersätt standardtexten med följande fråga. SQL-koden skickar ett återställningskommando till aviseringens utdata om medeltemperaturen under ett 30-sekundersintervall når 70 grader. Återställningskommandot har förprogrammerats i sensorn som en åtgärd som kan utföras.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Välj **Spara fråga**.

### <a name="configure-iot-edge-settings"></a>Konfigurera IoT Edge-inställningar

För att förbereda Stream Analytics-jobbet för distribution till en IoT Edge-enhet måste du associera jobbet med en container i ett lagringskonto. När du sedan distribuerar jobbet exporteras jobbdefinitionen till lagringscontainern.

1. Under **Konfigurera** väljer du **Inställningar för lagrings konto** och väljer sedan **Lägg till lagrings konto**.

   ![Azure Stream Analytics – Lägg till lagrings konto](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Välj det **lagrings konto** som du skapade i början av den här självstudien från den nedrullningsbara menyn.

1. För fältet **Container** väljer du **Skapa ny** och anger ett namn för lagringscontainern.

1. Välj **Spara**.

## <a name="deploy-the-job"></a>Distribuera jobbet

Du kan nu distribuera Azure Stream Analytics-jobbet till din IoT Edge-enhet.

I det här avsnittet använder du guiden **Ange moduler** på Azure Portal för att skapa ett *distributionsmanifest*. Ett distributionsmanifest är en JSON-fil som beskriver alla moduler som ska distribueras till en enhet, containerregistren som lagrar modulavbildningarna, hur modulerna ska hanteras och hur modulerna kan kommunicera med varandra. Din IoT Edge-enhet hämtar distributionsmanifestet från IoT Hub och använder sedan informationen i det för att distribuera och konfigurera alla dess tilldelade moduler.

I den här självstudien ska du distribuera två moduler. Den första är **SimulatedTemperatureSensor**, som är en modul som simulerar en temperatur-och fuktighets sensor. Den andra modulen är ditt Stream Analytics-jobb. Sensormodulen tillhandahåller dataströmmen som din jobbfråga analyserar.

1. Gå till din IoT-hubb på Azure Portal.

1. Gå till **IoT Edge** och öppna sedan informations sidan för IoT Edge enheten.

1. Välj **Ange moduler**.  

1. Om du tidigare har distribuerat SimulatedTemperatureSensor-modulen på den här enheten kan den fyllas i automatiskt. Om inte så lägger du till modulen genom att följa dessa steg:

   1. Klicka på **Lägg till** och välj **IoT Edge-modul**.
   1. Skriv **SimulatedTemperatureSensor** som namn.
   1. Ange **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** som URI för avbildning.
   1. Lämna de andra inställningarna oförändrade och välj **Lägg till**.

1. Lägg till ditt Azure Stream Analytics Edge-jobb med följande steg:

   1. Klicka på **Lägg till** och välj **Azure Stream Analytics-modul**.
   1. Välj din prenumeration och Azure Stream Analytics Edge-jobbet som du skapade.
   1. Välj **Spara**.

   När du har sparat ändringarna publiceras information om ditt Stream Analytics jobb till den lagrings behållare som du skapade.

1. När Stream Analytics-modulen läggs till i listan över moduler, Välj dess namn för att se hur den är strukturerad och uppdatera inställningarna på sidan **uppdatera IoT Edge-modulen** .

   Fliken **Modulnamn** har **bild-URI: n** som pekar på en standard Azure Stream Analytics-avbildning. Den här avbildningen används för alla Stream Analytics-moduler som distribueras till en IoT Edge-enhet.

   Fliken **dubbla inställningar** visar den JSON som definierar den Azure Stream Analytics (ASA) egenskap som kallas **ASAJobInfo**. Värdet för den egenskapen pekar på jobbdefinitionen i lagringscontainern. Den här egenskapen är hur Stream Analytics avbildningen konfigureras med information om det aktuella jobbet.

   Som standard tar modulen Stream Analytics samma namn som det jobb den baseras på. Du kan ändra modulnamnet på den här sidan om du vill, men det är inte nödvändigt.

1. Välj **Uppdatera** eller **Avbryt**.

1. Anteckna namnet på din Stream Analytics-modul eftersom du behöver den i nästa steg. Välj sedan **Nästa: vägar** för att fortsätta.

1. På fliken **vägar** definierar du hur meddelanden ska skickas mellan moduler och IoT Hub. Meddelanden skapas med hjälp av namn/värde-par. Ersätt standard `route` `upstream` namn och-värden med de par som visas i följande tabell, följande namn/värde-par, och ersätter instanserna av _{Modulnamn}_ med namnet på din Azure Stream Analytics-modul.

    | Name | Värde |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    De vägar som du deklarerar här definierar flödet av data genom IoT Edge-enheten. Telemetridata från SimulatedTemperatureSensor skickas till IoT Hub och till de **temperatur** indata som konfigurerades i Stream Analyticss jobbet. **Aviserings** meddelanden skickas till IoT Hub och till SimulatedTemperatureSensor-modulen för att utlösa återställnings kommandot.

1. Välj **Nästa: granska + skapa**.

1. På fliken **Granska + skapa** kan du se hur den information som du har angett i guiden konverteras till ett JSON-distributions manifest. När du är klar med att granska manifestet väljer du **skapa**.

1. Du kommer tillbaka till sidan med enhets information. Välj **Uppdatera**.  

    Du bör se den nya Stream Analytics-modulen som körs, tillsammans med IoT Edge-agenten och IoT Edge Hub-moduler. Det kan ta några minuter innan informationen når din IoT Edge-enhet och sedan kan de nya modulerna starta. Om du inte ser modulerna som körs direkt kan du fortsätta att uppdatera sidan.

    ![Modulen SimulatedTemperatureSensor och ASA som rapporteras av enheten](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Visa data

Nu kan du gå till din IoT Edge-enhet för att ta en titt på interaktionen mellan modulen Azure Stream Analytics och SimulatedTemperatureSensor-modulen.

1. Kontrollera att alla moduler körs i Docker:

   ```cmd/sh
   iotedge list  
   ```

1. Visa alla systemloggar och statistikdata. Använd Stream Analytics-modulnamnet:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Visa återställnings kommandot påverkar SimulatedTemperatureSensor genom att Visa sensor loggarna:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Du kan se om datorns temperatur gradvis stiger tills den når 70 grader i 30 sekunder. Då utlöser Stream Analytics-modulen en återställning och datortemperaturen går tillbaka till 21.

   ![Återställa kommandoutdata till modulloggar](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du ett Azure Streaming Analytics-jobb för att analysera data från din IoT Edge-enhet. Sedan läste du in den här Azure Stream Analytics-modulen på din IoT Edge-enhet för att bearbeta och agera på ökningar i temperatur lokalt samt skicka den aggregerade dataströmmen till molnet. Om du vill se hur Azure IoT Edge kan skapa flera lösningar för ditt företag fortsätter du med de andra självstudiekurserna.

> [!div class="nextstepaction"]
> [Distribuera en Azure Machine Learning-modell som en modul](tutorial-deploy-machine-learning.md)