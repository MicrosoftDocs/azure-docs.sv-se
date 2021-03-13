---
title: För hands versions funktioner för Azure Firewall Premium
description: Azure Firewall Premium är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 22ee920c38a8cacaf37a1e3a6cfc5165dfa0bb44
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419957"
---
# <a name="azure-firewall-premium-preview-features"></a>För hands versions funktioner för Azure Firewall Premium

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA certifierings logo typ" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI-certifierings logo typ" border="false":::


> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview är en nästa generations brand vägg med funktioner som krävs för mycket känsliga och reglerade miljöer.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Översikts diagram över Azure Firewall Premium":::

I för hands versionen av Azure Firewall Premium används en brand Väggs princip, en global resurs som kan användas för att centralt hantera brand väggarna med Azure Firewall Manager. Från och med den här versionen kan alla nya funktioner konfigureras via brand Väggs principen. Brand Väggs regler (klassisk) fortsätter att stödjas och kan användas för att konfigurera befintliga standard brand Väggs funktioner.  Brand Väggs principer kan hanteras oberoende av varandra eller med Azure Firewall Manager. En brand Väggs princip som är associerad med en enda brand vägg har ingen avgift.

> [!IMPORTANT]
> För närvarande stöds inte SKU: n för brand Väggs Premium i Secure Hub-distributioner och Tvingad tunnel konfiguration. 

I för hands versionen av Azure Firewall Premium ingår följande funktioner:

- **TLS-inspektion** – dekrypterar utgående trafik, bearbetar data och krypterar sedan data och skickar dem till målet.
- **IDP: er** – ett system för identifiering och skydd av nätverks intrång (IDP: er) gör att du kan övervaka nätverks aktiviteter för skadlig aktivitet, Logga information om den här aktiviteten, rapportera den och eventuellt försöka blockera den.
- **URL-filtrering** – utökar Azure-brandväggens funktioner för FQDN-filtrering för att överväga en hel URL. Till exempel i `www.contoso.com/a/c` stället för `www.contoso.com` .
- **Webb kategorier** – administratörer kan tillåta eller neka användar åtkomst till webbplats kategorier som spel webbplatser, sociala media webbplatser och andra.


## <a name="tls-inspection"></a>TLS-kontroll

Azure Firewall Premium avslutar utgående och östra-väst TLS-anslutningar. Kontrollen av inkommande TLS stöds med [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) som tillåter kryptering från slut punkt till slut punkt. Azure-brandväggen gör de nödvändiga säkerhetsfunktionerna för mervärde och krypterar om trafiken som skickas till det ursprungliga målet.

> [!TIP]
> TLS 1,0 och 1,1 är inaktuella och stöds inte. TLS-1,0 och 1,1-versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de inte. Migrera till TLS 1,2 så snart som möjligt.

Läs mer om den mellanliggande certifikat UTFÄRDARens mellanliggande certifikat krav för Azure Firewall Premium i [Azure Firewall Premium Preview-certifikat](premium-certificates.md).

## <a name="idps"></a>IDP: er

Med ett system för identifiering och skydd av nätverks intrång (IDP: er) kan du övervaka nätverket för skadlig aktivitet, Logga information om den här aktiviteten, rapportera den och eventuellt försöka blockera den. 

I för hands versionen av Azure Firewall Premium finns en IDP: er som möjliggör snabb identifiering av attacker genom att söka efter vissa mönster, till exempel byte sekvenser i nätverks trafik eller kända skadliga Instruction-sekvenser som används av skadlig kod. IDP: er-signaturer är fullständigt hanterade och uppdateras kontinuerligt.

Azure Firewall-signaturer/rulesets inkluderar:
- En tonvikt på finger avtryck av faktiskt skadlig kod, kommando och kontroll, sårbarhets paket och i den vilda skadliga aktivitet som missas av traditionella förebyggande metoder.
- Över 35 000-regler i över 50-kategorier.
    - Kategorierna inkluderar skadlig kod, kommando och kontroll, DoS-attacker, botnät, informations händelser, sårbarheter, sårbarheter, SCADA nätverks protokoll, sårbarhets pakets aktivitet med mera.
