---
title: Azure Security Center FAQ – insamling och agenter för data
description: Vanliga frågor om data insamling, agenter och arbets ytor för Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2020
ms.author: memildin
ms.openlocfilehash: 4ef01045d1ca16d0101cdd9ccfcd118231cd28de
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456121"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Vanliga frågor och svar om data insamling, agenter och arbets ytor

Security Center samlar in data från dina virtuella Azure-datorer (VM), skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure-datorer (inklusive lokala datorer) för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Faktureras jag för Azure Monitor loggar på arbets ytorna som skapats av Security Center?

Nej. Arbets ytor som skapats av Security Center, och som kon figurer ATS för Azure Monitor loggar per nod fakturering, debiteras inte Azure Monitor loggar. Security Center faktureringen baseras alltid på din Security Center säkerhets princip och de lösningar som är installerade på en arbets yta:

- **Azure Defender av** – Security Center aktiverar lösningen "SecurityCenterFree" på standard arbets ytan. Du debiteras inte om Azure Defender är inaktive rad.

- **Azure Defender på** – Security Center aktiverar "Security"-lösningen på standard arbets ytan.

Information om priser i din valuta och enligt din region finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Pris nivån för Log Analytics för arbets ytor som skapats av Security Center påverkar inte Security Center fakturering.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Vad är Log Analytics agenten?

För att övervaka säkerhets problem och hot, Azure Security Center är beroende av [Log Analytics agenten](../azure-monitor/agents/log-analytics-agent.md) – detta är samma agent som används av Azure Monitor tjänsten. 

Agenten kallas ibland Microsoft Monitoring Agent (eller "MMA"). 

Agenten samlar in olika säkerhetsrelaterade konfigurations detaljer och händelse loggar från anslutna datorer och kopierar sedan data till din Log Analytics arbets yta för ytterligare analys. Exempel på sådana data är: operativ systemets typ och version, operativ system loggar (Windows-händelseloggar), processer som körs, dator namn, IP-adresser och inloggad användare.

Se till att datorerna kör ett av de operativ system som stöds för agenten enligt beskrivningen på följande sidor:

