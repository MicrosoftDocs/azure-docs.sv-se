---
title: Använd Övervakare för övervakning av virtuella Windows-datorer – Azure
description: Använda Azure Monitor för virtuella Windows-datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e9da1071686dafa003a5a49d0864b77644493344
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594458"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Använd Azure Monitor för virtuella Windows-datorer för att övervaka distributionen (för hands version)

>[!IMPORTANT]
>Azure Monitor för det virtuella Windows-skrivbordet är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor för Windows Virtual Desktop (för hands version) är en instrument panel som bygger på Azure Monitor arbets böcker som hjälper IT-proffs att förstå sina Windows-miljöer för virtuella datorer. Det här avsnittet beskriver hur du konfigurerar Azure Monitor för virtuella Windows-datorer för att övervaka dina Windows-miljöer för virtuella skriv bord.

## <a name="requirements"></a>Krav

Innan du börjar använda Azure Monitor för virtuella Windows-datorer måste du konfigurera följande saker:

- Alla Windows-miljöer för virtuella skriv bord som du övervakar måste vara baserade på den senaste versionen av Windows Virtual Desktop som är kompatibel med Azure Resource Manager.

- Minst en konfigurerad Log Analytics-arbetsyta.

- Aktivera data insamling för följande saker i din Log Analytics-arbetsyta:
    - Alla nödvändiga prestanda räknare
    - Prestanda räknare eller händelser som används i Azure Monitor för virtuella Windows-datorer
    - Data från diagnostikverktyget för alla objekt i miljön som du övervakar.
    - Virtuella datorer (VM) i den miljö som du ska övervaka.

Alla som övervakar Azure Monitor för virtuella Windows-datorer för din miljö behöver också följande Läs behörighet:

- Läs behörighet till resurs gruppen där miljöns resurser finns.

- Läs behörighet till de resurs grupper där miljöns sessionsbaserade värdar finns

>[!NOTE]
> Med Läs behörighet kan endast administratörer visa data. De behöver olika behörigheter för att hantera resurser i Windows-portalen för virtuella datorer.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Öppna Azure Monitor för virtuellt Windows-skrivbord

Du kan öppna Azure Monitor för virtuella Windows-datorer med någon av följande metoder:

