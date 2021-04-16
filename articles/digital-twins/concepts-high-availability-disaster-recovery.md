---
title: Hög tillgänglighet och haveriberedskap
titleSuffix: Azure Digital Twins
description: Beskriver funktionerna i Azure och Azure Digital Twins som hjälper dig att skapa Azure IoT-lösningar med hög tillgänglig kapacitet för haveriberedskap.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 41edef58910fe2b772831ef083e5aca8bb52a321
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482276"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins hög tillgänglighet och haveriberedskap

Ett viktigt övervägande för motståndskraftiga IoT-lösningar är affärskontinuhet och haveriberedskap. Genom att **designa för hög tillgänglighet (HA)** och haveriberedskap **(DR)** kan du definiera och uppnå lämpliga drifttidsmål för din lösning.

I den här artikeln beskrivs funktionerna för ha och dr som erbjuds specifikt av Azure Digital Twins tjänsten.

Azure Digital Twins har stöd för följande funktionsalternativ:
* *Hög tillgänglighet inom regionen* – Inbyggd redundans för att leverera tjänstens drifttid
* *Haveri mellan* regioner – Redundans till en geo-länkad Azure-region i händelse av ett oväntat datacenterfel

Du kan också se avsnittet [*Metodtips för*](#best-practices) allmän Azure-vägledning om att utforma för HA/DR.

## <a name="intra-region-ha"></a>Hög hög regions-HA
 
Azure Digital Twins tillhandahåller ha inom regionen genom att implementera redundanser i tjänsten. Detta återspeglas i [serviceavtalet för](https://azure.microsoft.com/support/legal/sla/digital-twins) drifttid. **Inget ytterligare arbete krävs av utvecklarna av en Azure Digital Twins för att dra nytta av dessa FUNKTIONER för den här ha-lösningen.** Även Azure Digital Twins erbjuder en relativt hög drifttidsgaranti kan tillfälliga fel fortfarande förväntas, precis som med alla distribuerade databehandlingsplattformar. Lämpliga återförsöksprinciper bör vara inbyggda i de komponenter som interagerar med ett molnprogram för att hantera tillfälliga fel.

## <a name="cross-region-dr"></a>Dr i flera regioner

Det kan finnas sällsynta situationer när ett datacenter upplever längre avbrott på grund av strömavbrott eller andra händelser i regionen. Sådana händelser är sällsynta, och under sådana fel kanske inte ha-funktionen inom regionen som beskrivs ovan. Azure Digital Twins åtgärdar detta med Microsoft-initierad redundans.

**Microsoft-initierad redundans** används av Microsoft i sällsynta fall för att redundanskoppla alla Azure Digital Twins instanser från en berörd region till motsvarande geoparade region. Den här processen är ett standardalternativ (utan möjlighet för användare att välja bort) och kräver ingen åtgärd från användaren. Microsoft förbehåller sig rätten att fastställa när det här alternativet kommer att användas. Den här mekanismen omfattar inte ett användarmedgivande innan användarens instans har överfördr alltför ofta.

>[!NOTE]
> Vissa **Azure-tjänster** tillhandahåller också ytterligare ett alternativ som kallas kundinitierad redundans, vilket gör att kunder kan initiera en redundans endast för sin instans, till exempel att köra ett DR-programgranskning. Den här mekanismen stöds **för närvarande inte** av Azure Digital Twins. 

## <a name="monitor-service-health"></a>Övervaka tjänstens hälsa

När Azure Digital Twins instanserna har återställts kan du övervaka processen [](../service-health/service-health-overview.md) med hjälp av Azure Service Health verktyget. Service Health spårar hälsotillståndet för dina Azure-tjänster i olika regioner och prenumerationer och delar information som påverkar tjänster om avbrott och driftstopp.

Under en redundanshändelse kan Service Health ge en indikation om när tjänsten är ur drift och när den är tillbaka.

Om du vill Service Health händelser...
1. Gå till [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) i Azure Portal (du kan använda den här länken eller söka efter den med hjälp av portalens sökfält).
1. Använd den vänstra menyn för att växla till *sidan Hälsohistorik.*
1. Leta efter ett *problemnamn* som börjar **Azure Digital Twins** och välj det.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Skärmbild av Azure Portal som visar sidan Hälsohistorik. Det finns en lista över flera problem från de senaste dagarna och ett problem som kallas &quot;Azure Digital Twins – Europa, västra – Minimerad&quot; är markerat." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Allmän information om avbrottet finns på *fliken* Sammanfattning.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="På sidan Hälsohistorik är fliken Sammanfattning markerad. På fliken visas allmän information, till exempel den resurs som påverkades, dess region och dess prenumeration." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Mer information och uppdateringar om problemet över tid finns på fliken *Problemuppdateringar.*

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="På sidan Hälsohistorik är fliken Problemuppdateringar markerad. På fliken visas flera poster som visar aktuell status från en dag sedan." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Observera att informationen som visas i det här verktyget inte är specifik för en Azure Digital-instans. När du har använt Service Health för att förstå vad som händer med Azure Digital Twins-tjänsten i en viss region eller prenumeration kan du ta ett steg längre i övervakningen med hjälp av resource [health-verktyget](troubleshoot-resource-health.md) för att öka detaljnivå för specifika instanser och se om de påverkas.

## <a name="best-practices"></a>Bästa praxis

Metodtips för HA/DR finns i följande Azure-vägledning om det här ämnet: 
* I artikeln [*Teknisk vägledning för affärskontinuhet*](/azure/architecture/framework/resiliency/overview) i Azure beskrivs ett allmänt ramverk för att hjälpa dig att tänka på affärskontinuhet och haveriberedskap. 
* Dokumentet [*Haveriberedskap och hög tillgänglighet*](/azure/architecture/framework/resiliency/backup-and-recovery) för Azure-program ger arkitekturvägledning för strategier för Azure-program för att uppnå hög tillgänglighet (HA) och haveriberedskap (DR).

## <a name="next-steps"></a>Nästa steg 

Läs mer om att komma igång Azure Digital Twins lösningar:
 
* [*Vad är Azure Digital Twins?*](overview.md)
* [*Snabbstart: Utforska ett exempelscenario*](quickstart-azure-digital-twins-explorer.md)