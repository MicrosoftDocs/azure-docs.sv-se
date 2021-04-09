---
title: Fäst lokalisera strategi
description: 'Lär dig mer om de olika strategierna när du anropar Sök-API: et'
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048484"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Förstå klassen AnchorLocateCriteria
I den här artikeln får du lära dig de olika alternativen som du kan använda när du frågar efter ett ankare. Vi kommer att gå över AnchorLocateCriteria-klassen, dess alternativ och giltiga alternativ kombinationer.

## <a name="anchor-locate-criteria"></a>Fäst Sök villkor
[Klassen AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) hjälper dig att fråga tjänsten för tidigare skapade ankare. Ett AnchorLocateCriteria-objekt kan användas per övervakare när som helst. Varje AnchorLocateCriteria-objekt måste innehålla **exakt en** av följande egenskaper: [identifierare](#identifiers), [NearAnchor](#nearanchor)eller [NearDevice](#neardevice). Ytterligare egenskaper som [strategi](#strategy), [BypassCache](#bypasscache)och [RequestedCategories](#requestedcategories) kan anges om du vill. 

### <a name="properties"></a>Egenskaper
Definiera **exakt en** av följande egenskaper i din Övervakare:
#### <a name="identifiers"></a>Identifierare
*Standardvärde: tom sträng mat ris*

Med hjälp av identifierare kan du definiera en lista med ankar-ID: n för ankare som du vill hitta. Anchor-ID: n returneras ursprungligen till dig efter att du har skapat ankaret. Med identifierade identifierare begränsar AnchorLocateCriteria uppsättningen med begärda ankare till ankare med matchande ankar-ID. Den här egenskapen anges med en sträng mat ris. 

#### <a name="nearanchor"></a>NearAnchor
*Standardvärde: inte angivet*

Med NearAnchor kan du ange att AnchorLocateCriteria begränsar uppsättningen av begärda ankare till ankare inom ett önskat avstånd från en vald fäst punkt. Du måste ange det här valda ankaret som käll ankare. Du kan också ange det önskade avståndet från käll fäst punkten och det maximala antalet ankare som returneras för att ytterligare begränsa sökningen.
Den här egenskapen anges med ett NearAnchorCriteria-objekt.

#### <a name="neardevice"></a>NearDevice
*Standardvärde: inte angivet*

Med NearDevice kan du ange att AnchorLocateCriteria begränsar uppsättningen av begärda ankare till de nära enhetens fysiska plats. Alla aktiverade sensorer kommer att användas för att identifiera ankare runt enheten. Om du vill ha bästa möjliga möjlighet att hitta ankare bör du konfigurera SensorCapabilities för att ge sessionen åtkomst till alla lämpliga sensorer. Mer information om hur du konfigurerar och använder den här egenskapen finns i [grov omlokalisering – Azure spatiala ankare | Microsoft docs](./coarse-reloc.md) och *hur du skapar och hittar ankare med grov omlokalisering* i [C#](../how-tos/set-up-coarse-reloc-unity.md), [mål-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Den här egenskapen anges med ett NearDeviceCriteria-objekt.

### <a name="additional-properties"></a>Ytterligare egenskaper
#### <a name="bypasscache"></a>BypassCache
*Standardvärde: falskt*

När ett ankare har skapats eller hittats i en session lagras det också i cacheminnet.  När den här egenskapen har angetts till False kommer alla efterföljande frågor i samma session att returnera det cachelagrade värdet. Ingen begäran till ASA-tjänsten har gjorts.

#### <a name="requestedcategories"></a>RequestedCategories
*Standardvärde: egenskaper | Geospatial*

Den här egenskapen används för att avgöra vilka data som returneras från en fråga med hjälp av AnchorLocateCriteria. Standardvärdet returnerar både egenskaper och spatialdata. Detta bör inte ändras om både egenskaper och spatialdata är önskade. Den här egenskapen kan anges med AnchorDataCategory-uppräkningen.

AnchorDataCategory Enum-värde | Returnerade data
-----|------------
Inget | Inga data returneras
Egenskaper| Fäst punkts egenskaper, inklusive AppProperties, returneras.
Spatial| Spatial information om ett ankare returneras.

#### <a name="strategy"></a>Strategi
*Standardvärde: AnyStrategy*

Strategin definierar ytterligare hur ankare ska placeras. Strategi egenskapen kan anges med en LocateStrategy-uppräkning.

LocateStrategy Enum-värde | Beskrivning
---------------|------------
AnyStrategy | Den här strategin gör det möjligt för systemet att använda kombinationer av VisualInformation och Relations strategier för att hitta ankare. 
VisualInformation|Den här strategin försöker hitta ankare genom att matcha visuell information från den aktuella omgivningen till dem i ankarets visuella objekt. Ett Ankares visuella objekt syftar på den visuella information som för närvarande är kopplad till ankaret. Den här visuella informationen är vanligt vis men inte exklusivt insamlad under skapandet av ankare. För närvarande tillåts denna strategi bara tillsammans med egenskaperna NearDevice eller Identifiers.
Relation|Den här strategin försöker hitta ankare genom att använda befintliga [anslutna ankare](./anchor-relationships-way-finding.md#connect-anchors). För närvarande tillåts denna strategi bara tillsammans med egenskaperna NearAnchor eller Identifiers. När det används med egenskapen identifierare, krävs det att användaren i samma session redan har hittat en eller flera ankare med redan upprättat kopplings relationer till de ankare vars ID: n har angetts i matrisen för identifierare. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Giltiga kombinationer av egenskaper för LocateStrategy och AnchorLocateCriteria 

Alla kombinationer av strategi-och AnchorLocateCriteria egenskaper tillåts för närvarande inte av systemet. I följande tabell visas tillåtna kombinationer:



Egenskap | AnyStrategy | Relation | VisualInformation
-------- | ------------|--------------|-------------------
Identifierare | &check;    | &check;     | &check;
NearAnchor  | &check;   (är som standard relationen) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Nästa steg

Se [hur du skapar och lokaliserar ankare med hjälp av Azures spatiala ankare](../create-locate-anchors-overview.md) för några fler exempel som använder klassen AnchorLocateCriteria.