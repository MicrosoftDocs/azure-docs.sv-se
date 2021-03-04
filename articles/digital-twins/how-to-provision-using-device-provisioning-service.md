---
title: Hantera enheter automatiskt med hjälp av DPS
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar en automatiserad process för att etablera och dra tillbaka IoT-enheter i Azure Digitals med hjälp av DPS (Device Provisioning service).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 24dac044982d59e93da17ee75190f378d5e3cdea
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050927"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Hantera enheter automatiskt i Azure Digitals med hjälp av enhets etablerings tjänsten (DPS)

I den här artikeln får du lära dig hur du integrerar Azure Digitals med [enhets etablerings tjänsten (DPS)](../iot-dps/about-iot-dps.md).

Lösningen som beskrivs i den här artikeln gör att du kan automatisera processen för att **_etablera_** och **_dra tillbaka_** IoT Hub enheter i Azure Digitals, med hjälp av enhets etablerings tjänsten. 

Mer information om hur du _etablerar_ och drar _tillbaka_ faser och för att bättre förstå de allmänna enhets hanterings faser som är gemensamma för alla Enterprise IoT-projekt finns i [avsnittet *enhets livs cykel*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) i IoT Hub enhets hanteringens dokumentation.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera etableringen måste du ha en **digital Azure-instans** som innehåller modeller och dubbla. Den här instansen bör också konfigureras med möjlighet att uppdatera Digital dubbel information baserat på data. 

Om du inte redan har konfigurerat den här inställningen kan du skapa den genom att följa självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md). Självstudien vägleder dig genom att konfigurera en digital Azure-instans med modeller och dubbla, en ansluten Azure- [IoT Hub](../iot-hub/about-iot-hub.md)och flera [Azure Functions](../azure-functions/functions-overview.md) för att sprida data flödet.

