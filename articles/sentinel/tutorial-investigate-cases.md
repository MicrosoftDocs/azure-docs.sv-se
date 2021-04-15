---
title: Undersöka incidenter med Azure Sentinel| Microsoft Docs
description: I den här självstudien lär du dig att använda Azure Sentinel för att skapa avancerade aviseringsregler som genererar incidenter som du kan tilldela och undersöka.
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
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375852"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Självstudie: Undersöka incidenter med Azure Sentinel

> [!IMPORTANT]
> Undersökningsdiagrammet är nu **allmänt tillgängligt.** 

Den här självstudien hjälper dig att undersöka incidenter med Azure Sentinel. När du har anslutit dina datakällor till Azure Sentinel vill du få ett meddelande när något misstänkt inträffar. Om du vill göra detta Azure Sentinel du skapa avancerade aviseringsregler som genererar incidenter som du kan tilldela och undersöka.

I den här artikeln beskrivs:
> [!div class="checklist"]
> * Undersöka incidenter
> * Använda undersökningsdiagrammet
> * Reagera på hot

En incident kan innehålla flera aviseringar. Det är en sammanställning av alla relevanta bevis för en specifik undersökning. En incident skapas baserat på analysregler som du skapade på **analytics-sidan.** Egenskaperna som är relaterade till aviseringarna, till exempel allvarlighetsgrad och status, anges på incidentnivå. När du Azure Sentinel vet vilka typer av hot du letar efter och hur du hittar dem kan du övervaka identifierade hot genom att undersöka incidenter.

## <a name="prerequisites"></a>Förutsättningar
- Du kommer bara att kunna undersöka incidenten om du använde entitetsmappningsfälten när du konfigurerade din analysregel. Undersökningsdiagrammet förutsätter att den ursprungliga incidenten har entiteter.

