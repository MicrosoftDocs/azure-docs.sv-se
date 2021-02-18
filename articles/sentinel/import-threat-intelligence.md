---
title: Importera Hot information till Azure Sentinel | Microsoft Docs
description: Använd Hot information-flöden i Azure Sentinel för att analysera data, identifiera hot och generera aviseringar och incidenter. Visualisera hot informations information med arbets böcker.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 0b2a6088effc735076d56ba83dd85135392147b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574988"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importera hotinformation till Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Introduktion till hot information

Cyberhot Threat intelligence (CTI) är information som beskriver kända befintliga eller potentiella hot mot system och användare. Den här typen av information tar många formulär, från skrivna rapporter som innehåller information om en viss hot aktörs motivation, infrastruktur och teknik, till specifika observationer av IP-adresser, domäner och filhashar som är associerade med cyberhot-hot. CTI används av organisationer för att tillhandahålla grundläggande sammanhang till ovanlig aktivitet, så att säkerhets personal snabbt kan vidta åtgärder för att skydda sina personer och till gångar. CTI kan hämtas från många platser, till exempel datafeeds med öppen källkod, Hot informations delnings grupper, kommersiella informations flöden och lokal information som samlas in i samband med säkerhets undersökningar i en organisation.

I en SIEM-lösning (Security information and Event Management) som Azure Sentinel, är den mest använda formen av CTI hot indikatorer, vilket ofta kallas indikatorer för komprometterande eller IoCs. Hot indikatorer är data som kopplar observationer, till exempel URL: er, filhasher eller IP-adresser med känd hot aktivitet, till exempel nätfiske, botnät eller skadlig kod. Den här typen av hot information kallas ofta taktiska Threat Intelligence eftersom det kan användas för säkerhets produkter och automatisering i stor skala för att skydda och identifiera potentiella hot mot en organisation. I Azure Sentinel kan du använda hot indikatorer för att identifiera skadlig aktivitet som observerats i din miljö och som ger en kontext till säkerhetsövervakare för att under lätta svars beslut.

Du kan integrera Hot information (TI) i Azure Sentinel genom följande aktiviteter:

- Använd **data anslutningar** till olika ti-plattformar för att [Importera Hot information](./connect-threat-intelligence.md) till Azure Sentinel.
- Visa och hantera den importerade hot informationen i **loggar** och i det nya **Threat Intelligence** -avsnittet i Azure Sentinel.
- Använd de inbyggda **analys** regel mallarna för att generera säkerhets aviseringar och incidenter med hjälp av din importerade Hot information.
- Visualisera nyckelinformation om din Hot information i Azure Sentinel med **arbets boken Hot information**.

Hot information ger också användbar kontext inom andra Azure Sentinel-upplevelser, till exempel **jakt** och **bärbara datorer**, och även om de inte beskrivs i den här artikeln, åtgärdas dessa erfarenheter i [det här fantastiska blogg inlägget av Ian Hellen på Jupyter Notebooks i Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), som täcker användningen av CTI i antecknings böcker.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Azure Sentinel data Connectors för hot information

Precis som alla andra händelse data i Azure Sentinel importeras hot indikatorer med hjälp av data kopplingar. Det finns två data anslutningar i Azure Sentinel som tillhandahålls specifikt för hot indikatorer, **hot intelligens-taxii** och **Threat Intelligence-plattformar**. Du kan antingen använda antingen data koppling eller båda kopplingarna, beroende på var din organisation ser hot indikatorerna. Låt oss prata om var och en av data anslutningarna.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Lägga till hot indikatorer i Azure Sentinel med hot Intelligence Platforms data koppling

