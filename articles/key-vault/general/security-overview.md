---
title: Översikt över Azure Key Vault säkerhet
description: En översikt över säkerhetsfunktioner och metod tips för Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: mbaldwin
ms.openlocfilehash: c7635fdc2012ab404709733d8f5849465c2ee82f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071581"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Du använder Azure Key Vault för att skydda krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord i molnet. När du lagrar känsliga och affärs kritiska data måste du vidta åtgärder för att maximera säkerheten för dina valv och de data som lagras i dem.

Den här artikeln innehåller en översikt över säkerhetsfunktioner och metod tips för Azure Key Vault. 

> [!NOTE]
> En omfattande lista över Azure Key Vault säkerhets rekommendationer finns i [säkerhets bas linjen för Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Nätverkssäkerhet

Du kan minska exponeringen för dina valv genom att ange vilka IP-adresser som har åtkomst till dem. Med tjänst slut punkter för virtuella nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slut punkterna kan du också begränsa åtkomsten till en lista över IPv4-adress intervall (Internet Protocol version 4). Alla användare som ansluter till nyckel valvet utanför dessa källor nekas åtkomst.  Fullständig information finns i [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)

När brand Väggs reglerna är aktiva kan användarna bara läsa data från Key Vault när deras begär Anden kommer från tillåtna virtuella nätverk eller IPv4-adress intervall. Detta gäller även för att komma åt Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckel valv från Azure Portal, kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klient dator inte finns i listan över tillåtna. Detta påverkar också Key Vault väljare från andra Azure-tjänster. Användarna kanske kan se en lista över nyckel valv, men inte lista nycklar, om brand Väggs reglerna förhindrar sin klient dator.  Anvisningar för implementering finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md)

Med tjänsten Azure Private Link kan du komma åt Azure Key Vault och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket. En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.  För implementerings steg, se [integrera Key Vault med Azures privata länk](private-link-service.md)

## <a name="tls-and-https"></a>TLS och HTTPS

- Key Vault klient delen (data planet) är en server för flera innehavare. Det innebär att nyckel valv från olika kunder kan dela samma offentliga IP-adress. För att uppnå isolering autentiseras och auktoriseras varje HTTP-begäran oberoende av andra begär Anden.
- Du kan identifiera äldre versioner av TLS för att rapportera sårbarheter, men eftersom den offentliga IP-adressen delas, är det inte möjligt för Key Vault Service-teamet att inaktivera gamla versioner av TLS för enskilda nyckel valv på transport nivå.
- HTTPS-protokollet gör att klienten kan delta i TLS-förhandling. **Klienter kan genomdriva den senaste versionen av TLS**, och varje gång en klient gör det, kommer hela anslutningen att använda motsvarande nivå skydd. Det faktum att Key Vault fortfarande har stöd för äldre TLS-versioner påverkar inte säkerheten för anslutningar med nyare TLS-versioner.
- Trots kända sårbarheter i TLS-protokollet finns det ingen känd attack som tillåter en skadlig agent att extrahera information från ditt nyckel valv när angriparen initierar en anslutning med en TLS-version som har sårbarheter. Angriparen behöver fortfarande autentisera och auktorisera sig själv, och så länge som legitima klienter alltid ansluter med de senaste TLS-versionerna, finns det inget sätt för autentiseringsuppgifterna som har läckts bort från sårbarheter i gamla TLS-versioner.

## <a name="identity-management"></a>Identitetshantering

När du skapar ett nyckel valv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla som försöker hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD. I båda fallen kan program komma åt Key Vault på tre sätt:

- **Endast program**: programmet representerar ett huvud namn för tjänsten eller en hanterad identitet. Den här identiteten är det vanligaste scenariot för program som regelbundet behöver komma åt certifikat, nycklar eller hemligheter från nyckel valvet. För att det här scenariot ska fungera `objectId` måste programmet anges i åtkomst principen och `applicationId` får _inte_ anges eller måste vara `null` .
- **Endast användare**: användaren får åtkomst till nyckel valvet från alla program som är registrerade i klienten. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. För att det här scenariot ska fungera `objectId` måste användaren anges i åtkomst principen och `applicationId` får _inte_ anges eller måste vara `null` .
- **Program-Plus-användare** (kallas ibland _sammansatt identitet_): användaren krävs åtkomst till nyckel valvet från ett särskilt program _och_ programmet måste använda OBO-flödet för att personifiera användaren. För att det här scenariot ska `applicationId` fungera `objectId` måste både och anges i åtkomst principen. `applicationId`Identifierar det program som krävs och `objectId` identifierar användaren. Det här alternativet är för närvarande inte tillgängligt för data planet Azure RBAC (för hands version).

