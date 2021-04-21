---
title: Riktlinjer för begränsning i Azure Key Vault
description: Key Vault begränsning begränsar antalet samtidiga anrop för att förhindra överanvändning av resurser.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749544"
---
# <a name="azure-key-vault-throttling-guidance"></a>Riktlinjer för begränsning i Azure Key Vault

Begränsning är en process som du initierar som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra överanvändning av resurser. Azure Key Vault (AKV) har utformats för att hantera ett stort antal begäranden. Om ett överväldigande antal begäranden inträffar hjälper begränsning av klientens begäranden till att bibehålla optimal prestanda och tillförlitlighet för AKV-tjänsten.

Begränsningsgränserna varierar beroende på scenariot. Om du till exempel utför en stor mängd skrivningar är risken för begränsning högre än om du bara utför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar Key Vault dess gränser?

Tjänstbegränsningar i Key Vault förhindrar missbruk av resurser och säkerställer tjänstkvaliteten för alla Key Vault klienter. När ett tröskelvärde för tjänsten överskrids begränsar Key Vault ytterligare begäranden från klienten under en viss tidsperiod, returnerar HTTP-statuskod 429 (för många begäranden) och begäran misslyckas. Misslyckade begäranden som returnerar 429 räknas inte mot begränsningsgränserna som spåras av Key Vault. 

Key Vault ursprungligen utformats för att användas för att lagra och hämta dina hemligheter vid distributionen.  Världen har utvecklats och Key Vault används vid körning för att lagra och hämta hemligheter, och ofta vill appar och tjänster använda Key Vault som en databas.  Aktuella gränser stöder inte höga dataflödeshastigheter.

