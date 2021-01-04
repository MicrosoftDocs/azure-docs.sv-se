---
title: Fakturor för Azure Enterprise-registrering
description: Den här artikeln förklarar hur du hanterar och använder din Azure Enterprise-faktura.
author: bandersmsft
ms.author: banders
ms.date: 12/09/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: c64127f7ebbb416d9dfebb5e417cd27d51697665
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030730"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Fakturor för Azure Enterprise-registrering

Den här artikeln förklarar hur du hanterar och använder fakturan för ditt Azure Enterprise-avtal (Azure EA). Din faktura är en representation av din räkning. Kontrollera att den är korrekt. Du bör även bekanta dig med andra uppgifter som kan behövas för hantering av din faktura.

## <a name="view-usage-summary-and-download-reports"></a>Visa användningssammanfattning och nedladdning av rapporter

Företagsadministratörer kan visa en sammanfattning av användningsdata, förbrukad Azure-förskottsbetalning samt avgifter som är kopplade till ytterligare användning i Azure Enterprise-portalen. Avgifter presenteras på sammanfattningsnivå för alla konton och prenumerationer.

Om du vill visa detaljerad användning för specifika konton laddar du ned rapporten med användningsinformation:

1. Logga in på Azure Enterprise-portalen.
1. Välj **Rapporter**.
1. Välj fliken **Ladda ned användning**.
1. I listan över rapporter väljer du **Ladda ned** för den månadsrapport som du vill hämta.

   > [!NOTE]
   > Rapporten med användningsinformation inkluderar inga tillämpliga skatter.
   >
   > Det kan finnas en svarstid på upp till åtta timmar från den tidpunkt då användningen började gälla till dess att den återspeglas i rapporten.

Så här visar du rapporter och diagram för användningssammanfattning:

