---
title: Automatisk hantering av enheter med enhets etablerings tjänsten
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar en automatiserad process för att etablera och dra tillbaka IoT-enheter i Azure Digitals med hjälp av DPS (Device Provisioning service).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ad1351b7c9a649a553ce54422b99a13c286437d6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107303"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Hantera enheter automatiskt i Azure Digitals med hjälp av enhets etablerings tjänsten (DPS)

I den här artikeln får du lära dig hur du integrerar Azure Digitals med [enhets etablerings tjänsten (DPS)](../iot-dps/about-iot-dps.md).

Lösningen som beskrivs i den här artikeln gör att du kan automatisera processen för att **_etablera_** och **_dra tillbaka_** IoT Hub enheter i Azure Digitals, med hjälp av enhets etablerings tjänsten. 

Mer information om hur du _etablerar_ och drar _tillbaka_ faser och för att bättre förstå de allmänna enhets hanterings faser som är gemensamma för alla Enterprise IoT-projekt finns i [avsnittet *enhets livs cykel*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) i IoT Hub enhets hanteringens dokumentation.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera etableringen måste du ställa in följande:
* en **digital Azure-instans**. Följ instruktionerna i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md) för att skapa en digital Azure-instans. Samla in instansens **_värdnamn_** i Azure Portal ([instruktioner](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* en **IoT-hubb**. Anvisningar finns i avsnittet *skapa en IoT Hub* i den här [IoT Hub snabb](../iot-hub/quickstart-send-telemetry-cli.md)starten.
* en [**Azure-funktion**](../azure-functions/functions-overview.md) som uppdaterar Digital dubbel information baserat på IoT Hub data. Följ instruktionerna i så här: mata in [*IoT Hub-data*](how-to-ingest-iot-hub-data.md) för att skapa den här Azure-funktionen. Samla in funktions **_namnet_** för att använda det i den här artikeln.

I det här exemplet används även en **enhets Simulator** som inkluderar etablering med Device Provisioning-tjänsten. Enhets simulatorn finns här: [Azure Digitals, dubbla och IoT Hub integrations exempel](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Hämta exempelprojektet på datorn genom att gå till exempel länken och välja *Hämta zip* -knappen under rubriken. Zippa upp den hämtade mappen.

Du behöver [**Node.js**](https://nodejs.org/download) installerat på datorn. Enhets simulatorn baseras på **Node.js**, version 10.0. x eller senare.

## <a name="solution-architecture"></a>Lösningsarkitektur

Bilden nedan visar arkitekturen för den här lösningen med hjälp av Azure Digitals dubbla med Device Provisioning-tjänsten. Det visar både enhets etableringen och indragnings flödet.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagram över enheter och flera Azure-tjänster i ett scenario från slut punkt till slut punkt. Data flödar fram och tillbaka mellan en termostat enhet och DPS. Data flödar också ut från DPS till IoT Hub och till Azure Digitals dubbla steg via en Azure-funktion med etiketten &quot;allokering&quot;. Data från en manuell &quot;ta bort enhet&quot;-åtgärd flödar genom IoT Hub > Event Hubs > Azure Functions > Azures digitala dubbla." lightbox="media/how-to-provision-using-dps/flows.png":::

Den här artikeln är indelad i två delar:
* [*Automatisk etablering av enhet med enhets etablerings tjänst*](#auto-provision-device-using-device-provisioning-service)
* [*Automatisk borttagning av enhet med IoT Hub livs cykel händelser*](#auto-retire-device-using-iot-hub-lifecycle-events)

För djupare förklaringar av varje steg i arkitekturen, se deras enskilda avsnitt senare i artikeln.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Automatisk etablering av enhet med enhets etablerings tjänst

I det här avsnittet ska du ansluta enhets etablerings tjänsten till Azure Digital-enheter för att etablera enheter automatiskt genom sökvägen nedan. Detta är ett utdrag från den fullständiga arkitekturen som visas [ovan](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagram över etablerings flöde – ett utdrag av lösnings arkitektur diagrammet med siffror som innehåller avsnitt i flödet. Data flödar fram och tillbaka mellan en termostat-enhet och DPS (1 för enhets > DPS och 5 för DPS > Device). Data flödar också ut från DPS till IoT Hub (4) och till Azure Digital-dubblare (3) genom en Azure-funktion med etiketten &quot;allokering&quot; (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Här följer en beskrivning av process flödet:
1. Enheten kontaktar DPS-slutpunkten och skickar identifierings information för att bevisa sin identitet.
2. DPS verifierar enhetens identitet genom att verifiera registrerings-ID och nyckel mot registrerings listan och anropar en [Azure-funktion](../azure-functions/functions-overview.md) för att utföra tilldelningen.
3. Azure-funktionen skapar [en ny enhet](concepts-twins-graph.md) i Azure Digitals dubbla enheter för enheten. Den dubbla får samma namn som enhetens **registrerings-ID**.
4. DPS registrerar enheten med en IoT-hubb och fyller enhetens önskade dubbla tillstånd.
5. IoT Hub returnerar information om enhets-ID och anslutnings information för IoT Hub till enheten. Enheten kan nu ansluta till IoT Hub.

Följande avsnitt beskriver stegen för att konfigurera det här enhets flödet för automatisk etablering.

### <a name="create-a-device-provisioning-service"></a>Skapa en enhets etablerings tjänst

När en ny enhet tillhandahålls med Device Provisioning-tjänsten kan en ny enhet skapas i Azure Digitals med samma namn som registrerings-ID: t.

Skapa en instans av Device Provisioning-tjänsten som ska användas för att etablera IoT-enheter. Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: konfigurera IoT Hub Device Provisioning service med Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Följande Azure CLI-kommando skapar en enhets etablerings tjänst. Du måste ange ett namn, en resurs grupp och en region för enhets etablerings tjänsten. Om du vill se vilka regioner som stöder enhets etablerings tjänsten kan du gå till [*Azure-produkter som är tillgängliga per region*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Kommandot kan köras i [Cloud Shell](https://shell.azure.com)eller lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Lägg till en funktion som ska användas med enhets etablerings tjänsten

I ditt app-projekt som du skapade i avsnittet [krav](#prerequisites) skapar du en ny funktion som du kan använda med enhets etablerings tjänsten. Den här funktionen kommer att användas av enhets etablerings tjänsten i en [anpassad resursallokeringsprincip](../iot-dps/how-to-use-custom-allocation-policies.md) för att etablera en ny enhet.

Börja med att öppna programmet Function i Visual Studio på datorn och följ stegen nedan.

#### <a name="step-1-add-a-new-function"></a>Steg 1: Lägg till en ny funktion 

Lägg till en ny funktion av typen *http-trigger* till Function-exempelprojektet i Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Skärm bild av Visual Studio-vyn för att lägga till en Azure-funktion av typen http-utlösare i ditt Function app-projekt." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Steg 2: Fyll i funktions koden

Lägg till ett nytt NuGet-paket i projektet: [Microsoft. Azure. devices. Provisioning. service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Du kan behöva lägga till fler paket i projektet även om paketen som används i koden inte är en del av projektet redan.

I den nyligen skapade funktions kod filen klistrar du in följande kod, byter namn på funktionen till *DpsAdtAllocationFunc. cs* och sparar filen.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Steg 3: publicera Function-appen till Azure

Publicera projektet med *DpsAdtAllocationFunc. cs* -funktionen i Function-appen i Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Skapa registrering av enhets etablering

Därefter måste du skapa en registrering i enhets etablerings tjänsten med hjälp av en **anpassad tilldelnings funktion**. Följ anvisningarna i avsnittet [*skapa registrering*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) i artikeln principer för anpassad allokering i dokumentationen för enhets etablerings tjänsten.

Se till att du väljer följande alternativ för att länka registreringen till funktionen som du nyss skapade, medan du går igenom flödet.

* **Välj hur du vill tilldela enheter till hubbar**: anpassade (Använd Azure Function).
* **Välj de IoT-hubbar som den här gruppen kan tilldelas:** Välj ditt IoT Hub-namn eller Välj *länken en ny IoT Hub* -knapp och välj IoT Hub i list rutan.

Välj sedan knappen *Välj en ny funktion* för att länka din app till registrerings gruppen. Fyll sedan i följande värden:

* **Prenumeration**: din Azure-prenumeration fylls i automatiskt. Se till att det är rätt prenumeration.
* **Funktionsapp**: Välj namnet på din Function-app.
* **Funktion**: Välj DpsAdtAllocationFunc.

Spara informationen.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Skärm bild av fönstret information om grupp information för att välja anpassad (Använd Azure Function) och namnet på din IoT Hub i avsnittet Välj hur du vill tilldela enheter till hubbar och välja de IoT-hubbar som gruppen kan tilldelas till. Välj också din prenumeration, funktions-app i list rutan och se till att välja DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

När du har skapat registreringen används **primär nyckeln** för registreringen senare för att konfigurera enhets simulatorn för den här artikeln.

### <a name="set-up-the-device-simulator"></a>Konfigurera enhets simulatorn

I det här exemplet används en enhets simulator som inkluderar etablering med enhets etablerings tjänsten. Enhets simulatorn finns här: [Azure Digitals, dubbla och IoT Hub integrations exempel](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Om du inte redan har hämtat exemplet hämtar du det nu genom att gå till exempel länken och välja *Hämta zip* -knappen under rubriken. Zippa upp den hämtade mappen.

#### <a name="upload-the-model"></a>Ladda upp modellen

Enhets simulatorn är en termostat-enhet som använder modellen med detta ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Du måste ladda upp den här modellen till digitala Azure-enheter innan du kan skapa en dubbla av den här typen för enheten.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Mer information om modeller finns i [*instruktion: hantera modeller*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Konfigurera och kör simulatorn

I kommando fönstret navigerar du till det hämtade *Azure Digital-exemplet och IoT Hub-integrering* som du zippat upp tidigare och sedan i katalogen *Device-Simulator* . Installera sedan beroendena för projektet med följande kommando:

```cmd
npm install
```

I din enhets Simulator katalog kopierar du filen. kuvert. template till en ny fil med namnet. kuvert och samlar in följande värden för att fylla i inställningarna:

* PROVISIONING_IDSCOPE: om du vill hämta det här värdet går du till enhets etablerings tjänsten i [Azure Portal](https://portal.azure.com/)och väljer sedan *Översikt* i meny alternativen och letar efter fält *-ID-omfånget*.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Skärm bild av vyn Azure Portal av översikts sidan enhets etablering för att kopiera värdet för ID-omfång." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: du kan välja ett registrerings-ID för din enhet.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: det här är den primära nyckeln för den registrering som du har skapat tidigare. Om du vill hämta det här värdet igen går du till enhets etablerings tjänsten i Azure Portal, väljer *Hantera registreringar* och väljer sedan den registrerings grupp som du skapade tidigare och kopierar *primär nyckeln*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Skärm bild av Azure Portals visning av sidan Hantera registreringar för enhets etablerings tjänsten för att kopiera värdet för primär nyckel för SAS." lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Använd nu värdena ovan för att uppdatera. miljö fil inställningar.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Spara och stäng filen.

### <a name="start-running-the-device-simulator"></a>Starta körning av enhets simulatorn

I mappen *Device-Simulator* i kommando fönstret startar du enhets simulatorn med följande kommando:

```cmd
node .\adt_custom_register.js
```

Du bör se att enheten registreras och är ansluten till IoT Hub och sedan börjar skicka meddelanden.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Skärm bild av Fönstret Kommando visar enhets registrering och sändning av meddelanden" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Verifiera

Som ett resultat av flödet som du har skapat i den här artikeln registreras enheten automatiskt i digitala Azure-enheter. Använd följande [Azure Digitals flätade CLI](how-to-use-cli.md) -kommando för att hitta enhetens enhet i Azure Digitals-instansen som du skapade.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Du bör se den dubbla av enheten som finns på Azure Digitals-instansen.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Skärm bild av Fönstret Kommando som visar nyskapade dubbla." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatisk borttagning av enhet med IoT Hub livs cykel händelser

I det här avsnittet ska du lägga till IoT Hub livs cykel händelser till Azure Digital-meddelanden för att automatiskt dra tillbaka enheter via sökvägen nedan. Detta är ett utdrag från den fullständiga arkitekturen som visas [ovan](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagram över indragningen av enhets flödet – ett utdrag av lösnings arkitektur diagrammet med siffror som innehåller avsnitt i flödet. Termostat-enheten visas utan anslutningar till Azure-tjänsterna i diagrammet. Data från en manuell &quot;ta bort enhet&quot;-åtgärd flödar genom IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digitals dubblare (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Här följer en beskrivning av process flödet:
1. En extern eller manuell process utlöser borttagningen av en enhet i IoT Hub.
2. IoT Hub tar bort enheten och skapar en [enhets livs cykel](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) händelse som kommer att dirigeras till en [Event Hub](../event-hubs/event-hubs-about.md).
3. En Azure-funktion tar bort den dubbla enheten i Azure Digitals.

I följande avsnitt går vi igenom stegen för att ställa in det här flödet för automatisk dragande av enhet.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Därefter skapar du en Azure [Event Hub](../event-hubs/event-hubs-about.md) för att ta emot IoT Hub livs cykel händelser. 

Följ stegen som beskrivs i snabb starten för att [*skapa en Event Hub*](../event-hubs/event-hubs-create.md) . Namnge Event Hub- *lifecycleevents*. Du använder det här namnet på händelsehubben när du konfigurerar IoT Hub väg och en Azure-funktion i nästa avsnitt.

Skärm bilden nedan visar hur du skapar händelsehubben.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Skärm bild av Azure Portals fönstret för att skapa en Event Hub med namnet lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Skapa SAS-princip för händelsehubben

Därefter måste du skapa en [princip för signatur för delad åtkomst (SAS)](../event-hubs/authorize-access-shared-access-signature.md) för att konfigurera Event Hub med din Function-app.
Gör
1. Navigera till händelsehubben som du nyss skapade i Azure Portal och välj principer för **delad åtkomst** i meny alternativen till vänster.
2. Välj **Lägg till**. I fönstret *Lägg till SAS-princip* som öppnas anger du ett valfritt princip namn och markerar kryss rutan *Lyssna* .
3. Välj **Skapa**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Skärm bild av Azure Portal för att lägga till en SAS-princip för Event Hub." lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurera Event Hub med Function-appen

Konfigurera sedan Azure Function-appen som du konfigurerade i avsnittet [krav](#prerequisites) för att arbeta med den nya händelsehubben. Du gör detta genom att ställa in en miljö variabel i Function-appen med händelse hubbens anslutnings sträng.

1. Öppna den princip som du nyss skapade och kopiera **anslutnings strängen – primär nyckel** värde.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Skärm bild av Azure Portal för att kopiera anslutnings strängen – primär nyckel." lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Lägg till anslutnings strängen som en variabel i funktionen app-inställningar med följande Azure CLI-kommando. Kommandot kan köras i [Cloud Shell](https://shell.azure.com)eller lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Lägg till en funktion för att dra tillbaka med IoT Hub livs cykel händelser

I ditt app-projekt som du skapade i avsnittet [krav](#prerequisites) skapar du en ny funktion för att dra tillbaka en befintlig enhet med IoT Hub livs cykel händelser.

Mer information om livs cykel händelser finns i [*IoT Hub händelser som inte är telemetri*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Mer information om hur du använder Event Hubs med Azure Functions finns i [*Azure Event Hubs-utlösare för Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Börja med att öppna programmet Function i Visual Studio på datorn och följ stegen nedan.

#### <a name="step-1-add-a-new-function"></a>Steg 1: Lägg till en ny funktion
     
Lägg till en ny funktion av typen *Event Hub-utlösare* i Function app-projektet i Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Skärm bild av Visual Studio-fönstret för att lägga till en Azure-funktion av typen Event Hub-utlösare i ditt Function app-projekt." lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Steg 2: Fyll i funktions koden

I den nyligen skapade funktions kod filen klistrar du in följande kod, byter namn på funktionen till `DeleteDeviceInTwinFunc.cs` och sparar filen.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Steg 3: publicera Function-appen till Azure

Publicera projektet med *DeleteDeviceInTwinFunc. cs* -funktionen i Function-appen i Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Skapa en IoT Hub väg för livs cykel händelser

Nu ska du konfigurera ett IoT Hub väg, för att dirigera enhets livs cykel händelser. I det här fallet kommer du specifikt att lyssna på enhets borttagnings händelser, identifierade av `if (opType == "deleteDeviceIdentity")` . Detta utlöser borttagningen av det digitala dubbla objektet, och slutför indragningen av en enhet och dess digitala enhet.

Först måste du skapa en händelse nav-slutpunkt i IoT Hub. Sedan lägger du till en väg i IoT Hub för att skicka livs cykel händelser till den här Event Hub-slutpunkten.
Följ de här stegen för att skapa en Event Hub-slutpunkt:

1. I [Azure Portal](https://portal.azure.com/)navigerar du till IoT-hubben som du skapade i avsnittet [krav](#prerequisites) och väljer **meddelanderoutning** i meny alternativen till vänster.
2. Välj fliken **anpassade slut punkter** .
3. Välj **+ Lägg till** och välj **händelse hubbar** för att lägga till en typ slut punkt för händelse hubbar.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Skärm bild av Visual Studio-fönstret för att lägga till en anpassad slut punkt för Event Hub." lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. I fönstret *lägger du till en händelse Hubbs slut punkt* som öppnas väljer du följande värden:
    * **Slut punkts namn**: Välj ett slut punkts namn.
    * **Namn område för händelsehubben**: Välj din Event Hub-namnrymd i list rutan.
    * **Event Hub-instans**: Välj namnet på den händelsehubben som du skapade i föregående steg.
5. Välj **Skapa**. Låt fönstret vara öppet för att lägga till en väg i nästa steg.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Skärm bild av Visual Studio-fönstret för att lägga till en Event Hub-slutpunkt." lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Härnäst ska du lägga till en väg som ansluter till slut punkten som du skapade i steget ovan, med en cirkulations fråga som skickar borttagnings händelserna. Följ de här stegen för att skapa en väg:

1. Navigera till fliken *vägar* och välj **Lägg** till för att lägga till en väg.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Skärm bild av Visual Studio-fönstret för att lägga till en väg för att skicka händelser." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. På sidan *Lägg till en väg* som öppnas väljer du följande värden:

   * **Namn**: Välj ett namn för din väg. 
   * **Slut punkt**: Välj den slut punkt för händelse hubbar som du skapade tidigare i list rutan.
   * **Data källa**: Välj *livs cykel händelser för enhet*.
   * **Cirkulations fråga**: ange `opType='deleteDeviceIdentity'` . Den här frågan begränsar enhetens livs cykel händelser så att de endast skickar Delete-händelserna.

3. Välj **Spara**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Skärm bild av Azure Portals fönstret för att lägga till en väg för att skicka livs cykel händelser." lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

När du har gått igenom det här flödet är allt inställt på att dra tillbaka enheter från slut punkt till slut punkt.

### <a name="validate"></a>Verifiera

Om du vill utlösa processen för pensionering måste du manuellt ta bort enheten från IoT Hub.

Du kan göra detta med ett [Azure CLI-kommando](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) eller i Azure Portal. Följ stegen nedan för att ta bort enheten i Azure Portal:

1. Navigera till din IoT-hubb och välj **IoT-enheter** i meny alternativen till vänster. 
2. Du ser en enhet med det ID för enhets registrering som du valde i den [första halvan av den här artikeln](#auto-provision-device-using-device-provisioning-service). Alternativt kan du välja en annan enhet att ta bort, så länge den har en dubbel i Azure Digital-, så att du kan kontrol lera att den dubbla tas bort automatiskt när enheten har tagits bort.
3. Välj enheten och välj **ta bort**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Skärm bild av Azure Portal att ta bort enheten från IoT-enheterna." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Det kan ta några minuter att se ändringarna som återges i Azure Digital-dubbla.

Använd följande [Azure Digitals flätade CLI](how-to-use-cli.md) -kommando för att kontrol lera att enhetens enhet i Azure Digitals-instansen har tagits bort.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Du bör se att enhetens enhets enhet inte kan hittas på Azures digitala dubbla instanser längre.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Skärm bild av Fönstret Kommando som visar dubbla hittades inte." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver resurserna som skapats i den här artikeln följer du stegen nedan för att ta bort dem.

Med hjälp av Azure Cloud Shell eller lokalt Azure CLI kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) . Detta tar bort resurs gruppen. Azure Digitals dubbla instanser, IoT-hubben och nav enhets registreringen. avsnittet Event Grid och associerade prenumerationer. Event Hub-namnområdet och båda Azure Functions appar, inklusive tillhör ande resurser som lagring.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ta sedan bort den Project-exempel mapp som du laddade ned från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

De digitala multiplarna som skapas för enheterna lagras som en platt hierarki i Azure Digitals, men de kan berikas med modell information och en hierarki med flera nivåer för organisationen. Läs mer om det här konceptet i:

* [*Koncept: digitala och dubbla grafer*](concepts-twins-graph.md)

Mer information om hur du använder HTTP-förfrågningar med Azure Functions finns i:

* [*Azure http-begäran utlösare för Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med modell-och diagram data som redan finns lagrade i Azure Digitals. Mer information om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram finns i följande avsnitt:

* [*Anvisningar: hantera en digital, dubbel*](how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)