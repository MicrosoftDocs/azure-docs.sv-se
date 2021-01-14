---
title: Använd program ändrings analys i Azure Monitor för att hitta problem med webb program | Microsoft Docs
description: Använd program ändrings analys i Azure Monitor för att felsöka program problem på Live-webbplatser på Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 728fd8f4705d24f719b6dd47ba88d89fb399fd5a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195882"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Använda program ändrings analys (för hands version) i Azure Monitor

När ett problem med en aktiv webbplats eller ett avbrott inträffar kan du snabbt avgöra rotor saken är kritiskt. Vanliga övervaknings lösningar kan varna dig för problem. De kan till och med Visa vilken komponent som Miss fungerar. Men aviseringen kan inte alltid direkt förklara orsaken till felet. Du vet att webbplatsen arbetade fem minuter sedan och att den nu är bruten. Vad har ändrats under de senaste fem minuterna? Detta är den fråga som program ändrings analysen har utformats för att svara i Azure Monitor.

Genom att bygga vidare på kraften i [Azures resurs diagram](../../governance/resource-graph/overview.md)får du insikter om dina Azure-programändringar för att öka den observerade och minska MTTR (genomsnittlig tid för reparation).

> [!IMPORTANT]
> Ändrings analys är för närvarande en för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal. Den här versionen rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsade funktioner. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="overview"></a>Översikt

Ändrings analysen identifierar olika typer av ändringar, från infrastruktur skiktet hela vägen till program distributionen. Det är en Azure-adressresurs på prenumerations nivå som kontrollerar resurs ändringar i prenumerationen. Med ändrings analys får du data för olika diagnostiska verktyg som hjälper användarna att förstå vilka ändringar som kan ha orsakat problem.

Följande diagram illustrerar arkitekturen för ändrings analys:

![Arkitektur diagram över hur ändrings analys får ändra data och ger den till klient verktyg](./media/change-analysis/overview.png)

## <a name="data-sources"></a>Datakällor

Frågor om program ändrings analys för Azure Resource Manager spårade egenskaper, proxy-konfigurationer och webbappar i gäst ändringar. Dessutom spårar tjänsten resurs beroende ändringar för att diagnostisera och övervaka ett program från slut punkt till slut punkt.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Ändringar i Azure Resource Manager spårade egenskaper

Med hjälp av [Azure Resource Graph](../../governance/resource-graph/overview.md)får du en historisk förteckning över hur Azure-resurser som är värdar för ditt program har ändrats över tid. Spårade inställningar, till exempel hanterade identiteter, plattforms-OS-uppgradering och värdnamn kan identifieras.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Inställnings ändringar för Azure Resource Manager-proxy

Inställningar som IP-konfigurationsfil, TLS-inställningar och tilläggs versioner är ännu inte tillgängliga i Azures resurs diagram, så du kan ändra analys frågor och beräkna dessa ändringar på ett säkert sätt för att ge mer information om vad som har ändrats i appen.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Ändringar i distribution och konfiguration av webbappar (ändringar i gästen)

Med ändrings analys samlas distributions-och konfigurations status för ett program var fjärde timme. Den kan till exempel identifiera ändringar i program miljöns variabler. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från i Resource Manager-ändringar kanske kod distributionens ändrings information inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändrings analysen väljer du **Uppdatera**.

