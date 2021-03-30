---
title: Diagnostik-och lösnings verktyg
description: Lär dig hur du kan felsöka problem med din app i Azure App Service med diagnostik-och lösnings verktyget i Azure Portal.
keywords: App Service, Azure App Service, diagnostik, support, webbapp, fel sökning, självhjälp
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: d58341979b0bbe0699a5ca293b20394c43cde1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962816"
---
# <a name="azure-app-service-diagnostics-overview"></a>Översikt över Azure App Service diagnostik

När du kör ett webb program måste du vara för beredd för eventuella problem som kan uppstå, från 500 fel till dina användare, vilket visar att platsen är avstängd. App Service Diagnostics är en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan att det krävs någon konfiguration. När du stöter på problem med din app, kan App Service diagnostik ta reda på vad som är fel för att hjälpa dig med rätt information för att enklare och snabbare felsöka och lösa problemet.

Även om den här upplevelsen är mest användbar när du har problem med din app under de senaste 24 timmarna, är alla diagnostiska grafer alltid tillgängliga så att du kan analysera dem.

App Service diagnostik fungerar bara för din app i Windows, utan även appar på [Linux/behållare](./overview.md#app-service-on-linux), [App Service-miljön](./environment/intro.md)och [Azure Functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>Öppna App Service diagnostik

Om du vill komma åt App Service diagnostik navigerar du till din App Service webbapp eller App Service-miljön i [Azure Portal](https://portal.azure.com). Klicka på **diagnostisera och lös problem** i det vänstra navigerings fältet.

För Azure Functions navigerar du till din Function-app. i det övre navigerings fönstret klickar du på **plattforms funktioner** och väljer **diagnostisera och löser problem** i avsnittet **resurs hantering** .

På Start sidan för App Service diagnostik kan du välja den kategori som bäst beskriver problemet med din app med hjälp av nyckelorden på varje start sida. Den här sidan är också där du kan hitta **diagnostikverktyg** för Windows-appar. Se [diagnostikverktyg (endast för Windows-app)](#diagnostic-tools-only-for-windows-app).

![Startsida](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Om din app är avstängd eller är långsam kan du [samla in en profilerings spårning](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) för att identifiera orsaken till problemet. Profilering är lätt vikt och är utformat för produktions scenarier.
>

## <a name="interactive-interface"></a>Interaktivt gränssnitt

När du har valt en kategori för start sidan som bäst överensstämmer med appens problem, kan App Service diagnosticss interaktiva gränssnitt, Genie, hjälpa dig att diagnostisera och lösa problem med din app. Du kan använda de kortkommandon som tillhandahålls av Genie för att visa en fullständig diagnostisk rapport för den problem kategori som du är intresse rad av. Panel gen vägarna ger dig ett direkt sätt att komma åt dina diagnostiska mått.

![Panel gen vägar](./media/app-service-diagnostics/tile-shortcuts-2.png)

När du har klickat på dessa paneler kan du se en lista över de avsnitt som handlar om problemet som beskrivs i panelen. De här avsnitten innehåller kodfragment av viktig information från den fullständiga rapporten. Du kan klicka på något av dessa avsnitt om du vill undersöka problemen ytterligare. Du kan också klicka på **Visa fullständig rapport** om du vill utforska alla ämnen på en enda sida.

![Ämnen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Visa fullständig rapport](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostisk rapport

När du har valt att undersöka problemet ytterligare genom att klicka på ett ämne kan du Visa mer information om avsnittet som ofta kompletteras med grafer och markdowns. Diagnostiska rapporter kan vara ett kraftfullt verktyg för att hitta problemet med din app.

![Diagnostisk rapport](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Health Checkup

Om du inte vet vad som är fel med din app eller inte vet var du ska börja felsöka dina problem, är Health Checkup en bra plats för att starta. Health Checkup analyserar dina program så att du får en snabb, interaktiv översikt över vad som är felfritt och vad som är fel, vilket ger dig information om var du ska undersöka problemet. Det intelligenta och interaktiva gränssnittet ger vägledning genom fel söknings processen. Health Checkup är integrerat med Genie-upplevelsen för Windows-appar och en diagnostisk rapport för webbapp för Linux-appar.

### <a name="health-checkup-graphs"></a>Health Checkup-diagram

Det finns fyra olika grafer i Health Checkup.

- **begär Anden och fel:** En graf som visar antalet begär Anden som gjorts under de senaste 24 timmarna tillsammans med HTTP server-fel.
- **app-prestanda:** En graf som visar svars tiden under de senaste 24 timmarna för olika percentils grupper.
- **CPU-användning:** En graf som visar den totala processor användningen i procent per instans under de senaste 24 timmarna.  
- **minnes användning:** En graf som visar den totala mängden fysiskt minnes användning per instans under de senaste 24 timmarna.

![Health Checkup](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Undersök problem med program kod (endast för Windows-appar)

Eftersom många app-problem är relaterade till problem i din program kod, integreras App Service Diagnostics med [Application Insights](../azure-monitor/app/app-insights-overview.md) för att markera undantag och beroende problem för att korrelera med den valda stillestånds tiden. Application Insights måste aktive ras separat.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Om du vill visa Application Insights undantag och beroenden väljer du genvägar till **webbappens ned-** eller **webbappens långsamma** panel.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Fel söknings steg (endast för Windows-app)

Om ett problem upptäcks med en viss problem kategori inom de senaste 24 timmarna, kan du Visa hela Diagnostikrapporten och App Service diagnostik kan du uppmanas att visa mer fel söknings råd och nästa steg för att få en mer guidad upplevelse.

![Application Insights och fel sökning och nästa steg](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostikverktyg (endast för Windows-app)

Diagnostikverktygen innehåller mer avancerade diagnostikverktyg som hjälper dig att undersöka problem med program kod, långsam, anslutnings strängar med mera. och proaktiva verktyg som hjälper dig att undvika problem med CPU-användning, begär Anden och minne.

### <a name="proactive-cpu-monitoring"></a>Proaktiv CPU-övervakning

Proaktiv CPU-övervakning ger dig ett enkelt, proaktivt sätt att vidta en åtgärd när din app eller underordnade process för din app förbrukar höga processor resurser. Du kan ställa in egna CPU-regler för att tillfälligt minimera ett högt CPU-problem tills den faktiska orsaken till det oväntade problemet har påträffats. Mer information finns i [minska dina CPU-problem innan de inträffar](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktiv CPU-övervakning](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatisk återställning och proaktiv automatisk återställning

Automatisk återställning är en åtgärd som du kan vidta när din app har oväntat beteende. Du kan ställa in egna regler baserat på antalet begär Anden, långsam begäran, minnes gräns och HTTP-statuskod för att utlösa åtgärder. Använd verktyget för att tillfälligt minimera ett oväntat beteende tills du hittar rotor saken. Mer information finns i avsnittet [om att presentera den nya automatiska lagnings upplevelsen i App Service Diagnostics](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatisk återställning](./media/app-service-diagnostics/auto-healing-10.png)

Precis som proaktiv CPU-övervakning är proaktiv automatisk återställning en lösning för att åtgärda oväntade beteenden i din app. Proaktiv automatisk återställning startar om appen när App Service bestämmer att appen är i ett oåterkalleligt tillstånd. Mer information finns i [Introduktion till proaktiv automatisk läka](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigering och ändrings analys (endast för Windows-app)

I ett stort team med kontinuerlig integrering och där din app har många beroenden kan det vara svårt att hitta den speciella ändring som orsakar ett problem med skadan. Navigator hjälper dig att få insyn i appens topologi genom att automatiskt återge en beroende karta för appen och alla resurser i samma prenumeration. Med navigatören kan du Visa en lista över ändringar som har gjorts av din app och dess beroenden och begränsa en ändring som orsakar ett skadat beteende. Det går att komma åt den via panelens **navigerings** panel och måste aktive ras innan du använder den första gången. Mer information finns i [få insyn i appens beroenden med Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Standard sida för Navigator](./media/app-service-diagnostics/navigator-default-page-11.png)

![Diff-vy](./media/app-service-diagnostics/diff-view-12.png)

Ändrings analyser för app-ändringar kan nås via panel gen vägar, **program ändringar** och **program krascher** i **tillgänglighet och prestanda** så att du kan använda dem samtidigt med andra mått. Innan du använder funktionen måste du först aktivera den. Mer information finns i avsnittet [om den nya ändrings analys upplevelsen i App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Publicera dina frågor eller feedback på [UserVoice](https://feedback.azure.com/forums/169385-web-apps) genom att lägga till [diag] i rubriken.