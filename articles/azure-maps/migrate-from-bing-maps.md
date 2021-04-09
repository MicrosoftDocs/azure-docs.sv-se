---
title: 'Självstudie: Migrera från Bing Maps till Azure Maps | Microsoft Azure Maps'
description: 'En själv studie kurs om hur du migrerar från Bing Maps till Microsoft Azure Maps. Vägledningen vägleder dig genom hur du växlar till Azure Maps API: er och SDK: er.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388965"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Självstudie: Migrera från Bing Maps till Azure Maps

Den här guiden innehåller information om hur du migrerar webb-, mobil-och serverbaserade program från Bing Maps till Azure Maps-plattformen. Den här guiden innehåller jämför ande kod exempel, förslag på migrering och metod tips för migrering till Azure Maps. 

I den här självstudiekursen lär du dig:

> [!div class="checklist"]
> * Jämförelse på hög nivå för motsvarande Bing Maps-funktioner som finns i Azure Maps.
> * Vilka licensierings skillnader som bör beaktas.
> * Planera migreringen.
> * Här hittar du tekniska resurser och support.

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com). Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information om autentisering i Azure Maps finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Översikt över Azure Maps plattform

Azure Maps ger utvecklare från alla branscher kraftfulla geospatiala funktioner, förpackade med de nya mappnings data som är tillgängliga för att tillhandahålla geografisk kontext för webb-och mobil program. Azure Maps är en Azure One API-kompatibel uppsättning REST API: er för kartor, sökning, routning, trafik, tids zoner, inhägnad, mappa data, väder data och många fler tjänster som åtföljs av både webb-och Android-SDK: er för att göra utvecklingen enkel, flexibel och portabel över flera plattformar. [Azure Maps finns också i Power BI](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Plattforms jämförelse på hög nivå

I följande tabell finns en lista över Bing Maps-funktioner på hög nivå och det relativa stödet för dessa funktioner i Azure Maps. Den här listan innehåller inte ytterligare Azure Maps funktioner som tillgänglighet, avgränsnings-API: er, trafik tjänster, spatiala åtgärder, åtkomst till direkt kart panel och batch Services.

| Bing Maps-funktion                     | Azure Maps support |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Planerad            |
| UWP SDK                               | Ej tillämpligt                 |
| WPF SDK                               | Ej tillämpligt                 |
| API: er för REST service                     | ✓                  |
| Automatiska förslag                           | ✓                  |
| Vägvisningar (inklusive Truck)          | ✓                  |
| Avstånds mat ris                       | ✓                  |
| Utökade privilegier                            | ✓ (För hands version)        |
| Bilder – statisk karta                  | ✓                  |
| Bilder metadata                      | ✓                  |
| ISO Kron                            | ✓                  |
| Lokala insikter                        | ✓                  |
| Lokal sökning                          | ✓                  |
| Plats igenkänning                  | ✓                  |
| Platser (omvänt/omvända landskod) | ✓                  |
| Optimerade res vägar            | Planerad            |
| Fäst vid vägar                         | ✓                  |
| Avstånds Data Services (SDS)           | Delvis            |
| Tidszon                             | ✓                  |
| Trafik incidenter                     | ✓                  |
| Konfigurations drivna kartor             | Ej tillämpligt                |

Bing Maps innehåller grundläggande nyckelbaserad autentisering. Azure Maps ger både grundläggande nyckelbaserad autentisering och mycket säker Azure Active Directory autentisering.

## <a name="licensing-considerations"></a>Licens överväganden

När du migrerar till Azure Maps från Bing Maps bör följande information beaktas med avseende på licensiering.

* Azure Maps avgifter för användning av interaktiva kartor baserat på antalet inlästa kart paneler, medan Bing Maps debiteras för inläsningen av kart kontrollen (sessioner). För att minska kostnaderna för utvecklare Azure Maps automatiskt cachelagrar kartor. En Azure Maps transaktion skapas för varje 15 kart paneler som läses in. I de interaktiva Azure Maps SDK: erna används 512-pixel-paneler och i genomsnitt genereras en eller färre transaktioner per sid-vy.

* Azure Maps tillåter att data från sin plattform lagras i Azure. Den kan också cachelagras på andra platser i upp till sex månader enligt [användnings villkoren](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Här följer några licens-relaterade resurser för Azure Maps:

-   [Sidan Azure Maps prissättning](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps användnings villkor](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (ingår i villkoren för Microsoft Online Services)
-   [Välj rätt pris nivå i Azure Maps](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Föreslagen migrations plan

Här är ett exempel på en plan med hög nivå migrering.

1.  Ta reda på vad Bing Maps SDK: er och tjänster som ditt program använder och kontrol lera att Azure Maps tillhandahåller alternativa SDK: er och tjänster som du kan migrera till.
2.  Skapa en Azure-prenumeration (om du inte redan har en) på <https://azure.com> .
3.  Skapa ett Azure Maps konto ([dokumentation](./how-to-manage-account-keys.md)) och en autentiseringsnyckel eller Azure Active Directory ([dokumentation](./how-to-manage-authentication.md)).
4.  Migrera din program kod.
5.  Testa det migrerade programmet.
6.  Distribuera det migrerade programmet till produktion.

## <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

Följ dessa steg om du vill skapa ett Azure Maps konto och få åtkomst till Azure Maps-plattformen:

1. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Skapa ett [Azure Maps-konto](./how-to-manage-account-keys.md).
4. [Hämta din Azure Maps prenumerations nyckel](./how-to-manage-authentication.md#view-authentication-details) eller konfigurera Azure Active Directory autentisering för förbättrad säkerhet.

## <a name="azure-maps-technical-resources"></a>Azure Maps tekniska resurser

Här är en lista över användbara tekniska resurser för Azure Maps.

* Översikt: <https://azure.com/maps>
* Handlingar <https://aka.ms/AzureMapsDocs>
* Kod exempel för webb-SDK: <https://aka.ms/AzureMapsSamples>
* Forum för utvecklare: <https://aka.ms/AzureMapsForums>
* Videos <https://aka.ms/AzureMapsVideos>
* Blogg <https://aka.ms/AzureMapsBlog>
* Azure Maps feedback (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Stöd för migrering

Utvecklare kan söka efter stöd för migrering via [forumen](/answers/topics/azure-maps.html) eller via ett av de många support alternativen för Azure: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Ny terminologi

Följande lista innehåller vanliga villkor för Bing Maps och deras motsvarande Azure Maps villkor.

| Villkor för Bing Maps                    | Azure Mapss period                                                |
|-----------------------------------|----------------------------------------------------------------|
| Satellitvy                            | Satellit eller antenn                                            |
| Anvisningar                        | Kan även kallas routning                             |
| Entiteter                          | Geometries eller funktioner                                         |
| `EntityCollection`                | Data källa eller lager                                           |
| `Geopoint`                        | Position                                                       |
| `GeoXML`                          | XML-filer i den spatiala IO-modulen                             |
| Mark överlägg                    | Bild skikt                                                    |
| Hybrid (i referens till mappnings typ) | Satellit med vägar                                           |
| Inforuta                           | Popup                                                          |
| Location                          | Position                                                       |
| `LocationRect`                    | Avgränsnings ruta                                                   |
| Map Type                          | Kart stil                                                      |
| Navigeringsfält                    | Kartans stil väljare, zoomnings kontroll, bredd kontroll, kompass kontroll |
| Kartnålsikon                           | Bubbel lager, symbol lager eller HTML-markör                      |

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du migrerar ditt Bing Maps-program med följande artiklar:

> [!div class="nextstepaction"]
> [Migrera en webbapp](migrate-from-bing-maps-web-app.md)
