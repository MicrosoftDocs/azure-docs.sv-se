---
title: Säkerhetsöversikt
titleSuffix: Azure Cognitive Search
description: Lär dig mer om säkerhetsfunktionerna i Azure Kognitiv sökning för att skydda slut punkter, innehåll och åtgärder.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097644"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Säkerhets översikt för Azure Kognitiv sökning

I den här artikeln beskrivs säkerhetsfunktionerna i Azure Kognitiv sökning som skyddar innehåll och åtgärder.

För inkommande begär Anden som görs till en Sök tjänst finns det ett förlopp för säkerhets åtgärder som skyddar Sök tjänstens slut punkt: från API-nycklar på begäran till inkommande regler i brand väggen, till privata slut punkter som helt skyddar din tjänst från det offentliga Internet.

För utgående begär Anden som görs till andra tjänster görs den dominerande begäran av indexerare som läser innehåll från externa källor. Du kan ange autentiseringsuppgifter för anslutnings strängen. Eller så kan du konfigurera en hanterad identitet för att söka i en betrodd tjänst vid åtkomst till data från Azure Storage, Azure SQL, Cosmos DB eller andra Azure-datakällor. En hanterad identitet är en ersättning för autentiseringsuppgifter eller åtkomst nycklar på anslutningen. Mer information om den här funktionen finns i [Anslut till en data källa med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md).

Skriv åtgärder till externa tjänster: en Sök tjänst skriver till loggfiler och skriver till Azure Storage när du skapar kunskaps lager, bevarar cachelagrade berikade berikade och bevarar fel söknings sessioner. Andra tjänst-till-tjänst-anrop, till exempel Cognitive Services, görs i det interna nätverket.

Titta på den här videon med snabb takt för en översikt över säkerhets arkitekturen och varje funktions kategori.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Nätverkssäkerhet

<a name="service-access-and-authentication"></a>

Inkommande säkerhetsfunktioner skyddar Sök tjänstens slut punkt genom att öka säkerhets-och komplexitets nivåerna. För det första krävs en API-nyckel för autentiserad åtkomst för alla begär Anden. För det andra kan du ange brand Väggs regler som begränsar åtkomsten till vissa IP-adresser. För avancerat skydd är ett tredje alternativ att aktivera Azures privata länk för att skydda tjänstens slut punkt från all Internet trafik.

### <a name="public-access-using-api-keys"></a>Offentlig åtkomst med API-nycklar

Som standard nås en Sök tjänst via det offentliga molnet med hjälp av nyckelbaserad autentisering för administratörs-eller frågans åtkomst till Sök tjänstens slut punkt. Inlämning av en giltig nyckel betraktas som bevis för att begäran härstammar från en betrodd entitet. Nyckelbaserad autentisering beskrivs i nästa avsnitt.

### <a name="configure-ip-firewalls"></a>Konfigurera IP-brandväggar

Om du vill kontrol lera åtkomsten till din Sök tjänst ytterligare kan du skapa ingående brand Väggs regler som tillåter åtkomst till en speciell IP-adress eller ett intervall med IP-adresser. Alla klient anslutningar måste göras via en tillåten IP-adress, annars nekas anslutningen.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagram över exempel arkitektur för begränsad åtkomst till IP":::

Du kan använda portalen för att [Konfigurera inkommande åtkomst](service-configure-firewall.md).

