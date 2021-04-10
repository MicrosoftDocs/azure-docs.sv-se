---
title: Styr vilken trafik som övervakas
description: Sensorer utför automatiskt djup identifiering av paket för IT-trafiken och löser information om nätverks enheter, till exempel enhetens attribut och nätverks beteendet. Det finns flera verktyg för att styra vilken typ av trafik som varje sensor identifierar.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784449"
---
# <a name="control-what-traffic-is-monitored"></a>Styr vilken trafik som övervakas

Sensorer utför automatiskt djup identifiering av paket för IT-trafiken och löser information om nätverks enheter, till exempel enhetens attribut och beteende. Det finns flera verktyg för att styra vilken typ av trafik som varje sensor identifierar.

## <a name="learning-and-smart-it-learning-modes"></a>Lägen för inlärning och smart IT-utbildning

Inlärnings läget instruerar din sensor att lära sig din nätverks vanliga aktivitet. Exempel är enheter som identifierats i nätverket, protokoll som identifierats i nätverket, fil överföringar mellan vissa enheter med mera. Den här aktiviteten blir din nätverks bas linje.

Inlärnings läget aktive ras automatiskt efter installationen och förblir aktiverat tills det stängs av. Den ungefärliga inlärnings läges perioden är mellan två och sex veckor, beroende på nätverkets storlek och komplexitet. När inlärnings läget är inaktiverat efter den här perioden kommer alla nya aktiviteter att utlösa aviseringar. Aviseringar utlöses när princip motorn identifierar avvikelser från din inlärt bas linje.

När inlärnings perioden har slutförts och inlärnings läget har inaktiverats kan sensorn identifiera en ovanligt hög bas linje ändringar som är resultatet av normal IT-aktivitet, till exempel DNS-och HTTP-begäranden. Aktiviteten kallas icke deterministiska IT-beteende. Beteendet kan även utlösa onödiga varningar om princip överträdelser och system meddelanden. För att minska mängden av dessa aviseringar och aviseringar kan du aktivera funktionen **smart IT-inlärning** .

När smart IT-inlärning är aktiverat spårar sensorn nätverks trafik som genererar icke-deterministiska IT-beteenden baserat på vissa aviserings scenarier.

Sensorn övervakar trafiken i sju dagar. Om samma icke-deterministiska IT-trafik identifieras inom sju dagar fortsätter den att övervaka trafiken under ytterligare sju dagar. Om trafiken inte upptäcks under en fullständig sju dagar, är smart IT-inlärning inaktiverat för det scenariot. Ny trafik som har identifierats för det scenariot kommer bara att generera aviseringar och meddelanden.

Genom att arbeta med smart IT-utbildning kan du minska antalet onödiga aviseringar och meddelanden som orsakas av störningar i IT-scenarier.

Om sensorn styrs av den lokala hanterings konsolen kan du inte inaktivera inlärnings lägena. I sådana fall kan inlärnings läget endast inaktive ras från hanterings konsolen.

Inlärnings funktionerna (inlärning och smart IT-utbildning) är aktiverade som standard.

Aktivera eller inaktivera inlärning:

- Välj **Systeminställningar** och växla mellan alternativen **inlärning** och **smart IT-utbildning** .

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Växla skärm för Systeminställningar.":::

## <a name="configure-subnets"></a>Konfigurera undernät

Under näts konfigurationerna påverkar hur du ser enheter i enhets kartan.

Sensorn upptäcker som standard under näts konfigurationen och fyller i dialog rutan **under näts konfiguration** med den här informationen.

För att aktivera fokus på enheter med samma enhet sammanställs IT-enheter automatiskt av under nätet i enhets kartan. Varje undernät presenteras som en enda enhet på kartan, inklusive en interaktiv och utöknings möjlighet att "öka detalj nivån" till ett IT-undernät och tillbaka.

När du arbetar med undernät väljer du under nätet för Internet-anslutningsdelning för att identifiera de andra undernätet. Du kan sedan fokusera på Map-vyn i nätverk med olika nätverk och minimera till en minimal presentation av IT-nätverksens element. Den här ansträngningen minskar det totala antalet enheter som visas på kartan och ger en tydlig bild av elementen för nätverks-och ICS-nätverket.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Skärm bild som visar filtrering till ett nätverk.":::

Du kan ändra konfigurationen eller ändra informationen i under nätet manuellt genom att exportera identifierade data, ändra den manuellt och sedan importera listan över undernät som du har definierat manuellt. Mer information om export och import finns i [Importera enhets information](how-to-import-device-information.md).

I vissa fall, till exempel miljöer som använder offentliga intervall som interna intervall, kan du instruera sensorn att matcha alla undernät som interna undernät genom att välja alternativet **identifiera inte Internet aktivitet** . När du väljer det alternativet:

