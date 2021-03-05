---
title: Riktlinjer för begränsning i Azure Key Vault
description: Key Vault begränsning begränsar antalet samtidiga anrop för att förhindra överanvändning av resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7bdc3ac517df6b73fba7231cfe0fdc9855803782
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175761"
---
# <a name="azure-key-vault-throttling-guidance"></a>Riktlinjer för begränsning i Azure Key Vault

Begränsning är en process som du initierar som begränsar antalet samtidiga anrop till Azure-tjänsten för att förhindra överanvändning av resurser. Azure Key Vault (AKV) är utformat för att hantera en stor mängd begär Anden. Om det uppstår ett överbelastat antal begär Anden, kan en begränsning av klientens förfrågningar upprätthålla optimala prestanda och tillförlitlighet för AKV-tjänsten.

Begränsnings gränserna varierar beroende på scenariot. Om du till exempel utför en stor mängd skrivningar är risken för begränsning högre än om du bara genomför läsningar.

## <a name="how-does-key-vault-handle-its-limits"></a>Hur hanterar Key Vault sina gränser?

Tjänst begränsningar i Key Vault förhindra missbruk av resurser och garantera tjänst kvalitet för alla Key Vault klienter. När ett tröskelvärde för tjänsten överskrids, Key Vault begränsar alla ytterligare begär Anden från klienten under en viss tids period, returnerar HTTP-statuskod 429 (för många begär Anden) och begäran Miss lyckas. Misslyckade förfrågningar som returnerar en 429 räknas inte mot begränsnings gränserna som spåras av Key Vault. 

Key Vault har ursprungligen utformats för att användas för att lagra och hämta dina hemligheter vid distributions tillfället.  Världen har utvecklats och Key Vault används vid körning för att lagra och hämta hemligheter och ofta appar och tjänster vill använda Key Vault som en databas.  Aktuella gränser stöder inte höga data flödes hastigheter.

