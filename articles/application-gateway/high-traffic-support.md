---
title: Stöd för Application Gateway hög trafik volym
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar Azure Application Gateway som stöd för stora nätverks trafiks volym scenarier.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586120"
---
# <a name="application-gateway-high-traffic-support"></a>Stöd för höga trafikvolymer i Application Gateway

>[!NOTE]
> I den här artikeln beskrivs några rekommendationer som hjälper dig att konfigurera din Application Gateway för att hantera extra trafik för en hög trafik volym som kan uppstå. Tröskelvärden för aviseringar är enbart förslag och generiskt i beskaffenhet. Användarna kan fastställa tröskelvärden för aviseringar baserat på deras förväntningar på arbets belastning och användning.

Du kan använda Application Gateway med brand vägg för webbaserade program (WAF) för en skalbar och säker metod för att hantera trafik till dina webb program.

Det är viktigt att du skalar Application Gateway enligt din trafik och med en bit-till-buffert så att du är för beredd för trafik toppar eller toppar och minimerar påverkan som den kan ha i din QoS. Följande rekommendationer hjälper dig att konfigurera Application Gateway med WAF för att hantera extra trafik.

Se [statistik dokumentationen](./application-gateway-metrics.md) för en fullständig lista över mått som erbjuds av Application Gateway. Se [visualisera mått](./application-gateway-metrics.md#metrics-visualization) i Azure Portal och [Azure Monitor-dokumentationen](../azure-monitor/alerts/alerts-metric.md) om hur du ställer in aviseringar för mått.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Skalning för Application Gateway v1 SKU (standard/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Ange antalet instanser baserat på din högsta CPU-användning
Om du använder en v1 SKU-Gateway har du möjlighet att ställa in Application Gateway upp till 32 instanser för skalning. Kontrol lera din Application Gateways CPU-användning under den senaste månaden för alla toppar över 80% är den tillgänglig som ett mått för att övervaka. Vi rekommenderar att du ställer in antalet instanser enligt din högsta användning och med 10% till 20% ytterligare buffert för att redovisa eventuella trafik toppar.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Mått för v1-processor användning" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Använd v2-SKU över v1 för dess funktioner för automatisk skalning och prestanda för delar
V2-SKU: n erbjuder automatisk skalning för att säkerställa att Application Gateway kan skalas upp när trafiken ökar. Det ger också andra betydande prestanda för delar, till exempel 5x bättre TLS-avlastning, snabbare distribution och uppdaterings tider, zon redundans och mer jämfört med v1. Mer information finns i vår [v2-dokumentation](./application-gateway-autoscaling-zone-redundant.md) och i vår dokumentation om v1-till v2- [migrering](./migrate-v1-v2.md) för att lära dig hur du migrerar dina befintliga v1 SKU-gatewayer till v2-SKU: n. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Automatisk skalning för Application Gateway v2 SKU (Standard_v2/WAF_v2 SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Ange maximalt antal instanser till högsta möjliga (125)
 
För Application Gateway v2 SKU, ställer du in maximalt antal instanser på det maximala möjliga värdet 125, vilket gör att Application Gateway kan skalas ut efter behov. Detta gör det möjligt för IT att hantera den möjliga ökningen av trafiken till dina program. Du debiteras bara för de kapacitets enheter (CUs) som du använder. 

Se till att kontrol lera under näts storleken och antalet tillgängliga IP-adresser i ditt undernät och ange maximalt antal instanser baserat på det. Om ditt undernät inte har tillräckligt med utrymme för att rymma måste du återskapa din gateway i samma eller ett annat undernät som har tillräckligt med kapacitet. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="Konfiguration för automatisk skalning i v2" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Ange minsta antal instanser baserat på användning av genomsnittlig beräknings enhet

För Application Gateway v2-SKU tar automatisk skalning upp sex till sju minuter för att skala ut och etablera ytterligare uppsättning instanser som är redo att ta trafik. Tills, om det finns kort toppar i trafiken, kan dina befintliga Gateway-instanser komma under stress och detta kan orsaka oväntad fördröjning eller förlust av trafik. 

Vi rekommenderar att du ställer in minsta antal instanser på en optimal nivå. Om du t. ex. kräver 50 instanser för att hantera trafiken vid hög belastning, så är det en bra idé att ställa in minst 25 till 30 i stället för på <10 så att även när det finns kort trafik trafik, Application Gateway skulle kunna hantera den och ge tillräckligt med tid för automatisk skalning för att svara och börjar gälla.

Kontrol lera dina beräknings enhets mått under den senaste månaden. Compute Unit-mått är en representation av din gateways processor användning och utifrån din högsta användning dividerat med 10. du kan ange det minsta antalet instanser som krävs. Observera att 1 Application Gateway-instans kan hantera minst 10 beräknings enheter

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 beräknar mått för beräknings enheter" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Manuell skalning för Application Gateway v2 SKU: n (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Ange antalet instanser baserat på användningen av den högsta beräknings enheten 

Till skillnad från automatisk skalning, i manuell skalning, måste du manuellt ange antalet instanser av programgatewayen baserat på trafik kraven. Vi rekommenderar att du ställer in antalet instanser enligt din högsta användning och med 10% till 20% ytterligare buffert för att redovisa eventuella trafik toppar. Om din trafik till exempel kräver 50 instanser vid hög belastning, etablera 55 till 60-instanser för att hantera oväntade trafik toppar som kan uppstå.

Kontrol lera dina beräknings enhets mått under den senaste månaden. Compute Unit-mått är en representation av din gateways processor användning och baserat på din högsta användning dividerat med 10, du kan ange antalet instanser som krävs, eftersom 1 Application Gateway-instans kan hantera minst 10 beräknings enheter

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Om du vill få meddelanden om trafik eller användnings avvikelser kan du ställa in aviseringar för vissa mått. Se [statistik dokumentation](./application-gateway-metrics.md) för en fullständig lista över mått som erbjuds av Application Gateway. Se [visualisera mått](./application-gateway-metrics.md#metrics-visualization) i Azure Portal och [Azure Monitor-dokumentationen](../azure-monitor/alerts/alerts-metric.md) om hur du ställer in aviseringar för mått.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Aviseringar för Application Gateway v1 SKU (standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Avisera om Genomsnittlig CPU-belastning korsar 80%

Under normala förhållanden bör CPU-användningen inte regelbundet överskrida 90 %,eftersom detta kan orsaka långsamma svarstider på de webbplatser som Application Gateway är värd för, vilket kan vara frustrerande för kunderna. Du kan styra eller förbättra processor användningen genom att ändra konfigurationen för Application Gateway genom att öka antalet instanser eller genom att flytta till en större SKU-storlek eller göra båda. Ange en avisering om måttet för processor användning hamnar över 80% Average.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Varna om tröskelvärdet för antal felaktiga värdar överskrider tröskelvärdet

Det här måttet indikerar antalet backend-servrar som Application Gateway inte kan avsöka. Detta kommer att fånga problem där Application Gateway-instanser inte kan ansluta till Server delen. Avisera om det här talet hamnar över 20% av Server dels kapaciteten. T.ex. Om du för närvarande har 30 backend-servrar i sin backend-pool, ställer du in en avisering om antalet felaktiga värdar går över 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Varna om svars status (4xx, 5xx) överskrider tröskeln 

Skapa en avisering när Application Gateway svars status är 4xx eller 5xx. Det kan finnas tillfälliga 4xx-eller 5XX-svar som visas på grund av tillfälliga problem. Du bör Observera gatewayen i produktion för att fastställa det statiska tröskelvärdet eller använda dynamiskt tröskelvärde för aviseringen.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Varna om misslyckade förfrågningar överskrider tröskeln 

Skapa avisering när mått för misslyckade förfrågningar överskrider tröskelvärdet. Du bör Observera gatewayen i produktion för att fastställa det statiska tröskelvärdet eller använda dynamiskt tröskelvärde för aviseringen.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Exempel: Konfigurera en avisering för fler än 100 misslyckade begär Anden under de senaste 5 minuterna

Det här exemplet visar hur du använder Azure Portal för att ställa in en avisering när antalet misslyckade förfrågningar under de senaste 5 minuterna är mer än 100.
1. Navigera till din **Application Gateway**.
2. På den vänstra panelen väljer du **mått** på fliken **övervakning** . 
3. Lägg till ett mått för **misslyckade förfrågningar**.
4. Klicka på **ny varnings regel** och definiera villkor och åtgärder
5. Klicka på **skapa aviserings regel** för att skapa och aktivera aviseringen

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 skapa aviseringar" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Aviseringar för Application Gateway v2 SKU (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Avisera om användningen av beräknings enheter korsar 75% av genomsnittlig användning 

Compute Unit är måttet för att beräkna användningen av Application Gateway. Kontrol lera den genomsnittliga beräknings enhets användningen under den senaste månaden och ange en avisering om den korsar 75% av den. Om din genomsnittliga användning till exempel är 10 beräknings enheter, ställer du in en avisering på 7,5 CUs. Detta varnar dig om användningen ökar och ger dig tid att svara. Du kan öka det lägsta om du tror att den här trafiken kommer att behållas för att varna dig om att trafiken kan öka. Följ skalnings förslagen ovan för att skala ut efter behov.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exempel: Konfigurera en avisering på 75% av genomsnittligt CU-användning

Det här exemplet visar hur du använder Azure Portal för att ställa in en avisering när 75% av genomsnittligt CU-användning uppnås. 
1. Navigera till din **Application Gateway**.
2. På den vänstra panelen väljer du **mått** på fliken **övervakning** . 
3. Lägg till ett mått för **Genomsnittligt antal aktuella beräknings enheter**. 
4. Om du har angett att det minsta antalet instanser ska vara den genomsnittliga CU-användningen kan du gå vidare och ange en avisering när 75% av de minsta instanserna används. Om den genomsnittliga användningen till exempel är 10 CUs, ställer du in en avisering på 7,5 CUs. Detta varnar dig om användningen ökar och ger dig tid att svara. Du kan öka det lägsta om du tror att den här trafiken kommer att behållas för att varna dig om att trafiken kan öka. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 Compute Unit-aviseringar" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> Du kan ställa in aviseringen på ett lägre eller högre antal CU i procent beroende på hur känsliga du vill ha till gång till potentiella trafik toppar.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Avisera om kapacitets enhets användningen korsar 75% av topp användningen 

Kapacitets enheter representerar total Gateway-användning med avseende på data flöde, beräkning och antal anslutningar. Kontrol lera din maximala kapacitets enhets användning under den senaste månaden och ange en avisering om den korsar 75% av den. Om din maximala användning till exempel är 100 kapacitets enheter, ställer du in en avisering på 75 CUs. Följ de två förslagen ovan för att skala ut efter behov.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Varna om tröskelvärdet för antal felaktiga värdar överskrider tröskelvärdet 

Det här måttet indikerar antalet backend-servrar som Application Gateway inte kan avsöka. Detta kommer att fånga problem där Application Gateway-instanser inte kan ansluta till Server delen. Avisera om det här talet hamnar över 20% av Server dels kapaciteten. T.ex. Om du för närvarande har 30 backend-servrar i sin backend-pool, ställer du in en avisering om antalet felaktiga värdar går över 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Varna om svars status (4xx, 5xx) överskrider tröskeln 

Skapa en avisering när Application Gateway svars status är 4xx eller 5xx. Det kan finnas tillfälliga 4xx-eller 5XX-svar som visas på grund av tillfälliga problem. Du bör Observera gatewayen i produktion för att fastställa det statiska tröskelvärdet eller använda dynamiskt tröskelvärde för aviseringen.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Varna om misslyckade förfrågningar överskrider tröskeln 

Skapa avisering när mått för misslyckade förfrågningar överskrider tröskelvärdet. Du bör Observera gatewayen i produktion för att fastställa det statiska tröskelvärdet eller använda dynamiskt tröskelvärde för aviseringen.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Varna om sista byte svars tid för Server delen överskrider tröskelvärdet 

Det här måttet anger tidsintervallet mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten. Skapa en avisering om svars fördröjningen för Server delen är högre än vad som är vanligt. Ange till exempel att det ska aviseras när svars tiden för Server delen ökar med mer än 30% från det vanliga värdet.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Varna om tröskeln för Application Gateway totala tiden överskrider tröskelvärdet

Detta är intervallet från den tidpunkt då Application Gateway tar emot den första byten i HTTP-begäran till den tidpunkt då den senaste svars byte har skickats till klienten. Bör skapa en avisering om svars fördröjningen för Server delen är högre än vad som är vanligt. De kan till exempel ställa in att de ska aviseras när den totala tids fördröjningen ökar med mer än 30% från det vanliga värdet.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Konfigurera WAF med geo-filtrering och bot-skydd för att stoppa attacker
Om du vill ha ett extra säkerhets lager framför ditt program, använder du Application Gateway WAF_v2 SKU för WAF-funktioner. Du kan konfigurera v2-SKU: n så att den endast tillåter åtkomst till dina program från ett specifikt land/region eller länder/regioner. Du ställer in en anpassad WAF-regel för att explicit tillåta eller blockera trafik baserat på Geo-platsen. Mer information finns i [regler för geo-filtrering](../web-application-firewall/ag/geomatch-custom-rules.md) och [Konfigurera anpassade regler för Application Gateway WAF_v2 SKU via PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Aktivera bot-skydd för att blockera kända dåliga robotar. Detta bör minska mängden trafik som får till ditt program. Mer information finns i [bot-skydd med konfigurera instruktioner](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Aktivera diagnostik på Application Gateway och WAF

Med diagnostikloggar kan du Visa brand Väggs loggar, prestanda loggar och åtkomst loggar. Du kan använda dessa loggar i Azure för att hantera och felsöka programgatewayer. Mer information finns i vår [diagnostiska dokumentation](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Konfigurera en TLS-princip för extra säkerhet
Se till att du använder den senaste TLS-[AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)(TLS policy version). Detta tillämpar TLS 1,2 och starkare chiffer. Mer information finns i [Konfigurera TLS-principinställningar och cipher-paket via PowerShell](./application-gateway-configure-ssl-policy-powershell.md).