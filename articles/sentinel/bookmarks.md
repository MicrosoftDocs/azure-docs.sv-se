---
title: Använd jakt bok märken för data utredningar i Azure Sentinel
description: Den här artikeln beskriver hur du använder bok märkes bok för Azure Sentinel för att hålla reda på data.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94651320"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Hålla koll på data när du jagar hot med Azure Sentinel

Hot jakt kräver vanligt vis en granskande av berg av loggdata som letar efter bevis på skadligt beteende. Under den här processen kan du söka efter händelser som de vill komma ihåg, gå tillbaka och analysera som en del av att verifiera potentiella Hypotheses och förstå den fullständiga artikeln i en kompromiss.

Med bok märken i Azure Sentinel kan du göra detta genom att bevara frågorna som du körde i **Azure Sentinel-loggar**, tillsammans med frågeresultaten som du anser är relevanta. Du kan också registrera dina kontextbaserade observationer och lägga till referenser till dina upptäckter genom att lägga till kommentarer och taggar. Genom att visa bokmärkta data blir det enklare för dig och andra teammedlemmar att samarbeta.

Du kan när som helst gå tillbaka till bok märkes data på fliken **bok märken** i **jakt** fönstret. Du kan använda filtrerings- och sökalternativ för att snabbt hitta specifika data för din aktuella undersökning. Du kan också visa dina data bok märken direkt i **HuntingBookmark** -tabellen i din Log Analytics-arbetsyta. Exempel:

> [!div class="mx-imgBorder"]
> ![Visa HuntingBookmark-tabell](./media/bookmarks/bookmark-table.png)

Genom att Visa bok märken från tabellen kan du filtrera, sammanfatta och koppla data från bok märken med andra data källor, vilket gör det lätt att söka efter bekräftande bevis.

Om du hittar något som brådskande måste åtgärdas under för hands versionen av dina loggar i ett par klick kan du skapa ett bok märke och befordra det till en incident eller lägga till bok märket i en befintlig incident. Mer information om incidenter finns i [Självstudier: Undersök incidenter med Azure Sentinel](tutorial-investigate-cases.md). 

I för hands versionen kan du också visualisera dina data bok märken genom att klicka på **Undersök** från bok märkes information. Detta startar en undersökning i vilken du kan visa, undersöka och visuellt förmedla dina resultat med ett interaktivt diagram och en tids linje.

## <a name="add-a-bookmark"></a>Lägga till ett bokmärke

1. I Azure Portal navigerar du till **kontroll**  >  **Threat Management**  >  **jakt** för att köra frågor om misstänkt och avvikande beteende.

2. Välj en av jakt frågorna och till höger, i frågor om jakt, väljer du **Kör fråga**. 

3. Välj **Visa frågeresultat**. Exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Visa frågeresultaten från Azure Sentinel-jakt](./media/bookmarks/new-processes-observed-example.png)
    
    Den här åtgärden öppnar frågeresultaten i fönstret **loggar** .

4. Använd kryss rutorna i listan logg frågeresultat för att välja en eller flera rader som innehåller den information som du hittar intressant.

5. Välj **Lägg till bok märke**:
    
    > [!div class="mx-imgBorder"]
    > ![Lägg till jakt bok märke i fråga](./media/bookmarks/add-hunting-bookmark.png)

6. Till höger, i fönstret **Lägg till bok märke** , kan du också uppdatera bok märkes namnet, lägga till taggar och kommentarer som hjälper dig att identifiera vad som var intressant för objektet.

7. I avsnittet **information om frågor** använder du List rutorna för att extrahera information från frågeresultaten för entitetstyper för **konto**, **värd** och **IP-adress** . Den här åtgärden mappar den valda entitetstypen till en viss kolumn från frågeresultatet. Exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Mappa entitetstyper för jakt bok märken](./media/bookmarks/map-entity-types-bookmark.png)
    
    Om du vill visa bok märket i undersöknings diagrammet (för närvarande i för hands version) måste du mappa minst en entitetstyp som antingen är **konto**, **värd** eller **IP-adress**. 

5. Klicka på **Spara** för att genomföra ändringarna och lägga till bok märket. Alla data som har bok märken delas med andra Utforskare, och är ett första steg mot en gemensam gransknings upplevelse.

 
> [!NOTE]
> Resultatet av logg frågan stöder bok märken när det här fönstret öppnas från Azure Sentinel. Du kan till exempel välja **allmänna**  >  **loggar** i navigerings fältet, välja händelse länkar i utrednings diagrammet eller välja ett aviserings-ID från den fullständiga informationen om en incident (för närvarande i för hands version). Du kan inte skapa bok märken när fönstret **loggar** öppnas från andra platser, till exempel direkt från Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Visa och uppdatera bok märken 

1. I Azure Portal går du till **kontroll**  >  **Threat Management**  >  **jakt**. 

2. Välj fliken **bok märken** om du vill visa listan över bok märken.

