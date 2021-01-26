---
title: Övervaka, diagnostisera och Felsök Azure Storage | Microsoft Docs
description: Använd funktioner som lagrings analys, loggning på klient sidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 50d78e83bbbeb4b0252c83f9f52e94599ea6946c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787968"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Övervaka, diagnostisera och felsök Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Översikt
Att diagnostisera och felsöka problem i ett distribuerat program som finns i en moln miljö kan vara mer komplext än i traditionella miljöer. Program kan distribueras i en PaaS-eller IaaS-infrastruktur, lokalt, på en mobil enhet eller i en kombination av dessa miljöer. Normalt kan programmets nätverks trafik passera offentliga och privata nätverk och programmet kan använda flera lagrings tekniker som Microsoft Azure Storage tabeller, blobbar, köer eller filer förutom andra data lager, till exempel Relations-och dokument databaser.

För att kunna hantera sådana program kan du övervaka dem proaktivt och förstå hur du diagnostiserar och felsöker alla aspekter av dem och deras beroende tekniker. Som användare av Azure Storage-tjänster bör du kontinuerligt övervaka de lagrings tjänster som programmet använder för oväntade ändringar i beteendet (till exempel långsammare än vanliga svars tider) och använda loggning för att samla in mer detaljerade data och analysera ett problem i djupet. Diagnostikinformation som du får från både övervakning och loggning hjälper dig att avgöra rotor saken till problemet som ditt program har påträffat. Sedan kan du felsöka problemet och fastställa lämpliga steg som du kan vidta för att åtgärda det. Azure Storage är en kärn tjänst i Azure och utgör en viktig del av de flesta lösningar som kunderna distribuerar till Azure-infrastrukturen. Azure Storage innehåller funktioner för att förenkla övervakning, diagnostisering och fel sökning av lagrings problem i dina molnbaserade program.

* [Introduktion]
  * [Hur den här guiden organiseras]
* [Övervaka lagrings tjänsten]
  * [Övervaknings tjänstens hälsa]
  * [Övervaknings kapacitet]
  * [Övervaka tillgänglighet]
  * [Övervaka prestanda]
* [Diagnostisera lagrings problem]
  * [Problem med tjänst hälsa]
  * [Prestanda problem]
  * [Diagnostisera fel]
  * [Problem med Storage-emulator]
  * [Lagrings loggnings verktyg]
  * [Använda verktyg för nätverks loggning]
* [Spårning från slut punkt till slut punkt]
  * [Korrelera logg data]
  * [ID för klientförfrågan]
  * [ID för serverbegäran]
  * [Tidsstämplar]
