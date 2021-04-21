---
title: Azure Key Vault säkerhetsöversikt
description: En översikt över säkerhetsfunktioner och metodtips för Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819450"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault-säkerhet

Azure Key Vault krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar och lösenord) i molnet. När du lagrar känsliga och affärskritiska data måste du dock vidta åtgärder för att maximera säkerheten för dina valv och de data som lagras i dem.

Den här artikeln innehåller en översikt över säkerhetsfunktioner och metodtips för Azure Key Vault.

> [!NOTE]
> En omfattande lista över Azure Key Vault säkerhetsrekommendationer finns i [Säkerhetsbaslinje för Azure Key Vault](security-baseline.md).

## <a name="network-security"></a>Nätverkssäkerhet

Du kan minska valvexponeringen genom att ange vilka IP-adresser som har åtkomst till dem. Med tjänstslutpunkter för virtuellt nätverk Azure Key Vault du begränsa åtkomsten till ett angivet virtuellt nätverk. Med slutpunkterna kan du också begränsa åtkomsten till en lista över IPv4-adressintervall (Internet Protocol version 4). Alla användare som ansluter till ditt nyckelvalv utanför dessa källor nekas åtkomst.  Fullständig information finns i [Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)

När brandväggsreglerna är i kraft kan användarna bara läsa data från Key Vault när deras begäranden kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även åtkomst Key Vault från Azure Portal. Även om användarna kan bläddra till ett nyckelvalv från Azure Portal kanske de inte kan lista nycklar, hemligheter eller certifikat om deras klientdator inte finns med i listan över tillåtna. Implementeringssteg finns i [Konfigurera Azure Key Vault brandväggar och virtuella nätverk](network-security.md)

Azure Private Link Service ger dig åtkomst Azure Key Vault och Azure-värdtjänster för kunder/partner via en privat slutpunkt i ditt virtuella nätverk. En privat Azure-slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontroll.  Implementeringssteg finns i [Integrera Key Vault med Azure Private Link](private-link-service.md)

## <a name="tls-and-https"></a>TLS och HTTPS

- Den Key Vault klientsidan (dataplanet) är en server för flera innehavare. Det innebär att nyckelvalv från olika kunder kan dela samma offentliga IP-adress. För att uppnå isolering autentiseras och auktoriserar varje HTTP-begäran oberoende av andra begäranden.
- Du kan identifiera äldre versioner av TLS för att rapportera sårbarheter, men eftersom den offentliga IP-adressen delas är det inte möjligt för Key Vault-tjänstteamet att inaktivera gamla versioner av TLS för enskilda nyckelvalv på transportnivå.
- HTTPS-protokollet gör att klienten kan delta i TLS-förhandling. **Klienter kan tillämpa den senaste versionen av TLS**, och när en klient gör det använder hela anslutningen motsvarande nivåskydd. Det faktum att Key Vault fortfarande stöder äldre TLS-versioner försämrar inte säkerheten för anslutningar med nyare TLS-versioner.
- Trots kända säkerhetsrisker i TLS-protokollet finns det ingen känd attack som skulle tillåta att en illvillig agent extraherar information från nyckelvalvet när angriparen initierar en anslutning med en TLS-version som har sårbarheter. Angriparen skulle fortfarande behöva autentisera och auktorisera sig själv, och så länge legitima klienter alltid ansluter med de senaste TLS-versionerna går det inte att läcka autentiseringsuppgifter från sårbarheter i gamla TLS-versioner.

## <a name="identity-management"></a>Identitetshantering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras det automatiskt med Azure AD-klientorganisationen för prenumerationen. Alla som försöker hantera eller hämta innehåll från ett valv måste autentiseras av Azure AD. I båda fallen kan program komma åt Key Vault på tre sätt:

- **Endast program:** Programmet representerar ett huvudnamn för tjänsten eller en hanterad identitet. Den här identiteten är det vanligaste scenariot för program som regelbundet behöver åtkomst till certifikat, nycklar eller hemligheter från nyckelvalvet. För att det här scenariot ska fungera måste för programmet anges i åtkomstprincipen och får inte `objectId` anges eller måste vara `applicationId`  `null` .
- **Endast användare:** Användaren kommer åt nyckelvalvet från alla program som är registrerade i klientorganisationen. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. För att det här scenariot ska fungera måste användarens anges i åtkomstprincipen och `objectId` får inte anges eller måste vara `applicationId`  `null` .
- **Application-plus-user** (kallas ibland sammansatt _identitet):_ Användaren måste komma åt nyckelvalvet från ett visst program och programmet måste använda OBO-flödet (on-behalf-of authentication) för att personifiera användaren.  För att det här scenariot ska `applicationId` fungera måste både och anges i `objectId` åtkomstprincipen. `applicationId`identifierar det obligatoriska programmet och identifierar `objectId` användaren. Det här alternativet är för närvarande inte tillgängligt för dataplanet Azure RBAC.

