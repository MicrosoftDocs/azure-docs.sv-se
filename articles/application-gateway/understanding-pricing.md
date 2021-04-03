---
title: Förstå priser – Azure Application Gateway
description: 'Den här artikeln beskriver fakturerings processen för Azure Application Gateway och brand vägg för webbaserade program för båda v1-till v2-SKU: er'
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 89ba6b7a69c95951a083628f23be68d811c7768c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601619"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Förstå priser för Azure Application Gateway och brand vägg för webbaserade program

>[!NOTE]
>Priserna som visas i den här artikeln är exempel och är endast i illustrations syfte. Information om priser enligt din region finns på sidan med [priser](https://azure.microsoft.com/pricing/details/application-gateway/).

Azure Application Gateway är en belastnings Utjämnings lösning på Layer 7, som möjliggör skalbar, hög tillgänglig och säker leverans av webb program på Azure.

Det finns inga start avgifter eller avgångs kostnader som är kopplade till Application Gateway.
Du faktureras bara för de resurser som är företablerade och används baserat på den faktiska förbrukningen per timme. Kostnader som är kopplade till Application Gateway delas in i två komponenter: fasta kostnader och rörliga kostnader. De faktiska kostnaderna i varje komponent kan variera beroende på vilken SKU som används.

I den här artikeln beskrivs kostnader som är kopplade till varje SKU och vi rekommenderar att användare använder det här dokumentet för att planera och hantera kostnader som är kopplade till Azure Application Gateway.

## <a name="v1-skus"></a>V1 SKU: er

Standard Application Gateway och WAF v1 SKU: er faktureras som en kombination av:

* Fast kostnad

    Kostnaden baserat på den tid som en viss typ av Application Gateway/WAF har tillhandahållits och körs för bearbetnings begär Anden.
    Den fasta kostnads komponenten tar hänsyn till följande faktorer:
    * Nivå – standard Application Gateway eller WAF
    * Storlek – liten, medel & stor
    * Antal instanser – antal instanser som ska distribueras

    För N-instanser är de associerade kostnaderna N * kostnad för en instans av en viss nivå (standard & WAF) & storlek (liten, medel & stor) kombination.

* Rörlig kostnad

    Kostnaden baserat på mängden data som bearbetas av Application Gateway-/WAF. Både begäran och svars byte som bearbetas av Application Gateway skulle övervägas för fakturering.

Total kostnad = fast kostnad + rörlig kostnad

### <a name="standard-application-gateway"></a>Standard Application Gateway

Fast kostnad & rörlig kostnad beräknas enligt Application Gateway typ.
I följande tabell visas exempel priser baserade på en ögonblicks bild av priserna för USA, östra USA, som endast är avsedda för illustration.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fast kostnad (pris för USA, östra)

|              Application Gateway typ             |  Kostnader ($/timme)  |
| ------------------------------------------------- | ---------------|
|                     Liten                         |    $0,025      |
|                     Medel                        |    $0,07       |
|                     Stor                         |    $0,32       |

De beräknade månads priserna baseras på 730 timmars användning per månad.

#### <a name="variable-cost-east-us-region-pricing"></a>Rörlig kostnad (pris för USA, östra)

|              Bearbetade data             |  Liten ($/GB)  |  Medel ($/GB) |  Stor ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Första 10 TB/månad                       |     $0,008     |      Kostnadsfri      |     Kostnadsfri      |
| Nästa 30 TB (10 – 40 TB)/månad             |     $0,008     |     $0,007     |     Kostnadsfri      |
| Över 40 TB/månad                        |     $0,008     |     $0,007     |     $0,0035   |

Mer information om priser enligt din region finns på sidan med [priser](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Fasta kostnader & rörliga kostnader beräknas enligt den etablerade Application Gateway typen.

I följande tabell visas exempel priser baserade på en ögonblicks bild av priserna för USA, östra USA och endast för illustration.

#### <a name="fixed-cost-east-us-region-pricing"></a>Fast kostnad (pris för USA, östra)

|              Application Gateway typ             |  Kostnader ($/timme)  |
| ------------------------------------------------- | ---------------|
|                     Liten                         |       NA       |
|                     Medel                        |     $0,126     |
|                     Stor                         |     $0,448     |

De beräknade månads priserna baseras på 730 timmars användning per månad.

#### <a name="variable-cost-east-us-region-pricing"></a>Rörlig kostnad (pris för USA, östra)

|              Bearbetade data             |  Liten ($/GB)  |  Medel ($/GB) |  Stor ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Första 10 TB/månad                       |     $0,008     |      Kostnadsfri      |     Kostnadsfri      |
| Nästa 30 TB (10 – 40 TB)/månad             |     $0,008     |     $0,007     |     Kostnadsfri      |
| Över 40 TB/månad                        |     $0,008     |     $0,007     |     $0,0035   |

Mer information om priser enligt din region finns på sidan med [priser](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Utgående data överföringar – data som går ut från Azures Data Center från programgatewayer debiteras enligt standard [priser för data överföring](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Exempel 1 (a) – standard Application Gateway med 1 instans antal

Vi antar att du har skapat en standard-Application Gateway av medelhög typ med 1 instans och att den bearbetar 500 GB under en månad. Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

Fast pris = $0,07 * 730 (timmar) = $51,1 de beräknade månads priserna baseras på 730 timmars användning per månad.

Variabla kostnader = kostnads fri (medelhög nivå har inga kostnader för de första 10 TB bearbetade per månad) totalkostnad = $51,1 + 0 = $51,1 

> [!NOTE]
> För att stödja scenarier med hög tillgänglighet måste du konfigurera minst 2 instanser för v1 SKU: er. Se [SLA för Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Exempel 1 (b) – standard Application Gateway med instans antal för > 1

Vi antar att du har skapat en standard Application Gateway av medelhög typ med fem instanser och att den bearbetar 500 GB under en månad. Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

Fast pris = 5 (antal instanser) * $0,07 * 730 (timmar) = $255,5 beräknade månads priser baseras på 730 timmars användning per månad.

Variabla kostnader = kostnads fri (medelhög nivå har inga kostnader för de första 10 TB bearbetade per månad) totalkostnad = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Exempel 2 – WAF Application Gateway

Vi antar att du har skapat en liten standard Application Gateway och en stor typ av WAF Application Gateway under de första 15 dagarna i månaden. Den lilla Application Gateway bearbetar 15 TB under den tid som den är aktiv och den stora WAF-programgatewayen bearbetar 100 TB under den tid som den är aktiv. Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande: 

###### <a name="small-instance-standard-application-gateway"></a>Liten instans standard Application Gateway

24 timmar * 15 dagar = 360 timmar

Fast pris = $0,025 * 360 (timmar) = $9

Variabla kostnader = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Totalkostnad = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Stor instans WAF Application Gateway
24 timmar * 15 dagar = 360 timmar

Fast pris = $0,448 * 360 (timmar) = $161,28

Variabla kostnader = 60 * 1000 * $0.0035/GB = $210 (stor nivå har inga kostnader för de första 40 TB som bearbetas per månad)

Totalkostnad = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>V2 SKU: er  

Application Gateway v2-och WAF v2-SKU: er stöder automatisk skalning och garanterar hög tillgänglighet som standard.

### <a name="key-terms"></a>Viktiga termer

##### <a name="capacity-unit"></a>Kapacitets enhet 
Kapacitets enhet är mått för kapacitets användning för en Application Gateway över flera parametrar.

En enskild kapacitets enhet består av följande parametrar:
* 2500 beständiga anslutningar
* 2,22 – Mbps-genomflöde
* 1 beräknings enhet

Om någon av dessa parametrar överskrids, krävs en annan n kapacitets enhet (er), även om de andra två parametrarna inte överskrider den här gränsen för en enskild kapacitets enhet.
Parametern med den högsta användningen bland de tre ovan kommer att användas internt för att beräkna kapacitets enheter, vilket i sin tur faktureras.

##### <a name="compute-unit"></a>Compute-enhet
Compute Unit är måttet på förbrukad beräknings kapacitet. Faktorer som påverkar förbrukningen av beräknings enheter är TLS-anslutningar/s, URL-omskrivning och bearbetning av WAF-regler. Antalet begär Anden som en beräknings enhet kan hantera beror på olika kriterier som TLS-certifikat nyckel storlek, algoritm för nyckel utbyte, huvud skrivning av huvuden och i händelse av WAF-inkommande begär ande storlek.

Vägledning för beräknings enhet:
* Standard_v2 – varje beräknings enhet kan ha cirka 50 anslutningar per sekund med RSA 2048-bitars TLS-certifikat.

* WAF_v2 – varje beräknings enhet har stöd för cirka 10 samtidiga begär Anden per sekund för 70-30% blandning av trafik med 70% begär Anden som är mindre än 2 KB GET/POST och återstående högre. WAF prestanda påverkas inte av svars storleken för närvarande.

##### <a name="instance-count"></a>Antal instanser 
För etablering av resurser för Application Gateway v2 SKU: er definieras i antal instanser. Varje instans garanterar minst 10 kapacitets enheter vad gäller bearbetnings kapacitet. Samma instans kan eventuellt stödja fler än 10 kapacitets enheter för olika trafik mönster beroende på kapacitets enhetens parametrar.

Manuellt definierade skalning och begränsningar som angetts för autoskalning (minimum eller maximum) anges i antal instanser. Den manuellt inställda skalningen för instans antal och det minsta antalet instanser i autoskalning config reserverar 10 kapacitets enheter/instans. Dessa reserverade CUs kommer att faktureras så länge som Application Gateway är aktiv oavsett den faktiska resursförbrukning. Om den faktiska förbrukningen överskrider tröskelvärdet för 10 kapacitets enheter/instanser debiteras ytterligare kapacitets enheter under variabel komponenten.

V2 SKU: er faktureras baserat på förbrukningen och består av två delar:

* Fasta kostnader

    Kostnaden baserat på den tid som Application Gateway v2-/WAF v2 har tillhandahållits och är tillgänglig för bearbetning av begär Anden. Detta säkerställer hög tillgänglighet – även om 0 instanser är reserverade genom att ange 0 i minsta antal instanser som en del av autoskalning. 
    
    Den fasta kostnaden inkluderar också kostnaden som är kopplad till den offentliga IP-adressen som är kopplad till Application Gateway.

    Antalet instanser som körs vid en viss tidpunkt betraktas inte som en faktor för fasta kostnader för v2 SKU: er. De fasta kostnaderna för att köra en Standard_V2 (eller WAF_V2) blir desamma per timme oavsett hur många instanser som körs inom samma Azure-region.

* Kapacitets enhets kostnader 

    Kostnaden baserat på antalet kapacitets enheter som antingen reserver ATS eller används och som krävs för att bearbeta inkommande begär Anden.  Förbruknings kostnader beräknas per timme.

Totala kostnader = fasta kostnader + kapacitets enhets kostnader

> [!NOTE]
> En del av en timme debiteras som en hel timme.

I följande tabell visas exempel priser baserade på en ögonblicks bild av priserna för USA, östra USA och endast för illustration.

#### <a name="fixed-costs-east-us-region-pricing"></a>Fasta kostnader (priser för USA, östra)

|              V2 SKU             |  Kostnader ($/timme)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

De beräknade månads priserna baseras på 730 timmars användning per månad.

#### <a name="variable-costs-east-us-region-pricing"></a>Variabla kostnader (priser för USA, östra)

|        Kapacitets enhet         |  Standard_V2 ($/timme)  |  WAF_V2 ($/timme) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $0,008        |     $0,0144    |

Mer information om priser enligt din region finns på sidan med [priser](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Utgående data överföringar – data som går ut från Azures Data Center från programgatewayer debiteras enligt standard [priser för data överföring](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="example-1-a--manual-scaling"></a>Exempel 1 (a) – manuell skalning 
Vi antar att du har skapat en Standard_V2 Application Gateway med manuell skalning inställd på 8 instanser under hela månaden. Under den här tiden får den ett genomsnitt på 88,8-Mbit/s-data överföring.

Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

1 CU kan hantera 2,22-Mbit/s-genomflöde.

CUs krävs för att hantera 88,8 Mbit/s = 88,8/2,22 = 40 CUs 

Företablerade CUs = 8 (antal instanser) * 10 = 80 

Sedan 80 (reserverad kapacitet) > 40 (nödvändig kapacitet) krävs ingen ytterligare CUs. 

Fast pris = $0,246 * 730 (timmar) = $179,58

Variabla kostnader = $0,008 * 8 (instans enheter) * 10 (kapacitets enheter) * 730 (timmar) = $467,2

Totalkostnad = $179,58 + $467,2 = $646,78

![Diagram över manuell skalning 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Exempel 1 (b) – manuell skalning med trafik som går utöver den etablerade kapaciteten

Vi antar att du har skapat en Standard_V2 Application Gateway med manuell skalning inställd på 3 instanser under hela månaden. Under den här tiden får den ett genomsnitt på 88,8-Mbit/s-data överföring.

Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

1 CU kan hantera 2,22 Mbit/s genom strömning.

CUs krävs för att hantera 88,8 Mbit/s = 88,8/2,22 = 40 

Företablerade CUs = 3 (antal instanser) * 10 = 30 

Sedan 40 (nödvändig kapacitet) > 30 (reserverad kapacitet) krävs ytterligare CUs.
Antalet ytterligare CUs som används beror på den kostnads fria kapaciteten som är tillgänglig för varje instans.

Om bearbetnings kapacitet som motsvarar ytterligare 10 CUs var tillgänglig för användning inom de 3 reserverade instanserna.

Fast pris = $0,246 * 730 (timmar) = $179,58

Variabla kostnader = $0,008 * (3 (instans enheter) * 10 (kapacitets enheter) + 10 (ytterligare kapacitets enheter)) * 730 (timmar) = $233,6

Totalkostnad = $179,58 + $233,6 = $413,18

Men om bearbetnings kapaciteten motsvarar endast mer än 7 ytterligare CUs var tillgänglig för användning inom de 3 reserverade instanserna.
I det här scenariot är Application Gateway-resursen under skalad och kan eventuellt leda till ökad fördröjning eller begär Anden som tas bort.

Fast pris = $0,246 * 730 (timmar) = $179,58

Variabla kostnader = $0,008 * (3 (instans enheter) * 10 (kapacitets enheter) + 7 (ytterligare kapacitets enheter)) * 730 (timmar) = $216,08

Totalkostnad = $179,58 + $216,08 = $395,66


![Diagram över manuell skalning 2.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> I händelse av manuell skalning kan eventuella ytterligare förfrågningar som överskrider den maximala bearbetnings kapaciteten för de reserverade instanserna orsaka att ditt program är tillgängligt. I situationer med hög belastning kan reserverade instanser kunna tillhandahålla mer än 10 kapacitets enheter för bearbetnings kapacitet, beroende på konfigurationen och typen av inkommande begär Anden. Men vi rekommenderar att du etablerar antalet instanser enligt dina trafik krav.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Exempel 2 – WAF_V2 instans med automatisk skalning

Vi antar att du har etablerad en WAF_V2 med automatisk skalning aktiverat och ställt in det lägsta antalet instanser på 6 för hela månaden. Belastningen på begäran har orsakat att WAF-instansen skalas ut och utnyttjar 65 kapacitets enheter (skala ut från 5 kapacitets enheter, medan 60 enheter har reserver ATS) för hela månaden.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

De beräknade månads priserna baseras på 730 timmars användning per månad.

Fast pris = $0,443 * 730 (timmar) = $323,39

Variabla kostnader = $0,0144 * 65 (kapacitets enheter) * 730 (timmar) = $683,28

Totalkostnad = $323,39 + $683,28 = $1006,67

![Diagram över automatisk skalning 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Den faktiska trafik som observerats för din Application Gateway är troligen inte att ha ett sådant konstant mönster av trafik och den observerade belastningen på din Application Gateway skulle variera beroende på faktisk användning.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Exempel 3 (a) – WAF_V2 instans med autoskalning och 0 lägsta skalnings konfiguration

Vi antar att du har etablerad en WAF_V2 med automatisk skalning aktiverat och angett det lägsta antalet instanser som 0 för hela månaden. Belastningen på WAF är minimal men alltid tillgänglig per timme under hela månaden. Belastningen är lägre än kapaciteten för en enskild kapacitets enhet.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

De beräknade månads priserna baseras på 730 timmars användning per månad.

Fast pris = $0,443 * 730 (timmar) = $323,39

Variabla kostnader = $0,0144 * 1 (kapacitets enheter) * 730 (timmar) = $10,512

Totalkostnad = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Exempel 3 (b) – WAF_V2 instans med autoskalning med 0 minuters antal instanser

Vi antar att du har skapat en WAF_V2 med automatisk skalning aktiverat och ställt in det lägsta antalet instanser till 0 under hela månaden. Det finns dock 0 trafik som riktas mot WAF-instansen under hela månaden.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

Fast pris = $0,443 * 730 (timmar) = $323,39

Variabla kostnader = $0,0144 * 0 (kapacitets enheter) * 730 (timmar) = $0

Totalkostnad = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Exempel 3 (C) – WAF_V2 instans med manuell skalning inställt på 1 instans

Anta att du har skapat en WAF_V2 och ställt in den på manuell skalning med det minsta acceptabla värdet på 1 instans för hela månaden. Det finns dock 0 trafik som är riktad mot WAF för hela månaden.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

De beräknade månads priserna baseras på 730 timmars användning per månad.

Fast pris = $0,443 * 730 (timmar) = $323,39

Variabla kostnader = $0,0144 * 1 (antal instanser) * 10 (kapacitets enheter) * 730 (timmar) = $105,12

Totalkostnad = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Exempel 4 – WAF_V2 med automatisk skalning, kapacitets enhets beräkningar

Vi antar att du har skapat en WAF_V2 med automatisk skalning aktiverat och ställt in det lägsta antalet instanser till 0 under hela månaden. Under den här tiden får den 25 nya TLS-anslutningar per sekund med en genomsnittlig 8,88-Mbit/s-data överföring.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

De beräknade månads priserna baseras på 730 timmars användning per månad.

Fast pris = $0,443 * 730 (timmar) = $323,39

Variabla kostnader = $0,0144 * 730 (timmar) * {max (25/50, 8.88/2.22)} = $23,36 (4 kapacitets enheter som krävs för att hantera 8,88 Mbit/s)

Totalkostnad = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Exempel 5 (a) – Standard_V2 med automatisk skalning, tidsbaserade beräkningar

Vi antar att du har etablerad en standard_V2 med automatisk skalning aktiverat och ställt in det lägsta antalet instanser på 0 och att denna Programgateway är aktiv i 2 timmar.
Under den första timmen tar den emot trafik som kan hanteras av 10 kapacitets enheter och under den andra timmen tar den emot trafik som krävde 20 kapacitets enheter för att hantera belastningen.
Dina Application Gateway kostnader enligt ovan nämnda priser beräknas enligt följande:

Fast pris = $0,246 * 2 (timmar) = $0,492

Variabla kostnader = $0,008 * 10 (kapacitets enheter) * 1 (timmar) + $0,008 * 20 (kapacitets enheter) * 1 (timmar) = $0,24

Totalkostnad = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Övervaka fakturerings användning

Du kan visa förbruknings mängden för olika parametrar (beräknings enhet, data flöde & beständiga anslutningar) samt de kapacitets enheter som används som en del av Application Gateway måtten under avsnittet **övervakning** .

![Diagram över mått-avsnittet.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Användbara mått för kostnads uppskattning

* Aktuella kapacitetsenheter

    Antal kapacitets enheter som används för att belastningsutjämna trafiken mellan de tre parametrarna – aktuella anslutningar, data flöde och beräknings enhet

* Fasta fakturerbara kapacitetsenheter

    Det minsta antal kapacitets enheter som hålls etablerade enligt inställningen för minsta antal instanser (en instans översätts till 10 kapacitets enheter) i Application Gateway-konfigurationen.

* Uppskattade fakturerade kapacitetsenheter

    Uppskattade fakturerade kapacitetsenheter indikerar antalet kapacitetsenheter som faktureringen beräknas med. Detta beräknas som det större värdet mellan Aktuella kapacitetsenheter (kapacitetsenheter som krävs för att belastningsutjämna trafiken) och Fasta fakturerbara kapacitetsenheter (lägsta antalet kapacitetsenheter som är etablerade).

Fler mått, till exempel data flöde, aktuella anslutningar och beräknings enheter, är också tillgängliga för att förstå Flask halsar och beräkna antalet kapacitets enheter som krävs. Detaljerad information finns på [Application Gateway mått](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Exempel – beräkna kapacitets enheter som används

**Observerade mått:**

* Compute-enheter = 17,38
* Data flöde = 1.37 M byte/SEK – 10,96 Mbit/s
* Aktuella anslutningar = 123.08 k
* Beräknade kapacitets enheter = max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Observerade kapacitets enheter i mått = 49,23

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns mer information om hur prissättning fungerar i Azure Application Gateway:

* [Sidan priser för Azure Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Pris kalkylator för Azure Application Gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
