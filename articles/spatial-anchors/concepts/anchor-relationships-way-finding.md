---
title: Relationer mellan fästpunkter och navigering
description: Lär dig mer om den konceptuella modellen bakom ankar förhållanden. Lär dig att ansluta ankare inom ett utrymme och att använda närliggande API för att uppfylla ett sätt att söka efter ett scenario.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696155"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Fäst relationer och hur du hittar dem i Azure spatiala ankare

Genom att använda ankar relationer kan du skapa anslutna ankare i ett utrymme och sedan ställa frågor så här:

* Finns det några ankare i närheten?
* Hur långt bort är det?

## <a name="examples"></a>Exempel

Du kan använda anslutna ankare i sådana fall som följande:

* En arbets uppgift måste utföra en uppgift som omfattar att besöka olika platser i en industri fabrik. Fabriken har spatial ankare på varje plats. En HoloLens-eller mobilapp hjälper dig att hjälpa arbets tagaren från en plats till nästa. Appen ber först om de nära spatiala ankarena och vägleder sedan arbets tagaren till nästa plats. Appen visar allmän riktning och avstånd till nästa plats.

* En Museum skapar spatiala ankare vid offentliga skärmar. Dessa ankare utgör tillsammans en entimmes guidad visning av Museum: s viktiga offentliga skärmar. På en offentlig skärm kan besökare öppna Museum-appen för Mixad verklighet på sin mobila enhet. Sedan pekar de sin telefon kamera runt utrymmet för att se den allmänna riktningen och avståndet till de andra offentliga visningarna i visningen. När en användare vägleder sig mot en offentlig visning uppdaterar appen den allmänna riktningen och avståndet för att hjälpa användaren.

## <a name="set-up-way-finding"></a>Konfigurera hur du söker

En app som använder stegvis riktning och avstånd mellan ankare för att ge vägledning använder *väg-find*. Sättet att hitta skiljer sig från att navigera i tur och upp. I tur och inaktive-navigering är användarna interaktiva runt väggar, via dörrar och mellan golv. Med hjälp av kan användaren upptäcka tips om den allmänna riktningen för målet. Men det hjälper också användaren att navigera genom strukturen till målet.

Om du vill skapa en metod för att hitta kan du först förbereda ett utrymme för upplevelsen och utveckla en app som användarna kommer att interagera med. Detta är de konceptuella stegen:

1. **Planera utrymmet**: Bestäm vilka platser inom utrymmet som ska ingå i hur man hittar dem. I våra scenarier kan fabriks ledaren eller Museum Tour-koordinatorn bestämma vilka platser som ska ingå i hur man hittar dem.
2. **Anslut fäst punkter**: besök de valda platserna för att skapa spatiala ankare. Du kan göra detta i ett administratörs läge för slutanvändarens app eller i en annan app helt och hållet. Du ansluter eller relaterar varje fäst punkt till de andra. Tjänsten underhåller dessa relationer.
3. **Starta slutanvändarens upplevelse**: användarna kör appen för att hitta en fäst punkt som kan finnas på någon av de valda platserna. Den övergripande designen bör fastställa de platser där användarna kan ange upplevelsen.
4. **Hitta närliggande ankare**: när användaren har hittat en fäst punkt kan appen begära närliggande ankare. Den här proceduren returnerar en attityd mellan enheten och dessa ankare.
5. **Vägleder användaren**: appen kan använda attityden för var och en av dessa ankare för att ge vägledning om användarens allmänna riktning och avstånd. Till exempel kan kamera flödet i appen Visa en ikon och en pil som representerar varje möjligt mål, vilket visas i följande bild.
6. **Förfina vägledningen**: när användaren går kan appen regelbundet beräkna en ny attityd mellan enheten och mål ankaret. Appen fortsätter att förfina vägledningarna som hjälper användaren att komma till målet.

    ![Ett exempel på hur en app kan visa vägledning om hur du hittar dem](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ansluta ankare

För att du ska kunna bygga ett sätt att hitta en miljö måste du först placera fäst punkterna på de valda platserna. I det här avsnittet förutsätter vi att appens administratör redan har avslutat det här arbetet.

### <a name="connect-anchors-in-a-single-session"></a>Ansluta ankare i en enda session

Så här ansluter du ankare:

1. Gå till den första platsen och skapa fäst punkten A med hjälp av en CloudSpatialAnchorSession.
2. Gå till den andra platsen. Den underliggande MR/AR-plattformen spårar rörelsen.
3. Skapa fäst punkt B med samma CloudSpatialAnchorSession. Ankare A och B är nu anslutna. Tjänsten spatial ankare underhåller den här relationen.
4. Fortsätt med proceduren för återstående ankare.

### <a name="connect-anchors-in-multiple-sessions"></a>Ansluta ankare i flera sessioner

Du kan ansluta spatiala ankare över flera sessioner. Med den här metoden kan du skapa och ansluta vissa ankare samtidigt och sedan skapa och ansluta flera ankare senare.

Så här ansluter du ankare över flera sessioner:

1. Appen skapar vissa ankare (ankare A och B) i en CloudSpatialAnchorSession.
2. Vid en annan tidpunkt hittar appen en av dessa ankare (till exempel ankare A) med hjälp av en ny CloudSpatialAnchorSession.
3. Gå till en ny plats. Den underliggande plattformen för Mixad verklighet eller förstärkt verklighet spårar rörelsen.
4. Skapa fäst punkt C med samma CloudSpatialAnchorSession. Ankare A, B och C är nu anslutna. Tjänsten spatial ankare underhåller den här relationen.

Du kan fortsätta med den här proceduren för fler ankare och fler sessioner över tid.

### <a name="verify-anchor-connections"></a>Verifiera fäst anslutningar

Appen kan verifiera att två ankare är anslutna genom att utfärda en fråga för närliggande ankare, vilket du gör genom att ange `NearAnchorCriteria` på en `CloudSpatialAnchorWatcher` . När frågans resultat innehåller mål ankaret, verifieras ankar anslutningen. Om ankarena inte är anslutna kan appen försöka ansluta dem igen.

Här följer några orsaker till varför ankare kanske inte kan ansluta:

* Den underliggande plattformen för Mixad verklighet eller förhöjd verklighet förlorade spårning under processen för att ansluta ankare.
* På grund av ett nätverks fel under kommunikationen med tjänsten spatiala ankare, gick det inte att spara ankar anslutningen.

### <a name="find-sample-code"></a>Hitta exempel kod

För att hitta exempel kod som visar hur du ansluter ankare och gör närliggande frågor, se [spatiala ankare exempel appar](https://github.com/Azure/azure-spatial-anchors-samples).
