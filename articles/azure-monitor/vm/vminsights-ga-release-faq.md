---
title: Vanliga frågor och svar om Azure Monitor for VMs (GA) | Microsoft Docs
description: Azure Monitor for VMs är en lösning i Azure som kombinerar hälso-och prestanda övervakning av operativ systemet på den virtuella Azure-datorn samt identifierar program komponenter och beroenden automatiskt med andra resurser och mappar kommunikationen mellan dem. I den här artikeln besvaras vanliga frågor om GA-versionen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 1958c5fcdac4ae2a080dd8a43178c204ba5fadd6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625643"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor for VMs allmänt tillgängliga (GA)
Vanliga frågor och svar om allmän tillgänglighet omfattar ändringar som gjorts i Q4 2019 och Q1 2020 när vi för beredde för GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Uppdateringar för Azure Monitor for VMs
Vi har lanserat en ny version av Azure Monitor for VMs i januari 2020 före vårt GA-meddelande. Kunder som aktiverar Azure Monitor for VMs får nu GA-versionen, men befintliga kunder som använder den version av Azure Monitor for VMs från Q4 2019 och tidigare uppmanas att uppgradera. Det här vanliga avsnittet innehåller vägledning för att utföra en uppgradering i stor skala om du har stora distributioner över flera arbets ytor.


Med den här uppgraderingen lagras Azure Monitor for VMs prestanda data i samma *InsightsMetrics* -tabell som [Azure Monitor för behållare](../insights/container-insights-overview.md), vilket gör det enklare för dig att fråga de två data uppsättningarna. Du kan också lagra Mer Diverse data uppsättningar som vi inte kunde lagra i tabellen som användes tidigare. 

Våra prestanda visningar använder nu de data som vi lagrar i *InsightsMetrics* -tabellen.  Om du ännu inte har uppgraderat för att använda den senaste VMInsights-lösningen på arbets ytan visas inte längre information i dina diagram.  Du kan uppgradera från vår **Kom igång** -sida enligt beskrivningen nedan.


## <a name="what-is-changing"></a>Vad ändras?
Vi har släppt en ny lösning med namnet VMInsights, som innehåller ytterligare funktioner för data insamling tillsammans med en ny plats för att lagra dessa data i din Log Analytics-arbetsyta. 

Tidigare aktiverade vi ServiceMap-lösningen på din arbets yta och ställer in prestanda räknare i Log Analytics arbets ytan för att skicka data till *perf* -tabellen. Den här nya lösningen skickar data till en tabell med namnet *InsightsMetrics* som också används av Azure Monitor för behållare. Det här tabell schemat gör att vi kan lagra ytterligare mått och tjänst data uppsättningar som inte är kompatibla med tabell formatet *perf* .

Vi har uppdaterat våra prestanda diagram för att använda de data som vi lagrar i *InsightsMetrics* -tabellen. Du kan uppgradera för att använda *InsightsMetrics* -tabellen från vår sida för att **komma igång** enligt beskrivningen nedan.


## <a name="how-do-i-upgrade"></a>Hur gör jag för att uppgradering?
När en Log Analytics arbets yta uppgraderas till den senaste versionen av Azure Monitor till virtuella datorer, uppgraderas beroende agenten på varje virtuell dator som är ansluten till arbets ytan. Varje virtuell dator som kräver uppgradering identifieras på fliken **Kom igång** i Azure Monitor for VMs i Azure Portal. När du väljer att uppgradera en virtuell dator uppgraderas arbets ytan för den virtuella datorn tillsammans med andra virtuella datorer som är kopplade till arbets ytan. Du kan välja en enskild virtuell dator eller flera virtuella datorer, resurs grupper eller prenumerationer. 

Använd följande kommando för att uppgradera en arbets yta med hjälp av PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Vad ska jag göra med prestanda räknarna i min arbets yta om jag installerar VMInsights-lösningen?

Den tidigare metoden för att aktivera Azure Monitor for VMs använda prestanda räknare i din arbets yta. Den aktuella versionen lagrar dessa data i en tabell med namnet `InsightsMetrics` . Du kan välja att inaktivera dessa prestanda räknare i din arbets yta om du inte längre behöver använda dem. 

>[!NOTE]
>Om du har varnings regler som refererar till dessa räknare i `Perf` tabellen måste du uppdatera dem för att referera till nya data som lagras i `InsightsMetrics` tabellen. Se vår dokumentation för exempel på logg frågor som du kan använda för att referera till den här tabellen.
>

