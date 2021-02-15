---
title: Definiera en ny IoT-enhets typ i Azure IoT Central | Microsoft Docs
description: Den här artikeln visar dig som Solution Builder och hur du skapar en ny mall för Azure IoT-enheter i ditt Azure IoT Central-program. Du definierar telemetri, tillstånd, egenskaper och kommandon för din typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 22e948a0100f23dbddef8fc138576bb4b9372c77
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363210"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definiera en ny IoT-enhetstyp i Azure IoT Central-programmet

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

En enhets mall är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett [Azure IoT Central-program](concepts-app-templates.md).

Ett verktyg kan till exempel skapa en enhets mal len för en ansluten fläkt med följande egenskaper:

- Skickar temperatur telemetri
- Skickar plats egenskap
- Skickar fel händelser för fläkt motor
- Skickar fläkt drifts tillstånd
- Ger en skrivbar fläkt hastighets egenskap
- Innehåller ett kommando för att starta om enheten
- Ger dig en övergripande vy av enheten med hjälp av en vy

Med den här enhets mal len kan en operatör skapa och ansluta Real fläkt enheter. Alla dessa fläktar har mått, egenskaper och kommandon som operatörer använder för att övervaka och hantera dem. Operatorer använder [enhets vyer](#add-views) och-formulär för att interagera med fläkt enheterna. En enhets utvecklare använder mallen för att förstå hur enheten interagerar med programmet. Läs mer i [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Endast konstruktörer och administratörer kan skapa, redigera och ta bort mallar för enheter. Alla användare kan skapa enheter på sidan **enheter** från befintliga enhets mallar.

I ett IoT Central-program använder en enhets mall en enhets modell för att beskriva funktionerna i en enhet. Som ett verktyg har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och [implementera sedan dess enhets modell i enhets koden](concepts-telemetry-properties-commands.md).
- Importera en enhets mall från [Azure-certifierad för IoT-katalogen](https://aka.ms/iotdevcat). Anpassa enhets mal len efter dina krav i IoT Central.
> [!NOTE]
> IoT Central kräver en fullständig modell med alla refererade gränssnitt i samma fil, när du importerar en modell från modell databasen använder du nyckelordet "Expanded" för att få den fullständiga versionen.
Till exempel. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Skapa en enhets modell med hjälp av [Digitals definitions språk (DTDL)-version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Visual Studio Code har ett tillägg som stöder redigering av DTDL-modeller. Mer information finns i [Installera och använda redigerings verktygen för DTDL](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Publicera sedan modellen till den offentliga modellens lagrings plats. Läs mer i [enhets modellens lagrings plats](../../iot-pnp/concepts-model-repository.md). Implementera din enhets kod från modellen och Anslut din riktiga enhet till ditt IoT Central-program. IoT Central söker efter och importerar enhets modellen från det offentliga lagrings platsen och skapar en mall för enheten. Du kan sedan lägga till alla moln egenskaper, anpassningar och vyer som ditt IoT Central program behöver för enhets mal len.
- Redigera en enhets modell med hjälp av DTDL. Implementera din enhets kod från modellen. Importera enhets modellen manuellt till IoT Central programmet och Lägg sedan till eventuella moln egenskaper, anpassningar och vyer som dina IoT Central program behöver.

> [!TIP]
> IoT Central kräver en fullständig modell med alla refererade gränssnitt i samma fil. När du importerar en modell från modell databasen använder du nyckelordet *Expanded* för att hämta den fullständiga versionen.
> Till exempel [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Du kan också lägga till enhets mallar i ett IoT Central program med hjälp av [REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) eller [CLI](howto-manage-iot-central-from-cli.md).

Vissa [programmallar](concepts-app-templates.md) innehåller redan enhetsspecifika mallar som är användbara i det scenario som program mal len stöder. Se till exempel [in-Store Analytics-arkitektur](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Skapa en enhets mall från enhets katalogen

Som ett verktyg kan du snabbt börja skapa din lösning med hjälp av en certifierad enhet. Se listan i [Azure IoT-katalogen](https://catalog.azureiotsolutions.com/alldevices). IoT Central integreras med enhets katalogen så att du kan importera en enhets modell från vilken som helst av de certifierade enheterna. Så här skapar du en enhets mal len från någon av dessa enheter i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny** och välj sedan någon av de certifierade enheterna från katalogen. IoT Central skapar en enhets mal len baserat på den här enhets modellen.
1. Lägg till eventuella moln egenskaper, anpassningar eller vyer i din enhets mall.
1. Välj **publicera** för att göra mallen tillgänglig för operatörer för att visa och ansluta enheter.

## <a name="create-a-device-template-from-scratch"></a>Skapa en enhets mal len från grunden

En enhets mall innehåller:

- En _enhets modell_ som anger telemetri, egenskaper och kommandon som enheten implementerar. Dessa funktioner är ordnade i en eller flera komponenter.
- _Moln egenskaper_ som definierar information som IoT Central program lagrar om dina enheter. Till exempel kan en moln egenskap registrera datumet då enheten senast servades. Den här informationen delas aldrig med enheten.
- _Anpassningar_ gör att verktyget åsidosätter vissa av definitionerna i enhets modellen. Verktyget kan till exempel åsidosätta namnet på en enhets egenskap. Egenskaps namn visas i IoT Central vyer och formulär.
- Med _vyer och formulär_ kan verktyget skapa ett användar gränssnitt som gör att operatörer kan övervaka och hantera de enheter som är anslutna till ditt program.

Så här skapar du en enhets mal len i IoT Central:

1. Gå till sidan **Device templates** i ditt IoT Central-program.
1. Välj **+ ny**  >  **IoT-enhet**. Välj sedan **Nästa: anpassa**.
1. Ange ett namn för mallen, till exempel **termostat**. Välj sedan **Nästa: granska** och välj sedan **skapa**.
1. IoT Central skapar en tom enhets mall och låter dig välja att skapa en anpassad modell från grunden eller importera en DTDL modell.

## <a name="manage-a-device-template"></a>Hantera en enhets mall

Du kan byta namn på eller ta bort en mall från mallens start sida.

När du har lagt till en enhets modell i mallen kan du publicera den. Innan du har publicerat mallen kan du inte ansluta en enhet som baseras på den här mallen för dina operatörer att se på sidan **enheter** .

## <a name="create-a-capability-model"></a>Skapa en kapacitets modell

För att skapa en enhets modell kan du:

- Använd IoT Central för att skapa en anpassad modell från grunden.
- Importera en DTDL-modell från en JSON-fil. En Device Builder kan använda Visual Studio Code för att skapa en enhets modell för ditt program.
- Välj en av enheterna i enhets katalogen. Med det här alternativet importeras enhets modellen som tillverkaren har publicerat för den här enheten. En enhets modell som importeras som detta publiceras automatiskt.

## <a name="manage-a-capability-model"></a>Hantera en kapacitets modell

När du har skapat en enhets modell kan du:

- Lägg till komponenter i modellen. En modell måste ha minst en komponent.
- Redigera modellens metadata, till exempel ID, namnrymd och namn.
- Ta bort modellen.

## <a name="create-a-component"></a>Skapa en komponent

En enhets modell måste ha minst en standard komponent. En komponent är en återanvändbar samling funktioner.

Så här skapar du en komponent:

1. Gå till din enhets modell och välj **+ Lägg till komponent**.

1. På sidan **Lägg till ett komponent gränssnitt** kan du:

    - Skapa en anpassad komponent från grunden.
    - Importera en befintlig komponent från en DTDL-fil. En Device Builder kan använda Visual Studio Code för att skapa ett komponent gränssnitt för enheten.

1. När du har skapat en komponent väljer du **Redigera identitet** för att ändra komponentens visnings namn.

1. Om du väljer att skapa en anpassad komponent från grunden kan du lägga till enhetens funktioner. Enhets funktioner är telemetri, egenskaper och kommandon.

### <a name="telemetry"></a>Telemetri

Telemetri är en data ström med värden som skickas från enheten, vanligt vis från en sensor. En sensor kan till exempel rapportera omgivnings temperatur.

I följande tabell visas konfigurations inställningarna för en telemetri-funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för telemetri-värdet som används i vyer och formulär. |
| Namn | Namnet på fältet i telemetri meddelandet. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Telemetridata. |
| Semantisk typ | Den semantiska typen av telemetri, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen telemetri, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Allvarlighetsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information** eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för telemetri-värdet, till exempel **mph**, **%** eller **&deg; C**. |
| Visa enhet | En visnings enhet som används i vyer och formulär. |
| Kommentar | Eventuella kommentarer om telemetri-funktionerna. |
| Beskrivning | En beskrivning av telemetri-funktionen. |

### <a name="properties"></a>Egenskaper

Egenskaperna representerar tidpunkts värden. En enhet kan till exempel använda en egenskap för att rapportera mål temperaturen som den försöker nå. Du kan ange skrivbara egenskaper från IoT Central.

I följande tabell visas konfigurations inställningarna för en egenskaps funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för egenskap svärdet som används i vyer och formulär. |
| Namn | Egenskapens namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Immaterialrätt. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Egenskaps data typen, t. ex. Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. Schemat är inte tillgängligt för semantiska typer av händelse och tillstånd. |
| Skrivbar | Om egenskapen inte är skrivbar kan enheten rapportera egenskaps värden till IoT Central. Om egenskapen är skrivbar kan enheten rapportera egenskaps värden till IoT Central och IoT Central kan skicka egenskaps uppdateringar till enheten.
| Allvarlighetsgrad | Endast tillgängligt för den semantiska händelse typen. Allvarlighets graderna är **fel**, **information** eller **Varning**. |
| Tillstånds värden | Endast tillgängligt för semantisk typ av tillstånd. Definiera möjliga tillstånds värden, som var och en har visnings namn, namn, uppräknings typ och värde. |
| Enhet | En enhet för egenskap svärdet, till exempel **mph**, **%** eller **&deg; C**. |
| Visa enhet | En visnings enhet som används i vyer och formulär. |
| Kommentar | Kommentarer om egenskaps funktionen. |
| Beskrivning | En beskrivning av egenskaps funktionen. |

### <a name="commands"></a>Kommandon

Du kan anropa enhets kommandon från IoT Central. Kommandon kan skicka parametrar till enheten och få ett svar från enheten. Du kan till exempel anropa ett kommando för att starta om en enhet på 10 sekunder.

I följande tabell visas konfigurations inställningarna för en kommando funktion:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för kommandot som används i vyer och formulär. |
| Namn | Kommandots namn. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. Det här fältet måste vara alfanumeriskt. |
| Typ av kapacitet | Kommandoprompt. |
| Kommentar | Eventuella kommentarer om kommando funktionen. |
| Beskrivning | En beskrivning av kommando funktionen. |
| Förfrågan | Om aktive rad, en definition av Request-parametern, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |
| Svarsåtgärder | Om aktive rad, en definition av kommando svaret, inklusive: namn, visnings namn, schema, enhet och visnings enhet. |

Mer information om hur enheter implementerar kommandon finns i [telemetri, egenskap och kommando nytto laster > kommandon och tids krävande kommandon](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Offline-kommandon

Du kan välja köa kommandon om en enhet för närvarande är offline genom att aktivera **kön om alternativet offline** för ett kommando i enhets mal len.

Det här alternativet använder IoT Hub meddelanden från molnet till enheten för att skicka meddelanden till enheter. Mer information finns i IoT Hub artikeln [skicka meddelanden från moln till enhet](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Meddelanden från moln till enhet:

- Är enkelriktade meddelanden till enheten från din lösning.
- Garantera en meddelande leverans på minst en gång. IoT Hub sparar meddelanden från molnet till enheten i köer per enhet, vilket garanterar återhämtning mot anslutnings-och enhets problem.
- Kräv att enheten ska implementera en meddelande hanterare för att bearbeta meddelandet från molnet till enheten.

> [!NOTE]
> Det här alternativet är endast tillgängligt i IoT Central webb gränssnitt. Den här inställningen tas inte med om du exporterar en modell eller komponent från enhets mal len.

## <a name="manage-a-component"></a>Hantera en komponent

Om du inte har publicerat komponenten kan du redigera de funktioner som definieras av komponenten. Om du vill göra några ändringar när du har publicerat komponenten måste du skapa en ny version av enhets mal len och versionen av [komponenten](howto-version-device-template.md). Du kan göra ändringar som inte kräver versions hantering, till exempel visnings namn eller enheter, i avsnittet **Anpassa** .

Du kan också exportera komponenten som en JSON-fil om du vill återanvända den i en annan funktions modell.

## <a name="add-cloud-properties"></a>Lägga till molnegenskaper

Använd moln egenskaper för att lagra information om enheter i IoT Central. Moln egenskaper skickas aldrig till en enhet. Du kan till exempel använda moln egenskaper för att lagra namnet på kunden som har installerat enheten, eller enhetens senaste service datum.

I följande tabell visas konfigurations inställningarna för en moln egenskap:

| Fält | Beskrivning |
| ----- | ----------- |
| Visningsnamn | Visnings namnet för moln egenskap svärdet som används i vyer och formulär. |
| Namn | Namnet på moln egenskapen. IoT Central genererar ett värde för det här fältet från visnings namnet, men du kan välja ett eget värde om det behövs. |
| Semantisk typ | Den semantiska typen för egenskapen, till exempel temperatur, tillstånd eller händelse. Valet av semantisk typ avgör vilka av följande fält som är tillgängliga. |
| Schema | Data typen Cloud Property, till exempel Double, String eller Vector. De tillgängliga alternativen bestäms av semantisk typ. |

## <a name="add-customizations"></a>Lägg till anpassningar

Använd anpassningar när du behöver ändra en importerad komponent eller lägga till IoT Central-/regionsspecifika funktioner till en funktion. Du kan bara anpassa fält som inte bryter mot komponentens kompatibilitet. Du kan till exempel:

- Anpassa visnings namn och enheter för en funktion.
- Lägg till en standard färg som ska användas när värdet visas i ett diagram.
- Ange start-, minimi-och max värden för en egenskap.

Du kan inte anpassa funktions namnet eller funktions typen. Om det finns ändringar som du inte kan göra i avsnittet **Anpassa** , måste du ha version av din enhets mall och-komponent för att ändra funktionen.

### <a name="generate-default-views"></a>Generera standardvyer

Att generera standardvyer är ett snabbt sätt att visualisera viktig enhets information. Du har upp till tre standardvyer som skapats för din enhets mall:

- **Kommandon**: en vy med enhets kommandon och låter operatören skicka dem till enheten.
- **Översikt**: en vy med enhets telemetri, som visar diagram och mått.
- **Om**: en vy med enhets information och visar enhets egenskaper.

När du har valt **generera standardvyer** ser du att de har lagts till automatiskt under avsnittet **vyer** i din enhets mall.

## <a name="add-views"></a>Lägga till vyer

Lägg till vyer i en enhets mall för att aktivera operatörer för att visualisera en enhet med hjälp av diagram och mått. Du kan ha flera vyer för en enhets mall.

Så här lägger du till en vy i en enhets mal len:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **visualisera enheten**.
1. Ange ett namn för vyn i **visnings namn**.
1. Lägg till paneler i vyn från listan över statiska, egenskaper, moln egenskaper, telemetri och kommando paneler. Dra och släpp de paneler som du vill lägga till i vyn.
1. Om du vill rita flera telemetri värden på en enda diagram panel väljer du telemetri-värden och väljer sedan **kombinera**.
1. Konfigurera varje panel som du lägger till för att anpassa hur data visas. Öppna det här alternativet genom att välja kugg hjuls ikonen eller genom att välja **ändra konfiguration** på diagram panelen.
1. Ordna och ändra storlek på panelerna i vyn.
1. Spara ändringarna.

### <a name="configure-preview-device-to-view"></a>Konfigurera förhands gransknings enheten för visning

Välj **Konfigurera för hands versions enhet** om du vill visa och testa vyn. Med den här funktionen kan du se vyn när din operatör ser den när den har publicerats. Använd den här funktionen för att kontrol lera att dina vyer visar rätt data. Du kan välja bland följande alternativ:

- Ingen förhands gransknings enhet.
- Den riktiga testen het som du har konfigurerat för din enhets mall.
- En befintlig enhet i programmet med hjälp av enhets-ID: t.

## <a name="add-forms"></a>Lägg till formulär

Lägg till formulär i en enhets mall för att aktivera operatörer för att hantera en enhet genom att visa och ange egenskaper. Operatörer kan bara redigera moln egenskaper och skrivbara enhets egenskaper. Du kan ha flera formulär för en enhets mall.

Så här lägger du till ett formulär i en enhets mal len:

1. Gå till din enhets mall och välj **vyer**.
1. Välj **Redigera enhets-och moln data**.
1. Ange ett namn för ditt formulär i **formulär namn**.
1. Välj hur många kolumner som ska användas för att utforma ditt formulär.
1. Lägg till egenskaper till ett befintligt avsnitt i formuläret eller välj egenskaper och sedan **Lägg till avsnitt**. Använd avsnitt för att gruppera egenskaper i formuläret. Du kan lägga till en rubrik i ett avsnitt.
1. Konfigurera varje egenskap i formuläret för att anpassa dess beteende.
1. Ordna egenskaperna i formuläret.
1. Spara ändringarna.

## <a name="publish-a-device-template"></a>Publicera en enhetsmall

Innan du kan ansluta en enhet som implementerar din enhets modell måste du publicera din enhets mall.

När du har publicerat en enhets mall kan du bara göra begränsade ändringar i enhets modellen. Om du vill ändra en komponent måste du [skapa och publicera en ny version](./howto-version-device-template.md).

Om du vill publicera en enhets mal len går du till din enhets mall och väljer **publicera**.

När du har publicerat en enhets mall kan en operatör gå till sidan **enheter** och lägga till antingen verkliga eller simulerade enheter som använder din enhets mall. Du kan fortsätta att ändra och spara din enhets mall när du gör ändringar. När du vill skicka ut dessa ändringar till operatören och visa dem på sidan **enheter** måste du välja **publicera** varje gång.

## <a name="next-steps"></a>Nästa steg

Om du är en enhets utvecklare är ett förslag till nästa steg att läsa om [versions hantering av enhets mallar](./howto-version-device-template.md).
