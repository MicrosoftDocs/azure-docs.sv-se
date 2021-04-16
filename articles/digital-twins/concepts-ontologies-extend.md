---
title: Utöka ontologier
titleSuffix: Azure Digital Twins
description: Lär dig mer om orsakerna och strategierna bakom att utöka en ontologi
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b38b4910773c433ed63fd2082c5cbefce81e0e9e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480238"
---
# <a name="extending-ontologies"></a>Utöka ontologier 

En [ontologi](concepts-ontologies.md)som är branschstandard, till exempel [DTDL-baserad RealEstateCore-ontologi](https://github.com/Azure/opendigitaltwins-building)för smarta byggnader, är ett bra sätt att börja skapa din IoT-lösning. Branschontologier tillhandahåller en omfattande uppsättning basgränssnitt som är utformade för din domän och utformade för att fungera utan vidare i Azure IoT-tjänster, till exempel Azure Digital Twins. 

Det är dock möjligt att din lösning har specifika behov som inte omfattas av branschens ontologi. Du kanske till exempel vill länka dina digitala tvillingar till 3D-modeller som lagras i ett separat system. I det här fallet kan du utöka en av dessa ontologier och lägga till dina egna funktioner samtidigt som du behåller alla fördelar med den ursprungliga ontologin.

I den här artikeln används den DTDL-baserade [RealEstateCore-ontologin](https://www.realestatecore.io/) som grund för exempel på att utöka ontologier med nya DTDL-egenskaper. De tekniker som beskrivs här är dock allmänna och kan tillämpas på valfri del av en DTDL-baserad ontologi med valfri DTDL-funktion (telemetri, egenskap, relation, komponent eller kommando). 

## <a name="realestatecore-space-hierarchy"></a>Utrymmeshierarki för RealEstateCore 

I den DTDL-baserade RealEstateCore-ontologin används utrymmeshierarkin för att definiera olika typer av utrymmen: Rum, Byggnader, Zon osv. Hierarkin sträcker sig från var och en av dessa modeller för att definiera olika typer av rum, byggnader och zoner. 

En del av hierarkin ser ut som diagrammet nedan. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="Flödesdiagram som illustrerar en del av utrymmeshierarkin RealEstateCore. På den översta nivån finns det ett element som heter Space. den är ansluten med en &quot;utökar&quot;-pil nedåt till Rummet. Rummet ansluts med två &quot;utökar&quot;-pilar ned en nivå till ConferenceRoom och Office."::: 

Mer information om Ontologin RealEstateCore finns i [*Begrepp: Använda ontologier som är branschstandard.*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)

## <a name="extending-the-realestatecore-space-hierarchy"></a>Utöka utrymmeshierarkin RealEstateCore 

Ibland har din lösning specifika behov som inte omfattas av branschens ontologi. I det här fallet kan du genom att utöka hierarkin fortsätta att använda branschens ontologi samtidigt som du anpassar den efter dina behov. 

I den här artikeln diskuterar vi två olika fall där en utökning av ontologins hierarki är användbar: 

* Lägga till nya gränssnitt för begrepp som inte finns i branschens ontologi. 
* Lägga till ytterligare egenskaper (eller relationer, komponenter, telemetri eller kommandon) i befintliga gränssnitt.

### <a name="add-new-interfaces-for-new-concepts"></a>Lägga till nya gränssnitt för nya koncept 

I det här fallet vill du lägga till gränssnitt för koncept som behövs för din lösning, men som inte finns i branschens ontologi. Om din lösning till exempel har andra typer av rum som inte representeras i den DTDL-baserade RealEstateCore-ontologin kan du lägga till dem genom att utöka dem direkt från RealEstateCore-gränssnitten. 

I exemplet nedan visas en lösning som måste representera "fokusrum" som inte finns i ontologin RealEstateCore. Ett fokusrum är ett litet utrymme som utformats för att människor ska kunna fokusera på en uppgift några timmar i taget. 

Om du vill utöka branschens ontologi med det här nya konceptet skapar du ett nytt gränssnitt som sträcker sig från gränssnitten i [branschens](concepts-models.md#model-inheritance) ontologi. 

När du har lagt till gränssnittet för fokusrum visar den utökade hierarkin den nya rumstypen. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="Flödesdiagram som illustrerar utrymmeshierarkin RealEstateCore ovan, med ett nytt tillägg. På den nedre nivån med ConferenceRoom och Office finns det ett nytt element som heter FocusRoom (ansluts också via en &quot;utökar&quot;-pil från Rummet)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Lägga till ytterligare funktioner i befintliga gränssnitt 

I det här fallet vill du lägga till fler egenskaper (eller relationer, komponenter, telemetri eller kommandon) i gränssnitt som finns i branschens ontologi.

I det här avsnittet visas två exempel: 
* Om du skapar en lösning som visar 3D-ritningar av utrymmen som du redan har i ett befintligt system kanske du vill associera varje digital tvilling med dess 3D-ritning (efter ID) så att när lösningen visar information om utrymmet kan den också hämta 3D-ritningen från det befintliga systemet. 
* Om din lösning behöver spåra online-/offlinestatus för konferensrum kanske du vill spåra statusen för konferensrum för användning i visning eller frågor. 

Båda exemplen kan implementeras med nya egenskaper: en egenskap som associerar 3D-ritningen med den digitala tvillingen och en "online"-egenskap som anger om konferensrum är `drawingId` online eller inte. 

Normalt vill du inte ändra branschens ontologi direkt eftersom du vill kunna lägga till uppdateringar i den i din lösning i framtiden (vilket skulle skriva över dina tillägg). I stället kan dessa typer av tillägg göras i din egen gränssnittshierarki som sträcker sig från den DTDL-baserade RealEstateCore-ontologin. Varje gränssnitt som du skapar använder flera gränssnittsarv för att utöka det överordnade RealEstateCore-gränssnittet och dess överordnade gränssnitt från din utökade gränssnittshierarki. Med den här metoden kan du använda branschens ontologi och dina tillägg tillsammans. 

För att utöka branschens ontologi skapar du dina egna gränssnitt som sträcker sig från gränssnitten i branschens ontologi och lägger till de nya funktionerna i dina utökade gränssnitt. För varje gränssnitt som du vill utöka skapar du ett nytt gränssnitt. De utökade gränssnitten är skrivna i DTDL (se avsnittet DTDL för utökade gränssnitt senare i det här dokumentet). 

När du har utökat den del av hierarkin som visas ovan ser den utökade hierarkin ut som i diagrammet nedan. Här lägger det utökade Space-gränssnittet till `drawingId` egenskapen som innehåller ett ID som associerar den digitala tvillingen med 3D-ritningen. Dessutom lägger ConferenceRoom-gränssnittet till en "online"-egenskap som innehåller onlinestatus för konferensrumet. Genom arv innehåller ConferenceRoom-gränssnittet alla funktioner från RealEstateCore ConferenceRoom-gränssnittet, samt alla funktioner från det utökade Space-gränssnittet. 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="Flödesdiagram som illustrerar den utökade RealEstateCore-utrymmeshierarkin ovan, med fler nya tillägg. Rummet delar nu sin nivå med ett Space-element, som ansluter med en &quot;utökar&quot;-pil nedåt en nivå till ett nytt rumselement bredvid ConferenceRoom och Office.  De nya elementen är anslutna till den befintliga ontologin med fler &quot;utökar&quot;-relationer."::: 

## <a name="using-the-extended-space-hierarchy"></a>Använda den utökade utrymmeshierarkin 

När du skapar digitala tvillingar med hjälp av den utökade Space-hierarkin kommer varje digital tvillingmodell att vara en från den utökade Space-hierarkin (inte den ursprungliga bransch ontologin) och innehåller alla funktioner från branschens ontologi och de utökade gränssnitten genom att använda gränssnittsarv.

Varje digital tvillings modell är ett gränssnitt från den utökade hierarkin, som visas i diagrammet nedan. 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="Ett utdrag från den utökade Utrymmeshierarkin RealEstateCore, inklusive Utrymme (översta nivån), ett Rum (mellannivå) och ConferenceRoom, Office och FocusRoom (lägre nivå). Namnen på modellerna är anslutna till varje element (till exempel är Rum anslutet till en modell som heter Room101)."::: 

När du frågar efter digitala tvillingar med hjälp av modell-ID :t (operatorn) ska modell-ID:n från `IS_OF_MODEL` den utökade hierarkin användas. Till exempel `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Bidra tillbaka till den ursprungliga ontologin 

I vissa fall kommer du att utöka branschens ontologi på ett sätt som är brett användbart för de flesta användare av ontologin. I det här fallet bör du överväga att bidra tillbaka dina tillägg till den ursprungliga ontologin. Varje ontologi har olika processer för att bidra, så se ontologins GitHub-lagringsplats för bidragsinformation. 

## <a name="dtdl-for-new-interfaces"></a>DTDL för nya gränssnitt 

DTDL för nya gränssnitt som sträcker sig direkt från branschens ontologi skulle se ut så här. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL för utökade gränssnitt 

DTDL för de utökade gränssnitten, begränsat till den del som beskrivs ovan, skulle se ut så här. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Nästa steg

Fortsätt på vägen för att utveckla modeller baserat på ontologier: [*Använda ontologistrategier i en modellutvecklingsväg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).