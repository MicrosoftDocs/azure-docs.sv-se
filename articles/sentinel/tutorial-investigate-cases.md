---
title: Undersök incidenter med Azure Sentinel | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure Sentinel för att skapa avancerade aviserings regler som genererar incidenter som du kan tilldela och undersöka.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 4ef08ac8d386bd8a28dce38cb53aed31d79b37a2
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566343"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Självstudie: undersöka incidenter med Azure Sentinel

> [!IMPORTANT]
> Undersöknings diagrammet är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Den här självstudien hjälper dig att undersöka incidenter med Azure Sentinel. När du har anslutit dina data källor till Azure Sentinel vill du bli meddelad när något misstänkt inträffar. För att du ska kunna göra detta kan du med Azure Sentinel skapa avancerade aviserings regler som genererar incidenter som du kan tilldela och undersöka.

Den här artikeln beskriver:
> [!div class="checklist"]
> * Undersöka incidenter
> * Använd undersöknings diagrammet
> * Reagera på hot

En incident kan innehålla flera aviseringar. Det är en agg regering av alla relevanta bevis för en speciell undersökning. En incident skapas baserat på analys regler som du skapade på sidan **analys** . Egenskaperna som rör aviseringarna, till exempel allvarlighets grad och status, anges på incident nivå. När du har informerat Azure Sentinel vet vilka typer av hot du letar efter och hur du hittar dem kan du övervaka identifierade hot genom att undersöka incidenter.

## <a name="prerequisites"></a>Förutsättningar
- Du kommer bara att kunna undersöka incidenten om du använde enhets mappnings fälten när du konfigurerade din analys regel. Undersöknings diagrammet kräver att den ursprungliga incidenten innehåller entiteter.

- Om du har en gäst användare som behöver tilldela incidenter måste användaren tilldelas [katalog läsar](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) rollen i din Azure AD-klient. Vanliga (icke-gäst) användare har den här rollen tilldelad som standard.

## <a name="how-to-investigate-incidents"></a>Så här undersöker du incidenter

1. Välj **incidenter**. På sidan **incidenter** kan du se hur många incidenter du har, hur många som är öppna, hur många som är inställda på att **pågå**och hur många som är stängda. För varje incident kan du se tiden det har inträffat och status för incidenten. Titta på allvarlighets graden för att bestämma vilka incidenter som ska hanteras först.

    ![Visa allvarlighets grad för incident](media/tutorial-investigate-cases/incident-severity.png)

1. Du kan filtrera incidenterna efter behov, till exempel efter status eller allvarlighets grad.

1. Välj en speciell incident för att påbörja en undersökning. Till höger kan du se detaljerad information om incidenten, inklusive allvarlighets grad, sammanfattning av antalet berörda enheter, de obehandlade händelser som utlöste den här incidenten och incidentens unika ID.

1. Om du vill visa mer information om aviseringar och entiteter i incidenten väljer du **Visa fullständig information** på sidan incident och granskar de relevanta flikarna som sammanfattar incident informationen. På fliken **aviseringar** granskar du själva aviseringen. Du kan se all relevant information om aviseringen – frågan som utlöste aviseringen, antalet resultat som returneras per fråga och möjligheten att köra spel böcker på aviseringarna. Om du vill öka detalj nivån ytterligare i incidenten väljer du antalet **händelser**. Detta öppnar frågan som skapade resultaten och de händelser som utlöste aviseringen i Log Analytics. På fliken **entiteter** kan du se alla entiteter som du har mappat som en del av varnings regel definitionen.

    ![Visa aviserings information](media/tutorial-investigate-cases/alert-details.png)

1. Om du aktivt undersöker en incident, är det en bra idé att ställa in incidentens status på **pågående** tills du stänger den.

