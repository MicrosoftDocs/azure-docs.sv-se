---
title: Microsoft Azure Maps väder tjänster (för hands version) vanliga frågor och svar
description: Få svar på vanliga frågor om Azure Maps väder tjänster (för hands version) data och funktioner.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9c8e971b4fda313ffede58455dd6d057d6848ce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678137"
---
# <a name="azure-maps-weather-services-preview-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Maps väder tjänster (för hands version)

> [!IMPORTANT]
> Azure Maps väder tjänster finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller svar på vanliga frågor om Azure Maps data och funktioner för [väder tjänster](/rest/api/maps/weather) . Följande avsnitt beskrivs:

* Data källor och data modeller
* Täckning och tillgänglighet för väder tjänster
* Data uppdaterings frekvens
* Utveckla med Azure Maps SDK: er
* Alternativ för att visualisera väder data, inklusive Microsoft Power BI-integrering

## <a name="data-sources-and-data-models"></a>Data källor och data modeller

**Hur Azure Maps data källans väder data?**

Azure Maps har skapats med samarbete mellan mobilitets-och plats teknik partner i världs klass, inklusive AccuWeather, som tillhandahåller underliggande väder data. För att läsa meddelande om Azure Maps "samarbete med AccuWeather, se [regn eller Skin: Azure Maps väder tjänster kommer att få insikter om ditt företag](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/).

AccuWeather har information om väder och miljö i real tid som är tillgänglig överallt i världen, i stort sett på grund av deras partnerskap med ett stort antal nationella myndigheter och andra patentskyddade avtal. En lista med den grundläggande informationen nedan finns nedan.

* Offentligt tillgängliga globala Surface-observationer från myndighets myndigheter
* Data uppsättningar för patentskyddade observationer från myndigheter och privata företag
* Data med hög upplöst radar för över 40 länder/regioner
* Förstklassiga globala blixt data i real tid
* Myndighets utfärdade väderleks varningar för över 60 länder/regioner och områden
* Satellit data från polystations väder satelliter som täcker hela världen
* Över 150 numeriska prognos modeller, inklusive intern, tillverkarspecifik modellering, myndighets modeller som amerikanskt global prognos system (GFS) och unika downscaled-modeller som tillhandahålls av privata företag
* Observationer av luft kvalitet
* Observationer från transport avdelningar

Många tusentals observationer av ytan, tillsammans med andra data, är införlivade för att skapa och påverka de aktuella villkor som görs tillgängliga för användarna. Detta inkluderar inte bara fritt tillgängliga standard data uppsättningar, utan även unika observationer som erhållits från nationella meteorologiska tjänster i många länder/regioner, inklusive Indien, Brasilien och Kanada och andra patentskyddade indata. Dessa unika data uppsättningar ökar den spatiala och temporala matchningen av aktuella villkors data för våra användare. 

De här data uppsättningarna granskas i real tid för att uppnå precision för det digitala prognos systemet, som använder sig av AccuWeather-algoritmer för att kontinuerligt ändra prognoserna, vilket säkerställer att de alltid införlivar de senaste data och därmed maximerar sin kontinuerliga noggrannhet.

**Vilka modeller skapar väder prognos data?**

Många väder väglednings system används för att formulera globala prognoser. Över 150 numeriska prognos modeller används varje dag, både externa och interna data uppsättningar. Detta omfattar myndighets modeller som Europeiska centrets ECMWF och USA: s globala prognos system (GFS). Dessutom kan AccuWeather införliva patentskyddade högpresterande modeller som downscale prognoser till specifika platser och strategiska regionala domäner för att förutse väder med ytterligare noggrannhet. AccuWeather unika blandnings-och viktnings algoritmer har utvecklats under de senaste årtiondena. Dessa algoritmer försäkrar optimalt de många prognos inmatningarna för att tillhandahålla mycket exakta prognoser.

## <a name="weather-services-preview-coverage-and-availability"></a>Täckning och tillgänglighet för väder tjänster (för hands version)

**Vilken typ av täckning kan jag förväntar mig för olika länder/regioner?**

Täckningen för väder tjänster varierar beroende på land/region. Alla funktioner är inte tillgängliga i varje land/region. Mer information finns i [dokumentation om täckning](./weather-coverage.md).

## <a name="data-update-frequency"></a>Data uppdaterings frekvens

**Hur ofta uppdateras aktuella villkors data?**

Aktuella villkors data är ungefär uppdaterade minst en gång i timmen, men kan uppdateras oftare med snabbt föränderliga villkor – till exempel stora temperatur ändringar, förändringar i luft rummet, ändringar i utfällning och så vidare. De flesta observations stationer runtom i världen rapporterar många gånger per timme när villkoren ändras. Några områden kommer dock fortfarande bara att uppdateras en gång, två gånger eller fyra gånger per timme vid schemalagda intervall.  

Azure Maps cachelagrar aktuella villkors data i upp till 10 minuter för att under lätta uppdaterings frekvensen för data i nära real tid. Om du vill se när det cachelagrade svaret upphör att gälla och undvika att Visa inaktuella data kan du använda informationen om förfallet huvud i HTTP-huvudet för Azure Maps API-svaret.

**Hur ofta uppdateras dagliga och Tim prognos data?**

