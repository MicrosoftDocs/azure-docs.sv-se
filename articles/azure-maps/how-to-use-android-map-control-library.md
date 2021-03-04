---
title: Komma igång med Android Map-kontroll | Microsoft Azure Maps
description: Bekanta dig med Azure Maps Android SDK. Se hur du skapar ett projekt i Android Studio, installerar SDK och skapar en interaktiv karta.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 5888a5f34ef65fc1015b6e73af1d03368a8329b2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098527"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Komma igång med Azure Maps Android SDK

Azure Maps Android SDK är ett vektor kart bibliotek för Android. Den här artikeln vägleder dig genom processerna för att installera Azure Maps Android SDK och läsa in en karta.

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) .

## <a name="localizing-the-map"></a>Lokalisera kartan

Azure Maps Android SDK innehåller tre olika sätt att ange språk och regional vy för kartan. Följande kod visar hur du ställer in språket på franska ("fr-FR") och den regionala vyn till "Auto".

Det första alternativet är att skicka språket och Visa regional information i `AzureMaps` klassen med hjälp av metoderna static `setLanguage` och `setView` Global. Detta anger standard språket och den regionala vyn för alla Azure Maps kontroller som läses in i din app.

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Alternatively use Azure Active Directory authenticate.
    //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

Det andra alternativet är att skicka språket och Visa information i kart kontrollens XML-fil.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Det tredje alternativet är att program mässigt ange språket och den regionala vyn för kartan med hjälp av Maps- `setStyle` metoden. Detta kan göras när som helst för att ändra språket och den regionala vyn för kartan.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Här är ett exempel på Azure Maps med språket "fr-FR" och regional View inställd på "Auto".

![Azure Maps, kart bild som visar etiketter på franska](media/how-to-use-android-map-control-library/android-localization.png)

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="navigating-the-map"></a>Navigera i kartan

Det finns flera olika sätt på vilka kartan kan zoomas, panoreras, roteras och visas. Följande information innehåller alla olika sätt att navigera i kartan.

**Zooma kartan**

* Tryck på kartan med två fingrar och dra ihop för att zooma ut eller sprida fingrarna från varandra för att zooma in.
* Dubbel tryck på kartan för att zooma in en nivå.
* Dubbel tryck med två fingrar för att zooma ut kartan med en nivå.
* Tryck två gånger; vid andra knackningar håller du fingret på kartan och drar upp för att zooma in eller ned för att zooma ut.

**Panorera kartan**

* Vidrör kartan och dra i vilken riktning som helst.

**Rotera kartan**

* Vidrör kartan med två fingrar och rotera.

**Bredd på kartan**

* Vidrör kartan med två fingrar och dra dem uppåt eller nedåt.

## <a name="azure-government-cloud-support"></a>Azure Government Cloud Support

Azure Maps Android SDK stöder Azure Government molnet. Azure Maps Android SDK nås från samma maven-lagringsplats. Följande uppgifter måste göras för att ansluta till Azure Government Cloud-versionen av Azure Mapss plattformen.

På samma plats där Azure Maps autentiseringsinformation anges lägger du till följande kodrad för att se till att kartan använder moln domänen Azure Maps myndighet.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Se till att använda Azure Maps autentiseringsinformation från Azure Government moln plattform när du autentiserar kartan och tjänsterna.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till överläggs data på kartan:

> [!div class="nextstepaction"]
> [Hantera autentisering i Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Ändra kart format i Android Maps](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
