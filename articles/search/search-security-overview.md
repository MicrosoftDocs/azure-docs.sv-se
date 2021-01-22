---
title: Säkerhetsöversikt
titleSuffix: Azure Cognitive Search
description: Lär dig mer om säkerhetsfunktionerna i Azure Kognitiv sökning för att skydda slut punkter, innehåll och åtgärder.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/22/2020
ms.custom: references_regions
ms.openlocfilehash: 49364681f0c5b4b6cc4d5f20778edb61e9f6f5b3
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695788"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Säkerhet i Azure Kognitiv sökning – översikt

I den här artikeln beskrivs säkerhetsfunktionerna i Azure Kognitiv sökning som skyddar innehåll och åtgärder.

+ På lagrings nivån är data krypteringen inbyggd för allt tjänst hanterat innehåll som sparas till disk, inklusive index, synonym mappningar och definitioner av indexerare, data källor och färdighetsuppsättningar. Alternativt kan du lägga till Kundhanterade nycklar (CMK) för kompletterande kryptering av indexerat innehåll. För tjänster som skapats efter 1 2020 augusti utökas CMK-kryptering till data på temporära diskar, för fullständig "dubbel kryptering" av indexerat innehåll.

+ Inkommande säkerhet syftar på skydd på Sök tjänstens slut punkt vid ökande säkerhets nivåer: från API-nycklar på begäran till inkommande regler i brand väggen till privata slut punkter som helt skyddar din tjänst från det offentliga Internet.

+ Utgående säkerhet relaterar till indexerare som hämtar innehåll från externa källor. För utgående begär Anden ställer du in en hanterad identitet för att söka efter en betrodd tjänst vid åtkomst till data från Azure Storage, Azure SQL, Cosmos DB eller andra Azure-datakällor. En hanterad identitet är en ersättning för autentiseringsuppgifter eller åtkomst nycklar på anslutningen. Utgående säkerhet beskrivs inte i den här artikeln. Mer information om den här funktionen finns i [Anslut till en data källa med hjälp av en hanterad identitet](search-howto-managed-identities-data-sources.md).

Titta på den här videon med snabb takt för en översikt över säkerhets arkitekturen och varje funktions kategori.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Krypterad överföring och lagring

I Azure Kognitiv sökning börjar krypteringen med anslutningar och överföring och utökar till innehåll som lagras på disk. För Sök tjänster på det offentliga Internet lyssnar Azure Kognitiv sökning på HTTPS-port 443. Alla klient-till-tjänst-anslutningar använder TLS 1,2-kryptering. Tidigare versioner (1,0 eller 1,1) stöds inte.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="diagram som illustrerar olika typer av säkerhet på varje nivå av service engagemang":::

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

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Inkommande säkerhet och Endpoint Protection

Inkommande säkerhetsfunktioner skyddar Sök tjänstens slut punkt genom att öka säkerhets-och komplexitets nivåerna. För det första krävs en API-nyckel för autentiserad åtkomst för alla begär Anden. För det andra kan du ange brand Väggs regler som begränsar åtkomsten till vissa IP-adresser. För avancerat skydd är ett tredje alternativ att aktivera Azures privata länk för att skydda tjänstens slut punkt från all Internet trafik.

### <a name="public-access-using-api-keys"></a>Offentlig åtkomst med API-nycklar

Som standard nås en Sök tjänst via det offentliga molnet med hjälp av nyckelbaserad autentisering för administratörs-eller frågans åtkomst till Sök tjänstens slut punkt. En [API-nyckel](search-security-rbac.md) är en sträng som består av slumpmässigt genererade siffror och bokstäver. Typ av nyckel (administratör eller fråga) fastställer åtkomst nivån. Inlämning av en giltig nyckel betraktas som bevis för att begäran härstammar från en betrodd entitet.

Det finns två åtkomst nivåer till din Sök tjänst, som aktive ras med följande API-nycklar:

+ Administratörs nyckel (tillåter Läs-och skriv åtkomst för [create-Read-Update-Delete-Delete-Delete-](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) åtgärder på Sök tjänsten)

+ Fråga efter nyckel (tillåter skrivskyddad åtkomst till dokument samlingen för ett index)

