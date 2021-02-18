---
title: Kontinuerlig export kan skicka Azure Security Centers aviseringar och rekommendationer till Log Analytics arbets ytor eller Azure Event Hubs
description: Lär dig hur du konfigurerar kontinuerlig export av säkerhets aviseringar och rekommendationer till Log Analytics arbets ytor eller Azure Event Hubs
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/24/2020
ms.author: memildin
ms.openlocfilehash: 226ca943554ca24f3332f24f5a9baf571b432917
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590626"
---
# <a name="continuously-export-security-center-data"></a>Exportera Security Center data kontinuerligt

Azure Security Center genererar detaljerade säkerhets aviseringar och rekommendationer. Du kan visa dem i portalen eller med programmerings verktyg. Du kan också behöva exportera viss eller all den här informationen för att spåra med andra övervaknings verktyg i din miljö. 

Med **kontinuerlig export** kan du helt anpassa *vad* som ska exporteras och *var* det ska gå. Du kan till exempel konfigurera det så att:

- Alla aviseringar med hög allvarlighets grad skickas till en Azure Event Hub
- Alla aviseringar om medelhög eller högre allvarlighets grad från sårbarhets bedömning av dina SQL-servrar skickas till en speciell Log Analytics-arbetsyta
- Vissa rekommendationer levereras till en Event Hub-eller Log Analytics-arbetsyta när de genereras 
- De säkra poängen för en prenumeration skickas till en Log Analytics arbets yta när poängen för en kontroll ändras med 0,01 eller mer 

Även om funktionen kallas *kontinuerlig*, finns det också ett alternativ för att exportera vecko Visa ögonblicks bilder av säkra poäng-eller reglerande data.

Den här artikeln beskriver hur du konfigurerar kontinuerlig export till Log Analytics arbets ytor eller Azure Event Hubs.

> [!NOTE]
> Om du behöver integrera Security Center med en SIEM, se [Stream-aviseringar till en Siem-, Soar-eller IT-Tjänstehanterings lösning](export-to-siem.md).

> [!TIP]
> Security Center erbjuder också alternativet att utföra en eng ång slö, manuell export till CSV. Läs mer i [en manuell export av aviseringar och rekommendationer](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|Kostnadsfri|
|Nödvändiga roller och behörigheter:|<ul><li>**Säkerhets administratör** eller **ägare** av resurs gruppen</li><li>Skriv behörigheter för mål resursen</li><li>Om du använder Azure Policy "DeployIfNotExist"-principer som beskrivs nedan måste du också ha behörighet för att tilldela principer</li></ul>|
|Moln|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) US Gov, annan gov<br>![Yes](./media/icons/yes-icon.png) Kina, gov (till Event Hub)|
|||


## <a name="what-data-types-can-be-exported"></a>Vilka data typer kan exporteras?

Vid kontinuerlig export kan följande data typer exporteras när de ändras:

- Säkerhetsaviseringar
- Säkerhetsrekommendationer 
- Säkerhets resultat som kan ses som "sub"-rekommendationer som resultat från genomsökningar av sårbarhets bedömning eller vissa system uppdateringar. Du kan välja att inkludera dem med deras "överordnade" rekommendationer, till exempel "system uppdateringar bör installeras på dina datorer".
- Säkra poäng (per prenumeration eller per kontroll)
- Information om regelefterlevnad

> [!NOTE]
> Export av säker Poäng och regelefterlevnad är en förhands gransknings funktion och är inte tillgänglig i offentliga moln. 

## <a name="set-up-a-continuous-export"></a>Konfigurera en löpande export 

Du kan konfigurera kontinuerlig export från Security Center sidor i Azure Portal, via Security Center REST API eller i skala med hjälp av de angivna Azure Policy mallarna. Välj lämplig flik nedan för information om var och en.

### <a name="use-the-azure-portal"></a>[**Använda Azure-portalen**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>Konfigurera kontinuerlig export från Security Center sidor i Azure Portal

Stegen nedan är nödvändiga om du konfigurerar en kontinuerlig export till Log Analytics arbets yta eller Azure Event Hubs.

