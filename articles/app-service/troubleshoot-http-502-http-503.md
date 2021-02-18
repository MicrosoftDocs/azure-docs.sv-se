---
title: Åtgärda HTTP 502 och HTTP 503-fel
description: Felsök 502 Felaktig gateway och 503 service otillgängliga fel i appen som finns i Azure App Service.
tags: top-support-issue
keywords: 502 Felaktig gateway, 503-tjänsten är inte tillgänglig, fel 503, fel 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 801f4840d0d1447f839e5c2c4bc3168741b62b63
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586253"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Felsök HTTP-fel för "502 Felaktig gateway" och "503-tjänsten är inte tillgänglig" i Azure App Service
"502 Felaktig gateway" och "503-tjänsten är inte tillgänglig" är vanliga fel i din app som finns [Azure App Service](./overview.md). Den här artikeln hjälper dig att felsöka de här felen.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och klicka på alternativet för att **få support**.

## <a name="symptom"></a>Symptom
När du bläddrar till appen returneras fel meddelandet HTTP "502 Felaktig gateway" eller "HTTP" 503-tjänsten är inte tillgänglig ".

## <a name="cause"></a>Orsak
Det här problemet orsakas ofta av problem på program nivå, till exempel:

* begär Anden som tar lång tid
* program som använder högt minne/CPU
* programmet kraschar på grund av ett undantag.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Fel söknings steg för att lösa "502 Felaktig gateway" och "503-tjänsten är inte tillgänglig"
Fel sökning kan delas upp i tre olika uppgifter i nummerordning:

1. [Observera och övervaka programmets beteende](#observe)
2. [Samla in data](#collect)
3. [Åtgärda problemet](#mitigate)

[App Service](overview.md) ger dig olika alternativ i varje steg.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observera och övervaka programmets beteende
#### <a name="track-service-health"></a>Spåra tjänstens hälsa
Microsoft Azure publicizes varje gången det uppstår avbrott i tjänsten eller prestanda försämring. Du kan spåra hälso tillståndet för tjänsten på Azure- [portalen](https://portal.azure.com/). Mer information finns i [spåra tjänstens hälsa](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Övervaka din app
Med det här alternativet kan du ta reda på om ditt program har problem. Klicka på panelen **förfrågningar och fel** i appens blad. På bladet **mått** visas alla mått som du kan lägga till.

Några av de mått som du kanske vill övervaka för din app är

* Genomsnittlig arbets mängd för minne
* Genomsnittlig svars tid
* CPU-tid
* Minnes arbets mängd
* Begäranden

![övervaka app för att lösa HTTP-fel på 502 Felaktig gateway och 503-tjänsten är inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Mer information finns i:

* [Övervaka appar i Azure App Service](web-sites-monitor.md)
* [Få varningsmeddelanden](../azure-monitor/alerts/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. samla in data
#### <a name="use-the-diagnostics-tool"></a>Använda verktyget diagnostik
App Service ger en intelligent och interaktiv upplevelse som hjälper dig att felsöka din app utan att det krävs någon konfiguration. När du stöter på problem med din app, pekar diagnostikverktyget ut vad som är fel för att hjälpa dig med rätt information för att enklare och snabbare felsöka och lösa problemet.

Om du vill komma åt App Service diagnostik navigerar du till din App Service app eller App Service-miljön i [Azure Portal](https://portal.azure.com). Klicka på **diagnostisera och lös problem** i det vänstra navigerings fältet.

#### <a name="use-the-kudu-debug-console"></a>Använda fel söknings konsolen kudu
App Service levereras med en fel söknings konsol som du kan använda för att felsöka, utforska, ladda upp filer, samt JSON-slutpunkter för att få information om din miljö. Detta kallas för *kudu-konsolen* eller *SCM-instrumentpanelen* för din app.

Du kan komma åt den här instrument panelen genom att gå till länken **https:// &lt; ditt program namn>. scm.azurewebsites.net/**.

Några av de saker som kudu tillhandahåller är:

* miljö inställningar för ditt program
* logg ström
* diagnostisk dump
* fel söknings konsolen där du kan köra PowerShell-cmdlets och Basic DOS-kommandon.

En annan användbar funktion i kudu är att, om ditt program ska leda till undantag i första chansen, kan du använda kudu och SysInternals-verktyget ProcDump för att skapa minnes dum par. Dessa minnes dum par är ögonblicks bilder av processen och kan ofta hjälpa dig att felsöka mer komplicerade problem med din app.

Mer information om vilka funktioner som är tillgängliga i kudu finns i [online verktyg för Azure Websites som du bör känna](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)till.

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. åtgärda problemet
#### <a name="scale-the-app"></a>Skala appen
I Azure App Service kan du, för bättre prestanda och data flöde, ändra skalan då du kör programmet. Att skala upp en app innefattar två relaterade åtgärder: att ändra App Service plan till en högre pris nivå och konfigurera vissa inställningar när du har bytt till den högre pris nivån.

Mer information om skalning finns i [skala en app i Azure App Service](manage-scale-up.md).

Du kan också välja att köra programmet på fler än en instans. Detta ger inte bara till gång till mer bearbetnings kapacitet, men ger dig också viss fel tolerans. Om processen går ned på en instans fortsätter den andra instansen fortfarande att betjäna begär Anden.

Du kan ställa in skalningen på manuell eller automatisk.

#### <a name="use-autoheal"></a>Använd autoläka
Autoreparation återanvänder arbets processen för din app baserat på de inställningar du väljer (t. ex. konfigurations ändringar, begär Anden, minnesbaserade gränser eller tiden som krävs för att utföra en begäran). Det mesta av tiden är att återvinna processen är det snabbaste sättet att återställa från ett problem. Även om du alltid kan starta om appen direkt i Azure Portal gör autoläka det automatiskt åt dig. Allt du behöver göra är att lägga till vissa utlösare i rot web.config för din app. Observera att de här inställningarna fungerar på samma sätt även om ditt program inte är ett .NET-konto.

Mer information finns i [Automatisk återställning av Azure Web Sites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Starta om appen
Detta är ofta det enklaste sättet att återställa från engångs problem. I [Azure Portal](https://portal.azure.com/)har du alternativen för att stoppa eller starta om din app på bladet för din app.

 ![Starta om appen för att lösa HTTP-fel på 502 Felaktig gateway och 503-tjänsten är inte tillgänglig](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Du kan också hantera din app med hjälp av Azure PowerShell. Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).