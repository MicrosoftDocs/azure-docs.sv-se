---
title: Översikt över Azure Management – Azure-styrning
description: Översikt över hanterings områden för Azure-program och-resurser med länkar till innehåll på hanterings verktyg för Azure.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593933"
---
# <a name="what-are-the-azure-management-areas"></a>Vad är Azures hanterings områden?

Styrning i Azure är en aspekt av Azure-hantering. Den här artikeln beskriver olika hanterings områden för att distribuera och underhålla dina resurser i Azure.

Hantering syftar på uppgifter och processer som krävs för att underhålla företagsprogram samt resurser som stöder dessa program. Azure har många tjänster och verktyg som samarbetar för att tillhandahålla fullständig hantering. Dessa tjänster är inte bara för resurser i Azure, utan även i andra moln och lokalt. Att förstå de olika verktygen och hur de fungerar tillsammans är det första steget i utformningen av en komplett hanterings miljö.

Följande diagram visar de olika hanteringsområdena som krävs för att underhålla alla program och resurser. Dessa olika områden kan ses som en livs cykel. Varje zon krävs i kontinuerliga framgångar över livs längd för en resurs. Den här resurs livs cykeln börjar med den första distributionen, genom fortsatta åtgärder och slutligen när den dras tillbaka.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Diagram över ämnes områden för hantering i Azure." border="false":::
   Diagram som visar elementen migrera, säkra, skydda, övervaka, konfigurera och styr de delar av tjänsternas hjul som stöder hantering och styrning i Azure. Säker har säkerhets hantering och skydd mot hot som under objekt. Skydda har säkerhets kopiering och haveri beredskap som under objekt. Övervakaren har app, infraröd och nätverks övervakning och Log Analytics och diagnostik som under objekt. Konfigurera har konfiguration, uppdaterings hantering, automatisering och skript som under objekt. Och regler har princip hantering och kostnads hantering som under objekt.
:::image-end:::

Ingen enskild Azure-tjänst fyller fullständigt kraven i ett visst hanterings utrymme. I stället realiseras varje tjänst av flera tjänster som arbetar tillsammans. Vissa tjänster, till exempel Application Insights, tillhandahåller riktade övervaknings funktioner för webb program. Andra, som Azure Monitor loggar, lagrar hanterings data för andra tjänster. Med den här funktionen kan du analysera data av olika typer som samlas in av olika tjänster.

Följande avsnitt beskriver i korta ordalag de olika hanteringsområdena och tillhandahåller länkar till detaljerat innehåll om de viktigaste Azure-tjänsterna.

## <a name="monitor"></a>Monitor

Övervakning handlar om att samla in och analysera data för att granska prestanda, hälsa och tillgänglighet för dina resurser. En effektiv övervaknings strategi hjälper dig att förstå driften av komponenter och öka din drift tid med meddelanden. Läs en översikt över övervakning som täcker de olika tjänsterna som används vid [övervakning av Azure-program och-resurser](../azure-monitor/overview.md).

## <a name="configure"></a>Konfigurera

Konfigurera syftar på den första distributionen och konfigurationen av resurser och pågående underhåll.
Genom att automatisera dessa uppgifter kan du undvika redundans, minimera din tid och ansträngning och öka din precision och effektivitet. [Azure Automation](../automation/automation-intro.md) innehåller många tjänster för att automatisera konfigurationsåtgärder. Medan Runbooks hanterar process automatisering, hjälper konfiguration och uppdaterings hantering att hantera konfigurationen.

## <a name="govern"></a>Styrning

Styrning tillhandahåller mekanismer och processer för att behålla kontrollen över dina program och resurser i Azure. Det omfattar att planera initiativ och att fatta beslut om strategiska prioriteringar.
Styrning i Azure implementeras främst genom två tjänster. Med [Azure policy](./policy/overview.md) kan du skapa, tilldela och hantera princip definitioner för att tillämpa regler för dina resurser.
Den här funktionen håller resurserna i överensstämmelse med företagets standarder.
Med [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kan du spåra moln användning och utgifter för dina Azure-resurser och andra moln leverantörer.

## <a name="secure"></a>Säkerhet

Hantera säkerheten för dina resurser och data. Ett säkerhets program omfattar att utvärdera hot, samla in och analysera data och efterlevnad av dina program och resurser. Säkerhets övervakning och hot analys tillhandahålls av [Azure Security Center](../security-center/security-center-introduction.md), som innehåller enhetlig säkerhets hantering och Avancerat skydd för arbets belastningar i hybrid moln. Mer information och vägledning om hur du skyddar Azure-resurser finns i [Introduktion till Azure-säkerhet](../security/fundamentals/overview.md) .

## <a name="protect"></a>Skydda

Skydd syftar till att hålla dina program och data tillgängliga, även med avbrott som ligger utanför din kontroll. Skydd i Azure tillhandahålls av två tjänster. [Azure Backup](../backup/backup-overview.md) tillhandahåller säkerhetskopiering och återställning av data, antingen i molnet eller lokalt. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) ger affärs kontinuitet och omedelbar återställning under en katastrof.

## <a name="migrate"></a>Migrera

Migrering refererar till att överföra arbetsbelastningar som körs lokalt till Azure-molnet.
[Azure Migrate](../migrate/migrate-services-overview.md) är en tjänst som hjälper dig att utvärdera migreringens lämplighet för lokala virtuella datorer till Azure. Azure Site Recovery migrerar virtuella datorer [från lokala platser](../site-recovery/migrate-tutorial-on-premises-azure.md) eller [från Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database migration](../dms/dms-overview.md) hjälper dig att migrera databas källor till Azure Data Platforms.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-styrning finns i följande artiklar:

- Se [Azure styrnings hubben](./index.yml).
- Se [styrning i moln implementerings ramverket för Azure](/azure/cloud-adoption-framework/govern/)