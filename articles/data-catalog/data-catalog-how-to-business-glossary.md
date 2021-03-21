---
title: Konfigurera företags ord listan i Azure Data Catalog
description: Instruktions artiklar som visar företagets ord lista i Azure Data Catalog för att definiera och använda en gemensam affärs terminologi för att tagga registrerade data till gångar.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 6137072b443d54dfc63c2be69e20228d428ad1b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674724"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Konfigurera företags ord listan för styrt märkord

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion

Azure Data Catalog aktiverar identifiering av data källor så att du enkelt kan identifiera och förstå de data källor som du behöver för att utföra analyser och fatta beslut. Dessa funktioner ger största möjliga påverkan när du kan hitta och förstå de många tillgängliga data källorna.

En Data Catalog funktion som främjar bättre förståelse för till gångs data är taggning. Genom att använda taggning kan du associera nyckelord med en till gång eller en kolumn, vilket i sin tur gör det lättare att identifiera till gången via sökning eller bläddring. Taggning gör det också lättare att förstå till gångens kontext och syfte.

Taggning kan dock ibland orsaka problem av sig själv. Några exempel på problem som taggning kan introducera är:

* Användning av förkortningar på vissa till gångar och utökad text på andra. Den här inkonsekvensen hindrar identifieringen av till gångar, även om avsikten var att tagga till gångarna med samma tagg.
* Potentiella variationer i betydelse, beroende på kontext. Till exempel kan en tagg som kallas *intäkt* på en kund data uppsättning innebära intäkter per kund, men samma tagg i en kvartals försäljnings uppsättning kan innebära kvartals inkomst för företaget.  

Data Catalog innehåller en företags ord lista för att hjälpa dig att åtgärda dessa och andra liknande utmaningar.

Genom att använda Data Catalog affärs ord lista kan en organisation dokumentera viktiga affärs villkor och deras definitioner för att skapa en gemensam affärs vokabulär. Denna styrning möjliggör konsekvent användning av data i hela organisationen. När en term har definierats i företags ord listan kan den tilldelas till en data till gång i katalogen. Den här metoden, som *styr Taggar*, är samma metod som taggning.

## <a name="glossary-availability-and-privileges"></a>Tillgänglighet och privilegier för ord lista

Företags ord listan är bara tillgänglig i standard versionen av Azure Data Catalog. Den kostnads fria versionen av Data Catalog innehåller inte en ord lista och har inte funktioner för reglerad taggning.

Du kan komma åt företagets ord lista via alternativet **ord lista** på navigerings menyn i data Catalogs portalen.  

![Data Catalog – få åtkomst till företagets ord lista](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog administratörer och medlemmar av rollen ord lista administratörer kan skapa, redigera och ta bort ord listans villkor i företags ord listan. Alla Data Catalog användare kan visa term definitionerna och tagga till gångar med ord listans villkor.

![Data Catalog – Lägg till en ny ord lista](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Ord lista skapas

Data Catalog administratörer och ord lista administratörer kan skapa ord lista genom att klicka på knappen **nytt villkor** . Varje ord lista innehåller följande fält:

* En företags definition för termen
* En beskrivning som samlar in avsett användnings-eller affärs regler för till gången eller kolumnen
* En lista över intressenter som vet flest villkor
* Den överordnade termen, som definierar i vilken hierarki termen är organiserad

## <a name="glossary-term-hierarchies"></a>Ord listans hierarkier

Genom att använda Data Catalog affärs ord lista kan en organisation beskriva affärs terminologin som en hierarki med villkor, och det kan skapa en klassificering av villkor som bättre representerar företagets taxonomi.

En term måste vara unik på en särskild nivå i hierarkin. Dubblettnamn är inte tillåtna. Det finns ingen gräns för antalet nivåer i en hierarki, men en hierarki är ofta lättare att förstå när det finns tre nivåer eller färre.

Användning av hierarkier i företags ord listan är valfritt. Om du lämnar fältet överordnat villkor tomt för ord listans villkor skapas en fast (icke hierarkisk) lista över termer i ord listan.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagga till gångar med ord listans villkor

När ord listan har definierats i katalogen är upplevelsen av taggade till gångar optimerad för att söka i ord listan som en användare skriver en tagg. Data Catalog-portalen visar en lista över matchande ord listor som du kan välja bland. Om användaren väljer en ord lista i listan, läggs termen till till gången som en tagg (kallas även för en ord List etikett). Användaren kan också välja att skapa en ny tagg genom att skriva en term som inte finns i ord listan (kallas även för en användar tagg).

![Data till gång märkt med en användar tag och två ord lista](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Användar taggar är den enda typ av tagg som stöds i den kostnads fria versionen av Data Catalog.

### <a name="hover-behavior-on-tags"></a>Hov rings beteende för Taggar

I Data Catalog Portal är de två typerna av Taggar visuellt åtskilda och visar olika hov rings beteenden. När du hovrar över en användar tag kan du se etikettext och den eller de användare som har lagt till taggen. När du hovrar över en ord lista visas även definitionen av ord listan och en länk för att öppna företags ord listan för att visa hela definitionen av termen.

### <a name="search-filters-for-tags"></a>Sök filter för Taggar

Ord listans Taggar och användar taggar är både sökbara och du kan använda dem som filter i en sökning.

## <a name="summary"></a>Sammanfattning

Genom att använda företags ord listan i Azure Data Catalog och den reglerade tagg som det tillåter, kan du identifiera, hantera och identifiera data till gångar på ett konsekvent sätt. Företags ord listan kan främja inlärningen av affärs terminologin av organisations medlemmar. Ord listan har även stöd för att samla in meningsfulla metadata, vilket fören klar till gångs identifiering och förståelse.

## <a name="next-steps"></a>Nästa steg

* [REST API dokumentation för ord listans verksamhet](/rest/api/datacatalog/data-catalog-glossary)