- 20 till 40 + nya regler frigörs varje dag.
- Låg falsk positiv klassificering med hjälp av tillstånds-till-art-Sandbox och global sensor för att få feedback från andra användare.

Med IDP: er kan du identifiera attacker i alla portar och protokoll för icke-krypterad trafik. Men när HTTPS-trafiken behöver kontrol leras kan Azure-brandväggen använda sin TLS-kontroll för att dekryptera trafiken och identifiera skadlig aktivitet bättre.  

Med IDP: er bypass-listan kan du inte filtrera trafik till någon av IP-adresserna, intervallen och under näten som anges i listan över undantag. 

## <a name="url-filtering"></a>URL-filtrering

URL-filtrering utökar Azure firewalls FQDN-filtrerings funktion för att överväga en hel URL. Till exempel i `www.contoso.com/a/c` stället för `www.contoso.com` .  

URL-filtrering kan tillämpas både på HTTP-och HTTPS-trafik. När HTTPS-trafik inspekteras kan Azure Firewall Premium Preview använda sin TLS-kontroll för att dekryptera trafiken och extrahera mål-URL: en för att kontrol lera om åtkomst tillåts. TLS-kontrollen kräver att du anmäler dig på program regel nivå. När den är aktive rad kan du använda URL: er för filtrering med HTTPS. 

## <a name="web-categories"></a>Webb kategorier

Med webb kategorier kan administratörer tillåta eller neka användar åtkomst till webbplats kategorier som spel webbplatser, webbplatser för sociala medier och andra. Webb kategorier kommer också att ingå i Azure Firewall standard, men det är mer finjusterat i Azure Firewall Premium Preview. Till skillnad från webb kategori funktionen i standard-SKU: n som matchar kategorin baserat på ett FQDN, matchar Premium-SKU: n kategorin enligt hela URL: en för både HTTP-och HTTPS-trafik. 

Om till exempel Azure-brandväggen fångar upp en HTTPS-begäran för `www.google.com/news` förväntas följande kategorisering: 

- Brand Väggs standard – endast FQDN-delen kommer att undersökas, så `www.google.com` kommer att kategoriseras som *sökmotor*. 

- Brand Väggs Premium – hela URL: en kommer att undersökas, så `www.google.com/news` kommer att kategoriseras som *Nyheter*.

Kategorierna organiseras baserat på allvarlighets grad under **ansvar**, **hög bandbredd**, **företags användning**, **produktivitets förlust**, **allmän surfning** och **Okategoriserade**.

### <a name="category-exceptions"></a>Kategori undantag

Du kan skapa undantag för dina webb kategori regler. Skapa en separat regel samling för Tillåt eller neka med en högre prioritet inom regel samlings gruppen. Du kan till exempel konfigurera en regel samling som tillåter `www.linkedin.com` med prioritet 100 med en regel samling som nekar **sociala nätverk** med prioritet 200. Detta skapar undantaget för den fördefinierade webb kategorin för **sociala nätverk** .

### <a name="categorization-change"></a>Ändring av kategorisering

Du kan begära en kategoriserings ändring om du:

 - Tänk på att en FQDN eller URL ska vara under en annan kategori 
 
eller 

- ha en föreslagen kategori för en Okategoriserade-FQDN eller URL