*Administratörs nycklar* skapas när tjänsten är etablerad. Det finns två administratörs nycklar som anges som *primär* och *sekundär* för att hålla dem direkt, men i själva verket är de utbytbara. Varje tjänst har två administratörs nycklar så att du kan återställa en över utan att förlora åtkomsten till tjänsten. Du kan [Återskapa administratörs nyckeln](search-security-api-keys.md#regenerate-admin-keys) regelbundet enligt rekommendationer för Azure-säkerhet, men du kan inte lägga till det totala antalet admin-nycklar. Det finns högst två administrations nycklar per Sök tjänst.

*Frågeinställningar* skapas som de behövs och är utformade för klient program som utfärdar frågor. Du kan skapa upp till 50 frågeinställningar. I program kod anger du Sök-URL och en fråge-API-nyckel för att tillåta skrivskyddad åtkomst till dokument samlingen för ett särskilt index. Tillsammans är slut punkten, en API-nyckel för skrivskyddad åtkomst och ett mål index som definierar omfattning och åtkomst nivå för anslutningen från klient programmet.

Autentisering krävs för varje begäran, där varje begäran består av en obligatorisk nyckel, en åtgärd och ett objekt. Vid samman koppling är de två behörighets nivåerna (fullständig eller skrivskyddad) plus kontexten (till exempel en fråga-åtgärd för ett index) tillräckliga för att tillhandahålla fullständig säkerhet för tjänst åtgärder. Mer information om nycklar finns i [skapa och hantera API-nycklar](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>IP-begränsad åtkomst

Om du vill kontrol lera åtkomsten till din Sök tjänst ytterligare kan du skapa ingående brand Väggs regler som tillåter åtkomst till en speciell IP-adress eller ett intervall med IP-adresser. Alla klient anslutningar måste göras via en tillåten IP-adress, annars nekas anslutningen.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="diagram över exempel arkitektur för begränsad åtkomst till IP":::

Du kan använda portalen för att [Konfigurera inkommande åtkomst](service-configure-firewall.md).

Du kan också använda REST-API: er för hantering. Från och med API version 2020-03-13, med parametern [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) , kan du begränsa åtkomsten till din tjänst genom att identifiera IP-adresser, individuellt eller i ett intervall, som du vill bevilja åtkomst till din Sök tjänst.

### <a name="private-endpoint-no-internet-traffic"></a>Privat slut punkt (ingen Internet trafik)

En [privat slut punkt](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåter en klient i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) att säkert komma åt data i ett sökindex över en [privat länk](../private-link/private-link-overview.md).

Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för anslutningar till din Sök tjänst. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Ett VNET möjliggör säker kommunikation mellan resurser, med ditt lokala nätverk och Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="diagram över exempel arkitektur för åtkomst till privat slut punkt":::

Även om den här lösningen är den säkraste, är användningen av ytterligare tjänster en extra kostnad, så se till att du har en tydlig förståelse av fördelarna innan du simhopp i. eller mer information om kostnader finns på [sidan med priser](https://azure.microsoft.com/pricing/details/private-link/). Mer information om hur dessa komponenter fungerar tillsammans finns på videon överst i den här artikeln. Täckning av privat slut punkts alternativ börjar på 5:48 i videon. Instruktioner för hur du konfigurerar slut punkten finns i [skapa en privat slut punkt för Azure kognitiv sökning](service-create-private-endpoint.md).

## <a name="index-access"></a>Index åtkomst

I Azure Kognitiv sökning är ett enskilt index inte ett skydds Bart objekt. I stället fastställs åtkomst till ett index på tjänst lagret (Läs-eller skriv åtkomst till tjänsten), tillsammans med kontexten för en åtgärd.

För slut användar åtkomst kan du strukturera förfrågningar om att ansluta med hjälp av en [frågegrupp](search-security-rbac.md), som gör begäran skrivskyddad och inkludera det särskilda index som används av din app. I en förfrågan finns det ingen idé att koppla index eller att komma åt flera index samtidigt, så att alla begär Anden är riktade mot ett enda index med definition. Därför definierar själva begäran (en nyckel plus ett enskilt mål index) säkerhets gränserna.

Administratörs-och utvecklarens åtkomst till index är inte differentierad: båda behöver Skriv behörighet för att skapa, ta bort och uppdatera objekt som hanteras av tjänsten. Alla med en [Administratörs nyckel](search-security-rbac.md) till din tjänst kan läsa, ändra eller ta bort alla index i samma tjänst. För att skydda mot oavsiktlig eller skadlig borttagning av index, är din interna käll kontroll för kod till gångar en påföljd för att återföra en oönskad index borttagning eller ändring. Azure Kognitiv sökning har redundans i klustret för att säkerställa tillgängligheten, men den lagrar eller kör inte din egna kod som används för att skapa eller läsa in index.

För lösningar med flera innehavare som kräver säkerhets gränser på index nivån omfattar sådana lösningar vanligt vis en mellan nivå, som kunder använder för att hantera index isolering. Mer information om användnings fallet för flera innehavare finns i [design mönster för SaaS-program för flera innehavare och Azure kognitiv sökning](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Användaråtkomst

Hur en användare kommer åt ett index och andra objekt bestäms av typen av API-nyckel i begäran. De flesta utvecklare skapar och tilldelar [frågeinställningar](search-security-api-keys.md) för Sök begär Anden på klient sidan. En frågegrupp beviljar skrivskyddad åtkomst till sökbart innehåll i indexet.

Om du behöver detaljerad kontroll över varje användare över Sök resultat kan du bygga säkerhets filter på dina frågor och returnera dokument som är associerade med en viss säkerhets identitet. I stället för fördefinierade roller och roll tilldelningar implementeras identitetsbaserade åtkomst kontroller som ett *filter* som trimmar Sök Resultat för dokument och innehåll baserat på identiteter. I följande tabell beskrivs två metoder för att trimma Sök Resultat av obehörigt innehåll.

| Metod | Beskrivning |
|----------|-------------|
|[Säkerhets trimning baserat på identitets filter](search-security-trimming-for-azure-search.md)  | Dokumenterar det grundläggande arbets flödet för att implementera åtkomst kontroll för användar identitet. Det omfattar att lägga till säkerhets identifierare i ett index och sedan förklarar filtreringen för fältet för att trimma resultat från otillåtet innehåll. |
|[Säkerhets trimning baserat på Azure Active Directory identiteter](search-security-trimming-for-azure-search-with-aad.md)  | Den här artikeln visar hur du hämtar identiteter från Azure Active Directory (Azure AD), en av de [kostnads fria tjänsterna](https://azure.microsoft.com/free/) i Azure Cloud Platform. |

## <a name="administrative-rights"></a>Administrativa rättigheter

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) för etablering av Azure-resurser. I Azure Kognitiv sökning används Resource Manager för att skapa eller ta bort tjänsten, hantera API-nycklar och skala tjänsten. Därför avgör Azure Role-tilldelningar vilka som kan utföra dessa uppgifter, oavsett om de använder [portalen](search-manage.md), [POWERSHELL](search-manage-powershell.md)eller [hantering REST-API: er](/rest/api/searchmanagement/search-howto-management-rest-api).

Administratörs rättigheter över innehåll som är värd för tjänsten, till exempel möjligheten att skapa eller ta bort ett index, härleds genom API-nycklar enligt beskrivningen i [föregående avsnitt](#index-access).

> [!TIP]
> Med hjälp av Azure-omfattande mekanismer kan du låsa en prenumeration eller resurs för att förhindra oavsiktlig eller obehörig borttagning av Sök tjänsten av användare med administratörs behörighet. Mer information finns i [låsa resurser för att förhindra oväntad borttagning](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Certifieringar och efterlevnad

Azure Kognitiv sökning har certifierats vara kompatibel för flera globala, regionala och branschspecifika standarder för både det offentliga molnet och Azure Government. För den fullständiga listan hämtar du [fakta bladet om **Microsoft Azure regelefterlevnad**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) från sidan officiella gransknings rapporter.

Om du vill ha kompatibilitet kan du använda [Azure policy](../governance/policy/overview.md) för att implementera de bästa metoderna för säkerhet i [Azure](../security/benchmarks/introduction.md). Azure Security Benchmark är en samling säkerhets rekommendationer, som du kan använda för att mäta säkerhets kontroller som mappar till viktiga åtgärder som du bör vidta för att minimera hot mot tjänster och data. Det finns för närvarande 11 säkerhets kontroller, inklusive [nätverks säkerhet](../security/benchmarks/security-control-network-security.md), [loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md)och [data skydd](../security/benchmarks/security-control-data-protection.md) för att ge ett fåtal.

Azure Policy är en inbyggd funktion i Azure som hjälper dig att hantera efterlevnad för flera standarder, inklusive de som ingår i Azures säkerhets benchmark. För välkända benchmarks kan Azure Policy tillhandahålla inbyggda definitioner som ger både kriterier och ett åtgärds bara svar som behandlar icke-efterlevnad.

För Azure Kognitiv sökning finns det för närvarande en inbyggd definition. Det är för diagnostisk loggning. Med den här inbyggda kan du tilldela en princip som identifierar Sök tjänster som saknar diagnostikloggning och sedan aktiverar den. Mer information finns i [Azure policy regler för regelefterlevnad för Azure kognitiv sökning](security-controls-policy.md).

## <a name="see-also"></a>Se även

+ [Grunderna i Azure-säkerhet](../security/fundamentals/index.yml)
+ [Azure-säkerhet](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)
