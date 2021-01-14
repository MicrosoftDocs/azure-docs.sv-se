---
title: Övervakning av Azure Traffic Manager-slutpunkt | Microsoft Docs
description: Den här artikeln kan hjälpa dig att förstå hur Traffic Manager använder slut punkts övervakning och automatisk slut punkts växling för att hjälpa Azure-kunder att distribuera program med hög tillgänglighet
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: duau
ms.openlocfilehash: 31048a0abd939c81b64e87b4a146ae3b6934803f
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183917"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning för Traffic Manager

Azure Traffic Manager innehåller inbyggd slut punkts övervakning och automatisk redundansväxling av slut punkter. Den här funktionen hjälper dig att leverera program med hög tillgänglighet som är elastiska till slut punkts felet, inklusive Azure-regioner.

## <a name="configure-endpoint-monitoring"></a>Konfigurera slut punkts övervakning

Om du vill konfigurera slut punkts övervakning måste du ange följande inställningar på din Traffic Manager profil:

* **Protokoll**. Välj HTTP, HTTPS eller TCP som det protokoll som Traffic Manager använder vid avsökning av slut punkten för att kontrol lera dess hälsa. HTTPS-övervakningen verifierar inte om ditt TLS/SSL-certifikat är giltigt – det kontrollerar bara att certifikatet finns.
* **Port**. Välj den port som används för begäran.
* **Sökväg**. Den här konfigurations inställningen är endast giltig för HTTP-och HTTPS-protokollen, för vilka du måste ange Sök vägs inställningen. Att ange den här inställningen för TCP övervaknings protokollet resulterar i ett fel. För HTTP-och HTTPS-protokoll ger du den relativa sökvägen och namnet på webb sidan eller filen som övervakningen har åtkomst till. Ett snedstreck (/) är en giltig post för den relativa sökvägen. Det här värdet innebär att filen finns i rot katalogen (standard).
* **Anpassade huvud inställningar** Med den här konfigurations inställningen kan du lägga till vissa HTTP-huvuden i hälso kontrollerna som Traffic Manager skickar till slut punkter under en profil. Anpassade huvuden kan anges på en profil nivå som ska användas för alla slut punkter i profilen och/eller på en slut punkts nivå som endast gäller för den slut punkten. Du kan använda anpassade rubriker för att få hälso kontroller till slut punkter i en miljö för flera klienter som dirigeras korrekt till deras mål genom att ange ett värd huvud. Du kan också använda den här inställningen genom att lägga till unika huvuden som kan användas för att identifiera Traffic Manager ursprungliga HTTP (S)-begär Anden och bearbeta dem på olika sätt. Du kan ange upp till åtta huvud: värdepar seprated med kommatecken. Till exempel "header1: värde1, header2: värde2". 
* **Förväntade status kod intervall** Med den här inställningen kan du ange flera kod intervall för framgång i formatet 200-299, 301-301. Om dessa status koder tas emot som svar från en slut punkt när en hälso kontroll initieras, Traffic Manager markerar dessa slut punkter som felfria. Du kan ange högst 8 status kod intervall. Den här inställningen gäller endast HTTP-och HTTPS-protokoll och alla slut punkter. Den här inställningen finns på Traffic Manager profil nivå och som standard definieras värdet 200 som status kod för lyckad status.
* **Avsöknings intervall**. Det här värdet anger hur ofta en slut punkt kontrol leras för hälsan från en Traffic Manager-probing-agent. Du kan ange två värden här: 30 sekunder (normal avsökning) och 10 sekunder (snabb avsökning). Om inga värden anges anges profilen till ett standardvärde på 30 sekunder. Besök sidan med [Traffic Manager priser](https://azure.microsoft.com/pricing/details/traffic-manager) om du vill veta mer om snabba priser för avsökning.
* **Tolererat antal felaktiga försök**. Det här värdet anger hur många fel en Traffic Manager probing-agenten ska tolerera innan den markeras som ohälsosam. Värdet kan vara mellan 0 och 9. Värdet 0 innebär att ett enda övervaknings fel kan orsaka att slut punkten markeras som ohälsosam. Om inget värde anges används standardvärdet 3.
* **Timeout för avsökning**. Den här egenskapen anger hur lång tid som Traffic Manager avsöknings agenten ska vänta innan den överväger att kontrol lera ett fel när en hälso kontroll avsökning skickas till slut punkten. Om avsöknings intervallet är inställt på 30 sekunder kan du ange timeout-värdet mellan 5 och 10 sekunder. Om inget värde anges används ett standardvärde på 10 sekunder. Om avsöknings intervallet är inställt på 10 sekunder kan du ange tids gräns värdet mellan 5 och 9 sekunder. Om inget timeout-värde anges används standardvärdet 9 sekunder.

    ![Slutpunktsövervakning för Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Bild: Traffic Manager slut punkts övervakning**

## <a name="how-endpoint-monitoring-works"></a>Hur slut punkts övervakning fungerar

Om övervaknings protokollet är inställt på HTTP eller HTTPS gör Traffic Manager probing-agenten en GET-begäran till slut punkten med hjälp av protokollet, porten och den relativa sökvägen som anges. Om det får ett svar på 200 – OK, eller något av svaren som kon figurer ATS i **\* intervallet för förväntade status koder**, betraktas denna slut punkt som felfri. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna tids gränsen, så försöker Traffic Manager probing-agenten igen enligt inställningen för det tolererade antalet fel (inga omförsök görs om den här inställningen är 0). Om antalet efterföljande fel i följd är högre än inställningen för det tolererade antalet fel, markeras den slut punkten som ohälsosam. 

Om övervaknings protokollet är TCP initierar Traffic Manager probing-agenten en begäran om TCP-anslutning med den angivna porten. Om slut punkten svarar på begäran med ett svar för att upprätta anslutningen markeras hälso kontrollen som lyckad och Traffic Manager avsöknings agenten återställer TCP-anslutningen. Om svaret är ett annat värde, eller om inget svar tas emot inom den angivna tids gränsen, så försöker Traffic Manager probing-agenten igen enligt inställningen för det tolererade antalet fel (inga försök görs om den här inställningen är 0). Om antalet efterföljande fel i följd är högre än inställningen för det tolererade antalet fel, markeras den slut punkten som felaktig.

I samtliga fall kan Traffic Manager avsökningar från flera platser och den efterföljande beräkningen av felen ske i varje region. Det innebär också att slut punkter tar emot hälso avsökningar från Traffic Manager med en högre frekvens än inställningen som används för avsöknings intervall.

>[!NOTE]
>För HTTP-eller HTTPS-övervakningsprogram är en vanlig övning på slut punkts sidan att implementera en anpassad sida i ditt program, till exempel/Health.aspx. Med hjälp av den här sökvägen för övervakning kan du utföra programspecifika kontroller, till exempel kontrol lera prestanda räknare eller verifiera databasens tillgänglighet. Baserat på dessa anpassade kontroller returnerar sidan en lämplig HTTP-statuskod.

Alla slut punkter i en Traffic Manager profil resurs övervaknings inställningar. Om du behöver använda olika övervaknings inställningar för olika slut punkter kan du skapa [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Status för slut punkt och profil

Du kan aktivera och inaktivera Traffic Manager profiler och slut punkter. Men en ändring i slut punkts status kan också uppstå som ett resultat av Traffic Manager automatiserade inställningar och processer.

### <a name="endpoint-status"></a>Slut punkts status

Du kan aktivera eller inaktivera en angiven slut punkt. Den underliggande tjänsten, som kan fortfarande vara felfri, påverkas inte. Om du ändrar slut punkts statusen styrs tillgängligheten för slut punkten i Traffic Manager profilen. När en slut punkts status är inaktive rad kontrollerar Traffic Manager inte hälso tillståndet och slut punkten ingår inte i ett DNS-svar.

### <a name="profile-status"></a>Profilstatus

Med inställningen profil status kan du aktivera eller inaktivera en speciell profil. Statusen för slut punkten påverkar en enda slut punkt, men profil statusen påverkar hela profilen, inklusive alla slut punkter. När du inaktiverar en profil kontrol leras inte slut punkterna för hälso tillstånd och inga slut punkter ingår i ett DNS-svar. En [NXDOMAIN](https://tools.ietf.org/html/rfc2308) svars kod returneras för DNS-frågan.

### <a name="endpoint-monitor-status"></a>Status för slut punkts övervakare

Status för slut punkts övervakare är ett Traffic Manager-genererat värde som visar slut punktens status. Du kan inte ändra den här inställningen manuellt. Status för slut punkts övervakning är en kombination av resultaten av slut punkts övervakning och den konfigurerade slut punkts statusen. De möjliga värdena för status för slut punkts övervakning visas i följande tabell:

| Profilstatus | Slut punkts status | Status för slut punkts övervakare | Kommentarer |
| --- | --- | --- | --- |
| Inaktiverad |Enabled |Inaktiv |Profilen har inaktiverats. Även om slut punktens status är aktive rad, har profil statusen (inaktive rad) företräde. Slut punkter i inaktiverade profiler övervakas inte. En NXDOMAIN svars kod returneras för DNS-frågan. |
| &lt;alla&gt; |Inaktiverad |Inaktiverad |Slut punkten har inaktiverats. Inaktiverade slut punkter övervakas inte. Slut punkten ingår inte i DNS-svar, vilket innebär att den inte tar emot trafik. |
| Enabled |Enabled |Online |Slut punkten övervakas och är felfri. Den ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Degraderad |Hälso kontroller för slut punkts övervakningen fungerar inte. Slut punkten ingår inte i DNS-svar och tar inte emot trafik. <br>Ett undantag till detta är om alla slut punkter är degraderade, vilket innebär att alla av dem anses vara returnerade i fråge svaret.</br>|
| Enabled |Enabled |CheckingEndpoint |Slut punkten övervakas, men resultatet från den första avsökningen har ännu inte tagits emot. CheckingEndpoint är ett tillfälligt tillstånd som vanligt vis inträffar omedelbart efter att en slut punkt har lagts till eller Aktiver ATS i profilen. En slut punkt i det här läget ingår i DNS-svar och kan ta emot trafik. |
| Enabled |Enabled |Stoppad |Webb programmet som slut punkten pekar på är inte igång. Kontrol lera inställningarna för webbapp. Detta kan också inträffa om slut punkten är av typen kapslad slut punkt och den underordnade profilen är inaktive rad eller inaktiv. <br>En slut punkt med status stoppad övervakas inte. Den ingår inte i DNS-svar och tar inte emot trafik. Ett undantag till detta är om alla slut punkter är degraderade, vilket innebär att alla är degraderade i svaret.</br>|

Mer information om hur status för slut punkts övervakning beräknas för kapslade slut punkter finns i [kapslade Traffic Manager profiler](traffic-manager-nested-profiles.md).

>[!NOTE]
> En stoppad status för slut punkts övervakning kan inträffa på App Service om ditt webb program inte körs på standard-nivån eller senare. Mer information finns i [Traffic Manager integration med App Service](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Status för profil övervakare

Profil övervaknings statusen är en kombination av den konfigurerade profil statusen och status värden för slut punkts övervakaren för alla slut punkter. De möjliga värdena beskrivs i följande tabell:

| Profil status (som konfigurerat) | Status för slut punkts övervakare | Status för profil övervakare | Kommentarer |
| --- | --- | --- | --- |
| Inaktiverad |&lt;en &gt; eller en profil utan definierade slut punkter. |Inaktiverad |Profilen har inaktiverats. |
| Enabled |Status för minst en slut punkt har degraderats. |Degraderad |Granska de enskilda slut punkts status värdena för att avgöra vilka slut punkter som kräver ytterligare uppmärksamhet. |
| Enabled |Statusen för minst en slut punkt är online. Inga slut punkter har försämrad status. |Online |Tjänsten accepterar trafik. Ingen ytterligare åtgärd krävs. |
| Enabled |Status för minst en slut punkt är CheckingEndpoint. Inga slut punkter har statusen online eller degraderas. |CheckingEndpoints |Detta över gångs tillstånd inträffar när en profil skapas eller aktive ras. Slut punktens hälso tillstånd kontrol leras för första gången. |
| Enabled |Status för alla slut punkter i profilen är antingen inaktive rad eller stoppad, eller så har profilen inga definierade slut punkter. |Inaktiv |Inga slut punkter är aktiva, men profilen är fortfarande aktive rad. |

## <a name="endpoint-failover-and-recovery"></a>Redundansväxling och återställning av slut punkt

Traffic Manager kontrollerar regelbundet hälso tillståndet för varje slut punkt, inklusive Felaktiga slut punkter. Traffic Manager upptäcker när en slut punkt blir felfri och försätts i rotationen igen.

En slut punkt är inte felfri när någon av följande händelser inträffar:

- Om övervaknings protokollet är HTTP eller HTTPS:
    - Ett icke-200-svar, eller ett svar som inte innehåller det status intervall som anges i inställningen för **förväntade status kod områden** , tas emot (inklusive en annan 2xx-kod eller en 301/302-omdirigering).
- Om övervaknings protokollet är TCP: 
    - Ett svar som inte är ACK eller SYN-ACK tas emot som svar på den SYN-begäran som skickats av Traffic Manager för att försöka upprätta en anslutning.
- Standardvärde. 
- Eventuella andra anslutnings problem som leder till att slut punkten inte kan uppnås.

Mer information om fel sökning av misslyckade kontroller finns i [Felsöka degraderad status på Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Tids linjen i följande bild är en detaljerad beskrivning av övervaknings processen för Traffic Manager slut punkt med följande inställningar: övervaknings protokollet är HTTP, intervallet är 30 sekunder, antalet tolererade haverier är 3, timeout-värdet är 10 sekunder och DNS-TTL är 30 sekunder.

![Traffic Manager slut punkt för redundans och återställning efter fel](./media/traffic-manager-monitoring/timeline.png)

**Bild: failover och återställnings ordning för Traffic Manager-slutpunkt**

1. **Hämta**. För varje slut punkt utför Traffic Manager övervaknings systemet en GET-begäran på den sökväg som anges i övervaknings inställningarna.
2. **200 OK eller anpassat kod intervall har angetts Traffic Manager profil övervaknings inställningar** . Övervaknings systemet förväntar sig en HTTP 200 OK eller det anpassade kod intervallet som anges Traffic Manager meddelande för profil övervaknings inställningar som ska returneras inom 10 sekunder. När den tar emot det här svaret känner det av att tjänsten är tillgänglig.
3. **30 sekunder mellan kontrollerna**. Slut punktens hälso kontroll upprepas var 30: e sekund.
4. **Tjänsten är inte tillgänglig**. Tjänsten blir otillgänglig. Traffic Manager inte att veta förrän nästa hälso kontroll.
5. **Försöker komma åt övervaknings Sök vägen**. Övervaknings systemet utför en GET-begäran, men får inget svar inom tids gränsen på 10 sekunder (Alternativt kan ett icke-200-svar tas emot). Den försöker sedan tre gånger, med 30 sekunders intervall. Om ett av försöken lyckas återställs antalet försök.
6. **Status har angetts till degraderad**. Efter ett fjärde i följd, markerar övervaknings systemet otillgänglig slut punkts status som försämrad.
7. **Trafiken omdirigeras till andra slut punkter**. Traffic Manager DNS-namnservrar uppdateras och Traffic Manager inte längre returnerar slut punkten som svar på DNS-frågor. Nya anslutningar dirigeras till andra tillgängliga slut punkter. Tidigare DNS-svar som inkluderar den här slut punkten kan dock fortfarande cachelagras av rekursiva DNS-servrar och DNS-klienter. Klienterna fortsätter att använda slut punkten tills DNS-cachen upphör att gälla. När DNS-cachen upphör att gälla, gör klienterna nya DNS-frågor och dirigeras till olika slut punkter. Varaktigheten för cachen styrs av TTL-inställningen i Traffic Manager profilen, till exempel 30 sekunder.
8. **Hälso kontrollerna fortsätter**. Traffic Manager fortsätter att kontrol lera slut punktens hälso tillstånd när den har försämrad status. Traffic Manager identifierar när slut punkten återgår till hälsan.
9. **Tjänsten är online igen**. Tjänsten blir tillgänglig. Slut punkten behåller sin degraderade status i Traffic Manager tills övervaknings systemet utför nästa hälso kontroll.
10. **Trafik till tjänsten återupptas**. Traffic Manager skickar en GET-begäran och får ett 200 OK-status svar. Tjänsten har återgått till felfritt tillstånd. Traffic Manager namnservrarna uppdateras och de börjar distribuera tjänstens DNS-namn i DNS-svar. Trafiken återgår till slut punkten som cachelagrade DNS-svar som returnerar andra slut punkter upphör att gälla, och eftersom befintliga anslutningar till andra slut punkter avbryts.

    > [!NOTE]
    > Eftersom Traffic Manager fungerar på DNS-nivå, kan det inte påverka befintliga anslutningar till någon slut punkt. När den dirigerar trafik mellan slut punkter (antingen genom ändrade profil inställningar, eller vid redundansväxling eller återställning efter fel), dirigerar Traffic Manager nya anslutningar till tillgängliga slut punkter. Andra slut punkter kan dock fortsätta att ta emot trafik via befintliga anslutningar tills dessa sessioner avslutas. För att tillåta trafik att tömmas från befintliga anslutningar bör program begränsa sessionens varaktighet för varje slut punkt.

## <a name="traffic-routing-methods"></a>Metoder för trafik dragning

När en slut punkt har statusen degraderad returneras den inte längre som svar på DNS-frågor. I stället väljs och returneras en alternativ slut punkt. Den metod för trafik routning som kon figurer ATS i profilen avgör hur den alternativa slut punkten väljs.

* **Prioritet**. Slut punkter utgör en prioriterad lista. Den första tillgängliga slut punkten i listan returneras alltid. Om statusen för en slut punkt är försämrad returneras nästa tillgängliga slut punkt.
* **Viktat**. Alla tillgängliga slut punkter väljs slumpmässigt baserat på deras tilldelade vikt och vikter för de andra tillgängliga slut punkterna.
* **Prestanda**. Slut punkten som närmar sig slutanvändaren returneras. Om den här slut punkten inte är tillgänglig flyttas Traffic Manager trafik till slut punkterna i nästa närmaste Azure-region. Du kan konfigurera alternativa reserv planer för prestanda trafik routning genom att använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geografisk**. Slut punkten som mappas för att hantera den geografiska platsen baserat på frågans IP-adress returneras. Om den här slut punkten inte är tillgänglig, kommer en annan slut punkt inte att väljas för redundansväxling, eftersom en geografisk plats kan mappas till en slut punkt i en profil (mer information finns i [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Vi rekommenderar att kunder använder kapslade Traffic Manager profiler med fler än en slut punkt som profil slut punkter när de använder geografisk routning.
* **Multivärde** Flera slut punkter som har mappats till IPv4/IPv6-adresser returneras. När en fråga tas emot för den här profilen, returneras felfria slut punkter baserat på det **maximala antalet poster i svar** svärdet som du har angett. Standardvärdet för antal svar är två slut punkter.
* **Undernät** Slut punkten som är mappad till en uppsättning IP-adressintervall returneras. När en begäran tas emot från den IP-adressen, är slut punkten som returnerades den som är mappad för den IP-adressen. 

Mer information finns i [Traffic Manager metoder för trafik dirigering](traffic-manager-routing-methods.md).

> [!NOTE]
> Ett undantag till normal trafik dragnings beteende inträffar när alla kvalificerade slut punkter har statusen degraderad. Traffic Manager gör ett "bästa ansträngning"-försök och *svarar som om alla slut punkter för degraderad status faktiskt är i online-läge*. Det här beteendet är ett bättre alternativ, vilket skulle vara att inte returnera någon slut punkt i DNS-svaret. Inaktiverade eller stoppade slut punkter övervakas därför inte, anses de inte vara berättigade till trafik.
>
> Det här tillståndet orsakas ofta av felaktig konfiguration av tjänsten, till exempel:
>
> * En åtkomst kontrol lista [ACL] blockerar Traffic Manager hälso kontroller.
> * En felaktig konfiguration av övervaknings porten eller protokollet i Traffic Manager-profilen.
>
> Resultatet av detta är att om Traffic Manager hälso kontroller inte har kon figurer ATS korrekt, kan det visas från trafikroutningen på samma sätt som *om Traffic Manager fungerar* korrekt. I det här fallet kan det dock inte hända att slut punkts växlingen påverkar den övergripande program tillgängligheten. Det är viktigt att kontrol lera att profilen visar statusen online, inte en degraderad status. En onlinestatus anger att hälso kontrollerna för Traffic Manager fungerar som förväntat.

Mer information om fel sökning av misslyckade hälso kontroller finns i [Felsöka degraderad status på Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Vanliga frågor och svar

* [Är Traffic Manager elastisk till Azures regions problem?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Hur påverkar valet av resurs grupps plats Traffic Manager?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Hur gör jag för att avgöra den aktuella hälsan för varje slut punkt?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Kan jag övervaka HTTPS-slutpunkter?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Använder jag en IP-adress eller ett DNS-namn när jag lägger till en slut punkt?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Vilka typer av IP-adresser kan jag använda när jag lägger till en slut punkt?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Kan jag använda olika slut punkts typer för adressering i samma profil?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Vad händer när en post av typen inkommande fråga skiljer sig från den post typ som är kopplad till slut punkternas adress typ?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Kan jag använda en profil med IPv4/IPv6-adresserade slut punkter i en kapslad profil?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Jag har stoppat en slut punkt för ett webb program i Traffic Manager profil, men jag får ingen trafik även när jag har startat om den. Hur kan jag åtgärda detta?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Kan jag använda Traffic Manager även om mitt program saknar stöd för HTTP eller HTTPS?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Vilka speciella svar krävs från slut punkten vid användning av TCP-övervakning?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Hur snabbt kan Traffic Manager flytta mina användare bort från en felaktig slut punkt?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Hur kan jag ange olika övervaknings inställningar för olika slut punkter i en profil?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Hur kan jag tilldela HTTP-huvuden till Traffic Manager hälso kontroller i mina slut punkter?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Vilket värd huvud använder hälso kontroller för slut punkter?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Vilka IP-adresser kommer hälso kontrollerna från?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Hur många hälso kontroller till min slut punkt kan jag förväntar mig från Traffic Manager?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Hur kan jag få ett meddelande om en av mina slut punkter upphör?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Nästa steg

Lär dig [hur Traffic Manager fungerar](traffic-manager-how-it-works.md)

Läs mer om [metoderna för trafik-routning](traffic-manager-routing-methods.md) som stöds av Traffic Manager

Lär dig hur du [skapar en Traffic Manager-profil](traffic-manager-manage-profiles.md)

[Felsöka degraderad status](traffic-manager-troubleshooting-degraded.md) för en Traffic Manager slut punkt