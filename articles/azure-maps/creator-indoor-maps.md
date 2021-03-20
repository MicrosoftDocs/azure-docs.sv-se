---
title: Arbeta med inomhus Maps i Azure Maps Creator (förhands granskning)
description: I den här artikeln beskrivs begrepp som gäller för Azure Maps Creator Services (för hands version)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903572"
---
# <a name="creator-preview-for-indoor-maps"></a>Skapare (för hands version) för inomhus kartor


> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Den här artikeln beskriver begrepp och verktyg som gäller för Azure Maps Creator. Vi rekommenderar att du läser den här artikeln innan du börjar använda Azure Maps Creator API och SDK.

Du kan använda Creator för att utveckla program med kart funktioner baserade på inlednings data från kartan. Den här artikeln beskriver processen för att ladda upp, konvertera, skapa och använda dina kart data. Hela arbets flödet illustreras i diagrammet nedan.

![Data arbets flöde för skapare karta](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Skapa Azure Maps skapare (förhands granskning) 

Om du vill använda Creator Services (för hands version) måste Azure Maps Creator skapas i ett Azure Maps konto. Information om hur du skapar Azure Maps Creator i Azure Maps finns i [hantera Azure Maps skapare](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Ladda upp ett ritnings paket

Skapare (för hands version) samlar in insamlade kart data genom att konvertera ett uppladdat ritnings paket. Ritnings paketet representerar en konstruerad eller ombyggnadd funktion. Information om krav för ritnings paket finns i [krav för ritnings paket](drawing-requirements.md).

Använd [Azure Maps data (för hands version) Ladda upp API](/rest/api/maps/data/uploadpreview) för att ladda upp ett ritnings paket.  Vid en lyckad uppladdning returnerar data överförings-API: et en användar data identifierare ( `udid` ). `udid`Kommer att användas i nästa steg för att konvertera det överförda paketet till insamlade kart data.

## <a name="convert-a-drawing-package"></a>Konvertera ett ritnings paket

[Tjänsten Azure Maps Conversion](/rest/api/maps/conversion) konverterar ett överfört ritnings paket till insamlade kart data. Konverterings tjänsten verifierar även paketet. Verifierings problem delas in i två typer: fel och varningar. Om några fel upptäcks Miss lyckas konverterings processen. Om varningar identifieras kommer konverteringen att lyckas. Vi rekommenderar dock att du granskar och åtgärdar alla varningar. En varning innebär att en del av konverteringen ignorerades eller automatiskt korrigerades. Om du inte löser varningarna kan det leda till fel i senare processer. Mer information finns i [varningar och fel i Drawing Package](drawing-conversion-error-codes.md).

När ett fel inträffar innehåller konverterings tjänsten en länk till det fristående webb programmet [Azure Maps Drawing Error](drawing-error-visualizer.md) -program. Du kan använda fel Visualiseraren för att kontrol lera [varningar och fel för ritnings paket](drawing-conversion-error-codes.md) som uppstod under konverterings processen. När du har åtgärdat felen kan du försöka överföra och konvertera paketet.

## <a name="create-indoor-map-data"></a>Skapa inliggande kart data

Azure Maps Creator (för hands version) har tre tjänster:

* [Data uppsättnings tjänst](/rest/api/maps/dataset/createpreview).
Använd data uppsättnings tjänsten för att skapa en data uppsättning från ett konverterat ritnings paket data.
* [TILESET-tjänst](/rest/api/maps/tileset/createpreview).
Använd TILESET-tjänsten för att skapa en vektorbaserad representation av en data uppsättning. Program kan använda en TILESET för att presentera en visuell panel baserad vy av data uppsättningen.
* [Funktions tillstånds tjänst](/rest/api/maps/featurestate). Använd funktions tillstånds tjänsten för att stödja dynamisk Map-formatering. Med dynamisk kart stil kan program avspegla real tids händelser på utrymmen i IoT-systemet.

### <a name="datasets"></a>Datauppsättningar

En data uppsättning är en samling funktioner i insamlings kartor. Funktionerna i den inställda kartan representerar anläggningar som definierats i ett konverterat ritnings paket. När du har skapat en data uppsättning med [data uppsättnings tjänsten](/rest/api/maps/dataset/createpreview)kan du skapa valfritt antal [tilesets](#tilesets) -eller [funktions statesets](#feature-statesets).

Med [data uppsättnings tjänsten](/rest/api/maps/dataset/createpreview) kan utvecklare när som helst lägga till eller ta bort anläggningar i en befintlig data uppsättning. Mer information om hur du uppdaterar en befintlig data uppsättning med hjälp av API: et finns i tilläggs alternativen i [data uppsättnings tjänsten](/rest/api/maps/dataset/createpreview). Ett exempel på hur du uppdaterar en data uppsättning finns i [data underhåll](#data-maintenance).

### <a name="tilesets"></a>Tilesets

En TILESET är en samling vektor data som representerar en uppsättning enhetliga rutnäts paneler. Utvecklare kan använda [TILESET-tjänsten](/rest/api/maps/tileset/createpreview) för att skapa tilesets från en data uppsättning.

För att avspegla olika innehålls faser kan du skapa flera tilesets från samma data uppsättning. Du kan till exempel göra en TILESET med möbler och utrustning och en annan TILESET utan möbler och utrustning.  Du kan välja att generera ett TILESET med de senaste data uppdateringarna och en utan de senaste data uppdateringarna.

Förutom vektor data tillhandahåller TILESET metadata för optimering av kart rendering. TILESET metadata innehåller till exempel en min-och Max zoomnings nivå för TILESET. Metadata innehåller också en avgränsnings ruta som definierar TILESET geografiska omfattning. Med markerings rutan kan ett program program mässigt ange rätt mitt punkt. Mer information om TILESET metadata finns i [TILESET List API](/rest/api/maps/tileset/listpreview).

När en TILESET har skapats kan den hämtas av [rendera v2-tjänsten](#render-v2-service).

Om en TILESET blir inaktuell och inte längre är användbar kan du ta bort TILESET. Mer information om hur du tar bort tilesets finns i  [data underhåll](#data-maintenance).

>[!NOTE]
>En TILESET är oberoende av den data uppsättning som den skapades från. Om du skapar tilesets från en data uppsättning och sedan uppdaterar den data uppsättningen kommer tilesets inte att uppdateras. För att avspegla ändringar i en data uppsättning måste du skapa nya tilesets. Om du tar bort en TILESET påverkas däremot inte data uppsättningen.

### <a name="feature-statesets"></a>Funktionen statesets

Funktionen statesets är samlingar med dynamiska egenskaper (*tillstånd*) som har tilldelats till data uppsättnings funktioner, till exempel rum eller utrustning. Ett exempel på ett *tillstånd* kan vara temperatur eller användning. Varje *tillstånd* är ett nyckel/värde-par som innehåller namnet på egenskapen, värdet och tidsstämpeln för den senaste uppdateringen.

Med [funktions tillstånds tjänsten](/rest/api/maps/featurestate/createstatesetpreview) kan du skapa och hantera en funktions-stateset för en data uppsättning. Stateset definieras av ett eller flera *tillstånd*. Varje funktion, till exempel ett rum, kan ha ett anslutet *tillstånd* .

Värdet för varje *tillstånd* i en stateset kan uppdateras eller hämtas av IoT-enheter eller andra program.  Om du till exempel använder [funktions tillstånds uppdaterings-API](/rest/api/maps/featurestate/updatestatespreview): t kan enheter som mäter utrymmes användningen systematiskt publicera status ändringen i ett rum.

Ett program kan använda funktionen stateset för att dynamiskt återge funktioner i en funktion enligt deras aktuella tillstånd och respektive kart stil. Mer information om hur du använder funktionen statesets till stil funktioner i en åter givnings karta finns i avsnittet om [webb-SDK-modul i inomhus](#indoor-maps-module).

>[!NOTE]
>Precis som tilesets påverkar inte den befintliga funktionen stateset och om du tar bort en funktion stateset påverkas inte den data uppsättning som den är kopplad till.

## <a name="using-indoor-maps"></a>Använda inomhus Maps

### <a name="render-v2-service"></a>Rendera v2-tjänsten

Azure Maps [rendera v2-tjänsten – Hämta kart panels-API (för hands version)](/rest/api/maps/renderv2/getmaptilepreview) har utökats för att stödja Creator (för hands version) tilesets.

Rendera v2-tjänsten – API: et för kart tillstånds panel gör att program kan begära tilesets. Tilesets kan sedan integreras i en kart kontroll eller SDK. Ett exempel på en kart kontroll som använder rendera v2-tjänsten finns i [modulen kartor inomhus](#indoor-maps-module).

### <a name="web-feature-service-api"></a>API för webb funktions tjänst

Data uppsättningar kan frågas med hjälp av [WFS-API (Web Feature service)](/rest/api/maps/wfs). WFS följer [Open GEOSPATIAL CONSORTIUM API-funktioner](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). Med WFS-API: et kan du fråga efter funktioner i själva data uppsättningen. Du kan till exempel använda WFS för att hitta alla Mötes rum i mitten av en specifik anläggning och golv nivå.

### <a name="indoor-maps-module"></a>Modul för inomhuskartor

[Azure Maps-webbsdk: n](./index.yml) innehåller modulen inomhus Maps. Den här modulen ger utökade funktioner till Azure Maps *Kartkontroll* -biblioteket. Modulen kartor i inomhus återger inliggande Maps som skapats i Creator (för hands version). Den integrerar widgetar som t. ex. *vånings väljare*, som hjälper användarna att visualisera olika våningar.

I modulen inmatnings kartor kan du skapa webb program som integrerar inliggande kart data med andra [Azure Maps tjänster](./index.yml). De vanligaste program inställningarna kan omfatta att lägga till kunskap till inomhus kartor från andra kartor, till exempel Road, bilder, väder och överföring.

Modulen inomhus Maps stöder också dynamisk Map-formatering. Stegvisa instruktioner för hur du implementerar funktionen stateset Dynamic format i ett program finns i [så här använder du modulen för](how-to-use-indoor-module.md) inaktive kar kartor

### <a name="azure-maps-integration"></a>Azure Maps-integrering

När du börjar utveckla lösningar för ingångs kartor kan du identifiera sätt att integrera befintliga Azure Maps-funktioner. Till exempel kan en till gångs spårning eller säkerhets scenarier implementeras med hjälp av [Azure Maps API för polystängsel](/rest/api/maps/spatial/postgeofence) med Creators inomhus Maps. API: et för polystängsel kan användas för att avgöra till exempel om en arbets tagare går in eller lämnar bestämda inomhus områden. Mer information om hur du ansluter Azure Maps med IoT-telemetri finns [här](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Data underhåll

 Med Azure Maps Creator (för hands version) lista, uppdatera och ta bort API kan du Visa, uppdatera och ta bort dina data uppsättningar, tilesets och funktions statesets.

>[!NOTE]
>När du granskar en lista över objekt och bestämmer dig för att ta bort dem, måste du ta hänsyn till effekten av borttagningen på alla beroende API: er eller program. Om du till exempel vill ta bort en TILESET som för närvarande används av ett program med hjälp av [rendering v2 – Hämta kart panels-API: et](/rest/api/maps/renderv2/getmaptilepreview)för att ta bort den TILESET skulle det leda till ett program haveri som TILESET.

### <a name="example-updating-a-dataset"></a>Exempel: uppdatera en data uppsättning

I följande exempel visas hur du uppdaterar en data uppsättning, skapar en ny TILESET och tar bort en gammal TILESET.

1. Följ stegen i avsnitten [Ladda upp ett ritnings paket](#upload-a-drawing-package) och [konvertera ett ritnings paket](#convert-a-drawing-package) för att ladda upp och konvertera det nya ritnings paketet.

2. Använd [data uppsättningen skapa API](/rest/api/maps/dataset/createpreview) för att lägga till konverterade data i den befintliga data uppsättningen.

3. Använd [TILESET skapa API](/rest/api/maps/tileset/createpreview) för att generera en ny TILESET från den uppdaterade data uppsättningen. Spara den nya tilesetId för steg 4.

4. Uppdatera TILESET-identifieraren i programmet för att aktivera visualiseringen av den uppdaterade Campus-datauppsättningen. Om den gamla TILESET inte längre används kan du ta bort den.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa en skapare (för hands version) karta](tutorial-creator-indoor-maps.md)