Om du bestämmer dig för att hålla prestanda räknarna aktiverade debiteras du för de data som matas in och lagras i `Perf` tabellen baserat på [Log Analytics priser [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Hur påverkar den här ändringen mina varnings regler?

Om du har skapat [logg aviseringar](../alerts/alerts-unified-log.md) som frågar `Perf` tabell mål prestanda räknare som har Aktiver ATS i arbets ytan, bör du uppdatera reglerna så att de refererar till `InsightsMetrics` tabellen i stället. Den här vägledningen gäller även för alla logg Sök regler som använder `ServiceMapComputer_CL` och `ServiceMapProcess_CL` , eftersom dessa data uppsättningar flyttas till `VMComputer` och `VMProcess` tabeller.

Vi kommer att uppdatera dessa vanliga frågor och svar och vår dokumentation för att inkludera exempel på loggs öknings varningar för de data uppsättningar som vi samlar in.

## <a name="how-will-this-affect-my-bill"></a>Hur kommer detta att påverka min faktura?

Faktureringen baseras fortfarande på inmatade data och behålls i Log Analytics arbets ytan.

Prestanda data för dator nivå som vi samlar in är samma, är av en liknande storlek som de data som vi har lagrat i `Perf` tabellen och kommer att kosta ungefär samma belopp.

## <a name="what-if-i-only-want-to-use-service-map"></a>Vad händer om jag bara vill använda Tjänstkarta?

Det är bra. Du kommer att se prompter i Azure Portal när du visar Azure Monitor for VMs om den kommande uppdateringen. När du har lanserat får du en fråga om att du ska uppdatera till den nya versionen. Om du bara vill använda [Maps](vminsights-maps.md) -funktionen kan du välja att inte uppgradera och fortsätta använda Maps-funktionen i Azure Monitor for VMs och tjänstkarta-lösningen som nås från din arbets yta eller instrument panels panel.

Om du väljer att aktivera prestanda räknarna manuellt i arbets ytan kan du se data i några av våra prestanda diagram som visas från Azure Monitor. När den nya lösningen lanseras uppdaterar vi våra prestanda diagram för att fråga data som lagras i `InsightsMetrics` tabellen. Om du vill se data från tabellen i dessa diagram måste du uppgradera till den nya versionen av Azure Monitor for VMs.

Ändringarna för att flytta data från `ServiceMapComputer_CL` och `ServiceMapProcess_CL` påverkar både Tjänstkarta och Azure Monitor for VMS, så du måste ändå planera för den här uppdateringen.

Om du väljer att inte uppgradera till **VMInsights** -lösningen kommer vi fortsätta att tillhandahålla äldre versioner av arbets böckerna för prestanda som refererar till data i `Perf` tabellen.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Kommer Tjänstkarta data uppsättningar också att lagras i InsightsMetrics?

Data uppsättningarna kommer inte att dupliceras om du använder båda lösningarna. Båda erbjudandena delar de data uppsättningar som ska lagras i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection` och `VMBoundPort` tabeller för att lagra de kart data uppsättningar som vi samlar in.  

`InsightsMetrics`Tabellen kommer att lagra data uppsättningar för virtuella datorer, processer och tjänster som vi samlar in och kommer bara att fyllas i om du använder Azure Monitor for VMS och lösningen för VM-insikter. Tjänstkarta lösning samlar inte in eller lagrar data i `InsightsMetrics` tabellen.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Får jag dubbla avgifter om jag har Tjänstkarta-och VMInsights-lösningar i min arbets yta?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection` och `VMBoundPort` . Du debiteras inte för dessa data om du har båda lösningarna på arbets ytan.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Om jag tar bort antingen Tjänstkarta-eller VMInsights-lösningen tas mina data bort?

Nej, de två lösningarna delar mappnings data uppsättningar som vi lagrar i `VMComputer` (tidigare ServiceMapComputer_CL), `VMProcess` (tidigare ServiceMapProcess_CL), `VMConnection` och `VMBoundPort` . Om du tar bort en av lösningarna märker dessa data uppsättningar att det fortfarande finns en lösning på plats som använder data och den finns kvar i arbets ytan Log Analytics. Du måste ta bort båda lösningarna från din arbets yta för att data ska kunna tas bort från den.

## <a name="health-feature-is-in-limited-public-preview"></a>Hälso funktionen är i begränsad offentlig för hands version

Vi har fått mycket bra feedback från kunder om vår funktions uppsättning för VM-hälsa. Det är mycket intressantare för den här funktionen och du kan piffa över dess potential för att stödja övervaknings arbets flöden. Vi planerar att göra en serie ändringar för att lägga till funktioner och åtgärda den feedback vi har fått. 

Vi har flyttat den här funktionen till en **begränsad offentlig för hands version** för att minimera effekten av dessa ändringar för nya kunder. Den här uppdateringen skedde i oktober 2019.

Vi planerar att starta om denna hälso funktion i 2020 efter Azure Monitor for VMs är i GA.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Hur kommer befintliga kunder att få åtkomst till hälso funktionen?

Befintliga kunder som använder hälso funktionen fortsätter att ha åtkomst till den, men den erbjuds inte till nya kunder.  

För att få åtkomst till funktionen kan du lägga till följande funktions flagga `feature.vmhealth=true` i Azure Portal-URL: en [https://portal.azure.com](https://portal.azure.com) . Exempel `https://portal.azure.com/?feature.vmhealth=true` .

Du kan också använda den här korta URL: en, som ställer in funktions flaggan automatiskt: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Som en befintlig kund kan du fortsätta att använda hälso funktionen på virtuella datorer som är anslutna till en befintlig installation av arbets ytan med hälso funktionen.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Jag använder VM-hälsa nu med en miljö och vill distribuera den till en ny

Om du är en befintlig kund som använder hälso funktionen och vill använda den för en ny lansering kan du kontakta oss på vminsights@microsoft.com att be om anvisningar.

## <a name="next-steps"></a>Nästa steg

Om du vill förstå de krav och metoder som hjälper dig att övervaka dina virtuella datorer kan du läsa [distribuera Azure Monitor for VMS](../insights/vminsights-enable-overview.md).
