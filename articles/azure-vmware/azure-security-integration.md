---
title: Skydda dina virtuella datorer med Azure VMware-lösningen med Azure Security Center-integrering
description: Lär dig hur du skyddar dina virtuella Azure VMware-lösningar med Azures inbyggda säkerhets verktyg från en enda instrument panel i Azure Security Center.
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: e461e7e49619f7063c0583975f36a2e3dfe5a724
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937346"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Skydda dina virtuella datorer med Azure VMware-lösningen med Azure Security Center-integrering

Inbyggda Azure-säkerhetsverktyg ger en säker infrastruktur för en hybrid miljö i Azure, Azure VMware-lösning och lokala virtuella datorer (VM). Den här artikeln visar hur du konfigurerar Azure-verktyg för hybrid miljö säkerhet. Du använder olika verktyg för att identifiera och hantera olika typer av hot.

## <a name="azure-native-services"></a>Inbyggda Azure-tjänster

Här är en snabb sammanfattning av varje Azure Native-tjänst:

- **Log Analytics arbets yta:** Log Analytics-arbetsytan är en unik miljö för lagring av loggdata. Varje arbetsyta har sitt eget datalager och sin egen konfiguration. Data källor och lösningar har kon figurer ATS för att lagra data i en angiven arbets yta.
- **Azure Security Center:** Azure Security Center är ett enhetligt infrastruktur säkerhets hanterings system. Det stärker säkerhets positionen i data centren och ger avancerat skydd för Hybrid arbets belastningar i molnet eller lokalt.
- **Azure Sentinel:** Azure Sentinel är en molnbaserad SIEM-lösning (Security information Event Management) och SOAR (Security Orchestration autoresponse). Den ger intelligent säkerhets analys och hot information i en miljö. Det är en enskild lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar.

## <a name="topology"></a>Topologi