I alla typer av åtkomst autentiseras programmet med Azure AD. Programmet använder alla [autentiseringsmetoder som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på programtypen. Programmet hämtar en token för en resurs i planet för att bevilja åtkomst. Resursen är en slutpunkt i hanterings- eller dataplanet, baserat på Azure-miljön. Programmet använder token och skickar en REST API till Key Vault. Om du vill veta mer kan du granska [hela autentiseringsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Fullständig information finns i [Key Vault grundläggande om autentisering](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Key Vault alternativ för autentisering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras det automatiskt med Azure AD-klientorganisationen för prenumerationen. Alla anropare i båda plan måste registreras i den här klientorganisationen och autentiseras för att få åtkomst till nyckelvalvet. I båda fallen kan program komma åt Key Vault på tre sätt:

- **Endast program:** Programmet representerar ett huvudnamn för tjänsten eller en hanterad identitet. Den här identiteten är det vanligaste scenariot för program som regelbundet behöver åtkomst till certifikat, nycklar eller hemligheter från nyckelvalvet. För att det här scenariot ska fungera måste för programmet anges i åtkomstprincipen och `objectId` får inte anges eller måste vara `applicationId`  `null` .
- **Endast användare:** Användaren kommer åt nyckelvalvet från alla program som är registrerade i klientorganisationen. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. För att det här scenariot ska fungera måste användarens anges i åtkomstprincipen och får inte `objectId` anges eller måste vara `applicationId`  `null` .
- **Program plusanvändare** (kallas ibland sammansatt _identitet):_ Användaren måste komma åt nyckelvalvet från ett visst program och programmet måste använda OBO-flödet (on-behalf-of authentication) för att personifiera användaren.  För att det här scenariot ska `applicationId` fungera måste både och anges i `objectId` åtkomstprincipen. `applicationId`identifierar det obligatoriska programmet och identifierar `objectId` användaren. Det här alternativet är för närvarande inte tillgängligt för dataplanet Azure RBAC (förhandsversion).

I alla typer av åtkomst autentiseras programmet med Azure AD. Programmet använder alla [autentiseringsmetoder som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på programtypen. Programmet hämtar en token för en resurs i planet för att bevilja åtkomst. Resursen är en slutpunkt i hanterings- eller dataplanet, baserat på Azure-miljön. Programmet använder token och skickar en REST API till Key Vault. Om du vill veta mer kan du granska [hela autentiseringsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Modellen med en enda mekanism för autentisering till båda plan har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckelvalv i organisationen.
- Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen.
- Organisationer kan anpassa autentisering med hjälp av alternativen i Azure AD, till exempel för att aktivera multifaktorautentisering för ökad säkerhet.

## <a name="access-model-overview"></a>Översikt över åtkomstmodell

Åtkomsten till ett nyckelvalv styrs via två gränssnitt: **hanteringsplanet** och **dataplanet**. Det är på hanteringsplanet du hanterar Key Vault själv. Åtgärder i det här planet omfattar att skapa och ta bort nyckelvalv, Key Vault egenskaper och uppdatera åtkomstprinciper. Dataplanet är den plats där du arbetar med de data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Båda plan använder [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) för autentisering. För auktorisering använder hanteringsplanet rollbaserad åtkomstkontroll i [Azure (Azure RBAC)](../../role-based-access-control/overview.md) och dataplanet använder en [Key Vault-åtkomstprincip](./assign-access-policy-portal.md) och [Azure RBAC för att Key Vault dataplansåtgärder.](./rbac-guide.md)

För att få åtkomst till ett nyckelvalv i endera planet måste alla anropare (användare eller program) ha rätt autentisering och auktorisering. Autentiseringen upprättar identiteten för anroparen. Auktorisering avgör vilka åtgärder som anroparen kan köra. Autentisering med Key Vault fungerar tillsammans [med Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), som ansvarar för att autentisera identiteten för ett visst **säkerhetsobjekt**.

Ett säkerhetsobjekt är ett objekt som representerar en användare, grupp, tjänst eller ett program som begär åtkomst till Azure-resurser. Azure tilldelar ett unikt **objekt-ID till** varje säkerhetsobjekt.

- Ett **säkerhetsobjekt** för användaren identifierar en person som har en profil i Azure Active Directory.
- Ett  gruppsäkerhetsobjekt identifierar en uppsättning användare som skapats i Azure Active Directory. Alla roller eller behörigheter som tilldelats gruppen beviljas till alla användare i gruppen.
- Ett **huvudnamn för** tjänsten är en typ av säkerhetsobjekt som identifierar ett program eller en tjänst, det vill säga ett kodstycke i stället för en användare eller grupp. Objekt-ID:t för tjänstens huvudnamn kallas dess **klient-ID** och fungerar som dess användarnamn. Tjänstens huvudnamns **klienthemlighet** eller **certifikat** fungerar som dess lösenord. Många Azure-tjänster stöder tilldelning av [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) med automatiserad hantering av **klient-ID** och **certifikat.** Hanterad identitet är det säkraste och rekommenderade alternativet för autentisering i Azure.

Mer information om autentisering för att Key Vault finns i [Autentisera till Azure Key Vault](authentication.md)

## <a name="privileged-access"></a>Privilegierad åtkomst

Auktorisering avgör vilka åtgärder som anroparen kan utföra. Auktorisering i Key Vault använder en kombination av rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../../role-based-access-control/overview.md) och Azure Key Vault åtkomstprinciper.

Åtkomst till valv sker via två gränssnitt eller plan. Dessa plan är hanteringsplanet och dataplanet.

- *Hanteringsplanet* är den plats där du hanterar Key Vault och det är det gränssnitt som används för att skapa och ta bort valv. Du kan också läsa key vault-egenskaper och hantera åtkomstprinciper.
- Med *dataplanet* kan du arbeta med data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Program får åtkomst till planen via slutpunkter. Åtkomstkontrollerna för de två planen fungerar oberoende av varandra. Om du vill ge ett program åtkomst att använda nycklar i ett nyckelvalv beviljar du dataplansåtkomst med hjälp av en Key Vault åtkomstprincip eller Azure RBAC. Om du vill ge en användare läsbehörighet Key Vault egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat), beviljar du hanteringsplanåtkomst med Azure RBAC.

I följande tabell visas slutpunkterna för hanterings- och dataplan.

| &nbsp;Åtkomstplan | Slutpunkter för åtkomst | Operations | &nbsp;Åtkomstkontrollmekanism |
| --- | --- | --- | --- |
| Hanteringsplanet | **Globala:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckelvalv<br><br>Ange Key Vault åtkomstprinciper<br><br>Ange Key Vault taggar | Azure RBAC |
| Dataplanet | **Globala:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: kryptera, dekryptera, wrapKey, unwrapKey, signera, verifiera, hämta, lista, skapa, uppdatera, importera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br> Certifikat: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Hemligheter: hämta, lista, ange, ta bort, återställa, säkerhetskopiera, återställa, rensa | Key Vault åtkomstprincip eller Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Hantera administrativ åtkomst till Key Vault

När du skapar ett nyckelvalv i en resursgrupp hanterar du åtkomsten med hjälp av Azure AD. Du ger användare eller grupper möjlighet att hantera nyckelvalven i en resursgrupp. Du kan bevilja åtkomst på en specifik omfångsnivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckelvalv tilldelar du en `key vault Contributor` fördefinierad roll till användaren i ett specifikt omfång. Följande omfångsnivåer kan tilldelas till en Azure-roll:

- **Prenumeration:** En Azure-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser i prenumerationen.
- **Resursgrupp:** En Azure-roll som tilldelats på resursgruppsnivå gäller för alla resurser i den resursgruppen.
- **Specifik resurs:** En Azure-roll som tilldelats för en specifik resurs gäller för den resursen. I det här fallet är resursen ett specifikt nyckelvalv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Azure RBAC: Inbyggda roller.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Om en användare har behörighet till ett nyckelvalvs hanteringsplan kan användaren ge sig själv åtkomst till dataplanet genom att `Contributor` ange en Key Vault åtkomstprincip. Du bör kontrollera vem som har `Contributor` rollåtkomst till dina nyckelvalv. Se till att endast behöriga personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.

### <a name="controlling-access-to-key-vault-data"></a>Kontrollera åtkomst till Key Vault data

Key Vault åtkomstprinciper beviljar behörigheter separat till nycklar, hemligheter eller certifikat. Du kan endast ge en användare åtkomst till nycklar och inte hemligheter. Åtkomstbehörigheter för nycklar, hemligheter och certifikat hanteras på valvnivå.

> [!IMPORTANT]
> Key Vault åtkomstprinciper har inte stöd för detaljerade behörigheter på objektnivå, till exempel en specifik nyckel, hemlighet eller certifikat. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckelvalvet.

Du kan ange åtkomstprinciper för ett nyckelvalv med [hjälp av Azure Portal,](assign-access-policy-portal.md) [Azure CLI,](assign-access-policy-cli.md) [Azure PowerShell](assign-access-policy-powershell.md)eller [REST-API:Key Vault Management.](/rest/api/keyvault/)

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Key Vault loggning sparar information om de aktiviteter som utförs i valvet. Fullständig information finns i Key Vault [loggning.](logging.md)

Du kan integrera Key Vault med Event Grid meddelas när statusen för en nyckel, ett certifikat eller en hemlighet som lagras i nyckelvalvet har ändrats. Mer information finns i [Monitoring Key Vault with Azure Event Grid](event-grid-overview.md)

Det är också viktigt att övervaka hälsotillståndet för nyckelvalvet för att se till att tjänsten fungerar som avsett. Information om hur du gör det finns i [Övervakning och aviseringar för Azure Key Vault](alert.md).

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

Azure Key Vault mjuk borttagning och rensning kan du återställa borttagna valv och valvobjekt. Fullständig information finns i [Azure Key Vault översikt över mjuk borttagning.](soft-delete-overview.md)

Du bör också göra regelbundna valv back ups när du uppdaterar/tar bort/skapar objekt i ett valv.  

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhetsbaslinje](security-baseline.md)
- [Azure Key Vault metodtips](security-baseline.md)
- [Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Inbyggda roller](../../role-based-access-control/built-in-roles.md)