Många organisationer använder TIP-lösningar (Threat Intelligence Platform) för att samla in hot indikator flöden från olika källor, för att granska data i plattformen och sedan välja vilka hot indikatorer som ska gälla för olika säkerhetslösningar som nätverks enheter, avancerade hot skydds lösningar eller Siem, till exempel Azure Sentinel. Om din organisation använder en integrerad tips lösning, till exempel MISP, Avvikelsei ThreatStream, ThreatConnect, EclecticIQ-plattform, ThreatQ Threat intelligence-plattform eller Palo RJ-nätverk "MineMeld, **kan du** använda ditt tips för att importera hot indikatorer till Azure Sentinel. Eftersom anslutningen fungerar [Microsoft Graph med tiIndicators-API: et](/graph/api/resources/tiindicator) för att åstadkomma detta kan anslutningen också användas av en anpassad Threat intelligence-plattform för att dra nytta av tiIndicators-API: et för att skicka indikatorer till Azure Sentinel (och till andra Microsoft-säkerhetslösningar som Defender ATP).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Import Sök väg för hot information":::

Följ dessa steg om du vill importera hot indikatorer till Azure Sentinel från din integrerade tips-eller anpassade Threat Intelligence-lösning:

1. Hämta ett program-ID och klient hemlighet från din Azure Active Directory

1. Mata in den här informationen i din tips lösning eller ett anpassat program

1. Aktivera data koppling för Threat Intelligence-plattformar i Azure Sentinel

Här är en detaljerad titt på var och en av de här stegen.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Hämta ett program-ID och klient hemlighet från din Azure Active Directory

Oavsett om du arbetar med ett tips eller med en anpassad lösning, kräver tiIndicators-API lite grundläggande information för att ansluta och skicka hot indikatorer. De tre uppgifterna du behöver hämta är:
- Program-ID (klient)
- Katalog-ID (klient)
- Klienthemlighet

Den här informationen kommer alltid från din Azure Active Directory via en process som kallas **app-registrering** , som innehåller följande tre steg:
- Registrera en app på Azure Active Directory
- Ange de behörigheter som krävs av appen för att ansluta till Microsoft Graph tiIndicators API och skicka hot indikatorer
- Få medgivande från din organisation för att ge dessa behörigheter till det här programmet.  

**Registrera ett program med Azure Active Directory**

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till tjänsten **Azure Active Directory** .

1. Välj **app-registreringar** på menyn och välj **ny registrering**.

1. Välj ett namn för program registreringen, Välj alternativ knappen **enskild klient** och välj **Registrera**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registrera ett program":::

1. Kopiera ID-värdena för **program (klient** ) och **katalog (klient)** på skärmen som visas. Detta är de två första uppgifterna du behöver senare för att konfigurera ditt tips eller en anpassad lösning för att skicka hot indikatorer till Azure Sentinel.

**Ange de behörigheter som programmet kräver**

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till tjänsten **Azure Active Directory** .

1. Välj **app-registreringar** på menyn och välj den nyligen registrerade appen.

1. Välj **API-behörigheter** på menyn och klicka på knappen **Lägg till en behörighet** .

1. På sidan **Välj ett API** väljer du **Microsoft Graph** för att välja från en lista över Microsoft Graph behörigheter.

1. När du tillfrågas **vilka typer av behörigheter kräver programmet?** Välj **program behörigheter**. Detta är den typ av behörighet som används av program som autentiseras med app-ID och program hemligheter (API-nycklar).

1. Välj **ThreatIndicators. readwrite. OwnedBy** och välj **Lägg till behörigheter** för att lägga till behörigheten till appens lista över behörigheter.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Ange behörigheter":::

**Få medgivande från din organisation för att bevilja dessa behörigheter**

1. För att bevilja medgivande behöver du en Azure Active Directory global administratör för att välja alternativet **bevilja administratörs medgivande för din klient** organisation på APPENs API-behörigheter. Om du inte har rollen global administratör på ditt konto är den här knappen inte tillgänglig och du måste be en global administratör från din organisation att utföra det här steget.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Bevilja medgivande":::

1. När medgivande har beviljats för din app, bör du se en grön bock markering under **status**.
 
