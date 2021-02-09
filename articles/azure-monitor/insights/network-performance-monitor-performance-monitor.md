---
title: Prestanda övervakare i Övervakare av nätverksprestanda
description: Prestanda övervaknings funktionen i Övervakare av nätverksprestanda hjälper dig att övervaka nätverks anslutningen mellan olika platser i nätverket. Du kan övervaka moln distributioner och lokala platser, flera data Center och avdelnings kontor och verksamhets kritiska program på flera nivåer eller mikrotjänster.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 6502d4259aa383c1466967d01282cecfbb8d1ded
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832971"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Övervakare av nätverksprestanda lösning: prestanda övervakning

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda till den nya anslutnings övervakaren](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Prestanda övervaknings funktionen i [övervakare av nätverksprestanda](network-performance-monitor.md) hjälper dig att övervaka nätverks anslutningen mellan olika platser i nätverket. Du kan övervaka moln distributioner och lokala platser, flera data Center och avdelnings kontor och verksamhets kritiska program på flera nivåer eller mikrotjänster. Med prestanda övervakaren kan du identifiera nätverks problem innan användarna klagar. Viktiga fördelar är att du kan: 

- Övervaka förlust och svars tid i olika undernät och Ställ in aviseringar.
- Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket.
- Felsök tillfälliga och tidpunkts nätverks problem, som är svåra att replikera.
- Fastställ det speciella segmentet i nätverket, vilket är ansvars för försämrade prestanda.
- Övervaka nätverkets hälso tillstånd utan behov av SNMP.


![Övervakning av nätverksprestanda](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguration
Öppna konfigurationen för Övervakare av nätverksprestanda genom att öppna Övervakare av nätverksprestanda- [lösningen](network-performance-monitor.md)och välja **Konfigurera**.

![Konfigurera Övervakare av nätverksprestanda](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Skapa nya nätverk

Ett nätverk i Övervakare av nätverksprestanda är en logisk behållare för undernät. Det hjälper dig att organisera övervakningen av din nätverks infrastruktur utifrån dina behov. Du kan skapa ett nätverk med ett eget namn och lägga till undernät till det enligt din affärs logik. Du kan till exempel skapa ett nätverk med namnet London och lägga till alla undernät i ditt London Data Center. Du kan också skapa ett nätverk med namnet *ContosoFrontEnd* och lägga till det i det här nätverket alla undernät som heter Contoso och som hanterar appens klient del. Lösningen skapar automatiskt ett standard nätverk som innehåller alla undernät som identifierats i din miljö. 

När du skapar ett nätverk lägger du till ett undernät i det. Sedan tas det under nätet bort från standard nätverket. Om du tar bort ett nätverk returneras alla dess undernät automatiskt till standard nätverket. Standard nätverket fungerar som en behållare för alla undernät som inte finns i något användardefinierat nätverk. Det går inte att redigera eller ta bort standard nätverket. Det finns alltid i systemet. Du kan skapa så många anpassade nätverk som du behöver. I de flesta fall ordnas under näten i din organisation i fler än ett nätverk. Skapa ett eller flera nätverk för att gruppera dina undernät för din affärs logik.

Så här skapar du ett nytt nätverk:


1. Välj fliken **nätverk** .
1. Välj **Lägg till nätverk** och ange sedan nätverks namn och beskrivning. 
2. Välj ett eller flera undernät och välj sedan **Lägg till**. 
3. Välj **Spara** för att spara konfigurationen. 


### <a name="create-monitoring-rules"></a>Skapa övervaknings regler 

Prestanda övervakaren genererar hälso händelser när tröskelvärdet för prestanda för nätverks anslutningar mellan två under nätverk eller mellan två nätverk bryts. Systemet kan lära sig dessa tröskelvärden automatiskt. Du kan också ange anpassade tröskelvärden. Systemet skapar automatiskt en standard regel som genererar en hälso händelse när förlust eller fördröjning mellan ett par nätverks-eller under nätverks länkar bryter mot systemet. Den här processen hjälper lösningen att övervaka din nätverks infrastruktur tills du inte har skapat några övervaknings regler explicit. Om standard regeln är aktive rad skickar alla noder syntetiska transaktioner till alla andra noder som du har aktiverat för övervakning. Standard regeln är användbar med små nätverk. Ett exempel är ett scenario där du har ett litet antal servrar som kör en mikrotjänst och du vill se till att alla servrar har anslutning till varandra.

>[!NOTE]
> Vi rekommenderar att du inaktiverar standard regeln och skapar anpassade övervaknings regler, särskilt i stora nätverk där du använder ett stort antal noder för övervakning. Anpassade övervaknings regler kan minska trafiken som genereras av lösningen och hjälpa dig att organisera övervakningen av nätverket.

Skapa övervaknings regler enligt din affärs logik. Ett exempel är om du vill övervaka prestanda för nätverks anslutningen för två Office-platser till huvud kontoret. Gruppera alla undernät i Office-site1 i nätverk O1. Gruppera sedan alla undernät i Office-site2 i Network O2. Slutligen kan du gruppera alla undernät i huvud kontoret i nätverk H. skapa två övervaknings regler – en mellan O1 och H och den andra mellan O2 och H. 

Skapa anpassade övervaknings regler:

1. Välj **Lägg till regel** på fliken **övervaka** och ange regel namn och beskrivning.
2. Välj det par av nätverks-eller under nätverks länkar som ska övervakas från listorna. 
3. Välj det nätverk som innehåller de under nätverk som du vill använda i list rutan nätverk. Välj sedan under nätverkserna i list rutan motsvarande under nätverk. Om du vill övervaka alla under nätverk i en nätverks länk väljer du **alla under nätverk**. På samma sätt väljer du de andra under nätverk som du vill använda. Om du vill undanta övervakning för specifika under nätverks länkar från de val du gjort väljer du **Lägg till undantag**. 
4. Välj mellan ICMP-och TCP-protokoll för att köra syntetiska transaktioner. 
5. Om du inte vill skapa hälso händelser för de objekt som du har valt avmarkerar du **Aktivera hälso övervakning på länkarna som omfattas av den här regeln**. 
6. Välj övervaknings villkor. Ange tröskelvärden för att ange anpassade tröskelvärden för generering av hälso tillstånds händelser. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverket eller under nätverks paret genereras en hälso händelse. 
7. Välj **Spara** för att spara konfigurationen. 

När du har sparat en övervaknings regel kan du integrera regeln med Aviseringshantering genom att välja **skapa avisering**. En varnings regel skapas automatiskt med Sök frågan. Andra obligatoriska parametrar fylls i automatiskt. Med hjälp av en varnings regel kan du ta emot e-postbaserade aviseringar, förutom de befintliga aviseringarna inom Övervakare av nätverksprestanda. Aviseringar kan även utlösa återställnings åtgärder med Runbooks, eller så kan de integreras med befintliga service hanterings lösningar med hjälp av Webhooks. Välj **Hantera avisering** om du vill redigera aviserings inställningarna. 

Nu kan du skapa fler prestanda övervaknings regler eller flytta till lösningens instrument panel för att använda funktionen.

### <a name="choose-the-protocol"></a>Välj protokoll

Övervakare av nätverksprestanda använder syntetiska transaktioner för att beräkna nätverks prestanda mått som paket förlust och länk fördröjning. Om du vill förstå det här konceptet bättre bör du överväga en Övervakare av nätverksprestanda-agent som är ansluten till ena änden av en nätverks länk. Den här Övervakare av nätverksprestanda agenten skickar avsöknings paket till en andra Övervakare av nätverksprestanda-agent som är ansluten till en annan del av nätverket. Den andra agenten svarar med svars paket. Den här processen upprepas några gånger. Genom att mäta antalet svar och den tid det tar att ta emot varje svar, bedömer den första Övervakare av nätverksprestanda agenten länk fördröjning och paket. 

Formatet, storleken och sekvensen för dessa paket bestäms av det protokoll som du väljer när du skapar övervaknings regler. Baserat på paketets protokoll kan de mellanliggande nätverks enheterna, till exempel routrar och växlar, bearbeta paketen på olika sätt. Valet av protokoll påverkar därför precisionen för resultaten. Valet av protokoll avgör också om du måste utföra manuella steg när du har distribuerat Övervakare av nätverksprestanda-lösningen. 

Övervakare av nätverksprestanda erbjuder dig valet mellan ICMP-och TCP-protokoll för att köra syntetiska transaktioner. Om du väljer ICMP när du skapar en syntetisk transaktions regel använder Övervakare av nätverksprestanda agenter ICMP-EKOBEGÄRANMEDDELANDEN för att beräkna nätverks fördröjningen och paket förlusten. ICMP ECHO använder samma meddelande som skickas av det konventionella Ping-verktyget. När du använder TCP som protokoll skickar Övervakare av nätverksprestanda agenter TCP SYN-paket över nätverket. Det här steget följs av en slut för ande av TCP-handskakning och anslutningen tas bort med hjälp av de flesta paket. 

Överväg följande information innan du väljer ett protokoll: 

* **Identifiering av flera nätverks vägar.** TCP är mer korrekt vid identifiering av flera vägar och det behövs färre agenter i varje undernät. En eller två agenter som använder TCP kan till exempel identifiera alla redundanta sökvägar mellan undernät. Du behöver flera agenter som använder ICMP för att uppnå liknande resultat. Med hjälp av ICMP, om du har ett antal vägar mellan två undernät, behöver du fler än 5N agenter i antingen käll-eller mål under nät.

* **Resultatens noggrannhet.** Routrar och växlar brukar tilldela lägre prioritet till ICMP ECHO-paket jämfört med TCP-paket. När nätverks enheter är mycket inlästa i vissa situationer återspeglar de data som erhålls av TCP mer noggrant förlust och latens för program. Detta inträffar eftersom de flesta av program trafiken flödar över TCP. I sådana fall ger ICMP mindre exakta resultat jämfört med TCP. 

* **Brand Väggs konfiguration.** TCP-protokollet kräver att TCP-paket skickas till en mål Port. Standard porten som används av Övervakare av nätverksprestandas agenter är 8084. Du kan ändra porten när du konfigurerar agenter. Kontrol lera att nätverks brand väggarna eller reglerna för nätverks säkerhets gruppen (NSG) i Azure tillåter trafik på porten. Du måste också se till att den lokala brand väggen på datorerna där agenterna är installerade är konfigurerad för att tillåta trafik på den här porten. Du kan använda PowerShell-skript för att konfigurera brand Väggs regler på dina datorer som kör Windows, men du måste konfigurera nätverks brand väggen manuellt. ICMP fungerar däremot inte med hjälp av en port. I de flesta företags scenarier tillåts ICMP-trafik genom brand väggarna så att du kan använda verktyg för nätverksdiagnostik som Ping-verktyget. Om du kan pinga en dator från en annan kan du använda ICMP-protokollet utan att behöva konfigurera brand väggarna manuellt.

>[!NOTE] 
> Vissa brand väggar kan blockera ICMP, vilket kan leda till återöverföring som resulterar i ett stort antal händelser i din säkerhets information och händelse hanterings system. Kontrol lera att det protokoll som du väljer inte blockeras av en nätverks brand vägg eller en NSG. Annars kan inte Övervakare av nätverksprestanda övervaka nätverks segmentet. Vi rekommenderar att du använder TCP för övervakning. Använd ICMP i scenarier där du inte kan använda TCP, till exempel när: 
>
> - Du använder Windows-klientbaserade noder eftersom TCP RAW-socketar inte tillåts i Windows-klienter.
> - Nätverks brand väggen eller NSG blockerar TCP.
> - Du vet inte hur du byter protokoll.

Om du väljer att använda ICMP under distributionen kan du när som helst växla till TCP genom att redigera standard övervaknings regeln.

1. Gå till övervakaren för **nätverks prestanda**   >  ****   >  **Konfigurera**   >  **övervakaren**. Välj sedan **standard regel**. 
2. Bläddra till avsnittet **protokoll** och välj det protokoll som du vill använda. 
3. Välj **Spara** för att tillämpa inställningen. 

Även om standard regeln använder ett särskilt protokoll, kan du skapa nya regler med ett annat protokoll. Du kan även skapa en blandning av regler där vissa regler använder ICMP och andra använder TCP. 

## <a name="walkthrough"></a>Genomgång 

Nu ska vi titta på en enkel undersökning i rotor saken för en hälso händelse.

På instrument panelen för lösningen visar en hälso händelse att en nätverks länk inte är felfri. Om du vill undersöka problemet väljer du panelen **nätverks länkar som övervakas** .

Den nedrullningsbara sidan visar att nätverks länken **DMZ2-DMZ1** inte är felfri. Välj **Visa under näts länkar** för den här nätverks länken. 


På sidan öka detalj nivån visas alla under nätverks länkar i nätverks länken **DMZ2-DMZ1** . För båda under nätverks länkarna är fördröjningen överkorsade tröskelvärdet, vilket gör att nätverks länken inte är felfri. Du kan också se svars tids trender för båda under nätverks länkarna. Använd tids markerings kontrollen i diagrammet för att fokusera på det begärda tidsintervallet. Du kan se tiden på dagen då svars tiden nått sin topp. Sök igenom loggarna senare under den här tids perioden för att undersöka problemet. Välj **Visa noden länkar** om du vill öka detalj nivån ytterligare. 
 
 ![Sidan under nätverks länkar](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

På samma sätt som på föregående sida, visar den nedrullningsbara sidan för den specifika under nätverks länken dess ingående länkar. Du kan utföra liknande åtgärder på samma sätt som i föregående steg. Välj **Visa topologi** för att Visa topologin mellan de två noderna. 
 
 ![Sidan nod länkar](media/network-performance-monitor-performance-monitor/node-links.png) 

Alla sökvägar mellan de två valda noderna ritas i Topology-kartan. Du kan visualisera hopp-för-hop-topologin för vägar mellan två noder i Topology-kartan. Det ger dig en tydlig bild av hur många vägar som finns mellan de två noderna och vilka sökvägar som data paketen tar. Flask halsar i nätverks prestanda visas i rött. Om du vill hitta en felaktig nätverks anslutning eller en felaktig nätverks enhet kan du titta på de röda elementen på topologins karta. 

 ![Instrument panel för topologi med topologi](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Du kan granska förlust, svars tid och antal hopp i varje sökväg i **Åtgärds** fönstret. Använd rullnings listen för att visa information om felaktiga sökvägar. Använd filtren för att välja sökvägar med fel hopp så att topologin för endast de valda Sök vägarna ritas. Om du vill zooma in eller ut ur Topology-kartan använder du mus hjulet. 

I följande bild visas den bakomliggande orsaken till problemen med de olika delarna av nätverket i de röda Sök vägarna och hoppen. Välj en nod i topologin kartan för att visa nodens egenskaper, som innehåller FQDN och IP-adress. Om du väljer ett hopp visas hoppets IP-adress. 
 
![Topologi för topologi med valda egenskaper](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Nästa steg
[Sök i loggar](../log-query/log-query-overview.md) om du vill visa detaljerade data poster för nätverks prestanda.