I alla typer av åtkomst autentiserar programmet med Azure AD. Programmet använder en [autentiseringsmetod som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på program typen. Programmet hämtar en token för en resurs i planet för att ge åtkomst. Resursen är en slut punkt i hanterings-eller data planet, baserat på Azure-miljön. Programmet använder token och skickar en REST API begäran till Key Vault. Läs mer i [hela autentiserings flödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Fullständig information finns i [Key Vault grundläggande autentisering](authentication-fundamentals.md)

## <a name="privileged-access"></a>Privilegierad åtkomst

Auktorisering avgör vilka åtgärder som anroparen kan utföra. Auktorisering i Key Vault använder en kombination av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) och Azure Key Vault åtkomst principer.

Åtkomst till valv äger rum genom två gränssnitt eller plan. Dessa plan är hanterings planet och data planet.

- *Hanterings planet* är den plats där du hanterar Key Vault och är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa Key Vault-egenskaper och hantera åtkomst principer.
- Med *data planet* kan du arbeta med data som lagras i ett nyckel valv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Program får åtkomst till planen via slut punkter. Åtkomst kontrollerna för de två planerna fungerar oberoende av varandra. För att ge en program åtkomst till att använda nycklar i ett nyckel valv beviljar du data Plans åtkomst med hjälp av en Key Vault åtkomst princip eller Azure RBAC (för hands version). För att ge en användare Läs behörighet till Key Vault egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat) ger du åtkomst till hanterings planet med Azure RBAC.

I följande tabell visas slut punkterna för hanterings-och data planen.

| Åtkomst &nbsp; plan | Slutpunkter för åtkomst | Operations | Mekanism för åtkomst &nbsp; kontroll |
| --- | --- | --- | --- |
| Hanteringsplanet | **EAN**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckel valv<br><br>Ange Key Vault åtkomst principer<br><br>Ange Key Vault Taggar | Azure RBAC |
| Dataplanet | **EAN**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: kryptera, dekryptera, wrapKey, unwrapKey, signera, verifiera, Hämta, lista, skapa, uppdatera, importera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br> Certifikat: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, Hämta, lista, skapa, importera, uppdatera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br>  Hemligheter: Hämta, lista, ange, ta bort, återställa, säkerhetskopiera, Återställ, rensa | Key Vault åtkomst princip eller Azure RBAC (för hands version)|

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckel valv i en resurs grupp hanterar du åtkomst med hjälp av Azure AD. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du kan bevilja åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad `key vault Contributor` roll till användaren vid en bestämd omfattning. Följande omfattnings nivåer kan tilldelas en Azure-roll:

- **Prenumeration**: en Azure-roll som tilldelas på prenumerations nivån gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp**: en Azure-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs**: en Azure-roll som är tilldelad en angiven resurs gäller resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör noggrant kontrol lera vem som har `Contributor` roll åtkomst till dina nyckel valv. Se till att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.

### <a name="controlling-access-to-key-vault-data"></a>Kontrol lera åtkomsten till Key Vault data

Key Vault åtkomst principer beviljar behörigheter separat till nycklar, hemligheter eller certifikat. Du kan endast ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomst behörigheter för nycklar, hemligheter och certifikat hanteras på valv nivån.

> [!IMPORTANT]
> Key Vault åtkomst principer stöder inte detaljerade behörigheter på objekt nivå som en speciell nyckel, hemlighet eller certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckel valvet.

Du kan ställa in åtkomst principer för ett nyckel valv använder [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md)eller [Key Vault hantering REST-API: er](/rest/api/keyvault/).

Du kan begränsa åtkomsten till data planet genom att använda [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)). Du kan konfigurera [brand väggar och virtuella nätverks regler](network-security.md) för ett extra säkerhets lager.

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Key Vault loggning sparar information om de aktiviteter som utförts i valvet. Fullständig information finns i [Key Vault loggning](logging.md).

Du kan integrera Key Vault med Event Grid för att bli meddelad när status för en nyckel, ett certifikat eller hemligt lagrat i Key Vault har ändrats. Mer information finns i [övervaknings Key Vault med Azure Event Grid](event-grid-overview.md)

Det är också viktigt att övervaka hälso tillståndet för nyckel valvet för att se till att tjänsten fungerar som den ska. Information om hur du gör detta finns i [övervakning och aviseringar för Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Med Azure Key Vault mjuk borttagning och rensnings skydd kan du återställa borttagna valv och valv objekt. Fullständig information finns i [Översikt över Azure Key Vault mjuk borttagning](soft-delete-overview.md).

Du bör också utföra regelbundna säkerhets kopieringar av ditt valv om att uppdatera/ta bort/skapa objekt i ett valv.  

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhets bas linje](security-baseline.md)
- [Metod tips för Azure Key Vault](security-baseline.md)
- [Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)
