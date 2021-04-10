---
title: Automatisera hot svar med spel böcker i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar automatisering i Azure Sentinel och visar hur du använder spel böcker för att automatisera hot förebyggande åtgärder och-svar.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610099"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatisera hot svar med spel böcker i Azure Sentinel

Den här artikeln förklarar vad Azure Sentinel-spel böcker är och hur du använder dem för att implementera åtgärder för säkerhets dirigering, automatisering och svar (SOAR), vilket ger bättre resultat när du sparar tid och resurser.

## <a name="what-is-a-playbook"></a>Vad är en Spelbok?

SIEM/SOC-team är vanligt vis översvämmas med säkerhets aviseringar och incidenter regelbundet, på volymer så stora att tillgänglig personal är överväldigad. Detta resulterar i en alltför ofta i situationer där många aviseringar ignoreras och många incidenter inte har undersökts, vilket gör att organisationen blir sårbar för angrepp som går vidare.

Många, om inte de flesta, av de här aviseringarna och incidenterna följer återkommande mönster som kan åtgärdas med hjälp av en viss och definierad uppsättning reparations åtgärder.

En Spelbok är en samling av dessa reparations åtgärder som kan köras från Azure Sentinel som en rutin. En Spelbok kan hjälpa dig att automatisera och dirigera ditt hot svar. den kan köras manuellt eller ställas in så att den körs automatiskt som svar på vissa aviseringar eller incidenter, när de utlöses av en analys regel eller en Automation-regel.

Spel böcker skapas och tillämpas på prenumerations nivån, men fliken **spel böcker** (i det nya **Automation** -bladet) visar alla spel böcker som är tillgängliga för alla valda prenumerationer.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps grundläggande begrepp

Spel böcker i Azure Sentinel baseras på arbets flöden som skapats i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), en moln tjänst som hjälper dig att schemalägga, automatisera och dirigera uppgifter och arbets flöden mellan system i hela företaget. Det innebär att spel böcker kan dra nytta av alla Power-och anpassningsbarhet för Logic Apps inbyggda mallar.

> [!NOTE]
> Eftersom Azure Logic Apps är en separat resurs kan ytterligare avgifter tillkomma. Mer information finns på sidan med [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) prissättning.

Azure Logic Apps kommunicerar med andra system och tjänster med hjälp av kopplingar. Följande är en kort förklaring av anslutningar och några av deras viktiga attribut:

- **Hanterad koppling:** En uppsättning åtgärder och utlösare som kapslar runt API-anrop till en viss produkt eller tjänst. Azure Logic Apps erbjuder hundratals anslutningar för att kommunicera med både Microsoft-tjänster och tjänster som inte kommer från Microsoft.
  - [Lista över alla Logic Apps kopplingar och deras dokumentation](/connectors/connector-reference/)

- **Anpassad anslutning:** Du kanske vill kommunicera med tjänster som inte är tillgängliga som färdiga kopplingar. Anpassade anslutningar riktar sig till detta behov genom att låta dig skapa (och till och med dela) en koppling och definiera egna utlösare och åtgärder.
  - [Skapa dina egna anpassade Logic Apps-kopplingar](/connectors/custom-connectors/create-logic-apps-connector)

- **Azure Sentinel-koppling:** Om du vill skapa spel böcker som interagerar med Azure Sentinel använder du Azure Sentinel-anslutningen.
  - [Dokumentation om Azure Sentinel Connector](/connectors/azuresentinel/)