![Skärm bild av knappen "Skanna ändringar nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar i resurs beroenden kan också orsaka problem i en webbapp. Om en webbapp till exempel anropar en Redis cache kan Redis cache-SKU: n påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar ändrings analysen webbappens DNS-post. På så sätt identifieras ändringar i alla app-komponenter som kan orsaka problem.
För närvarande stöds följande beroenden:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="application-change-analysis-service"></a>Analys tjänst för program ändring

Beräknings-och agg regeringar för program ändrings analys gör ändrings data från de data källor som anges ovan. Den innehåller en uppsättning analyser för användare som enkelt kan navigera bland alla resurs ändringar och identifiera vilken ändring som är relevant i fel söknings-eller övervaknings kontexten.
Resurs leverantören Microsoft. ChangeAnalysis måste registreras med en prenumeration för Azure Resource Manager spårade egenskaper och proxy-inställningar ändra att data ska vara tillgängliga. När du anger ett verktyg för att diagnostisera och lösa problem, eller ta fram fliken för att skapa ändrings analyser, registreras denna resurs leverantör automatiskt. Det finns inga prestanda-eller kostnads implementeringar för din prenumeration. När du aktiverar ändrings analys för webbappar (eller aktiverar verktyget diagnostisera och lösa problem) har den försumbar prestanda påverkan på webbappen och ingen fakturerings kostnad.
För att webbappen ska ändras i gästen krävs separat aktivering för att genomsöka filer i en webbapp. Mer information finns i avsnittet om att [ändra analyser i verktyget diagnostisera och lösa problem](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) längre fram i den här artikeln.

## <a name="visualizations-for-application-change-analysis"></a>Visualiseringar för program ändrings analys

### <a name="standalone-ui"></a>Fristående gränssnitt

I Azure Monitor finns det ett fristående fönster för ändrings analys som visar alla ändringar med insikter i program beroenden och resurser.

Sök efter ändrings analys i Sök fältet på Azure Portal för att starta upplevelsen.

![Skärm bild av sökning av ändrings analys i Azure Portal](./media/change-analysis/search-change-analysis.png)

Alla resurser under en vald prenumeration visas med ändringar från de senaste 24 timmarna. För att optimera prestanda för sid inläsning visar tjänsten 10 resurser i taget. Klicka på nästa sidor om du vill visa fler resurser. Vi arbetar med att ta bort den här begränsningen.

![Skärm bild av bladet för ändrings analys i Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Visa alla ändringar genom att klicka på en resurs. Vid behov kan du öka detalj nivån i en ändring om du vill visa JSON-formaterad ändrings information och insikter.

![Skärm bild av ändrings information](./media/change-analysis/change-details.png)

Använd knappen Skicka feedback i bladet eller e-postmeddelandet för all feedback changeanalysisteam@microsoft.com .

![Skärm bild av knappen feedback i bladet ändra analys](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Webbappen diagnostisera och lösa problem

I Azure Monitor är även ändrings analys inbyggd i självbetjänings **diagnos och lösa problem** . Få åtkomst till den här upplevelsen från sidan **Översikt** i ditt App Service-program.

![Skärm bild av knappen "Översikt" och "diagnosticera och lösa problem"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Program ändrings analys i verktyget diagnostisera och lösa problem

Program ändrings analys är en fristående detektor i webbappen diagnostisera och lösa problem verktyg. Den sammanställs också i **program krascher** och i **webb program**. När du anger verktyget diagnostisera och lösa problem registreras **Microsoft. ChangeAnalysis** Resource Provider automatiskt. Följ de här instruktionerna för att aktivera webb program i gäst ändrings spårning.

1. Välj **tillgänglighet och prestanda**.

    ![Skärm bild av fel söknings alternativen "tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

2. Välj **program ändringar**. Funktionen är också tillgänglig i **program krascher**.

   ![Skärm bild av knappen "program krascher"](./media/change-analysis/application-changes.png)

3. Länken leder till program ändring Aalysis-gränssnittet som är begränsat till webbappen. Om webb program i gäst ändrings spårning inte är aktive rad, följer du banderollen för att hämta ändringar av fil-och app-inställningar.

   ![Skärm bild av alternativen för "program krascher"](./media/change-analysis/enable-changeanalysis.png)

4. Aktivera **ändrings analys** och välj **Spara**. Verktyget visar alla webbappar under en App Service plan. Du kan använda plan nivå växeln för att aktivera ändrings analyser för alla webbappar under en plan.

    ![Skärm bild av användar gränssnittet "Aktivera ändrings analys"](./media/change-analysis/change-analysis-on.png)

5. Ändrings data finns också i Välj identifierings program för **webbappar ned** och **programkrascher** . Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om dessa ändringar. Som standard visas ändringar under de senaste 24 timmarna för att hjälpa till med omedelbara problem.

     ![Skärm bild av vyn ändra diff](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostisera och lösa problem med den virtuella datorn

Gå till diagnostisera och lösa problem verktyg för en virtuell dator.  Gå till **fel söknings verktyg**, bläddra nedåt på sidan och välj **analysera nya ändringar** för att visa ändringar på den virtuella datorn.

![Skärm bild av den virtuella datorn diagnostisera och lösa problem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Ändrings analys i fel söknings verktyg](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Ändrings historik för aktivitets logg
Funktionen [Visa ändrings historik](../platform/activity-log.md#view-change-history) i aktivitets loggen anropar program ändrings analys tjänstens Server del för att få ändringar som är associerade med en åtgärd. **Ändrings historik** som används för att anropa [Azures resurs diagram](../../governance/resource-graph/overview.md) direkt, men utbytt Server delen för att anropa program ändrings analys så att ändringar som returneras inkluderar resurs nivå ändringar från [Azure Resource graph](../../governance/resource-graph/overview.md), resurs egenskaper från [Azure Resource Manager](../../azure-resource-manager/management/overview.md)och ändringar i gästen från PaaS-tjänster som app Services webbappen. För att program ändrings analys tjänsten ska kunna söka efter ändringar i användarnas prenumerationer måste du registrera en resurs leverantör. Första gången du öppnar fliken **ändrings historik** börjar verktyget automatiskt att registrera **Microsoft. ChangeAnalysis** Resource Provider. Efter registreringen kommer ändringar från **Azure Resource Graph** att bli tillgängliga omedelbart och de senaste 14 dagarna. Ändringar från andra källor blir tillgängliga efter ~ 4 timmar efter det att prenumerationen har publicerats.

![Integration av aktivitets loggens ändrings historik](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>Integrering av VM Insights
Användare med aktiverade [VM-insikter](../insights/vminsights-overview.md) kan se vad som har ändrats på sina virtuella datorer, vilket kan orsaka eventuella toppar i ett mått diagram, till exempel CPU eller minne, och undrar vad som orsakade det. Ändrings data integreras i navigerings fältet för VM Insights-sidan. Användaren kan visa om några ändringar har gjorts i den virtuella datorn och klicka på **Undersök ändringar** för att Visa ändrings information i det fristående användar gränssnittet för program ändrings analys.

[![Integrering av VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>Aktivera ändrings analys i skala

Om din prenumeration innehåller flera webbappar är det inte effektivt att aktivera tjänsten på nivån för webbappen. Kör följande skript för att aktivera alla webb program i din prenumeration.

Förutsättningar:

- PowerShell-modulen AZ. Följ anvisningarna i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps)

Kör följande skript:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="troubleshoot"></a>Felsöka

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>Om du har problem med att registrera Microsoft. ändra Analysis Resource provider från fliken ändrings historik
Om det är första gången du visar ändrings historik efter dess integrering med program ändrings analys, kommer den automatiskt att registrera en resurs leverantör **Microsoft. ChangeAnalysis**. I sällsynta fall kan det Miss kan inträffa av följande orsaker:

- **Du har inte tillräcklig behörighet för att registrera Microsoft. ChangeAnalysis Resource Provider**. Det här fel meddelandet innebär att din roll i den aktuella prenumerationen inte har det definitions område för **Microsoft. support/register/åtgärd** som är associerat med den. Detta kan inträffa om du inte är ägare till en prenumeration och har delade åtkomst behörigheter via en medarbetare. t. ex. Visa åtkomst till en resurs grupp. Du kan åtgärda detta genom att kontakta Prenumerationens ägare för att registrera **Microsoft. ChangeAnalysis** -resurs leverantören. Detta kan göras i Azure Portal via **prenumerationer | Resurs leverantörer** och Sök efter ```Microsoft.ChangeAnalysis``` och registrera i användar gränssnittet, eller via Azure PowerShell eller Azure CLI.

    Registrera resurs leverantör via PowerShell: 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Det gick inte att registrera Microsoft. ChangeAnalysis Resource Provider**. Det här meddelandet innebär att något Miss lyckas omedelbart som UI skickat begäran om att registrera resurs leverantören, och det är inte relaterat till behörighets problem. Det kan förmodligen vara ett tillfälligt problem med Internet anslutningen. Försök att uppdatera sidan och kontrol lera din Internet anslutning. Om felet kvarstår kan du kontakta changeanalysishelp@microsoft.com

- **Det tar längre tid än förväntat**. Det här meddelandet innebär att registreringen tar längre tid än två minuter. Detta är ovanligt, men det innebär inte nödvändigt vis något att något har gått fel. Du kan gå till **prenumerationer | Resurs leverantör** för att kontrol lera registrerings statusen för **Microsoft. ChangeAnalysis** Resource Provider. Du kan prova att använda användar gränssnittet för att avregistrera, registrera om eller uppdatera för att se om det hjälper. Kontakta supporten om problemet kvarstår changeanalysishelp@microsoft.com .
    ![Felsök RP-registreringen tar för lång tid](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Skärm bild av verktyget diagnostisera och lösa problem för en virtuell dator med fel söknings verktyg valt.](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Skärm bild av panelen för att analysera nya ändringar av fel söknings verktyget för en virtuell dator.](./media/change-analysis/analyze-recent-changes.png)

### <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse-prenumeration stöds inte

- **Det gick inte att fråga Microsoft. ChangeAnalysis Resource Provider** med meddelande *Azure Lighthouse-prenumerationen stöds inte. ändringarna är bara tillgängliga i prenumerationens hem klient organisation*. Det finns en begränsning just nu för att kunna registrera Analysis Resource Provider via Azure Lighthouse-prenumerationen för användare som inte finns i hem klient organisationen. Vi förväntar sig att den här begränsningen ska åtgärdas inom en snar framtid. Om detta är ett blockerings problem finns det en lösning som inbegriper att skapa ett huvud namn för tjänsten och uttryckligen tilldela rollen för att tillåta åtkomst.  Kontakta changeanalysishelp@microsoft.com om du vill veta mer om det.

### <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Ett fel uppstod vid hämtning av ändringar. Uppdatera den här sidan eller kom tillbaka senare om du vill visa ändringar

Detta är det allmänna fel meddelandet som presenteras av program ändrings analys tjänsten när det inte gick att läsa in ändringar. Några kända orsaker är:
- Internet anslutnings fel från klient enheten
- Change Analysis Service är tillfälligt otillgängligt om du uppdaterar sidan efter några minuter åtgärdar det här problemet vanligt vis. Om felet kvarstår kan du kontakta changeanalysishelp@micorosoft.com

### <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Du har inte tillräcklig behörighet för att visa vissa ändringar. Kontakta administratören för Azure-prenumerationen

Detta är det allmänna obehöriga fel meddelandet, som förklarar att den aktuella användaren inte har tillräcklig behörighet för att visa ändringen. Åtkomst till minst läsare krävs för resursen för att Visa infrastruktur ändringar som returneras av Azure Resource Graph och Azure Resource Manager. För webb program ändringar i gäst filen och konfigurations ändringar krävs minst deltagar roll.

### <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Det gick inte att registrera Microsoft. ChangeAnalysis Resource Provider
Det här meddelandet innebär att något Miss lyckas omedelbart som UI skickat begäran om att registrera resurs leverantören, och det är inte relaterat till behörighets problem. Det kan förmodligen vara ett tillfälligt problem med Internet anslutningen. Försök att uppdatera sidan och kontrol lera din Internet anslutning. Om felet kvarstår kan du kontakta changeanalysishelp@microsoft.com
 
### <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Du har inte tillräcklig behörighet för att registrera Microsoft. ChangeAnalysis Resource Provider. Kontakta administratören för Azure-prenumerationen.
Det här fel meddelandet innebär att din roll i den aktuella prenumerationen inte har det definitions område för **Microsoft. support/register/åtgärd** som är associerat med den. Detta kan inträffa om du inte är ägare till en prenumeration och har delade åtkomst behörigheter via en medarbetare. t. ex. Visa åtkomst till en resurs grupp. Du kan åtgärda detta genom att kontakta Prenumerationens ägare för att registrera **Microsoft. ChangeAnalysis** -resurs leverantören. Detta kan göras i Azure Portal via **prenumerationer | Resurs leverantörer** och Sök efter ```Microsoft.ChangeAnalysis``` och registrera i användar gränssnittet, eller via Azure PowerShell eller Azure CLI.

Registrera resurs leverantör via PowerShell: 

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Nästa steg

- Aktivera Application Insights för [Azure App Services-appar](azure-web-apps.md).
- Aktivera Application Insights för [virtuella Azure-datorer och skalnings uppsättningar för IIS-värdbaserade appar i Azure](azure-vm-vmss-apps.md).
- Lär dig mer om [Azure Resource Graph](../../governance/resource-graph/overview.md), som hjälper dig att analysera energi ändringar.