3. Använd sökrutan eller filter alternativen för att hjälpa dig att hitta ett speciellt bok märke.

4. Välj enskilda bok märken och Visa bok märkes informationen i det högra informations fönstret.

5. Gör dina ändringar efter behov, som sparas automatiskt.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Utforska bok märken i undersöknings diagrammet

> [!IMPORTANT]
> Utforska bok märken i undersöknings diagrammet och själva undersöknings diagrammet finns för närvarande i en offentlig för hands version.
> Dessa funktioner tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. I Azure Portal navigerar du till fliken **Sentinel**  >  **Threat Management**  >  **jakt**  >  **bok märken** och väljer det bok märke eller bok märken som du vill undersöka.

2. I bok märkes detaljerna ser du till att minst en entitet är mappad. För **entiteter** visas till exempel poster för **IP**, **dator** eller **konto**.

3. Klicka på **Undersök** för att Visa bok märket i undersöknings diagrammet.

Anvisningar om hur du använder undersöknings diagrammet finns i [använda undersökningen-diagrammet till djupet](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Lägga till bok märken till en ny eller befintlig incident

> [!IMPORTANT]
> Att lägga till bok märken till en ny eller befintlig incident är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. I Azure Portal navigerar du till fliken **Sentinel**  >  **Threat Management**  >  **jakt**  >  **bok märken** och väljer det bok märke eller bok märken som du vill lägga till i en incident.

2. Välj **incident åtgärder (förhands granskning)** från kommando fältet:
    
    > [!div class="mx-imgBorder"]
    > ![Lägg till bok märken i incidenten](./media/bookmarks/incident-actions.png)

3. Välj antingen **Skapa ny incident** eller **Lägg till i befintlig incident, om** det behövs. Sedan:
    
    - För en ny incident: kan du uppdatera informationen om incidenten och sedan välja **skapa**.
    - För att lägga till ett bok märke till en befintlig incident: Välj en incident och välj sedan **Lägg till**. 

Så här visar du bok märket i incidenten: gå till **kontroll**  >  **Threat Management**-  >  **incidenter** och välj incidenten med ditt bok märke. Välj **Visa fullständig information** och välj sedan fliken **bok märken** .

> [!TIP]
> Som ett alternativ till alternativet **incident åtgärder (förhands granskning)** i kommando fältet kan du använda snabb menyn (**...**) för en eller flera bok märken för att välja alternativ för att **skapa en ny incident**, **lägga till i befintlig incident** och **ta bort från incidenten**. 

## <a name="view-bookmarked-data-in-logs"></a>Visa bok märkes data i loggar

Om du vill visa frågor, resultat eller historik för bok märken väljer du bok märket på fliken **jakt**  >  **bok märken** och använder länkarna i informations fönstret: 

- **Visa käll frågan** om du vill visa käll frågan i fönstret **loggar** .

- **Visa bok märkes loggar** om du vill visa alla metadata för bok märken som innehåller vem som gjort uppdateringen, de uppdaterade värdena och tidpunkten då uppdateringen ägde rum.

Du kan också Visa rå data bok märken för alla bok märken genom att välja **bok märkes loggar** från kommando fältet på fliken **jakt**  >  **bok märken** :

> [!div class="mx-imgBorder"]
> ![Bok märkes loggar](./media/bookmarks/bookmark-logs.png)

I den här vyn visas alla dina bok märken med tillhör ande metadata. Du kan använda KQL-frågor ( [Keyword Query Language](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) ) för att filtrera ned till den senaste versionen av det specifika bok märke som du letar efter.

> [!NOTE]
> Det kan finnas en betydande fördröjning (mätt i minuter) mellan den tidpunkt då du skapar ett bok märke och när det visas på fliken **bok märken** .

## <a name="delete-a-bookmark"></a>Ta bort ett bok märke
 
1.  I Azure Portal navigerar du till fliken **Sentinel**  >  **Threat Management**  >  **jakt**  >  **bok märken** och väljer det bok märke eller bok märken som du vill ta bort. 

2. Högerklicka på dina val och välj alternativet för att ta bort bok märket eller bok märkena. Ta till exempel **bort bok märke** om du bara har valt ett bok märke och **ta bort 2 bok märken** om du har valt två bok märken.
    
Om du tar bort bok märket tas bok märket bort från listan på fliken **bok märke** . **HuntingBookmark** -tabellen för din Log Analytics-arbetsyta kommer att fortsätta att innehålla tidigare bok märkes poster, men den senaste posten kommer att ändra värdet för **SoftDelete** till true, vilket gör det enkelt att filtrera bort gamla bok märken. Om du tar bort ett bok märke tas inga entiteter bort från undersökningen som är associerade med andra bok märken eller aviseringar. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kör en jakt undersökning med hjälp av bok märken i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:


- [Söker proaktivt efter hot](hunting.md)
- [Använd antecknings böcker för att köra automatiserade jakt kampanjer](notebooks.md)