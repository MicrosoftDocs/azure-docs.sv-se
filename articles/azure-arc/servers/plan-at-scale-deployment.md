---
title: Planera och distribuera Azure Arc aktiverade servrar
description: Lär dig hur du gör det möjligt för ett stort antal datorer att Azure Arc-aktiverade servrar för att förenkla konfigurationen av viktiga funktioner för säkerhet, hantering och övervakning i Azure.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831986"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Planera och distribuera Arc-aktiverade servrar

Distribution av en IT-infrastrukturtjänst eller ett affärsprogram är en utmaning för alla företag. För att kunna köra det bra och undvika oväntade överraskningar och oplanerade kostnader måste du planera noggrant för det så att du är så redo som möjligt. Om du vill planera för Azure Arc-aktiverade servrar i valfri skala bör det omfatta de design- och distributionskriterier som måste uppfyllas för att slutföra uppgifterna.

För att distributionen ska gå smidigt bör planen upprätta en tydlig förståelse för:

* Roller och ansvarsområden.
* Inventering av fysiska servrar eller virtuella datorer för att verifiera att de uppfyller nätverks- och systemkraven.
* Den kompetensuppsättning och utbildning som krävs för att möjliggöra en lyckad distribution och hantering.
* Godkännandekriterier och hur du spårar dess framgång.
* Verktyg eller metoder som ska användas för att automatisera distributionerna.
* Identifierade risker och riskreduceringsplaner för att undvika fördröjningar, avbrott osv.
* Så här undviker du avbrott under distributionen.
* Vad är eskaleringsvägen när ett betydande problem uppstår?

Syftet med den här artikeln är att säkerställa att du är förberedd för en lyckad distribution av Azure Arc-aktiverade servrar över flera fysiska produktionsservrar eller virtuella datorer i din miljö.

## <a name="prerequisites"></a>Förutsättningar