Nu när din app har registrerats och behörigheter har beviljats kan du få den sista åtgärden i listan – en klient hemlighet för din app.

1. Öppna [Azure Portal](https://portal.azure.com/) och navigera till tjänsten **Azure Active Directory** .

1. Välj **app-registreringar** på menyn och välj den nyligen registrerade appen.

1. Välj **certifikat & hemligheter** på menyn och klicka på knappen **ny klient hemlighet** för att hämta en hemlighet (API-nyckel) för din app.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Hämta klient hemlighet":::

1. Klicka på knappen **Lägg till** och **Se till att kopiera klient hemligheten**, eftersom du inte kan hämta den här hemligheten igen om du lämnar den här sidan. Du behöver det här värdet när du konfigurerar ditt tips eller en anpassad lösning.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Mata in den här informationen i din tips lösning eller ett anpassat program

Nu har du alla tre uppgifter som du behöver för att konfigurera ditt tips eller en anpassad lösning för att skicka hot indikatorer till Azure Sentinel. 
- Program-ID (klient)
- Katalog-ID (klient)
- Klienthemlighet

Ange dessa värden i konfigurationen av ditt integrerade tips eller anpassade lösning där det behövs, och hot indikatorer skickas via den Microsoft Graph tiIndicators-API som är riktad mot Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Aktivera data koppling för Threat Intelligence-plattformar i Azure Sentinel

Det sista steget i integrerings processen är att aktivera data kopplingen för **Threat Intelligence-plattformarna** i Azure Sentinel. Detta är det steg som importerar hot indikatorerna som skickas från ditt tips eller en anpassad lösning via Microsoft Graph tiIndicators API till Azure Sentinel. Dessa indikatorer är tillgängliga för alla Azure Sentinel-arbetsytor för din organisation. Följ dessa steg om du vill aktivera data koppling för Threat Intelligence-plattformar för varje arbets yta:

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** dit du vill importera hot indikatorerna som skickas från ditt tips eller en anpassad lösning.

1. Välj **data kopplingar** på menyn, Välj **Threat Intelligence-plattformar** från anslutnings galleriet och klicka på knappen **Öppna kopplings sidan** .

1. Eftersom du redan har slutfört appens registrering och konfigurerat tips eller anpassad lösning för att skicka hot indikatorer, är det enda steget kvar att klicka på knappen **Anslut** .

På några minuter ska hot indikatorer börja flöda till den här Azure Sentinel-arbetsytan.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Lägga till hot indikatorer i Azure Sentinel med hot information – TAXI data Connector

Den mest tillämpade bransch standarden för överföring av hot information är en [kombination av Stix-dataformat och taxii-protokollet](https://oasis-open.github.io/cti-documentation/). Om din organisation erhåller hot indikatorer från lösningar som har stöd för den aktuella STIX/TAXIi-versionen (2,0 eller 2,1) kan du använda **Threat Intelligence-** data kopplingen för att ta dina hot indikatorer till Azure Sentinel. Threat Intelligence – TAXI data Connector möjliggör en inbyggd TAXIi-klient i Azure Sentinel som används för att importera Hot information från TAXIi 2. x-servrar.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXIi import väg":::
 
Följ dessa steg om du vill importera STIX-formaterade hot indikatorer till Azure Sentinel från en TAXI-Server:

1. Hämta TAXIi Server API-roten och samlings-ID

1. Aktivera Threat Intelligence – TAXIi data Connector i Azure Sentinel

Nu ska vi ta en detaljerad titt på vart och ett av dessa steg.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Hämta TAXIi Server API-roten och samlings-ID

TAXIi 2. x-servrar annonserar API-rötter, som är webb adresser som är värdar för samlingar med hot information. Oftast kan API-roten hämtas via dokumentations sidan för den hot Intelligence-provider som är värd för TAXI-servern. Men ibland är den enda information som annonseras en URL som kallas för en identifierings slut punkt. I så fall är det enkelt att hitta API-roten med hjälp av slut punkt för identifiering. Om du redan känner till de TAXIbaserade Server-API-rot-och samlings-ID: n som du vill arbeta med kan du gå vidare till nästa avsnitt, **Aktivera Hot information-taxii data Connector i Azure Sentinel**.

Nu ska vi titta på ett faktiskt exempel på hur du använder ett enkelt kommando rads verktyg som kallas [sväng](https://en.wikipedia.org/wiki/CURL), som finns i Windows och de flesta Linux-distributioner, för att identifiera API-roten och söka i samlingarna för en taxi-Server endast från identifierings slut punkten. I det här exemplet använder vi identifierings slut punkten för [Limo](https://www.anomali.com/community/limo) ThreatStream taxii 2,0-servern.

1.  Från en webbläsare navigerar du till [THREATSTREAM taxi 2,0 Server Discovery-slutpunkten](https://limo.anomali.com/taxii) för att hämta API-roten. Autentisera med användaren och lösen ordet `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Använd verktyget för vändning och API-roten ( https://limo.anomali.com/api/v1/taxii2/feeds/) från föregående steg för att bläddra i listan över samlings-ID: n som finns i API-roten

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Nu har du all information du behöver för att ansluta Azure Sentinel till en eller flera TAXI Server samlingar från Limo.

| **API-rot** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Samlings-ID |
| ------------------------------------------------------------ | ------------: |
| **Phish-tank**                                               | 107           |
| **IP-adresser för Abuse.ch-utpressning**                                  | 135           |
| **Abuse.ch-utpressnings domäner**                              | 136           |
| **DShield Genomsök IP-adresser**                                     | 150           |
| **Lista över skadlig kod-Hotlist**                            | 200           |
| **Blutmagie TOR-noder**                                      | 209           |
| **Nya hot C&C-Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **Cyberbrott**                                               |  41           |
| **Nya hot – komprometterade**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Aktivera Threat Intelligence – TAXIi data Connector i Azure Sentinel

Följ dessa steg om du vill importera hot indikatorer till Azure Sentinel från en TAXI-Server:

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** som du vill importera hot indikatorer från taxii-servern till.

1. Välj **data kopplingar** på menyn, Välj **Hot information-taxii** i anslutnings galleriet och klicka på knappen **Öppna kopplings sidan** .

1. Skriv ett **namn** på den här taxii Server-samlingen, **API-rot-URL**, **samlings-ID**, **användar namn** (om det behövs) och **lösen ord** (om det behövs) och klicka på knappen **Lägg till** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Konfigurera TAXIi-servrar":::
 
Du bör få en bekräftelse på att en anslutning till TAXI-servern har upprättats och du kan upprepa steg (4) över så många gånger som du vill ansluta till flera samlingar från samma eller olika TAXI-servrar.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Visa dina hot indikatorer i Azure Sentinel

Nu när du har importerat hot indikatorer till Azure Sentinel med hjälp av **Threat Intelligence-plattformarna** och/eller **hot Intelligence-taxii** data Connector kan du visa dem i **ThreatIntelligenceIndicator** -tabellen i **loggar** där alla dina Azure Sentinel Event-data lagras. Den här tabellen utgör grunden för frågor som utförs av andra funktioner i Azure Sentinel, till exempel analyser och arbets böcker. Så här hittar du och visar dina hot indikatorer i **ThreatIntelligenceIndicator** -tabellen.

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** som du har importerat hot indikatorer till med hjälp av någon av Threat Intelligence data Connector.

1. Välj **loggar** i avsnittet **Allmänt** på menyn för Azure-kontroll.

1. Tabellen **ThreatIntelligenceIndicator** finns under **Azure Sentinel** -gruppen.

1. Välj ikonen för **förhands gransknings data** (ögat) bredvid tabell namnet och välj knappen **se i Frågeredigeraren** för att köra en fråga som visar poster från den här tabellen.

Resultatet bör se ut ungefär som den exempel hot indikator som visas nedan:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Exempel fråga efter data":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Hantera dina hot indikatorer i det nya Threat Intelligence-avsnittet i Azure Sentinel

Med det nya **Threat Intelligence** -avsnittet, som är tillgängligt från menyn för Azure Sentinel, kan du också visa, sortera, filtrera och söka efter dina importerade hot indikatorer utan att skriva en **loggad** fråga. Med det här nya området kan du också skapa hot indikatorer direkt i Azure Sentinel-gränssnittet, samt utföra vanliga administrativa uppgifter för hot information som indikator märkning och skapa nya indikatorer som rör säkerhets undersökningar.
Nu ska vi titta på två vanliga uppgifter och skapa nya hot indikatorer och tagga indikatorer för enkel gruppering och referens.

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** som du har importerat hot indikatorer till med hjälp av någon av Threat Intelligence data Connector.

1. Välj **Hot information** från avsnittet Threat Management på Azure Sentinel-menyn.

1. Välj knappen **Lägg till ny** på den översta menyn på sidan.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Lägg till en ny hot indikator" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Välj indikator typ och fyll sedan i de obligatoriska fälten som är markerade med en röd asterisk (*) på den **nya indikator** panelen.

1. Välj **Använd**. Indikatorn läggs till i rutnätet för indikatorer och skickas även till tabellen ThreatIntelligenceIndicator i **loggar**.

Att tagga hot indikatorer är ett enkelt sätt att gruppera dem så att de blir lättare att hitta. Normalt kan du tillämpa en tagg på indikatorer som rör en viss incident eller till indikatorer som visar hot från en viss känd aktör eller en välkänd angrepps kampanj. Du kan tagga hot indikatorer individuellt eller flera markerings indikatorer och tagga dem på samma gång. Nedan visas ett exempel på hur du kan tagga flera indikatorer med ett incident-ID. Eftersom taggning är kostnads fritt, är en rekommenderad metod att skapa standard namngivnings konventioner för hot indikator taggar. Du kan använda flera taggar för varje indikator.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Använd taggar för hot indikatorer" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analys placerar dina hot indikatorer för att identifiera potentiella hot

Du har fått dina hot indikatorer inmatade i Azure Sentinel; du har sett hur du kan visa och hantera dem. nu kan du se vad de kan göra åt dig. Det viktigaste användnings fallet för hot indikatorer i SIEM-lösningar som Azure Sentinel är till Power Analytics-regler.  Dessa indikatorbaserade regler jämför rå händelser från dina data källor mot dina hot indikatorer för att identifiera säkerhetshot i din organisation. I Azure Sentinel **Analytics** skapar du analys regler som körs regelbundet och genererar säkerhets aviseringar. Reglerna styrs av frågor, tillsammans med konfigurationer som bestämmer hur ofta regeln ska köras, vilken typ av frågeresultat som ska generera säkerhets aviseringar och eventuella automatiserade svar som ska utlösas när aviseringar genereras.

Även om du alltid kan skapa nya analys regler från grunden tillhandahåller Azure Sentinel en uppsättning inbyggda regelmallar, som skapats av Microsoft-säkerhetstekniker, som du kan använda som-är eller ändra för att uppfylla dina behov. Du kan enkelt identifiera de regelmallar som använder hot indikatorer, eftersom de är titlarna från "**ti Map**...". Alla dessa regelmallar fungerar på samma sätt, med den enda skillnaden att typ av hot indikatorer används (domän, e-post, filhash, IP-adress eller URL) och vilken händelse typ som ska matchas mot. Varje mall visar en lista över nödvändiga data källor som krävs för att regeln ska fungera, så att du snabbt kan se om du har de nödvändiga händelserna som redan har importer ATS i Azure Sentinel.

Låt oss ta en titt på någon av dessa regelmallar och gå igenom hur du aktiverar och konfigurerar regeln för att generera säkerhets aviseringar med hjälp av de hot indikatorer som du har importerat till Azure Sentinel. I det här exemplet ska vi använda regel mal len för **att mappa IP-entiteten ti-mapp till AzureActivity**. Den här regeln matchar eventuella hot indikator för IP-typ med alla dina Azure Activity events. När en matchning hittas genereras en **avisering** , samt en motsvarande **incident** för undersökning av ditt team för säkerhets åtgärder. Den här analys regeln fungerar bara om du har aktiverat en eller båda av **Threat Intelligence** -dataanslutningarna (för att importera hot indikatorer) och **Azure Activity** data Connector (för att importera Azures händelser på prenumerations nivå).

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** som du importerade hot indikatorer till med hjälp av data kopplingarna för **Threat Intelligence** och Azure-aktivitets data med hjälp av **Azure Activity** data Connector.

1. Välj **analys** från **konfigurations** avsnittet på menyn för Azure-kontroll.

1. Välj fliken **regelmallar** om du vill se en lista över tillgängliga mallar för analys regler.

1. Navigera till regeln med rubriken **ti Map-IP-enhet till AzureActivity** och se till att du har anslutit alla data källor som krävs enligt beskrivningen nedan.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Nödvändiga data källor":::

1. Välj den här regeln och klicka på knappen **Skapa regel** . Detta öppnar en guide för att konfigurera regeln. Slutför inställningarna här och välj knappen **Nästa: Ange regel logik >** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Skapa analys regel":::

1. Regel logik delen i guiden innehåller:
    - Frågan som ska användas i regeln.

    - Enhets mappningar, som talar om för Azure Sentinel hur du känner igen entiteter som konton, IP-adresser och URL: er, så att **incidenter** och **undersökningar** förstår hur du arbetar med data i säkerhets aviseringar som genereras av den här regeln.

    - Schemat för att köra den här regeln.

    - Det antal frågeresultat som krävs innan en säkerhets avisering genereras.

    Standardinställningarna i mallen är:
    - Kör en gång i timmen.

    - Matcha eventuella hot indikatorer för IP-adresser från tabellen **ThreatIntelligenceIndicator** med alla IP-adresser som hittades under den senaste timmen av händelser från tabellen **AzureActivity** .

    - Generera en säkerhets avisering om frågeresultaten är större än noll, vilket innebär att eventuella matchningar hittas.

Du kan lämna standardinställningarna eller ändra någon av dessa så att den uppfyller dina krav. När du är färdig väljer du knappen **Nästa: automatiserat svar >**

1. I det här steget i guiden kan du konfigurera en automatisering som du vill utlösa när en säkerhets avisering genereras från den här analys regeln. Automatisering i Azure Sentinel görs med **spel böcker**, som drivs av Azure Logic Apps. Mer information finns i den här [självstudien: Konfigurera automatiska hot svar i Azure Sentinel](./tutorial-respond-threats-playbook.md). I det här exemplet väljer vi bara **Nästa: granska >** om du vill fortsätta.

1. Det sista steget validerar inställningarna i regeln. När du är redo att aktivera regeln väljer du knappen **skapa** och du är klar.

Nu när du har aktiverat din analys regel kan du hitta din aktiverade regel på fliken **aktiva regler** i **analys** avsnittet i Azure Sentinel. Du kan redigera, aktivera, inaktivera, duplicera eller ta bort den aktiva regeln därifrån. Den nya regeln körs omedelbart vid aktivering och sedan körs den enligt det definierade schemat.

Enligt standardinställningarna, varje gång regeln körs enligt schemat, genererar alla resultat som påträffas en säkerhets avisering. Säkerhets aviseringar i Azure Sentinel kan visas i avsnittet **loggar** i Azure Sentinel i tabellen **SecurityAlert** under **Azure Sentinel** -gruppen.

I Azure Sentinel genererar aviseringarna som genereras från analys reglerna även säkerhets incidenter som finns i **incidenter** under **Threat Management** på Azure Sentinel-menyn. Incidenter är det som dina säkerhets drifts team kommer att prioritering och undersöka för att fastställa lämpliga svars åtgärder. Du hittar detaljerad information i den här [självstudien: Undersök incidenter med Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Arbets böcker ger insikter om din Hot information

Slutligen kan du använda en Azure Sentinel-arbetsbok för att visualisera nyckelinformation om din Hot information i Azure Sentinel, och du kan enkelt anpassa arbets böckerna efter dina affärs behov.
Nu ska vi gå igenom hur du hittar en hot informations arbets bok som finns i Azure Sentinel, och vi visar också hur du gör ändringar i arbets boken för att anpassa den.

1. Öppna [Azure Portal](https://portal.azure.com/) och gå till **Azure Sentinel** -tjänsten.

1. Välj den **arbets yta** som du har importerat hot indikatorer till med hjälp av någon av Threat Intelligence data Connector.

1. Välj **arbets böcker** i avsnittet **Threat Management** på menyn för Azure-kontroll.

1. Navigera till arbets boken med rubriken **Hot information** och kontrol lera att du har data i tabellen **ThreatIntelligenceIndicator** enligt nedan.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Verifiera data":::
 
1. Välj knappen **Spara** och välj en Azure-plats där du vill lagra arbets boken. Det här steget krävs om du ska ändra arbets boken på ett valfritt sätt och spara ändringarna.

1. Välj nu knappen **Visa Sparad arbets bok** för att öppna arbets boken för visning och redigering.

1. Nu bör du se de standard diagram som tillhandahålls av mallen. Nu ska vi göra vissa ändringar i ett av diagrammen. Välj knappen **Redigera** överst på sidan för att ange redigerings läge för arbets boken.

1. Vi lägger till ett nytt diagram med hot indikatorer efter hot typ. Rulla längst ned på sidan och välj Lägg till fråga.

1. Lägg till följande text i text rutan för **logg frågan Log Analytics arbets yta** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. I list rutan **visualisering** väljer du **stapeldiagram**.

1. Klicka på knappen **klar redigering** . Du har skapat ett nytt diagram för din arbets bok.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Stapeldiagram":::

Arbets böcker tillhandahåller kraftfulla interaktiva instrument paneler som ger dig insikter om alla aspekter av Azure Sentinel. Det finns ett helt parti som du kan göra med arbets böcker, medan de angivna mallarna är en bra start punkt vill du förmodligen gå in i och anpassa dessa mallar eller skapa nya instrument paneler som kombinerar många olika data källor så att du kan visualisera dina data på ett unikt sätt. Eftersom Azure Sentinel-arbetsböcker baseras på Azure Monitor arbets böcker finns det redan omfattande dokumentation tillgänglig och många fler mallar. En bra plats för att börja är den här artikeln om hur du [skapar interaktiva rapporter med Azure Monitor arbets böcker](../azure-monitor/visualize/workbooks-overview.md). 

Det finns också en omfattande community med [Azure Monitor arbets böcker på GitHub](https://github.com/microsoft/Application-Insights-Workbooks) där du kan hämta ytterligare mallar och bidra med dina egna mallar.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig om hot information-funktionerna i Azure Sentinel och det nya bladet för hot information. Praktisk vägledning om hur du använder funktionerna för hot information i Azure Sentinel finns i följande artiklar:

- [Anslut hot informations data](./connect-threat-intelligence.md) till Azure Sentinel.
- Skapa [inbyggda](./tutorial-detect-threats-built-in.md) eller [anpassade](./tutorial-detect-threats-custom.md) aviseringar och [Undersök](./tutorial-investigate-cases.md) incidenter i Azure Sentinel.