* [Log Analytics agent för operativ system som stöds av Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

* [Log Analytics agent för operativ system som stöds av Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Läs mer om de [data som samlas in av Log Analytics agenten](security-center-enable-data-collection.md).




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Vad är kvalificerat för en virtuell dator för automatisk etablering av den Log Analytics Agent installationen?

Virtuella Windows-eller Linux IaaS-datorer är kvalificerade om:

- Tillägget för Log Analytics agent är inte installerat på den virtuella datorn.
- Den virtuella datorn körs.
- Windows eller Linux [Azure Virtual Machine agent](../virtual-machines/extensions/agent-windows.md) är installerad.
- Den virtuella datorn används inte som en installation av en brand vägg för webbaserade program eller nästa generations brand vägg.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>Var skapas standard arbets ytan Log Analytics?

Platsen för standard arbets ytan beror på din Azure-region:

- För virtuella datorer i USA och Brasilien är arbets ytans plats USA
- För virtuella datorer i Kanada är arbets ytans plats Kanada
- För virtuella datorer i Europa är platsen för arbets ytan Europa
- För virtuella datorer i Storbritannien är arbets ytans plats i Storbritannien
- För virtuella datorer i Asien, östra och Sydostasien arbets ytans plats är Asien
- För virtuella datorer i Korea är platsen för arbets ytan Korea
- För virtuella datorer i Indien är platsen för arbets ytan Indien
- För virtuella datorer i Japan är platsen för arbets ytan Japan
- För virtuella datorer i Kina är arbets ytans plats Kina
- För virtuella datorer i Australien är arbets ytan Australien


## <a name="what-security-events-are-collected-by-the-log-analytics-agent"></a>Vilka säkerhets händelser samlas in av Log Analytics agenten?

En fullständig lista över de säkerhets händelser som samlas in av agenten finns i [vilka händelse typer som lagras för inställningarna "common" och "minimal" säkerhets händelser?](security-center-enable-data-collection.md#what-event-types-are-stored-for-common-and-minimal).

> [!IMPORTANT]
> Observera att för vissa tjänster, t. ex. Azure-brandvägg, om du har aktiverat loggning och valt en chatt-resurs som ska loggas (till exempel genom att ange loggen som *utförlig*) kan du se betydande påverkan på din Log Analytics lagrings plats behov. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan jag ta bort standard arbets ytor som skapats av Security Center?

**Det rekommenderas inte att du tar bort standard arbets ytan.** Security Center använder standard arbets ytorna för att lagra säkerhets data från dina virtuella datorer. Om du tar bort en arbets yta kan Security Center inte samla in dessa data och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga.

Ta bort Log Analytics agenten på de virtuella datorer som är anslutna till den borttagna arbets ytan för att återställa. Security Center installerar om agenten och skapar nya standard arbets ytor.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hur kan jag använda min befintliga Log Analytics-arbetsyta?

Du kan välja en befintlig Log Analytics-arbetsyta för att lagra data som samlas in av Security Center. Så här använder du din befintliga Log Analytics-arbetsyta:

- Arbets ytan måste vara kopplad till den valda Azure-prenumerationen.
- Du måste minst ha Läs behörighet för att få åtkomst till arbets ytan.

Så här väljer du en befintlig Log Analytics arbets yta:

1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj relevant prenumeration.
1. Öppna sidan **Automatisk etablering** .
1. För Log Analytics agent väljer du **Redigera konfiguration**. 

    :::image type="content" source="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png" alt-text="Konfigurationen för den Log Analytics agent som ska användas när automatisk distribution används" lightbox="./media/security-center-enable-data-collection/edit-configuration-auto-deploy-agent.png":::

1. Välj **Anslut virtuella Azure-datorer till en annan arbets yta** och välj din befintliga arbets yta.

    :::image type="content" source="./media/security-center-enable-data-collection/choose-workspace.png" alt-text="Välja en arbets yta som inte är standard för din Log Analytics agent att rapportera till" lightbox="./media/security-center-enable-data-collection/choose-workspace.png":::

    > [!TIP]
    > Listan innehåller bara arbets ytor som du har åtkomst till och som finns i din Azure-prenumeration.

1. Välj **Spara**. Du får en fråga om du vill konfigurera om övervakade virtuella datorer.

    - Välj **Nej** om du vill att de nya inställningarna för arbets ytan **bara ska gälla för nya virtuella datorer**. De nya inställningarna för arbets ytan gäller endast nya agent installationer. nyligen identifierade virtuella datorer som inte har Log Analytics-agenten installerad.
    - Välj **Ja** om du vill att de nya inställningarna för arbets ytan ska **tillämpas på alla virtuella datorer**. Dessutom återansluts varje virtuell dator som är ansluten till en Security Center skapad arbets yta till den nya mål arbets ytan.

    > [!NOTE]
    > Om du väljer **Ja** tar du inte bort några arbets ytor som skapats av Security Center tills alla virtuella datorer har återanslutits till den nya mål arbets ytan. Den här åtgärden Miss lyckas om en arbets yta tas bort för tidigt.


## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Vad händer om Log Analytics agent redan har installerats som ett tillägg på den virtuella datorn?<a name="mmaextensioninstalled"></a>

När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätter inte befintliga anslutningar till användar arbets ytor. Security Center kommer att lagra säkerhets data från en virtuell dator i en arbets yta som redan är ansluten, förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.

Mer information finns i [Automatisk etablering i fall av en redan befintlig agent installation](security-center-enable-data-collection.md#preexisting).



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Vad händer om en Log Analytics Agent installeras direkt på datorn men inte som ett tillägg (direkt agent)?<a name="directagentinstalled"></a>

Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg), kommer Security Center att installera Log Analytics agent tillägget, och kan uppgradera Log Analytics agent till den senaste versionen.

Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar dessutom till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).

Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "säkerhet" eller "SecurityCenterFree" på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.

För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.

För befintliga datorer på prenumerationer som har registrerats på Security Center före mars 17 2019, installeras inte Log Analytics agent-tillägget och datorn påverkas inte om en befintlig agent identifieras. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på dessa datorer

Mer information finns i nästa avsnitt [Vad händer om en System Center Operations Manager-eller OMS Direct-agent redan är installerad på den virtuella datorn?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Vad händer om en System Center Operations Manager agent redan är installerad på den virtuella datorn?<a name="scomomsinstalled"></a>

Security Center kommer att installera Log Analytics agent-tillägget sida vid sida till den befintliga System Center Operations Manager agenten. Den befintliga agenten fortsätter att rapportera till System Center Operations Manager servern på vanligt sätt. Observera att Operations Manager agent och Log Analytics agent delar gemensamma kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen. OBS! om version 2012 av Operations Manager agenten är installerad ska du inte aktivera automatisk etablering (hanterings funktioner kan gå förlorade när Operations Manager-servern också är version 2012).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Vad är effekten av att ta bort de här tilläggen?

Om du tar bort Microsoft Monitoring Extension kan Security Center inte samla in säkerhets data från den virtuella datorn och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga. Inom 24 timmar fastställer Security Center att den virtuella datorn saknar tillägget och installerar tillägget igen.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Vill du Hur gör jag för att stoppa den automatiska Agent installationen och arbets ytan?

Du kan inaktivera automatisk etablering för dina prenumerationer i säkerhets principen, men detta rekommenderas inte. Stänga av de automatiska etablerings gränserna Security Center rekommendationer och aviseringar. Så här inaktiverar du automatisk etablering:

1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj relevant prenumeration.
1. Om din prenumeration har aktiverat Azure Defender öppnar du **Azure Defender-planer** och väljer **Azure Defender av**.

    :::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Aktivera eller inaktivera Azure Defender":::

1. På sidan **Automatisk etablering** väljer du penna och inaktiverar automatisk etablering i  **säkerhets principen – data insamlings** sidan.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Aktivera automatisk distribution för Log Analytics agent":::

1. Välj **Spara**.


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Bör jag inte välja att skapa en automatisk agent installation och skapa arbets ytor?

> [!NOTE]
> Var noga med att granska avsnitten [vilka är konsekvenserna av väljer out?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) och [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) om du väljer att inte välja automatisk etablering.

Du kanske vill inaktivera automatisk etablering om följande gäller för dig:

- Automatisk agent installation av Security Center gäller för hela prenumerationen. Du kan inte använda automatisk installation för en delmängd av virtuella datorer. Om det finns kritiska virtuella datorer som inte kan installeras med Log Analytics-agenten bör du inte välja automatisk etablering.
- Installationen av Log Analytics agent-tillägget uppdaterar agentens version. Detta gäller för en direkt agent och en System Center Operations Manager agent (i den senare Operations Manager och Log Analytics agent delar vanliga körnings bibliotek, som kommer att uppdateras i processen). Om den installerade Operations Manager agenten är version 2012 och uppgraderas kan hanterings funktioner gå förlorade när Operations Manager-servern också är version 2012. Överväg väljer från automatisk etablering om den installerade Operations Manager-agenten är version 2012.
- Om du har en anpassad arbets yta utanför prenumerationen (en centraliserad arbets yta) bör du inte välja automatisk etablering. Du kan installera Log Analytics agent-tillägget manuellt och ansluta det till arbets ytan utan att Security Center åsidosätta anslutningen.
- Om du vill undvika att skapa flera arbets ytor per prenumeration och du har en egen anpassad arbets yta i prenumerationen har du två alternativ:

   1. Du kan välja att inte använda automatisk etablering. När du har migrerat anger du inställningarna för standard arbets ytan enligt beskrivningen i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Du kan också låta migreringen slutföras, Log Analytics agenten ska installeras på de virtuella datorerna och de virtuella datorer som är anslutna till den skapade arbets ytan. Välj sedan din egen anpassade arbets yta genom att ange inställningen standard arbets yta med väljer i för att konfigurera om de redan installerade agenterna. Mer information finns i [Hur kan jag använda min befintliga Log Analytics-arbetsyta?](#how-can-i-use-my-existing-log-analytics-workspace)


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Vilka är konsekvenserna av väljer out of automatisk etablering?

När migreringen är klar kan Security Center inte samla in säkerhets data från den virtuella datorn och vissa säkerhets rekommendationer och aviseringar är inte tillgängliga. Om du avinstallerar installerar du Log Analytics-agenten manuellt. Se [rekommenderade steg när du väljer ut](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Vilka är de rekommenderade stegen vid väljer av automatisk etablering?

Installera Log Analytics agent-tillägget manuellt så Security Center kan samla in säkerhets data från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar. Se [agent installation för virtuell Windows-dator](../virtual-machines/extensions/oms-windows.md) eller [agent installation för virtuell Linux-dator](../virtual-machines/extensions/oms-linux.md) för att få hjälp med installationen.

Du kan ansluta agenten till en befintlig anpassad arbets yta eller Security Center skapade arbets ytan. Om en anpassad arbets yta inte har lösningarna "säkerhet" eller "SecurityCenterFree" aktiverade måste du använda en lösning. Om du vill använda väljer du den anpassade arbets ytan eller prenumerationen och använder en pris nivå via sidan **säkerhets policy – pris nivå** .

:::image type="content" source="./media/security-center-platform-migration-faq/pricing-tier.png" alt-text="Aktivera eller inaktivera Azure Defender":::

Security Center aktiverar rätt lösning på arbets ytan baserat på den valda pris nivån.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center?<a name="remove-oms"></a>

Du kan ta bort den Log Analytics agenten manuellt. Detta rekommenderas inte eftersom det begränsar Security Center rekommendationer och aviseringar.

> [!NOTE]
> Om data insamling har Aktiver ATS kommer Security Center att installera om agenten efter att du har tagit bort den.  Du måste inaktivera data insamlingen innan du tar bort agenten manuellt. Se Hur gör jag för att stoppa den automatiska Agent installationen och arbets ytan? instruktioner om inaktive ring av data insamling.

Ta bort agenten manuellt:

1.    Öppna **Log Analytics** på portalen.

1.    På sidan Log Analytics väljer du en arbets yta:

1.    Välj de virtuella datorer som du inte vill övervaka och välj **Koppla från**.

   ![Ta bort agenten][3]

> [!NOTE]
> Om en virtuell Linux-dator redan har en icke-tillägg OMS-agent tar borttagning av tillägget bort agenten även om du måste installera om den.


## <a name="how-do-i-disable-data-collection"></a>Vill du Hur gör jag för att inaktivera data insamling?

Automatisk etablering rekommenderas för att få säkerhets aviseringar och rekommendationer om system uppdateringar, sårbarheter för operativ system och Endpoint Protection. Automatisk etablering är inaktiverat som standard.

Om du har aktiverat den, men nu vill inaktivera den:

1. Öppna **Security Center** på [Azure Portal](https://portal.azure.com)och välj **priser och inställningar**.

1. Välj den prenumeration där du vill inaktivera automatisk etablering.

1. Inaktivera växlingen för Log Analytics-agenten under **Automatisk etablering**.


## <a name="how-do-i-enable-data-collection"></a>Hur gör jag för att aktivera data insamling?

Du kan aktivera data insamling för din Azure-prenumeration i säkerhets principen. För att aktivera data insamling. [Logga](https://portal.azure.com)in på Azure Portal, Välj **bläddra**, Välj **Security Center** och välj **säkerhets princip**. Välj den prenumeration som du vill aktivera automatisk etablering för. När du väljer en prenumerations **säkerhets princip öppnas data insamling** . Under **Automatisk etablering** väljer du **på**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Vad händer när data insamlingen är aktive rad?

När automatisk etablering har Aktiver ATS etablerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Automatisk etablering rekommenderas, men det finns även manuell agent installation. [Lär dig hur du installerar Log Analytics agent-tillägget](../azure-monitor/vm/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

Agenten gör det möjligt för processen att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas av Security Centers identifierings tjänster. Mer information om de uppgifter som registrerats för varje ny process finns [i beskrivnings fält i 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). Agenten samlar även in 4688-händelser som skapats på den virtuella datorn och lagrar dem i sökningen.

Agenten aktiverar även insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md), Security Center konfigurerar en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Den här principen gör att AppLocker genererar händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att Observera att den här principen inte kommer att konfigureras på datorer där det redan finns en konfigurerad AppLocker-princip. 

När Security Center identifierar misstänkt aktivitet på den virtuella datorn, meddelas kunden via e-post om [säkerhets kontakt information](security-center-provide-security-contact-details.md) har tillhandahållits. En avisering visas också i Security Center säkerhets aviserings instrument panel.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Kommer Security Center fungera med en OMS-Gateway?

Ja. Azure Security Center utnyttjar Azure Monitor för att samla in data från virtuella Azure-datorer och-servrar med hjälp av Log Analytics agenten.
För att samla in data måste varje virtuell dator och Server ansluta till Internet via HTTPS. Anslutningen kan dirigeras via en proxy eller via [OMS-gatewayen](../azure-monitor/agents/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Påverkar övervaknings agenten prestanda för mina servrar?

Agenten förbrukar en nominell mängd system resurser och påverkar inte prestandan. Mer information om prestanda påverkan och agenten och tillägget finns i [planerings-och drift guide](security-center-planning-and-operations-guide.md#data-collection-and-storage).




<!--Image references-->
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png