- Om du har en gästanvändare som behöver tilldela incidenter måste användaren tilldelas rollen [Katalogläsare](../active-directory/roles/permissions-reference.md#directory-readers) i din Azure AD-klientorganisation. Vanliga (icke-gäst)-användare har den här rollen tilldelad som standard.

## <a name="how-to-investigate-incidents"></a>Så här undersöker du incidenter

1. Välj **Incidenter.** På **sidan** Incidenter kan du se hur många incidenter du har, hur många som är öppna, hur många du har angett som **Pågår** och hur många som är stängda. För varje incident kan du se när den inträffade och status för incidenten. Titta på allvarlighetsgraden för att bestämma vilka incidenter som ska hanteras först.

    ![Visa allvarlighetsgrad för incidenter](media/tutorial-investigate-cases/incident-severity.png)

1. Du kan filtrera incidenterna efter behov, till exempel efter status eller allvarlighetsgrad.

1. Välj en specifik incident för att påbörja en undersökning. Till höger kan du se detaljerad information om incidenten, inklusive dess allvarlighetsgrad, en sammanfattning av antalet berörda entiteter, de råhändelser som utlöste incidenten och incidentens unika ID.

1. Om du vill visa mer information om  aviseringarna och entiteterna i incidenten väljer du Visa fullständig information på incidentsidan och granskar relevanta flikar som sammanfattar incidentinformationen. 

    ![Visa aviseringsinformation](media/tutorial-investigate-cases/incident-timeline.png)

    Exempel:

    - På fliken **Tidslinje** granskar du tidslinjen för aviseringar och bokmärken i incidenten, som kan hjälpa dig att rekonstruera tidslinjen för angriparens aktivitet.
    - På fliken **Aviseringar** granskar du själva aviseringen. Du kan se all relevant information om aviseringen – frågan som utlöste aviseringen, antalet resultat som returneras per fråga och möjligheten att köra spelböcker på aviseringarna. Om du vill öka detaljgranskningen ytterligare i incidenten väljer du antalet **händelser.** Då öppnas frågan som genererade resultaten och de händelser som utlöste aviseringen i Log Analytics. 
    - På fliken **Entiteter** kan du se alla entiteter som du mappade som en del av definitionen av aviseringsregeln.

1. Om du aktivt undersöker en incident är det en bra idé att ange incidentens status **till Pågår** tills du stänger den.

1. Incidenter kan tilldelas till en specifik användare. För varje incident kan du tilldela en ägare genom att ange **fältet Incidentägare.** Alla incidenter startar som otilldelade. Du kan också lägga till kommentarer så att andra analytiker kan förstå vad du har undersökt och vilka problem du har kring incidenten.

    ![Tilldela incident till användare](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Välj **Undersök** för att visa undersökningskartan.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Använd undersökningsdiagrammet för att göra en djupdykning

Undersökningsdiagrammet gör det möjligt för analytiker att ställa rätt frågor för varje undersökning. Undersökningsdiagrammet hjälper dig att förstå omfattningen och identifiera rotorsaken till ett potentiellt säkerhetshot genom att korrelera relevanta data med alla berörda entiteter. Du kan fördjupa dig och undersöka en entitet som visas i diagrammet genom att välja den och välja mellan olika expansionsalternativ.  
  
Undersökningsdiagrammet ger dig:

- **Visuell kontext från rådata:** Det visuella livediagrammet visar entitetsrelationer som extraheras automatiskt från rådata. På så sätt kan du enkelt se anslutningar mellan olika datakällor.

- **Identifiering av fullständigt undersökningsomfång:** Utöka undersökningsomfånget med hjälp av inbyggda utforskningsfrågor för att visa hela omfattningen av ett intrång.

- **Inbyggda undersökningssteg:** Använd fördefinierade utforskningsalternativ för att se till att du ställer rätt frågor vid hot.

Så här använder du undersökningsdiagrammet:

1. Välj en incident och välj sedan **Undersök.** Detta tar dig till undersökningsdiagrammet. Diagrammet innehåller en illustrerande karta över de entiteter som är direkt anslutna till aviseringen och varje resurs som är ansluten ytterligare.

   > [!IMPORTANT] 
   > - Du kommer bara att kunna undersöka incidenten om du använde entitetsmappningsfälten när du konfigurerade din analysregel. Undersökningsdiagrammet förutsätter att den ursprungliga incidenten har entiteter.
   >
   > - Azure Sentinel stöder för närvarande undersökning av **incidenter som är upp till 30 dagar gamla.**

   ![Visa karta](media/tutorial-investigate-cases/map1.png)

1. Välj en entitet för att öppna **fönstret Entiteter** så att du kan granska informationen om entiteten.

    ![Visa entiteter på kartan](media/tutorial-investigate-cases/map-entities.png)
  
1. Utöka din undersökning genom att hovra över varje entitet för att visa en lista över frågor som har utformats av våra säkerhetsexperter och analytiker per entitetstyp för att fördjupa undersökningen. Vi kallar dessa alternativ **för utforskningsfrågor.**

    ![Utforska mer information](media/tutorial-investigate-cases/exploration-cases.png)

   På en dator kan du till exempel begära relaterade aviseringar. Om du väljer en utforskningsfråga läggs de resulterande betitlarna tillbaka till diagrammet. I det här exemplet **returnerades följande aviseringar** i diagrammet när du valde Relaterade aviseringar:

    ![Visa relaterade aviseringar](media/tutorial-investigate-cases/related-alerts.png)

1. För varje utforskningsfråga kan du välja alternativet för att öppna råhändelseresultat och frågan som används i Log Analytics genom att välja **Händelser. \>**

1. För att förstå incidenten ger diagrammet dig en parallell tidslinje.

    ![Visa tidslinje på karta](media/tutorial-investigate-cases/map-timeline.png)

1. Hovra över tidslinjen för att se vilka saker i diagrammet som inträffade vid vilken tidpunkt.

    ![Använda tidslinjen på kartan för att undersöka aviseringar](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Stänga en incident

När du har löst en viss incident (till exempel när undersökningen har nått sin slutsats) bör du ange incidentens status till **Stängd.** När du gör det uppmanas du att klassificera incidenten genom att ange orsaken till att du stänger den. Det här steget är obligatoriskt. Klicka **på Välj** klassificering och välj något av följande i listrutan:

- Sann positiv – misstänkt aktivitet
- Godartad positiv – misstänkt men förväntad
- Falsk positiv – felaktig aviseringslogik
- Falsk positiv – felaktiga data
- Obestämd

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Skärmbild som visar de klassificeringar som är tillgängliga i listan Välj klassificering.":::

När du har valt lämplig klassificering lägger du till beskrivande text i **fältet** Kommentar. Detta är användbart om du behöver referera tillbaka till den här incidenten. Klicka **på** Tillämpa när du är klar så stängs incidenten.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du kommer igång med att undersöka incidenter med hjälp av Azure Sentinel. Fortsätt till självstudien [för att svara på hot med hjälp av automatiserade spelböcker.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Svara på hot för](tutorial-respond-threats-playbook.md) att automatisera dina svar på hot.

