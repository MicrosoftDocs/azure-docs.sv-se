---
title: Konfigurera den anslutna fabriks miljön – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar den anslutna fabriks lösnings acceleratorn inklusive dess topologi.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646216"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Konfigurera den anslutna fabriks lösnings acceleratorn

> [!IMPORTANT]
> Medan vi uppdaterar den här artikeln kan du läsa mer i [Azures industriella IoT](https://azure.github.io/Industrial-IoT/) .

Den anslutna fabriks lösnings acceleratorn visar en simulerad instrument panel för ett fiktivt företag contoso. Det här företaget har fabriker på flera globala platser globalt.

I den här artikeln används Contoso som exempel för att beskriva hur du konfigurerar topologin för en ansluten fabriks lösning.

## <a name="simulated-factories-configuration"></a>Konfiguration av simulerade fabriker

Varje contoso-fabrik har produktions linjer som består av tre stationer. Varje station är en riktig OPC UA-server med en speciell roll:

* Sammansättnings Station
* Test Station
* Förpacknings Station

Dessa OPC UA-servrar har OPC UA-noder och [OPC Publisher](overview-opc-publisher.md) skickar värdena för de här noderna till den anslutna fabriken. Du måste bland annat:

* Aktuell drift status, till exempel aktuell energi förbrukning.
* Produktions information, till exempel antalet producerade produkter.

Du kan använda instrument panelen för att öka detalj nivån för Contoso Factory-topologin från en global vy ned till en stationnivå-vy. På den anslutna fabriks instrument panelen kan du:

* Visualiseringen av OEE-och KPI-siffror för varje skikt i topologin.
* Visualiseringen av aktuella värden för OPC UA-noder på stationerna.
* Agg regeringen av OEE-och KPI-beloppen från Stations nivån till global nivå.
* Visualiseringen av aviseringar och åtgärder att utföra om värden når vissa tröskelvärden.

## <a name="connected-factory-topology"></a>Topologi för ansluten fabrik

Topologi för fabriker, produktions linjer och stationer är hierarkiskt:

* Den globala nivån har fabriks noder som underordnade.
* Fabrikerna har produktions linje noder som underordnade.
* Produktions raderna har Station-noder som underordnade.
* Stationerna (OPC UA-servrar) har OPC UA-noder som underordnade.

Varje nod i topologin har en gemensam uppsättning egenskaper som definierar:

* En unik identifierare för Topology-noden.
* Ett namn.
* En beskrivning.
* En bild.
* Underordnade till topologi-noden.
* Minimi-, mål-och max värden för OEE-och KPI-siffror och aviserings åtgärder att köra.

## <a name="topology-configuration-file"></a>Konfigurations fil för topologi

För att konfigurera egenskaperna som anges i föregående avsnitt, använder den anslutna fabriks lösningen en konfigurations fil som heter [ContosoTopologyDescription.jspå](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Du kan hitta den här filen i lösningens käll kod i `WebApp/Contoso/Topology` mappen.

Följande fragment visar en översikt över `ContosoTopologyDescription.json` konfigurations filen:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

De gemensamma egenskaperna för `<global_configuration>` , `<factory_configuration>` , `<production_line_configuration>` och `<station_configuration>` är:

* **Namn** (typ sträng)

  Definierar ett beskrivande namn som bara ska vara ett ord för topologi-noden som ska visas i instrument panelen.

* **Beskrivning** (typ sträng)

  Beskriver noden topologi i mer detalj.

* **Bild** (typ sträng)

  Sökvägen till en bild i WebApp-lösningen för att visa när information om noden topologi visas i instrument panelen.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (typ `<performance_definition>` )

  De här egenskaperna definierar minimalt värde, mål och maximala värden för den operativa figuren som används för att generera aviseringar. De här egenskaperna definierar också vilka åtgärder som ska utföras om en avisering identifieras.

`<factory_configuration>`Objekten och `<production_line_configuration>` har en egenskap:

* **GUID** (typ sträng)

  Identifierar noden topologi unikt.

`<factory_configuration>` har en egenskap:

* **Plats** (typ `<location_definition>` )

  Anger var fabriken finns.

`<station_configuration>` har egenskaper:

* **OpcUri** (typ sträng)

  Den här egenskapen måste anges till OPC UA Application URI för OPC UA-servern.
  Eftersom det måste vara globalt unikt av OPC UA-specifikationen används den här egenskapen för att identifiera noden för stationens topologi.

* **OpcNodes**, som är en matris med OPC UA-noder (typ `<opc_node_description>` )

`<location_definition>` har egenskaper:

* **Ort** (typ sträng)

  Namnet på staden närmast platsen

* **Land** (typ sträng)

  Land på platsen

* **Latitude** (typ dubbel)

  Platsens latitud

* **Longitud** (typ Double)

  Longitud för platsen

`<performance_definition>` har egenskaper:

* **Minimum** (typ Double)

  Lägre tröskel som värdet kan uppnå. Om det aktuella värdet är under det här tröskelvärdet genereras en avisering.

* **Mål** (typ Double)

  Idealiskt mål värde.

* **Maximalt** (typ Double)

  Övre tröskelvärde som värdet kan uppnå. Om det aktuella värdet är över det här tröskelvärdet genereras en avisering.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definierar en uppsättning åtgärder som kan vidtas som svar på en minimal avisering.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definierar en uppsättning åtgärder som kan vidtas som svar på en maximal avisering.

`<alert_action`> har egenskaper:

* **Typ** (typ sträng)

  Typ av aviserings åtgärd. Följande typer är kända:

  * **AcknowledgeAlert**: status för aviseringen ska ändras till bekräftad.
  * **CloseAlert**: Alla äldre aviseringar av samma typ bör inte längre visas i instrument panelen.
  * **CallOpcMethod**: en OPC UA-metod ska anropas.
  * **Openwebb sida**: ett webbläsarfönster öppnas med ytterligare sammanhangsbaserad information.

* **Beskrivning** (typ sträng)

  Beskrivning av åtgärden som visas på instrument panelen.

* **Parameter** (typ sträng)

  Parametrar som krävs för att utföra åtgärden. Värdet beror på åtgärds typen.

  * **AcknowledgeAlert**: ingen parameter krävs.
  * **CloseAlert**: ingen parameter krävs.
  * **CallOpcMethod**: Node-informationen och PARAMETRARNA för OPC UA-metoden som anropar formatet "nodeId of Parent Node, nodeId of metoden to Call, URI: n för OPC UA-Server."
  * **Openwebb sida**: URL: en som ska visas i webbläsarfönstret.

`<opc_node_description>` innehåller information om OPC UA-noder i en station (OPC UA-Server). Noder som inte representerar några befintliga OPC UA-noder, men som används som lagring i beräknings logiken för den anslutna fabriken är också giltiga. Den har följande egenskaper:

* **NodeID** (typ sträng)

  Adressen för OPC UA-noden i stationens (OPC UA Server) adress utrymme. Syntaxen måste anges i OPC UA-specifikationen för en NodeId.

* **SymbolicName** (typ sträng)

  Namn som ska visas i instrument panelen när värdet för den här OPC UA-noden visas.

* **Relevans** (matris av typ sträng)

  Anger för vilken beräkning av OEE eller KPI som värdet för OPC UA-noden är relevant. Varje mat ris element kan vara något av följande värden:

  * **OeeAvailability_Running**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeeAvailability_Fault**: värdet är relevant för beräkning av OEE-tillgänglighet.
  * **OeePerformance_Ideal**: värdet är relevant för beräkning av OEE-prestanda och är vanligt vis ett konstant värde.
  * **OeePerformance_Actual**: värdet är relevant för beräkning av OEE-prestanda.
  * **OeeQuality_Good**: värdet är relevant för beräkning av OEE-kvalitet.
  * **OeeQuality_Bad**: värdet är relevant för beräkning av OEE-kvalitet.
  * **Kpi1**: värdet är relevant för beräkning av Kpi1.
  * **Kpi2**: värdet är relevant för beräkning av Kpi2.

* **Opcode** (typ sträng)

  Anger hur värdet för OPC UA-noden hanteras i Time Series Insight-frågor och OEE/KPI-beräkningar. Varje insikts fråga för Time Series riktar sig till ett särskilt tidsintervall, som är en parameter till frågan och ger ett resultat. OpCode styr hur resultatet beräknas och kan vara något av följande värden:

  * **Diff**: skillnaden mellan det sista och det första värdet i TimeSpan.
  * **Gmsn**: medelvärdet av alla värden i TimeSpan.
  * **Sum**: summan av alla värden i TimeSpan.
  * **Senaste**: används inte för närvarande.
  * **Count**: antalet värden i TimeSpan.
  * **Max**: det maximala värdet i TimeSpan.
  * **Min**: det minimala värdet i TimeSpan.
  * **Const**: resultatet är det värde som anges av Property ConstValue.
  * **SubMaxMin**: skillnaden mellan maximalt och minimalt värde.
  * **TimeSpan**: TimeSpan.

* **Enheter** (typ sträng)

  Definierar en enhet för värdet som visas i instrument panelen.

* **Synlig** (typ boolesk)

  Anger om värdet ska visas i instrument panelen.

* **ConstValue** (typ Double)

  Om **opcode** är **Const** är den här egenskapen värdet för noden.

* **Minimum** (typ Double)

  Om det aktuella värdet sjunker under det här värdet genereras en minimal avisering.

* **Maximalt** (typ Double)

  Om det aktuella värdet aktive ras ovanför det här värdet genereras en maximal avisering.

* **MinimumAlertActions** (typ `<alert_action>` )

  Definierar en uppsättning åtgärder som kan vidtas som svar på en minimal avisering.

* **MaximumAlertActions** (typ `<alert_action>` )

  Definierar en uppsättning åtgärder som kan vidtas som svar på en maximal avisering.

På Stations nivå visas även **simulerings** objekt. De här objekten används bara för att konfigurera den anslutna fabriks simuleringen och ska inte användas för att konfigurera en verklig topologi.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Hur konfigurations data används vid körning

Alla egenskaper som används i konfigurations filen kan grupperas i olika kategorier beroende på hur de används. Dessa kategorier är:

### <a name="visual-appearance"></a>Visuellt utseende

Egenskaperna i den här kategorin definierar det visuella utseendet på den anslutna fabriks instrument panelen. Exempel:

* Name
* Beskrivning
* Bild
* Location
* Enheter
* Synliga

### <a name="internal-topology-tree-addressing"></a>Invändigt träd i topologi

WebApp hanterar en intern data ord lista som innehåller information om alla topologier för topologier. Egenskaperna **GUID** och **OpcUri** används som nycklar för att komma åt den här ord listan och måste vara unik.

### <a name="oeekpi-computation"></a>OEE/KPI-beräkning

OEE/KPI-siffrorna för den anslutna fabriks simuleringen är parameterstyrda av:

* OPC UA Node-värden som ska ingå i beräkningen.
* Hur talet beräknas från telemetri värden.

Ansluten fabrik använder OEE-formlerna som publiceras av [http://www.oeefoundation.org](http://www.oeefoundation.org) .

OPC UA Node-objekt i stationer möjliggör taggning för användning i OEE/KPI-beräkning. Egenskapen **relevans** anger för vilken OEE/KPI-bild som OPC UA-Node-värdet ska användas. Egenskapen **opcode** definierar hur värdet ingår i beräkningen.

### <a name="alert-handling"></a>Aviserings hantering

Ansluten fabrik har stöd för en enkel minsta/högsta tröskelbaserade mekanism för generering av aviseringar. Det finns ett antal fördefinierade åtgärder som du kan konfigurera som svar på dessa aviseringar. Följande egenskaper styr den här mekanismen:

* Maximal
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Korrelera med telemetridata

För vissa åtgärder, t. ex. visualisering av det sista värdet eller skapandet av Time Series Insight-frågor, behöver WebApp ett adresserings schema för inmatade telemetridata. Telemetrin som skickas till ansluten fabrik måste också lagras i interna data strukturer. De två egenskaperna som aktiverar dessa åtgärder är at-Station (OPC UA-Server) och OPC UA Node-nivå:

* **OpcUri**

  Identifierar (globalt unikt) den OPC UA-server som Telemetrin kommer från. I de inmatade meddelandena skickas den här egenskapen som **ApplicationUri**.

* **NodeId**

  Identifierar Node-värdet i OPC UA-servern. Formatet på egenskapen måste vara angivet i OPC UA-specifikationen. I de inmatade meddelandena skickas den här egenskapen som **nodeID**.

Se [Vad är OPC Publisher](overview-opc-publisher.md) för mer information om hur telemetri-data matas in i den anslutna fabriken.

## <a name="example-how-kpi1-is-calculated"></a>Exempel: hur KPI1 beräknas

Konfigurationen i `ContosoTopologyDescription.json` filen styr hur OEE/KPI-siffror beräknas. I följande exempel visas hur egenskaper i den här filen styr beräkningen av KPI1.

I anslutna fabriks KPI1 används för att mäta antalet tillverkade produkter under den senaste timmen. Varje station (OPC UA-Server) i den anslutna fabriks simuleringen tillhandahåller en OPC UA-nod ( `NodeId: "ns=2;i=385"` ) som ger telemetri för att beräkna denna KPI.

Konfigurationen för den här OPC UA-noden ser ut som i följande kodfragment:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Den här konfigurationen aktiverar frågor mot telemetri-värden för den här noden med Time Series Insights. Den Time Series Insights frågan hämtar:

* Antalet värden.
* Det minimala värdet.
* Det maximala värdet.
* Medelvärdet av alla värden.
* Summan av alla värden för alla unika **OpcUri** (**ApplicationUri**), **nodeId** -par under ett angivet tidsintervall.

En egenskap för värdet för **NumberOfManufactureredProducts** -noden är att det bara ökar. Om du vill beräkna antalet produkter som tillverkas i TimeSpan använder Connected Factory **opcode** **SubMaxMin**. Beräkningen hämtar det lägsta värdet i början av TimeSpan och det maximala värdet i slutet av TimeSpan.

**Opcode** i konfigurationen konfigurerar beräknings logiken för att beräkna resultatet av skillnaden mellan högsta och lägsta värde. Dessa resultat ackumuleras sedan längst ned till rot nivån (global) och visas i instrument panelen.

## <a name="next-steps"></a>Nästa steg

Ett förslag till nästa steg är att lära dig hur du [anpassar den anslutna fabriks lösningen](iot-accelerators-connected-factory-customize.md).