![Ett diagram som visar arkitekturen för Azure-integrerad säkerhet.](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics agenten aktiverar insamling av loggdata från Azure, Azure VMware-lösning och lokala virtuella datorer. Loggdata skickas till Azure Monitor loggar och lagras i en Log Analytics arbets yta. Du kan distribuera Log Analytics agenten med hjälp av Arc-aktiverade servrar [stöd för VM-tillägg](../azure-arc/servers/manage-vm-extensions.md) för nya och befintliga virtuella datorer. 

När loggarna har samlats in av Log Analytics arbets ytan kan du konfigurera arbets ytan Log Analytics med Azure Security Center. Azure Security Center kommer att bedöma sårbarhets statusen för virtuella Azure VMware-lösningar och utlösa en avisering för eventuella kritiska säkerhets risker. Till exempel utvärderas saknade korrigeringsfiler för operativ system, felaktig säkerhet och [Endpoint Protection](../security-center/security-center-services.md).

Du kan konfigurera arbets ytan Log Analytics med Azure Sentinel för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar. I föregående diagram är Azure Security Center ansluten till Azure Sentinel med hjälp av Azure Security Center Connector. Azure Security Center vidarebefordrar miljö problemet till Azure Sentinel för att skapa en incident och mappa med andra hot. Du kan också skapa en fråga för schemalagda regler för att identifiera oönskad aktivitet och konvertera den till incidenterna.

## <a name="benefits"></a>Fördelar

- Azure inbyggda tjänster kan användas för hybrid miljö säkerhet i Azure, Azure VMware-lösning och lokala tjänster.
- Med hjälp av en Log Analytics arbets yta kan du samla in data eller loggarna till en enda punkt och presentera samma data för olika Azure Native-tjänster.
- Azure Security Center innehåller säkerhetsfunktioner som övervakning av fil integritet, filbegränsad attack identifiering, utvärdering av operativ Systems korrigering, utvärdering av säkerhets konfiguration och Endpoint Protection.
- Med Azure Sentinel kan du:
    - Samla in data i moln skala över alla användare, enheter, program och infrastruktur, både lokalt och i flera moln.
    - Identifiera tidigare oidentifierade hot.
    - Undersök hot med artificiell intelligens och söka efter misstänkta aktiviteter i stor skala.
    - Svara på incidenter snabbt med inbyggd samordning och automatisering av vanliga uppgifter.

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Du behöver en Log Analytics arbets yta för att samla in data från olika källor. Se stegen i [skapa en Log Analytics arbets yta från Azure Portal](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Distribuera Security Center och konfigurera virtuella datorer med Azure VMware-lösningen

Azure Security Center är ett förkonfigurerat verktyg och behöver inte distribueras. Sök efter **Security Center** i Azure Portal och markera den.

### <a name="enable-azure-defender"></a>Aktivera Azure Defender

Azure Defender utökar Azure Security Centers avancerade hot skydd i dina hybrid arbets belastningar både lokalt och i molnet. För att skydda dina virtuella datorer i Azure VMware-lösningen måste du aktivera Azure Defender. 

1. I Security Center väljer du **komma igång**.

2. Välj fliken **Uppgradera** och välj sedan din prenumeration eller arbets yta. 

3. Välj **uppgradering** för att aktivera Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Lägg till virtuella Azure VMware-lösningar i Security Center

1. I Azure Portal söker du på **Azure-bågen** och markerar den.

2. Under resurser väljer du **servrar** och sedan **+ Lägg till**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="En skärm bild som visar Azure båg Server-sidan för att lägga till en virtuell Azure VMware-lösning i Azure.":::

3. Välj **skapa skript**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="En skärm bild av sidan Azure Arc som visar alternativ för att lägga till en server med interaktiv skript."::: 
 
4. På fliken **förutsättningar** väljer du **Nästa**.

5. Fyll i följande information på fliken **Resursinformation** : 
    - Prenumeration
    - Resursgrupp
    - Region 
    - Operativsystem
    - Information om proxyserver
    
    Välj sedan **Nästa: Taggar**.

6. På fliken **taggar** väljer du **Nästa**.

7. På fliken **Ladda ned och kör skript** väljer du **Hämta**.

8. Ange operativ systemet och kör skriptet på din virtuella Azure VMware-lösning.

## <a name="view-recommendations-and-passed-assessments"></a>Visa rekommendationer och slutförda utvärderingar

1. I Azure Security Center väljer du **inventering** i det vänstra fönstret.

2. För resurs typ väljer du **servrar – Azure-båge**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="En skärm bild av sidan Azure Security Center inventering som visar servrar – Azure Arc som valts under resurs typ.":::

3. Välj namnet på din resurs. En sida öppnas som visar säkerhets hälso informationen för din resurs.

4. Under **rekommendations lista** väljer du **rekommendationer**, **slutförda utvärderingar** och **otillgängliga bedömningar** för att visa dessa uppgifter.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="En skärm bild av Azure Security Center som visar säkerhets rekommendationer och utvärderingar.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Distribuera en Azure Sentinel-arbetsyta

Azure Sentinel skapas ovanpå en Log Analytics-arbetsyta. Ditt första steg i onboarding av Azure Sentinel är att välja den Log Analytics arbets yta som du vill använda för detta ändamål.

1. Sök efter **Azure Sentinel** i Azure Portal och markera det.

2. På sidan Azure Sentinel-arbetsytor väljer du **+ Lägg till**.

3. Välj arbets ytan Log Analytics och välj **Lägg till**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Aktivera data insamlare för säkerhets händelser på virtuella Azure VMware-lösningar

Nu är du redo att ansluta Azure Sentinel med dina data källor, i det här fallet säkerhets händelser.

1. På sidan Azure Sentinel-arbetsytor väljer du den konfigurerade arbets ytan.

2. Under konfiguration väljer du **data kopplingar**.

3. Under kolumnen anslutnings namn väljer du **säkerhets händelser** i listan och väljer sedan **Öppna kopplings sida**.

4. På sidan koppling väljer du de händelser som du vill strömma och väljer sedan **tillämpa ändringar**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Skärm bild av sidan säkerhets händelser i Azure Sentinel där du kan välja vilka händelser som ska strömmas.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Anslut Azure Sentinel med Azure Security Center  

1. På sidan Azure Sentinel-arbetsyta väljer du den konfigurerade arbets ytan.

2. Under konfiguration väljer du **data kopplingar**.

3. Välj **Azure Security Center** i listan och välj sedan **Öppna kopplings sida**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Skärm bild av sidan data kopplingar i Azure Sentinel som visar val för att ansluta Azure Security Center med Azure Sentinel.":::

4. Välj **Anslut** för att ansluta Azure Security Center med Azure Sentinel.

5. Aktivera **Skapa incident** för att generera en incident för Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Skapa regler för att identifiera säkerhetshot

När du har anslutit data källor till Azure Sentinel kan du skapa regler för att generera aviseringar baserat på identifierade hot. I följande exempel skapar vi en regel för att identifiera försök att logga in på Windows Server med fel lösen ord.

1. På översikts sidan för Azure Sentinel väljer du **Analytics** under konfigurationer.

2. Under konfigurationer väljer du **analys**.

3. Välj **+ skapa** och välj **regel för schemalagd fråga** i list rutan.

4. Ange den information som krävs på fliken **Allmänt** .

    - Name
    - Beskrivning
    - Taktiker
    - Allvarlighetsgrad
    - Status

    Välj **Nästa: Ange regel logik >**.

5. Ange den information som krävs på fliken **Ange regel logik** .

    - Regel fråga (här visas exempel frågan)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mappa entiteter
    - Fråga schemaläggning
    - Aviseringströskelvärde
    - Händelse gruppering
    - Under tryckning

    Välj **Nästa**.

6. På fliken **incident inställningar** aktiverar du **skapa incidenter från aviseringar som utlösts av den här analys regeln** och väljer **nästa: automatiserat svar >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Skärm bild av guiden analys regel för att skapa en ny regel i Azure Sentinel som visar skapa incidenter från aviseringar som utlösts av den här analys regeln som aktive rad.":::

7. Välj **Nästa: granska >**.

8. På fliken **Granska och skapa** granskar du informationen och väljer **skapa**.

När det tredje misslyckade försöket att logga in på Windows Server utlöser den skapade regeln en incident för varje misslyckat försök.

## <a name="view-generated-alerts"></a>Visa genererade aviseringar

Du kan visa genererade incidenter med Azure Sentinel. Du kan också tilldela incidenter och stänga dem när de har lösts, allt från Azure Sentinel.

1. Gå till översikts sidan för Azure Sentinel.

2. Välj **incidenter** under Threat Management.

3. Välj en incident. Du kan sedan tilldela incidenten till ett team för lösning.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Skärm bild av sidan för Azure Sentinel-incidenter där incidenten har valts och alternativet att tilldela incidenten för lösning.":::

    När du har löst problemet kan du stänga det.

## <a name="hunt-security-threats-with-queries"></a>Söker efter säkerhetshot med frågor

Du kan skapa frågor eller använda den tillgängliga fördefinierade frågan i Azure Sentinel för att identifiera hot i din miljö. Följande steg kör en fördefinierad fråga.

1. Gå till översikts sidan för Azure Sentinel.

2. Under hot hantering väljer du **jakt**. En lista över fördefinierade frågor visas.

3. Välj en fråga och välj sedan **Kör fråga**.

4. Välj **Visa resultat** för att kontrol lera resultaten.

### <a name="create-a-new-query"></a>Skapa en ny fråga

1.  Under Threat Management väljer du **jakt** och sedan **+ ny fråga**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Skärm bild av sidan för Azure Sentinel-jakt med + ny fråga markerad.":::

2. Fyll i följande information för att skapa en anpassad fråga.

    - Name
    - Beskrivning
    - Anpassad fråga
    - Ange mappning
    - Taktiker
    
3. Välj **Skapa**. Du kan sedan välja den skapade frågan, **köra frågan** och **Visa resultaten**.

## <a name="next-steps"></a>Nästa steg

- Lär dig att använda [Azure Defender-instrumentpanelen](../security-center/azure-defender-dashboard.md).
- Utforska det fullständiga skydds intervallet som erbjuds av [Azure Defender](../security-center/azure-defender.md).
- Lär dig mer om [Avancerad attack identifiering i Multistage i Azure Sentinel](../azure-monitor/learn/quick-create-workspace.md).
