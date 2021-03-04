---
title: Planera för en storskalig distribution av Azure Arc-aktiverade servrar
description: Lär dig hur du aktiverar ett stort antal datorer till Azure Arc-aktiverade servrar för att förenkla konfigurationen av viktiga säkerhets-, hanterings-och övervaknings funktioner i Azure.
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: 0e77fc00f94f2f46c60bb2c5dcecc10a4e2e3bc5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032236"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>Planera för en storskalig distribution av Azure Arc-aktiverade servrar

Distribution av en IT-infrastruktur eller ett affärs program är en utmaning för alla företag. För att kunna köra den väl och undvika eventuella oväntade överraskningar och oplanerade kostnader måste du noggrant planera för det för att säkerställa att du är så klar som möjligt. Om du vill planera för distribution av Azure Arc-aktiverade servrar i stor skala bör det täcka de design-och distributions kriterier som måste uppfyllas för att slutföra aktiviteterna för att stödja en storskalig distribution.

För att distributionen ska fortsätta smidigt bör ditt abonnemang skapa en tydlig förståelse av:

* Roller och ansvars områden.
* Inventering av fysiska servrar eller virtuella datorer för att kontrol lera att de uppfyller nätverks-och system kraven.
* Den kunskaps uppsättning och utbildning som krävs för att aktivera lyckad distribution och pågående hantering.
* Godkännande kriterier och hur du spårar dess framgång.
* Verktyg eller metoder som ska användas för att automatisera distributioner.
* Identifierade risker och minsknings planer för att undvika fördröjningar, avbrott osv.
* Undvik avbrott under distributionen.
* Vad är eskaleringen när ett stort problem uppstår?

Syftet med den här artikeln är att se till att du är för beredd för en lyckad distribution av Azure Arc-aktiverade servrar över flera fysiska produktions servrar eller virtuella datorer i din miljö.

## <a name="prerequisites"></a>Förutsättningar