1. Logga in på Azure Enterprise-portalen.
1. Välj en förskottsbetalningsperiod.
   Om du vill ändra datumintervallet för **Användningssammanfattning** kan du växla från **M** (månatligen) till **C** (anpassad, Custom) längst upp till höger på sidan och sedan ange anpassade startdatum och slutdatum.  
   ![Skapa och visa användningssammanfattning och ladda ned rapporter i anpassad vy](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Om du vill visa mer information kan du välja en period eller månad i diagrammet.
   - Diagrammet visar användning månad över månad med en analys av utnyttjad användning, tjänstöverförbrukning, separat debiterade avgifter samt Azure Marketplace-avgifter.
   - För den valda månaden kan du använda fälten nedanför diagrammet för att filtrera efter avdelningar, konton och prenumerationer.
   - Du kan växla mellan **Avgifter efter tjänster** och **Avgifter efter hierarki**.
   - Visa information från **Azure-tjänst**, **Separat debiterade avgifter** och **Azure Marketplace** genom att expandera respektive avsnitt.

Titta på den här videon för att se hur du visar användning:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Ladda ned CSV-rapporter

Företagsadministratörer använder sidan Nedladdning av månadsrapport för att ladda ned följande rapporter som CSV-filer:

- Saldo och avgifter
- Användningsinformation
- Azure Marketplace-avgifter
- Prisdokument

Så här laddar du ned rapporter:

1. Välj **Rapporter** i Azure Enterprise-portalen.
2. Välj **Ladda ned användning** längst upp på sidan.
3. Välj **Ladda ned** intill månadens rapport.

   > [!NOTE]
   > Det kan förekomma en fördröjning på upp till 72 timmar från det aktuella användningsdatumet tills användningen visas i rapporten.
   >
   > Användare som laddar ned CSV-filer med Safari till Excel kan få formateringsfel. Undvik fel genom att öppna filen med ett redigeringsprogram.

![Exempel som visar sidan Ladda ned användning](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Titta på den här videon för att se hur du laddar ned användningsinformation:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Nedladdning av avancerad rapport

Du kan använda nedladdning av avancerad rapport om du vill få rapporter som omfattar specifika datumintervall eller konton. Utdatafilen är i CSV-format för att tillgodose stora uppsättningar av poster.

1. I Azure Enterprise-portalen väljer du **Nedladdning av avancerad rapport**.
1. Välj ett lämpligt datumintervall och lämpliga konton.
1. Välj **Request Usage Data** (Begär användningsdata).
1. Välj knappen **Uppdatera** tills rapportens status uppdateras till **Ladda ned**.
1. Ladda ned rapporten.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Ladda ned användningsrapporter och faktureringsinformation för en tidigare registrering

Du kan ladda ned användningsrapporter och faktureringsinformation för en tidigare registrering efter att en registreringsöverföring har skett. Historisk rapportering finns i både Azure Enterprise-portalen och i Cost Management.

Azure Enterprise-portalen filtrerar bort inaktiva registreringar från vyn. Du behöver avmarkera kryssrutan **Aktiva** om du vill visa inaktiva överförda registreringar.  

![Om rutan Aktiva avmarkeras kan användaren se inaktiva registreringar](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Ändra ett IO-nummer för en kommande överförbrukningsfaktura

Azure Enterprise-portalen genererar automatiskt ett standardmässigt inköpsordernummer (IO) såvida inte företagsadministratören anger ett före fakturadatumet. Företagsadministratörer kan uppdatera inköpsordernumret upp till sju dagar från och med att det automatiska fakturameddelandet tagits emot via e-post.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Azure-tjänster:

1. I Azure Enterprise-portalen väljer du **Rapport** > **Användningssammanfattning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet.
1. Välj alternativknappen **Azure-tjänster**.
1. Välj en **fakturaperiod** i listrutan med datumintervall.

   Du kan redigera ett inköpsordernummer inom sju dagar efter att du fått ett fakturameddelande, men det måste vara innan du betalar fakturan.
1. Ange ett nytt IO-nummer i fältet **IO-nummer**.
1. Välj **Spara** för att spara ändringen.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Så här uppdaterar du inköpsordernumret för Azure Marketplace:

1. I Azure Enterprise-portalen väljer du **Rapport** > **Användningssammanfattning**.
1. Välj **Redigera IO-nummer** i det övre högra hörnet.
1. Välj alternativknappen **Marketplace**.
1. Välj en **fakturaperiod** i listrutan med datumintervall.

   Du kan redigera ett inköpsordernummer inom sju dagar efter att du fått ett fakturameddelande, men det måste vara innan du betalar fakturan.
1. Ange ett nytt IO-nummer i fältet **IO-nummer**.
1. Välj **Spara** för att spara ändringen.

## <a name="azure-enterprise-billing-frequency"></a>Faktureringsfrekvens för Azure Enterprise

Microsoft fakturerar årligen på registreringsdatumet köp av Microsoft Azure-tjänsterna som betalas i förskott. Microsoft fakturerar i efterskott för all användning som överstiger beloppet för en förskottsbetalning.

- Avgifter för förskottsbetalningar beräknas baserat på månadspris och faktureras årligen i förväg.
- Avgifter för överförbrukning beräknas varje månad och faktureras i efterskott i slutet av faktureringsperioden.

### <a name="billing-intervals"></a>Faktureringsintervall

Faktureringsintervallet beror på hur du väljer att göra dina köp med förskottsbetalning. Din årliga förskottsbetalning sammanfaller med antingen:

- årsdagen för registreringen eller
- giltighetsdatumet för din ettåriga tilläggsprenumeration.

Datumet du fick på överförbrukningsfakturan beror på startdatumet och konfigurationen:

- **Direktregistreringar med startdatum före 1 maj 2018**:
  - Om du har ett Enterprise Azure-avtal (EA) med direktregistrering har du en årlig faktureringsperiod för Azure-tjänster, exklusive Azure Marketplace-tjänster. Din faktureringsperiod baseras på årsdagen: det datum då avtalet började gälla.
  - Om du överskrider 150 % av ditt tröskelbelopp för förskottsbetalning för Azure EA kommer du automatiskt att konverteras till kvartalsvis fakturering som baseras på din årsdag. Du får också en överförbrukningsfaktura för Azure-tjänsten.
  - Om du inte överskrider 150 % av ditt tröskelbelopp för Azure-förskottsbetalning kommer registreringen att behålla den årliga faktureringsperioden. Överförbrukningsfakturan erhålls i slutet av året för förskottsbetalningen.

- **Direktregistreringar med startdatum efter 1 maj 2018**:
  - Kundens Azure-förbrukning och avgifter faktureras på separata fakturor per månad.
  - Eventuella avgifter som inte täcks av din Azure-förskottsbetalning betalas som överförbrukning.  

- **Indirekta registreringar med en registrering som startat före 1 maj 2018**:

  Om du har ett Enterprise-avtal med indirekt registrering med ett startdatum före den 1 maj 2018 faktureras du kvartalsvis. Kanalpartnern fakturerar dig direkt.  

- **Indirekta registreringar med startdatum efter 1 maj 2018**:

  Du faktureras per månad.  

### <a name="increase-your-azure-prepayment"></a>Öka din Azure-förskottsbetalning

Du kan när som helst öka din förskottsbetalning. Du debiteras för det återstående antalet månader i det årets förskottsbetalningsperiod. Om du till exempel registrerar dig för en ettårig tilläggsprenumeration och ökar förskottsbetalningen under månad sex, faktureras du för de återstående sex månaderna i den perioden. Förskottsbetalningen uppdateras sedan för de sista sex månaderna i förskottsbetalningsperioden. Dessa nya kvantiteter används för att fastställa eventuella överförbrukningsavgifter.

### <a name="overage"></a>Överanvändning

Du debiteras för överförbrukning för den användning eller de reservationer som överskrider förskottsbetalningen under faktureringsperioden. Om du vill veta hur överförbrukningen har beräknats för enskilda poster kan du se rapporten med en sammanfattning av användningen eller kontakta din kanalpartner.

För varje post på fakturan visas:

- **Beräknat belopp**: det totala antalet avgifter
- **Utnyttjad förskottsbetalning**: beloppet för din förskottsbetalning som används för att täcka avgifterna
- **Nettobelopp**: de avgifter som överstiger din förskottsbetalning

Tillämpliga skatter beräknas bara på det nettobelopp som överstiger förskottsbetalningen.

Faktureringen av överförbrukning sker automatiskt. Tiden för meddelanden och fakturor beror på slutdatumet för din faktureringsperiod.

- En avisering om överförbrukning skickas vanligtvis sju dagar efter slutdatumet för faktureringen.
- Överförbrukningsfakturor skickas sju till nio dagar efter meddelandet.
- Du kan granska avgifterna och uppdatera systemgenererade IO-nummer under de sju dagarna mellan överförbrukningsmeddelandet och faktureringen.

### <a name="azure-marketplace"></a>Azure Marketplace

Från och med faktureringsperioden april 2019 får kunder en enskild Azure-faktura eftersom vi har slagit ihop Azure- och Azure Marketplace-debiteringarna i en enda faktura i stället för två separata fakturor. Den här ändringen påverkar inte kunder i Australien, Japan eller Singapore.

Under övergången till en kombinerad faktura får du en delfaktura för Azure Marketplace. Den här slutliga separata fakturan visar Azure Marketplace-avgifterna som uppstått före datumet för din faktureringsuppdatering. Azure Marketplace-avgifter som uppstått efter detta datum kommer att visas på din Azure-faktura. Efter övergångsperioden visas alla Azure-avgifter och Azure Marketplace-avgifter på den kombinerade fakturan.  

### <a name="adjust-billing-frequency"></a>Justera faktureringsfrekvensen

Faktureringsfrekvensen för en kund är årlig, kvartalsvis eller månatlig. Faktureringsperioden fastställs när en kund signerar sitt avtal. Månatlig fakturering är det kortaste faktureringsintervallet.

- **Godkännande** från en företagsadministratör krävs för att ändra en faktureringscykel från årlig till kvartalsvis för direkta registreringar. Godkännande från en partneradministratör krävs för indirekta registreringar. Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.
- **En ändring** i avtalet krävs för att ändra en faktureringsperiod från årlig eller kvartalsvis till månatlig.  Eventuella ändringar av den befintliga faktureringscykeln för registrering kräver godkännande från en företagsadministratör eller från din kontaktperson för faktureringen.
- **Skicka** ditt godkännande till [supporten via Azure Enterprise-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Välj problemkategori: **Fakturering**.

Ändringen träder i kraft i slutet av det aktuella faktureringskvartalet.

Om en Amendment M503 (ändring) signeras kan du flytta valfritt avtal från valfri frekvens till månatlig fakturering. 

### <a name="request-an-invoice-copy"></a>Begära en fakturakopia

Kontakta din partner om du vill begära en kopia av din faktura.

## <a name="credits-and-adjustments"></a>Krediter och justeringar

Du kan visa alla krediter eller justeringar som tillämpats för din registrering i avsnittet **Rapporter** i [Azure Enterprise-portalen](https://ea.azure.com).

Så här visar du krediter:

1. I [Azure Enterprise-portalen](https://ea.azure.com) väljer du avsnittet **Rapporter**.
1. Välj **Användningssammanfattning**.
1. Ändra vyn **M** till **C** i det övre högra hörnet.
1. Utöka justeringsfältet i tabellen med förskottsbetalningar för Azure-tjänsten.
1. Därmed visas de krediter som används för registreringen och en kort förklaring. Ett exempel: Kredit enligt serviceavtal.

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Betala din överförbrukning med din Azure-förskottsbetalning

Om du vill använda din Azure-förskottsbetalning för överförbrukning måste du uppfylla följande kriterier:

- Du har ådragit dig överförbrukningsavgifter som inte har betalats, och som gäller inom ett år efter den fakturerade tjänstens slutdatum.
- Din tillgängliga Azure-förskottsbetalning omfattar det fullständiga antalet gällande avgifter, inklusive alla tidigare ej betalda Azure-fakturor.
- Den faktureringsperiod som du vill slutföra för måste vara helt avslutad. Fakturering avslutas helt efter den femte dagen varje månad.
- Den faktureringsperiod som du vill förskjuta för måste vara helt avslutad.
- Din Azure-förskottsbetalningsrabatt baseras på den faktiska nya förskottsbetalningen minus eventuella belopp som planeras för den tidigare förbrukningen. Detta krav gäller endast för aktuella överförbrukningsavgifter. Detta gäller endast för tjänster som förbrukar Azure-förskottsbetalningar, och gäller därmed inte Azure Marketplace-avgifter. Azure Marketplace-avgifter faktureras separat.

Om du vill slutföra en överförbrukningsförskjutning kan du eller kontoteamet skapa en supportbegäran. Ett e-postmeddelande med godkännande från företagsadministratören eller faktureringskontakten krävs.

## <a name="move-charges-to-another-enrollment"></a>Flytta avgifter till en annan registrering

Användningsdata flyttas bara när en överföring antedateras. Det finns två alternativ för att flytta användningsdata från en registrering till en annan:

- Kontoöverföringar från en registrering till en annan registrering
- Registreringsöverföringar från en registrering till en annan registrering

För endera alternativet måste du skicka en [supportbegäran](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) till EA-supportteamet för att få hjälp. 

## <a name="enterprise-agreement-usage-calculations"></a>Användningsberäkningar för Enterprise-avtal

Grundläggande information om offentliga priser, måttenheter, vanliga frågor och svar och riktlinjer för användningsrapportering för varje enskild tjänst finns i [Azure-tjänster](https://azure.microsoft.com/services/) och [Azure-priser](https://azure.microsoft.com/pricing/). Du hittar mer information om EA-beräkningar i följande avsnitt.

### <a name="enterprise-agreement-units-of-measure"></a>Måttenhet för Enterprise-avtal

Enheten för Enterprise-avtal skiljer sig ofta från andra program som prenumerationsavtalet för Microsoft Online (MOSA). Den här skillnaden innebär att för ett antal tjänster aggregeras måttenheten för att ge det normaliserade priset. Den måttenhet som visas i användningssammanfattningsvyn i Azure Enterprise-portalen är alltid Enterprise-måttet. En fullständig lista över aktuella måttenheter och omvandlingar för varje tjänst finns i Excel-filen [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konvertering mellan rapporten för användningsinformation och sidan med sammanfattning av användning

I den nedladdningsbara rapporten med användningsdata kan du se rådata för resursanvändning med upp till sex decimaler. Men användningsdata som visas på Azure Enterprise-portalen avrundas till fyra decimaler för förskottsbetalningsenheter och trunkeras till noll decimaler för överförbrukningsenheter. Rådata om användningen avrundas till fyra decimaler innan de konverteras till enheter som används på Azure Enterprise-portalen. Sedan avrundas de konverterade Enterprise-enheterna om igen till fyra decimaler. Du kan endast se de faktiska förbrukade timmarna före konverteringen i den nedladdningsbara rapporten för användning och inte i Azure Enterprise-portalen.

Ett exempel: Anta att 694.533404 är det faktiska antalet SQL Server-timmar som rapporteras i rapporten för användningsinformation. Dessa enheter konverteras till 6.94533404 enheter för 100 beräkningstimmar, och avrundas sedan till 6.9453 som visas i Azure Enterprise-portalen.

- För att fastställa det beräknade faktureringsbeloppet multipliceras dessa enheter sedan med enhetspriset för förskottsbetalningar och resultatet trunkeras till två decimaler. För japanska yen (JPY) och koreanska won (Won) avrundas det beräknade beloppet till noll decimaler.
- För överförbrukning trunkeras de fakturerbara enheterna till sex decimaler och multipliceras sedan med enhetspriset för överförbrukning för att fastställa det beräknade faktureringsbeloppet.
- För fakturering av hanterad tjänstleverantör (MSP) trunkeras all användning som är kopplad till en avdelning som är markerad som MSP till noll decimaler efter konvertering till EA-måttenheten. Detta innebär att summan av den här användningen kan bli lägre än totalsumman av all användning i Azure Enterprise-portalen. Det beror på om MSP är inom saldot för Azure-förskottsbetalningen eller om användningen räknas som överförbrukning.

### <a name="graduated-pricing"></a>Graderat pris

Priset för Enterprise-avtal inkluderar för närvarande inte graderat pris där priset per enhet minskar när användningen ökar. När du byter från ett MOSA-program med graderat pris till ett Enterprise-avtal sätts priset i proportion till tjänstens basnivå minus efter justering för eventuella rabatter för Enterprise-avtalet.

### <a name="partial-hour-billing"></a>Debitering per deltimme

All debiterad användning baseras på minuter som konverteras till deltimmar i stället för att räknas upp till heltimmar. De listade Enterprise-timpriserna motsvarar det totala antalet timmar plus deltimmar.

### <a name="average-daily-consumption"></a>Genomsnittlig daglig förbrukning

Vissa tjänster prissätts månadsvis, men användningen rapporteras per dag. I dessa fall utvärderas användningen en gång per dag, dividerat med 31 och räknas samman för antalet dagar i den aktuella faktureringsmånaden. Det innebär att priserna aldrig är högre än förväntat under en månad och att de är något lägre för de månader som har mindre än 31 dagar.

### <a name="compute-hours-conversion"></a>Konvertering av beräkningstimmar

Före 28 januari 2016 angavs all användning för virtuella A0-, A2-, A3- och A4- Standard-och Basic-datorer och molntjänster som mätarminuter för virtuella A1-datorer. Virtuella A0-datorer räknades som bråkdelar av virtuella A1-datorer medan A2, A3 och A4 konverterades till multipler. Eftersom detta ledde till viss förvirring för våra kunder införde vi en ändring för att tilldela användning per minut till dedikerade A0-, A2-, A3- och A4-mätare.

Den nya mätningen började gälla mellan 27 och 28 januari 2016. I den nedladdningsbara CSV-filen som visar användning under den här övergångsperioden visas båda:

- Användning som angavs på A1-mätaren
- Användning som angavs på den nya dedikerade mätaren som motsvarar storleken på din distribution.

| **Distributionsstorlek** | **Användning anges som en multipel av A1 till och med 26 januari 2016** | **Användning anges på dedikerad mätare från och med 27 januari 2016** |
| --- | --- | --- |
| A0 | 0,25 av A1-timme | 1 av A0-timme |
| A2 | 2 av A1-timme | 1 av A2-timme |
| A3 | 4 av A1-timme | 1 av A3-timme |
| A4 | 8 av A1-timme | 1 av A4-timme |

### <a name="regions"></a>Regioner

För de tjänster där zon och region påverkar prissättningen visar följande tabell en mappning för geografiska områden och regioner:

| Geografi | Dataöverföringsregioner | CDN-regioner |
| --- | --- | --- |
| Zon 1 | Nordeuropa <br> Västeuropa <br> USA, Västra <br> USA, Östra <br> USA, norra centrala <br> USA, södra centrala <br> USA, östra 2 <br> USA, centrala | Nordamerika <br> Europa |
| Zon 2 | Östra stillahavsområdet i Asien <br> Sydöstra stillahavsområdet i Asien <br> Japan, östra <br> Japan, västra <br> Australien, östra <br> Australien, sydöstra | Asien och stillahavsområdet <br> Japan <br> Latinamerika <br> Mellanöstern/Afrika <br> Australien, östra <br> Australien, sydöstra |
| Zon 3 | Brasilien, södra |   |

Det finns inga kostnader för utgående data mellan tjänster i samma datacenter. Till exempel Microsoft 365 och Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Azure-förskottsbetalningar och ej fakturerad användning

Azure-förskottsbetalningar är ett belopp du betalar i förväg för Azure-tjänster. Azure-förskottsbetalningen förbrukas när tjänster används. Första parts Azure-tjänster debiteras mot Azure-förskottsbetalningar. Men vissa avgifter debiteras separat och Azure Marketplace-tjänster förbrukar inte Azure-förskottsbetalningar.

### <a name="charges-billed-separately"></a>Avgifter som debiteras separat

Vissa produkter och tjänster från tredjepartskällor använder inte Azure-förskottsbetalningar. Dessa artiklar faktureras i stället separat som en del av fakturan för överförbrukning i standardfaktureringsperioden.

Vi har slagit samman alla avgifter för Azure och Azure Marketplace till en enda faktura som sammanfaller med registreringens faktureringsperiod. Den kombinerade fakturan gäller inte för kunder i Australien, Japan eller Singapore.

På den kombinerade fakturan visas först Azure-användning, följt av eventuella avgifter för Azure Marketplace. För kunder i Australien, Japan eller Singapore visas Marketplace-avgifterna på en separat faktura.

Om det inte finns någon överförbrukning i slutet av standardfaktureringsperioden görs en separat fakturering av de avgifter som debiteras separat. Detta gäller kunder i Australien, Japan och Singapore.

Följande tjänster faktureras separat:

- Canonical
- Citrix XenApp Essentials
- Registrerad användare för Citrix XenDesktop
- Openlogic
- Registrerad användare för Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (månatligen)
- Visual Studio Enterprise (årsvis)
- Visual Studio Professional (månatligen)
- Visual Studio Professional (årsvis)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Det här kan du förvänta dig när kanalpartnern har ändrats

Om ändringen av kanal partner sker i mitten av månaden får kunden en faktura för användning under den föregående associerade partnern, och en annan faktura för användningen under den nya partnern.

Fakturorna släpps efter den månad då faktureringsperioden slutar. Om faktureringstakten är månatlig kommer septemberfakturan att släppas i oktober för båda partnerna. Om faktureringsperioden är kvartals- eller årsvis kan kunden förvänta sig en faktura för användningen under perioden för den tidigare associerade partnern, medan resten hamnar på den nya partnern baserat på faktureringstakten.

## <a name="next-steps"></a>Nästa steg

- I Excel-filerna nedan finns information om Azure-tjänster. Informationen uppdateras den 6:e och 20:e i varje månad.

   | Titel | Beskrivning | Filnamn |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Visar aktiva tjänster och innefattar: <br>  <ul><li>tjänstekategori</li>   <li>beskrivande namn på tjänst</li>   <li>Förskottsbetalningar – namn och artikelnummer</li> <li>förbrukningsnamn och artikelnummer</li>   <li>måttenhet</li>   <li>konverteringsfaktorer mellan den rapporterade användningen och användningen som visas i Enterprise-portalen</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Det här kalkylbladet innehåller en lista över alla möjliga kombinationer av tjänsterelaterade fält i den nedladdningsbara användningsrapporten. | Service\_Download\_Fields.xlsx |

- Information om hur du tolkar din faktura och dina kostnader finns i [Förstå fakturan för ditt Azure Enterprise-avtal](../understand/review-enterprise-agreement-bill.md).
- Om du vill börja använda Azure Enterprise-portalen kan du läsa [Komma igång med Azure EA-portalen](ea-portal-get-started.md).