Du är välkommen att skicka en begäran till [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Regioner som stöds

För hands versionen av Azure Firewall Premium stöds i följande regioner:

- Västeuropa (offentlig/Europa)
- USA, östra (offentlig/USA)
- Östra Australien (offentlig/Australien)
- Sydostasien (offentlig/Asien och stillahavsområdet)
- Storbritannien, södra (offentlig/Storbritannien)
- Europa, norra (offentlig/Europa)
- USA, östra 2 (offentliga/USA)
- Södra centrala USA (offentliga/USA)
- USA, västra 2 (offentliga/USA)
- USA, västra (offentlig/USA)
- USA, centrala (offentlig/USA)
- Norra centrala USA (offentlig/USA)
- Japan, östra (offentlig/Japan)
- Asien, östra (offentlig/Asien och stillahavsområdet)
- Kanada, centrala (offentliga/Kanada)
- Centrala Frankrike (offentliga/Frankrike)
- Södra Afrika (offentlig/Sydafrika)
- Förenade Arabemiraten Nord (offentlig/Förenade Arabemiraten)
- Schweiz, norra (offentlig/Schweiz)
- Södra Brasilien (offentlig/Brasilien)
- Östra Norge (offentliga/Norge)
- Australien, centrala (offentlig/Australien)
- Australien, centrala 2 (offentlig/Australien)
- Australien, sydöstra (offentlig/Australien)
- Kanada, östra (offentliga/Kanada)
- Centrala USA-EUAP (offentlig/Kanarie (USA))
- Frankrike, södra (offentlig/Frankrike)
- Västra Japan (offentlig/Japan)
- Republiken Korea (offentlig/Korea)
- Förenade Arabemiraten Central (offentlig/Förenade Arabemiraten)
- Storbritannien, västra (offentlig/Storbritannien)
- USA, västra centrala (offentlig/USA)
- Västra Indien (offentlig/Indien)


## <a name="known-issues"></a>Kända problem

För hands versionen av Azure Firewall Premium har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|TLS-kontroll stöds endast på HTTPS-standardporten|TLS-kontroll stöder endast HTTPS/443. |Inga. Andra portar kommer att stödjas i GA.|
|ESNI-stöd för FQDN-matchning i HTTPS|Krypterad SNI stöds inte i HTTPS-handskakning.|Idag stöder bara Firefox ESNI via anpassad konfiguration. Rekommenderad lösning är att inaktivera den här funktionen.|
|Klient certifikat (TLS)|Klient certifikat används för att bygga ett ömsesidigt identitets förtroende mellan klienten och servern. Klient certifikat används under en TLS-förhandling. Azure-brandväggen förhandlar om en anslutning till servern och har ingen åtkomst till klient certifikatets privata nyckel.|Inget|
|QUIC/HTTP3|QUIC är den nya huvud versionen av HTTP. Det är ett UDP-baserat protokoll över 80 (PLAN) och 443 (SSL). Kontrollen FQDN/URL/TLS stöds inte.|Konfigurera överföring av UDP 80/443 som nätverks regler.|
|Säker hubb och Tvingad tunnel trafik stöds inte i Premium|För närvarande stöds inte SKU: n för brand Väggs Premium i Secure Hub-distributioner och Tvingad tunnel konfiguration.|Åtgärd har schemalagts för GA.|
Ej betrodda kund signerade certifikat|Kundens signerade certifikat är inte betrodda av brand väggen när den har tagits emot från en intranät-baserad webb server.|Åtgärd har schemalagts för GA.
|Fel käll-och mål-IP-adresser i aviseringar för IDP: er med TLS-kontroll.|När du aktiverar TLS-kontroll och IDP: er utfärdar en ny avisering, är den visade käll-och mål-IP-adressen fel (den interna IP-adressen visas i stället för den ursprungliga IP-adressen).|Åtgärd har schemalagts för GA.|
|Fel Källans IP-adress i aviseringar med IDP: er för HTTP (utan TLS-kontroll).|När oformaterad text-HTTP-trafik används och IDP: er utfärdar en ny avisering, och målet är en offentlig IP-adress, är den visade käll-IP-adressen fel (den interna IP-adressen visas i stället för den ursprungliga IP-adressen).|Åtgärd har schemalagts för GA.|
|Certifikat spridning|När ett CA-certifikat används i brand väggen kan det ta mellan 5-10 minuter innan certifikatet börjar gälla.|Åtgärd har schemalagts för GA.|
|Kringgå IDP: er|Kringgå IDP: er fungerar inte för utgående trafik i TLS, och käll-IP-adress och käll-IP-grupper stöds inte.|Åtgärd har schemalagts för GA.|
|Stöd för TLS 1,3|TLS 1,3 stöds delvis. TLS-tunneln från klient till brand väggen baseras på TLS 1,2 och från brand väggen till den externa webb servern baseras på TLS 1,3.|Uppdateringar unders öks.|




## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Firewall Premium-certifikat](premium-certificates.md)
- [Distribuera och konfigurera Azure Firewall Premium Preview](premium-deploy.md)
- [Migrera till Azure Firewall Premium Preview](premium-migrate.md)