1. Välj **pris & inställningar** från Security Center marginal List.
1. Välj den prenumeration som du vill konfigurera data exporten för.
1. Från List rutan på sidan Inställningar för den prenumerationen väljer du **löpande export**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Export alternativ i Azure Security Center":::

    Här ser du export alternativen. Det finns en flik för varje tillgängligt export mål. 

1. Välj den datatyp som du vill exportera och välj bland filtren för varje typ (till exempel endast exportera aviseringar med hög allvarlighets grad).
1. Välj lämplig export frekvens:
    - **Strömning** – utvärderingar skickas i real tid när en resurs hälso tillstånd uppdateras (om inga uppdateringar inträffar skickas inga data).
    - **Ögonblicks bilder** – en ögonblicks bild av det aktuella läget för alla utvärderingar av efterlevnad kommer att skickas varje vecka (detta är en förhands gransknings funktion för varje veckas ögonblicks bilder av säkra poäng och information om regelefterlevnad).

1. Om ditt val till exempel innehåller någon av dessa rekommendationer, kan du inkludera resultaten av sårbarhets utvärderingen tillsammans med dem:
    - Avgöranden för sårbarhets bedömning på SQL-databaser bör åtgärdas
    - Utvärderings resultat av säkerhets risker på dina SQL-servrar på datorer bör åtgärdas (för hands version)
    - Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas (drivs av Qualys)
    - Säkerhets risker på dina virtuella datorer bör åtgärdas
    - Systemuppdateringar ska ha installerats på dina datorer

    Om du vill inkludera undersöknings resultaten med dessa rekommendationer aktiverar du alternativet **Inkludera säkerhets resultat** .

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Ta med säkerhets brister växla i kontinuerlig export konfiguration" :::

1. I området "Exportera mål" väljer du var du vill spara data. Data kan sparas i ett mål för en annan prenumeration (till exempel på en central Event Hub-instans eller en central Log Analytics-arbetsyta).
1. Välj **Spara**.

### <a name="use-the-rest-api"></a>[**Använda REST API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Konfigurera kontinuerlig export med REST API

Kontinuerlig export kan konfigureras och hanteras via Azure Security Center [automations API](/rest/api/securitycenter/automations). Använd det här API: et för att skapa eller uppdatera regler för export till någon av följande möjliga destinationer:

- Azure Event Hub
- Log Analytics-arbetsyta
- Azure Logic Apps 

API: et tillhandahåller ytterligare funktioner som inte är tillgängliga från Azure Portal, till exempel:

* **Större volym** – API: et gör att du kan skapa flera export konfigurationer på en enda prenumeration. Sidan för **kontinuerlig export** i Security Centers Portal gränssnitt stöder bara en export konfiguration per prenumeration.

* **Ytterligare funktioner** – API: et erbjuder ytterligare parametrar som inte visas i användar gränssnittet. Du kan till exempel lägga till taggar till din Automation-resurs och definiera din export baserat på en bredare uppsättning aviserings-och rekommendations egenskaper än de som erbjuds på sidan för **kontinuerlig export** i Security Center användarens Portal gränssnitt.

* **Mer fokuserat omfång** – API: et ger en mer detaljerad nivå för omfattningen av dina export konfigurationer. När du definierar en export med API: et kan du göra det på resurs grupps nivå. Om du använder sidan för **kontinuerlig export** i Security Center användarens Portal gränssnitt måste du definiera den på prenumerations nivå.

    > [!TIP]
    > Om du har konfigurerat flera export konfigurationer med hjälp av API: et, eller om du har använt endast API-parametrar, kommer dessa extra funktioner inte att visas i Security Center användar gränssnittet. I stället är det en banderoll som informerar dig om att andra konfigurationer finns.

Läs mer om automations-API: et i [REST API-dokumentationen](/rest/api/securitycenter/automations).





### <a name="deploy-at-scale-with-azure-policy"></a>[**Distribuera i skala med Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Konfigurera kontinuerlig export i skala med hjälp av de angivna principerna

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Om du vill distribuera dina kontinuerliga export konfigurationer i organisationen använder du de angivna Azure Policy "DeployIfNotExist"-principer som beskrivs nedan för att skapa och konfigurera kontinuerliga export procedurer.