* Datorerna kör ett operativsystem [som stöds för](agent-overview.md#supported-operating-systems) Connected Machine-agenten.
* Datorerna har anslutning från ditt lokala nätverk eller en annan molnmiljö till resurser i Azure, antingen direkt eller via en proxyserver.
* För att installera och konfigurera Arc-aktiverade servrar Connected Machine-agent, ett konto med förhöjd behörighet (det vill säga en administratör eller som rot) på datorerna.
* Om du vill publicera datorer är du medlem i **Azure Connected Machine Onboarding-rollen.**
* Om du vill läsa, ändra och ta bort en dator är du medlem i **Azure Connected Machine rollen Resursadministratör.**

## <a name="pilot"></a>Pilot

Innan du distribuerar till alla produktionsdatorer börjar du med att utvärdera distributionsprocessen innan du implementerar den brett i din miljö. För en pilot bör du identifiera ett representativt urval av datorer som inte är viktiga för företagets förmåga att utföra verksamheten. Du bör se till att ge tillräckligt med tid för att köra piloten och utvärdera dess påverkan: vi rekommenderar minst 30 dagar.

Upprätta en formell plan som beskriver pilotens omfattning och detaljer. Följande är ett exempel på vad en plan bör innehålla för att hjälpa dig att komma igång.

* Mål – Beskriver de affärs- och tekniska faktorer som ledde till beslutet att ett pilotprojekt är nödvändigt.
* Urvalskriterier – Anger de kriterier som används för att välja vilka aspekter av lösningen som ska demonstreras via ett pilottest.
* Omfång – Beskriver pilotens omfattning, inklusive men inte begränsat till lösningskomponenter, förväntat schema, pilotens varaktighet och antal datorer att rikta in sig på.
* Framgångskriterier och mått – Definiera pilotens framgångskriterier och specifika mått som används för att fastställa framgångsnivån.
* Utbildningsplan – Beskriver planen för att utbilda systemtekniker, administratörer osv. som är nybörjare på Azure och it-tjänster under pilottestet.
* Övergångsplan – Beskriver den strategi och de kriterier som används för att vägleda övergången från pilot till produktion.
* Återställning – Beskriver procedurerna för att återställa ett pilottest till ett fördistributionstillstånd.
* Risker – Lista alla identifierade risker för piloten och associerade med produktionsdistribution.

## <a name="phase-1-build-a-foundation"></a>Fas 1: Skapa en grund

I den här fasen gör systemtekniker eller administratörer det möjligt för kärnfunktionerna i organisationens Azure-prenumeration att starta grunden innan du aktiverar dina datorer för hantering av Arc-aktiverade servrar och andra Azure-tjänster.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| [Skapa en resursgrupp](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | En dedikerad resursgrupp för att endast inkludera Arc-aktiverade servrar och centralisera hantering och övervakning av dessa resurser. | En timme |
| Använd [taggar för](../../azure-resource-manager/management/tag-resources.md) att organisera datorer. | Utvärdera och utveckla en IT-anpassad [taggningsstrategi](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) som kan hjälpa dig att minska komplexiteten med att hantera dina Arc-aktiverade servrar och förenkla hanteringen av beslut. | En dag |
| Utforma och distribuera [Azure Monitor loggar](../../azure-monitor/logs/data-platform-logs.md) | Utvärdera [design- och distributionsöverväganden](../../azure-monitor/logs/design-logs-deployment.md) för att avgöra om din organisation ska använda en befintlig eller implementera en annan Log Analytics-arbetsyta för att lagra insamlade loggdata från hybridservrar och datorer. <sup>1</sup> | En dag |
| [Utveckla en Azure Policy styrningsplan](../../governance/policy/overview.md) | Bestäm hur du ska implementera styrning av hybridservrar och datorer i prenumerations- eller resursgruppsomfånget med Azure Policy. | En dag |
| Konfigurera [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) (RBAC) | Utveckla en åtkomstplan för att kontrollera vem som har åtkomst till att hantera Arc-aktiverade servrar och möjligheten att visa sina data från andra Azure-tjänster och -lösningar. | En dag |
| Identifiera datorer där Log Analytics-agenten redan är installerad | Kör följande loggfråga i [Log Analytics för att stödja](../../azure-monitor/logs/log-analytics-overview.md) konvertering av befintliga Log Analytics-agentdistributioner till tilläggs hanterad agent:<br> Pulsslag <br> &#124; där TimeGenerated > ago(30d) <br> &#124; där ResourceType == "machines" och (ComputerEnvironment == "Non-Azure") <br> &#124; summarize by Computer, ResourceProvider, ResourceType, ComputerEnvironment | En timme |

<sup>1</sup> Ett viktigt övervägande när du utvärderar utformningen av Log Analytics-arbetsytan är integrering med Azure Automation som stöd för dess Uppdateringshantering- och Ändringsspårning och inventering-funktion samt Azure Security Center och Azure Sentinel. Om din organisation redan har ett Automation-konto och aktiverat dess hanteringsfunktioner som är länkade till en Log Analytics-arbetsyta kan du utvärdera om du kan centralisera och effektivisera hanteringsåtgärder, samt minimera kostnaderna genom att använda de befintliga resurserna jämfört med att skapa ett duplicerat konto, en arbetsyta osv.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fas 2: Distribuera Arc-aktiverade servrar

Därefter lägger vi till grunden i fas 1 genom att förbereda för och distribuera Arc-aktiverade servrar Connected Machine-agent.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| Ladda ned det fördefinierade installationsskriptet | Granska och anpassa det fördefinierade installationsskriptet för storskalig distribution av Connected Machine-agenten för att stödja dina automatiserade distributionskrav.<br><br> Exempel på storskaliga onboarding-resurser:<br><br> <ul><li> [Grundläggande distributionsskript i stor skala](onboard-service-principal.md)</ul></li> <ul><li>[Registrering i stor skala VMware vSphere virtuella Windows Server-datorer](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Registrering i stor skala VMware vSphere virtuella Linux-datorer](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Registrering av AWS EC2-instanser i stor skala med Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Skalbar distribution med PowerShell-fjärrkommunikation](./onboard-powershell.md) (endast Windows)</ul></li>| En eller flera dagar beroende på krav, organisationsprocesser (till exempel ändrings- och versionshantering) och automatiseringsmetod som används. |
| [Skapa tjänstens huvudnamn](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Skapa ett huvudnamn för tjänsten för att ansluta datorer icke-interaktivt Azure PowerShell eller från portalen.| En timme |
| Distribuera connected machine-agenten till målservrarna och datorerna |Använd ditt automatiseringsverktyg för att distribuera skripten till dina servrar och ansluta dem till Azure.| En eller flera dagar beroende på din versionsplan och om du följer en fasfasad lansering. |

## <a name="phase-3-manage-and-operate"></a>Fas 3: Hantera och arbeta

Fas 3 ser administratörer eller systemtekniker som möjliggör automatisering av manuella uppgifter för att hantera och använda connected machine-agenten och datorn under livscykeln.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
|Skapa en Resource Health avisering |Om en server slutar skicka pulsslag till Azure i mer än 15 minuter kan det betyda att den är offline, att nätverksanslutningen har blockerats eller att agenten inte körs. Utveckla en plan för hur du ska reagera och undersöka dessa incidenter och använd [Resource Health aviseringar](../..//service-health/resource-health-alert-monitor-guide.md) för att få aviseringar när de startar.<br><br> Ange följande när du konfigurerar aviseringen:<br> **Resurstyp**  =  **Azure Arc aktiverade servrar**<br> **Aktuell resursstatus**  =  **Ej tillgänglig**<br> **Tidigare resursstatus**  =  **Tillgänglig** | En timme |
|Skapa en Azure Advisor avisering | För bästa möjliga upplevelse och de senaste säkerhets- och felkorrigeringarna rekommenderar vi att Azure Arc-aktiverade serveragenten är uppdaterad. In-of-date-agenter identifieras med en [Azure Advisor avisering](../../advisor/advisor-alerts-portal.md).<br><br> Ange följande när du konfigurerar aviseringen:<br> **Rekommendationstyp**  =  **Uppgradera till den senaste versionen av Azure Connected Machine Agent** | En timme |
|[Tilldela Azure-principer](../../governance/policy/assign-policy-portal.md) till din prenumeration eller resursgruppsomfång |Tilldela principen **Aktivera Azure Monitor for VMs** [(och](../../azure-monitor/vm/vminsights-enable-policy.md) andra som uppfyller dina behov) till prenumerations- eller resursgruppsomfånget. Azure Policy kan du tilldela principdefinitioner som installerar de agenter som krävs för VM-insikter i din miljö.| Det varierar |
|[Aktivera Uppdateringshantering arc-aktiverade servrar](../../automation/update-management/enable-from-automation-account.md) |Konfigurera Uppdateringshantering i Azure Automation att hantera uppdateringar av operativsystemet för dina virtuella Windows- och Linux-datorer som är registrerade med Arc-aktiverade servrar. | 15 minuter |

## <a name="next-steps"></a>Nästa steg

* Felsökningsinformation finns i guiden Felsöka [Connected Machine-agenten.](troubleshoot-agent-onboard.md)

* Lär dig hur du förenklar distributionen med andra Azure-tjänster som Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) och andra Azure [VM-tillägg som stöds.](manage-vm-extensions.md)