---
title: Använd övervaka Windows Virtual Desktop Monitor – Azure
description: Använda Azure Monitor för virtuella Windows-datorer.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7da35f77dd232e5f523e2bdc3f125c68015ab871
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448193"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Använd Azure Monitor för virtuella Windows-datorer för att övervaka distributionen

Azure Monitor för virtuella Windows-datorer är en instrument panel som bygger på Azure Monitor arbets böcker som hjälper IT-proffs att förstå sina Windows-miljöer för virtuella datorer. Det här avsnittet beskriver hur du konfigurerar Azure Monitor för virtuella Windows-datorer för att övervaka dina Windows-miljöer för virtuella skriv bord.

## <a name="requirements"></a>Krav

Innan du börjar använda Azure Monitor för virtuella Windows-datorer måste du konfigurera följande saker:

- Alla Windows-miljöer för virtuella skriv bord som du övervakar måste vara baserade på den senaste versionen av Windows Virtual Desktop som är kompatibel med Azure Resource Manager.
- Minst en konfigurerad Log Analytics-arbetsyta. Använd en angiven Log Analytics arbets yta för dina Windows-värdar för virtuella skriv bord för att säkerställa att prestanda räknare och händelser bara samlas in från värdar i Windows-distributionen för virtuella datorer.
- Aktivera data insamling för följande saker i din Log Analytics-arbetsyta:
    - Diagnostik från din Windows-miljö för virtuella datorer
    - Rekommenderade prestanda räknare från dina virtuella Windows-värdar för fjärrskrivbordssessioner
    - Rekommenderade Windows-händelseloggar från dina Windows-sessioner för virtuella Skriv bords värdar

 Den data konfigurations process som beskrivs i den här artikeln är den enda du behöver för att övervaka det virtuella Windows-skrivbordet. Du kan inaktivera alla andra objekt som skickar data till din Log Analytics-arbetsyta för att spara kostnader.

Alla som övervakar Azure Monitor för virtuella Windows-datorer för din miljö behöver också följande Läs behörighet:

- Läs åtkomst till de Azure-prenumerationer som innehåller dina Windows-resurser för virtuella skriv bord
- Läs åtkomst till prenumerationens resurs grupper som innehåller dina virtuella Windows-värdar för fjärrskrivbordssession
- Läs åtkomst till Log Analytics arbets ytan eller arbets ytorna

>[!NOTE]
> Med Läs behörighet kan endast administratörer visa data. De behöver olika behörigheter för att hantera resurser i Windows-portalen för virtuella datorer.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Öppna Azure Monitor för virtuellt Windows-skrivbord

Du kan öppna Azure Monitor för virtuella Windows-datorer med någon av följande metoder:

