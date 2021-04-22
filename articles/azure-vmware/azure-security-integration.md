---
title: Skydda dina Azure VMware Solution virtuella datorer med Azure Security Center integrering
description: Skydda dina Azure VMware Solution virtuella datorer med Azures inbyggda säkerhetsverktyg från Azure Security Center instrumentpanel.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877524"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Skydda dina Azure VMware Solution virtuella datorer med Azure Security Center integrering

Azures inbyggda säkerhetsverktyg ger skydd för en hybridmiljö med Azure, Azure VMware Solution virtuella datorer och lokala virtuella datorer. Den här artikeln visar hur du ställer in Azure-verktyg för säkerhet i hybridmiljöer. Du använder dessa verktyg för att identifiera och hantera olika hot.

## <a name="azure-native-services"></a>Inbyggda Azure-tjänster

Här är en snabb sammanfattning av inbyggda Azure-tjänster:

- **Log Analytics-arbetsyta:** Log Analytics-arbetsytan är en unik miljö för att lagra loggdata. Varje arbetsyta har sitt eget datalager och sin egen konfiguration. Datakällor och lösningar konfigureras för att lagra sina data på en specifik arbetsyta.
- **Azure Security Center:** Azure Security Center är ett enhetligt system för säkerhetshantering av infrastruktur. Det ökar säkerheten för datacenter och ger avancerat skydd mot hot i hybridarbetsbelastningar i molnet eller lokalt.
- **Azure Sentinel:** Azure Sentinel är en molnbaserad SIEM-lösning (Security Information Event Management). Den tillhandahåller säkerhetsanalys, aviseringsidentifiering och automatiserad hotsvar i en miljö.

## <a name="topology"></a>Topologi