Key Vault skapades ursprungligen med de gränser som angavs [i Azure Key Vault tjänstbegränsningar.](service-limits.md)  Här följer några Key Vault bästa praxis för att maximera dataflödet för att maximera ditt dataflöde för att maximera dina dataflöden:
1. Kontrollera att det finns begränsningar.  Klienten måste respektera exponentiella back off-principer för 429-talet och se till att du gör återförsök enligt riktlinjerna nedan.
1. Dela upp Key Vault trafik mellan flera valv och olika regioner.   Använd ett separat valv för varje säkerhets-/tillgänglighetsdomän.   Om du har fem appar, var och en i två regioner, rekommenderar vi 10 valv som var och en innehåller hemligheterna som är unika för appen och regionen.  En prenumerationsomfattande gräns för alla transaktionstyper är fem gånger den enskilda gränsen för nyckelvalv. Till exempel är HSM-andra transaktioner per prenumeration begränsade till 5 000 transaktioner på 10 sekunder per prenumeration. Överväg att cachelagra hemligheten i din tjänst eller app för att även minska RPS direkt till nyckelvalvet och/eller hantera burst-baserad trafik.  Du kan också dela upp trafiken mellan olika regioner för att minimera svarstiden och använda en annan prenumeration/ett annat valv.  Skicka inte mer än prenumerationsgränsen till Key Vault i en enda Azure-region.
1. Cachelagra hemligheterna som du hämtar Azure Key Vault i minnet och återanvänd från minnet när det är möjligt.  Läs om från Azure Key Vault när den cachelagrade kopian slutar fungera (t.ex. eftersom den roterades vid källan). 
1. Key Vault är utformad för dina egna tjänsthemligheter.   Om du lagrar dina kunders hemligheter (särskilt i scenarier med nyckellagring med högt dataflöde) bör du överväga att placera nycklarna i en databas eller ett lagringskonto med kryptering och bara lagra huvudnyckeln i Azure Key Vault.
1. Kryptera, omsluta och verifiera åtgärder med offentlig nyckel kan utföras utan åtkomst till Key Vault, vilket inte bara minskar risken för begränsning, utan även förbättrar tillförlitligheten (så länge du cachelagrar det offentliga nyckelmaterialet korrekt).
1. Om du använder Key Vault för att lagra autentiseringsuppgifter för en tjänst kontrollerar du om tjänsten stöder Azure AD-autentisering för direktautentisering. Detta minskar belastningen på Key Vault, förbättrar tillförlitligheten och förenklar koden eftersom Key Vault nu kan använda Azure AD-token.  Många tjänster har flyttats till att använda Azure AD-autentisering.  Se den aktuella listan under [Tjänster som stöder hanterade identiteter för Azure-resurser.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Överväg att sprida belastningen/distributionen under en längre tidsperiod för att hålla dig under de aktuella RPS-gränserna.
1. Om din app består av flera noder som behöver läsa samma hemligheter bör du överväga att använda ett förfjärmönster, där en entitet läser hemligheten från Key Vault och bläddrar ut till alla noder.   Cachelagra endast hämtade hemligheter i minnet.
Om du upptäcker att ovanstående fortfarande inte uppfyller dina behov fyller du i tabellen nedan och kontaktar oss för att avgöra vilken ytterligare kapacitet som kan läggas till (exempel som anges nedan endast i illustrerande syfte).

| Valvnamn | Valvregion | Objekttyp (hemlighet, nyckel eller certifikat) | Åtgärd(er)* | Nyckeltyp | Nyckellängd eller kurva | HSM-nyckel?| RPS med stabilt tillstånd krävs | Högsta RPS som behövs |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Nyckel | Tecken | EC | P-256 | No | 200 | 1000 |

\* En fullständig lista över möjliga värden finns i [Azure Key Vault åtgärder](/rest/api/keyvault/key-operations).

Om ytterligare kapacitet godkänns bör du observera följande när kapaciteten ökar:
1. Ändringar i datakonsekvensmodellen. När ett valv tillåts visas med ytterligare dataflödeskapacitet garanterar Key Vault-tjänstens datakonsekvens ändringar (krävs för att uppfylla RPS med högre volym eftersom den underliggande Azure Storage-tjänsten inte kan hålla sig).  I ett nötskal:
  1. **Utan att tillåta** listning: Key Vault-tjänsten återspeglar resultatet av en skrivåtgärd (t.ex. SecretSet, CreateKey) omedelbart i efterföljande anrop (t.ex. SecretGet, KeySign).
  1. **Med lista över** tillåtna : Key Vault-tjänsten återspeglar resultatet av en skrivåtgärd (t.ex. SecretSet, CreateKey) inom 60 sekunder i efterföljande anrop (t.ex. SecretGet, KeySign).
1. Klientkoden måste respektera backend-principen för 429 återförsök. Klientkoden som anropar Key Vault tjänsten får inte omedelbart försöka igen Key Vault när den tar emot en 429-svarskod.  Den Azure Key Vault vägledningen för begränsning som publiceras här rekommenderar att du tillämpar exponentiell backoff när du tar emot en 429 HTTP-svarskod.

Om du har ett giltigt affärsfall för högre begränsningsgränser kan du kontakta oss.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Så här begränsar du din app som svar på tjänstbegränsningar

Följande är **metodtips som** du bör implementera när tjänsten begränsas:
- Minska antalet åtgärder per begäran.
- Minska frekvensen för begäranden.
- Undvik omedelbara återförsök. 
    - Alla begäranden ackumuleras mot dina användningsgränser.

När du implementerar appens felhantering använder du HTTP-felkoden 429 för att identifiera behovet av begränsning på klientsidan. Om begäran misslyckas igen med en HTTP 429-felkod uppstår fortfarande en gräns för Azure-tjänsten. Fortsätt att använda den rekommenderade begränsningsmetoden på klientsidan och försök igen tills den lyckas.

Kod som implementerar exponentiell backoff visas nedan. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Det är enkelt att använda den här koden i ett C#-klientprogram. 

### <a name="recommended-client-side-throttling-method"></a>Rekommenderad begränsningsmetod på klientsidan

På HTTP-felkod 429 börjar du att minska klienten med en exponentiell backoff-metod:

1. Vänta 1 sekund, försök igen
2. Om väntetiden är begränsad i 2 sekunder försöker du igen
3. Om väntetiden är begränsad i 4 sekunder försöker du igen
4. Om väntetiden är begränsad i 8 sekunder försöker du igen
5. Om väntetiden fortfarande är begränsad 16 sekunder försöker du igen

I det här läget bör du inte få HTTP 429-svarskoder.

## <a name="see-also"></a>Se även

En djupare orientering av begränsning i Microsoft Cloud finns i [Begränsningsmönster.](/azure/architecture/patterns/throttling)
