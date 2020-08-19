---
title: Vanliga frågor om Azure App konfiguration
description: Läs vanliga frågor och svar om Azure App konfiguration, t. ex. hur det skiljer sig från Azure Key Vault.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: b1483230313b9e1b8e59cafea478b14ba0dfcc70
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587352"
---
# <a name="azure-app-configuration-faq"></a>Vanliga frågor om Azure App konfiguration

I den här artikeln får du svar på vanliga frågor om Azure App konfiguration.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Hur skiljer sig app-konfigurationen från Azure Key Vault?

Med appens konfiguration kan utvecklare hantera program inställningar och kontrol lera tillgänglighets funktioner. Det syftar till att förenkla många av aktiviteterna för att arbeta med komplexa konfigurations data.

Konfiguration av appar stöder:

- Hierarkiska namnrymder
- Märkning
- Omfattande frågor
- Hämta batch
- Specialiserade hanterings åtgärder
- Ett användar gränssnitt för funktions hantering

App-konfigurationen kompletterar Key Vault och de två bör användas sida vid sida i de flesta program distributioner.

## <a name="should-i-store-secrets-in-app-configuration"></a>Bör jag lagra hemligheter i appens konfiguration?

Även om app-konfigurationen tillhandahåller härdnings säkerhet är Key Vault fortfarande den bästa platsen för att lagra program hemligheter. Key Vault tillhandahåller kryptering på maskin varu nivå, detaljerade åtkomst principer och hanterings åtgärder, till exempel certifikat rotation.

