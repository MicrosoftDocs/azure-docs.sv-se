---
title: Distribuera agenter automatiskt för Azure Security Center | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar automatisk etablering av Log Analytics agent och andra agenter och tillägg som används av Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: 17f3440df4fa88995f2148680aba926207a0e46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561270"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Konfigurera automatisk etablering för agenter och tillägg från Azure Security Center

Azure Security Center samlar in data från dina resurser med hjälp av relevanta agenter eller tillägg för den resursen och den typ av data insamling som du har aktiverat. Använd procedurerna nedan för att se till att dina resurser har de nödvändiga agenter och tillägg som används av Security Center.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="availability"></a>Tillgänglighet

| Aspekt                  | Information                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions tillstånd:          | **Funktion**: automatisk etablering är allmänt tillgänglig (ga)<br>**Agent och tillägg**: Log Analytics agent för virtuella Azure-datorer är ga, Microsoft Dependency agent är i för hands version, princip tillägg för KUBERNETES är ga                |
| Priset                | Kostnadsfri                                                                                                                                                                                                                         |
| Destinationer som stöds: | ![Ja](./media/icons/yes-icon.png) Azure-datorer<br>![Inga](./media/icons/no-icon.png) Azure Arc-datorer<br>![Inga](./media/icons/no-icon.png) Kubernetes-noder<br>![Inga](./media/icons/no-icon.png) Virtual Machine Scale Sets |
| Moln                 | ![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov, Kina gov, andra gov                                                                                                      |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Hur samlar Security Center in data?

Security Center samlar in data från dina virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure (inklusive lokala) datorer för att övervaka säkerhets problem och hot. 

Data insamling krävs för att ge insyn i saknade uppdateringar, felkonfigurerade OS-säkerhetsinställningar, Endpoint Protection-status och hälso-och hot skydd. Data insamling krävs bara för beräknings resurser, till exempel virtuella datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och datorer som inte är Azure-datorer. 

Du kan dra nytta av Azure Security Center även om du inte etablerar agenter. Du har dock begränsad säkerhet och de funktioner som anges ovan stöds inte.  

Data samlas in med:

- **Log Analytics agenten** läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. Exempel på sådana data är: operativ systemets typ och version, operativ system loggar (Windows-händelseloggar), processer som körs, dator namn, IP-adresser och inloggad användare.
- **Säkerhets tillägg**, till exempel [Azure policy-tillägg för Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), som också kan tillhandahålla data för att Security Center om specialiserade resurs typer.

> [!TIP]
> Som Security Center har växt, har de typer av resurser som kan övervakas också växt. Antalet tillägg har också växt. Automatisk etablering har utökats för att stödja ytterligare resurs typer genom att använda funktionerna i Azure Policy.

## <a name="why-use-auto-provisioning"></a>Varför ska jag använda automatisk etablering?
Alla agenter och tillägg som beskrivs på den här sidan *kan* installeras manuellt (se [manuell installation av Log Analytics agent](#manual-agent)). **Automatisk etablering** minskar dock hanterings kostnader genom att installera alla nödvändiga agenter och tillägg på befintliga och nya datorer för att säkerställa snabbare säkerhets täckning för alla resurser som stöds. 

Vi rekommenderar att du aktiverar automatisk etablering, men den är inaktive rad som standard.

## <a name="how-does-auto-provisioning-work"></a>Hur fungerar automatisk etablering?
Security Center inställningarna för automatisk etablering har en växling för varje typ av tillägg som stöds. När du aktiverar automatisk etablering av ett tillägg tilldelar du lämplig **distribution om principen inte finns** . Med den här princip typen ser du till att tillägget har allokerats på alla befintliga och framtida resurser av den typen.

> [!TIP]
> Läs mer om Azure Policy effekter, inklusive distribuera om de inte finns i [förstå Azure policys effekter](../governance/policy/concepts/effects.md).


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Aktivera automatisk etablering av Log Analytics agent och tillägg <a name="auto-provision-mma"></a>

När automatisk etablering är aktiverat för Log Analytics agent distribuerar Security Center agenten på alla virtuella Azure-datorer som stöds och eventuella nya som skapats. En lista över plattformar som stöds finns [i plattformar som stöds i Azure Security Center](security-center-os-coverage.md).

Så här aktiverar du automatisk etablering av Log Analytics agent:

1. Från Security Center menyn väljer du **pris & inställningar**.
1. Välj relevant prenumeration.
1. På sidan **Automatisk etablering** ställer du in Log Analytics agentens status **på på**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Aktivera automatisk etablering av Log Analytics agenten":::

1. I fönstret konfigurations alternativ definierar du arbets ytan som ska användas.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Konfigurations alternativ för automatisk etablering Log Analytics agenter till virtuella datorer" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Anslut virtuella Azure-datorer till standard arbets ytorna som skapats av Security Center** -Security Center skapar en ny resurs grupp och standard arbets yta på samma plats och ansluter agenten till den arbets ytan. Om en prenumeration innehåller virtuella datorer från flera platser skapar Security Center flera arbets ytor för att säkerställa efterlevnaden av data sekretess krav.

        Namngivnings konventionen för arbets ytan och resurs gruppen är: 
        - Arbetsyta: DefaultWorkspace-[prenumerations-ID]-[geo] 
        - Resurs grupp: DefaultResourceGroup-[geo] 

        Security Center aktiverar automatiskt en Security Center-lösning på arbets ytan enligt den pris nivå som angetts för prenumerationen. 

        > [!TIP]
        > Frågor om standard arbets ytor finns i:
        >
        > - [Faktureras jag för Azure Monitor loggar på arbets ytorna som skapats av Security Center?](faq-data-collection-agents.md#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center)
        > - [Var skapas standard arbets ytan Log Analytics?](faq-data-collection-agents.md#where-is-the-default-log-analytics-workspace-created)
        > - [Kan jag ta bort standard arbets ytor som skapats av Security Center?](faq-data-collection-agents.md#can-i-delete-the-default-workspaces-created-by-security-center)

    - **Ansluta virtuella Azure-datorer till en annan arbets yta** – i list rutan väljer du den arbets yta där insamlade data ska lagras. List rutan innehåller alla arbets ytor i alla dina prenumerationer. Du kan använda det här alternativet om du vill samla in data från virtuella datorer som körs i olika prenumerationer och lagra dem på den valda arbets ytan.  

        Om du redan har en befintlig Log Analytics arbets yta kan du vilja använda samma arbets yta (kräver Läs-och Skriv behörighet på arbets ytan). Det här alternativet är användbart om du använder en central arbets yta i din organisation och vill använda den för säkerhets data insamling. Läs mer i [Hantera åtkomst till logg data och arbets ytor i Azure Monitor](../azure-monitor/logs/manage-access.md).

        Om den valda arbets ytan har redan en "Security"-eller "SecurityCenterFree"-lösning aktive rad, anges prissättningen automatiskt. Om inte, installerar du en Security Center-lösning på arbets ytan:

        1. Från Security Center menyn öppnar du **pris &s inställningar**.
        1. Välj den arbets yta som du vill ansluta agenterna till.
        1. Välj **Azure Defender på** eller **Azure Defender av**.

1. I konfigurationen för **Windows säkerhets händelser** väljer du mängden rå data som ska lagras:
    - **Ingen** – inaktivera lagring av säkerhets händelser. Det här är standardinställningen.
    - **Minimal** – en liten uppsättning händelser för när du vill minimera händelse volymen.
    - **Common** – en uppsättning händelser som uppfyller de flesta kunder och ger en fullständig Gransknings logg.
    - **Alla händelser** – för kunder som vill se till att alla händelser lagras.

    > [!TIP]
    > Information om hur du ställer in dessa alternativ på arbets ytans nivå finns i [ställa in säkerhets händelse på arbets ytans nivå](#setting-the-security-event-option-at-the-workspace-level).
    > 
    > Mer information om de här alternativen finns i [alternativ för Windows säkerhets händelse för den Log Analytics agenten](#data-collection-tier).

1. Välj **Använd** i konfigurations fönstret.

1. Aktivera automatisk etablering av ett tillägg som inte är Log Analytics-agenten: 

    1. Om du aktiverar automatisk etablering för Microsofts beroende agent kontrollerar du att Log Analytics-agenten är inställd på automatisk distribution.
    1. Växla statusen till **på** för det relevanta tillägget.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="Växla för att aktivera automatisk etablering för K8s policy-tillägg":::

    1. Välj **Spara**. Azure-principen är tilldelad och en reparations uppgift skapas.

        |Anknytning  |Policy  |
        |---------|---------|
        |Policytillägg för Kubernetes|[Distribuera Azure Policy-tillägg till Azure Kubernetes service-kluster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft-beroende agent (för hands version) (Windows VM)|[Distribuera beroende agent för virtuella Windows-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft-beroende agent (för hands version) (virtuella Linux-datorer)|[Distribuera beroende agent för virtuella Linux-datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |||

1. Välj **Spara**. Om en arbets yta behöver tillhandahållas kan Agent installationen ta upp till 25 minuter.

1. Du får frågan om du vill konfigurera om övervakade virtuella datorer som tidigare var anslutna till en standard arbets yta:

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="Granska alternativen för att konfigurera om övervakade virtuella datorer":::

    - **Nej** – dina nya inställningar för arbets ytan tillämpas bara på nyidentifierade virtuella datorer som inte har Log Analytics-agenten installerad.
    - **Ja** – dina nya inställningar för arbets ytan kommer att gälla för alla virtuella datorer och varje virtuell dator som är ansluten till en Security Center arbets yta som skapats på nytt kommer att återanslutas till den nya mål arbets ytan.

   > [!NOTE]
   > Om du väljer **Ja** tar du inte bort arbets ytorna som skapats av Security Center tills alla virtuella datorer har återanslutits till den nya mål arbets ytan. Den här åtgärden Miss lyckas om en arbets yta tas bort för tidigt.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Windows säkerhets händelse alternativ för Log Analytics agent <a name="data-collection-tier"></a> 

Om du väljer en data insamlings nivå i Azure Security Center påverkas endast *lagringen* av säkerhets händelser i Log Analytics-arbetsytan. Log Analytics-agenten samlar fortfarande in och analyserar de säkerhets händelser som krävs för Security Centers hot skydd, oavsett vilken nivå av säkerhets händelser du väljer att lagra i din arbets yta. Om du väljer att lagra säkerhets händelser kan du undersöka, söka och granska händelser i din arbets yta.

### <a name="requirements"></a>Krav 
Azure Defender krävs för att lagra Windows säkerhets händelse data. [Läs mer om Azure Defender](azure-defender.md).

Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

### <a name="information-for-azure-sentinel-users"></a>Information för Azure Sentinel-användare 
Användare av Azure Sentinel: Observera att insamling av säkerhets händelser inom kontexten för en enskild arbets yta kan konfigureras antingen från Azure Security Center eller Azure Sentinel, men inte med båda. Om du planerar att lägga till Azure Sentinel till en arbets yta som redan får aviseringar från Azure Security Center och är inställt på samla in säkerhets händelser, har du två alternativ:
- Lämna samlingen säkerhets händelser i Azure Security Center som den är. Du kan fråga efter och analysera dessa händelser i Azure Sentinel och i Azure Defender. Du kommer dock inte att kunna övervaka anslutningens anslutnings status eller ändra dess konfiguration i Azure Sentinel. Om detta är viktigt för dig bör du överväga det andra alternativet.
- Inaktivera insamling av säkerhets händelser i Azure Security Center (genom att ställa in **säkerhets händelser i Windows** på **ingen** i konfigurationen av din Log Analytics-agent). Lägg sedan till Connector Security Events i Azure Sentinel. Precis som med det första alternativet kan du fråga efter och analysera händelser i både Azure Sentinel och Azure Defender/ASC, men du kommer nu att kunna övervaka anslutnings status för anslutningen eller ändra dess konfiguration i och endast i Azure Sentinel.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>Vilka händelse typer lagras för "common" och "minimal"?
Dessa uppsättningar har utformats för att hantera typiska scenarier. Se till att utvärdera vilken som passar dina behov innan du implementerar den.

För att fastställa händelserna för **vanliga** och **minimala** alternativ fungerade vi med kunder och bransch standarder för att lära dig mer om den ofiltrerada frekvensen för varje händelse och deras användning. Vi använde följande rikt linjer i den här processen:

- **Minimal** – se till att den här uppsättningen bara omfattar händelser som kan tyda på en lyckad överträdelse och viktiga händelser som har en mycket låg volym. Den här uppsättningen innehåller till exempel användaren lyckade och misslyckade inloggningar (händelse-ID 4624, 4625), men det innehåller inte någon utloggning som är viktigt för granskning men inte meningsfullt för identifiering och har en relativt hög volym. Merparten av data volymen för den här uppsättningen är inloggnings händelser och händelse för att skapa process (händelse-ID 4688).
- **Common** – ange en fullständig Gransknings logg för användare i den här uppsättningen. Den här uppsättningen innehåller till exempel både användar inloggningar och användar inloggningar (händelse-ID 4634). Vi inkluderar gransknings åtgärder som säkerhets grupps ändringar, viktiga Kerberos-åtgärder för domänkontrollanter och andra händelser som rekommenderas av bransch organisationer.

Händelser som har mycket låg volym ingår i den gemensamma uppsättningen som huvud motivation att välja den över alla händelser är att minska volymen och inte filtrera bort vissa händelser.

Här är en fullständig analys av händelse-ID: n för säkerhet och app Locker för varje uppsättning:

| Datanivå | Insamlade händelse indikatorer |
| --- | --- |
| Minimal | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755,,,,,,, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697,,,,,, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771,,,,,, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902,,,,,, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272,,,,,, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Om du använder grupprincip objekt (GPO) rekommenderar vi att du aktiverar gransknings princip processen för att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Mer information om hur du skapar en process 4688 finns i [vanliga frågor om](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)Security Center. Mer information om de här gransknings principerna finns i [rekommendationer för gransknings principer](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Om du vill aktivera insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md)konfigurerar Security Center en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Detta gör att AppLocker genererar händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att Observera att den här principen inte kommer att konfigureras på datorer där det redan finns en konfigurerad AppLocker-princip. 
> - Om du vill samla in [händelse-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)för Windows Filtering Platform måste du aktivera [Granska filter plattforms anslutning](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>Ställer in säkerhets händelse alternativet på arbets ytans nivå

Du kan definiera nivån för säkerhets händelse data som ska lagras på arbets ytans nivå.

1. Från Security Center menyn i Azure Portal väljer du **pris & inställningar**.
1. Välj relevant arbets yta. De enda data insamlings händelserna för en arbets yta är de Windows-säkerhetshändelser som beskrivs på den här sidan.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="Ange säkerhets händelse data som ska lagras i en arbets yta":::

1. Välj mängden rå händelse data som ska lagras och välj **Spara**.

## <a name="manual-agent-provisioning"></a>Manuell agent etablering <a name="manual-agent"></a>
 
Så här installerar du Log Analytics agenten manuellt:

1. Inaktivera automatisk etablering.

1. Du kan också skapa en arbets yta.

1. Aktivera Azure Defender på den arbets yta där du installerar Log Analytics agenten:

    1. Från Security Center menyn väljer du **pris & inställningar**.

    1. Ange den arbets yta som du vill installera agenten på. Kontrol lera att arbets ytan finns i samma prenumeration som du använder i Security Center och att du har Läs-/Skriv behörighet för arbets ytan.

    1. Välj **Azure Defender på** och **Spara**.

       >[!NOTE]
       >Om arbets ytan redan har en **säkerhets** -eller **SecurityCenterFree** -lösning aktive rad anges prissättningen automatiskt. 

1. Om du vill distribuera agenter på nya virtuella datorer med en Resource Manager-mall installerar du Log Analytics-agenten:

   - [Installera Log Analytics agent för Windows](../virtual-machines/extensions/oms-windows.md)
   - [Installera Log Analytics agent för Linux](../virtual-machines/extensions/oms-linux.md)

1. Om du vill distribuera agenter på dina befintliga virtuella datorer följer du anvisningarna i [samla in data om Azure Virtual Machines](../azure-monitor/vm/quick-collect-azurevm.md) (avsnittet **samla in händelse-och prestanda data** är valfria).

1. Om du vill använda PowerShell för att distribuera agenterna kan du använda anvisningarna från Virtual Machines-dokumentationen:

    - [För Windows-datorer](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [För Linux-datorer](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> Instruktioner för hur du integrerar Security Center med hjälp av PowerShell finns i [Automatisera onboarding av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatisk etablering i fall av en redan befintlig agent installation <a name="preexisting"></a> 

Följande användnings fall anger hur automatisk etablering fungerar i fall när det redan finns en agent eller ett tillägg installerat. 

- **Log Analytics-agenten är installerad på datorn, men inte som ett tillägg (direkt agent)** – om Log Analytics agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg) kommer Security Center att installera Log Analytics agent-tillägget och uppgradera Log Analytics-agenten till den senaste versionen.
Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar även till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).
Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "säkerhet" eller "SecurityCenterFree" på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.

    För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.

    För befintliga datorer på prenumerationer som har registrerats på Security Center före 17 mars 2019, kommer Log Analytics agent tillägget inte att installeras, och datorn påverkas inte. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på de här datorerna.
  
- **System Center Operations Manager agenten är installerad på datorn** – Security Center kommer att installera Log Analytics agent-tillägget vid sidan av den befintliga Operations Manager. Den befintliga Operations Manager-agenten fortsätter att rapportera till Operations Manager servern på normalt sätt. Den Operations Manager agenten och Log Analytics agent delar vanliga kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen. Om Operations Manager agent version 2012 är installerad ska **du inte** aktivera automatisk etablering.

- **Det finns redan ett befintligt VM-tillägg**:
    - När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätter inte befintliga anslutningar till användar arbets ytor. Security Center kommer att lagra säkerhets data från den virtuella datorn i arbets ytan som redan är ansluten, förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.
    - Om du vill se vilken arbets yta som det befintliga tillägget skickar data till kör du testet för att [Verifiera anslutningen med Azure Security Center](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center). Du kan också öppna Log Analytics arbets ytor, välja en arbets yta, välja den virtuella datorn och titta på Log Analytics agent-anslutningen.
    - Om du har en miljö där Log Analytics-agenten är installerad på klient arbets stationer och rapporterar till en befintlig Log Analytics arbets yta, granskar du listan över [operativ system som stöds av Azure Security Center](security-center-os-coverage.md) för att kontrol lera att operativ systemet stöds. Mer information finns i [befintliga Log Analytics-kunder](./faq-azure-monitor-logs.md).
 

## <a name="disable-auto-provisioning"></a>Inaktivera automatisk etablering <a name="offprovisioning"></a>

När du inaktiverar automatisk etablering, tillhandahålls inte agenterna på nya virtuella datorer.

Så här inaktiverar du automatisk etablering av en agent:

1. Från Security Center menyn i portalen väljer du **pris & inställningar**.
1. Välj relevant prenumeration.
1. Välj **Automatisk etablering**.
1. Växla statusen till **av** för den relevanta agenten.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="Växlar för att inaktivera automatisk etablering per agent typ":::

1. Välj **Spara**. När automatisk etablering har inaktiverats visas inte avsnittet standard konfiguration av arbets yta:

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="När automatisk etablering har inaktiverats är konfigurations cellen tom":::


> [!NOTE]
>  Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten etablerades. Information om hur du tar bort OMS-tillägget finns i [Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center](faq-data-collection-agents.md#remove-oms).
>


## <a name="troubleshooting"></a>Felsökning

-   Information om hur du identifierar installations problem med automatisk etablering finns i [övervaka agentens hälso problem](security-center-troubleshooting-guide.md#mon-agent).
-  Information om hur du identifierar nätverks krav för övervaknings agenten finns i [Felsöka nätverks krav för övervaknings agenten](security-center-troubleshooting-guide.md#mon-network-req).
-   Information om hur du identifierar manuella onboarding-problem finns i [så här felsöker du problem med Operations Management Suite onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).



## <a name="next-steps"></a>Nästa steg
Den här sidan förklaras hur du aktiverar automatisk etablering för Log Analytics agent och andra Security Center tillägg. Det beskrivs också hur du definierar en Log Analytics arbets yta där insamlade data ska lagras. Båda åtgärderna krävs för att aktivera data insamling. Om du lagrar data i Log Analytics, oavsett om du använder en ny eller befintlig arbets yta, kan det uppstå fler avgifter för data lagring. Information om priser i din valuta och enligt din region finns [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/).