Du behöver följande värden senare i den här artikeln när du konfigurerar din instans. Om du behöver samla in värdena igen kan du använda länkarna nedan för instruktioner.
* Azure Digitals dubbla instans **_värd namn_** ([Sök i portalen](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Anslutnings sträng_** för Azure Event Hubs-anslutningssträng ([Sök i portalen](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

I det här exemplet används även en **enhets Simulator** som inkluderar etablering med Device Provisioning-tjänsten. Enhets simulatorn finns här: [Azure Digitals, dubbla och IoT Hub integrations exempel](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Hämta exempelprojektet på datorn genom att gå till exempel länken och välja *Hämta zip* -knappen under rubriken. Zippa upp den hämtade mappen.

Enhets simulatorn baseras på **Node.js**, version 10.0. x eller senare. [*Förbereda utvecklings miljön*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) beskriver hur du installerar Node.js för den här själv studie kursen på antingen Windows eller Linux.

## <a name="solution-architecture"></a>Lösningsarkitektur

Bilden nedan visar arkitekturen för den här lösningen med hjälp av Azure Digitals dubbla med Device Provisioning-tjänsten. Det visar både enhets etableringen och indragnings flödet.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="En vy över en enhet och flera Azure-tjänster i ett scenario från slut punkt till slut punkt. Data flödar fram och tillbaka mellan en termostat enhet och DPS. Data flödar också ut från DPS till IoT Hub och till Azure Digitals dubbla steg via en Azure-funktion med etiketten &quot;allokering&quot;. Data från en manuell &quot;ta bort enhet&quot;-åtgärd flödar genom IoT Hub > Event Hubs > Azure Functions > Azures digitala dubbla.":::

Den här artikeln är indelad i två delar:
* [*Automatisk etablering av enhet med enhets etablerings tjänst*](#auto-provision-device-using-device-provisioning-service)
* [*Automatisk borttagning av enhet med IoT Hub livs cykel händelser*](#auto-retire-device-using-iot-hub-lifecycle-events)

För djupare förklaringar av varje steg i arkitekturen, se deras enskilda avsnitt senare i artikeln.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Automatisk etablering av enhet med enhets etablerings tjänst

I det här avsnittet ska du ansluta enhets etablerings tjänsten till Azure Digital-enheter för att etablera enheter automatiskt genom sökvägen nedan. Detta är ett utdrag från den fullständiga arkitekturen som visas [ovan](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Etablera flöde – ett utdrag av lösnings arkitektur diagrammet med siffror som etiketterar sektioner i flödet. Data flödar fram och tillbaka mellan en termostat-enhet och DPS (1 för enhets > DPS och 5 för DPS > Device). Data flödar också ut från DPS till IoT Hub (4) och till Azure Digital-dubblare (3) genom en Azure-funktion med etiketten &quot;allokering&quot; (2).":::

Här följer en beskrivning av process flödet:
1. Enheten kontaktar DPS-slutpunkten och skickar identifierings information för att bevisa sin identitet.
2. DPS verifierar enhetens identitet genom att verifiera registrerings-ID och nyckel mot registrerings listan och anropar en [Azure-funktion](../azure-functions/functions-overview.md) för att utföra tilldelningen.
3. Azure-funktionen skapar [en ny enhet](concepts-twins-graph.md) i Azure Digitals dubbla enheter för enheten.
4. DPS registrerar enheten med en IoT-hubb och fyller enhetens önskade dubbla tillstånd.
5. IoT Hub returnerar information om enhets-ID och anslutnings information för IoT Hub till enheten. Enheten kan nu ansluta till IoT Hub.

Följande avsnitt beskriver stegen för att konfigurera det här enhets flödet för automatisk etablering.

### <a name="create-a-device-provisioning-service"></a>Skapa en enhets etablerings tjänst

När en ny enhet tillhandahålls med enhets etablerings tjänsten kan en ny enhet skapas i Azure Digitals.

Skapa en instans av Device Provisioning-tjänsten som ska användas för att etablera IoT-enheter. Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: konfigurera IoT Hub Device Provisioning service med Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Följande Azure CLI-kommando skapar en enhets etablerings tjänst. Du måste ange ett namn, en resurs grupp och en region. Kommandot kan köras i [Cloud Shell](https://shell.azure.com)eller lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Därefter skapar du en HTTP-begäran-utlöst funktion i en Function-app. Du kan använda Function-appen som skapats i kursen från slut punkt till slut punkt ([*Självstudier: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)) eller din egen.

Den här funktionen kommer att användas av enhets etablerings tjänsten i en [anpassad resursallokeringsprincip](../iot-dps/how-to-use-custom-allocation-policies.md) för att etablera en ny enhet. Mer information om hur du använder HTTP-förfrågningar med Azure Functions finns i [*Azure http-begäran utlösare för Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

Lägg till en ny funktion i projektet för Function-appen. Lägg också till ett nytt NuGet-paket i projektet: `Microsoft.Azure.Devices.Provisioning.Service` .

I den nyligen skapade funktions kod filen klistrar du in följande kod.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

Spara filen och publicera om din Function-app. Anvisningar om hur du publicerar Function-appen finns i avsnittet [*Publicera appen*](tutorial-end-to-end.md#publish-the-app) i slut punkt till slut punkt.

### <a name="configure-your-function"></a>Konfigurera funktionen

Därefter måste du ställa in miljövariabler i din Function-app från tidigare, som innehåller referensen till den Azure Digital-instans som du har skapat. Om du använde självstudierna från slut punkt till slut punkt ([*Självstudier: ansluta en slut punkt till slut punkt*](tutorial-end-to-end.md)) är inställningen redan konfigurerad.

Lägg till inställningen med följande Azure CLI-kommando:

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Se till att roll tilldelningen behörigheter och hanterad identitet är korrekt konfigurerad för Function-appen, enligt beskrivningen i avsnittet [*tilldela behörigheter till Function-appen*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) i slut punkt till slut punkt.

### <a name="create-device-provisioning-enrollment"></a>Skapa registrering av enhets etablering

Därefter måste du skapa en registrering i enhets etablerings tjänsten med hjälp av en **anpassad tilldelnings funktion**. Följ anvisningarna i avsnittet [*skapa registrering*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) och [*Härled unika enhets nycklar*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) i artikeln enhets etablerings tjänster om anpassade allokeringsregler.

När du går igenom flödet länkar du registreringen till funktionen som du nyss skapade genom att välja din funktion under steget för att **välja hur du vill tilldela enheter till hubbar**. När du har skapat registreringen används registrerings namnet och den primära eller sekundära SAS-nyckeln senare för att konfigurera enhets simulatorn för den här artikeln.

### <a name="set-up-the-device-simulator"></a>Konfigurera enhets simulatorn

I det här exemplet används en enhets simulator som inkluderar etablering med enhets etablerings tjänsten. Enhets simulatorn finns här: [Azure Digitals, dubbla och IoT Hub integrations exempel](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Om du inte redan har hämtat exemplet hämtar du det nu genom att gå till exempel länken och välja *Hämta zip* -knappen under rubriken. Zippa upp den hämtade mappen.

Öppna ett kommando fönster och navigera till den hämtade mappen och sedan till *Device Simulator-* katalogen. Installera beroenden för projektet med följande kommando:

```cmd
npm install
```

Kopiera sedan filen *. miljö. template* till en ny fil med namnet *. kuvert* och fyll i följande inställningar:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Spara och stäng filen.

### <a name="start-running-the-device-simulator"></a>Starta körning av enhets simulatorn

I mappen *Device-Simulator* i kommando fönstret startar du enhets simulatorn med följande kommando:

```cmd
node .\adt_custom_register.js
```

Du bör se att enheten registreras och är ansluten till IoT Hub och sedan börjar skicka meddelanden.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Fönstret Kommando visar enhets registrering och sändning av meddelanden":::

### <a name="validate"></a>Verifiera

Som ett resultat av flödet som du har skapat i den här artikeln registreras enheten automatiskt i digitala Azure-enheter. Använd följande [Azure Digitals flätade CLI](how-to-use-cli.md) -kommando för att hitta enhetens enhet i Azure Digitals-instansen som du skapade.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Du bör se den dubbla av enheten som finns på Azure Digitals-instansen.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Fönstret Kommando visar nyskapade dubbla":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatisk borttagning av enhet med IoT Hub livs cykel händelser

I det här avsnittet ska du lägga till IoT Hub livs cykel händelser till Azure Digital-meddelanden för att automatiskt dra tillbaka enheter via sökvägen nedan. Detta är ett utdrag från den fullständiga arkitekturen som visas [ovan](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Dra tillbaka enhets flödet – ett utdrag av lösnings arkitektur diagrammet med siffror som delar av flödet. Termostat-enheten visas utan anslutningar till Azure-tjänsterna i diagrammet. Data från en manuell &quot;ta bort enhet&quot;-åtgärd flödar genom IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digitals dubblare (3).":::

Här följer en beskrivning av process flödet:
1. En extern eller manuell process utlöser borttagningen av en enhet i IoT Hub.
2. IoT Hub tar bort enheten och skapar en [enhets livs cykel](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) händelse som kommer att dirigeras till en [Event Hub](../event-hubs/event-hubs-about.md).
3. En Azure-funktion tar bort den dubbla enheten i Azure Digitals.

I följande avsnitt går vi igenom stegen för att ställa in det här flödet för automatisk dragande av enhet.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Nu måste du skapa en Azure [Event Hub](../event-hubs/event-hubs-about.md)som ska användas för att ta emot IoT Hub livs cykel händelser. 

Gå igenom stegen som beskrivs i snabb starten för att [*skapa en Event Hub*](../event-hubs/event-hubs-create.md) med följande information:
* Om du använder självstudierna från slut punkt till slut punkt ([*självstudie: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)) kan du återanvända den resurs grupp som du skapade för självstudierna från slut punkt till slut punkt.
* Namnge din Event Hub- *lifecycleevents* eller något annat alternativ och kom ihåg namn området du skapade. Du kommer att använda dessa när du ställer in livs cykel funktionen och IoT Hub vägen i nästa avsnitt.

### <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Nästa steg är att skapa en Event Hubs-utlöst funktion i en funktionsapp. Du kan använda Function-appen som skapats i kursen från slut punkt till slut punkt ([*Självstudier: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)) eller din egen. 

Namnge Event Hub-utlösaren *lifecycleevents* och Anslut Event Hub-utlösaren till händelsehubben som du skapade i föregående steg. Om du har använt ett annat händelsehubben, ändrar du det så att det matchar i Utlösarens namn nedan.

Den här funktionen använder IoT Hub enhetens livs cykel händelse för att dra tillbaka en befintlig enhet. Mer information om livs cykel händelser finns i [*IoT Hub händelser som inte är telemetri*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Mer information om hur du använder Event Hubs med Azure Functions finns i [*Azure Event Hubs-utlösare för Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

I din publicerade Function-app lägger du till en ny funktions klass av typen *Event Hub-utlösare* och klistrar in koden nedan.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

Spara projektet och publicera sedan Function-appen igen. Anvisningar om hur du publicerar Function-appen finns i avsnittet [*Publicera appen*](tutorial-end-to-end.md#publish-the-app) i slut punkt till slut punkt.

### <a name="configure-your-function"></a>Konfigurera funktionen

Därefter måste du ställa in miljövariabler i din Function-app från tidigare, som innehåller referensen till den Azure Digital-instans som du har skapat och Event Hub. Om du använde självstudien från slut punkt till slut punkt ([*Självstudier: ansluta en slut punkt till slut punkt*](./tutorial-end-to-end.md)) är den första inställningen redan konfigurerad.

Lägg till inställningen med detta Azure CLI-kommando. Kommandot kan köras i [Cloud Shell](https://shell.azure.com)eller lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Sedan måste du konfigurera funktions miljö variabeln för att ansluta till den nyss skapade händelsehubben.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Se till att roll tilldelningen behörigheter och hanterad identitet är korrekt konfigurerad för Function-appen, enligt beskrivningen i avsnittet [*tilldela behörigheter till Function-appen*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) i slut punkt till slut punkt.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Skapa en IoT Hub väg för livs cykel händelser

Nu måste du konfigurera en IoT Hub väg, för att dirigera enhets livs cykel händelser. I det här fallet kommer du specifikt att lyssna på enhets borttagnings händelser, identifierade av `if (opType == "deleteDeviceIdentity")` . Detta utlöser borttagningen av det digitala dubbla objektet, och slutför indragningen av en enhet och dess digitala enhet.

Instruktioner för att skapa en IoT Hub väg beskrivs i den här artikeln: [*använd IoT Hub meddelanderoutning för att skicka meddelanden från enhet till moln till olika slut punkter*](../iot-hub/iot-hub-devguide-messages-d2c.md). Avsnittet *icke-telemetri-händelser* förklarar att du kan använda **enhets livs cykel händelser** som data källa för vägen.

De steg du måste gå igenom för installationen är:
1. Skapa en anpassad IoT Hub Event Hub-slutpunkt. Den här slut punkten måste vara riktad mot händelsehubben som du skapade i avsnittet [*skapa en Event Hub*](#create-an-event-hub) .
2. Lägg till en *enhets livs cykel händelse* väg. Använd slut punkten som skapades i föregående steg. Du kan begränsa enhetens livs cykel händelser så att de endast skickar borttagnings händelser genom att lägga till cirkulations frågan `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Lägg till en väg":::

När du har gått igenom det här flödet är allt inställt på att dra tillbaka enheter från slut punkt till slut punkt.

### <a name="validate"></a>Verifiera

Om du vill utlösa processen för pensionering måste du manuellt ta bort enheten från IoT Hub.

I den [första halvan av den här artikeln](#auto-provision-device-using-device-provisioning-service)skapade du en enhet i IoT Hub och en motsvarande digital enhet. 

Gå nu till IoT Hub och ta bort enheten (du kan göra detta med ett [Azure CLI-kommando](/cli/azure/ext/azure-iot/iot/hub/module-identity?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_iot_hub_module_identity_delete) eller i [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Enheten tas bort automatiskt från digitala Azure-enheter. 

Använd följande [Azure Digitals flätade CLI](how-to-use-cli.md) -kommando för att kontrol lera att enhetens enhet i Azure Digitals-instansen har tagits bort.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Du bör se att enhetens enhets enhet inte kan hittas på Azures digitala dubbla instanser längre.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Fönstret Kommando visar dubbla hittades inte":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver resurserna som skapats i den här artikeln följer du stegen nedan för att ta bort dem.

Med hjälp av Azure Cloud Shell eller lokalt Azure CLI kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Detta tar bort resurs gruppen. Azure Digitals dubbla instanser, IoT-hubben och nav enhets registreringen. avsnittet Event Grid och associerade prenumerationer. Event Hub-namnområdet och båda Azure Functions appar, inklusive tillhör ande resurser som lagring.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ta sedan bort den Project-exempel mapp som du laddade ned från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

De digitala multiplarna som skapas för enheterna lagras som en platt hierarki i Azure Digitals, men de kan berikas med modell information och en hierarki med flera nivåer för organisationen. Läs mer om det här konceptet i:

* [*Koncept: digitala och dubbla grafer*](concepts-twins-graph.md)

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med modell-och diagram data som redan finns lagrade i Azure Digitals. Mer information om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram finns i följande avsnitt:

* [*Anvisningar: hantera en digital, dubbel*](how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](how-to-query-graph.md)