- Gå till [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Sök efter och välj **Windows Virtual Desktop** från Azure Portal och välj **insikter**.

- Sök efter och välj **Azure Monitor** från Azure Portal. Välj **Insights-hubb** under **insikter** och under **andra** väljer du **virtuellt Windows-skrivbord** för att öppna instrument panelen på sidan Azure Monitor.

När du har Azure Monitor för det virtuella Windows-skrivbordet, markerar du en eller flera av kryss rutorna med namnet **prenumeration**, **resurs grupp**, **värd pool** och **tidsintervall** baserat på vilken miljö du vill övervaka.

>[!NOTE]
>Windows Virtual Desktop stöder för närvarande bara övervakning av en prenumeration, en resurs grupp och en adresspool i taget. Om du inte hittar den miljö som du vill övervaka kan du läsa [vår fel söknings dokumentation](troubleshoot-azure-monitor.md) för kända funktions begär Anden och problem.!

## <a name="set-up-with-the-configuration-workbook"></a>Konfigurera med konfigurations arbets boken

Om det här är första gången du öppnar Azure Monitor för Windows Virtual Desktop måste du konfigurera Azure Monitor för dina Windows-resurser för virtuella skriv bord. Så här konfigurerar du dina resurser:

1. Öppna din arbets bok i Azure Portal.
2. Välj **Öppna konfigurations arbets boken**.

Konfigurations arbets boken konfigurerar din övervaknings miljö och gör att du kan kontrol lera konfigurationen när du har slutfört installationen. Det är viktigt att kontrol lera konfigurationen om objekt på instrument panelen inte visas korrekt eller när produkt gruppen publicerar uppdateringar som kräver ytterligare data punkter.

## <a name="host-pool-diagnostic-settings"></a>Diagnostikinställningar för värdpool

Du måste aktivera Azure Monitor diagnostikinställningar på alla objekt i den Windows Virtual Desktop-miljö som stöder den här funktionen.

1. Öppna Azure Monitor för virtuellt Windows-skrivbord på [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)och välj sedan **konfigurations arbets bok**.

2. Välj en miljö som ska övervakas under **prenumeration**, **resurs grupp** och **adresspool**.

3. Under **diagnostikinställningar för Virtualiseringsvärd** kontrollerar du om Windows Virtual Desktop Diagnostics är aktiverat för den här poolen. Om de inte gör det visas ett fel meddelande om att det inte gick att hitta någon befintlig diagnostisk konfiguration för den valda poolen. 
   
   Följande tabeller ska vara aktiverade:

    - Checkpoint
    - Fel
    - Hantering
    - Anslutning
    - HostRegistration

    >[!NOTE]
    > Om du inte ser fel meddelandet behöver du inte utföra steg 4.

4. Välj **Konfigurera värdbaserad pool**.

5. Välj **Distribuera**.

6. Uppdatera arbets boken.

Du kan lära dig mer om hur du aktiverar diagnostik på alla objekt i Windows-miljön för virtuella skriv bord eller använder arbets ytan Log Analytics när [du skickar diagnostik för virtuella Windows-datorer till Log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Konfigurera Log Analytics

Om du vill börja använda Azure Monitor för virtuella Windows-datorer måste du också ha minst en Log Analytics arbets yta för att samla in data från miljön som du planerar att övervaka och tillhandahålla den i arbets boken. Om du redan har en konfiguration kan du gå vidare till [Konfigurera prestanda räknare](#set-up-performance-counters). Om du vill skapa en ny Log Analytics arbets yta för Azure-prenumerationen som innehåller din Windows Virtual Desktop-miljö, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Standard avgifter för data lagring för Log Analytics kommer att gälla. För att starta rekommenderar vi att du väljer modellen betala per användning och justerar när du skalar distributionen och tar med mer data. Mer information finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Konfigurera prestanda räknare

Du måste aktivera vissa prestanda räknare för insamling i motsvarande exempel intervall i Log Analytics-arbetsytan. Dessa prestanda räknare är de enda räknare du behöver för att övervaka virtuella Windows-datorer. Du kan inaktivera alla andra för att spara kostnader.

Om du redan har aktiverat prestanda räknare och vill ta bort dem, följer du anvisningarna i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md) för att konfigurera om prestanda räknarna. Även om artikeln beskriver hur du lägger till räknare kan du ta bort dem på samma plats.

Om du inte redan har konfigurerat prestanda räknare kan du konfigurera dem för Azure Monitor för virtuella Windows-datorer:

1. Gå till [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)och välj sedan **konfigurations arbets boken** längst ned i fönstret.

2. Under **Log Analytics konfiguration** väljer du den arbets yta som du har skapat för din prenumeration.

3. I **prestanda räknare för arbets ytan** visas en lista över de räknare som krävs för övervakning. På höger sida av listan markerar du objekten i listan **saknade räknare** för att aktivera de räknare du behöver för att börja övervaka din arbets yta.

4. Välj **Konfigurera prestanda räknare**.

5. Välj **Använd config**.

6. Uppdatera konfigurations arbets boken och fortsätt konfigurera din miljö.

Du kan också lägga till nya prestanda räknare efter den inledande konfigurationen när tjänsten uppdateras och kräver nya övervaknings verktyg. Du kan kontrol lera att alla nödvändiga räknare är aktiverade genom att markera dem i listan över **saknade räknare** .

>[!NOTE]
>Prestanda räknare för ineffekts fördröjning är bara kompatibla med Windows 10 RS5 och senare eller Windows Server 2019 och senare.

Mer information om hur du manuellt lägger till prestanda räknare som inte redan är aktiverade för insamling finns i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Konfigurera Windows-händelser

Därefter måste du aktivera vissa Windows-händelser för insamling i Log Analytics-arbetsytan. De händelser som beskrivs i det här avsnittet är de enda Azure Monitor för virtuella Windows-datorer. Du kan inaktivera alla andra för att spara kostnader.

Konfigurera Windows-händelser:

1. Om du redan har aktiverat Windows-händelser och vill ta bort dem tar du bort de händelser som du inte vill ha innan du använder konfigurations arbets boken för att aktivera uppsättningen som krävs för övervakning.

2. Gå till Azure Monitor för virtuellt Windows-skrivbord på [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)och välj sedan **konfigurations arbets bok** längst ned i fönstret.

3. I **konfiguration av Windows-händelser** finns det en lista över Windows-händelser som krävs för övervakning. På höger sida av listan finns listan över **saknade händelser** där du hittar de nödvändiga händelse namnen och händelse typerna som inte är aktiverade för din arbets yta. Registrera vart och ett av dessa namn för senare.

4. Välj **konfiguration av öppen arbets yta**.

5. Välj **data**.

6. Välj **händelse loggar i Windows**.

7. Lägg till de händelse namn som saknas i steg 3 och den händelse typ som krävs för varje.

8. Uppdatera konfigurations arbets boken och fortsätt konfigurera din miljö.

Du kan lägga till nya Windows-händelser efter den inledande konfigurationen om uppdateringar av övervaknings verktyget kräver att nya händelser aktive ras. För att se till att alla nödvändiga händelser är aktiverade går du tillbaka till listan över **saknade händelser** och aktiverar eventuella saknade händelser som visas där.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Installera Log Analytics agent på alla värdar

Slutligen måste du installera Log Analytics agent på alla värdar i poolen för att skicka data från värdarna till den valda arbets ytan.

Så här installerar du Log Analytics agent:

1. Gå till Azure Monitor för virtuellt Windows-skrivbord på [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)och välj sedan **konfigurations arbets bok** längst ned i fönstret.

2. Om Log Analytics inte har kon figurer ATS för alla värdar i poolen visas ett fel längst ned i avsnittet Log Analytics konfiguration med meddelandet "vissa värdar i poolen skickar inte data till den valda Log Analytics-arbetsytan." Välj **Lägg till värdar till arbets ytan** för att lägga till de valda värdarna. Om du inte ser fel meddelandet stoppar du det här.

3. Uppdatera konfigurations arbets boken.

>[!NOTE]
>Värd datorn måste köra för att installera Log Analytics-tillägget. Om automatisk distribution Miss lyckas på en värd kan du alltid installera tillägget på en värd manuellt. Information om hur du installerar tillägget manuellt finns [Log Analytics tillägget för virtuella datorer för Windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Valfritt: Konfigurera aviseringar

Du kan konfigurera Azure Monitor för virtuella Windows-datorer för att meddela dig om några allvarliga Azure Monitor aviseringar sker i den valda prenumerationen. Det gör du genom att följa anvisningarna i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Azure Monitor. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet.

För att tillhandahålla korrekta och effektiva fel söknings funktioner inkluderar insamlade data ett Portal-sessions-ID, Azure Active Directory användar-ID och namnet på Portal-fliken där händelsen inträffade. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Information om hur du visar eller tar bort dina person uppgifter som samlas in av tjänsten finns i [Azure Data subject-begäranden för GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Mer information om GDPR finns i [avsnittet GDPR på service Trust-portalen](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Windows Virtual Desktop Azure Portal, finns här några resurser som kan hjälpa dig att:

- Ta en titt på vårt [ord](azure-monitor-glossary.md) för att lära dig mer om termer och begrepp som rör Azure Monitor för virtuella Windows-datorer.
- Om det uppstår ett problem kan du läsa vår [fel söknings guide](troubleshoot-azure-monitor.md) för hjälp.