* Datorerna kör ett [operativ system som stöds](agent-overview.md#supported-operating-systems) för den anslutna dator agenten.
* Datorerna har anslutning från ditt lokala nätverk eller en annan moln miljö till resurser i Azure, antingen direkt eller via en proxyserver.
* För att installera och konfigurera Arc-aktiverade servrar som är anslutna till dator agenten, ett konto med förhöjd behörighet (dvs. administratör eller som rot) på datorerna.
* För att publicera datorer är du medlem i rollen **Azure Connected Machine onboarding** .
* Om du vill läsa, ändra och ta bort en dator är du medlem i rollen **resurs administratör för Azure Connected Machine** .

## <a name="pilot"></a>Pilot

Innan du distribuerar till alla produktions datorer börjar du med att utvärdera den här distributions processen innan du inför den i stor miljö. För en pilot kan du identifiera en representativ sampling av datorer som inte är viktiga för dina företag som kan driva verksamhet. Du bör vara säker på att få tillräckligt med tid för att köra piloten och bedöma dess påverkan: Vi rekommenderar minst 30 dagar.

Upprätta en formell plan som beskriver omfattningen och detaljerna i piloten. Följande är ett exempel på hur en plan bör omfatta för att hjälpa dig att komma igång.

* Mål – beskriver de affärs-och teknik driv rutiner som ledde till beslutet om att en pilot är nödvändig.
* Urvalskriterier – anger de kriterier som används för att välja vilka aspekter av lösningen som visas via en pilot.
* Omfattning – beskriver omfånget för piloten, som innehåller men inte begränsat till lösnings komponenter, förväntat schema, varaktighet för piloten och antalet datorer som ska riktas mot målet.
* Framgångs kriterier och mått – definiera pilotens lyckade kriterier och de åtgärder som används för att fastställa nivån på framgång.
* Utbildnings plan – beskriver planen för utbildning av system tekniker, administratörer osv. som är nya för Azure och IT-tjänster under piloten.
* Över gångs plan – beskriver strategin och kriterierna som används för att vägleda över gången från pilot till produktion.
* Ångring – beskriver procedurerna för att återställa en pilot till för distributions tillstånd.
* Risker – visar alla identifierade risker för att genomföra piloten och kopplad till produktions distributionen.

## <a name="phase-1-build-a-foundation"></a>Fas 1: bygga en grund

I den här fasen aktiverar system tekniker eller administratörer de grundläggande funktionerna i organisationens organisations Azure-prenumeration för att starta stiftelsen innan du aktiverar datorerna för hantering av Arc-aktiverade servrar och andra Azure-tjänster.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| [Skapa en resurs grupp](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | En dedikerad resurs grupp som endast ska innehålla Arc-aktiverade servrar och centralisera hanteringen och övervakning av dessa resurser. | En timme |
| Använd [taggar](../../azure-resource-manager/management/tag-resources.md) för att organisera datorer. | Utvärdera och utveckla en anpassad [taggnings strategi](/cloud-adoption-framework/decision-guides/resource-tagging/) som kan hjälpa till att minska komplexiteten med att hantera dina Arc-aktiverade servrar och förenkla fatta beslut om hantering. | En dag |
| Utforma och distribuera [Azure Monitor loggar](../../azure-monitor/logs/data-platform-logs.md) | Utvärdera [design-och distributions överväganden](../../azure-monitor/logs/design-logs-deployment.md) för att avgöra om din organisation ska använda en befintlig eller implementera en annan Log Analytics arbets yta för att lagra insamlade loggdata från hybrid servrar och datorer. <sup>1</sup> | En dag |
| [Utveckla en Azure policy](../../governance/policy/overview.md) styrnings plan | Bestäm hur du ska implementera styrning av hybrid servrar och datorer i prenumerations-eller resurs grupps omfånget med Azure Policy. | En dag |
| Konfigurera [rollbaserad åtkomst kontroll](../../role-based-access-control/overview.md) (RBAC) | Utveckla en åtkomst plan för att kontrol lera vem som har åtkomst till hantering av Arc-aktiverade servrar och möjlighet att visa data från andra Azure-tjänster och-lösningar. | En dag |
| Identifiera datorer med Log Analytics agent redan installerad | Kör följande logg fråga i [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) för att stödja konvertering av befintliga distributioner av Log Analytics-agent till en tilläggs hanterad agent:<br> Pulsslag <br> &#124; där TimeGenerated > sedan (30d) <br> &#124; där ResourceType = = "Machines" och (ComputerEnvironment = = "icke-Azure") <br> &#124; sammanfatta per dator, ResourceProvider, ResourceType, ComputerEnvironment | En timme |

<sup>1</sup> en viktig roll när du utvärderar din Log Analytics ytans design, är integration med Azure Automation som stöd för dess uppdateringshantering-och ändringsspårning-och inventerings funktion samt Azure Security Center och Azure Sentinel. Om din organisation redan har ett Automation-konto och har aktiverat dess hanterings funktioner som är kopplade till en Log Analytics arbets yta, kan du utvärdera om du kan centralisera och effektivisera hanterings åtgärder, samt minimera kostnaderna genom att använda dessa befintliga resurser och skapa ett duplicerat konto, en arbets yta osv.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fas 2: Distribuera Arc-aktiverade servrar

Därefter lägger vi till den grund som anges i fas 1 genom att förbereda distributionen och utföra installationen av agenten.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| Hämta det fördefinierade installations skriptet | Granska och anpassa det fördefinierade installations skriptet för distribution av den anslutna dator agenten för att stödja de automatiserade distributions kraven.<br><br> Exempel på för inskalning av onboarding-resurser:<br><br> <ul><li> [Skript för grundläggande distribution i skala](onboard-service-principal.md)</ul></li> <ul><li>[Onboarding-VMware vSphere virtuella Windows Server-datorer](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[Onboarding-VMware vSphere virtuella Linux-datorer](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[Vid skalning av onboarding AWS EC2-instanser med Ansible](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[Vid skalnings distribution med PowerShell-fjärrkommunikation](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (endast Windows)</ul></li>| En eller flera dagar beroende på kraven, organisatoriska processer (till exempel hantering av ändringar och utgåvor) och vilken Automation-metod som används. |
| [Skapa tjänstens huvudnamn](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Skapa ett huvud namn för tjänsten för att ansluta datorer icke-interaktivt med hjälp av Azure PowerShell eller från portalen.| En timme |
| Distribuera den anslutna dator agenten till mål servrarna och datorerna |Använd Automation-verktyget för att distribuera skripten till dina servrar och ansluta dem till Azure.| En eller flera dagar beroende på din versions plan och efter en stegvis distribution. |

## <a name="phase-3-manage-and-operate"></a>Fas 3: hantera och arbeta

Fas 3 ser administratörer eller system tekniker som möjliggör automatisering av manuella uppgifter för att hantera och använda den anslutna dator agenten och datorn under deras livs cykel.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
|Skapa en Resource Health-avisering |Om en server slutar att skicka pulsslag till Azure under mer än 15 minuter, kan det betyda att den är offline, att nätverks anslutningen har blockerats eller att agenten inte körs. Utveckla en plan för hur du ska svara på och undersöka dessa incidenter och Använd [Resource Health aviseringar](../..//service-health/resource-health-alert-monitor-guide.md) för att få ett meddelande när de startar.<br><br> Ange följande när du konfigurerar aviseringen:<br> **Resurs typ**  =  **Azure Arc-aktiverade servrar**<br> **Aktuell resurs status**  =  **Inte tillgänglig**<br> Status för tidigare **resurs**  =  **Tillgänglig** | En timme |
|Skapa en Azure Advisor-avisering | För bästa möjliga upplevelse och de senaste säkerhets-och fel korrigeringarna rekommenderar vi att du håller Azure Arc-aktiverade servrar uppdaterade. Inaktuella agenter kommer att identifieras med en [Azure Advisor-avisering](../../advisor/advisor-alerts-portal.md).<br><br> Ange följande när du konfigurerar aviseringen:<br> **Rekommendations typ**  =  **Uppgradera till den senaste versionen av Azure Connected Machine agent** | En timme |
|[Tilldela Azure-principer](../../governance/policy/assign-policy-portal.md) till din prenumeration eller resurs grupps omfång |Tilldela [principen](../../azure-monitor/vm/vminsights-enable-policy.md) för att **Aktivera Azure Monitor for VMS** (och andra som uppfyller dina behov) till prenumerations-eller resurs grupps omfånget. Med Azure Policy kan du tilldela princip definitioner som installerar de agenter som krävs för Azure Monitor for VMs i din miljö.| Det varierar |
|[Aktivera Uppdateringshantering för dina Arc-aktiverade servrar](../../automation/update-management/enable-from-automation-account.md) |Konfigurera Uppdateringshantering i Azure Automation för att hantera operativ system uppdateringar för virtuella Windows-och Linux-datorer som är registrerade med ARC-aktiverade servrar. | 15 minuter |

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du fören klar distributionen med andra Azure-tjänster som Azure Automation [tillstånds konfiguration](../../automation/automation-dsc-overview.md) och andra [Azure VM-tillägg](manage-vm-extensions.md)som stöds.