- Offentliga IP-adresser kommer att behandlas som lokala adresser.

- Inga aviseringar skickas om otillåten Internet aktivitet, vilket minskar aviseringar och aviseringar som tas emot på externa adresser.

Konfigurera undernät:

1. På menyn på sidan väljer du **Systeminställningar**.

2. I fönstret **systeminställning** väljer du **undernät**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Skärm bild som visar skärmen för under näts konfiguration."::: 

3. Om du vill lägga till undernät automatiskt när nya enheter identifieras, ska du behålla **inlärningen för automatiska undernät** markerade.

4. Om du vill matcha alla undernät som interna undernät väljer du **identifiera inte Internet aktivitet**.

5. Välj **Lägg till nätverk** och definiera följande parametrar för varje undernät:

    - Under nätets IP-adress.
    - Nät maskens adress.
    - Under näts namnet. Vi rekommenderar att du namnger varje undernät med ett meningsfullt namn som du lätt kan identifiera, så att du kan skilja mellan IT-nätverk. Namnet kan bestå av upp till 60 tecken.

6. Om du vill markera det här under nätet som ett nätverks under nät väljer du **ICS-undernät**.

7. Om du vill presentera under nätet separat när du ordnar kartan enligt Purdue-nivå väljer du **åtskiljd**.

8. Om du vill ta bort ett undernät väljer du :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Om du vill ta bort alla undernät väljer du **Rensa alla**.

10. Om du vill exportera konfigurerade undernät väljer du **Exportera**. Under näts tabellen laddas ned till din arbets Station.

11. Välj **Spara**.

### <a name="importing-information"></a>Importera information 

Om du vill importera information om undernät väljer du **Importera** och väljer en CSV-fil som ska importeras. Under näts informationen uppdateras med den information som du har importerat. Om du är intresse post av ett tomt fält förlorar du dina data.

## <a name="detection-engines"></a>Identifierings motorer

Självstudien analys motorer eliminerar behovet av att uppdatera signaturer eller definiera regler. Motorerna använder ICS-baserad beteende analys och data vetenskap för att kontinuerligt analysera nätverks trafik med avvikelser, skadlig kod, drift problem, protokoll överträdelser och bas linje nätverks aktivitetens avvikelser.

> [!NOTE]
> Vi rekommenderar att du aktiverar alla säkerhets motorer.

När en motor identifierar en avvikelse utlöses en avisering. Du kan visa och hantera aviseringar från aviserings skärmen eller från ett partner system.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Skärm bild som visar identifiering av avvikelser.":::

Namnet på den motor som utlöste aviseringen visas under aviserings rubriken.

### <a name="protocol-violation-engine"></a>Protokolls brotts motor 

Ett protokoll fel uppstår när paket strukturen eller fält värden inte följer protokoll specifikationen.

Exempel scenario: *"Ogiltig Modbus-åtgärd (funktions kod noll)"* -avisering. Den här varningen anger att en primär enhet skickade en begäran med funktions kod 0 till en sekundär enhet. Den här åtgärden tillåts inte enligt protokoll specifikationen, och den sekundära enheten kanske inte kan hantera inaktuella inmatade enheter.

### <a name="policy-violation-engine"></a>Princip överträdelse motor

En princip överträdelse inträffar med en avvikelse från bas linje beteendet som definierats i inlärda eller konfigurerade inställningar.

Exempel scenario: *"obehörig HTTP-användaragent"-* avisering. Den här varningen anger att ett program som inte har lärts eller godkänts av en princip används som en HTTP-klient på en enhet. Detta kan vara en ny webbläsare eller ett annat program på den enheten.

### <a name="malware-engine"></a>Motor för skadlig kod

Motorn för skadlig kod identifierar skadlig nätverks aktivitet.

Exempel scenario: *"misstanke om skadlig aktivitet (Stuxnet)"-* avisering. Den här varningen anger att sensorn identifierade misstänkt nätverks aktivitet som har visat sig vara relaterad till den skadliga kod som Stuxnet. Den här skadliga koden är ett avancerat beständigt hot som är avsett för industriella kontroll-och SCADA-nätverk.

### <a name="anomaly-engine"></a>Avvikelse motor

Avvikelse motorn identifierar avvikelser i nätverks beteendet.

Exempel scenario: *"periodiska beteende i kommunikations kanalen"* -avisering. Komponenten kontrollerar nätverks anslutningar och hittar periodisk och cykliskt beteende för data överföring. Det här beteendet är vanligt i industriella nätverk.

### <a name="operational-engine"></a>Drift motor