- **Utlösare:** En anslutnings komponent som startar en Spelbok. Den definierar det schema som Spelbok förväntar sig att ta emot när det utlöses. Azure Sentinel Connector har för närvarande två utlösare:
  - [Aviserings utlösare](/connectors/azuresentinel/#triggers): Spelbok tar emot aviseringen som indatamängd.
  - [Incident utlösare](/connectors/azuresentinel/#triggers): Spelbok tar emot incidenten som indatamängd, tillsammans med alla aviseringar och entiteter som ingår.

    > [!IMPORTANT]
    >
    > - **Incident utlösaren** för spel böcker är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

- **Åtgärder:** Åtgärder är alla steg som inträffar efter utlösaren. De kan ordnas sekventiellt, parallellt eller i en matris med komplexa villkor.

- **Dynamiska fält:** Temporära fält, som bestäms av utdata-schemat för utlösare och åtgärder och fylls i av deras faktiska utdata, som kan användas i de åtgärder som följer.

### <a name="permissions-required"></a>Behörigheter som krävs

 För att ge ditt SecOps-team möjlighet att använda Logic Apps för åtgärder för säkerhets dirigering, automation och respons (SOAR) – det vill säga för att skapa och köra spel böcker i Azure Sentinel, kan du tilldela Azure-roller, antingen till vissa medlemmar i din säkerhets åtgärds team eller till hela teamet. Här följer en beskrivning av de olika tillgängliga rollerna och de uppgifter som de ska tilldelas för:

#### <a name="azure-roles-for-logic-apps"></a>Azure-roller för Logic Apps

- Med **Logic app Contributor** kan du hantera Logi Kap par och köra spel böcker, men du kan inte ändra åtkomsten till dem (för att du behöver **ägar** rollen).
- Med **Logic app-operatör** kan du läsa, aktivera och inaktivera Logic Apps, men du kan inte redigera eller uppdatera dem.

#### <a name="azure-roles-for-sentinel"></a>Azure-roller för Sentinel

- Med rollen **Azure Sentinel Contributor** kan du koppla en Spelbok till en analys regel.
- Med **Azure Sentinel responder** -rollen kan du köra en Spelbok manuellt.
- **Azure Sentinel Automation-deltagare** gör det möjligt för automatiserings regler att köra spel böcker. Det används inte i något annat syfte.

#### <a name="learn-more"></a>Läs mer

- [Lär dig mer om Azure-roller i Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Lär dig mer om Azure-roller i Azure Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Steg för att skapa en Spelbok

- [Definiera automatiserings scenariot](#use-cases-for-playbooks).

- [Bygg Azure Logic-appen](tutorial-respond-threats-playbook.md).

- [Testa din Logic app](#run-a-playbook-manually-on-an-alert).

- Koppla Spelbok till en [Automation-regel](#incident-creation-automated-response) eller en [analys regel](#alert-creation-automated-response), eller [Kör manuellt vid behov](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Användnings fall för spel böcker

Azure Logic Apps-plattformen erbjuder hundratals åtgärder och utlösare, så nästan alla automatiserings scenarion kan skapas. Azure Sentinel rekommenderar att du börjar med följande SOC-scenarier:

#### <a name="enrichment"></a>Berikande

**Samla in data och koppla dem till incidenten för att fatta smartare beslut.**

Exempel:

En Azure Sentinel-incident skapades från en avisering av en analys regel som genererar IP-enheter.

Incidenten utlöser en Automation-regel som kör en Spelbok med följande steg:

- Starta när en [ny Azure Sentinel-incident skapas](/connectors/azuresentinel/#triggers). Entiteterna som representeras i incidenten lagras i incident utlösare dynamiska fält.

- Fråga en extern Threat Intelligence-Provider för varje IP-adress, till exempel [virus total](https://www.virustotal.com/), för att hämta mer data.

- Lägg till returnerade data och insikter som kommentarer till incidenten.

#### <a name="bi-directional-sync"></a>Dubbelriktad synkronisering

**Spel böcker kan användas för att synkronisera dina Azure Sentinel-incidenter med andra biljett system.**

Exempel:

Skapa en Automation-regel för att skapa incidenter och bifoga en Spelbok som öppnar en biljett i ServiceNow:

- Starta när en [ny Azure Sentinel-incident skapas](/connectors/azuresentinel/#triggers).

- Skapa en ny biljett i [ServiceNow](/connectors/service-now/).

- Ta med i biljetten incident namn, viktiga fält och en URL till Azure Sentinel-incidenten för enkel pivotering.

#### <a name="orchestration"></a>Orkestrering

**Använd SOCs chatt-plattform för att få bättre kontroll över incidenter-kön.**

Exempel:

En Azure Sentinel-incident skapades från en avisering av en analys regel som genererar användar namn och IP-adress enheter.

Incidenten utlöser en Automation-regel som kör en Spelbok med följande steg:

- Starta när en [ny Azure Sentinel-incident skapas](/connectors/azuresentinel/#triggers).

- Skicka ett meddelande till din säkerhets åtgärds kanal i [Microsoft Teams](/connectors/teams/) eller [slack](/connectors/slack/) för att se till att dina säkerhetsanalytiker är medvetna om incidenten.

- Skicka all information i aviseringen via e-post till den erfarna nätverks administratören och säkerhets administratören. E-postmeddelandet innehåller alternativ knappar för att **blockera** och **Ignorera** användare.

- Vänta tills ett svar tas emot från administratörerna och fortsätt sedan att köra.

- Om administratörer har valt **block**, skicka ett kommando till brand väggen för att blockera IP-adressen i aviseringen och en annan till Azure AD för att inaktivera användaren.

#### <a name="response"></a>Svarsåtgärder

**Svara omedelbart på hot, med minimala mänskliga beroenden.**

Två exempel:

**Exempel 1:** Svara på en analys regel som indikerar en komprometterad användare, som identifieras av [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Starta när en [ny Azure Sentinel-incident skapas](/connectors/azuresentinel/#triggers).

   - För varje användar enhet i incidenten som misstänks vara komprometterad:

     - Skicka ett team meddelande till användaren och begär bekräftelse på att användaren vidtog den misstänkta åtgärden.

     - Kontrol lera med Azure AD Identity Protection för att [Bekräfta att användarens status är komprometterad](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection förser användaren som **riskfylld** och tillämpar principen som redan har kon figurer ATS, till exempel för att kräva att användaren använder MFA vid nästa inloggning.

        > [!NOTE]
        > Spelbok initierar inte någon tvångs åtgärd för användaren eller så initierar den inte någon konfiguration av tvångs principen. Det visar bara Azure AD Identity Protection att tillämpa redan definierade principer efter behov. Eventuell tvång är helt beroende av lämpliga principer som definieras i Azure AD Identity Protection.

**Exempel 2:** Svara på en analys regel som indikerar en komprometterad dator, som identifieras av [Microsoft Defender för slut punkt](/windows/security/threat-protection/):

   - Starta när en [ny Azure Sentinel-incident skapas](/connectors/azuresentinel/#triggers).

   - Använd åtgärden **entiteter-Hämta värdar** i Azure Sentinel för att parsa de misstänkta datorer som ingår i incident enheterna.

   - Utfärda ett kommando till Microsoft Defender för slut punkt för att [isolera datorerna](/connectors/wdatp/#actions---isolate-machine) i aviseringen.

## <a name="how-to-run-a-playbook"></a>Så här kör du en Spelbok

Spel böcker kan köras antingen **manuellt** eller **automatiskt**.

Att köra dem manuellt innebär att när du får en avisering kan du välja att köra en Spelbok på begäran som ett svar på den valda aviseringen. Den här funktionen stöds för närvarande endast för aviseringar, inte för incidenter.

Att köra dem automatiskt innebär att ställa in dem som ett automatiserat svar i en analys regel (för aviseringar) eller som en åtgärd i en Automation-regel (för incidenter). [Läs mer om automatiserings regler](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Ange ett automatiskt svar

Säkerhets åtgärds team kan minska sin arbets belastning avsevärt genom att helt automatisera de rutinmässiga svaren på återkommande typer av incidenter och aviseringar, så att du kan koncentrera dig på unika incidenter och aviseringar, analysera mönster, skydda och få mer information.

Genom att ställa in automatiserat svar innebär det att varje gång en analys regel utlöses, förutom att skapa en avisering, kommer regeln att köra en Spelbok som kommer att ta emot den avisering som skapas av regeln.

Om aviseringen skapar en incident utlöser incidenten en Automation-regel som kan i sin tur köra en Spelbok som kommer att få som en inmatad incident som skapats av aviseringen.

#### <a name="alert-creation-automated-response"></a>Automatisk svars skapande av avisering

För spel böcker som utlöses av aviserings skapande och mottagning av aviseringar som indata (deras första steg är "när en Azure Sentinel-avisering utlöses"), koppla Spelbok till en analys regel:

1. Redigera [analys regeln](tutorial-detect-threats-custom.md) som genererar den avisering som du vill definiera ett automatiserat svar för.

1. Under **aviserings automatisering** på fliken **automatiserat svar** väljer du den Spelbok eller spel böcker som den här analys regeln ska utlösa när en avisering skapas.

#### <a name="incident-creation-automated-response"></a>Automatiserat svar för skapande av incident

För spel böcker som utlöses av incident skapande och ta emot incidenter som indata (deras första steg är "när en Azure Sentinel-incident utlöses") skapar du en Automation-regel och definierar en **Kör Spelbok** -åtgärd i den. Detta kan göras på två sätt:

- Redigera analys regeln som genererar den incident som du vill definiera ett automatiserat svar för. Under **incident automatisering** på fliken **automatiserat svar** skapar du en Automation-regel. Detta skapar bara ett automatiskt svar för den här analys regeln.

- På fliken **Automation-regler** på **Automation** -bladet skapar du en ny Automation-regel och anger lämpliga villkor och önskade åtgärder. Den här Automation-regeln används för alla analys regler som uppfyller de angivna villkoren.

    > [!NOTE]
    > För att köra en Spelbok från en Automation-regel använder Azure Sentinel ett tjänst konto som är specifikt auktoriserat att göra detta. Användningen av det här kontot (till skillnad från ditt användar konto) ökar tjänstens säkerhets nivå och aktiverar automations reglernas API för att stödja CI/CD-användningsfall.
    >
    > Det här kontot måste beviljas explicita behörigheter till den resurs grupp där Spelbok finns. I det här läget kommer alla automatiserings regler att kunna köra alla Spelbok i den resurs gruppen.
    >
    > När du lägger till åtgärden **Kör Spelbok** i en Automation-regel visas en nedrullningsbar listruta med spel böcker. Spel böcker som Azure Sentinel inte har behörighet till visas som otillgänglig ("nedtonat"). Du kan bevilja behörighet till Azure Sentinel på platsen genom att välja länken **Hantera Spelbok-behörigheter** .
    >
    > I ett[Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)-scenario (Multi-Tenant) måste du definiera behörigheter för den klient där Spelbok finns, även om den Automation-regel som anropar Spelbok finns i en annan klient. Om du vill göra det måste du ha **ägar** behörighet för Spelbok-resurs gruppen.

Se de [fullständiga anvisningarna för att skapa Automation-regler](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Köra en Spelbok manuellt på en avisering

Manuell utlösare är tillgänglig från Azure Sentinel-portalen på följande blad:

- I vyn **incidenter** väljer du en speciell incident, öppnar fliken **aviseringar** och väljer en avisering.

- I **undersökningen** väljer du en speciell avisering.

1. Klicka på **Visa spel böcker** för den valda aviseringen. Du får en lista över alla spel böcker som börjar med en **när en Azure Sentinel-avisering utlöses** och du har åtkomst till.

1. Starta genom att klicka på **Kör** på raden för en specifika Spelbok.

1. Välj fliken **körningar** om du vill visa en lista över alla tider då alla Spelbok har körts på den här aviseringen. Det kan ta några sekunder innan en helt avslutad körning visas i den här listan.

1. Om du klickar på en speciell körning öppnas den fullständiga körnings loggen i Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Köra en Spelbok manuellt på en incident

Stöds inte ännu. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Hantera din spel böcker

På fliken **spel böcker** visas en lista över alla spel böcker som du har åtkomst till, filtrerat efter de prenumerationer som för närvarande visas i Azure. Prenumerations filtret är tillgängligt från menyn **katalog + prenumeration** i det globala sidhuvudet.

Om du klickar på ett Spelbok namn dirigeras du till Spelbok-huvud sidan i Logic Apps. Kolumnen **status** anger om den är aktive rad eller inaktive rad.

**Utlösnings typ** representerar den Logic Apps utlösare som startar den här Spelbok.

| Utlösnings typ | Anger komponent typer i Spelbok |
|-|-|
| **Azure Sentinel-incident/-avisering** | Spelbok startas med en av kontroll utlösare (avisering, incident) |
| **Använda Azure Sentinel-åtgärd** | Spelbok startas med en icke-Sentinel-utlösare men använder en Azure Sentinel-åtgärd |
| **Övrigt** | Spelbok omfattar inte några Sentinel-komponenter |
| **Inte initierad** | Spelbok har skapats, men innehåller inga komponenter (utlösare eller åtgärder). |
|

På sidan spelboks Logic app kan du se mer information om Spelbok, inklusive en logg över alla gånger den har körts och resultatet (lyckades eller misslyckades, och annan information). Du kan också ange Logic Apps designer och redigera Spelbok direkt, om du har rätt behörighet.

### <a name="api-connections"></a>API-anslutningar

API-anslutningar används för att ansluta Logic Apps till andra tjänster. Varje gång en ny autentisering görs till en Logic Apps anslutning skapas en ny resurs av typen **API-anslutning** och innehåller den information som anges när du konfigurerar åtkomst till tjänsten.

Om du vill se alla API-anslutningar anger du *API-anslutningar* i sökrutan i Azure Portal. Observera intresse kolumnerna:

- Visnings namn – det "användarvänliga" namnet som du ger anslutningen varje gång du skapar en.
- Status-anger anslutnings status: fel, ansluten.
- Resurs grupp – API-anslutningar skapas i resurs gruppen för Spelbok-resursen (Logic Apps).

Ett annat sätt att Visa API-anslutningar är att gå till bladet **alla resurser** och filtrera det efter typ- *API-anslutning*. På så sätt kan du välja, tagga och ta bort flera anslutningar samtidigt.

För att ändra auktoriseringen av en befintlig anslutning anger du anslutnings resursen och väljer **Redigera API-anslutning**.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: använda spel böcker för att automatisera hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md)