1. Incidenter kan tilldelas till en speciell användare. För varje incident kan du tilldela en ägare genom att ange fältet **incident ägare** . Alla incidenter börjar som otilldelade. Du kan också lägga till kommentarer så att andra analytiker kan förstå vad du har undersökt och vilka frågor som gäller kring incidenten.

    ![Tilldela incident till användare](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Välj **Undersök** för att Visa undersöknings kartan.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Använd undersöknings diagrammet för att fördjupa dig

I undersöknings diagrammet kan analytiker ställa rätt frågor för varje undersökning. I undersöknings diagrammet får du hjälp att förstå omfattningen och identifiera rotor saken, av ett potentiellt säkerhetshot genom att korrelera relevanta data med alla berörda entiteter. Du kan gå djupare och undersöka alla entiteter som visas i diagrammet genom att markera den och välja mellan olika expansions alternativ.  
  
I undersöknings diagrammet får du:

- **Visuell kontext från rå data**: Live, Visual Graph visar entitetsrelationer som extraheras automatiskt från rå data. På så sätt kan du enkelt se anslutningar mellan olika data källor.

- **Fullständig identifiering av undersöknings omfång**: utöka din undersöknings omfattning med hjälp av inbyggda utforsknings frågor till ytan hela omfattningen av en överträdelse.

- **Inbyggda undersöknings steg**: Använd fördefinierade utforsknings alternativ för att se till att du ställer rätt frågor i ett hots ansikte.

Så här använder du undersöknings diagrammet:

1. Välj en incident och välj sedan **Undersök**. Då kommer du till undersöknings diagrammet. Grafen innehåller en illustrations karta över de entiteter som är direkt anslutna till aviseringen och varje resurs som är ansluten ytterligare.

   > [!IMPORTANT] 
   > Du kommer bara att kunna undersöka incidenten om du använde enhets mappnings fälten när du konfigurerade din analys regel. Undersöknings diagrammet kräver att den ursprungliga incidenten innehåller entiteter.

   ![Visa karta](media/tutorial-investigate-cases/map1.png)

1. Välj en entitet för att öppna fönstret **entiteter** så att du kan granska information om entiteten.

    ![Visa entiteter i kartan](media/tutorial-investigate-cases/map-entities.png)
  
1. Utöka undersökningen genom att hovra över varje entitet för att visa en lista med frågor som har utformats av våra säkerhets experter och analytiker per entitetstyp för att fördjupa undersökningen. Vi kallar dessa alternativ **utforsknings frågor**.

    ![Utforska mer information](media/tutorial-investigate-cases/exploration-cases.png)

   På en dator kan du till exempel begära relaterade aviseringar. Om du väljer en utforsknings fråga, läggs de resulterande rubrikerna tillbaka till i grafen. I det här exemplet kan du välja **relaterade aviseringar** som returnerade följande aviseringar i grafen:

    ![Visa relaterade aviseringar](media/tutorial-investigate-cases/related-alerts.png)

1. För varje utforsknings fråga kan du välja alternativet för att öppna de råa händelse resultaten och frågan som används i Log Analytics, genom att välja **händelser \> **.

1. För att förstå incidenten ger grafen en parallell tids linje.

    ![Visa tids linje i kartan](media/tutorial-investigate-cases/map-timeline.png)

1. Hovra över tids linjen för att se vilka saker i grafen som inträffade vid vilken tidpunkt som helst.

    ![Använd tids linjen i kartan för att undersöka aviseringar](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Stänga en incident

När du har löst en viss incident (till exempel när undersökningen har nått slutet) bör du ställa in incidentens status på **stängd**. När du gör det blir du ombedd att klassificera incidenten genom att ange orsaken till att du stänger den. Det här steget är obligatoriskt. Klicka på **Välj klassificering** och välj något av följande i list rutan:

- Sann positiv – misstänkt aktivitet
- Oskadlig positiv, misstänkt men förväntad
- Falsk positiv – felaktig aviserings logik
- Falsk positiv-felaktiga data
- Obestämd

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="{Alt-text}":::

När du har valt rätt klassificering lägger du till en beskrivande text i fältet **kommentar** . Detta är användbart i händelse av att du behöver referera till den här incidenten igen. Klicka på **Använd** när du är klar så stängs incidenten.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{Alt-text}":::

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du kommer igång med att undersöka incidenter med hjälp av Azure Sentinel. Fortsätt till självstudien för att [svara på hot med automatiserade spel böcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) för att automatisera dina svar på hot.