![Ett diagram som visar arkitekturen för integrerad säkerhet i Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics-agenten möjliggör insamling av loggdata från Azure, Azure VMware Solution och lokala virtuella datorer. Loggdata skickas till en Azure Monitor loggar och lagras på en Log Analytics-arbetsyta. Du kan distribuera Log Analytics-agenten med stöd för Arc-aktiverade [VM-tillägg](../azure-arc/servers/manage-vm-extensions.md) för servrar för nya och befintliga virtuella datorer. 

När loggarna har samlats in av Log Analytics-arbetsytan kan du konfigurera Log Analytics-arbetsytan med Azure Security Center. Azure Security Center kommer att utvärdera sårbarhetsstatusen för Azure VMware Solution virtuella datorer och skapa en avisering för eventuella kritiska säkerhetsrisk. Den utvärderar till exempel saknade operativsystemskorrigeringar, felaktiga säkerhetskonfigurationer och [Endpoint Protection.](../security-center/security-center-services.md)

Du kan konfigurera Log Analytics-arbetsytan med Azure Sentinel för aviseringsidentifiering, hotsynlighet, jakt och svar på hot. I föregående diagram är Azure Security Center ansluten till Azure Sentinel med Azure Security Center anslutningsappen. Azure Security Center kommer att vidarebefordra sårbarheten i miljön Azure Sentinel att skapa en incident och mappa med andra hot. Du kan också skapa den schemalagda regelfrågan för att identifiera oönskad aktivitet och konvertera den till incidenterna.

## <a name="benefits"></a>Fördelar

- Inbyggda Azure-tjänster kan användas för säkerhet i hybridmiljöer i Azure, Azure VMware Solution och lokala tjänster.
- Med hjälp av en Log Analytics-arbetsyta kan du samla in data eller loggar till en enda punkt och presentera samma data för olika inbyggda Azure-tjänster.
- Azure Security Center erbjuder många funktioner, inklusive:
    - Övervakning av filintegritet
    - Identifiering av fillös attack
    - Utvärdering av operativsystemskorrigering 
    - Utvärdering av säkerhetsfelkonfigurationer
    - Utvärdering av slutpunktsskydd
- Azure Sentinel kan du:
    - Samla in data i molnskala för alla användare, enheter, program och infrastruktur, både lokalt och i flera moln.
    - Identifiera tidigare oupptäckta hot.
    - Undersöka hot med artificiell intelligens och leta efter misstänkta aktiviteter i stor skala.
    - Reagera snabbt på incidenter med inbyggd orkestrering och automatisering av vanliga uppgifter.

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Du behöver en Log Analytics-arbetsyta för att samla in data från olika källor. Mer information finns i [Skapa en Log Analytics-arbetsyta från Azure Portal](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Distribuera Security Center och konfigurera Azure VMware Solution virtuella datorer

Azure Security Center är ett förkonfigurerat verktyg som inte kräver distribution. I Azure Portal du efter **Security Center** och markerar den.

### <a name="enable-azure-defender"></a>Aktivera Azure Defender

Azure Defender utökar Azure Security Center avancerade skydd mot hot i dina hybridarbetsbelastningar, både lokalt och i molnet. För att skydda Azure VMware Solution virtuella datorer måste du aktivera Azure Defender. 

1. I Security Center väljer du **Komma igång.**

2. Välj fliken **Uppgradera** och välj sedan din prenumeration eller arbetsyta. 

3. Välj **Uppgradera** för att aktivera Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Lägga Azure VMware Solution virtuella datorer till Security Center

1. I Azure Portal du på **Azure Arc** och markerar den.

2. Under Resurser väljer du **Servrar och** sedan **+Lägg till**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="En skärmbild som visar Azure Arc för att lägga till en Azure VMware Solution virtuell dator i Azure.":::

3. Välj **Generera skript.**
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="En skärmbild av Azure Arc visar alternativet för att lägga till en server med hjälp av ett interaktivt skript."::: 
 
4. På **fliken Förutsättningar** väljer du **Nästa.**

5. På **fliken Resursinformation** fyller du i följande information: 
    - Prenumeration
    - Resursgrupp
    - Region 
    - Operativsystem
    - Information om proxyserver
    
    Välj sedan **Nästa: Taggar**.

6. På fliken **Taggar** väljer du **Nästa.**

7. På fliken **Ladda ned och kör skript** väljer du Ladda **ned**.

8. Ange operativsystemet och kör skriptet på den virtuella Azure VMware Solution datorn.

## <a name="view-recommendations-and-passed-assessments"></a>Visa rekommendationer och godkända utvärderingar

1. I Azure Security Center väljer du **Inventering** i den vänstra rutan.

2. För Resurstyp väljer du **Servrar – Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="En skärmbild av sidan Azure Security Center inventering som visar servrar – Azure Arc under Resurstyp.":::

3. Välj namnet på din resurs. En sida öppnas med information om säkerhetshälsa för din resurs.

4. Under **Rekommendationslista** väljer du **flikarna Rekommendationer,** **Godkända** utvärderingar och Ej **tillgängliga utvärderingar** för att visa den här informationen.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="En skärmbild av Azure Security Center som visar säkerhetsrekommendationer och utvärderingar.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Distribuera en Azure Sentinel arbetsyta

Azure Sentinel bygger på en Log Analytics-arbetsyta. Ditt första steg i Azure Sentinel är att välja den Log Analytics-arbetsyta som du vill använda för detta ändamål.

1. I Azure Portal du efter **Azure Sentinel** och markerar den.

2. På sidan Azure Sentinel arbetsytor väljer du **+Lägg till**.

3. Välj Log Analytics-arbetsytan och välj Lägg **till**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Aktivera datainsamlare för säkerhetshändelser på Azure VMware Solution virtuella datorer

Nu är du redo att ansluta Azure Sentinel datakällor, i det här fallet säkerhetshändelser.

1. På sidan Azure Sentinel arbetsytor väljer du den konfigurerade arbetsytan.

2. Under Konfiguration väljer du **Datakopplingar.**

3. Under kolumnen Namn på anslutningsapp väljer **du Säkerhetshändelser** i listan och väljer sedan **Öppna anslutningssidan.**

4. På anslutningssidan väljer du de händelser som du vill strömma och väljer sedan **Tillämpa ändringar.**

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Skärmbild av sidan Säkerhetshändelser i Azure Sentinel där du kan välja vilka händelser som ska strömmas.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Ansluta Azure Sentinel med Azure Security Center  

1. På Azure Sentinel arbetsytan väljer du den konfigurerade arbetsytan.

2. Under Konfiguration väljer du **Datakopplingar.**

3. Välj **Azure Security Center** i listan och välj sedan **Öppna anslutningssidan.**

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Skärmbild av sidan Dataanslutningsappar i Azure Sentinel visar val för att ansluta Azure Security Center med Azure Sentinel.":::

4. Välj **Anslut** för att ansluta Azure Security Center med Azure Sentinel.

5. Aktivera **Skapa incident** för att generera en incident för Azure Security Center.

## <a name="create-rules-to-identify-security-threats"></a>Skapa regler för att identifiera säkerhetshot

När du har anslutit datakällor till Azure Sentinel kan du skapa regler för att generera aviseringar för identifierade hot. I följande exempel skapar vi en regel för försök att logga in på Windows Server med fel lösenord.

1. På Azure Sentinel översiktssida går du till Konfigurationer och väljer **Analytics.**

2. Under Konfigurationer väljer du **Analytics.**

3. Välj **+ Skapa** och välj Schemalagd frågeregel i **listrutan.**

4. På fliken **Allmänt** anger du nödvändig information.

    - Name
    - Beskrivning
    - Taktiker
    - Allvarlighetsgrad
    - Status

    Välj **Nästa: Ange regellogik >**.

5. På fliken **Ange regellogik** anger du nödvändig information.

    - Regelfråga (här visar vår exempelfråga)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Mappa entiteter
    - Schemaläggning av frågor
    - Aviseringströskelvärde
    - Händelsegruppering
    - Undertryckning

    Välj **Nästa**.

6. På fliken **Incidentinställningar aktiverar** du Skapa incidenter från aviseringar som **utlöses** av den här analysregeln och väljer **Nästa: Automatiserad >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Skärmbild av guiden Analysregel för att skapa en ny regel i Azure Sentinel. Visar Skapa incidenter från aviseringar som utlöses av den här regeln som aktiverade.":::

7. Välj **Nästa: Granska >**.

8. Granska **informationen på fliken** Granska och skapa och välj **Skapa.**

Efter det tredje misslyckade försöket att logga in på Windows Server utlöser den skapade regeln en incident för varje misslyckat försök.

## <a name="view-alerts"></a>Visa aviseringar

Du kan visa genererade incidenter med Azure Sentinel. Du kan också tilldela incidenter och stänga dem när de har lösts, allt inifrån Azure Sentinel.

1. Gå till Azure Sentinel översiktssidan.

2. Under Hothantering väljer du **Incidenter.**

3. Välj en incident. Du kan sedan tilldela incidenten till ett team för lösning.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Skärmbild av Azure Sentinel incidenter med incident valt och alternativ för att tilldela incidenten för lösning.":::

    När du har löst problemet kan du stänga det.

## <a name="hunt-security-threats-with-queries"></a>Sök efter säkerhetshot med frågor

Du kan skapa frågor eller använda den tillgängliga fördefinierade frågan i Azure Sentinel för att identifiera hot i din miljö. Följande steg kör en fördefinierad fråga.

1. Gå till Azure Sentinel översiktssidan.

2. Under Hothantering väljer du **Jakt.** En lista över fördefinierade frågor visas.

3. Välj en fråga och välj sedan **Kör fråga.**

4. Välj **Visa resultat** för att kontrollera resultatet.

### <a name="create-a-new-query"></a>Skapa en ny fråga

1.  Under Hothantering väljer du **Jakt** och sedan **+Ny fråga.**

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Skärmbild av Azure Sentinel jaktsida med + Ny fråga markerat.":::

2. Fyll i följande information för att skapa en anpassad fråga.

    - Name
    - Beskrivning
    - Anpassad fråga
    - Ange mappning
    - Taktiker
    
3. Välj **Skapa**. Du kan sedan välja den skapade frågan, **Köra fråga** och **Visa resultat.**

## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på hur du skyddar Azure VMware Solution virtuella datorer kanske du vill veta mer om:

- Använda Azure Defender [instrumentpanelen](../security-center/azure-defender-dashboard.md)
- [Avancerad attackidentifiering i flera Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Livscykelhantering för virtuella Azure VMware Solution datorer](lifecycle-management-of-azure-vmware-solution-vms.md)