Drifts motorn identifierar drifts incidenter eller fel enheter.

Exempel scenario: *"enheten misstänks vara frånkopplad)"* varning. Den här aviseringen utlöses när en enhet inte svarar på någon typ av begäran för en fördefinierad period. Den här aviseringen kan tyda på avstängning av enheten, från koppling eller fel.

### <a name="enable-and-disable-engines"></a>Aktivera och inaktivera motorer

När du inaktiverar en princip motor kommer den information som motorn genererar inte att vara tillgänglig för sensorn. Om du till exempel inaktiverar avvikelse motorn får du inga varningar om nätverks avvikelser. Om du har skapat en regel för vidarebefordran kommer avvikelser som motorn lär sig inte att skickas. Om du vill aktivera eller inaktivera en princip motor väljer du **aktive rad** eller **inaktive** rad för den aktuella motorn.

Det totala resultatet visas i det nedre högra hörnet på skärmen **system inställningar** . Poängen anger hur stor procent andel av det tillgängliga skyddet som är aktiverat via skydds motorer. Varje motor är 20 procent av det tillgängliga skyddet.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Skärm bild som visar poängen.":::

## <a name="configure-dhcp-address-ranges"></a>Konfigurera DHCP-adressintervall

Nätverket kan bestå av både statiska och dynamiska IP-adresser. Normalt hittas statiska adresser på nätverks växlings nätverk via historians, styrenheter och nätverks infrastruktur enheter som växlar och routrar. Dynamisk IP-allokering implementeras vanligt vis i gäst nätverk med bärbara datorer, datorer, smartphones och annan portabel utrustning (med hjälp av Wi-Fi eller LAN-fysiska anslutningar på olika platser).

Om du arbetar med dynamiska nätverk hanterar du ändringar av IP-adress som inträffar när nya IP-adresser tilldelas. Du gör detta genom att definiera DHCP-adressintervall.

Ändringar kan inträffa, till exempel när en DHCP-server tilldelar IP-adresser.

Genom att definiera dynamiska IP-adresser på varje sensor kan du använda omfattande, transparent stöd i instanser av IP-adress ändringar. Detta säkerställer omfattande rapportering för varje unik enhet.

Sensor konsolen visar den mest aktuella IP-adressen som är kopplad till enheten och indikerar vilka enheter som är dynamiska. Exempel:

- Rapporten data utvinnings rapport och enhets inventering sammanför all aktivitet som har lärts från enheten som en enhet, oavsett IP-adressen. De här rapporterna visar vilka adresser som har definierats som DHCP-adresser.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Skärm bild som visar enhets inventering.":::

- Fönstret **enhets egenskaper** visar om enheten har definierats som en DHCP-enhet.

Ange ett DHCP-adressintervall:

1.  På menyn på sidan väljer du **DHCP-intervall** i fönstret **Systeminställningar** .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Skärm bild som visar valet av DHCP-intervall.":::

2.  Definiera ett nytt intervall genom att ställa in **från** och **till** värden.

3.  Alternativt: definiera intervall namnet, upp till 256 tecken.

4.  Om du vill exportera intervallen till en CSV-fil väljer du **Exportera**.

5. Om du vill lägga till flera intervall från en CSV-fil manuellt väljer du **Importera** och sedan filen.

    > [!NOTE]
    > De intervall som du importerar från en CSV-fil skriver över de befintliga intervall inställningarna.

6. Välj **Spara**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Konfigurera DNS-servrar för omvänd söknings upplösning

För att förbättra enhets anrikningen kan du konfigurera flera DNS-servrar för omvänd sökning. Du kan matcha värdnamn eller FQDN som är associerade med de IP-adresser som identifieras i nätverks under nät. Om en sensor exempelvis identifierar en IP-adress kan den fråga flera DNS-servrar för att matcha värd namnet.

Alla CIDR-format stöds.

Värd namnet visas i enhetens inventering och enhets karta och i rapporter.

Du kan schemalägga matchnings scheman för omvänd sökning för olika Tim intervall, till exempel var 12: e timme. Eller så kan du schemalägga en angiven tid.

Så här definierar du DNS-servrar:

1. Välj **Systeminställningar** och välj sedan **DNS-inställningar**.

2. Välj **Lägg till DNS-Server**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Skärm bild som visar valet av Lägg till DNS-server.":::

3. I fältet **Schemalägg omvänd DNS-sökning** väljer du antingen:

    - Intervall (per timme).
  
    - En angiven tid. Använd europeisk formatering. Använd till exempel **14:30** och inte **2:30 PM**.

4. I fältet **DNS-serveradress** anger du DNS-IP-adressen.

5. I fältet **DNS-server port** anger du DNS-porten.

