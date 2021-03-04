---
title: 'Självstudie: Anslut en lösning från slut punkt till slut punkt'
titleSuffix: Azure Digital Twins
description: Självstudie för att bygga ut en Azure digital-lösning från slut punkt till slut punkt som drivs av enhets data.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d46a20079919f052ed343c9702ba02ce7f109b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "102036186"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Självstudie: Bygg ut en lösning från slut punkt till slut punkt

Om du vill konfigurera en fullständig lösning från slut punkt till slut punkt som drivs av real tids data från din miljö kan du ansluta din Azure Digital-instans till andra Azure-tjänster för hantering av enheter och data.

I den här självstudien kommer du att...
> [!div class="checklist"]
> * Konfigurera en digital Azure-instans
> * Lär dig mer om hur du skapar ett exempel scenario och skapar en instans av de fördefinierade komponenterna
> * Använd en [Azure Functions](../azure-functions/functions-overview.md) app för att dirigera simulerad telemetri från en [IoT Hub](../iot-hub/about-iot-hub.md) -enhet till digitala dubbla egenskaper
> * Sprid ändringar via det **dubbla diagrammet** genom att bearbeta digitala dubbla aviseringar med Azure Functions, slut punkter och vägar

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Kom igång med att skapa scenariot

Det exempel projekt som används i den här självstudien visar ett verkligt **skapande scenario** med en våning, ett rum och en termostat-enhet. Dessa komponenter visas digitalt i en digital Azure-instans, som sedan kommer att anslutas till [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)och två [Azure Functions](../azure-functions/functions-overview.md) för att under lätta data flödet.

Nedan visas ett diagram som representerar det fullständiga scenariot. 