Dagliga och Tim prognos data uppdateras flera gånger per dag, eftersom uppdaterade observationer tas emot.  Om till exempel en prognostiserad hög/låg temperatur överskrids, kommer prognos data att justeras vid nästa uppdaterings cykel. Detta kan inträffa i olika intervall men sker vanligt vis inom en timme. Många plötsliga väder förhållanden kan orsaka en ändring av prognos data. Till exempel, på en varm sommaren, kan en isolerad Thunderstorm plötsligt ta slut, och få tung moln täckning och regn. Den isolerade Storm kan släppa temperaturen effektivt med så mycket som 10 grader. Det nya temperatur värdet påverkar Tim-och dags prognoser för resten av dagen, och därför kommer de att uppdateras i våra data uppsättningar.

API: er för Azure Mapss prognos cachelagras i upp till 30 minuter. Om du vill se när det cachelagrade svaret upphör att gälla och undvika att Visa inaktuella data kan du använda informationen om förfallet huvud i HTTP-huvudet för Azure Maps API-svaret. Vi rekommenderar att du uppdaterar efter behov baserat på en bestämd produkt användnings fall och användar gränssnitt (användar gränssnitt).

## <a name="developing-with-azure-maps-sdks"></a>Utveckla med Azure Maps SDK: er

**Har Azure Maps Web SDK inbyggt stöd för väder tjänster (för hands version)?**

Azure Maps Web SDK tillhandahåller en Services-modul. Modulen tjänster är ett hjälp bibliotek som gör det enkelt att använda Azure Maps REST-tjänsterna i webb-eller Node.js program. med hjälp av Java Script eller TypeScript. Information om hur du kommer igång finns i vår [dokumentation](./how-to-use-services-module.md).

**Stöder Azure Maps Android SDK intern support för väder tjänster (för hands version)?**

Azure Maps Android SDK: er stöder Mercator panels lager som kan ha x/y/zoomnings format, fyr nyckels notation eller EPSG 3857-avgränsnings ruta.

Vi planerar att skapa en Services-modul för Java/Android som liknar Web SDK-modulen. Modulen Android-tjänster gör det enkelt att komma åt alla Azure Maps-tjänster i en Java-eller Android-app.  

## <a name="data-visualizations"></a>Datavisualiseringar  

**Har Azure Maps Power BI Visual support Azure Maps väder paneler?**

Ja. Information om hur du migrerar radar-och infraröda satellit paneler till Microsoft Power BI Visual finns i [lägga till ett panel lager till Power BI visuella](./power-bi-visual-add-tile-layer.md)objekt. 

**Hur gör jag för att tolka färger som används för radar-och satellit paneler?**

Artikeln Azure Maps [väder begrepp](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) innehåller en guide som hjälper dig att tolka färger som används för radar-och satellit paneler. I artikeln beskrivs färg exempel och HEXADECIMALa färg koder.
 
**Kan jag skapa radar-och satellit panels animeringar?**

Ja. Förutom radar-och satellit paneler i real tid kan Azure Maps kunder begära tidigare och framtida paneler för att förbättra data visualiseringar med kart överlägg. Detta kan göras genom att anropa [API: t för kartans panel v2](/rest/api/maps/renderv2/getmaptilepreview) eller genom att begära paneler via Azure Maps Web SDK. Radar paneler är tillgängliga i upp till 1,5 timmar förr och upp till 2 timmar i framtiden. Panelerna och är tillgängliga i 5-minuters intervall. IR-paneler tillhandahålls för upp till 3 timmar tidigare och är tillgängliga i 10-minuters intervall. Mer information finns i [exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)på en väder panel med öppen källkod.  

**Erbjuder du ikoner för olika väder förhållanden?**

Ja. Du kan hitta ikoner och deras respektive koder [här](./weather-services-concepts.md#weather-icons). Observera att endast vissa av API: erna för väder tjänst (för hands version), till exempel  [Hämta aktuella villkor](/rest/api/maps/weather/getcurrentconditionspreview), returnerar *iconCode* i svaret. Mer information finns i det aktuella [exemplet med kod exempel](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)för öppen källkod i WeatherConditions.

## <a name="next-steps"></a>Nästa steg

Om dessa vanliga frågor inte besvarar din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

* Avsnittet kommentarer i den här artikeln.
* [MSFT Q&en sida för Azure Maps](/answers/topics/azure-maps.html).
* Microsoft Support. Om du vill skapa en ny supportbegäran går du till fliken hjälp i [Azure Portal](https://portal.azure.com/), väljer **Hjälp +** support-knappen och väljer sedan **ny supportbegäran**.
* [Azure Maps UserVoice](https://feedback.azure.com/forums/909172-azure-maps) för att skicka funktions förfrågningar.

Lär dig hur du begär real tids data och prognostiserade väder data med hjälp av Azure Maps väder tjänster (för hands version):
> [!div class="nextstepaction"]
> [Begär väder data i real tid ](how-to-request-weather-data.md)

Koncept artikel för Azure Maps väder tjänster (för hands version):
> [!div class="nextstepaction"]
> [Begrepp för vädertjänster](weather-coverage.md)

Utforska API-dokumentationen för Azure Maps väder tjänster (för hands version):

> [!div class="nextstepaction"]
> [Azure Maps vädertjänster](/rest/api/maps/weather)