6. Matcha IP-adresser för nätverket till enhetens FQDN. I fältet **antal etiketter** lägger du till antalet domän etiketter som ska visas. Upp till 30 tecken visas från vänster till höger.

7. I fältet **undernät** anger du de undernät som du vill att DNS-servern ska fråga.

8. Välj **Aktivera** växling om du vill initiera omvänd sökning.

### <a name="test-the-dns-configuration"></a>Testa DNS-konfigurationen 

Genom att använda en testen het kontrollerar du att de inställningar som du har definierat fungerar korrekt:

1. Aktivera växling av **DNS-sökning** .

2. Välj **Testa regel**.

3. Ange en adress i **uppslags adress** för **DNS-testet för omvänd DNS-sökning för Server** dialog rutan.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Skärm bild som visar Sök adress fältet.":::

4. Välj **Testa regel**.

## <a name="configure-windows-endpoint-monitoring"></a>Konfigurera övervakning av Windows-slutpunkt

Med Windows Endpoint Monitoring-funktionen kan du konfigurera Azure Defender för IoT att selektivt avsöka Windows-system. Detta ger dig mer fokuserad och korrekt information om dina enheter, till exempel service pack nivåer.

Du kan konfigurera avsökning med ett visst intervall och värdar och konfigurera det så att det bara utförs så ofta som du vill. Du utför selektiv avsökning med hjälp av Windows Management Instrumentation (WMI), som är Microsofts standard skript språk för att hantera Windows-system.

> [!NOTE]
> - Du kan bara köra en genomsökning i taget.
> - Du får bäst resultat med användare som har behörighet som domän eller lokal administratör.
> - Innan du startar WMI-konfigurationen konfigurerar du en brand Väggs regel som öppnar utgående trafik från sensorn till det genomsökta under nätet med hjälp av UDP-port 135 och alla TCP-portar över 1024.

När avsökningen är färdig är en loggfil med alla avsöknings försök tillgängliga från alternativet att exportera en logg. Loggen innehåller alla IP-adresser som avsöktes. I loggen visas information om lyckade och misslyckade för varje IP-adress. Det finns också en felkod, som är en kostnads fri sträng som härletts från undantaget. Genomsökningen av den senaste loggen sparas bara i systemet.

Du kan utföra schemalagda genomsökningar eller manuella genomsökningar. När en genomsökning är färdig kan du visa resultatet i en CSV-fil.

**Förutsättningar**

Konfigurera en brand Väggs regel som öppnar utgående trafik från sensorn till det genomsökta under nätet med hjälp av UDP-port 135 och alla TCP-portar över 1024.

Så här konfigurerar du en automatisk sökning:

1. På menyn på sidan väljer du **Systeminställningar**.

2. Välj **övervakning av Windows-slutpunkt** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Skärm bild som visar valet av övervakning av Windows-slutpunkt.":::

3. I fönstret **skannings schema** konfigurerar du alternativen enligt följande:

      - **Efter fasta intervall (i timmar)**: Ange genomsöknings schema enligt intervall i timmar.

      - **Vid vissa tidpunkter**: Ange genomsöknings schema enligt vissa tider och välj **Spara sökning**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Skärm bild som visar knappen Spara skanning.":::

4. Definiera skannings intervallet genom att välja **Ange skannings intervall**.

5. Ange IP-adressintervall och Lägg till användare och lösen ord.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Skärm bild som visar hur du lägger till en användare och ett lösen ord.":::

6. Om du vill undanta ett IP-intervall från en genomsökning väljer du **inaktivera** bredvid intervallet.

7. Om du vill ta bort ett intervall väljer du :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: bredvid intervallet.

8. Välj **Spara**. Dialog rutan **Redigera skannings intervalls konfiguration** stängs och antalet intervall visas i fönstret **skannings intervall** .

Utföra en manuell genomsökning:

1. På menyn på sidan väljer du **Systeminställningar**.

2. Välj **övervakning av Windows-slutpunkt** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Skärm bild som visar installations skärmen för Windows Endpoint Monitoring.":::

3. I fönstret **åtgärder** väljer du **starta genomsökning**. Ett statusfält visas i **Åtgärds** fönstret och visar förloppet för skannings processen.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Skärm bild som visar knappen Starta skanning.":::

Visa genomsöknings resultat:

1. När genomsökningen är färdig väljer du **Visa Sök Resultat** i fönstret **åtgärder** . CSV-filen med genomsöknings resultaten hämtas till datorn.

## <a name="see-also"></a>Se även

[Undersök sensor identifieringar i en enhets inventering](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Undersök sensor identifieringar i enhets kartan](how-to-work-with-the-sensor-device-map.md)
