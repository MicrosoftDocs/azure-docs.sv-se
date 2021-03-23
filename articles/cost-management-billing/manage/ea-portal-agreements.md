---
title: Azure EA-avtal och ändringar
description: I den här artikeln förklaras hur Azure EA-avtal och ändringar påverkar din användning av Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: d03ae5062cc81b3e042ed3b714838e14929dbba2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778227"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure EA-avtal och ändringar

I den här artikeln beskrivs hur Azure EA-avtal och ändringar kan påverka åtkomst, användning och betalningar för Azure-tjänster.

## <a name="enrollment-provisioning-status"></a>Status för registreringens etablering

Startdatumet för en ny Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) definieras av det datum då det regionala driftcentret bearbetade den. Eftersom Azure-förskottsbetalningsorder via Azure EA-portalen bearbetas i UTC-tidszonen, kan det uppstå en fördröjning om din Azure-förskottsbetalningsorder bearbetades i en annan region. Startdatumet för täckningen på inköpsordern är startdatumet för Azure-förskottsbetalningen. Startdatumet för täckningen är det datum då Azure-förskottsbetalningen visas på Azure EA-portalen.

## <a name="support-for-enterprise-customers"></a>Support för Enterprise-kunder

 Azures [EA-supportavtalserbjudande](https://azure.microsoft.com/offers/enterprise-agreement-support/) är tillgängligt för vissa kunder.

## <a name="enrollment-status"></a>Registreringsstatus

Registreringar har något av följande statusvärden. Varje värde avgör hur du kan använda registreringen. Registreringsstatusen avgör i vilket stadie registreringen är. Statusen anger om du måste aktivera registreringen innan du kan använda den. Du kan också se om inledande perioden har upphört så att du debiteras för överförbrukning.

**Väntar** – registreringsadministratören måste logga in i Azure EA-portalen. Därefter får registreringen statusen **Aktiv**.

**Aktiv** – registreringen är tillgänglig och kan användas. Du kan skapa konton och prenumerationer i Azure EA-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.

**Obegränsad utökad giltighet** – statusen Obegränsad utökad giltighet tilldelas efter Enterprise-avtalets slutdatum. Innan EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören välja att:

- Förnya registreringen genom att utöka Azure-förskottsbetalningen
- Överföra den befintliga registreringen till en ny registrering
- Migrera till MOSP-programmet (Microsoft Online Subscription program)
- Bekräfta inaktiveringen av alla tjänster som är kopplade till registreringen

**Upphört** – EA-registreringen upphör när den når Enterprise-avtalets slutdatum. EA-kunden avregistreras från den utökade giltigheten och alla tjänster inaktiveras.

Från och med den 1 augusti 2019 godkänns inte nya avanmälningsformulär för Azures kommersiella kunder. I stället försätts alla registreringar i statusen Obegränsad utökad giltighet. Om du vill sluta använda Azure-tjänsterna stänger du din prenumeration i [Azure-portalen](https://portal.azure.com). Eller så kan din partner skicka en begäran om uppsägning. Kunder med avtal för myndigheter påverkas inte.

**Överförd** – statusen Överförd används för registreringar vars konton och tjänster har överförts till en ny registrering. Registreringar överförs inte automatiskt om ett nytt registreringsnummer genereras vid förnyandet. Det tidigare registreringsnumret måste tas med i kundens förnyelsebegäran om överföringen ska ske automatiskt.

## <a name="partner-markup"></a>Pålägg för partner

Påläggen för partner förbättrar kundernas kostnadsrapportering i Azure EA-portalen. I Azure EA-portalen visas den användning och de priser som partnern har konfigurerat för kunden.

Påläggen gör att partneradministratörer kan lägga till ett procentuellt prispålägg på de indirekta Enterprise-avtalen. Det procentuella pålägget gäller för alla Microsofts förstapartstjänster på Azure EA-portalen, till exempel mätarpriser, Azure-förskottsbetalning och beställningar. När partnern har publicerat pålägget ser kunderna kostnaderna för Azure i Azure EA-portalen. Några exempel är användningssammanfattningar, prislistor och nedladdade användningsrapporter.

Från och med september 2019 kan en partner lägga till påslag när som helst under en tidsperiod. De behöver inte vänta till nästa tidsperiod för att tillämpa pålägget.

Microsoft använder inte det angivna pålägget och tillhörande priser för något annat ändamål, om inte kanalpartnern uttryckligen godkänner det.

### <a name="how-the-calculation-works"></a>Så här fungerar beräkningen

Licenstjänsteleverantören tillhandahåller ett enda procentvärde i EA-portalen.    All kommersiell information på portalen lyfts upp av procenttalet som anges av licenstjänsteleverantören. Exempel:

- Kunden signerar ett EA med en Azure-förskottsbetalning på 100 000 USD.
- Mätarpriset för tjänst A är 10 USD/timme.
- Licenstjänsteleverantören anger en påläggsprocent på 10 % på EA-portalen.
- Exemplet nedan är hur kunden ser den kommersiella informationen:
    - Saldo: 110 000 USD.
    - Mätarpris för tjänst A: 11 USD/timme.
    - Användnings-/värdinformation för tjänst A för användning i 100 timmar: 1 100 USD.
    - Tillgängligt saldo för kunden efter avdrag för förbrukning av tjänst A: 108 900 USD.

### <a name="when-to-use-a-markup"></a>När du ska använda ett pålägg

Använd funktionen om du anger samma påläggsprocent på ALLA kommersiella transaktioner inom Enterprise-avtalet. Det vill säga – om du gör ett pålägg på Azure-förskottsbetalningsinformationen, mätarpriserna, beställningsinformationen osv.

Använd inte påläggsfunktionen om:
- Du använder olika priser mellan priser för Azure-förskottsbetalning och mätare.
- Du använder olika priser för olika mätare.

Om du använder olika priser för olika mätare rekommenderar vi att utveckla en anpassad lösning utifrån API-nyckeln, som kan tillhandahållas av kunden, för att hämta förbrukningsdata och tillhandahålla rapporter.

### <a name="other-important-information"></a>Annan viktig information

Den här funktionen är avsedd att ge en uppskattning av Azure-kostnaden till slutkunden. Licenstjänsteleverantören ansvarar för alla ekonomiska transaktioner med kunden under EA-avtalet.

Se till att granska den kommersiella informationen – saldo, prislista osv innan du publicerar de pålagda priserna till slutkund.

### <a name="how-to-add-a-price-markup"></a>Så här lägger du till ett prispålägg

**Steg ett: Lägga till prispålägg**

1. I EA-portalen väljer du **Reports** (Rapporter) i det vänstra navigeringsfältet.
1. Under _Usage Summary_ (Användningssammanfattning) klickar du på den blå frasen **Markup** (Pålägg).
1. Ange påläggsprocenten (mellan -100 till 100) och klicka på **Preview** (Förhandsversion).


**Steg två: Granska och verifiera**

Granska påläggspriset i _Sammanfattning av användning_ för förskottsbetalningsperioden i kundvyn. Microsoft-priset är fortfarande tillgängligt i partnervyn. Vyerna kan växlas med hjälp av partnerpåläggets växlingsknapp för personer högst upp till höger.

1. Granska priserna i prisdokumentet.
1. Du kan göra ändringar innan du publicerar genom att välja **Edit** (Redigera) på fliken _View Usage Summary > Customer View_ (Visa användningssammanfattning > Kundvy). 
   
Pålägg görs för både tjänsterna och förskottsbetalningssaldon med samma procentsatser. Om du har olika procentsatser för saldo och mätarpriser, eller olika procentsatser för olika tjänster, ska du inte använda den här funktionen.

**Steg tre: Publicera**

När priserna har granskats och verifierats klickar du på **Publish** (Publicera).
  
Priser med pålägg blir tillgängliga för företagsadministratörer direkt efter att du har valt publicera. Redigeringar kan inte göras av pålägg. Du måste inaktivera pålägg och börja om med steg 1.

### <a name="which-enrollments-have-a-markup-enabled"></a>Vilka registreringar har pålägg aktiverat?

Om du vill kontrollera om en registrering har en pålägg publicerad klickar du på **Manage** (Hantera) i det vänstra navigeringsfältet och klickar på fliken **Enrollment** (Registrering). Välj den registreringsrutan du vill markera och ser påläggsstatusen under _Enrollment Detail_ (Registreringsinformation). Här visas den aktuella statusen för påläggsfunktionen för det Enterprise-avtalet som Disabled (Inaktiverad), Preview (Förhandsversion) eller Published (Publicerad).

### <a name="how-can-the-customer-download-usage-estimates"></a>Hur kan kunden ladda ned användningsberäkningar?

När partnerpålägg har publicerats får den indirekta kunden åtkomst till månatliga CSV-filer för saldo och debiteringar samt månatliga CSV-filer för användning. Användningsinformationsfilerna innehåller resurskostnader och utökade kostnader.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Hur kan jag som partner använda pålägg på befintliga EA-kunder som tidigare fanns hos en annan partner?
Partners kan använda påläggsfunktionen (på Azure EA) efter att ett byte av kanalpartner har bearbetats. Du behöver inte vänta på nästa årsperiod.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Resursförskottsbetalning och begära kvotökningar

**Systemet tillämpar följande standardkvoter per prenumeration:**

| **Resurs** | **Standardkvot** | **Kommentarer** |
| --- | --- | --- |
| Beräkningsinstanser i Microsoft Azure | 20 samtidiga små beräkningsinstanser eller deras motsvarighet i andra beräkningsinstansstorlekar. | I följande tabell ges information om hur du beräknar det motsvarande antalet små instanser:<ul><li> Extra liten – 1 motsvarande liten instans </li><li> Liten – 1 motsvarande liten instans </li><li> Medelstor – 2 motsvarande små instanser </li><li> Stor – 4 motsvarande små instanser </li><li> Extra stor – 8 motsvarande små instanser </li> </ul>|
| Virtuella datorer – beräkningsinstanser i Microsoft Azure v2 | EA: 350 kärnor | Virtuella datorer – GA IaaS v2:<ul><li> A0\_A7-familjen – 350 kärnor </li><li> B\_A0\_A4-familjen – 350 kärnor </li><li> A8\_A9-familjen – 350 kärnor </li><li> DF-familjen – 350 kärnor</li><li> GF – 350 kärnor </li></ul>|
| Microsoft Azure Hosted Services | 6 värdtjänster | Den här gränsen för värdtjänster kan inte höjas till högre än sex för en enskild prenumeration. Om du behöver fler värdtjänster lägger du till ytterligare prenumerationer. |
| Microsoft Azure Storage | 5 lagringskonton, med maxstorlek på 100 TB för varje konto. | Du kan öka antalet lagringskonton till upp till 20 per prenumeration. Om du behöver fler lagringskonton lägger du till ytterligare prenumerationer. |
| SQL Azure | 149 databaser av någon typ (t.ex. Web Edition eller Business Edition). |   |
| Åtkomstkontroll | 50 namnområden per konto. 100 miljoner Access Control-transaktioner per månad |   |
| Service Bus | 50 namnområden per konto. 40 Service Bus-anslutningar | Kunder som köper Service Bus-anslutningar via anslutningspaket får kvoter som motsvarar mittpunkten mellan anslutningspaketet de har köpt och det näst högsta anslutningspaketstorleken. Kunder som väljer ett 500-paket har en kvot på 750. |

## <a name="resource-prepayment"></a>Resursförskottsbetalning

Microsoft tillhandahåller tjänster till dig upp till minst den associerade användningsnivå som ingår i den månatliga förskottsbetalning du köpt (förskottsbetalningen för tjänster), men all annan ökning av användningsnivån för tjänstresurser (t.ex. om du behöver lägga till fler beräkningsinstanser eller mer lagringsutrymme) beror på tjänstresursernas tillgänglighet.

Eventuella kvoter som beskrivs ovan utgör inte en förskottsbetalning för tjänster. Antalet samtidiga mindre beräkningsinstanser (eller motsvarande) som Microsoft ska tillhandahålla under en förskottsbetalning för tjänster beräknas genom att antalet bekräftade timmar med mindre beräkningsinstanser som köps under en månad divideras med antalet timmar under årets kortaste månad (dvs. februari – 672 timmar).

## <a name="requesting-a-quota-increase"></a>Begära en kvotökning

Du kan begära en kvotökning när som helst genom att skicka en [onlinebegäran](https://ms.portal.azure.com/). Ange följande information för att bearbeta din begäran:

- Microsoft-kontot eller arbets- eller skolkontot som är associerat med kontoinnehavaren för din prenumeration. Det här är e-postadressen som används för att logga in på Microsoft Azure-portalen för att hantera dina prenumerationer. Identifiera även att det här är kontot som är associerat med en EA-registrering.
- Resurserna och beloppet som du vill ha en kvotökning för.
- Prenumerations-ID på Azure Developer-portalen associerat med din tjänst.
  - Om du vill ha information om hur du får ditt prenumerations-ID kan du [kontakta supporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="plan-skus"></a>Plan-SKU:er

Plan-SKU:er ger möjlighet att köpa en svit med integrerade tjänster tillsammans till ett rabatterat pris. Plan-SKU:erna är utformade att komplettera varandra genom ytterligare integrerade erbjudanden och programsvit för större kostnadsbesparingar.

Ett exempel skulle vara prenumerationen på Operations Management Suite (OMS). OMS erbjuder ett enkelt sätt att få åtkomst till en fullständig uppsättning molnbaserade hanteringsfunktioner, inklusive analys, konfiguration, automatisering, säkerhet, säkerhetskopiering och haveriberedskap. OMS-prenumerationer omfattar rättigheter till System Center-komponenter för att tillhandahålla en komplett lösning för hybridmolnmiljöer.

Företagsadministratörer kan tilldela kontoinnehavare för att etablera tidigare köpta plan-SKU:er i EA-portalen genom att följa dessa steg:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Visa prisdokumentet för att kontrollera inkluderad mängd

1. Logga in som företagsadministratör.
1. Klicka på **Reports** (Rapporter) i det vänstra navigeringsfältet.
1. Klicka på fliken **Price Sheet** (Prisdokument).
1. Klicka på nedladdningsikonen i det övre högra hörnet.
1. Hitta motsvarande plan-SKU-artikelnummer med filter på kolumnen ”Included Quantity” (Inkluderad mängd) och välj värden större än ”0”.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Befintliga/nya kontoinnehavare för att skapa nya prenumerationer

**Steg ett: Logga in på kontot**
1. I Azure EA-portalen väljer du fliken **Manage** (Hantera) och går till **Subscription** (Prenumeration) på den översta menyn.
1. Kontrollera att du är inloggad som kontoinnehavare för kontot.
1. Klicka på **+Add Subscription** (Lägg till prenumeration).
1. Klicka på **Köp**.

Första gången du lägger till en prenumeration på ett konto måste du ange dina kontaktuppgifter. När du lägger till fler prenumerationer fylls dina kontaktuppgifter i åt dig.

Första gången du lägger till en prenumeration på ditt konto uppmanas du att godkänna MOSA-avtalet och en prisplan. Dessa avsnitt gäller INTE för Enterprise-avtalskunder men är nödvändiga just nu för att etablera din prenumeration. Tillägget till din Microsoft Azure Enterprise-avtalsregistrering ersätter ovanstående, och din kontraktsmässiga relation ändras inte. Markera rutan som anger att du godkänner villkoren.

**Steg två: Uppdatera prenumerationens namn**

Alla nya prenumerationer läggs till med det standardmässiga prenumerationsnamnet ”Microsoft Azure Enterprise”. Det är viktigt att uppdatera prenumerationsnamnet för att skilja det från andra prenumerationer i företagsregistreringen och se till att det går att känna igen på rapporter för företagsnivå.

Klicka på **Subscriptions** (Prenumerationer), klicka på den prenumeration som du har skapat och klicka sedan på **Edit Subscription Details** (Redigera prenumerationsdetaljer).

Uppdatera prenumerationsnamnet och tjänstadministratören och klicka på bockmarkeringen. Prenumerationsnamnet visas på rapporter och är även namnet på det projekt som är associerat med prenumerationen i utvecklingsportalen.
Det kan ta upp till 24 timmar innan nya prenumerationer sprids i prenumerationslistan.

Bara kontoinnehavare kan visa och hantera prenumerationer.

### <a name="troubleshooting"></a>Felsökning

**Kontoinnehavare visas med väntande status**

När nya kontoinnehavare läggs till i registreringen första gången visas alltid statusen ”väntande”. När kontoinnehavaren får välkomstmeddelandet kan han/hon logga in och aktivera sitt konto. Den här aktiveringen uppdaterar kontostatusen från ”väntande” till ”aktiv”.

**Användningar som debiteras efter plan-SKU:er har köpts**

Det här scenariot inträffar när kunden har distribuerat tjänster under fel registreringsnummer eller valt fel tjänster.

För att verifiera om du distribuerar under rätt registrering kan du kontrollera informationen om dina inkluderade enheter via prisdokumentet. Logga in som företagsadministratör och klicka på **Reports** (Rapporter) i det vänstra navigeringsfältet och välj fliken **Price Sheet** (Prisdokument). Klicka på nedladdningsikonen i det övre högra hörnet och hitta motsvarande plan-SKU-artikelnummer med filter på kolumnen ”Included Quantity” (Inkluderad mängd) och välj värden större än ”0”.

Se till att din OMS-plan visas i prisdokumentet under inkluderade enheter. Om det inte finns några inkluderade enheter för OMS-plan för registreringen kan din OMS-plan finnas under en annan registrering. Kontakta supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Om de inkluderade enheterna för tjänsterna i prisdokumentet inte stämmer överens med det du har distribuerat, t.ex. Operational Insights Premium – analyserade data kontra Operational Insights Standard – analyserade data, betyder det att du kan ha distribuerat tjänster som inte omfattas av planen. Kontakta supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) så att vi kan hjälpa dig.

**Etablerade plan-SKU-tjänster på fel registrering**

Om du har flera registreringar och har distribuerat tjänster under fel registreringsnummer, som inte har en OMS-plan, kontaktar du supporten för Azure EA-portalen på [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Nästa steg

- Om du vill börja använda Azures EA-portalen kan du läsa [Kom igång med Azure EA-portalen](ea-portal-get-started.md).
- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
