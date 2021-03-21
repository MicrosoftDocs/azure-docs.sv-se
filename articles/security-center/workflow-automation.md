---
title: Automatisering av arbets flöde i Azure Security Center | Microsoft Docs
description: Lär dig hur du skapar och automatiserar arbets flöden i Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 6268ff6cfb3d3e856edcd8f84af930d52f4cf9d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096181"
---
# <a name="automate-responses-to-security-center-triggers"></a>Automatisera svar på Security Center utlösare

Varje säkerhets program innehåller flera arbets flöden för incident svar. De här processerna kan omfatta att meddela relevanta intressenter, starta en ändrings hanterings process och tillämpa vissa åtgärder för reparation. Säkerhets experter rekommenderar att du automatiserar så många steg som beskrivs i de här procedurerna. Automation minskar kostnaderna. Det kan också förbättra säkerheten genom att se till att process stegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

I den här artikeln beskrivs funktionen för arbets flödes automatisering i Azure Security Center. Den här funktionen kan utlösa Logic Apps om säkerhets aviseringar, rekommendationer och ändringar i regelefterlevnad. Till exempel kanske du vill Security Center att e-posta en speciell användare när en avisering inträffar. Du lär dig också hur du skapar Logic Apps att använda [Azure Logic Apps](../logic-apps/logic-apps-overview.md).


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|**Rollen säkerhets administratör** eller **ägare** av resurs gruppen<br>Måste också ha Skriv behörighet för mål resursen<br><br>Om du vill arbeta med Azure Logic Apps arbets flöden måste du också ha följande Logic Apps roller/behörigheter:<br> - [Logic app operatörs](../role-based-access-control/built-in-roles.md#logic-app-operator) behörigheter krävs eller Läs-/utlösare för Logic app (den här rollen kan inte skapa eller redigera Logi Kap par). *Kör* bara befintliga)<br> - [Logic app Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor) -behörigheter krävs för att skapa och ändra Logic app<br>Om du vill använda Logic app-kopplingar kan du behöva ytterligare autentiseringsuppgifter för att logga in på deras respektive tjänster (till exempel dina Outlook/team/slack-instanser)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Skapa en Logic-app och definiera när den ska köras automatiskt 

1. Från Security Centers sid panelen väljer du **arbets flödes automatisering**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Lista över automatiseringar av arbets flöden":::

    Från den här sidan kan du skapa nya Automation-regler, samt aktivera, inaktivera eller ta bort befintliga.

1. Om du vill definiera ett nytt arbets flöde klickar du på **Lägg till arbets flödes automatisering**. 

    Ett fönster visas med alternativen för din nya automatisering. Här kan du ange:
    1. Ett namn och en beskrivning av Automation.
    1. Utlösare som kommer att initiera det här automatiska arbets flödet. Du kanske exempelvis vill att din Logic app ska köras när en säkerhets avisering som innehåller "SQL" genereras.

        > [!NOTE]
        > Om utlösaren är en rekommendation som har "under rekommendationer", till exempel **avgöranden om riskbedömning av SQL-databaser, bör** Logic app inte utlösas för varje ny säkerhets sökning. endast när statusen för den överordnade rekommendationen ändras.

    1. Den Logic-app som ska köras när utlösarens villkor uppfylls. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Fönstret Lägg till arbets flödes automatisering":::

1. I avsnittet åtgärder klickar du på **skapa en ny** för att starta processen för att skapa logiska appar.

    Du kommer att Azure Logic Apps.

    [![Skapa en ny Logic app](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Ange ett namn, en resurs grupp och en plats och klicka på **skapa**.

1. I den nya Logic-appen kan du välja mellan inbyggda och fördefinierade mallar i kategorin säkerhet. Eller så kan du definiera ett anpassat flöde av händelser som inträffar när den här processen utlöses.

    > [!TIP]
    > Ibland ingår parametrar i en-koppling i en Logic app som en del av en sträng och inte i ett eget fält. Ett exempel på hur du extraherar parametrar finns i steg #14 du [arbetar med Logic app-parametrar när du skapar Azure Security Center arbets flödes automatiseringar](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121).

    Logic App Designer stöder dessa Security Center-utlösare:

    - **När en Azure Security Center rekommendation skapas eller utlöses** – om din Logic-app är beroende av en rekommendation som inaktive ras eller ersätts, slutar automatiseringen att fungera och du måste uppdatera utlösaren. Information om hur du spårar ändringar i rekommendationer finns i [Azure Security Center viktig information](release-notes.md).

    - **När en Azure Security Center-avisering skapas eller utlöses** , kan du anpassa utlösaren så att den bara relaterar till aviseringar med de allvarlighets grader som intresserar dig.
    
    - **När en Security Center reglerings bedömning av efterlevnad skapas eller utlöses** , utlöses automatiseringar baserat på uppdateringar av regelefterlevnad.

    > [!NOTE]
    > Om du använder den äldre utlösaren "när ett svar på en Azure Security Center avisering utlöses", kommer dina Logi Kap par inte att startas av funktionen för arbets flödes automatisering. Använd i stället någon av de utlösare som anges ovan. 

    [![Exempel på Logic-appen](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. När du har definierat din Logic app går du tillbaka till fönstret definition för arbets flödes automatisering ("Lägg till arbets flödes automatisering"). Klicka på **Uppdatera** för att se till att den nya Logic-appen kan väljas.

    ![Uppdatera](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Välj din Logic app och spara Automation. Observera att List rutan Logic app endast visar Logic Apps med stöd för Security Center kopplingar som nämns ovan.


## <a name="manually-trigger-a-logic-app"></a>Aktivera en Logic-app manuellt

Du kan också köra Logic Apps manuellt vid visning av säkerhets aviseringar eller rekommendationer.

Om du vill köra en Logic-app manuellt öppnar du en avisering eller en rekommendation och klickar på **Utlös Logic app**:

[![Aktivera en Logic-app manuellt](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>Konfigurera automatisering av arbets flöde i skala med hjälp av de angivna principerna

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Om du vill distribuera dina automatiserings konfigurationer i organisationen använder du de angivna Azure Policy "DeployIfNotExist"-principer som beskrivs nedan för att skapa och konfigurera automatiserings procedurer för arbets flöden.

Kom igång med [automatiserings mallar för arbets flöden](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Så här implementerar du dessa principer:

1. I tabellen nedan väljer du den princip som du vill använda:

    |Mål  |Policy  |Princip-ID  |
    |---------|---------|---------|
    |Arbets flödes automatisering för säkerhets aviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |Arbets flödes automatisering för säkerhets rekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |Automatisering av arbets flöde för regelefterlevnad-ändringar|[Distribuera arbets flödes automatisering för Azure Security Center regelefterlevnad](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > Du kan också hitta dessa genom att söka Azure Policy:
    > 1. Öppna Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Komma åt Azure Policy":::
    > 2. Från Azure Policy-menyn väljer du **definitioner** och söker efter dem efter namn. 

1. På sidan relevanta Azure Policy väljer du **tilldela**.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Tilldela Azure Policy":::

1. Öppna varje flik och ange önskade parametrar:
    1. På fliken **grundläggande** anger du omfånget för principen. Om du vill använda centraliserad hantering tilldelar du principen till hanterings gruppen som innehåller de prenumerationer som ska använda konfigurationen för arbets flödes automatisering. 
    1. Ange resurs grupp och data typs information på fliken **parametrar** . 
        > [!TIP]
        > Varje parameter har en knapp beskrivning som förklarar de tillgängliga alternativen.
        >
        > Azure Policy fliken parametrar (1) ger till gång till liknande konfigurations alternativ som Security Center s sida för arbets flödes automatisering (2).
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Jämföra parametrarna i arbets flödes automatisering med Azure Policy" lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. Om du vill tillämpa den här tilldelningen på befintliga prenumerationer öppnar du fliken **reparation** och väljer alternativet för att skapa en reparations uppgift.

1. Granska sidan Sammanfattning och välj **skapa**.


## <a name="data-types-schemas"></a>Data typs scheman

Om du vill visa obehandlade händelse scheman för de säkerhets aviseringar eller rekommendationer som skickas till Logic App-instansen går du till [data typerna för arbets flödets Automation-scheman](https://aka.ms/ASCAutomationSchemas). Detta kan vara användbart i de fall där du inte använder Security Center inbyggda Logic app-kopplingar som nämns ovan, men använder i stället Logic Apps generiska HTTP-koppling – du kan använda Event JSON-schemat för att manuellt parsa det när du ser anpassa.


## <a name="faq-for-workflow-automation"></a>Vanliga frågor och svar om arbets flödes automatisering

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Stöder arbets flödes automatisering alla scenarier för affärs kontinuitet eller haveri beredskap (BCDR)?

När du förbereder din miljö för BCDR-scenarier där mål resursen drabbas av ett avbrott eller en annan katastrof, är det organisationens ansvar att förhindra data förlust genom att skapa säkerhets kopior enligt rikt linjerna från Azure Event Hubs, Log Analytics arbets yta och Logic app.

För varje aktiv automatisering rekommenderar vi att du skapar en identisk (inaktive rad) automatisering och lagrar den på en annan plats. När det uppstår ett avbrott kan du aktivera de här säkerhets kopierings automatiseringarna och upprätthålla normala åtgärder.

Lär dig mer om [verksamhets kontinuitet och haveri beredskap för Azure Logic Apps](../logic-apps/business-continuity-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att skapa Logic Apps, automatisera körningen i Security Center och köra dem manuellt.

För relaterat material, se: 

- [Microsoft Learn-modulen för att automatisera ett säkerhets svar med hjälp av arbets flödes automatisering](/learn/modules/resolve-threats-with-azure-security-center/)
- [Säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md)
- [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
- [Om Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Anslutningsprogram för Azure Logic Apps](../connectors/apis-list.md)
- [Scheman för datatyper för arbetsflödesautomation](https://aka.ms/ASCAutomationSchemas)