**Implementera dessa principer**

1. I tabellen nedan väljer du den princip som du vill använda:

    |Mål  |Policy  |Princip-ID  |
    |---------|---------|---------|
    |Kontinuerlig export till händelsehubben|[Distribuera export till händelsehubben för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Löpande export till Log Analytics arbets yta|[Distribuera export till Log Analytics-arbetsytan för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Du kan också hitta dessa genom att söka Azure Policy:
    > 1. Öppna Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Komma åt Azure Policy":::
    > 2. Från Azure Policy-menyn väljer du **definitioner** och söker efter dem efter namn. 

1. På sidan relevanta Azure Policy väljer du **tilldela**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Tilldela Azure Policy":::

1. Öppna varje flik och ange önskade parametrar:
    1. På fliken **grundläggande** anger du omfånget för principen. Om du vill använda centraliserad hantering tilldelar du principen till hanterings gruppen som innehåller de prenumerationer som ska använda kontinuerlig export konfiguration. 
    1. Ange resurs grupp och data typs information på fliken **parametrar** . 
        > [!TIP]
        > Varje parameter har en knapp beskrivning som förklarar de tillgängliga alternativen.
        >
        > Azure Policy fliken parametrar (1) ger till gång till liknande konfigurations alternativ som Security Center sidans löpande export (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Jämför parametrarna i kontinuerlig export med Azure Policy" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Om du vill tillämpa den här tilldelningen på befintliga prenumerationer öppnar du fliken **reparation** och väljer alternativet för att skapa en reparations uppgift.
1. Granska sidan Sammanfattning och välj **skapa**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Information om hur du exporterar till en Log Analytics-arbetsyta

Om du vill analysera Azure Security Center data i en Log Analytics arbets yta eller använda Azure-aviseringar tillsammans med Security Center aviseringar konfigurerar du kontinuerlig export till din Log Analytics arbets yta.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics tabeller och scheman

Säkerhets aviseringar och rekommendationer lagras i tabellerna *SecurityAlert* respektive *SecurityRecommendations* . 

Namnet på Log Analytics-lösningen som innehåller dessa tabeller beror på om du har aktiverat Azure Defender: Security (' Säkerhet och granskning ') eller SecurityCenterFree. 

> [!TIP]
> Om du vill se data på mål arbets ytan måste du aktivera någon av dessa lösningar **säkerhet och granskning** eller **SecurityCenterFree**.

![* SecurityAlert *-tabellen i Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Om du vill visa händelse scheman för de exporterade data typerna går du till [Log Analytics tabell scheman](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Visa exporterade aviseringar och rekommendationer i Azure Monitor

Du kan också välja att Visa exporterade säkerhets aviseringar och/eller rekommendationer i [Azure Monitor](../azure-monitor/alerts/alerts-overview.md). 

Azure Monitor ger en enhetlig aviserings upplevelse för en rad olika Azure-aviseringar, inklusive diagnostisk logg, mått aviseringar och anpassade aviseringar baserat på frågor från Log Analytics-arbetsyta.

Om du vill visa aviseringar och rekommendationer från Security Center i Azure Monitor konfigurerar du en varnings regel baserat på Log Analytics frågor (logg avisering):

1. På sidan **aviseringar** för Azure Monitor väljer du **ny aviserings regel**.

    ![Azure Monitor sidan aviseringar](./media/continuous-export/azure-monitor-alerts.png)

1. På sidan Skapa regel konfigurerar du din nya regel (på samma sätt som du konfigurerar en [logg varnings regel i Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md)):

    * För **resurs** väljer du den Log Analytics arbets yta som du exporterade säkerhets aviseringar och rekommendationer till.

    * För **villkor** väljer du **anpassad loggs ökning**. På sidan som visas konfigurerar du frågan, lookback perioden och frekvens perioden. I Sök frågan kan du skriva *SecurityAlert* eller *SecurityRecommendation* för att fråga data typerna som Security Center kontinuerligt exportera till när du aktiverar funktionen för kontinuerlig export till Log Analytics. 
    
    * Du kan också konfigurera den [Åtgärds grupp](../azure-monitor/alerts/action-groups.md) som du vill utlösa. Åtgärds grupper kan utlösa e-post som skickas, ITSM biljetter, Webhooks och mycket annat.
    ![Azure Monitor varnings regel](./media/continuous-export/azure-monitor-alert-rule.png)

Nu visas nya Azure Security Center aviseringar eller rekommendationer (beroende på dina konfigurerade regler för kontinuerlig export och det villkor som du har definierat i Azure Monitor varnings regel) i Azure Monitor aviseringar, med automatisk utlösning av en åtgärds grupp (om det finns).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Manuell export av aviseringar och rekommendationer

Om du vill ladda ned en CSV-rapport för aviseringar eller rekommendationer öppnar du sidan **säkerhets aviseringar** eller **rekommendationer** och väljer knappen **Hämta CSV-rapport** .

[![Hämta aviserings data som en CSV-fil](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> De här rapporterna innehåller aviseringar och rekommendationer för resurser från de för tillfället valda prenumerationerna.


## <a name="faq---continuous-export"></a>Vanliga frågor och svar – löpande export

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Vilka kostnader ingår i export av data?

Det kostar ingenting att aktivera löpande export. Kostnader kan tillkomma för inmatning och lagring av data i din Log Analytics arbets yta, beroende på din konfiguration där. 

Läs mer om [priser för Log Analytics-arbetsyta](https://azure.microsoft.com/pricing/details/monitor/).

Läs mer om [priser för Azure Event Hub](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>Innehåller exporten data om det aktuella läget för alla resurser?

Nej. Kontinuerlig export skapas för strömning av **händelser**:

- **Aviseringar** som tagits emot innan du aktiverade exporten exporteras inte.
- **Rekommendationer** skickas när en resurss kompatibilitetstillstånd ändras. Till exempel när en resurs blir från felfritt till dåligt. Med aviseringar exporteras därför rekommendationer för resurser som inte har ändrat tillstånd sedan du aktiverade exporten.
- **Säkra poäng (för hands version)** per säkerhets kontroll eller prenumeration skickas när en säkerhets kontrolls Poäng ändras med 0,01 eller mer. 
- **Status för regelefterlevnad (för hands version)** skickas när status för resursens kompatibilitet ändras.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>Varför skickas rekommendationer med olika intervall?

Olika rekommendationer har olika utvärderings intervall för kompatibilitet, vilket kan variera från några minuter till några dagar. Rekommendationerna skiljer sig därför från hur lång tid det tar innan de visas i exporten.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Stöder kontinuerlig export alla scenarier för affärs kontinuitet eller haveri beredskap (BCDR)?

När du förbereder din miljö för BCDR-scenarier där mål resursen drabbas av ett avbrott eller en annan katastrof, är det organisationens ansvar att förhindra data förlust genom att skapa säkerhets kopior enligt rikt linjerna från Azure Event Hubs, Log Analytics arbets yta och Logic app.

Läs mer i [Azure Event Hubs-geo-Disaster Recovery](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-with-azure-security-center-free"></a>Är kontinuerlig export tillgänglig med Azure Security Center kostnads fritt?

Ja! Observera att många Security Center-aviseringar endast anges när du har aktiverat Azure Defender. Ett bra sätt att förhandsgranska de aviseringar du får i dina exporterade data är att se de aviseringar som visas på Security Centerens sidor i Azure Portal.



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du konfigurerar kontinuerliga exporter av dina rekommendationer och aviseringar. Du har också lärt dig hur du hämtar dina aviserings data som en CSV-fil. 

Information om relaterade material finns i följande dokumentation: 

- Lär dig mer om [mallar för automatisering av arbets yta](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Dokumentation om Azure Event Hubs](../event-hubs/index.yml)
- [Dokumentation om Azure Sentinel](../sentinel/index.yml)
- [Dokumentation för Azure Monitor](../azure-monitor/index.yml)
- [Exportera data typer scheman](https://aka.ms/ASCAutomationSchemas)