- Gå till [aka.MS/azmonwvdi](https://aka.ms/azmonwvdi).
- Sök efter och välj **Windows Virtual Desktop** från Azure Portal och välj **insikter**.
- Sök efter och välj **Azure Monitor** från Azure Portal. Välj **Insights-hubb** under **insikter** och välj sedan **Windows Virtual Desktop**.
När du har öppnat sidan, anger du **prenumerationen**, **resurs gruppen**, **poolen** och **tidsintervallet** för den miljö som du vill övervaka.

>[!NOTE]
>Windows Virtual Desktop stöder för närvarande bara övervakning av en prenumeration, en resurs grupp och en adresspool i taget. Om du inte hittar den miljö som du vill övervaka kan du läsa [vår fel söknings dokumentation](troubleshoot-azure-monitor.md) för kända funktions begär Anden och problem.

## <a name="log-analytics-settings"></a>Log Analytics inställningar

Om du vill börja använda Azure Monitor för virtuella Windows-datorer måste du ha minst en Log Analytics arbets yta. Använd en angiven Log Analytics arbets yta för dina Windows-sessioner för virtuella Skriv bords värdar för att säkerställa att prestanda räknare och händelser bara samlas in i en Windows-distribution med virtuella skriv bord. Om du redan har konfigurerat en arbets yta kan du gå vidare till [Konfigurera med hjälp av konfigurations arbets boken](#set-up-using-the-configuration-workbook). Information om hur du ställer in en konfiguration finns [i skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Standard avgifter för data lagring för Log Analytics kommer att gälla. För att starta rekommenderar vi att du väljer modellen betala per användning och justerar när du skalar distributionen och tar med mer data. Mer information finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Konfigurera med hjälp av konfigurations arbets boken

Om det är första gången du öppnar Azure Monitor för virtuella Windows-datorer måste du konfigurera Azure Monitor för din Windows-miljö för virtuella datorer. Så här konfigurerar du dina resurser:

1. Öppna Azure Monitor för virtuellt Windows-skrivbord i Azure Portal på [aka.MS/azmonwvdi](https://aka.ms/azmonwvdi)och välj sedan **konfigurations arbets bok**.
2. Välj en miljö som ska konfigureras under **prenumeration**, **resurs grupp** och **värdbaserad pool**.

Konfigurations arbets boken konfigurerar din övervaknings miljö och gör att du kan kontrol lera konfigurationen när du har slutfört installationen. Det är viktigt att kontrol lera konfigurationen om objekt på instrument panelen inte visas korrekt eller när produkt gruppen publicerar uppdateringar som kräver nya inställningar.

### <a name="resource-diagnostic-settings"></a>Inställningar för resurs diagnostik

Om du vill samla in information om din Windows-infrastruktur för virtuella datorer måste du aktivera flera diagnostikinställningar på dina Windows-pooler för virtuella skriv bord och-arbets ytor (det här är din Windows-arbets yta för virtuella skriv bord, inte din Log Analytics-arbetsyta). Mer information om lagringspooler, arbets ytor och andra resurs objekt för virtuella Windows-datorer finns i vår [miljö guide](environment-setup.md).

Du kan lära dig mer om Windows Virtual Desktop Diagnostics och de diagnostiska tabeller som stöds när [du skickar diagnostik för virtuella Windows-datorer till Log Analytics](diagnostics-log-analytics.md).

Så här anger du inställningarna för resurs diagnostik i konfigurations arbets boken: 

1. Välj fliken **resurs diagnostiska inställningar** i arbets boken konfiguration. 
2. Välj **Log Analytics arbets yta** för att skicka Windows-diagnostik för virtuella skriv bord. 

#### <a name="host-pool-diagnostic-settings"></a>Diagnostikinställningar för värdpool

Konfigurera diagnostik för anslutningspoolen med hjälp av avsnittet resurs diagnostiska inställningar i konfigurations arbets boken:

1. Under **värd pool** kontrollerar du om Windows Virtual Desktop Diagnostics är aktiverat. Om de inte gör det visas ett fel meddelande om att det inte gick att hitta någon befintlig diagnostisk konfiguration för den valda poolen. Du måste aktivera följande diagnostiska tabeller som stöds:

    - Checkpoint
    - Fel
    - Hantering
    - Anslutning
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Om du inte ser fel meddelandet behöver du inte utföra steg 2 till 4.

2. Välj **Konfigurera värdbaserad pool**.
3. Välj **Distribuera**.
4. Uppdatera konfigurations arbets boken.

#### <a name="workspace-diagnostic-settings"></a>Diagnostikinställningar för arbets yta 

Konfigurera arbets ytans diagnostik med hjälp av avsnittet resurs diagnostiska inställningar i konfigurations arbets boken:

 1. Under **arbets yta** kontrollerar du om Windows Virtual Desktop Diagnostics är aktiverat för arbets ytan för virtuella Windows-datorer. Om de inte gör det visas ett fel meddelande om att det inte gick att hitta någon befintlig diagnostisk konfiguration för den valda arbets ytan. Du måste aktivera följande diagnostiska tabeller som stöds:
 
    - Checkpoint
    - Fel
    - Hantering
    - Feed
    
    >[!NOTE]
    > Om du inte ser fel meddelandet behöver du inte utföra steg 2-4.

2. Välj **Konfigurera arbets yta**.
3. Välj **Distribuera**.
4. Uppdatera konfigurations arbets boken.

### <a name="session-host-data-settings"></a>Data inställningar för Session Host

Om du vill samla in information om dina Windows-värdar för virtuella skriv bord måste du installera Log Analytics-agenten på alla värddatorer i värddatorn, se till att sessionernas värdar skickas till en Log Analytics arbets yta och konfigurera dina Log Analytics agent inställningar för att samla in prestanda data och Windows-händelseloggar.

Log Analytics-arbetsytan som du skickar data från Session Host till behöver inte vara samma som du skickar diagnostikdata till. Om du har Azure-sessionsbaserade värdar utanför din Windows-miljö för virtuella datorer rekommenderar vi att du har en angiven Log Analytics arbets yta för Windows Virtual Desktop-sessionen värd. 

Ange Log Analytics arbets ytan där du vill samla in sessions värd data: 

1. Välj fliken **data inställningar för sessions värd** i konfigurations arbets boken. 
2. Välj den **Log Analytics arbets yta** som du vill skicka sessionens värd data till. 

#### <a name="session-hosts"></a>Värdar för sessioner

Du måste installera Log Analytics-agenten på alla värdar i värddatorn och skicka data från dessa värdar till den valda Log Analytics-arbetsytan. Om Log Analytics inte har kon figurer ATS för alla sessioner i poolen, ser du avsnittet om sessionens **värdar** överst i **data inställningarna för sessionens värd** med meddelandet "vissa värdar i poolen skickar inte data till den valda Log Analytics arbets ytan."

>[!NOTE]
> Om du inte ser avsnittet om **sessions-värdar** eller fel meddelande, ställs alla sessions värdar in korrekt. Gå vidare till konfigurera instruktioner för [arbets ytans prestanda räknare](#workspace-performance-counters).

Så här konfigurerar du dina återstående sessionsbaserade värdar med hjälp av konfigurations arbets boken:

1. Välj **Lägg till värdar till arbets ytan**. 
2. Uppdatera konfigurations arbets boken.

>[!NOTE]
>Värd datorn måste köra för att installera Log Analytics-tillägget. Om automatisk distribution inte fungerar kan du installera tillägget på en värd manuellt i stället. Information om hur du installerar tillägget manuellt finns [Log Analytics tillägget för virtuella datorer för Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Prestanda räknare för arbets yta

Du måste aktivera vissa prestanda räknare för att samla in prestanda information från värdarna och skicka den till Log Analytics arbets ytan.

Om du redan har aktiverat prestanda räknare och vill ta bort dem, följer du anvisningarna i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md). Du kan lägga till och ta bort prestanda räknare på samma plats.

Konfigurera prestanda räknare med hjälp av konfigurations arbets boken: 

1. Under **arbets ytans prestanda räknare** i konfigurations arbets boken, kontrollerar du **konfigurerade räknare** för att se de räknare som du redan har aktiverat för att skicka till arbets ytan Log Analytics. Kontrol lera **saknade räknare** för att se till att du har aktiverat alla nödvändiga räknare.
2. Om du har räknare som saknar räknare väljer du **Konfigurera prestanda räknare**.
3. Välj **Använd config**.
4. Uppdatera konfigurations arbets boken.
5. Kontrol lera att alla nödvändiga räknare är aktiverade genom att kontrol lera listan över **saknade räknare** . 

#### <a name="configure-windows-event-logs"></a>Konfigurera händelse loggar i Windows

Du måste också aktivera vissa Windows-händelseloggar för att samla in fel, varningar och information från sessionerna som värdar och skicka dem till Log Analytics arbets ytan.

Om du redan har aktiverat Windows-händelseloggen och vill ta bort dem, följer du anvisningarna i [Konfigurera Windows-händelseloggar](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Du kan lägga till och ta bort Windows-händelseloggar på samma plats.

Konfigurera Windows-händelseloggar med hjälp av konfigurations arbets boken:

1. Under **konfiguration av Windows-händelseloggen** kontrollerar du **konfigurerade händelse loggar** för att se de händelse loggar som du redan har aktiverat för att skicka till arbets ytan Log Analytics. Kontrol lera om det finns **saknade händelse loggar** för att kontrol lera att du har aktiverat alla Windows-händelseloggar.
2. Om du saknar Windows-händelseloggar väljer du **Konfigurera händelser**.
3. Välj **Distribuera**.
4. Uppdatera konfigurations arbets boken.
5. Kontrol lera att alla nödvändiga Windows-händelseloggar är aktiverade genom att kontrol lera listan med **händelse loggar som saknas** . 

>[!NOTE]
>Om automatisk händelse distribution Miss lyckas väljer du **Öppna agent konfiguration** i arbets boken konfiguration för att manuellt lägga till eventuella Windows-händelseloggar som saknas. 

## <a name="optional-configure-alerts"></a>Valfritt: Konfigurera aviseringar

Med Azure Monitor för virtuella Windows-datorer kan du övervaka Azure Monitor aviseringar som sker i den valda prenumerationen i kontexten för dina Windows-datastations data. Azure Monitor aviseringar är en valfri funktion på dina Azure-prenumerationer och du måste konfigurera dem separat från Azure Monitor för Windows Virtual Desktop. Du kan använda ramverket Azure Monitor varningar för att ange anpassade aviseringar för virtuella Windows-datorer, diagnostik och resurser. Mer information om Azure Monitor aviseringar finns i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Azure Monitor. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet.

För att tillhandahålla korrekta och effektiva fel söknings funktioner inkluderar insamlade data ett Portal-sessions-ID, Azure Active Directory användar-ID och namnet på Portal-fliken där händelsen inträffade. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Information om hur du visar eller tar bort dina person uppgifter som samlas in av tjänsten finns i [Azure Data subject-begäranden för GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Mer information om GDPR finns i [avsnittet GDPR på service Trust-portalen](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat Azure Monitor för din Windows Virtual Desktop-miljö är här några resurser som kan hjälpa dig att börja övervaka din miljö:

- Ta en titt på vårt [ord](azure-monitor-glossary.md) för att lära dig mer om termer och begrepp som rör Azure Monitor för virtuella Windows-datorer.
- Information om hur du uppskattar, mäter och hanterar dina kostnader för data lagring finns i [beräkna Azure Monitor kostnader](azure-monitor-costs.md).
- Om det uppstår ett problem kan du läsa vår [fel söknings guide](troubleshoot-azure-monitor.md) för hjälp och kända problem.
