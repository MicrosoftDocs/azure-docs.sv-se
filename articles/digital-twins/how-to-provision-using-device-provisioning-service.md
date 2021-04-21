---
title: Hantera enheter automatiskt med device provisioning-tjänsten
titleSuffix: Azure Digital Twins
description: Se hur du ställer in en automatiserad process för att etablera och dra tillbaka IoT-enheter i Azure Digital Twins med enhetsetableringstjänsten (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 51b5714f9009cbe48aa49c6a04a1434cec12396e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790697"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Hantera enheter automatiskt i Azure Digital Twins device provisioning service (DPS)

I den här artikeln lär du dig att integrera Azure Digital Twins med [Device Provisioning Service (DPS).](../iot-dps/about-iot-dps.md)

Med lösningen som beskrivs i den här artikeln **** kan du automatisera processen för att etablera och dra tillbaka **_IoT Hub_** enheter i Azure Digital Twins med hjälp av Enhetsetableringstjänsten. 

Mer information om  etablerings- och tillbakafaserna och för att bättre förstå uppsättningen allmänna enhetshanteringssteg [  ](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) som är gemensamma för alla IoT-företagsprojekt finns i avsnittet Enhetslivscykel i IoT Hub:s dokumentation om enhetshantering. 

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera etableringen måste du konfigurera följande:
* en **Azure Digital Twins instans**. Följ anvisningarna i [*Anvisningar: Konfigurera en instans och autentisering för att skapa*](how-to-set-up-instance-portal.md) en Azure Digital Twins-instans. Samla in instansens **_värdnamn i_** Azure Portal ([instruktionerna](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* en **IoT-hubb**. Mer information finns i avsnittet *Skapa en IoT Hub* i den här IoT Hub [snabbstarten](../iot-hub/quickstart-send-telemetry-cli.md).
* en [**Azure-funktion**](../azure-functions/functions-overview.md) som uppdaterar digital tvillinginformation baserat på IoT Hub data. Följ instruktionerna i [*Anvisningar: Mata in IoT Hub-data för*](how-to-ingest-iot-hub-data.md) att skapa den här Azure-funktionen. Samla in **_funktionsnamnet för_** att använda det i den här artikeln.

Det här exemplet använder också en **enhetssimulator** som inkluderar etablering med enhetsetableringstjänsten. Enhetssimulatorn finns här: [Azure Digital Twins och IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Hämta exempelprojektet på datorn genom att navigera till exempellänken och välja **knappen Bläddra** kod under rubriken. Då kommer du till GitHub-lagringsplatsen för exemplet, som du kan ladda ned som *en . ZIP-fil* genom att välja **knappen** Kod och **Ladda ned ZIP.** 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Skärmbild av lagringsplatsen digital-twins-iothub-integration på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Packa upp den hämtade mappen.

Du måste installera [**Node.js**](https://nodejs.org/download) datorn. Enhetssimulatorn baseras på **Node.js** version 10.0.x eller senare.

## <a name="solution-architecture"></a>Lösningsarkitektur

Bilden nedan illustrerar arkitekturen för den här lösningen med hjälp Azure Digital Twins med Enhetsetableringstjänsten. Den visar både enhetsetabler- och tillbaka retire-flödet.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagram över enheten och flera Azure-tjänster i ett scenario från start till slut. Data flödar fram och tillbaka mellan en termostatenhet och DPS. Data flödar också ut från DPS till IoT Hub och för att Azure Digital Twins via en Azure-funktion med etiketten &quot;Allocation&quot; (Allokering). Data från en manuell åtgärd för att ta bort enhet flödar IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Den här artikeln är indelad i två avsnitt:
* [*Etablera enheten automatiskt med device provisioning-tjänsten*](#auto-provision-device-using-device-provisioning-service)
* [*Dra tillbaka enheten automatiskt med hjälp IoT Hub livscykelhändelser*](#auto-retire-device-using-iot-hub-lifecycle-events)

Mer ingående förklaringar av varje steg i arkitekturen finns i deras enskilda avsnitt senare i artikeln.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Etablera enheten automatiskt med device provisioning-tjänsten

I det här avsnittet kopplar du Device Provisioning Service till Azure Digital Twins att etablera enheter automatiskt via sökvägen nedan. Det här är ett utdrag från den fullständiga arkitektur som [visades tidigare](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagram över Etablera flöde – ett utdrag ur lösningsarkitekturdiagrammet med siffror som etiketterar avsnitt i flödet. Data flödar fram och tillbaka mellan en termostatenhet och DPS (1 för enhet > DPS och 5 för DPS > enhet). Data flödar också ut från DPS till IoT Hub (4) och till Azure Digital Twins (3) via en Azure-funktion med etiketten &quot;Allocation&quot; (2)." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Här är en beskrivning av processflödet:
1. Enheten kontaktar DPS-slutpunkten och anger identifierande information för att bevisa sin identitet.
2. DPS verifierar enhetsidentiteten genom att verifiera registrerings-ID:t och nyckeln mot registreringslistan och anropar en [Azure-funktion](../azure-functions/functions-overview.md) för att göra allokeringen.
3. Azure-funktionen skapar en ny [tvilling](concepts-twins-graph.md) i Azure Digital Twins för enheten. Tvillingen har samma namn som enhetens **registrerings-ID.**
4. DPS registrerar enheten med en IoT-hubb och fyller enhetens önskade tvillingtillstånd.
5. IoT-hubben returnerar information om enhets-ID och IoT-hubbanslutningsinformation till enheten. Enheten kan nu ansluta till IoT-hubben.

I följande avsnitt går vi igenom stegen för att konfigurera det här enhetsflödet för automatisk etablering.

### <a name="create-a-device-provisioning-service"></a>Skapa en enhetsetableringstjänst

När en ny enhet etableras med enhetsetableringstjänsten kan en ny tvilling för enheten skapas i Azure Digital Twins med samma namn som registrerings-ID:t.

Skapa en instans av enhetsetableringstjänsten som ska användas för att etablera IoT-enheter. Du kan antingen använda Azure CLI-anvisningarna nedan eller använda Azure Portal: [*Snabbstart:*](../iot-dps/quick-setup-auto-provision.md)Konfigurera IoT Hub Device Provisioning Service med Azure Portal .

Följande Azure CLI-kommando skapar en enhetsetableringstjänst. Du måste ange ett namn, en resursgrupp och en region för enhetsetableringstjänsten. Om du vill se vilka regioner som stöder Device Provisioning Service går du till [*Azure-produkter som är tillgängliga per region.*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)
Kommandot kan köras i [Cloud Shell](https://shell.azure.com)lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Lägga till en funktion som ska användas med Device Provisioning-tjänsten

I ditt funktionsappsprojekt som [](#prerequisites) du skapade i avsnittet förutsättningar skapar du en ny funktion som ska användas med device provisioning-tjänsten. Den här funktionen används av Device Provisioning Service i en anpassad [allokeringsprincip för](../iot-dps/how-to-use-custom-allocation-policies.md) att etablera en ny enhet.

Börja med att öppna funktionsappsprojektet i Visual Studio på datorn och följ stegen nedan.

#### <a name="step-1-add-a-new-function"></a>Steg 1: Lägg till en ny funktion 

Lägg till en ny funktion av typen *HTTP-utlösare* i funktionsappsprojektet i Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png" alt-text="Skärmbild av Visual Studio för att lägga till En Azure-funktion av typen HTTP-utlösare i funktionsappsprojektet." lightbox="media/how-to-provision-using-device-provisioning-service/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Steg 2: Fyll i funktionskod

Lägg till ett nytt NuGet-paket i projektet: [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Du kan också behöva lägga till fler paket i projektet om paketen som används i koden inte redan ingår i projektet.

I den nya funktionskodfilen klistrar du in följande kod, byter namn på funktionen till *DpsAdtAllocationFunc.cs* och sparar filen.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Steg 3: Publicera funktionsappen till Azure

Publicera projektet med *funktionen DpsAdtAllocationFunc.cs* till funktionsappen i Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Skapa registrering för enhetsetablering

Därefter måste du skapa en registrering i Device Provisioning Service med hjälp av en **anpassad allokeringsfunktion**. Följ anvisningarna för att göra detta i [*avsnittet Skapa registrering i*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) artikeln om anpassade allokeringsprinciper i dokumentationen för enhetsetableringstjänsten.

När du går igenom flödet ser du till att välja följande alternativ för att länka registreringen till den funktion som du nyss skapade.

* **Välj hur du vill tilldela enheter till hubbar:** Anpassad (Använd Azure-funktion).
* **Välj de IoT-hubbar som den här gruppen kan tilldelas till:** Välj namnet på din IoT-hubb eller välj knappen Länka en ny *IoT-hubb* och välj din IoT-hubb i listrutan.

Välj sedan knappen *Välj en ny funktion för* att länka funktionsappen till registreringsgruppen. Fyll sedan i följande värden:

* **Prenumeration:** Din Azure-prenumeration fylls i automatiskt. Kontrollera att det är rätt prenumeration.
* **Funktionsapp:** Välj funktionsappens namn.
* **Funktion:** Välj DpsAdtAllocationFunc.

Spara din information.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Skärmbild av fönstret Information om grupp för registrering av registrering till Välj anpassad (använd Azure-funktion) och ditt IoT-hubbnamn i avsnitten Välj hur du vill tilldela enheter till hubbar och Välj de IoT-hubbar som den här gruppen kan tilldelas. Välj även din prenumeration, funktionsapp i listrutan och se till att välja DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

När du har skapat registreringen används **primärnyckeln** för registreringen senare för att konfigurera enhetssimulatorn för den här artikeln.

### <a name="set-up-the-device-simulator"></a>Konfigurera enhetssimulatorn

Det här exemplet använder en enhetssimulator som innehåller etablering med enhetsetableringstjänsten. Enhetssimulatorn finns i [Azure Digital Twins och IoT Hub exempel på integrering](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) som du laddade ned i [avsnittet](#prerequisites) Förutsättningar.

#### <a name="upload-the-model"></a>Ladda upp modellen

Enhetssimulatorn är en enhet av termostattyp som använder modellen med följande ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Du måste ladda upp den här modellen till Azure Digital Twins innan du kan skapa en tvilling av den här typen för enheten.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Mer information om modeller finns i [*How-to: Manage models*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Konfigurera och köra simulatorn

I kommandofönstret navigerar du till det nedladdade exemplet Azure Digital Twins och *IoT Hub Integration* som du uppackade tidigare och sedan till katalogen *device-simulator.* Installera sedan beroendena för projektet med följande kommando:

```cmd
npm install
```

I enhetssimulatorkatalogen kopierar du sedan .env.template-filen till en ny fil med namnet .env och samlar in följande värden för att fylla i inställningarna:

* PROVISIONING_IDSCOPE: Om du vill hämta det här värdet går du till enhetsetableringstjänsten i [Azure Portal](https://portal.azure.com/)och väljer sedan Översikt i menyalternativen och letar efter fältet *ID-omfång.* 

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Skärmbild av Azure Portal på översiktssidan för enhetsetablering för att kopiera värdet för ID-omfång." lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: Du kan välja ett registrerings-ID för din enhet.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Det här är den primära nyckeln för den registrering som du konfigurerade tidigare. Om du vill hämta det här värdet igen går du till enhetsetableringstjänsten i Azure Portal, väljer *Hantera* registreringar och väljer sedan den registreringsgrupp som du skapade tidigare och kopierar *primärnyckeln*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Skärmbild av Azure Portal av sidan hantera registreringar för enhetsetableringstjänsten för att kopiera värdet för SAS-primärnyckeln." lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

Använd nu värdena ovan för att uppdatera inställningarna för .env-filen.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Spara och stäng filen.

### <a name="start-running-the-device-simulator"></a>Börja köra enhetssimulatorn

Fortfarande i katalogen *device-simulator* i kommandofönstret startar du enhetssimulatorn med följande kommando:

```cmd
node .\adt_custom_register.js
```

Du bör se att enheten har registrerats och anslutits till IoT Hub och sedan börjar skicka meddelanden.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Skärmbild av Fönstret Kommando som visar enhetsregistrering och skicka meddelanden" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Verifiera

På grund av det flöde som du har skapat i den här artikeln registreras enheten automatiskt i Azure Digital Twins. Använd följande [cli Azure Digital Twins kommando](how-to-use-cli.md) för att hitta enhetstvillingen i den Azure Digital Twins som du skapade.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Du bör se tvillingen för enheten som hittas i Azure Digital Twins instansen.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Skärmbild av den Fönstret Kommando som visar den nyligen skapade tvillingen." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Dra tillbaka enheten automatiskt med hjälp IoT Hub livscykelhändelser

I det här avsnittet kommer du att koppla IoT Hub livscykelhändelser för att Azure Digital Twins automatiskt dra tillbaka enheter via sökvägen nedan. Det här är ett utdrag från den fullständiga arkitektur som [visades tidigare](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagram över enhetsflödet Dra tillbaka – ett utdrag ur lösningsarkitekturdiagrammet med siffror som etiketterar avsnitt i flödet. Termostatenheten visas utan anslutningar till Azure-tjänsterna i diagrammet. Data från en manuell åtgärd för att ta bort enhet flödar IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3)." lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Här är en beskrivning av processflödet:
1. En extern eller manuell process utlöser borttagningen av en enhet i IoT Hub.
2. IoT Hub tar bort enheten och genererar en [enhetslivscykelhändelse](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) som dirigeras till en [händelsehubb](../event-hubs/event-hubs-about.md).
3. En Azure-funktion tar bort enhetens tvilling i Azure Digital Twins.

I följande avsnitt går vi igenom stegen för att konfigurera det här enhetsflödet för automatisk tillbaka pension.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Nu ska du skapa en [Azure-händelsehubb för att](../event-hubs/event-hubs-about.md) ta emot IoT Hub livscykelhändelser. 

Följ stegen som beskrivs i [*snabbstarten Skapa en händelsehubb.*](../event-hubs/event-hubs-create.md) Ge händelsehubben *namnet lifecycleevents*. Du använder det här händelsehubbens namn när du IoT Hub och en Azure-funktion i nästa avsnitt.

Skärmbilden nedan visar skapandet av händelsehubben.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Skärmbild av Azure Portal för att skapa en händelsehubb med namnet lifecycleevents." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Skapa SAS-princip för din händelsehubb

Därefter måste du skapa en princip för signatur för delad åtkomst [(SAS)](../event-hubs/authorize-access-shared-access-signature.md) för att konfigurera händelsehubben med din funktionsapp.
Gör
1. Gå till den händelsehubb som du precis skapade i Azure Portal och välj Principer **för delad** åtkomst i menyalternativen till vänster.
2. Välj **Lägg till**. I fönstret *Lägg till SAS-princip* som öppnas anger du ett principnamn och markerar *kryssrutan* Lyssna.
3. Välj **Skapa**.
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Skärmbild av Azure Portal för att lägga till en SAS-princip för händelsehubb." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurera händelsehubb med funktionsapp

Konfigurera sedan Azure-funktionsappen som du konfigurerade i avsnittet [förutsättningar för](#prerequisites) att arbeta med din nya händelsehubb. Du gör detta genom att ange en miljövariabel i funktionsappen med händelsehubbens anslutningssträng.

1. Öppna den princip som du nyss skapade och kopiera värdet För primär nyckel **för anslutningssträng.**

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Skärmbild av Azure Portal för att kopiera anslutningssträngens primära nyckel." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Lägg till anslutningssträngen som en variabel i funktionsappinställningarna med följande Azure CLI-kommando. Kommandot kan köras i [Cloud Shell](https://shell.azure.com)lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Lägga till en funktion som ska inaktiveras IoT Hub livscykelhändelser

I ditt funktionsappsprojekt som [](#prerequisites) du skapade i avsnittet förutsättningar skapar du en ny funktion för att dra tillbaka en befintlig enhet med hjälp IoT Hub livscykelhändelser.

Mer information om livscykelhändelser finns [*i IoT Hub händelser som inte är telemetri.*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) Mer information om hur du använder Event Hubs med Azure Functions finns [*i Azure Event Hubs utlösare för Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Börja med att öppna funktionsappsprojektet i Visual Studio på datorn och följ stegen nedan.

#### <a name="step-1-add-a-new-function"></a>Steg 1: Lägg till en ny funktion
     
Lägg till en ny funktion av typen *Händelsehubbutlösare* i funktionsappsprojektet i Visual Studio.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png" alt-text="Skärmbild av fönstret Visual Studio för att lägga till en Azure-funktion av typen Händelsehubbutlösare i funktionsappsprojektet." lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Steg 2: Fyll i funktionskod

I den nya funktionskodfilen klistrar du in följande kod, byter namn på funktionen `DeleteDeviceInTwinFunc.cs` till och sparar filen.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Steg 3: Publicera funktionsappen till Azure

Publicera projektet med *funktionen DeleteDeviceInTwinFunc.cs* till funktionsappen i Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Skapa en IoT Hub väg för livscykelhändelser

Nu ska du konfigurera en IoT Hub väg för att dirigera enhetslivscykelhändelser. I det här fallet lyssnar du specifikt på enhets borttagningshändelser som identifieras av `if (opType == "deleteDeviceIdentity")` . Detta utlöser borttagningen av det digitala tvillingobjektet och slutför tillbakagången för en enhet och dess digitala tvilling.

Först måste du skapa en händelsehubbslutpunkt i din IoT-hubb. Sedan lägger du till en väg i IoT Hub för att skicka livscykelhändelser till händelsehubbens slutpunkt.
Följ dessa steg för att skapa en händelsehubbslutpunkt:

1. I [Azure Portal](https://portal.azure.com/)navigerar du till den IoT-hubb [](#prerequisites) som du  skapade i avsnittet förutsättningar och väljer Meddelanderoutning i menyalternativen till vänster.
2. Välj fliken **Anpassade slutpunkter.**
3. Välj **+ Lägg till** och välj Event **Hubs för** att lägga till en slutpunkt av händelsehubbtyp.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Skärmbild av fönstret Visual Studio för att lägga till en anpassad slutpunkt för händelsehubben." lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. I fönstret Lägg *till en slutpunkt för händelsehubben* som öppnas väljer du följande värden:
    * **Slutpunktens namn:** Välj ett slutpunktsnamn.
    * **Namnområde för händelsehubb:** Välj händelsehubbens namnområde i listrutan.
    * **Händelsehubbinstans:** Välj händelsehubbens namn som du skapade i föregående steg.
5. Välj **Skapa**. Låt det här fönstret vara öppet för att lägga till en väg i nästa steg.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Skärmbild av fönstret Visual Studio för att lägga till en händelsehubbslutpunkt." lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Nu ska du lägga till en väg som ansluter till slutpunkten som du skapade i steget ovan med en routningsfråga som skickar borttagningshändelserna. Följ dessa steg för att skapa en väg:

1. Gå till fliken *Vägar och* välj Lägg till **för att** lägga till en väg.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Skärmbild av fönstret Visual Studio att lägga till en väg för att skicka händelser." lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. På sidan *Lägg till en* väg som öppnas väljer du följande värden:

   * **Namn:** Välj ett namn för din väg. 
   * **Slutpunkt:** Välj slutpunkten för händelsehubben som du skapade tidigare i listrutan.
   * **Datakälla:** Välj *Enhetslivscykelhändelser.*
   * **Routningsfråga:** Ange `opType='deleteDeviceIdentity'` . Den här frågan begränsar enhetslivscykelns händelser till att endast skicka borttagningshändelserna.

3. Välj **Spara**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Skärmbild av fönstret Azure Portal för att lägga till en väg för att skicka livscykelhändelser." lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

När du har gått igenom det här flödet är allt inställt på att dra tillbaka enheter från slutet till slut.

### <a name="validate"></a>Verifiera

För att utlösa tillbakagången måste du manuellt ta bort enheten från IoT Hub.

Du kan göra detta med ett [Azure CLI-kommando](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) eller i Azure Portal. Följ stegen nedan för att ta bort enheten i Azure Portal:

1. Gå till din IoT-hubb och välj **IoT-enheter** i menyalternativen till vänster. 
2. Du ser en enhet med det enhetsregistrerings-ID som du valde i den [första halvan av den här artikeln.](#auto-provision-device-using-device-provisioning-service) Du kan också välja en annan enhet att ta bort, så länge den har en tvilling i Azure Digital Twins så att du kan kontrollera att tvillingen tas bort automatiskt när enheten har tagits bort.
3. Välj enheten och välj Ta **bort.**

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Skärmbild av den Azure Portal att ta bort enhetstvillingen från IoT-enheterna." lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Det kan ta några minuter att se ändringarna som återspeglas i Azure Digital Twins.

Använd följande [cli Azure Digital Twins kommando](how-to-use-cli.md) för att verifiera att enhetens tvilling i den Azure Digital Twins instansen har tagits bort.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Du bör se att enhetstvillingen inte längre kan hittas Azure Digital Twins instansen.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Skärmbild av den Fönstret Kommando som visar att det inte går att hitta tvillingen." lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver resurserna som skapades i den här artikeln följer du dessa steg för att ta bort dem.

Med hjälp Azure Cloud Shell lokala Azure CLI kan du ta bort alla Azure-resurser i en resursgrupp med [kommandot az group delete.](/cli/azure/group#az_group_delete) Detta tar bort resursgruppen. Azure Digital Twins instansen; IoT-hubben och registreringen av hubbenheten; Event Grid-ämnet och tillhörande prenumerationer; event hubs-namnområdet och båda Azure Functions, inklusive associerade resurser som lagring.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ta sedan bort projektexempelmappen som du laddade ned från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

De digitala tvillingarna som skapas för enheterna lagras som en platt hierarki i Azure Digital Twins, men de kan berikas med modellinformation och en hierarki på flera nivåer för organisationen. Om du vill veta mer om det här konceptet kan du läsa:

* [*Koncept: Digitala tvillingar och tvillinggrafen*](concepts-twins-graph.md)

Mer information om hur du använder HTTP-begäranden med Azure-funktioner finns i:

* [*Azure HTTP-begärandeutlösare för Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med hjälp av modell- och diagramdata som redan lagras i Azure Digital Twins. Mer information om hur du hanterar, uppgraderar och hämtar information från twins-diagrammet finns i följande:

* [*Så här hanterar du en digital tvilling*](how-to-manage-twin.md)
* [*Så här frågar du efter tvillingdiagrammet*](how-to-query-graph.md)