* [Fel söknings vägledning]
  * [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
  * [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
  * [Mätningar visar ett högt värde för AverageServerLatency]
  * [Du upplever oväntade fördröjningar i en köad meddelandeleverans]
  * [Mätningar visar en ökning i PercentThrottlingError]
  * [Mätningar visar en ökning i PercentTimeoutError]
  * [Mätningar visar en ökning i PercentNetworkError]
  * [Klienten tar emot HTTP 403-meddelanden (förbjudet)]
  * [Klienten tar emot HTTP 404-meddelanden (hittades inte)]
  * [Klienten tar emot HTTP 409-meddelanden (konflikt)]
  * [Mått visar poster med låg PercentSuccess eller Analytics-logg har åtgärder med transaktions status ClientOtherErrors]
  * [Kapacitets mått visar en oväntad ökning av lagrings kapacitets användningen]
  * [Ditt problem uppstår när du använder Storage-emulatorn för utveckling eller testning]
  * [Du påträffar problem med att installera Azure SDK för .NET]
  * [Du har ett annat problem med en lagrings tjänst]
  * [Felsöka virtuella hård diskar på virtuella Windows-datorer](../../virtual-machines/troubleshooting/index.yml)   
  * [Felsöka virtuella hård diskar på virtuella Linux-datorer](../../virtual-machines/troubleshooting/index.yml)
  * [Felsöka Azure Files problem med Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Felsöka Azure Files problem med Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Bilagor]
  * [Bilaga 1: använda Fiddler för att avbilda HTTP-och HTTPS-trafik]
  * [Bilaga 2: använda wireshark för att avbilda nätverks trafik]
  * [Bilaga 4: använda Excel för att visa mått och logg data]
  * [Bilaga 5: övervakning med Application Insights för Azure-DevOps]

## <a name="introduction"></a><a name="introduction"></a>Insättning
Den här guiden visar hur du använder funktioner som Azure-lagringsanalys, loggning på klient sidan i Azure Storage klient bibliotek och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Storage relaterade problem.

![Diagram som visar flödet av information mellan klient program och Azure Storage-tjänster.][1]

Den här guiden är avsedd att läsas främst av utvecklare av onlinetjänster som använder Azure Storage tjänster och IT-proffs som ansvarar för att hantera sådana onlinetjänster. Målen för den här guiden är:

* För att hjälpa dig att upprätthålla hälso tillståndet och prestandan för dina Azure Storage-konton.
* För att ge dig nödvändiga processer och verktyg som hjälper dig att avgöra om ett problem i ett program är relaterat till Azure Storage.
* För att ge dig instruktioner om hur du löser problem som rör Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Hur den här guiden organiseras
Avsnittet "[övervaka din lagrings tjänst]" beskriver hur du övervakar Azure Storage tjänsternas hälso tillstånd och prestanda med Azure-lagringsanalys mått (lagrings mått).

Avsnittet "[diagnosticera lagrings problem]" beskriver hur du diagnostiserar problem med hjälp av Azure-lagringsanalys loggning (lagrings loggning). Det beskriver också hur du aktiverar loggning på klient sidan med hjälp av funktionerna i ett av klient biblioteken, till exempel lagrings klient biblioteket för .NET eller Azure SDK för Java.

Avsnittet "[slut punkt till slut punkts spårning]" beskriver hur du kan korrelera informationen som finns i olika loggfiler och mät data.

Avsnittet "[fel söknings vägledning]" innehåller fel söknings vägledning för några vanliga problem som kan uppstå i samband med lagringen.

"[Tillägg]" innehåller information om hur du använder andra verktyg som Wireshark och Netmon för att analysera nätverks paket data och Fiddler för att analysera http/https-meddelanden.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Övervaka lagrings tjänsten
Om du är bekant med Windows prestanda övervakning kan du tänka på lagrings mått som Azure Storage motsvarande räknare i Windows prestanda övervakaren. I lagrings mått får du en omfattande uppsättning mått (räknare i Windows prestanda övervaknings terminologi) som till exempel tjänst tillgänglighet, totalt antal begär Anden att betjäna eller procent lyckade förfrågningar till tjänst. En fullständig lista över tillgängliga mått finns i [Lagringsanalys Metrics Table schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Du kan ange om du vill att lagrings tjänsten ska samla in och aggregera mått varje timme eller varje minut. Mer information om hur du aktiverar mått och övervakar dina lagrings konton finns i [Aktivera lagrings mått och visa mått data](../blobs/monitor-blob-storage.md).

Du kan välja vilka Tim mått som du vill visa i [Azure Portal](https://portal.azure.com) och konfigurera regler som meddelar administratörer via e-post när ett Tim mått överskrider ett visst tröskelvärde. Mer information finns i [ta emot aviseringar](../../azure-monitor/platform/alerts-overview.md).

Vi rekommenderar att du läser [Azure Monitor för lagring](../../azure-monitor/insights/storage-insights-overview.md) (för hands version). Det är en funktion i Azure Monitor som erbjuder omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage tjänsters prestanda, kapacitet och tillgänglighet. Du behöver inte aktivera eller konfigurera något, och du kan direkt Visa dessa mått från de fördefinierade interaktiva diagrammen och andra visualiseringar som ingår.

Lagrings tjänsten samlar in mått med bästa möjliga ansträngning, men kanske inte spelar in varje lagrings åtgärd.

I Azure Portal kan du visa mått som tillgänglighet, totalt antal förfrågningar och Genomsnittlig latens nummer för ett lagrings konto. En meddelande regel har också ställts in för att varna en administratör om tillgänglighet sjunker under en viss nivå. Vid visning av dessa data är ett möjligt område för undersökning att den procent andel av tabell tjänster som fungerar är under 100% (mer information finns i avsnittet "[mått visar låg PercentSuccess-eller analys logg poster har åtgärder med transaktions status ClientOtherErrors]").

Du bör kontinuerligt övervaka dina Azure-program för att säkerställa att de är felfria och fungerar som förväntat av:

* Upprätta några bas linje mått för program som gör att du kan jämföra aktuella data och identifiera eventuella betydande ändringar i Azure Storage och ditt program. Värdena för dina bas linje mått kommer i många fall vara programspecifika och du bör upprätta dem när du testar programmet.
* Registrera minut mått och Använd dem för att övervaka aktivt för oväntade fel och avvikelser som till exempel toppar i antal fel eller begär ande frekvenser.
* Registrera Tim mått och använda dem för att övervaka genomsnitts värden, till exempel Genomsnittligt antal fel och begär ande frekvenser.
* Undersöka potentiella problem med hjälp av diagnostikverktyg som beskrivs senare i avsnittet "[diagnosticera lagrings problem]".

Diagrammen i följande bild visar hur medelvärdet som inträffar för Tim mått kan dölja toppar i aktivitet. Varje timmes mått visas med en stadig frekvens av begär Anden, medan minut måtten visar de variationer som faktiskt sker.

![Diagram som visar hur medelvärdet som inträffar för Tim mått kan dölja toppar i aktivitet.][3]

I resten av det här avsnittet beskrivs vilka mått du ska övervaka och varför.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Övervaknings tjänstens hälsa
Du kan använda [Azure Portal](https://portal.azure.com) för att Visa hälso tillståndet för lagrings tjänsten (och andra Azure-tjänster) i alla Azure-regioner runtom i världen. Med övervakning kan du se direkt om ett problem utanför kontrollen påverkar lagrings tjänsten i den region som du använder för ditt program.

[Azure Portal](https://portal.azure.com) kan också tillhandahålla meddelanden om incidenter som påverkar de olika Azure-tjänsterna.
OBS! den här informationen var tidigare tillgänglig, tillsammans med historiska data, på [instrument panelen för Azure-tjänsten](https://status.azure.com).
Mer information om Application Insights för Azure-DevOps finns i tillägget "[bilaga 5: övervakning med Application Insights för Azure DevOps](#appendix-5)".

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Övervaknings kapacitet
Lagrings mått lagrar bara kapacitets mått för Blob-tjänsten eftersom blobbar vanligt vis utgör en stor andel av lagrade data (vid tidpunkten för skrivning, det går inte att använda lagrings mått för att övervaka kapaciteten för dina tabeller och köer). Du hittar dessa data i **$MetricsCapacityBlob** tabellen om du har aktiverat övervakning av BLOB service. Lagrings mått registrerar dessa data en gång per dag och du kan använda värdet för **RowKey** för att avgöra om raden innehåller en entitet som relaterar till användar data (värde **data**) eller analys data (värde **analys**). Varje lagrad entitet innehåller information om mängden lagrings utrymme (**kapacitet** mätt i byte) och det aktuella antalet behållare (**ContainerCount**) och blobar (**ObjectCount**) som används i lagrings kontot. Mer information om de kapacitets mått som lagras i **$MetricsCapacityBlobs** tabellen finns i [Lagringsanalys Metrics Table schema](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Du bör övervaka dessa värden för en tidig varning att du närmar dig kapacitets gränserna för ditt lagrings konto. I Azure Portal kan du lägga till aviserings regler för att meddela dig om det aggregerade lagrings användningen överskrider eller hamnar under tröskelvärdena som du anger.
>
>

Information om hur du uppskattar storleken på olika lagrings objekt, till exempel blobbar, finns i blogg inlägget [förstå Azure Storage fakturering – bandbredd, transaktioner och kapacitet](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Övervaka tillgänglighet
Du bör övervaka tillgängligheten för lagrings tjänsterna i ditt lagrings konto genom att övervaka värdet i kolumnen **tillgänglighet** i tabellerna för tim-eller minut mått – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Kolumnen **tillgänglighet** innehåller ett procent värde som anger tillgängligheten för tjänsten eller den API-åtgärd som representeras av raden ( **RowKey** visar om raden innehåller mått för tjänsten som helhet eller för en viss API-åtgärd).

Alla värden som är mindre än 100% indikerar att vissa lagrings begär Anden inte fungerar. Du kan se varför de inte fungerar genom att undersöka de andra kolumnerna i mått data som visar antalet begär Anden med olika fel typer, till exempel **ServerTimeoutError**. Du bör förvänta dig att se till att **tillgänglighet** sjunker tillfälligt under 100% av orsaker som tillfälligt Server-timeout medan tjänsten flyttar partitioner till en bättre belastnings Utjämnings förfrågan. logiken för omprövning i klient programmet bör hantera sådana tillfälliga förhållanden. Artikeln [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) innehåller en lista över de transaktions typer som lagrings måtten innehåller i sin **tillgänglighets** beräkning.

I [Azure Portal](https://portal.azure.com)kan du lägga till aviserings regler för att meddela dig om **tillgänglighet** för en tjänst faller under ett tröskelvärde som du anger.

I avsnittet "[fel söknings vägledning]" i den här guiden beskrivs några vanliga problem med lagrings tjänster som rör tillgänglighet.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Övervaka prestanda
Om du vill övervaka lagrings tjänsternas prestanda kan du använda följande mått från tabellerna för Tim-och minut mått.

* Värdena i kolumnerna **AverageE2ELatency** och **AverageServerLatency** visar den genomsnittliga tiden som lagrings tjänsten eller API-åtgärds typen tar att bearbeta begär Anden. **AverageE2ELatency** är ett mått på en svars tid från slut punkt till slut punkt som omfattar den tid som krävs för att läsa begäran och skicka svaret förutom den tid det tar att bearbeta begäran (därför omfattar nätverks svars tiden när begäran når lagrings tjänsten). **AverageServerLatency** är ett mått på bara bearbetnings tiden och utesluter därför eventuella nätverks fördröjningar som är relaterade till kommunikation med klienten. I avsnittet "[mått visar hög AverageE2ELatency och låg AverageServerLatency]" senare i den här hand boken för en diskussion om varför det kan finnas en betydande skillnad mellan dessa två värden.
* Värdena i kolumnerna **Total ingress** och **TotalEgress** visar den totala mängden data, i byte, som kommer in i och ut ur lagrings tjänsten eller via en speciell typ av API-åtgärd.
* Värdena i kolumnen **TotalRequests** visar det totala antalet begär Anden som lagrings tjänsten för API-åtgärden tar emot. **TotalRequests** är det totala antalet begär Anden som lagrings tjänsten tar emot.

Normalt övervakar du för oväntade ändringar i dessa värden som en indikator att du har ett problem som kräver undersökning.

I [Azure Portal](https://portal.azure.com)kan du lägga till aviserings regler för att meddela dig om något av prestanda måtten för den här tjänsten faller under eller överskrider ett tröskelvärde som du anger.

I avsnittet "[fel söknings vägledning]" i den här guiden beskrivs några vanliga problem med lagrings tjänster som rör prestanda.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnostisera lagrings problem
Det finns ett antal sätt som du kan bli medveten om ett problem eller problem i programmet, inklusive:

* Ett större fel som gör att programmet kraschar eller slutar fungera.
* Betydande ändringar från bas linje värden i måtten som du övervakar enligt beskrivningen i föregående avsnitt "[övervakning av lagrings tjänsten]".
* Rapporter från användare av ditt program att en viss åtgärd inte slutförts som förväntat eller att någon funktion inte fungerar.
* Fel som genererats i programmet som visas i loggfiler eller någon annan meddelande metod.

Vanligt vis hör problem som rör Azure Storage-tjänster till en av fyra breda kategorier:

* Ditt program har ett prestanda problem som antingen rapporteras av dina användare eller visas genom ändringar i prestanda måtten.
* Ett problem har uppstått med Azure Storage-infrastrukturen i en eller flera regioner.
* Ditt program påträffar ett fel, antingen rapporterat av dina användare eller visas genom en ökning av ett av de fel beräknings mått som du övervakar.
* Under utveckling och testning kan du använda den lokala lagrings-emulatorn. Du kan stöta på problem som är specifikt relaterade till användningen av Storage-emulatorn.

I följande avsnitt beskrivs de steg som du bör följa för att diagnostisera och felsöka problem i var och en av dessa fyra kategorier. Avsnittet "[fel söknings vägledning]" senare i den här hand boken innehåller mer information om några vanliga problem som du kan stöta på.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problem med tjänst hälsa
Service Health-problem är vanligt vis utanför din kontroll. [Azure Portal](https://portal.azure.com) innehåller information om pågående problem med Azure-tjänster, inklusive lagrings tjänster. Om du har valt Read-Access Geo-Redundant lagring när du skapade ditt lagrings konto, och om dina data blir otillgängliga på den primära platsen, kan programmet tillfälligt växla till den skrivskyddade kopian på den sekundära platsen. För att kunna läsa från den sekundära platsen måste ditt program kunna växla mellan att använda de primära och sekundära lagrings platserna och kan arbeta i ett läge med begränsad funktionalitet med skrivskyddade data. Med Azure Storage klient biblioteken kan du definiera en princip för återförsök som kan läsa från sekundär lagring om en läsning från den primära lagringen Miss lyckas. Ditt program måste också vara medveten om att data på den sekundära platsen är konsekventa. Mer information finns i blogg inlägget [Azure Storage redundans alternativ och Geo-redundant lagring med Läs behörighet](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Prestanda problem
Upplevelsen av programprestanda kan vara högst subjektiv, särskilt från ett användarperspektiv. Därför är det viktigt att ha tillgång till jämförelsemått, så att du tydligt ser när det blir problem med prestandan. Många faktorer kan påverka prestandan för en Azure Storage-tjänst från klient program perspektivet. Dessa faktorer kan köras i lagrings tjänsten, i-klienten eller i nätverks infrastrukturen. Det är därför viktigt att ha en strategi för att identifiera ursprunget till prestanda problemet.

När du har identifierat den sannolika platsen för orsaken till prestanda problemet från måtten kan du använda loggfilerna för att hitta detaljerad information för att diagnostisera och felsöka problemet.

Avsnittet "[fel söknings vägledning]" senare i den här hand boken innehåller mer information om några vanliga problem som kan uppstå i samband med prestanda.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnostisera fel
Användare av programmet kan meddela dig om fel som rapporter ATS av klient programmet. Lagrings mått registrerar också antalet olika fel typer från dina lagrings tjänster som **NetworkError**, **ClientTimeoutError** eller **AuthorizationError**. När lagrings måtten endast registrerar antalet olika typer av fel, kan du få mer information om enskilda förfrågningar genom att undersöka Server-och klient sidan och nätverks loggar. HTTP-statuskoden som returnerades av lagrings tjänsten ger vanligt vis en indikation på varför begäran misslyckades.

> [!NOTE]
> Kom ihåg att du bör förvänta dig att se några tillfälliga fel: till exempel fel som beror på tillfälliga nätverks förhållanden eller program fel.
>
>

Följande resurser är användbara om du vill förstå lagringsrelaterade statusar och felkoder:

* [Vanliga REST API felkoder](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Fel koder för BLOB service](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Fel koder för Queue Service](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Fel koder för tabell tjänst](/rest/api/storageservices/Table-Service-Error-Codes)
* [Fel koder för fil tjänst](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problem med Storage-emulator
Azure SDK innehåller en lagringsreplik-emulator som du kan köra på en utvecklings arbets Station. Den här emulatorn simulerar de flesta av funktionerna i Azure Storage-tjänsterna och är användbar under utveckling och testning, så att du kan köra program som använder Azure Storage-tjänster utan att behöva en Azure-prenumeration och ett Azure Storage-konto.

I avsnittet "[fel söknings vägledning]" i den här guiden beskrivs några vanliga problem som uppstår när du använder Storage-emulatorn.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Lagrings loggnings verktyg
Lagrings loggning tillhandahåller loggning på Server sidan av lagrings begär Anden på ditt Azure Storage-konto. Mer information om hur du aktiverar loggning på Server sidan och kommer åt loggdata finns i [Aktivera lagrings loggning och åtkomst till loggdata](./storage-analytics-logging.md).

Med lagrings klient biblioteket för .NET kan du samla in loggnings data på klient sidan som relaterar till lagrings åtgärder som utförs av ditt program. Mer information finns i artikeln om [klientloggning med .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library).

> [!NOTE]
> I vissa fall (t. ex. SAS-auktoriseringsfel) kan en användare rapportera ett fel som gör att du inte kan hitta några begär ande data i lagrings loggarna på Server sidan. Du kan använda loggnings funktionerna i lagrings klient biblioteket för att undersöka om orsaken till problemet är på klienten eller använda verktyg för nätverks övervakning för att undersöka nätverket.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Använda verktyg för nätverks loggning
Du kan samla in trafiken mellan klienten och servern för att ge detaljerad information om de data som klienten och servern utbyter och underliggande nätverks förhållanden. Användbara verktyg för nätverks loggning är bland annat:

* [Fiddler](https://www.telerik.com/fiddler) är en kostnads fri proxy för webb fel sökning som gör det möjligt att undersöka rubriker och nytto Last data för http-och https-begäran och svarsmeddelanden. Mer information finns i [bilaga 1: använda Fiddler för att avbilda HTTP-och HTTPS-trafik](#appendix-1).
* [Microsoft Network Monitor (Netmon)](https://download.cnet.com/s/network-monitor/) och [wireshark](https://www.wireshark.org/) är kostnads fria analys verktyg för nätverks protokoll som gör att du kan visa detaljerad paket information för en mängd olika nätverks protokoll. Mer information om wireshark finns i "[tillägg 2: använda wireshark för att avbilda nätverks trafik](#appendix-2)".
* Om du vill utföra ett grundläggande anslutnings test för att kontrol lera att klient datorn kan ansluta till Azure Storage-tjänsten via nätverket kan du inte göra detta med hjälp av standard- **ping** -verktyget på klienten. Du kan dock använda [verktyget **TCPing**](https://www.elifulkerson.com/projects/tcping.php) för att kontrol lera anslutningen.

I många fall räcker det att logga data från lagrings loggning och lagrings klient biblioteket för att diagnosticera ett problem, men i vissa fall kan du behöva mer detaljerad information som dessa verktyg för nätverks loggning kan tillhandahålla. Med hjälp av Fiddler för att Visa HTTP-och HTTPS-meddelanden kan du till exempel Visa huvud-och nytto Last data som skickas till och från lagrings tjänsterna, vilket gör att du kan kontrol lera hur ett klient program försöker utföra lagrings åtgärder igen. Protokoll analys verktyg som Wireshark används på paket nivå så att du kan visa TCP-data, vilket gör att du kan felsöka borttappade paket och anslutnings problem. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Spårning från slut punkt till slut punkt
Spårning från slut punkt till slut punkt med en mängd olika loggfiler är en användbar teknik för att undersöka potentiella problem. Du kan använda information om datum/tid från dina mått data som en indikation på var du ska börja leta i loggfilerna för detaljerad information som kan hjälpa dig att felsöka problemet.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Korrelera logg data
När du visar loggar från klient program, nätverks spår och lagrings loggning på Server sidan, är det viktigt att kunna korrelera förfrågningar över de olika loggfilerna. Loggfilerna innehåller ett antal olika fält som är användbara som korrelations identifierare. Klientens begäran-ID är det mest användbara fältet som används för att korrelera poster i de olika loggarna. Ibland kan det vara praktiskt att använda antingen Server förfrågnings-ID eller tidsstämplar. I följande avsnitt finns mer information om de här alternativen.

### <a name="client-request-id"></a><a name="client-request-id"></a>ID för klientförfrågan
Lagrings klient biblioteket genererar automatiskt ett unikt ID för klient förfrågan för varje begäran.

* I loggen på klient sidan som lagrings klient biblioteket skapar visas ID för klientbegäran i fältet **klient begär ande-** ID för alla logg poster som relaterar till begäran.
* I ett nätverks spår, till exempel en som fångats av Fiddler, visas klientens förfrågnings-ID i begär ande meddelanden som ett HTTP-huvud värde för **x-MS-client-Request-ID** .
* I loggnings loggen på Server sidan visas klientens förfrågnings-ID i kolumnen klientbegäran ID.

> [!NOTE]
> Det är möjligt att flera begär Anden kan dela samma klient förfrågnings-ID eftersom klienten kan tilldela det här värdet (även om lagrings klient biblioteket tilldelar ett nytt värde automatiskt). När klienten försöker igen delar alla försök samma klient förfrågan-ID. Om en batch skickas från klienten, har batchen ett enda klientbegärans-ID.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>ID för serverbegäran
Lagrings tjänsten genererar automatiskt Server förfrågnings-ID: n.

* I lagrings loggen på Server sidan visas serverns förfrågnings-ID i kolumnen **begäran-ID-huvud** .
* I ett nätverks spår, till exempel en som fångats av Fiddler, visas serverns förfrågnings-ID i svarsmeddelanden som http-Huvudvärdet **x-MS-Request-ID** .
* I loggen på klient sidan som lagrings klient biblioteket skapar visas serverns förfrågnings-ID i kolumnen **Åtgärds text** för logg posten som visar information om Server svaret.

> [!NOTE]
> Lagrings tjänsten tilldelar alltid ett unikt ID för Server förfrågan till varje begäran som tas emot, så varje nytt försök från klienten och varje åtgärd som ingår i en batch har ett unikt ID för serverbegäran.
>
>

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

Kod exemplet nedan visar hur du använder ett anpassat ID för klientbegäran. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Om lagrings klient biblioteket genererar en **StorageException** i klienten innehåller egenskapen **RequestInformation** ett **RequestResult** -objekt som innehåller en **ServiceRequestID** -egenskap. Du kan också komma åt ett **RequestResult** -objekt från en **OperationContext** -instans.

Kod exemplet nedan visar hur du anger ett anpassat **ClientRequestId** -värde genom att koppla ett **OperationContext** -objekt till lagrings tjänsten. Det visar också hur du hämtar **ServerRequestId** -värdet från svarsmeddelandet.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>Tidsstämplar
Du kan också använda tidsstämplar för att hitta relaterade logg poster, men var noga med en klock skevning mellan klienten och servern som kan finnas. Sök plus eller minus 15 minuter för matchande poster på Server sidan baserat på klientens tidsstämpel. Kom ihåg att BLOB-metadata för blobbar som innehåller mått anger tidsintervallet för måtten som lagras i blobben. Det här tidsintervallet är användbart om du har många mått blobbar i samma minut eller timme.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Fel söknings vägledning
I det här avsnittet får du hjälp med diagnos och fel sökning av några vanliga problem som kan uppstå i programmet när du använder Azure Storage-tjänsterna. Använd listan nedan för att hitta den information som är relevant för det aktuella problemet.

**Fel sökning av besluts träd**

---
Är ditt problem relaterat till prestandan för en av lagrings tjänsterna?

* [Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]
* [Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]
* [Mätningar visar ett högt värde för AverageServerLatency]
* [Du upplever oväntade fördröjningar i en köad meddelandeleverans]

---
Är ditt problem relaterat till tillgängligheten för en av lagrings tjänsterna?

* [Mätningar visar en ökning i PercentThrottlingError]
* [Mätningar visar en ökning i PercentTimeoutError]
* [Mätningar visar en ökning i PercentNetworkError]

---
 Tar ditt klient program emot ett HTTP-4XX (till exempel 404) svar från en lagrings tjänst?

* [Klienten tar emot HTTP 403-meddelanden (förbjudet)]
* [Klienten tar emot HTTP 404-meddelanden (hittades inte)]
* [Klienten tar emot HTTP 409-meddelanden (konflikt)]

---
[Mått visar poster med låg PercentSuccess eller Analytics-logg har åtgärder med transaktions status ClientOtherErrors]

---
[Kapacitets mått visar en oväntad ökning av lagrings kapacitets användningen]

---
[Det uppstår oväntade omstarter av Virtual Machines som har ett stort antal anslutna virtuella hård diskar]

---
[Ditt problem uppstår när du använder Storage-emulatorn för utveckling eller testning]

---
[Du påträffar problem med att installera Azure SDK för .NET]

---
[Du har ett annat problem med en lagrings tjänst]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency
Bilden nedan från [Azure Portal](https://portal.azure.com) övervaknings verktyget visar ett exempel där **AverageE2ELatency** är betydligt högre än **AverageServerLatency**.

![Bild från Azure Portal som visar ett exempel där AverageE2ELatency är betydligt högre än AverageServerLatency.][4]

Lagrings tjänsten beräknar bara Metric- **AverageE2ELatency** för lyckade begär Anden och, till skillnad från **AverageServerLatency**, innehåller den tid som klienten tar för att skicka data och få bekräftelse från lagrings tjänsten. Därför kan skillnaden mellan **AverageE2ELatency** och **AverageServerLatency** vara antingen på grund av att klient programmet svarar långsamt eller på grund av villkor i nätverket.

> [!NOTE]
> Du kan också visa **E2ELatency** och **ServerLatency** för enskilda lagrings åtgärder i logg data för lagrings loggning.
>
>

#### <a name="investigating-client-performance-issues"></a>Undersöka problem med klient prestanda
Möjliga orsaker till att klienten svarar långsamt är att ha ett begränsat antal anslutningar eller trådar, eller ha ont om resurser, till exempel processor, minne eller nätverks bandbredd. Du kanske kan lösa problemet genom att ändra klient koden så att den blir mer effektiv (till exempel genom att använda asynkrona anrop till lagrings tjänsten) eller genom att använda en större virtuell dator (med fler kärnor och mer minne).

För tabell-och Queue Services kan Nagle-algoritmen också orsaka hög **AverageE2ELatency** jämfört med **AverageServerLatency**: Mer information finns i post [Nagle-algoritmen är inte läsvänlig mot små begär Anden](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests). Du kan inaktivera Nagle-algoritmen i koden med hjälp av **ServicePointManager** -klassen i namn området **system.net** . Du bör göra detta innan du gör några anrop till tabellen eller Queue Services i ditt program eftersom detta inte påverkar anslutningar som redan är öppna. Följande exempel kommer från metoden **Application_Start** i en arbets roll.

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Du bör kontrol lera loggarna på klient sidan för att se hur många förfrågningar som klient programmet skickar och kontrol lera om det finns allmänna .NET-relaterade prestanda Flask halsar i klienten, till exempel CPU, .NET-skräp insamling, nätverks användning eller minne. Som start punkt för att felsöka .NET-klient program, se [fel sökning, spårning och profilering](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Undersöka problem med nätverks fördröjning
Normalt orsakas svars tids fördröjning som orsakas av nätverket av tillfälliga förhållanden. Du kan undersöka både tillfälliga och bestående nätverks problem som tappade paket med hjälp av verktyg som Wireshark.

Mer information om hur du använder wireshark för att felsöka nätverks problem finns i "[tillägg 2: använda wireshark för att avbilda nätverks trafik]".

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider
I det här scenariot är den mest sannolika orsaken en fördröjning i lagrings begär Anden som når lagrings tjänsten. Du bör undersöka varför förfrågningar från klienten inte görs till Blob-tjänsten.

En möjlig orsak till att sändnings begär Anden skickas till klienten är att det finns ett begränsat antal tillgängliga anslutningar eller trådar.

Kontrol lera också om klienten utför flera återförsök och undersök orsaken om det är. För att avgöra om klienten utför flera återförsök kan du:

* Granska Lagringsanalys loggar. Om flera återförsök inträffar visas flera åtgärder med samma klient förfrågnings-ID, men med olika ID: n för Server förfrågningar.
* Granska klient loggarna. Utförlig loggning indikerar att ett försök har inträffat.
* Felsök koden och kontrol lera egenskaperna för det **OperationContext** -objekt som är associerat med begäran. Om åtgärden har gjorts innehåller egenskapen **RequestResults** flera unika ID: n för serverbegäran. Du kan också kontrol lera start-och slut tiderna för varje begäran. Mer information finns i kod exemplet i avsnittet [server begärande-ID].

Om det inte finns några problem i klienten bör du undersöka eventuella nätverks problem, till exempel paket förlust. Du kan använda verktyg som Wireshark för att undersöka nätverks problem.

Mer information om hur du använder wireshark för att felsöka nätverks problem finns i "[tillägg 2: använda wireshark för att avbilda nätverks trafik]".

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Mätningar visar ett högt värde för AverageServerLatency
Om det finns hög **AverageServerLatency** för att hämta BLOB-begäranden bör du använda lagrings loggnings loggarna för att se om det finns upprepade begär Anden för samma BLOB (eller uppsättning blobbar). För BLOB-uppladdnings begär Anden bör du undersöka vilken block storlek som klienten använder (till exempel block som är mindre än 64 KB kan resultera i omkostnader om inte läsningarna också är mindre än 64 KB) och om flera klienter laddar upp block till samma BLOB parallellt. Du bör också kontrol lera mätningarna per minut för toppar i antalet begär Anden som leder till att skalbarhets målen för varje sekund överskrids: Mer information finns i avsnittet "[mått" visar en ökning i PercentTimeoutError]. "

Om du ser hög **AverageServerLatency** för att hämta BLOB-begäranden när det upprepas begär anden till samma BLOB eller en uppsättning blobbar, bör du överväga att cachelagra dessa blobar med Azure cache eller Azure Content Delivery Network (CDN). För uppladdnings begär Anden kan du förbättra genomflödet genom att använda en större block storlek. För frågor till tabeller är det också möjligt att implementera cachelagring på klient sidan på klienter som utför samma fråge åtgärder och där data inte ändras ofta.

Höga **AverageServerLatency** -värden kan också vara ett symtom på dåligt utformade tabeller eller frågor som resulterar i skannings åtgärder eller som följer anti-mönstret Lägg till/lägga. Mer information finns i "[mått visar en ökning i PercentThrottlingError]".

> [!NOTE]
> Du hittar en fullständig check lista för prestanda genom här: [Microsoft Azure Storage prestanda-och skalbarhets check lista](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Du upplever oväntade fördröjningar i en köad meddelandeleverans
Om du upplever en fördröjning mellan tiden som ett program lägger till ett meddelande i en kö och tiden det blir tillgängligt för läsning från kön, bör du utföra följande steg för att diagnostisera problemet:

* Kontrol lera att programmet har lagt till meddelanden i kön. Kontrol lera att programmet inte försöker köra **AddMessage** -metoden flera gånger innan det lyckas. I loggarna för lagrings klient biblioteket visas eventuella upprepade återförsök för lagrings åtgärder.
* Kontrol lera att det inte finns någon klock skev mellan arbets rollen som lägger till meddelandet i kön och den arbets roll som läser meddelandet från kön som gör att den visas som om det sker en fördröjning i bearbetningen.
* Kontrol lera om den arbets roll som läser meddelanden från kön inte fungerar. Om en Queue-klient anropar metoden **GetMessage** men inte svarar med en bekräftelse, förblir meddelandet osynligt i kön tills **invisibilityTimeout** -perioden upphör att gälla. I det här läget blir meddelandet tillgängligt för bearbetning igen.
* Kontrol lera om köns längd växer över tid. Detta kan inträffa om du inte har tillräckliga arbets kamrater för att bearbeta alla meddelanden som andra arbetare placerar i kön. Kontrol lera också måtten för att se om borttagnings förfrågningarna Miss lyckas och antalet träffar i meddelanden, vilket kan tyda på upprepade misslyckade försök att ta bort meddelandet.
* Granska lagrings loggnings loggarna för alla köade åtgärder som har högre än förväntade **E2ELatency** -och **ServerLatency** -värden under en längre tids period än vanligt.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mätningar visar en ökning i PercentThrottlingError
Begränsnings fel inträffar när du överskrider skalbarhets målen för en lagrings tjänst. Lagrings tjänstens begränsningar för att säkerställa att ingen enskild klient eller klient organisation kan använda tjänsten på bekostnad av andra. Mer information finns i [skalbarhets-och prestanda mål för standard lagrings konton](scalability-targets-standard-account.md) för information om skalbarhets mål för lagrings konton och prestanda mål för partitioner i lagrings konton.

Om **PercentThrottlingError** -måttet visar en ökning av procent andelen begär Anden som inte har ett begränsnings fel, måste du undersöka ett av två scenarier:

* [Tillfällig ökning i PercentThrottlingError]
* [Permanent ökning i PercentThrottlingError-fel]

En ökning av **PercentThrottlingError** inträffar ofta samtidigt som en ökning av antalet lagrings begär Anden, eller när du börjar med att testa ditt program. Detta kan också visas i klienten som "503-servern är upptagen" eller 500 "HTTP-åtgärd vid timeout" HTTP-status meddelanden från lagrings åtgärder.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Tillfällig ökning i PercentThrottlingError
Om du ser toppar i värdet för **PercentThrottlingError** som sammanfaller med perioder med hög aktivitet för programmet, implementerar du en exponentiell (inte linjär) back av-strategi för återförsök i klienten. Med hjälp av återförsök minskar du den omedelbara belastningen på partitionen och hjälper ditt program att jämna ut trafik toppar. Mer information om hur du implementerar principer för återförsök med lagrings klient biblioteket finns i [namn området Microsoft. Azure. Storage. RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Du kan också se toppar i värdet för **PercentThrottlingError** som inte sammanfaller med perioder med hög aktivitet för programmet: den mest sannolika orsaken är att lagrings tjänsten flyttar partitioner för att förbättra belastnings utjämningen.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Permanent ökning i PercentThrottlingError-fel
Om du ser ett konsekvent högt värde för **PercentThrottlingError** efter en permanent ökning av dina transaktions volymer, eller när du utför dina första belastnings test på ditt program, måste du utvärdera hur programmet använder diskpartitioner och om det närmar sig skalbarhets målen för ett lagrings konto. Om du till exempel ser begränsnings fel i en kö (som räknas som en enda partition) bör du överväga att använda ytterligare köer för att sprida transaktionerna över flera partitioner. Om du ser begränsnings fel i en tabell, måste du överväga att använda ett annat partitionerings schema för att sprida transaktionerna över flera partitioner med hjälp av ett större antal nyckel värden. En vanlig orsak till det här problemet är lägga/Lägg till ett anti-mönster där du väljer datumet som partitionsnyckel och alla data på en viss dag skrivs till en partition: under belastning kan detta resultera i en Skriv Flask hals. Överväg antingen en annan partitionerings design eller utvärdera om användningen av Blob Storage kan vara en bättre lösning. Kontrol lera också om begränsningen inträffar som ett resultat av toppar i trafiken och Undersök hur du kan utjämna ditt mönster med begär Anden.

Om du distribuerar transaktioner över flera partitioner måste du fortfarande vara medveten om de skalbarhets gränser som angetts för lagrings kontot. Om du till exempel använde tio köer som bearbetar maximalt 2 000 1 KB meddelanden per sekund, kommer du att ha den totala gränsen på 20 000 meddelanden per sekund för lagrings kontot. Om du behöver bearbeta mer än 20 000 entiteter per sekund bör du överväga att använda flera lagrings konton. Du bör också tänka på att storleken på dina begär Anden och entiteter påverkar när lagrings tjänsten begränsar dina klienter: om du har större förfrågningar och entiteter kan du begränsas tidigare.

Ineffektiv frågans design kan också medföra att du når skalbarhets gränserna för Table-partitioner. Till exempel en fråga med ett filter som bara väljer en procent av entiteterna i en partition, men som genomsöker alla entiteter i en partition måste ha åtkomst till varje entitet. Varje entitet som läses räknas till det totala antalet transaktioner i partitionen. Därför kan du enkelt komma åt skalbarhets målen.

> [!NOTE]
> Prestanda testningen bör avslöja eventuella ineffektiva fråge modeller i ditt program.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mätningar visar en ökning i PercentTimeoutError
Dina mått visar en ökning i **PercentTimeoutError** för en av dina lagrings tjänster. På samma tid tar klienten emot en hög volym om HTTP-status meddelanden för "500 åtgärd" från lagrings åtgärder.

> [!NOTE]
> Du kan se timeout-fel tillfälligt som lagrings tjänstens belastnings Utjämnings begär Anden genom att flytta en partition till en ny server.
>
>

**PercentTimeoutError** -måttet är en agg regering av följande mått: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** och **SASServerTimeoutError**.

Serverns timeout-fel orsakas av ett fel på servern. Klientens tids gräns inträffar eftersom en åtgärd på servern har överskridit den timeout som anges av klienten. en klient som använder lagrings klient biblioteket kan till exempel ange en tids gräns för en åtgärd med hjälp av egenskapen **ServerTimeout** för klassen **QueueRequestOptions** .

Server-timeout indikerar ett problem med lagrings tjänsten som kräver ytterligare undersökning. Du kan använda mått för att se om du påträffar skalbarhets gränserna för tjänsten och identifiera eventuella toppar i trafik som kan orsaka det här problemet. Om problemet är tillfälligt kan det bero på belastnings Utjämnings aktivitet i tjänsten. Om problemet är beständigt och inte orsakas av att ditt program når skalbarhets gränserna för tjänsten bör du ge ett support ärende. För klient-timeout måste du bestämma om tids gränsen har angetts till ett lämpligt värde i klienten och antingen ändra timeout-värdet som anges i klienten eller undersöka hur du kan förbättra prestandan för åtgärderna i lagrings tjänsten, till exempel genom att optimera tabell frågorna eller minska storleken på dina meddelanden.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mätningar visar en ökning i PercentNetworkError
Dina mått visar en ökning i **PercentNetworkError** för en av dina lagrings tjänster. **PercentNetworkError** -måttet är en agg regering av följande mått: **NetworkError**, **AnonymousNetworkError** och **SASNetworkError**. Detta inträffar när lagrings tjänsten identifierar ett nätverks fel när klienten gör en lagrings förfrågan.

Den vanligaste orsaken till det här felet är att klienten kopplar från innan en tids gräns går ut i lagrings tjänsten. Undersök koden i klienten för att förstå varför och när klienten kopplas från lagrings tjänsten. Du kan också använda wireshark eller TCPing för att undersöka problem med nätverks anslutningen från klienten. Dessa verktyg beskrivs i [tilläggen].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klienten tar emot HTTP 403-meddelanden (förbjudet)
Om klientprogrammet utfärdar HTTP 403-fel (förbjudet) beror det förmodligen på att klienten använder en SAS (signatur för delad åtkomst) som har upphört att gälla när den skickar förfrågningar om lagring (även om det finns andra orsaker, som klockförskjutning, ogiltiga nycklar eller tomma rubriker). Om orsaken är en SAS-nyckel som har upphört att gälla visas inte några poster i Storage Logging-loggdata på serversidan. I följande tabell visas ett exempel från loggen på klient sidan som genereras av lagrings klient biblioteket som illustrerar det här problemet:

| Källa | Utförlighet | Utförlighet | ID för klientförfrågan | Åtgärds text |
| --- | --- | --- | --- | --- |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Startar åtgärden med plats primärt per plats läge PrimaryOnly. |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Startar synkron begäran till <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Väntar på svar. |
| Microsoft. Azure. Storage |Varning |2 |85d077ab-... |Ett undantag uppstod i väntan på svar: fjärrservern returnerade ett fel: (403) förbjudet. |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Svaret togs emot. Status kod = 403, begäran-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 =, ETag =. |
| Microsoft. Azure. Storage |Varning |2 |85d077ab-... |Ett undantag uppstod under åtgärden: fjärrservern returnerade ett fel: (403) tillåts inte. |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Kontrollerar om åtgärden bör göras om. Antal nya försök = 0, HTTP-status kod = 403, undantag = fjärrservern returnerade ett fel: (403) tillåts inte. |
| Microsoft. Azure. Storage |Information |3 |85d077ab-... |Nästa plats har angetts till primär, baserat på plats läget. |
| Microsoft. Azure. Storage |Fel |1 |85d077ab-... |Principen för återförsök tilläts inte för ett nytt försök. Fel med fjärrservern returnerade ett fel: (403) förbjudet. |

I det här scenariot bör du undersöka varför SAS-token upphör att gälla innan klienten skickar token till servern:

* Normalt bör du inte ange någon starttid när du skapar en SAS som klienten ska använda direkt. Om det förekommer små klockskillnader mellan värden som genererar SAS och lagringstjänsten kan tjänsten ta emot en SAS som inte har börjat gälla ännu.
* Ange inte en mycket kort giltighetstid för en SAS. Återigen kan små klockskillnader mellan värden som genererar SAS och lagringstjänsten göra att SAS tycks upphöra tidigare än förväntat.
* Matchar versions parametern i SAS-nyckeln (till exempel **sa = 2015-04-05**) den version av lagrings klient biblioteket som du använder? Vi rekommenderar att du alltid använder den senaste versionen av [lagrings klient biblioteket](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Om du genererar om dina lagringsåtkomstnycklar kan befintliga SAS-token bli ogiltiga. Det här problemet kan uppstå om du genererar SAS-token med lång förfallotid som klientappar ska cachelagra.

Om du använder Storage Client Library till att generera SAS-token är det enkelt att skapa en giltig token. Men om du använder lagrings REST API och skapar SAS-token manuellt, se [Delegera åtkomst med en signatur för delad åtkomst](/rest/api/storageservices/delegate-access-with-shared-access-signature).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klienten tar emot HTTP 404-meddelanden (hittades inte)
Om klientprogrammet tar emot ett HTTP 404-meddelande (hittades inte) från servern innebär det att objektet som klienten försökte använda (till exempel en entitet, tabell, blob, container eller kö) inte finns i lagringstjänsten. Här är några av de möjliga orsakerna:

* [Klienten eller en annan process har tagit bort objektet]
* [Det är problem med SAS-autentiseringen (signatur för delad åtkomst)]
* [JavaScript-koden på klientsidan har inte behörighet att komma åt objektet]
* [Nätverksfel]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klienten eller en annan process har tidigare tagit bort objektet
I scenarier där klienten försöker läsa, uppdatera eller ta bort data i en lagrings tjänst är det vanligt vis enkelt att identifiera i Server sidan loggar en tidigare åtgärd som tog bort objektet i fråga från lagrings tjänsten. Loggar data visar ofta att en annan användare eller process har tagit bort objektet. I lagrings loggen på Server sidan visas fälten åtgärds typ och begärd objekt nyckel när en klient tar bort ett objekt.

I det scenario där en klient försöker infoga ett objekt kan det hända att det inte omedelbart är uppenbart varför det här resulterar i ett HTTP 404-svar (inte hittas), med tanke på att klienten skapar ett nytt objekt. Men om klienten skapar en BLOB måste den kunna hitta BLOB-behållaren, om klienten skapar ett meddelande måste den kunna hitta en kö och om klienten lägger till en rad måste den kunna hitta tabellen.

Du kan använda loggen på klient sidan från lagrings klient biblioteket för att få en mer detaljerad förståelse av när klienten skickar vissa begär anden till lagrings tjänsten.

Följande logg på klient sidan som genereras av lagrings klient biblioteket visar problemet när klienten inte kan hitta behållaren för den blob som den skapar. Den här loggen innehåller information om följande lagrings åtgärder:

| ID för begäran | Åtgärd |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** -Metod för att ta bort BLOB-behållaren. Observera att den här åtgärden innehåller en **Head** -begäran för att kontrol lera om behållaren finns. |
| e2d06d78... |**CreateIfNotExists** -metoden för att skapa BLOB-behållaren. Observera att den här åtgärden omfattar en **Head** -begäran som söker efter behållarens förekomst. **Huvudet** returnerar ett 404-meddelande men fortsätter. |
| de8b1c3c-... |**UploadFromStream** -metoden för att skapa blobben. **Placerings** förfrågan Miss lyckas med ett 404-meddelande |

Logg poster:

| ID för begäran | Åtgärds text |
| --- | --- |
| 07b26a5d-... |Startar synkron begäran till `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = HEAD............ x-MS-client-Request-ID: 07b26a5d-.... x-MS-date: tis, 03 jun 2014 10:33:11 GMT. x-MS-version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svaret togs emot. Status kod = 200, begäran-ID = eeead849-... Content-MD5 =, ETag =    &quot; 0x8D14D2DC63D059B &quot; . |
| 07b26a5d-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| 07b26a5d-... |Laddar ned svars text. |
| 07b26a5d-... |Åtgärden har slutförts. |
| 07b26a5d-... |Startar synkron begäran till `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = ta bort........... x-MS-client-Request-ID: 07b26a5d-.... x-MS-date: tis, 03 jun 2014 10:33:12 GMT. x-MS-version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: container. |
| 07b26a5d-... |Väntar på svar. |
| 07b26a5d-... |Svaret togs emot. Status kod = 202, begäran-ID = 6ab2a4cf-..., Content-MD5 =, ETag =. |
| 07b26a5d-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| 07b26a5d-... |Laddar ned svars text. |
| 07b26a5d-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran till `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = HEAD............ x-MS-client-Request-ID: e2d06d78-.... x-MS-date: tis, 03 jun 2014 10:33:12 GMT. x-MS-version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Startar synkron begäran till `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = Lägg... 64. qCmF + TQLPhq/YYK50mP9ZQ = =...... x-MS-BLOB-Type: BlockBlob. x-MS-client-Request-ID: de8b1c3c-.... x-MS-date: tis, 03 jun 2014 10:33:12 GMT. x-MS-version: 2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Förbereder att skriva data i begäran. |
| e2d06d78-... |Ett undantag uppstod i väntan på svar: fjärrservern returnerade ett fel: (404) hittades inte.. |
| e2d06d78-... |Svaret togs emot. Status kod = 404, begäran-ID = 353ae3bc-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Svarshuvuden har bearbetats och fortsätter med resten av åtgärden. |
| e2d06d78-... |Laddar ned svars text. |
| e2d06d78-... |Åtgärden har slutförts. |
| e2d06d78-... |Startar asynkron begäran till `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = Lägg... 0......... x-MS-client-Request-ID: e2d06d78-.... x-MS-date: tis, 03 jun 2014 10:33:12 GMT. x-MS-version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: container. |
| e2d06d78-... |Väntar på svar. |
| de8b1c3c-... |Skriver data för begäran. |
| de8b1c3c-... |Väntar på svar. |
| e2d06d78-... |Ett undantag uppstod i väntan på svar: fjärrservern returnerade ett fel: (409) konflikt.. |
| e2d06d78-... |Svaret togs emot. Status kod = 409, begäran-ID = c27da20e-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Hämtar fel svars text. |
| de8b1c3c-... |Ett undantag uppstod i väntan på svar: fjärrservern returnerade ett fel: (404) hittades inte.. |
| de8b1c3c-... |Svaret togs emot. Status kod = 404, begäran-ID = 0eaeab3e-..., Content-MD5 =, ETag =. |
| de8b1c3c-... |Ett undantag uppstod under åtgärden: fjärrservern returnerade ett fel: (404) hittades inte.. |
| de8b1c3c-... |Principen för återförsök tilläts inte för ett nytt försök. Fel med fjärrservern returnerade ett fel: (404) hittades inte... |
| e2d06d78-... |Principen för återförsök tilläts inte för ett nytt försök. Fel med fjärrservern returnerade ett fel: (409) konflikt.. |

I det här exemplet visar loggen att klienten håller på att lämna förfrågningar från **CreateIfNotExists** -metoden (begärande-ID e2d06d78...) med begär Anden från **UploadFromStream** -metoden (de8b1c3c-...). Denna Interfoliering sker eftersom klient programmet anropar dessa metoder asynkront. Ändra den asynkrona koden i klienten för att säkerställa att den skapar behållaren innan du försöker överföra data till en BLOB i den behållaren. Vi rekommenderar att du skapar alla behållare i förväg.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Ett auktoriseringsfel för signaturen för delad åtkomst (SAS)
Om klient programmet försöker använda en SAS-nyckel som inte innehåller de nödvändiga behörigheterna för åtgärden, returnerar lagrings tjänsten ett HTTP 404-meddelande (hittades inte) till klienten. Samtidigt ser du också ett värde som inte är noll för **SASAuthorizationError** i måtten.

I följande tabell visas ett exempel på Server sidans logg meddelande från logg filen för lagrings loggning:

| Name | Värde |
| --- | --- |
| Start tid för begäran | 2014-05-30T06:17:48.4473697 Z |
| Åtgärdstyp     | GetBlobProperties            |
| Status för begäran     | SASAuthorizationError        |
| HTTP-statuskod   | 404                            |
| Autentiseringstyp| Säkerhets                          |
| Typ av tjänst       | Blob                         |
| Begärans-URL         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ? sa = 2014-02-14&SR = c&si = policy&sig = XXXXX &; API-version = 2014-02-14 |
| Rubrik för begäran-ID  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID för klientförfrågan  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Undersök varför ditt klient program försöker utföra en åtgärd som den inte har beviljats behörighet för.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>JavaScript-kod på klient sidan har inte behörighet att komma åt objektet
Om du använder en JavaScript-klient och lagrings tjänsten returnerar HTTP 404-meddelanden, söker du efter följande JavaScript-fel i webbläsaren:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Du kan använda F12-Utvecklarverktyg i Internet Explorer för att spåra meddelanden som utbyts mellan webbläsaren och lagrings tjänsten när du felsöker JavaScript-problem på klient sidan.
>
>

Felen beror på att webbläsaren implementerar [samma ursprungs princip](https://www.w3.org/Security/wiki/Same_Origin_Policy) säkerhets begränsning som förhindrar att en webb sida anropar ett API i en annan domän än den domän som sidan kommer från.

För att undvika problem med JavaScript-skript kan du konfigurera resurs delning mellan ursprung (CORS) för den lagrings tjänst som klienten har åtkomst till. Mer information finns i [stöd för resurs delning mellan ursprung (CORS) för Azure Storage Services](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Följande kod exempel visar hur du konfigurerar din blob-tjänst så att java script körs i Contoso-domänen för att få åtkomst till en BLOB i Blob Storage-tjänsten:

# <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>Nätverks problem
I vissa fall kan förlorade nätverks paket leda till att lagrings tjänsten returnerar HTTP 404-meddelanden till klienten. Exempel: när klient programmet tar bort en entitet från tabell tjänsten visas status meddelandet "HTTP 404 (hittades inte)" i klient programmet från tabell tjänsten. När du undersöker tabellen i tabellen Storage-tjänst ser du att tjänsten tog bort entiteten enligt begäran.

I undantags informationen i klienten ingår det ID för begäran (7e84f12d...) som tilldelats av tabell tjänsten för begäran: du kan använda den här informationen för att hitta information om begäran i lagrings loggarna på Server sidan genom att söka i kolumnen **Request-ID-header** i logg filen. Du kan också använda måtten för att identifiera när fel, till exempel detta inträffar, och sedan söka i loggfilerna baserat på den tidpunkt då måtten registrerade felet. Den här logg posten visar att borttagningen misslyckades med status meddelandet "HTTP (404) klient annat fel". Samma loggpost innehåller också det ID för begäran som genereras av klienten i kolumnen **client-Request-ID** (813ea74f...).

Loggen på Server sidan innehåller även en annan post med samma **klient-Request-ID** -värde (813ea74f...) för en lyckad borttagnings åtgärd för samma entitet och från samma klient. Denna slutförda borttagnings åtgärd ägde rum mycket strax före den misslyckade borttagnings förfrågan.

Den mest sannolika orsaken till det här scenariot är att klienten skickade en Delete-begäran för entiteten till tabell tjänsten, som lyckades, men inte fick någon bekräftelse från servern (kanske på grund av ett tillfälligt nätverks problem). Klienten försökte sedan utföra åtgärden igen automatiskt (med samma **klient-Request-ID**) och det nya försöket misslyckades eftersom entiteten redan har tagits bort.

Om det här problemet inträffar ofta bör du undersöka varför klienten inte kan ta emot bekräftelser från tabell tjänsten. Om problemet är tillfälligt bör du fånga fel meddelandet "HTTP (404) hittades inte" och logga in i klienten, men Tillåt att klienten fortsätter.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klienten får HTTP 409-meddelanden (konflikt)
I följande tabell visas ett utdrag från Server sidans logg för två klient åtgärder: **DeleteIfExists** följt av **CreateIfNotExists** med hjälp av samma BLOB container-namn. Varje klient åtgärd resulterar i två begär Anden som skickas till servern, först en **GetContainerProperties** -begäran om att kontrol lera om behållaren finns följt av **DeleteContainer** -eller **CreateContainer** -begäran.

| Timestamp | Åtgärd | Resultat | Containerns namn | ID för klientförfrågan |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Koden i klient programmet tar bort och återskapar omedelbart en BLOB-behållare med samma namn: **CreateIfNotExists** -metoden (klient förfrågan-ID bc881924-...) kan sluta att fungera med http 409-felet (konflikt). När en klient tar bort containrar, tabeller eller köer tar det en liten stund innan namnet blir tillgängligt igen.

Klientappen bör använda unika containernamn när nya containrar skapas om det är ett vanligt mönster att ta bort/återskapa dem.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Mått visar poster med låg PercentSuccess eller Analytics-logg har åtgärder med transaktions status ClientOtherErrors
**PercentSuccess** -måttet samlar in procent andelen åtgärder som lyckats baserat på deras HTTP-statuskod. Åtgärder med status koderna 2XX Count som lyckade, medan åtgärder med status koder i 3XX, 4XX och 5XX intervall räknas som lyckade och sänker värdet för **PercentSuccess** -måttet. I loggfilerna för lagring på Server sidan registreras dessa åtgärder med transaktions statusen **ClientOtherErrors**.

Det är viktigt att Observera att dessa åtgärder har slutförts och inte påverkar andra mått som tillgänglighet. Några exempel på åtgärder som kan utföras men som kan resultera i misslyckade HTTP-status koder är:

* **ResourceNotFound** (hittades inte 404), till exempel från en get-begäran till en blob som inte finns.
* **ResourceAlreadyExists** (konflikt 409), till exempel från en **CreateIfNotExist** -åtgärd där resursen redan finns.
* **ConditionNotMet** (inte ändrad 304), till exempel från en villkorlig åtgärd, till exempel när en klient skickar ett **etag** -värde och ett http **If-None-Match-** huvud för att begära en avbildning endast om den har uppdaterats sedan den senaste åtgärden.

Du hittar en lista över vanliga REST API fel koder som lagrings tjänsterna returnerar på sidan [vanliga REST API felkoder](/rest/api/storageservices/Common-REST-API-Error-Codes).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitets mått visar en oväntad ökning av lagrings kapacitets användningen
Om du ser plötsliga, oväntade förändringar i kapacitets användningen i ditt lagrings konto kan du undersöka orsakerna genom att först titta på tillgänglighets måtten. en ökning av antalet misslyckade borttagnings begär Anden kan till exempel leda till en ökning av den mängd blob-lagring som du använder som programspecifika rensnings åtgärder som du kanske har förväntat dig att frigöra utrymme kanske inte fungerar som förväntat (till exempel eftersom SAS-token som används för att frigöra utrymme har upphört att gälla).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Ditt problem uppstår när du använder Storage-emulatorn för utveckling eller testning
Du använder vanligt vis Storage-emulatorn under utveckling och testning för att undvika kravet på ett Azure Storage-konto. Vanliga problem som kan uppstå när du använder Storage-emulatorn är:

* [Funktionen "X" fungerar inte i Storage-emulatorn]
* [Fel: värdet för ett av HTTP-huvudena har inte rätt format "när du använder Storage-emulatorn]
* [Du måste ha administratörs behörighet för att köra Storage-emulatorn]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funktionen "X" fungerar inte i Storage-emulatorn
Storage-emulatorn stöder inte alla funktioner i Azure Storage-tjänster som fil tjänsten. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md).

För de funktioner som Storage-emulatorn inte stöder använder du Azure Storage-tjänsten i molnet.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Fel: värdet för ett av HTTP-huvudena har inte rätt format "när du använder Storage-emulatorn
Du testar ditt program som använder lagrings klient biblioteket mot den lokala lagrings emulatorn och metod anrop som **CreateIfNotExists** inte fungerar med fel meddelandet "värdet för ett av HTTP-huvudena har fel format." Det betyder att den version av Storage-emulatorn som du använder inte stöder den version av lagrings klient biblioteket som du använder. Lagrings klient biblioteket lägger till huvudet **x-MS-version** till alla begär Anden som det gör. Om Storage-emulatorn inte känner igen värdet i **x-MS-version-** huvudet, avvisar den begäran.

Du kan använda lagrings bibliotekets klient loggar för att se värdet för **x-MS-version-huvud** som det skickas. Du kan också se värdet för **sidhuvudet x-MS-version** om du använder Fiddler för att spåra begär Anden från klient programmet.

Det här scenariot uppstår vanligt vis om du installerar och använder den senaste versionen av lagrings klient biblioteket utan att uppdatera Storage-emulatorn. Du bör antingen installera den senaste versionen av Storage-emulatorn eller använda moln lagring i stället för emulatorn för utveckling och testning.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Du måste ha administratörs behörighet för att köra Storage-emulatorn
Du uppmanas att ange administratörs behörighet när du kör Storage-emulatorn. Detta inträffar bara när du initierar Storage-emulatorn för första gången. När du har initierat Storage-emulatorn behöver du inte administratörs behörighet för att köra den igen.

Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](storage-use-emulator.md). Du kan också initiera Storage-emulatorn i Visual Studio, vilket även kräver administratörs behörighet.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Du påträffar problem med att installera Azure SDK för .NET
När du försöker installera SDK Miss lyckas det att försöka installera lagringsprovidern på den lokala datorn. Installations loggen innehåller ett av följande meddelanden:

* CAQuietExec: fel: det gick inte att komma åt SQL-instansen
* CAQuietExec: fel: det gick inte att skapa databasen

Orsaken är ett problem med den befintliga LocalDB-installationen. Som standard använder Storage-emulatorn LocalDB för att bevara data när de simulerar Azure Storage-tjänster. Du kan återställa din LocalDB-instans genom att köra följande kommandon i ett kommando tolks fönster innan du försöker installera SDK: n.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Kommandot **Delete** tar bort gamla databasfiler från tidigare installationer av Storage-emulatorn.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Du har ett annat problem med en lagrings tjänst
Om de föregående fel söknings avsnitten inte innehåller det problem du har med en lagrings tjänst bör du använda följande metod för att diagnostisera och felsöka problemet.

* Kontrol lera dina mått för att se om det finns några ändringar från det förväntade bas rads beteendet. Från måtten kan du eventuellt avgöra om problemet är tillfälligt eller permanent och vilka lagrings åtgärder som problemet påverkar.
* Du kan använda mått informationen för att söka i logg data på Server sidan för mer detaljerad information om eventuella fel som inträffar. Den här informationen kan hjälpa dig att felsöka och lösa problemet.
* Om informationen i loggar på Server sidan inte räcker för att felsöka problemet, kan du använda klient bibliotekets loggar på klient sidan för att undersöka klient programmets beteende och verktyg som Fiddler, wireshark för att undersöka nätverket.

Mer information om hur du använder Fiddler finns i "[tillägg 1: använda Fiddler för att avbilda http och HTTPS-trafik]".

Mer information om hur du använder wireshark finns i "[tillägg 2: använda wireshark för att avbilda nätverks trafik]".

## <a name="appendices"></a><a name="appendices"></a>Bilagor
Tilläggen beskriver flera verktyg som kan vara användbara när du diagnostiserar och felsöker problem med Azure Storage (och andra tjänster). Dessa verktyg ingår inte i Azure Storage och vissa är produkter från tredje part. De verktyg som beskrivs i dessa tillägg omfattas inte av något support avtal som du kan ha med Microsoft Azure eller Azure Storage, och därför bör du undersöka licensierings-och support alternativen som finns tillgängliga från leverantörer av dessa verktyg.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Bilaga 1: använda Fiddler för att avbilda HTTP-och HTTPS-trafik
[Fiddler](https://www.telerik.com/fiddler) är ett användbart verktyg för att analysera http-och https-trafiken mellan ditt klient program och Azure Storage-tjänsten som du använder.

> [!NOTE]
> Fiddler kan avkoda HTTPS-trafik; Läs Fiddler-dokumentationen noggrant för att förstå hur det sker, och för att förstå säkerhets konsekvenserna.
>
>

Den här bilagan innehåller en kort genom gång av hur du konfigurerar Fiddler för att avbilda trafik mellan den lokala datorn där du har installerat Fiddler och Azure Storage-tjänsterna.

När du har startat Fiddler börjar den samla in HTTP-och HTTPS-trafik på den lokala datorn. Följande är några användbara kommandon för att kontrol lera Fiddler:

* Stoppa och börja samla in trafik. På huvud menyn går du till **filen** och klickar sedan på **avbilda trafik** för att växla mellan inläsning och inaktive ring.
* Spara insamlade trafik data. Gå till **fil** på huvud menyn, klicka på **Spara** och klicka sedan på **alla sessioner**: Detta gör att du kan spara trafiken i en arkivfil för en session. Du kan läsa in ett arkiv igen senare för analys eller skicka det om det krävs för Microsoft support.

Om du vill begränsa mängden trafik som Fiddler fångar in kan du använda filter som du konfigurerar på fliken **filter** . Följande skärm bild visar ett filter som bara fångar in trafik som skickas till **contosoemaildist.Table.Core.Windows.net** Storage-slutpunkt:

![Skärm bild som visar ett filter som bara fångar trafik som skickas till contosoemaildist.table.core.windows.net Storage-slutpunkten.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Bilaga 2: använda wireshark för att avbilda nätverks trafik
[Wireshark](https://www.wireshark.org/) är en analys av nätverks protokoll som gör att du kan visa detaljerad paket information för en mängd olika nätverks protokoll.

Följande procedur visar hur du registrerar detaljerad paket information för trafik från den lokala dator där du installerade wireshark till tabell tjänsten i ditt Azure Storage-konto.

1. Starta wireshark på den lokala datorn.
2. I avsnittet **Starta** väljer du det lokala nätverks gränssnittet eller gränssnitt som är anslutna till Internet.
3. Klicka på **avbildnings alternativ**.
4. Lägg till ett filter i text rutan för **Infångnings filter** . **Värd contosoemaildist.Table.Core.Windows.net** konfigurerar till exempel wireshark för att endast avbilda paket som skickas till eller från tabell tjänstens slut punkt i lagrings kontot för **contosoemaildist** . Ta en titt på den [fullständiga listan över Infångnings filter](https://wiki.wireshark.org/CaptureFilters).

   ![Skärm bild som visar hur du lägger till ett filter i text rutan för Infångnings filter.][6]
5. Klicka på **Starta**. Wireshark kommer nu att avbilda alla paket som skickas till eller från tabell tjänstens slut punkt när du använder klient programmet på den lokala datorn.
6. När du är färdig klickar du på **avbilda** på huvud menyn och sedan på **stoppa**.
7. Om du vill spara insamlade data i en wireshark-Infångnings fil går du till huvud menyn och klickar på **Arkiv** och sedan på **Spara**.

WireShark markerar eventuella fel som finns i fönstret **packetlist** . Du kan också använda fönstret med **expert information** (klicka på **analysera**, sedan på **expert information**) om du vill visa en sammanfattning av fel och varningar.

![Skärm bild som visar fönstret expert information där du kan visa en sammanfattning av fel och varningar.][7]

Du kan också välja att Visa TCP-data när program lagret ser det genom att högerklicka på TCP-data och välja **Följ TCP-dataström**. Detta är användbart om du har fångat din dump utan ett infångstfilter. Mer information finns i [följande TCP-strömmar](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Skärm bild som visar hur du visar TCP-data när program lagret ser det.][8]

> [!NOTE]
> Mer information om hur du använder wireshark finns i [Översikt över wireshark-användare](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Bilaga 4: använda Excel för att visa mått och logg data
Med många verktyg kan du ladda ned lagrings mått data från Azure Table Storage i ett avgränsat format som gör det enkelt att läsa in data i Excel för visning och analys. Lagrings loggnings data från Azure Blob Storage är redan i ett avgränsat format som du kan läsa in i Excel. Du måste dock lägga till lämpliga kolumn rubriker som är baserade i informationen i [Lagringsanalys logg format](/rest/api/storageservices/Storage-Analytics-Log-Format) och [Lagringsanalys mått tabell scheman](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

Importera lagrings loggnings data till Excel när du har laddat ned det från Blob Storage:

* På **data** -menyn klickar du på **från text**.
* Bläddra till logg filen som du vill visa och klicka på **Importera**.
* I steg 1 i **guiden text import** väljer du **avgränsad**.

I steg 1 i **guiden text import** väljer du **semikolon** som den enda avgränsaren och väljer dubbelt citat tecken som **text kvalificerare**. Klicka sedan på **Slutför** och välj var du vill placera data i din arbets bok.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Bilaga 5: övervakning med Application Insights för Azure-DevOps
Du kan också använda Application Insights funktionen för Azure-DevOps som en del av prestanda-och tillgänglighets övervakningen. Det här verktyget kan:

* Kontrol lera att din webb tjänst är tillgänglig och svarar. Oavsett om din app är en webbplats eller en app som använder en webb tjänst kan den testa din URL med några minuters mellanrum från platser runtom i världen och meddela dig om det uppstår problem.
* Diagnostisera snabbt prestanda problem eller undantag i din webb tjänst. Ta reda på om CPU eller andra resurser sträcker sig, Hämta stack spår från undantag och enkelt söka igenom logg spårningar. Om appens prestanda sjunker under godkända gränser kan Microsoft skicka ett e-postmeddelande till dig. Du kan övervaka både .NET-och Java-webbtjänster.

Du hittar mer information om [vad som är Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Nästa steg

Mer information om analyser i Azure Storage finns i följande resurser:

* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Lagrings analys](storage-analytics.md)
* [Mått för lagrings analys](storage-analytics-metrics.md)
* [Schema för lagrings analysens mått tabell](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Lagringsanalysloggar](storage-analytics-logging.md)
* [Logg format för Storage Analytics](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Introduktion]: #introduction
[Hur den här guiden organiseras]: #how-this-guide-is-organized

[Övervaka lagrings tjänsten]: #monitoring-your-storage-service
[Övervaknings tjänstens hälsa]: #monitoring-service-health
[Övervaknings kapacitet]: #monitoring-capacity
[Övervaka tillgänglighet]: #monitoring-availability
[Övervaka prestanda]: #monitoring-performance

[Diagnostisera lagrings problem]: #diagnosing-storage-issues
[Problem med tjänst hälsa]: #service-health-issues
[Prestanda problem]: #performance-issues
[Diagnostisera fel]: #diagnosing-errors
[Problem med Storage-emulator]: #storage-emulator-issues
[Lagrings loggnings verktyg]: #storage-logging-tools
[Använda verktyg för nätverks loggning]: #using-network-logging-tools

[Spårning från slut punkt till slut punkt]: #end-to-end-tracing
[Korrelera logg data]: #correlating-log-data
[ID för klientförfrågan]: #client-request-id
[ID för serverbegäran]: #server-request-id
[Tidsstämplar]: #timestamps

[Fel söknings vägledning]: #troubleshooting-guidance
[Mätningar visar ett högt värde för AverageE2ELatency och ett lågt värde för AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar låga värden för AverageE2ELatency och AverageServerLatency, men klienten har ändå långa svarstider]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Mätningar visar ett högt värde för AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Du upplever oväntade fördröjningar i en köad meddelandeleverans]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Mätningar visar en ökning i PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Tillfällig ökning i PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanent ökning i PercentThrottlingError-fel]: #permanent-increase-in-PercentThrottlingError
[Mätningar visar en ökning i PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Mätningar visar en ökning i PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klienten tar emot HTTP 403-meddelanden (förbjudet)]: #the-client-is-receiving-403-messages
[Klienten tar emot HTTP 404-meddelanden (hittades inte)]: #the-client-is-receiving-404-messages
[Klienten eller en annan process har tagit bort objektet]: #client-previously-deleted-the-object
[Det är problem med SAS-autentiseringen (signatur för delad åtkomst)]: #SAS-authorization-issue
[JavaScript-koden på klientsidan har inte behörighet att komma åt objektet]: #JavaScript-code-does-not-have-permission
[Nätverksfel]: #network-failure
[Klienten tar emot HTTP 409-meddelanden (konflikt)]: #the-client-is-receiving-409-messages

[Mått visar poster med låg PercentSuccess eller Analytics-logg har åtgärder med transaktions status ClientOtherErrors]: #metrics-show-low-percent-success
[Kapacitets mått visar en oväntad ökning av lagrings kapacitets användningen]: #capacity-metrics-show-an-unexpected-increase
[Ditt problem uppstår när du använder Storage-emulatorn för utveckling eller testning]: #your-issue-arises-from-using-the-storage-emulator
[Funktionen "X" fungerar inte i Storage-emulatorn]: #feature-X-is-not-working
[Fel: värdet för ett av HTTP-huvudena har inte rätt format "när du använder Storage-emulatorn]: #error-HTTP-header-not-correct-format
[Du måste ha administratörs behörighet för att köra Storage-emulatorn]: #storage-emulator-requires-administrative-privileges
[Du påträffar problem med att installera Azure SDK för .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Du har ett annat problem med en lagrings tjänst]: #you-have-a-different-issue-with-a-storage-service

[Bilagor]: #appendices
[Bilaga 1: använda Fiddler för att avbilda HTTP-och HTTPS-trafik]: #appendix-1
[Bilaga 2: använda wireshark för att avbilda nätverks trafik]: #appendix-2
[Bilaga 4: använda Excel för att visa mått och logg data]: #appendix-4
[Bilaga 5: övervakning med Application Insights för Azure-DevOps]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