Du kan också använda REST-API: er för hantering. Från och med API version 2020-03-13, med parametern [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , kan du begränsa åtkomsten till din tjänst genom att identifiera IP-adresser, individuellt eller i ett intervall, som du vill bevilja åtkomst till din Sök tjänst.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Nätverks isolering via en privat slut punkt (ingen Internet trafik)

Du kan upprätta en [privat slut punkt](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) att säkert komma åt data i ett sökindex över en [privat länk](../private-link/private-link-overview.md).

Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för anslutningar till din Sök tjänst. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Ett VNET möjliggör säker kommunikation mellan resurser, med ditt lokala nätverk och Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagram över exempel arkitektur för åtkomst till privat slut punkt":::

Även om den här lösningen är den säkraste, är användningen av ytterligare tjänster en extra kostnad, så se till att du har en tydlig förståelse av fördelarna innan du simhopp i. eller mer information om kostnader finns på [sidan med priser](https://azure.microsoft.com/pricing/details/private-link/). Mer information om hur dessa komponenter fungerar tillsammans finns på videon överst i den här artikeln. Täckning av privat slut punkts alternativ börjar på 5:48 i videon. Instruktioner för hur du konfigurerar slut punkten finns i [skapa en privat slut punkt för Azure kognitiv sökning](service-create-private-endpoint.md).

## <a name="authentication"></a>Autentisering

För inkommande begär anden till Sök tjänsten sker autentiseringen via en [obligatorisk API-nyckel](search-security-api-keys.md) (en sträng som består av slumpmässigt genererade siffror och bokstäver) som visar att begäran kommer från en betrodd källa. Kognitiv sökning stöder för närvarande inte Azure Active Directory autentisering för inkommande begär Anden.

Utgående begär Anden som görs av en indexerare omfattas av autentisering av den externa tjänsten. Under tjänsten indexerare i Kognitiv sökning kan göras till en betrodd tjänst på Azure och ansluta till andra tjänster med hjälp av en hanterad identitet. Mer information finns i [Konfigurera en indexerare-anslutning till en data källa med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Auktorisering

Kognitiv sökning tillhandahåller olika auktoriserings modeller för innehålls hantering och tjänst hantering. 

### <a name="authorization-for-content-management"></a>Auktorisering för innehålls hantering

Auktorisering av innehåll och åtgärder relaterade till innehåll är antingen skriv åtkomst, som härleds genom [API-nyckeln](search-security-api-keys.md) som anges i begäran. API-nyckeln är en autentiseringsmekanism, men kan även ge åtkomst beroende på typen av API-nyckel.

+ Administratörs nyckel (tillåter Läs-och Skriv behörighet för Create-Read-Update-Delete-Delete-Delete på Search Service), som skapas när tjänsten är etablerad

+ Fråga efter nyckel (tillåter skrivskyddad åtkomst till dokument samlingen för ett index), skapad som de behövs och är utformade för klient program som utfärdar frågor

I program kod anger du slut punkten och en API-nyckel för att tillåta åtkomst till innehåll och alternativ. En slut punkt kan vara själva tjänsten, index samlingen, ett enskilt index, en dokument samling eller ett enskilt dokument. Vid länkning tillsammans utgör slut punkten, åtgärden (till exempel en begäran om att skapa eller uppdatera) och behörighets nivån (fullständig eller skrivskyddad behörighet baserat på nyckeln) den säkerhets formel som skyddar innehåll och åtgärder.

### <a name="controlling-access-to-indexes"></a>Kontrol lera åtkomst till index

I Azure Kognitiv sökning är ett enskilt index inte ett skydds Bart objekt. I stället fastställs åtkomst till ett index på tjänst lagret (Läs-eller skriv åtkomst baserat på vilken API-nyckel du anger), tillsammans med kontexten för en åtgärd.

För skrivskyddad åtkomst kan du strukturera förfrågningar om att ansluta med hjälp av en [sessionsnyckel](search-security-rbac.md)och inkludera det speciella index som används av din app. I en förfrågan finns det ingen idé att koppla index eller att komma åt flera index samtidigt, så att alla begär Anden är riktade mot ett enda index med definition. Därför definierar själva begäran (en nyckel plus ett enskilt mål index) säkerhets gränserna.

Administratörs-och utvecklarens åtkomst till index är inte differentierad: båda behöver Skriv behörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Alla med en [Administratörs nyckel](search-security-rbac.md) till din tjänst kan läsa, ändra eller ta bort alla index i samma tjänst. För att skydda mot oavsiktlig eller skadlig borttagning av index, är din interna käll kontroll för kod till gångar en påföljd för att återföra en oönskad index borttagning eller ändring. Azure Kognitiv sökning har redundans i klustret för att säkerställa tillgängligheten, men den lagrar eller kör inte din egna kod som används för att skapa eller läsa in index.

För lösningar med flera innehavare som kräver säkerhets gränser på index nivån omfattar sådana lösningar vanligt vis en mellan nivå, som kunder använder för att hantera index isolering. Mer information om användnings fallet för flera innehavare finns i [design mönster för SaaS-program för flera innehavare och Azure kognitiv sökning](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Kontrol lera åtkomst till dokument

Om du behöver detaljerad kontroll över varje användare över Sök resultat kan du bygga säkerhets filter på dina frågor och returnera dokument som är associerade med en viss säkerhets identitet. 

Till följd av "säkerhet på radnivå", stöds inte behörighet till innehåll i indexet med fördefinierade roller eller roll tilldelningar som mappar till entiteter i Azure Active Directory. Alla användar behörigheter för data i externa system, till exempel Cosmos DB, överförs inte med dessa data som indexeras av Kognitiv sökning.

Lösningar för lösningar som kräver "säkerhet på radnivå" inkluderar att skapa ett fält i data källan som representerar en säkerhets grupp eller användar identitet och sedan använda filter i Kognitiv sökning för att selektivt trimma Sök Resultat från dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för att trimma Sök Resultat av obehörigt innehåll.

| Metod | Beskrivning |
|----------|-------------|
|[Säkerhets trimning baserat på identitets filter](search-security-trimming-for-azure-search.md)  | Dokumenterar det grundläggande arbets flödet för att implementera åtkomst kontroll för användar identitet. Det omfattar att lägga till säkerhets identifierare i ett index och sedan förklarar filtreringen för fältet för att trimma resultat från otillåtet innehåll. |
|[Säkerhets trimning baserat på Azure Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln visar hur du hämtar identiteter från Azure Active Directory (Azure AD), en av de [kostnads fria tjänsterna](https://azure.microsoft.com/free/) i Azure Cloud Platform. |

### <a name="authorization-for-service-management"></a>Auktorisering för Service Management

Service Management-åtgärder auktoriseras via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure RBAC är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) för etablering av Azure-resurser. 

I Azure Kognitiv sökning används Resource Manager för att skapa eller ta bort tjänsten, hantera API-nycklar och skala tjänsten. Därför avgör Azure Role-tilldelningar vilka som kan utföra dessa uppgifter, oavsett om de använder [portalen](search-manage.md), [POWERSHELL](search-manage-powershell.md)eller [hantering REST-API: er](/rest/api/searchmanagement/search-howto-management-rest-api).

[Tre grundläggande roller](search-security-rbac.md#management-tasks-by-role) definieras för Sök tjänstens administration. Roll tilldelningarna kan göras med hjälp av en metod som stöds (portal, PowerShell och så vidare) och omfattas av hela tjänsten. Rollen ägare och deltagare kan utföra olika administrations funktioner. Du kan tilldela rollen läsare till användare som bara visar viktig information.

> [!Note]
> Med hjälp av Azure-omfattande mekanismer kan du låsa en prenumeration eller resurs för att förhindra oavsiktlig eller obehörig borttagning av Sök tjänsten av användare med administratörs behörighet. Mer information finns i [låsa resurser för att förhindra oväntad borttagning](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Dataskydd

På lagrings nivån skapas data kryptering för allt tjänst hanterat innehåll som sparas på disk, inklusive index, synonym mappningar och definitioner av indexerare, data källor och färdighetsuppsättningar. Alternativt kan du lägga till Kundhanterade nycklar (CMK) för kompletterande kryptering av indexerat innehåll. För tjänster som skapats efter 1 2020 augusti utökas CMK-kryptering till data på temporära diskar, för fullständig "dubbel kryptering" av indexerat innehåll.

### <a name="data-in-transit"></a>Data under överföring

I Azure Kognitiv sökning börjar krypteringen med anslutningar och överföring och utökar till innehåll som lagras på disk. För Sök tjänster på det offentliga Internet lyssnar Azure Kognitiv sökning på HTTPS-port 443. Alla klient-till-tjänst-anslutningar använder TLS 1,2-kryptering. Tidigare versioner (1,0 eller 1,1) stöds inte.

### <a name="encrypted-data-at-rest"></a>Krypterade data i vila

I följande tabell beskrivs [data krypterings modeller](../security/fundamentals/encryption-models.md)för data som hanteras internt av Sök tjänsten. Vissa funktioner, till exempel kunskaps lager, stegvis anrikning och indexerad indexering, läses från eller skrivs till data strukturer i andra Azure-tjänster. Dessa tjänster har sina egna nivåer av krypterings stöd separat från Azure Kognitiv sökning.

| Modell | Nyckel&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Signaturkrav&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Begränsningar | Gäller för |
|------------------|-------|-------------|--------------|------------|
| kryptering på Server Sidan | Microsoft-hanterade nycklar | Ingen (inbyggt) | Ingen, tillgänglig på alla nivåer i alla regioner för innehåll som skapats efter 24 2018 januari. | Innehåll (index och synonym mappningar) och definitioner (indexerare, data källor, färdighetsuppsättningar) |
| kryptering på Server Sidan | Kundhanterade nycklar | Azure Key Vault | Tillgängligt på fakturerbara nivåer i alla regioner för innehåll som skapats efter 2019 januari. | Innehåll (index och synonym mappningar) på data diskar |
| dubbel kryptering på Server Sidan | Kundhanterade nycklar | Azure Key Vault | Tillgängligt på fakturerbara nivåer i valda regioner på Sök tjänster efter augusti 1 2020. | Innehåll (index och synonym mappningar) på data diskar och temporära diskar |

### <a name="service-managed-keys"></a>Tjänst nycklar som hanteras

Tjänst-hanterad kryptering är en Microsoft-intern åtgärd baserad på [Azure Storage tjänst kryptering](../storage/common/storage-service-encryption.md)med hjälp av 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). Det sker automatiskt vid all indexering, inklusive för stegvisa uppdateringar av index som inte är fullständigt krypterade (skapade före januari 2018).

### <a name="customer-managed-keys-cmk"></a>Kundhanterade nycklar (CMK)

Kundhanterade nycklar kräver ytterligare en fakturerings tjänst Azure Key Vault, som kan finnas i en annan region, men under samma prenumeration som Azure Kognitiv sökning. Genom att aktivera CMK-kryptering ökar du index storlek och försämrar frågans prestanda. Utifrån observationer som datum kan du vänta på att se en ökning på 30%-60% i fråge tiderna, även om den faktiska prestandan varierar beroende på index definitionen och typer av frågor. På grund av den här prestandan rekommenderar vi att du bara aktiverar den här funktionen på index som verkligen kräver det. Mer information finns i [Konfigurera kund hanterade krypterings nycklar i Azure kognitiv sökning](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Dubbel kryptering

I Azure Kognitiv sökning är dubbel kryptering en utökning av CMK. Det kan vara en enviknings kryptering (en gång av CMK, och återigen med tjänstens hanterade nycklar) och omfattande i omfattning, långsiktig lagring som skrivs till en data disk och kortsiktig lagring som skrivs till temporära diskar. Skillnaden mellan CMK före augusti 1 2020 och efter, och vad som gör att CMK en dubbel krypterings funktion i Azure Kognitiv sökning, är ytterligare kryptering av data i vila på temporära diskar.

Double Encryption är för närvarande tillgängligt för nya tjänster som skapats i dessa regioner efter 1 augusti:

+ USA, västra 2
+ East US
+ USA, södra centrala
+ US Gov, Virginia
+ US Gov, Arizona

## <a name="security-management"></a>Säkerhetshantering

### <a name="api-keys"></a>API-nycklar

Beroende av API-nyckelbaserad autentisering innebär att du bör ha en plan för att återskapa administratörs nyckeln med jämna mellanrum, enligt metod tips för Azure-säkerhet. Det finns högst två administrations nycklar per Sök tjänst. Mer information om hur du skyddar och hanterar API-nycklar finns i [skapa och hantera API-nycklar](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Aktivitets- och diagnostikloggar

Kognitiv sökning loggar inte användar identiteter, så du kan inte referera till loggar för information om en speciell användare. Tjänsten registrerar dock åtgärder för att skapa, läsa, uppdatera och ta bort, som du kanske kan korrelera med andra loggar för att förstå byrån för vissa åtgärder.

Med hjälp av aviseringar och loggnings infrastrukturen i Azure kan du hämta volym toppar eller andra åtgärder som avviker från förväntade arbets belastningar. Mer information om hur du konfigurerar loggar finns i [samla in och analysera logg data](search-monitor-logs.md) och [övervaka fråge förfrågningar](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Azure Kognitiv sökning deltar i vanliga revisioner och har certifierats mot ett antal globala, regionala och branschspecifika standarder för både det offentliga molnet och Azure Government. För den fullständiga listan hämtar du [fakta bladet om **Microsoft Azure regelefterlevnad**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) från sidan officiella gransknings rapporter.

Om du vill ha kompatibilitet kan du använda [Azure policy](../governance/policy/overview.md) för att implementera de bästa metoderna för säkerhet i [Azure](../security/benchmarks/introduction.md). Azure Security Benchmark är en samling säkerhets rekommendationer, som du kan använda för att mäta säkerhets kontroller som mappar till viktiga åtgärder som du bör vidta för att minimera hot mot tjänster och data. Det finns för närvarande 11 säkerhets kontroller, inklusive [nätverks säkerhet](../security/benchmarks/security-control-network-security.md), [loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md)och [data skydd](../security/benchmarks/security-control-data-protection.md) för att ge ett fåtal.

Azure Policy är en inbyggd funktion i Azure som hjälper dig att hantera efterlevnad för flera standarder, inklusive de som ingår i Azures säkerhets benchmark. För välkända benchmarks kan Azure Policy tillhandahålla inbyggda definitioner som ger både kriterier och ett åtgärds bara svar som behandlar icke-efterlevnad.

För Azure Kognitiv sökning finns det för närvarande en inbyggd definition. Det är för diagnostisk loggning. Med den här inbyggda kan du tilldela en princip som identifierar Sök tjänster som saknar diagnostikloggning och sedan aktiverar den. Mer information finns i [Azure policy regler för regelefterlevnad för Azure kognitiv sökning](security-controls-policy.md).

## <a name="see-also"></a>Se även

+ [Grunderna i Azure-säkerhet](../security/fundamentals/index.yml)
+ [Säkerhet i Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)