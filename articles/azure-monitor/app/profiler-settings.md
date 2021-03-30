---
title: Använd fönstret Azure Application Insights profiler-inställningar | Microsoft Docs
description: Se profilernas status och starta profilering av sessioner
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499417"
---
# <a name="configure-application-insights-profiler"></a>Konfigurera Application Insights Profiler

## <a name="updated-profiler-agent"></a>Agenten har uppdaterats
Utlösaren fungerar bara med version 2,6 eller senare av profilerings agenten. Om du kör en Azure App Service kommer agenten att uppdateras automatiskt. Du kan se vilken version av agenten du kör om du går till kudu-URL: en för din webbplats och lägger till \DiagnosticServices i slutet av den, så här:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . Application Insights Profiler webbjobbet ska vara version 2,6 eller senare. Du kan tvinga en uppgradering genom att starta om din webbapp. 

Om du kör profileraren på en virtuell dator eller moln tjänst måste du ha Windows Azure-diagnostik (WAD)-tilläggs version 16.0.4 eller senare installerat. Du kan kontrol lera versionen av WAD genom att logga in på den virtuella datorn och titta på den här katalogen: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Katalog namnet är den version av WAD som är installerad. Azure VM-agenten kommer att uppdatera WAD automatiskt när nya versioner är tillgängliga.

## <a name="profiler-settings-page"></a>Sidan Inställningar för profiler

Öppna fönstret Azure Application Insights profiler-inställningar genom att gå till fönstret Application Insights prestanda och sedan välja knappen **Konfigurera** profiler.

![Länk till sidan Inställningar för profiler öppnas][configure-profiler-entry]

Då öppnas en sida som ser ut så här:

![Sidan Inställningar för profiler][configure-profiler-page]

På sidan **konfigurera Application Insights profiler** finns följande funktioner:

| Funktion | Beskrivning |
|-|-|
Profilera nu | Startar profilering av sessioner för alla appar som är länkade till den här instansen av Application Insights.
Utlösare | Gör att du kan konfigurera utlösare som gör att profileraren kan köras. 
Senaste profilerings sessioner | Visar information om tidigare profilerings sessioner.

## <a name="profile-now"></a>Profilera nu
Med det här alternativet kan du starta en profilering av en session på begäran. När du klickar på den här länken börjar alla profiler som skickar data till den här Application Insights-instansen att avbilda en profil. Efter 5 till 10 minuter visas konsolsessionen i listan nedan.

För att en användare ska kunna utlösa en profilerad session manuellt, kräver de minst "Skriv"-åtkomst för rollen för Application Insights-komponenten. I de flesta fall får du den här åtkomsten automatiskt och inget ytterligare arbete krävs. Om du har problem kan du använda rollen som "Application Insights komponent deltagare" i prenumerations omfattnings rollen. [Läs mer om roll åtkomst kontroll med Azure-övervakning](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Utlösarens inställningar
![Utfällning av utlösnings inställningar][trigger-settings-flyout]

Om du klickar på knappen utlösare på Meny raden öppnas rutan Utlös ande inställningar. Du kan ställa in utlösare för att starta profilering när procent andelen processor-eller minnes användning träffar den nivå som du har angett.

| Inställning | Beskrivning |
|-|-|
På/av-knapp | På: profiler kan startas av den här utlösaren. Off: profiler startas inte av den här utlösaren.
Minnes tröskel | När den här procent andelen av minnet används startas profileraren.
Varaktighet | Anger hur lång tid profileraren ska köras när den utlöses.
Cooldown | Anger hur lång tid profiler ska vänta innan den söker efter minne eller CPU-användning igen när den har utlösts.

## <a name="recent-profiling-sessions"></a>Senaste profilerings sessioner
I det här avsnittet av sidan visas information om de senaste profilerings sessionerna. En profilerings session representerar den tids period då profilerings agenten tog en profil på en av datorerna som är värd för ditt program. Du kan öppna profilerna från en session genom att klicka på en av raderna. För varje session visar vi:

| Inställning | Beskrivning |
|-|-|
Utlöst av | Hur sessionen startades, antingen genom en utlösare, profil nu eller standard sampling. 
Appnamn | Namnet på det program som profilerades.
Dator instans | Namnet på datorn som profilens agent kördes på.
Timestamp | Tid när profilen fångades.
Spåra | Antal spårningar som kopplats till enskilda begär Anden.
CPU % | Procent andel CPU som användes när profileraren kördes.
Minnesoptimerade | Procent andelen minne som användes när profileraren kördes.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Använd webb prestanda test för att generera trafik till ditt program

Du kan utlösa profiler manuellt med ett enda klick. Anta att du kör ett test för webb prestanda. Du behöver spår för att hjälpa dig att förstå hur din webbapp körs under belastning. Att ha kontroll över när spårningar samlas in är viktigt, eftersom du vet när belastnings testet ska köras. Men det slumpmässiga samplings intervallet kan sakna det.

I nästa avsnitt visas hur det här scenariot fungerar:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Steg 1: generera trafik till din webbapp genom att starta ett test för webb prestanda

Om din webbapp redan har inkommande trafik eller om du bara vill generera trafik manuellt, hoppar du över det här avsnittet och fortsätter till steg 2.

1. I Application Insights-portalen väljer du **Konfigurera**  >  **prestanda testning**. 

1. Starta ett nytt prestandatest genom att välja knappen **nytt** .

   ![Skapa nytt prestandatest][create-performance-test]

1. I fönstret **nytt prestandatest** konfigurerar du URL för test mål. Godkänn alla standardinställningar och välj sedan **Kör test** för att börja köra belastnings testet.

    ![Konfigurera belastnings test][configure-performance-test]

    Det nya testet står i kö först, följt av status *pågår*.

    ![Belastnings testet skickas och placeras i kö][load-test-queued]

    ![Belastnings testet körs pågår][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Steg 2: starta en session på begäran

1. När belastnings testet körs startar du profiler för att samla in spårningar på webbappen medan den tar emot belastningen.

1. Gå till fönstret **Konfigurera** profiler.


### <a name="step-3-view-traces"></a>Steg 3: Visa spårningar

När profiler har körts klart följer du anvisningarna i aviseringen för att gå till fönstret prestanda och Visa spårningar.

## <a name="next-steps"></a>Nästa steg
[Aktivera profiler och Visa spårningar](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
