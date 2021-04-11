---
title: 'Självstudie: använda spel böcker med Automation-regler i Azure Sentinel'
description: Använd den här självstudien för att hjälpa dig att använda spel böcker tillsammans med automatiserings regler i Azure Sentinel för att automatisera incident svaret och åtgärda säkerhetshot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600626"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Självstudie: använda spel böcker med Automation-regler i Azure Sentinel

Den här självstudien visar hur du använder spel böcker tillsammans med automatiserings regler för att automatisera incident svar och åtgärda säkerhetshot som identifieras av Azure Sentinel. När du har slutfört den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Skapa en Automation-regel
> * Skapa en Spelbok
> * Lägga till åtgärder i en Spelbok
> * Koppla en Spelbok till en Automation-regel eller en analys regel för att automatisera hot svar

## <a name="what-are-automation-rules-and-playbooks"></a>Vad är Automation-regler och spel böcker?

Med automatiserings regler kan du prioritering incidenter i Azure Sentinel. Du kan använda dem för att automatiskt tilldela incidenter till rätt personal, stänga störningar i incidenter eller kända falska positiva identifieringar, ändra deras allvarlighets grad och lägga till taggar. De är också mekanismen genom vilken du kan köra spel böcker som svar på incidenter.

Spel böcker är samlingar av procedurer som kan köras från Azure Sentinel som svar på en avisering eller incident. En Spelbok kan hjälpa dig att automatisera och dirigera ditt svar och kan ställas in så att det körs automatiskt när vissa aviseringar eller incidenter genereras, genom att kopplas till en analys regel eller en Automation-regel. Det kan också köras manuellt på begäran.

Spel böcker i Azure Sentinel baseras på arbets flöden som skapats i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), vilket innebär att du får alla Power, anpassningsbarhet och inbyggda mallar av Logic Apps. Varje Spelbok skapas för den aktuella prenumerationen som den tillhör, men **spel böcker** visar alla de spel böcker som är tillgängliga för alla valda prenumerationer.

> [!NOTE]
> Eftersom spel böcker använder Azure Logic Apps kan ytterligare avgifter tillkomma. Mer information finns på sidan med [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) prissättning.

Om du till exempel vill stoppa potentiellt komprometterade användare från att flytta runt nätverket och stjäla information kan du skapa ett automatiserat, mångfacetterat svar på incidenter som genereras av regler som identifierar komprometterade användare. Du börjar med att skapa en Spelbok som vidtar följande åtgärder:

1. När Spelbok anropas av en Automation-regel som skickar en incident, öppnar Spelbok en biljett i [ServiceNow](/connectors/service-now/) eller något annat IT-biljettsystem.

1. Den skickar ett meddelande till din säkerhets åtgärds kanal i [Microsoft Teams](/connectors/teams/) eller [slack](/connectors/slack/) för att se till att dina säkerhetsanalytiker är medvetna om incidenten.

1. Den skickar också all information i incidenten i ett e-postmeddelande till den erfarna nätverks administratören och säkerhets administratören. E-postmeddelandet innehåller alternativ knappar för att **blockera** och **Ignorera** användare.

1. Spelbok väntar tills ett svar tas emot från administratörerna och fortsätter sedan med nästa steg.

1. Om administratörer väljer **blockera** skickas ett kommando till Azure AD för att inaktivera användaren och en till brand väggen för att blockera IP-adressen.

1. Om administratörer väljer **Ignorera**, stänger Spelbok incidenten i Azure Sentinel och biljetten i ServiceNow.

För att utlösa Spelbok skapar du en Automation-regel som körs när dessa incidenter genereras. Den regeln gör följande:

1. Regeln ändrar incident statusen till **aktiv**.

1. Den tilldelar incidenten till analytikern med hantering av den här typen av incident.

1. Den lägger till taggen "komprometterad användare".

