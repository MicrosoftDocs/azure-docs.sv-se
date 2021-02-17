---
title: Utöka Ontologies
titleSuffix: Azure Digital Twins
description: Läs om orsakerna och strategierna bakom utökning av en Ontology
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561547"
---
# <a name="extending-ontologies"></a>Utöka Ontologies 

En bransch standard [Ontology](concepts-ontologies.md), till exempel [DTDL-baserade RealEstateCore-Ontology för smarta byggnader](https://github.com/Azure/opendigitaltwins-building), är ett bra sätt att börja skapa din IoT-lösning. Bransch Ontologies tillhandahåller en omfattande uppsättning grundläggande gränssnitt som är utformade för din domän och som har utformats för att fungera direkt i Azure IoT-tjänster, till exempel Azure Digital-dubbla. 

Det är dock möjligt att din lösning kan ha speciella behov som inte täcks av branschens Ontology. Du kanske till exempel vill länka digitala dubbla till 3D-modeller som lagras i ett separat system. I det här fallet kan du utöka en av dessa Ontologies för att lägga till egna funktioner samtidigt som du behåller alla fördelar med den ursprungliga Ontology.

I den här artikeln används DTDL-baserade [RealEstateCore](https://www.realestatecore.io/) -Ontology som grund för exempel på att utöka Ontologies med nya DTDL-egenskaper. De metoder som beskrivs här är allmänna, och kan tillämpas på alla delar av en DTDL-baserad Ontology med en DTDL-funktion (telemetri, egenskap, relation, komponent eller kommando). 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore utrymme-hierarki 

I den DTDL-baserade RealEstateCore-Ontology används rymd-hierarkin för att definiera olika typer av utrymmen: rum, byggnader, zoner osv. Hierarkin sträcker sig från var och en av dessa modeller för att definiera olika typer av rum, byggnader och zoner. 

En del av hierarkin ser ut som diagrammet nedan. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Flödes diagram som illustrerar en del av RealEstateCore Space-hierarkin. På den högsta nivån finns det ett element som heter Space; den är ansluten med en utöknings pil ned en nivå till plats. Rummet är anslutet av två &quot;extends&quot;-pilar på en nivå till ConferenceRoom och Office."::: 

Mer information om RealEstateCore-Ontology finns i [*begrepp: anta bransch standard Ontologies*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology).

## <a name="extending-the-realestatecore-space-hierarchy"></a>Utöka RealEstateCore Space-hierarkin 

Ibland har din lösning vissa behov som inte täcks av branschens Ontology. I det här fallet kan du med utöka hierarkin fortsätta att använda branschens Ontology när du anpassar den efter dina behov. 

I den här artikeln diskuterar vi två olika fall där det är praktiskt att utöka Ontology-hierarkin: 

* Lägga till nya gränssnitt för koncept som inte beontologys i branschen. 
* Lägga till ytterligare egenskaper (eller relationer, komponenter, telemetri eller kommandon) till befintliga gränssnitt.

### <a name="add-new-interfaces-for-new-concepts"></a>Lägg till nya gränssnitt för nya koncept 

I det här fallet vill du lägga till gränssnitt för de begrepp som krävs för din lösning, men som inte finns i branschens Ontology. Om din lösning exempelvis har andra typer av rum som inte representeras i DTDL-baserade RealEstateCore-Ontology, kan du lägga till dem genom att utöka direkt från RealEstateCore-gränssnitten. 

Exemplet nedan visar en lösning som måste representera "fokus rum", som inte finns i RealEstateCore-Ontology. Ett fokuserat rum är ett litet utrymme som har utformats för att användare ska kunna fokusera på en uppgift under ett par timmar i taget. 

Om du vill utöka bransch Ontology med det nya konceptet skapar du ett nytt gränssnitt som [sträcker sig från](concepts-models.md#model-inheritance) gränssnitten i branschens Ontology. 

När du har lagt till fokus rums gränssnittet, visar den utökade hierarkin den nya rums typen. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Flödes diagram som illustrerar RealEstateCore utrymme-hierarkin från ovan, med ett nytt tillägg. På den nedre nivån med ConferenceRoom och Office finns ett nytt element med namnet FocusRoom (även anslutet via en utöknings pil från rummet)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Lägg till ytterligare funktioner i befintliga gränssnitt 

I det här fallet vill du lägga till fler egenskaper (eller relationer, komponenter, telemetri eller kommandon) till gränssnitt i branschen Ontology.

I det här avsnittet visas två exempel: 
* Om du skapar en lösning som visar 3D-ritningar av utrymmen som du redan har i ett befintligt system, kan du vilja associera varje digital grupp till dess 3D-ritning (med ID) så att även 3D-ritningen hämtas från det befintliga systemet när lösningen visar information om utrymmet. 
* Om din lösning behöver spåra statusen online/offline för konferens rum kanske du vill spåra konferens rummets status för användning i visning eller frågor. 

Båda exemplen kan implementeras med nya egenskaper: en `drawingId` egenskap som kopplar 3D-ritningen med den digitala dubbla och en "online"-egenskap som anger om konferens rummet är online eller inte. 

Normalt vill du inte ändra bransch Ontology direkt eftersom du vill kunna införliva uppdateringar i lösningen i din lösning i framtiden (som skulle skriva över dina tillägg). Dessa typer av tillägg kan i stället göras i din egen gränssnitts hierarki som sträcker sig från DTDL-baserade RealEstateCore-Ontology. Varje gränssnitt som du skapar använder flera gränssnitts arv för att utöka sitt överordnade RealEstateCore-gränssnitt och dess överordnade gränssnitt från den utökade gränssnitts-hierarkin. Med den här metoden kan du använda bransch Ontology och dina tillägg tillsammans. 

För att utöka bransch Ontology skapar du dina egna gränssnitt som utökar från gränssnitten i branschens Ontology och lägger till de nya funktionerna i de utökade gränssnitten. För varje gränssnitt som du vill utöka skapar du ett nytt gränssnitt. De utökade gränssnitten skrivs i DTDL (se avsnittet DTDL for Extended Interfaces senare i det här dokumentet). 

När du har utökat den del av hierarkin som visas ovan ser den utökade hierarkin ut som diagrammet nedan. Här lägger du till det utökade utrymmes gränssnittet och lägger till den `drawingId` egenskap som ska innehålla ett ID som associerar det digitala objektet med 3D-ritningen. Dessutom lägger ConferenceRoom-gränssnittet till en "online"-egenskap som kommer att innehålla online-statusen för konferens rummet. Genom arv innehåller ConferenceRoom-gränssnittet alla funktioner från RealEstateCore ConferenceRoom-gränssnittet, samt alla funktioner från det utökade utrymmes gränssnittet. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Flödes diagram som illustrerar hierarkin för utökade RealEstateCore-utrymmen från ovan, med fler nya tillägg. Room delar nu sin nivå med ett utrymmes element, som ansluter med en utöknings pil ned en nivå till ett nytt rums element bredvid ConferenceRoom och Office.  De nya elementen är anslutna till den befintliga Ontology med fler utöknings relationer."::: 

## <a name="using-the-extended-space-hierarchy"></a>Använda hierarkin för utökade utrymmen 

När du skapar digitala fält med hjälp av hierarkin utökat utrymme kommer varje digital-enhets modell att vara en från den utökade utrymmes-hierarkin (inte den ursprungliga branschens Ontology) och kommer att innehålla alla funktioner från branschens Ontology och de utökade gränssnitten, även om gränssnittet ärvs.

Varje digital-enhets modell är ett gränssnitt från den utökade hierarkin, som visas i diagrammet nedan. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Ett utdrag från RealEstateCore-hierarkin för utökade utrymmen, inklusive utrymme (toppnivå), ett rum (mellan nivå) och ConferenceRoom, Office och FocusRoom (lägre nivå). Namnen på modeller är anslutna till varje element (till exempel Room är anslutet till en modell med namnet Room101)."::: 

När du frågar efter digitala ledare med modell-ID ( `IS_OF_MODEL` operatorn) bör modell-ID från den utökade hierarkin användas. Till exempel `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Bidra tillbaka till den ursprungliga Ontology 

I vissa fall utökar du bransch Ontology på ett sätt som är brett användbart för de flesta användare av Ontology. I detta fall bör du överväga att bidra med dina tillägg till den ursprungliga Ontology. Varje Ontology har en annan process för att bidra, så kontrol lera ontologyens GitHub-lagringsplats för information om bidrag. 

## <a name="dtdl-for-new-interfaces"></a>DTDL för nya gränssnitt 

DTDL för nya gränssnitt som utökar direkt från branschens Ontology skulle se ut så här. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL för utökade gränssnitt 

DTDL för utökade gränssnitt, begränsade till den del som beskrivs ovan, ser ut så här. 

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

Fortsätt i sökvägen för att utveckla modeller baserade på Ontologies: [*använda Ontology-strategier i en modell utvecklings väg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).