Key Vault skapades ursprungligen med de gränser som angavs i [Azure Key Vault tjänst gränser](service-limits.md).  Här är några rekommenderade rikt linjer/bästa metoder för att Key Vault maximera ditt data flöde:
1. Se till att du har en begränsning på plats.  Klienten måste följa exponent back-off-principer för 429 och se till att du gör nya försök enligt anvisningarna nedan.
1. Dela upp Key Vault trafik mellan flera valv och olika regioner.   Använd ett separat valv för varje säkerhets-/tillgänglighets domän.   Om du har fem appar, var och en i två regioner, rekommenderar vi 10 valv som innehåller hemligheterna som är unika för appen och regionen.  En prenumerations gräns för alla transaktions typer är fem gånger den enskilda nyckel valvs gränsen. Till exempel är HSM-andra transaktioner per prenumeration begränsad till 5 000 transaktioner på 10 sekunder per prenumeration. Överväg att cachelagra hemligheten i din tjänst eller app för att också minska RPS direkt till nyckel valv och/eller hantera burst-baserad trafik.  Du kan också dela upp trafiken mellan olika regioner för att minimera svars tiden och använda en annan prenumeration/valv.  Skicka inte fler än prenumerations gränsen till Key Vault tjänsten i en enda Azure-region.
1. Cachelagra de hemligheter som du hämtar från Azure Key Vault i minnet och återanvänd från minnet när det är möjligt.  Läs bara från Azure Key Vault när den cachelagrade kopian slutar fungera (t. ex. på grund av att den har roterats vid källan). 
1. Key Vault har utformats för dina egna tjänste hemligheter.   Om du lagrar dina kunders hemligheter (särskilt för nyckel lagrings scenarier med höga data flöden) bör du överväga att placera nycklarna i en databas eller ett lagrings konto med kryptering och enbart lagra huvud nyckeln i Azure Key Vault.
1. Åtgärder för att kryptera, figursätta och kontrol lera offentliga nycklar kan utföras utan åtkomst till Key Vault, vilket inte bara minskar risken för begränsning, utan även ökar tillförlitligheten (så länge du cachelagrar det offentliga nyckel materialet).
1. Om du använder Key Vault för att lagra autentiseringsuppgifter för en tjänst kontrollerar du om tjänsten stöder Azure AD-autentisering för att autentisera direkt. Detta minskar belastningen på Key Vault, förbättrar tillförlitligheten och fören klar koden eftersom Key Vault nu kan använda Azure AD-token.  Många tjänster har flyttats till med Azure AD-autentisering.  Se den aktuella listan med [tjänster som har stöd för hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Överväg att sprida belastningen/distributionen över en längre tids period för att ligga under de nuvarande RPS-gränserna.
1. Om din app innehåller flera noder som behöver läsa samma hemligheter, kan du överväga att använda ett mönster för utskrivning, där en entitet läser hemligheten från Key Vault och fläktar ut till alla noder.   Cachelagra bara hämtade hemligheter i minnet.
Om du upptäcker att ovanstående fortfarande inte uppfyller dina behov kan du fylla i tabellen nedan och kontakta oss för att ta reda på vilken ytterligare kapacitet som kan läggas till (exempel som bara beskrivs i exempel).

| Valvnamn | Valv region | Objekt typ (hemligt, nyckel eller certifikat) | Åtgärd (er) * | Nyckel typ | Nyckel längd eller kurva | HSM-nyckel?| RPS för stabilt tillstånd krävs | Topp-RPS krävs |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Nyckel | Tecken | EC | P-256 | Inga | 200 | 1000 |

\* En fullständig lista över möjliga värden finns i [Azure Key Vault åtgärder](/rest/api/keyvault/key-operations).

Om ytterligare kapacitet godkänns bör du tänka på följande när kapaciteten ökar:
1. Modell ändringar för data konsekvens. När ett valv har tillåtts med ytterligare data flödes kapacitet ändras den Key Vault tjänstens data konsekvens ändringar (krävs för att uppfylla högre volym-RPS eftersom den underliggande Azure Storage-tjänsten inte kan fortsätta).  I en kortfattat så Jenkins:
  1. **Utan att tillåta registrering**: Key Vault tjänsten visar resultatet av en Skriv åtgärd (t. ex. SecretSet, CreateKey) omedelbart i efterföljande anrop (t. ex. SecretGet, inloggning).
  1. **Med Tillåt-lista**: Key Vault tjänsten visar resultatet av en Skriv åtgärd (t. ex. SecretSet, CreateKey) inom 60 sekunder i efterföljande anrop (t. ex. SecretGet, inloggning).
1. Klient koden måste uppfylla en princip för säkerhets kopiering för 429-försök. Klient koden som anropar tjänsten Key Vault får inte omedelbart försöka igen Key Vault begär anden när en 429-svarskod tas emot.  Vägledningen för Azure Key Vault begränsning som publiceras här rekommenderar att du använder exponentiell backoff när du tar emot en 429 HTTP-svarskod.

Om du har ett giltigt affärs ärende för högre begränsnings gränser kan du kontakta oss.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Så här begränsar du appen som svar på tjänst begränsningar

Följande är **metod tips** som du bör implementera när din tjänst är begränsad:
- Minska antalet åtgärder per begäran.
- Minska frekvensen för begär Anden.
- Undvik omedelbara återförsök. 
    - Alla begär Anden påförs mot dina användnings gränser.

När du implementerar din apps fel hantering kan du använda HTTP-felkoden 429 för att identifiera behovet av begränsning på klient sidan. Om begäran Miss lyckas med en HTTP 429-felkod påträffas fortfarande en Azure-tjänstegräns. Fortsätt att använda den rekommenderade begränsnings metoden på klient sidan och försök igen tills den lyckas.

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


Det är enkelt att använda den här koden i ett C#-program i klienten. 

### <a name="recommended-client-side-throttling-method"></a>Rekommenderad begränsnings metod på klient Sidan

På HTTP-felkod 429, påbörja begränsning av klienten med en exponentiell backoff metod:

1. Vänta 1 sekund, försök igen
2. Om du fortfarande begränsar vänta 2 sekunder kan du försöka igen
3. Om du fortfarande begränsar vänta 4 sekunder, försök igen
4. Om du fortfarande begränsar vänta 8 sekunder, försök igen
5. Om du fortfarande begränsar vänta 16 sekunder, försök igen

I det här läget bör du inte hämta svars koder för HTTP 429.

## <a name="see-also"></a>Se även

En djupare orientering av begränsningen för Microsoft Cloud finns i [begränsnings mönster](/azure/architecture/patterns/throttling).