Du kan skapa konfigurations värden för appar som refererar hemligheter som lagras i Key Vault. Mer information finns i [använda Key Vault referenser i en ASP.net Core-app](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Krypterar app-konfigurationen mina data?

Ja. App-konfigurationen krypterar alla nyckel värden som den innehåller och krypterar nätverkskommunikationen. Nyckel namn och etiketter används som index för att hämta konfigurations data och är inte krypterade.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Hur skiljer sig app-konfigurationen från Azure App Service inställningar?

Med Azure App Service kan du definiera inställningar för appar för varje App Service instans. De här inställningarna skickas som miljövariabler till program koden. Du kan associera en inställning med en angiven distributions plats om du vill. Mer information finns i [Konfigurera appinställningar](/azure/app-service/configure-common#configure-app-settings).

Med hjälp av Azure App konfiguration kan du däremot definiera inställningar som kan delas mellan flera appar. Detta inkluderar appar som körs i App Service, samt andra plattformar. Program koden använder de här inställningarna via konfigurationsprovidern för .NET och Java, via Azure SDK eller direkt via REST-API: er.

Du kan också importera och exportera inställningar mellan App Service-och app-konfiguration. Med den här funktionen kan du snabbt konfigurera ett nytt konfigurations Arkiv för appar utifrån befintliga App Service inställningar. Du kan också dela konfigurationen med en befintlig app som förlitar sig på App Service inställningar.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Finns det några storleks begränsningar för nycklar och värden som lagras i appens konfiguration?

Det finns en gräns på 10 KB för ett enda nyckel värdes objekt.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hur ska jag lagra konfigurationer för flera miljöer (test, mellanlagring, produktion och så vidare)?

Du styr vem som kan komma åt app-konfigurationen på en nivå per butik. Använd en separat lagrings plats för varje miljö som kräver olika behörigheter. Den här metoden ger bästa säkerhets isolering.

Om du inte behöver säkerhets isolering mellan miljöer kan du använda etiketter för att skilja mellan konfigurations värden. [Använd etiketter för att aktivera olika konfigurationer för olika miljöer](./howto-labels-aspnet-core.md) , vilket är ett fullständigt exempel.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Vilka är de rekommenderade sätten att använda app Configuration?

Se [metod tips](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Hur mycket kostar det att konfigurera appar?

Det finns två pris nivåer:

- Kostnadsfri nivå
- Standard nivå.

Om du har skapat en butik innan du påförs nivån standard, flyttades den automatiskt till den kostnads fria nivån när den är allmänt tillgänglig. Du kan välja att uppgradera till standard nivån eller stanna kvar på den kostnads fria nivån.

Du kan inte nedgradera en lagring från standard-nivån till den kostnads fria nivån. Du kan skapa en ny butik på den kostnads fria nivån och sedan importera konfigurations data till det arkivet.

## <a name="which-app-configuration-tier-should-i-use"></a>Vilken konfigurations nivå för appar ska jag använda?

Både konfigurations nivåer för appar erbjuder grundläggande funktioner, inklusive konfigurations inställningar, funktions flaggor, Key Vault referenser, grundläggande hanterings åtgärder, mått och loggar.

Tänk på följande när du väljer en nivå.

- **Resurser per prenumeration**: en resurs består av ett enda konfigurations lager. Varje prenumeration är begränsad till ett konfigurations lager på den kostnads fria nivån. Prenumerationer kan ha ett obegränsat antal konfigurations lager på standard nivån.
- **Lagring per resurs**: i den kostnads fria nivån begränsas varje konfigurations lagring till 10 MB lagrings utrymme. På standard-nivån kan varje konfigurations lagring använda upp till 1 GB lagrings utrymme.
- **Nyckel historik**: app-konfiguration lagrar en historik över alla ändringar som har gjorts i nycklar. På den kostnads fria nivån lagras den här historiken i sju dagar. På standard-nivån lagras den här historiken i 30 dagar.
- **Begär Anden per dag**: kostnads fria nivå lager är begränsade till 1 000 förfrågningar per dag. När en butik når 1 000-begäranden returneras HTTP-statuskod 429 för alla begär anden tills midnatt UTC.

    För standard-nivå butiker ingår de första 200 000 förfrågningarna varje dag i den dagliga avgiften. Ytterligare förfrågningar debiteras som överanvändning.

- **Service nivå avtal**: Standard nivån har ett service avtal på 99,9% tillgänglighet. Den kostnads fria nivån har inget service avtal.
- **Säkerhetsfunktioner**: båda nivåerna innehåller grundläggande säkerhetsfunktioner, inklusive kryptering med Microsoft-hanterade nycklar, autentisering via HMAC eller Azure Active Directory, RBAC-stöd och hanterad identitet. Standard-nivån innehåller fler avancerade säkerhetsfunktioner, inklusive stöd för privata länkar och kryptering med Kundhanterade nycklar.
- **Kostnad**: lager på standard nivån har en daglig användnings avgift. Det finns också ett överbelastnings tillägg för begär Anden förbi den dagliga allokeringen. Det kostar inget att använda en lagring på en kostnads fri nivå.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Kan jag uppgradera en butik från den kostnads fria nivån till standard nivån? Kan jag nedgradera en butik från standard-nivån till den kostnads fria nivån?

Du kan när som helst uppgradera från den kostnads fria nivån till standard-nivån.

Du kan inte nedgradera en lagring från standard-nivån till den kostnads fria nivån. Du kan skapa en ny butik på den kostnads fria nivån och sedan [Importera konfigurations data till det arkivet](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Finns det några begränsningar för antalet begär Anden som görs till app-konfigurationen?

Konfigurations lager på den kostnads fria nivån är begränsade till 1 000 förfrågningar per dag. Konfigurations lager på standard nivån kan uppleva tillfälliga begränsningar när begär ande frekvensen överskrider 20 000 begär Anden per timme.

När en butik når sin gräns returneras HTTP-statuskod 429 för alla begär Anden som görs tills tids perioden går ut. `retry-after-ms`Rubriken i svaret ger en föreslagen vänte tid (i millisekunder) innan begäran görs om.

Om ditt program regelbundet upplever HTTP-statuskod 429 svar bör du överväga att utforma om det för att minska antalet begär Anden som görs. Mer information finns i [minska begär Anden som görs till app-konfiguration](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Mitt program tar emot HTTP-statuskod 429 svar. Varför?

Du får en HTTP-statuskod 429-svar under följande omständigheter:

* Överskrider den dagliga gränsen för begäran för en butik på den kostnads fria nivån.
* Tillfälligt begränsning på grund av en hög begär ande frekvens för en butik på standard nivån.
* Mycket bandbredds användning.
* Försöker skapa eller ändra en nyckel när lagrings offerten har överskridits.

Kontrol lera innehållet i 429-svaret för en speciell orsak till varför begäran misslyckades.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hur kan jag få meddelanden om nya versioner och annan information om app-konfigurationen?

Prenumerera på vår [GitHub-annonsering lagrings platsen](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hur kan jag rapportera ett ärende eller lämna ett förslag?

Du kan kontakta oss direkt på [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Nästa steg

* [Om Azure App Configuration](./overview.md)