1. Slutligen anropar den Spelbok som du nyss skapade. ([Särskilda behörigheter krävs för det här steget](automate-responses-with-playbooks.md#incident-creation-automated-response).)

Spel böcker kan köras automatiskt som svar på incidenter genom att skapa Automation-regler som anropar spel böcker som åtgärder, som i exemplet ovan. De kan också köras automatiskt som svar på aviseringar, genom att analys regeln automatiskt ska köra en eller flera spel böcker när aviseringen genereras. 

Du kan också välja att köra en Spelbok manuellt på begäran, som ett svar på en vald avisering.

Få en mer fullständig och detaljerad introduktion till automatisering av hot svar med hjälp av [Automation-regler](automate-incident-handling-with-automation-rules.md) och [spel böcker](automate-responses-with-playbooks.md) i Azure Sentinel.

> [!IMPORTANT]
>
> - **Automation-regler** och användning av **incident utlösare** för spel böcker finns för närvarande som för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="create-a-playbook"></a>Skapa en Spelbok

Följ de här stegen för att skapa en ny Spelbok i Azure Sentinel:

### <a name="prepare-the-playbook-and-logic-app"></a>Förbereda Spelbok-och Logic-appen

1. Välj **Automation** på navigerings menyn i **Azure Sentinel** .

1. På den översta menyn väljer du **skapa** och **Lägg till ny Spelbok**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Lägg till en ny Spelbok":::

    En ny flik i webbläsaren öppnas och du tar dig till guiden **skapa en Logic Apps** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Skapa en logikapp":::

1. Ange din **prenumeration** och **resurs grupp** och ge din Spelbok ett namn under **Logic app-namn**.

1. För **region** väljer du den Azure-region där din Logic app-information ska lagras.

1. Om du vill övervaka den här Spelbok aktivitet för diagnostik markerar du kryss rutan **Aktivera Log Analytics** och anger **Log Analytics arbets ytans** namn.

1. Om du vill lägga till taggar till din Spelbok klickar du på **Nästa: taggar >** (inte kopplade till taggar som tillämpas av Automation-regler. [Lär dig mer om Taggar](../azure-resource-manager/management/tag-resources.md)). Annars klickar du på **Granska + skapa**. Bekräfta informationen du tillhandahöll och klicka på **skapa**.

1. Medan din Spelbok skapas och distribueras (det tar några minuter) kommer du till en skärm som kallas **Microsoft. EmptyWorkflow**. När meddelandet "distributionen är klar" visas klickar **du på gå till resurs.**

1. Du kommer att gå till din nya Spelbok- [Designer för Logic Apps](../logic-apps/logic-apps-overview.md), där du kan börja utforma arbets flödet. En skärm visas med en kort introduktions video och vanliga utlösare för Logic app och mallar. [Lär dig mer](../logic-apps/logic-apps-create-logic-apps-from-templates.md) om att skapa en spelbok med Logic Apps.

1. Välj den **tomma Logic app** -mallen.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galleri för Logic Apps designer-mallar":::

### <a name="choose-the-trigger"></a>Välj utlösaren

Varje Spelbok måste börja med en utlösare. Utlösaren definierar den åtgärd som ska starta Spelbok och det schema som Spelbok kommer att förväntas ta emot.

1. Leta efter Azure Sentinel i Sök fältet. Välj **Azure Sentinel** när det visas i resultatet.

1. På fliken resulterande **utlösare** visas de två utlösare som erbjuds av Azure Sentinel:
    - När ett svar på en Azure Sentinel-avisering utlöses
    - När en regel för att skapa Azure Sentinel-incidenter utlöses

   Välj den utlösare som matchar den typ av Spelbok som du skapar.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Välj en utlösare för din Spelbok":::

### <a name="add-actions"></a>Lägg till åtgärder

Nu kan du definiera vad som händer när du anropar Spelbok. Du kan lägga till åtgärder, logiska villkor, slingor eller växlar Skift läge, allt genom att välja **nytt steg**. Det här alternativet öppnar en ny ram i designern, där du kan välja ett system eller ett program för att interagera med eller ett villkor att ställa in. Ange namnet på systemet eller programmet i Sök fältet överst i ramen och välj sedan bland de tillgängliga resultaten.

I vart och ett av de här stegen visas en panel med två menyer: **dynamiskt innehåll** och **uttryck** när du klickar på ett fält. Från menyn med **dynamiskt innehåll** kan du lägga till referenser till attributen för aviseringen eller incidenten som skickades till Spelbok, inklusive värden och attribut för alla entiteter som ingår. Från menyn **uttryck** kan du välja från ett stort bibliotek med funktioner för att lägga till ytterligare logik i dina steg.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Designer för logikappar":::

Den här skärm bilden visar de åtgärder och villkor som du vill lägga till i skapa Spelbok som beskrivs i exemplet i början av det här dokumentet. Den enda skillnaden är att i Spelbok som visas här använder du **aviserings utlösaren** i stället för **incident utlösaren**. Det innebär att du anropar den här Spelbok från en analys regel direkt, inte från en Automation-regel. Båda metoderna för att anropa en Spelbok beskrivs nedan.

## <a name="automate-threat-responses"></a>Automatisera hot svar

Du har skapat din Spelbok och definierat utlösaren, angett villkoren och angett de åtgärder som ska vidtas och de utdata som ska skapas. Nu måste du bestämma de kriterier under vilka den ska köras och konfigurera den automatiserings funktion som kommer att köra den när dessa villkor uppfylls.

### <a name="respond-to-incidents"></a>Svara på incidenter

Du kan använda en Spelbok för att svara på en **incident** genom att skapa en [Automation-regel](automate-incident-handling-with-automation-rules.md) som körs när incidenten genereras, och i sin tur anropar den Spelbok.

Så här skapar du en Automation-regel:

1. Från **Automation** -bladet på navigerings menyn i Azure Sentinel väljer du **skapa** på den översta menyn och sedan **Lägg till ny regel**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Lägg till en ny regel":::

1. Panelen **Skapa ny Automation-regel** öppnas. Ange ett namn för regeln.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Skapa en Automation-regel":::

1. Om du vill att Automation-regeln ska börja gälla enbart för vissa analys regler anger du vilka som ska gälla för villkoret **om analys regel namn** .

1. Lägg till andra villkor som du vill att den här Automation-regelns aktivering ska bero på. Klicka på **Lägg till villkor** och Välj villkor i list rutan. Listan över villkor fylls i efter aviserings information och fält för enhets identifierare.

1. Välj de åtgärder som du vill att den här Automation-regeln ska vidta. Tillgängliga åtgärder är **tilldela ägare**, **ändra status**, **ändra allvarlighets grad**, **lägga till taggar** och **köra Spelbok**. Du kan lägga till så många åtgärder som du vill.

1. Om du lägger till en åtgärd för att **köra Spelbok** uppmanas du att välja i list rutan med tillgängliga spel böcker. Endast spel böcker som börjar med **incident utlösaren** kan köras från Automation-regler, så bara de visas i listan.

    > [!IMPORTANT]
    > Azure Sentinel måste beviljas explicita behörigheter för att kunna köra spel böcker från Automation-regler. Om en Spelbok visas som "nedtonad" i list rutan betyder det att Sentinel inte har behörighet till Spelbok-resurs gruppen. Klicka på länken **Hantera Spelbok behörigheter** för att tilldela behörigheter.
    > I panelen **Hantera behörigheter** som öppnas markerar du kryss rutorna för de resurs grupper som innehåller de spel böcker som du vill köra och klickar på **Använd**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Hantera behörigheter":::
    > - Du måste själv ha **ägar** behörighet till alla resurs grupper som du vill ge behörighet till Azure Sentinel, och du måste ha rollen **Logic app Contributor** i alla resurs grupper som innehåller spel böcker som du vill köra.
    > - I en distribution med flera innehavare, om Spelbok du vill köra finns i en annan klient, måste du bevilja Azure Sentinel-behörighet för att köra Spelbok i Spelbok-klienten.
    >    1. Välj **Inställningar** på navigerings menyn i Azure Sentinel i spel böcker-klienten.
    >    1. I bladet **Inställningar** väljer du fliken **Inställningar** . sedan expanderar **Spelbok Permissions** .
    >    1. Klicka på knappen **Konfigurera behörigheter** för att öppna panelen **Hantera behörigheter** som nämns ovan och fortsätt enligt beskrivningen där.

1. Ange ett förfallo datum för din Automation-regel om du vill att den ska ha en.

1. Ange ett nummer för **att avgöra** var i sekvensen av Automation regler som regeln ska köras.

1. Klicka på **Applicera**. Du är klar!

[Upptäck andra sätt](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) att skapa Automation-regler.

### <a name="respond-to-alerts"></a>Reagera på aviseringar

Du kan använda en Spelbok för att svara på en **avisering** genom att skapa en **analys regel** eller redigera en befintlig, som körs när aviseringen genereras och välja Spelbok som ett automatiserat svar i [guiden Analytics-regel](tutorial-detect-threats-custom.md).

1. Från **analys** bladet på navigerings menyn i Azure Sentinel väljer du den analys regel som du vill automatisera svaret för och klickar på **Redigera** i informations fönstret.

1. I **guiden Analytics-regel – sidan Redigera befintlig regel** väljer du fliken **automatiserat svar** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Fliken automatiserat svar":::

1. Välj din Spelbok i list rutan. Du kan välja fler än en Spelbok, men endast spel böcker som använder **aviserings utlösaren** är tillgängliga.

1. På fliken **Granska och skapa** väljer du **Spara**.

## <a name="run-a-playbook-on-demand"></a>Köra en spelbok på begäran

Du kan också köra en Spelbok på begäran.

 > [!NOTE]
 > Endast spel böcker som använder **aviserings utlösaren** kan köras på begäran.

Så här kör du en Spelbok på begäran:

1. På sidan **incidenter** väljer du en incident och klickar på **Visa fullständig information**.

2. På fliken **aviseringar** klickar du på den avisering som du vill köra Spelbok på och bläddrar sedan hela vägen till höger och klickar på **Visa spel böcker** och väljer en Spelbok som ska **köras** från listan över tillgängliga spel böcker i prenumerationen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder spel böcker och automation-regler i Azure Sentinel för att svara på hot. 
- Lär dig hur du [proaktivt söker efter hot](hunting.md) med Azure Sentinel.