Först ska du skapa Azure Digitals-instansen (**avsnitt A** i diagrammet) och sedan ställa in telemetri-dataflödet i de digitala dubblarna (**pil B**) och sedan konfigurera data spridningen via det dubbla diagrammet (**pil C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Bild av det fullständiga Bygg scenariot. Visar data som flödar från en enhet till IoT Hub, via en Azure-funktion (pil B) till en digital Azure-instans (del A), sedan från Event Grid till en annan Azure-funktion för bearbetning (pil C)":::

Om du vill arbeta igenom scenariot interagerar du med komponenterna i den fördefinierade exempel appen som du laddade ned tidigare.

Här följer de komponenter som implementeras av *AdtSampleApp* -exempel appen för bygg scenariot:
* Enhetsautentisering 
* Användnings exempel för [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (hittades i *CommandLoop.cs*)
* Konsol gränssnitt för att anropa Azure Digitals dubbla API: er
* *SampleClientApp* – ett exempel på Azure Digitals dubbla lösningar
* *SampleFunctionsApp* – en Azure Functions app som uppdaterar ditt Azure Digitals-diagram till följd av telemetri från IoT Hub och Azures digitala dubbla händelser

### <a name="instantiate-the-pre-created-twin-graph"></a>Instansiera det förskapade dubbla diagrammet

Först ska du använda *AdtSampleApp* -lösningen från exempelprojektet för att bygga upp Azure Digitals skärnings punkt från end-to-end-scenariot (**del A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Ett utdrag från det fullständiga scenariot för att skapa scenario bild markering A, Azure Digitals dubbla instanser":::

Kör projektet med den här knappen i verktygsfältet i Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Start knappen i Visual Studio (SampleClientApp-projekt)":::

Ett konsol fönster öppnas, utför autentisering och vänta på ett kommando. I den här konsolen kör du nästa kommando för att skapa en instans av exemplet på Azure Digitals dubbla lösningar.

> [!IMPORTANT]
> Om du redan har digitala dubbla och relationer i din Azure Digital-instansen tar du bort dem genom att köra det här kommandot och ersätta dem med de dubbla och relationerna i exempel scenariot.

```cmd/sh
SetupBuildingScenario
```

Utdata från det här kommandot är en serie med bekräftelse meddelanden som tre [**digitala dubblare**](concepts-twins-graph.md) skapas och ansluts i din Azure Digitals-instans: en våning med namnet *floor1*, ett rum med namnet *Room21* och en temperatur sensor med namnet *thermostat67*. Dessa digitala delar representerar de entiteter som skulle finnas i en verklig miljö.

De är anslutna via relationer till följande [**dubbla diagram**](concepts-twins-graph.md). Det dubbla diagrammet representerar miljön som helhet, inklusive hur entiteterna interagerar med och relaterar till varandra.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Ett diagram som visar att floor1 innehåller Room21 och Room21 innehåller thermostat67" border="false":::

Du kan kontrol lera de dubbla som skapats genom att köra följande kommando, som frågar den anslutna Azure Digital-instansen för alla digitala dubbla, som den innehåller:

```cmd/sh
Query
```

>[!TIP]
> Den här förenklade metoden tillhandahålls som en del av _**AdtE2ESample**_ -projektet. Utanför kontexten för den här exempel koden kan du när som helst fråga efter alla dubbla i din instans med hjälp av API: [er för frågor](/rest/api/digital-twins/dataplane/query) eller [CLI-kommandon](how-to-use-cli.md).
>
> Här är hela fråge texten för att hämta alla digitala enheter i din instans:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Därefter kan du sluta köra projektet. Låt lösningen vara öppen i Visual Studio, men eftersom du kommer att fortsätta att använda den i kursen.

## <a name="set-up-the-sample-function-app"></a>Konfigurera exempel funktions programmet

Nästa steg är att konfigurera en [Azure Functions-app](../azure-functions/functions-overview.md) som ska användas i den här självstudien för att bearbeta data. Function-appen, *SampleFunctionsApp*, innehåller två funktioner:
* *ProcessHubToDTEvents*: bearbetar inkommande IoT Hub data och uppdaterar Azure Digitals på motsvarande sätt
* *ProcessDTRoutedData*: bearbetar data från digitala dubbla och uppdaterar de överordnade i Azures digitala dubbla

I det här avsnittet ska du publicera den fördefinierade Function-appen och se till att Function-appen kan komma åt Azures digitala dubbla, genom att tilldela den en Azure Active Directory (Azure AD)-identitet. Genom att slutföra de här stegen får resten av självstudien att använda funktionerna i Function-appen. 

I Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet finns Function-appen i _**SampleFunctionsApp**_ -projektfilen. Du kan visa det i fönstret *Solution Explorer* .

### <a name="update-dependencies"></a>Uppdatera beroenden

Innan du publicerar appen är det en bra idé att se till att dina beroenden är uppdaterade och se till att du har den senaste versionen av alla paket som ingår.

I fönstret *Solution Explorer* expanderar du _**SampleFunctionsApp** >-beroenden_. Högerklicka på *paket* och välj *Hantera NuGet-paket...*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: hantera NuGet-paket för SampleFunctionsApp-projektet" border="false":::

Då öppnas paket hanteraren för NuGet. Välj fliken *uppdateringar* och om det finns paket som ska uppdateras markerar du kryss rutan för att *välja alla paket*. Tryck sedan på *Uppdatera*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: välja att uppdatera alla paket i NuGet Package Manager":::

### <a name="publish-the-app"></a>Publicera appen

Gå tillbaka till Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet och leta upp _**SampleFunctionsApp**_ -projektet i fönstret *Solution Explorer* .

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="assign-permissions-to-the-function-app"></a>Tilldela behörigheter till Function-appen

Om du vill göra det möjligt för Function-appen att komma åt Azure Digitals, är nästa steg att konfigurera en app-inställning, tilldela appen en Systemhanterad Azure AD-identitet och ge den identiteten *Azure Digitals data ägar* roll i Azure Digital-instansen. Den här rollen krävs för alla användare eller funktioner som vill utföra många data Plans aktiviteter på instansen. Du kan läsa mer om säkerhets-och roll tilldelningar i [*begrepp: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md).

I Azure Cloud Shell använder du följande kommando för att ange en program inställning som din Function-app ska använda för att referera till din Azure Digital-instansen. Fyll i plats hållarna med information om dina resurser (kom ihåg att URL: en för Azure Digital-instansen är värd namnet föregånget av *https://*).

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

Utdata är listan över inställningar för Azure-funktionen som nu ska innehålla en post som kallas **ADT_SERVICE_URL**.

Använd följande kommando för att skapa den systemhanterade identiteten. Leta efter fältet **principalId** i utdata.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Använd **principalId** -värdet från utdata i följande kommando för att tilldela Function-appens identitet till den *digitala Azure-dataägarens data ägar* roll för Azure Digitals-instansen.

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

Resultatet av det här kommandot är information om den roll tilldelning som du har skapat. Function-appen har nu åtkomst behörighet till din Azure Digital-instansen.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Bearbeta simulerad telemetri från en IoT Hub-enhet

Ett digitalt Azure-diagram är avsett att drivas av telemetri från riktiga enheter. 

I det här steget ansluter du en simulerad termostat-enhet som är registrerad i [IoT Hub](../iot-hub/about-iot-hub.md) till den digitala dubbla som representerar den i Azure Digitals dubbla. Eftersom den simulerade enheten avger telemetri, kommer data att dirigeras via *ProcessHubToDTEvents* Azure-funktionen som utlöser en motsvarande uppdatering i den digitala dubbla. På så sätt förblir den digitala enheten uppdaterad med den verkliga enhetens data. I Azure Digitals dubbla, kallas processen för att dirigera händelse data från en plats till en annan kallas för [**Routing Events**](concepts-route-events.md).

Detta sker i den här delen av scenariot från slut punkt till slut punkt (**pil B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Ett utdrag från det fullständiga scenariot för att skapa scenariot, pil B, elementen innan Azure Digitals: enhet, IoT Hub och första Azure Function":::

Här är de åtgärder som du ska utföra för att konfigurera den här enhets anslutningen:
1. Skapa en IoT-hubb som ska hantera den simulerade enheten
2. Anslut IoT-hubben till lämplig Azure-funktion genom att konfigurera en händelse prenumeration
3. Registrera den simulerade enheten i IoT Hub
4. Kör den simulerade enheten och generera telemetri
5. Fråga Azure Digital-dubblare för att se live-resultaten

### <a name="create-an-iot-hub-instance"></a>Skapa en IoT Hub-instans

Digitala Azure-enheter är utformade för att fungera tillsammans med [IoT Hub](../iot-hub/about-iot-hub.md), en Azure-tjänst för att hantera enheter och deras data. I det här steget ska du konfigurera en IoT-hubb som hanterar exempel enheten i den här självstudien.

I Azure Cloud Shell använder du det här kommandot för att skapa en ny IoT-hubb:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Utdata från det här kommandot är information om IoT-hubben som skapades.

Spara det **namn** som du gav din IoT Hub. Du ska använda det senare.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Anslut IoT-hubben till Azure Function

Sedan ansluter du IoT-hubben till Azure-funktionen *ProcessHubToDTEvents* i Function-appen som du publicerade tidigare, så att data kan flöda från enheten i IoT Hub via funktionen, som uppdaterar Azure Digitals dubbla.

Det gör du genom att skapa en **händelse prenumeration** på IoT Hub, med Azure Function som slut punkt. Detta "prenumererar"-funktionen på händelser som inträffar i IoT Hub.

I [Azure Portal](https://portal.azure.com/)navigerar du till din nyligen skapade IoT Hub genom att söka efter dess namn i det övre Sök fältet. Välj *händelser* på menyn hubb och välj *+ händelse prenumeration*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: IoT Hub händelse prenumeration":::

Då öppnas sidan *Skapa händelse prenumeration* .

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: skapa händelse prenumeration":::

Fyll i fälten enligt följande (fält som är fyllda som standard berörs inte):
* *information om*  >  händelse prenumeration **Namn**: ge din händelse prenumeration ett namn.
* *ämnes information*  >  **System ämnets namn**: Ange ett namn som ska användas i avsnittet system. 
* *händelse typer*  >  **Filtrera till händelse typer**: Välj *enhets telemetri* på Meny alternativen.
* *slut punkts information*  >  **Slut punkts typ**: Välj *Azure Function* från meny alternativen.
* *slut punkts information*  >  **Slut punkt**: Tryck på länken *Välj en slut punkt* . Då öppnas ett *Välj Azure Function* -fönster: :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal händelse prenumeration: Välj Azure Function" border="false":::
    - Fyll i din **prenumeration**, **resurs grupp**, **Function-app** och **Function** (*ProcessHubToDTEvents*). Vissa av dessa kan fyllas i automatiskt när du har valt prenumerationen.
    - **Bekräfta val** av träff.

Gå tillbaka till sidan *Skapa händelse prenumeration* och tryck på **skapa**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registrera den simulerade enheten med IoT Hub 

Det här avsnittet skapar en enhets representation i IoT Hub med ID- *thermostat67*. Den simulerade enheten ansluter till detta, och det här är hur telemetri-händelser kommer från enheten till IoT Hub, där den prenumererade Azure-funktionen från föregående steg lyssnar, redo att hämta händelserna och fortsätta att bearbeta.

I Azure Cloud Shell skapar du en enhet i IoT Hub med följande kommando:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Utdata är information om enheten som skapades.

### <a name="configure-and-run-the-simulation"></a>Konfigurera och kör simuleringen

Konfigurera sedan enhetssimulatorn för att skicka data till IoT Hub-instansen.

Börja med att hämta *anslutnings strängen för IoT Hub* med det här kommandot:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Hämta sedan *enhetens anslutningssträng* med det här kommandot:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Du kopplar dessa värden till enhets Simulator koden i det lokala projektet för att ansluta simulatorn till den här IoT-hubben och IoT Hub-enheten.

Öppna i ett nytt Visual Studio-fönster (från den hämtade Solution-mappen) _enhets simulatorn > **DeviceSimulator. SLN**_.

>[!NOTE]
> Nu bör du ha två Visual Studio-fönster, ett med _**DeviceSimulator. SLN**_ och ett från tidigare med _**AdtE2ESample. SLN**_.

I fönstret *Solution Explorer* i det nya Visual Studio-fönstret väljer du _DeviceSimulator/**AzureIoTHub.cs**_ för att öppna den i redigerings fönstret. Ändra följande anslutnings sträng värden till värdena som du samlade in ovan:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Spara filen.

Nu kan du se resultatet av den data simulering som du har ställt in genom att köra **DeviceSimulator** -projektet med den här knappen i verktygsfältet:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Start knappen i Visual Studio (DeviceSimulator-projekt)":::

Ett konsol fönster öppnas och visar simulerade simulerings meddelanden för temperatur. De skickas till IoT Hub, där de sedan hämtas och bearbetas av Azure-funktionen.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Konsol utmatning av enhets simulatorn som visar hur temperatur telemetri skickas":::

Du behöver inte göra något annat i den här konsolen, men lämna det samtidigt som du slutför nästa steg.

### <a name="see-the-results-in-azure-digital-twins"></a>Se resultatet i Azure Digitals flätas

*ProcessHubToDTEvents* -funktionen som du publicerade tidigare lyssnar på IoT Hub data och anropar ett digitalt Azure-API för att uppdatera *temperatur* egenskapen på *thermostat67* -dubbla.

Om du vill visa data från den digitala sidan av Azures dubbla sidor går du till Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet och kör projektet.

I fönstret projekt konsol som öppnas kör du följande kommando för att få de temperaturer som rapporteras av den digitala dubbla *thermostat67*:

```cmd
ObserveProperties thermostat67 Temperature
```

Du bör se de direktsända uppdaterade temperaturerna *från din Azure Digital-instans* som loggas till-konsolen varannan sekund.

>[!NOTE]
> Det kan ta några sekunder innan data från enheten sprids till den dubbla. De första temperatur avläsningarna kan visas som 0 innan data börjar tas emot.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Konsol utdata som visar loggen över temperatur meddelanden från digitala dubbla thermostat67":::

När du har verifierat att det fungerar som det ska kan du sluta köra båda projekten. Se till att Visual Studio-Fönstren är öppna, eftersom du fortsätter att använda dem i resten av självstudien.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Sprid Azure Digitals dubbla händelser genom grafen

Hittills i den här självstudien har du sett hur Azure Digital-enheter kan uppdateras från externa enhets data. Härnäst får du se hur ändringar i en Digitals, som kan spridas genom Azure Digitals grafer, med andra ord, så att du kan uppdatera dubbla från service-interna data.

Om du vill göra detta använder du Azure-funktionen *ProcessDTRoutedData* för att uppdatera en *rummets* dubbla när den anslutna *termostat* -sidan är uppdaterad. Detta sker i den här delen av scenariot från slut punkt till slut punkt (**pil C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Ett utdrag från det fullständiga scenariot för att skapa scenario bild markering C, elementen efter Azure Digitals dubbla: Event Grid och andra Azure Function":::

Här är de åtgärder som du ska utföra för att konfigurera det här data flödet:
1. Skapa en Event Grid slut punkt i Azure Digitals dubbla, som ansluter instansen till Event Grid
2. Konfigurera en väg i Azure Digitals flätas för att skicka dubbla egenskaps ändrings händelser till slut punkten
3. Distribuera en Azure Functions-app som lyssnar (via [Event Grid](../event-grid/overview.md)) på slut punkten och uppdaterar andra dubbla på motsvarande sätt
4. Kör den simulerade enheten och fråga Azure Digital-dubblare för att se live-resultaten

### <a name="set-up-endpoint"></a>Konfigurera slut punkt

[Event Grid](../event-grid/overview.md) är en Azure-tjänst som hjälper dig att dirigera och leverera händelser som kommer från Azure-tjänster till andra platser i Azure. Du kan skapa ett [Event Grid-ämne](../event-grid/concepts.md) om du vill samla in vissa händelser från en källa och prenumeranter kan sedan lyssna på ämnet för att ta emot händelserna när de kommer till.

I det här avsnittet ska du skapa ett event Grid-ämne och sedan skapa en slut punkt i Azure Digitals dubbla, som pekar (skickar händelser) till det ämnet. 

I Azure Cloud Shell kör du följande kommando för att skapa ett event Grid-ämne:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Om du vill skapa en lista med namn på Azure-regioner som kan skickas till kommandon i Azure CLI kör du följande kommando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Utdata från det här kommandot är information om det händelse rutnäts avsnitt som du har skapat.

Skapa sedan en Event Grid slut punkt i Azure Digitals, som ansluter din instans till ditt event Grid-ämne. Använd kommandot nedan och fyll i plats hållarnas fält efter behov:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Utdata från det här kommandot är information om den slut punkt som du har skapat.

Du kan också kontrol lera att slut punkten har skapats genom att köra följande kommando för att fråga din Azure Digital-instansen för den här slut punkten:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Leta efter `provisioningState` fältet i utdata och kontrol lera att värdet är "lyckades". Det kan också stå "etablering", vilket innebär att slut punkten fortfarande skapas. I det här fallet väntar du några sekunder och kör kommandot igen för att kontrol lera att det har slutförts.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Resultat av slut punkts frågan, som visar slut punkten med en provisioningState slutförd":::

Spara de namn som du gav ditt **Event Grid-ämne** och din event Grid **slut punkt** i Azure Digitals, dubbla. Du kommer att använda dem senare.

### <a name="set-up-route"></a>Konfigurera väg

Skapa sedan en Azure Digital-väg som skickar händelser till Event Grid slut punkten som du nyss skapade.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Utdata från det här kommandot är viss information om den väg som du har skapat.

>[!NOTE]
>Slut punkter (från föregående steg) måste ha slutfört etableringen innan du kan konfigurera en händelse väg som använder dem. Om det inte går att skapa en väg eftersom slut punkterna inte är klara, väntar du några minuter och försöker sedan igen.

#### <a name="connect-the-function-to-event-grid"></a>Anslut funktionen till Event Grid

Sedan prenumererar du på *ProcessDTRoutedData* Azure-funktionen i Event Grid-avsnittet som du skapade tidigare, så att telemetridata kan flöda från *thermostat67en* dubbla genom Event Grid-ämnet till funktionen, som går tillbaka till Azure Digital-dubbla och uppdaterar *Room21* dubbelt.

Om du vill göra det skapar du en **Event Grid-prenumeration** som skickar data från det **Event Grid-ämne** som du skapade tidigare till din *ProcessDTRoutedData* Azure-funktion.

I [Azure Portal](https://portal.azure.com/)navigerar du till ditt event Grid-ämne genom att söka efter dess namn i det övre Sök fältet. Välj *+ Händelseprenumeration*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: Event Grid händelse prenumeration":::

Stegen för att skapa den här händelse prenumerationen liknar när du prenumererade på den första Azure-funktionen för att IoT Hub tidigare i den här självstudien. Den här gången behöver du inte ange *telemetri* som händelse typ att lyssna efter och du kommer att ansluta till en annan Azure-funktion.

På sidan *Skapa händelse prenumeration* fyller du i fälten enligt följande (fält som är fyllda som standard nämns inte):
* *information om*  >  händelse prenumeration **Namn**: ge din händelse prenumeration ett namn.
* *slut punkts information*  >  **Slut punkts typ**: Välj *Azure Function* från meny alternativen.
* *slut punkts information*  >  **Slut punkt**: Tryck på länken *Välj en slut punkt* . Då öppnas ett *Välj Azure Function* -fönster:
    - Fyll i din **prenumeration**, **resurs grupp**, **Function-app** och **Function** (*ProcessDTRoutedData*). Vissa av dessa kan fyllas i automatiskt när du har valt prenumerationen.
    - **Bekräfta val** av träff.

Gå tillbaka till sidan *Skapa händelse prenumeration* och tryck på **skapa**.

### <a name="run-the-simulation-and-see-the-results"></a>Kör simuleringen och se resultatet

Nu kan du köra enhets simulatorn för att starta det nya händelse flödet som du har konfigurerat. Gå till Visual Studio-fönstret där _**DeviceSimulator**_ -projektet är öppet och kör projektet.

Precis som när du körde enhets simulatorn tidigare öppnas ett konsol fönster som visar simulerade simulerings meddelanden för temperatur. De här händelserna går igenom det flöde du ställde in tidigare för att uppdatera *thermostat67* och sedan gå igenom flödet som du har ställt in nyligen för att uppdatera *Room21* -dubbla för att matcha.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Konsol utmatning av enhets simulatorn som visar hur temperatur telemetri skickas":::

Du behöver inte göra något annat i den här konsolen, men lämna det samtidigt som du slutför nästa steg.

Om du vill visa data från den digitala sidan av Azures dubbla sidor går du till Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet och kör projektet.

I fönstret projekt konsol som öppnas kör du följande kommando för att få de temperaturer som rapporteras av **både** den digitala, dubbla *thermostat67* och den digitala dubbla *Room21*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Du bör se de direktsända uppdaterade temperaturerna *från din Azure Digital-instans* som loggas till-konsolen varannan sekund. Observera att temperaturen för *Room21* uppdateras för att matcha uppdateringarna till *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Konsol utdata som visar loggen över temperatur meddelanden från en termostat och ett rum":::

När du har verifierat att det fungerar som det ska kan du sluta köra båda projekten. Du kan också stänga Visual Studio-Fönstren, eftersom självstudien nu är slutförd.

## <a name="review"></a>Genomgång

Här är en recension av scenariot som du skapade i den här självstudien.

1. En digital Azure Digital-instans representerar en våning, ett rum och en termostat (representeras av **avsnitt a** i diagrammet nedan)
2. En simulerad enhets telemetri skickas till IoT Hub, där *ProcessHubToDTEvents* Azure-funktionen lyssnar efter telemetri-händelser. Funktionen *ProcessHubToDTEvents* Azure använder informationen i dessa händelser för att ställa in egenskapen *temperatur* på *thermostat67* (**pil B** i diagrammet).
3. Egenskaps ändrings händelser i Azure Digitals flätas dirigeras till ett event Grid-ämne där *ProcessDTRoutedData* Azure-funktionen lyssnar efter händelser. Funktionen *ProcessDTRoutedData* Azure använder informationen i dessa händelser för att ställa in egenskapen *temperatur* på *Room21* (**pil C** i diagrammet).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Bild av det fullständiga Bygg scenariot. Visar data som flödar från en enhet till IoT Hub, via en Azure-funktion (pil B) till en digital Azure-instans (del A), sedan från Event Grid till en annan Azure-funktion för bearbetning (pil C)":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört den här självstudien kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Om du vill fortsätta använda Azure Digital-instansen som du har skapat i den här artikeln, men ta bort några eller alla modeller, delar och relationer**, kan du använda [AZ DT](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true) CLI-kommandona i ett [Azure Cloud Shells](https://shell.azure.com) fönster för att ta bort de element som du vill ta bort.

    Det här alternativet tar inte bort några andra Azure-resurser som skapats i den här självstudien (IoT Hub, Azure Functions app osv.). Du kan ta bort dessa individuellt med de [DT-kommandon](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true) som är lämpliga för varje resurs typ.

Du kanske också vill ta bort projektmappen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett scenario från slut punkt till slut punkt som visar att Azure Digitals dubbla enheter drivs av direktsänd enhets data.

Börja sedan titta på begrepps dokumentationen för att lära dig mer om element som du har arbetat med i självstudien:

> [!div class="nextstepaction"]
> [*Begrepp: anpassade modeller*](concepts-models.md)