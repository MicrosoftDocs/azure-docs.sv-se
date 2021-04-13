---
title: Säker åtkomst till ett nyckelvalv
description: Åtkomstmodellen för Azure Key Vault, inklusive Active Directory-autentisering och resursslutpunkter.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: b1ec89db7bc12ffbd21c6e302e065449eba3ac20
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366495"
---
# <a name="secure-access-to-a-key-vault"></a>Säker åtkomst till ett nyckelvalv

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina nyckelvalv genom att endast tillåta auktoriserade program och användare. Den här artikeln innehåller en översikt över Key Vault åtkomstmodell. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

Mer information om Key Vault finns i [Om Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckelvalv finns [i Om nycklar, hemligheter och certifikat.](about-keys-secrets-certificates.md)

## <a name="access-model-overview"></a>Översikt över åtkomstmodell

Åtkomsten till ett nyckelvalv styrs via två gränssnitt: **hanteringsplanet** och **dataplanet**. Det är på hanteringsplanet du hanterar Key Vault själv. Åtgärder i det här planet omfattar att skapa och ta bort nyckelvalv, Key Vault egenskaper och uppdatera åtkomstprinciper. Dataplanet är den plats där du arbetar med de data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Båda plan använder [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) för autentisering. För auktorisering använder hanteringsplanet rollbaserad åtkomstkontroll i [Azure (Azure RBAC)](../../role-based-access-control/overview.md) och dataplanet använder en [Key Vault-åtkomstprincip](./assign-access-policy-portal.md) och [Azure RBAC för att Key Vault dataplansåtgärder.](./rbac-guide.md)

För att få åtkomst till ett nyckelvalv i endera planet måste alla anropare (användare eller program) ha rätt autentisering och auktorisering. Autentiseringen upprättar anroparens identitet. Auktorisering avgör vilka åtgärder som anroparen kan köra. Autentisering med Key Vault fungerar tillsammans [med Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), som ansvarar för att autentisera identiteten för ett visst **säkerhetsobjekt**.

Ett säkerhetsobjekt är ett objekt som representerar en användare, grupp, tjänst eller ett program som begär åtkomst till Azure-resurser. Azure tilldelar ett unikt **objekt-ID till** varje säkerhetsobjekt.

* Ett **säkerhetsobjekt** för användaren identifierar en person som har en profil i Azure Active Directory.

* Ett  gruppsäkerhetsobjekt identifierar en uppsättning användare som skapats i Azure Active Directory. Alla roller eller behörigheter som tilldelats gruppen beviljas till alla användare i gruppen.

* Ett **huvudnamn för** tjänsten är en typ av säkerhetsobjekt som identifierar ett program eller en tjänst, det vill säga ett kodstycke i stället för en användare eller grupp. Objekt-ID:t för tjänstens huvudnamn kallas dess **klient-ID** och fungerar som dess användarnamn. Tjänstens huvudnamns **klienthemlighet** eller **certifikat** fungerar som dess lösenord. Många Azure-tjänster stöder tilldelning av [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) med automatiserad hantering av **klient-ID** och **certifikat.** Hanterad identitet är det säkraste och rekommenderade alternativet för autentisering i Azure.

Mer information om autentisering för att Key Vault finns i [Autentisera till Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Key Vault alternativ för autentisering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras det automatiskt med Azure AD-klientorganisationen för prenumerationen. Alla anropare i båda plan måste registreras i den här klientorganisationen och autentiseras för att få åtkomst till nyckelvalvet. I båda fallen kan program komma åt Key Vault på tre sätt:

- **Endast program:** Programmet representerar ett huvudnamn för tjänsten eller en hanterad identitet. Den här identiteten är det vanligaste scenariot för program som regelbundet behöver åtkomst till certifikat, nycklar eller hemligheter från nyckelvalvet. För att det här scenariot ska fungera måste för programmet anges i åtkomstprincipen och får inte `objectId` anges eller måste vara `applicationId`  `null` .
- **Endast användare:** Användaren kommer åt nyckelvalvet från alla program som är registrerade i klientorganisationen. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. För att det här scenariot ska fungera måste användarens anges i åtkomstprincipen och `objectId` får inte anges eller måste vara `applicationId`  `null` .
- **Application-plus-user** (kallas ibland sammansatt _identitet):_ Användaren måste komma åt nyckelvalvet från ett visst program och programmet måste använda OBO-flödet (on-behalf-of authentication) för att personifiera användaren.  För att det här scenariot ska `applicationId` fungera måste både och anges i `objectId` åtkomstprincipen. `applicationId`identifierar det obligatoriska programmet och identifierar `objectId` användaren. Det här alternativet är för närvarande inte tillgängligt för dataplanet Azure RBAC.

I alla typer av åtkomst autentiseras programmet med Azure AD. Programmet använder alla [autentiseringsmetoder som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på programtypen. Programmet hämtar en token för en resurs i planet för att bevilja åtkomst. Resursen är en slutpunkt i hanterings- eller dataplanet, baserat på Azure-miljön. Programmet använder token och skickar en REST API till Key Vault. Om du vill veta mer kan du granska [hela autentiseringsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Modellen med en enda mekanism för autentisering till båda plan har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckelvalv i organisationen.
- Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen.
- Organisationer kan anpassa autentisering med hjälp av alternativen i Azure AD, till exempel för att aktivera multifaktorautentisering för ökad säkerhet.

## <a name="resource-endpoints"></a>Resursslutpunkter

Program kommer åt planen via slutpunkter. Åtkomstkontrollerna för de två planen fungerar oberoende av varandra. Om du vill ge ett program åtkomst att använda nycklar i ett nyckelvalv beviljar du dataplansåtkomst med hjälp av en Key Vault åtkomstprincip eller Azure RBAC. Om du vill ge en användare läsbehörighet till Key Vault egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat), beviljar du hanteringsplanet åtkomst med Azure RBAC.

I följande tabell visas slutpunkterna för hanterings- och dataplan.

| &nbsp;Åtkomstplan | Slutpunkter för åtkomst | Operations | Mekanism &nbsp; för åtkomstkontroll |
| --- | --- | --- | --- |
| Hanteringsplanet | **Globala:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckelvalv<br><br>Ange Key Vault åtkomstprinciper<br><br>Ange Key Vault taggar | Azure RBAC |
| Dataplanet | **Globala:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: kryptera, dekryptera, wrapKey, unwrapKey, signera, verifiera, hämta, lista, skapa, uppdatera, importera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br> Certifikat: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recover, backup, restore, purge<br><br>  Hemligheter: hämta, lista, ange, ta bort, återställa, säkerhetskopiera, återställa, rensa | Key Vault åtkomstprincip eller Azure RBAC |

## <a name="management-plane-and-azure-rbac"></a>Hanteringsplanet och Azure RBAC

I hanteringsplanet använder du [rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) för att auktorisera de åtgärder som en anropare kan köra. I Azure RBAC-modellen har varje Azure-prenumeration en instans av Azure AD. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder den Azure Resource Manager distributionsmodellen.

Du skapar ett nyckelvalv i en resursgrupp och hanterar åtkomst med hjälp av Azure AD. Du ger användare eller grupper möjlighet att hantera nyckelvalven i en resursgrupp. Du beviljar åtkomsten på en specifik omfångsnivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckelvalv tilldelar du en [fördefinierad Key Vault-deltagarroll](../../role-based-access-control/built-in-roles.md#key-vault-contributor) till användaren i ett specifikt omfång. Följande omfångsnivåer kan tilldelas till en Azure-roll:

- **Prenumeration:** En Azure-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser i prenumerationen.
- **Resursgrupp:** En Azure-roll som tilldelats på resursgruppsnivå gäller för alla resurser i den resursgruppen.
- **Specifik resurs:** En Azure-roll som tilldelats för en specifik resurs gäller för den resursen. I det här fallet är resursen ett specifikt nyckelvalv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md). 

Du måste ha `Microsoft.Authorization/roleAssignments/write` behörigheterna `Microsoft.Authorization/roleAssignments/delete` och, till exempel [Administratör för användaråtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) eller [Ägare](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Om en användare har behörighet till ett nyckelvalvs hanteringsplan kan användaren ge sig själv åtkomst till dataplanet genom att ange `Contributor` en Key Vault åtkomstprincip. Du bör kontrollera vem som har `Contributor` rollåtkomst till dina nyckelvalv. Se till att endast behöriga personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Dataplan och åtkomstprinciper

Du kan bevilja dataplansåtkomst genom att Key Vault åtkomstprinciper för ett nyckelvalv. För att kunna ange dessa åtkomstprinciper måste en användare, grupp eller ett program ha `Key Vault Contributor` behörighet för hanteringsplanet för nyckelvalvet.

Du ger en användare, grupp eller program åtkomst att köra specifika åtgärder för nycklar eller hemligheter i ett nyckelvalv. Key Vault har stöd för upp till 1 024 åtkomstprincipposter för ett nyckelvalv. Om du vill ge flera användare åtkomst till dataplanet skapar du en Azure AD-säkerhetsgrupp och lägger till användare i den gruppen.

Du kan se en fullständig lista över valv- och hemlighetsåtgärder här: [Key Vault åtgärdsreferens](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault åtkomstprinciper beviljar behörigheter separat till nycklar, hemligheter och certifikat.  Åtkomstbehörigheter för nycklar, hemligheter och certifikat finns på valvnivå. 

Mer information om hur du använder åtkomstprinciper för nyckelvalv finns i [Tilldela en Key Vault åtkomstprincip](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault åtkomstprinciper tillämpas på valvnivå. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckelvalvet.
Key Vault åtkomstprinciper stöder inte detaljerade behörigheter på objektnivå, till exempel en specifik nyckel, hemlighet eller certifikat. 
>

## <a name="data-plane-and-azure-rbac"></a>Dataplan och Azure RBAC

Rollbaserad åtkomstkontroll i Azure är en alternativ behörighetsmodell för att styra åtkomsten Azure Key Vault dataplanet, som kan aktiveras för enskilda nyckelvalv. Azure RBAC-behörighetsmodellen är exklusiv och när den har angetts blev åtkomstprinciper för valv inaktiva. Azure Key Vault definierar en uppsättning inbyggda Roller i Azure som omfattar vanliga uppsättningar med behörigheter som används för att komma åt nycklar, hemligheter eller certifikat.

När en Azure-roll tilldelas till ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjekt. Åtkomsten kan vara begränsad till prenumerationsnivån, resursgruppen, nyckelvalvet eller en enskild nyckel, hemlighet eller certifikat. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md)

Viktiga fördelar med att använda Azure RBAC-behörighet över åtkomstprinciper för valv är centraliserad hantering av [åtkomstkontroll och dess integrering med Privileged Identity Management (PIM).](../../active-directory/privileged-identity-management/pim-configure.md) Privileged Identity Management ger tidsbaserad och godkännandebaserad rollaktivering för att minska riskerna med överdriven, onödig eller missbrukad åtkomstbehörighet för resurser som är viktiga för dig.

Mer information om Key Vault dataplanet med Azure RBAC finns i Key Vault, certifikat och hemligheter med en [rollbaserad åtkomstkontroll i Azure](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Brandväggar och virtuella nätverk

För ytterligare ett lager av säkerhet kan du konfigurera brandväggar och regler för virtuella nätverk. Du kan konfigurera Key Vault brandväggar och virtuella nätverk för att neka åtkomst till trafik från alla nätverk (inklusive Internettrafik) som standard. Du kan bevilja åtkomst till trafik från specifika virtuella [Azure-nätverk](../../virtual-network/virtual-networks-overview.md) och offentliga IP-adressintervall för Internet, så att du kan skapa en säker nätverksgräns för dina program.

Här följer några exempel på hur du kan använda tjänstslutpunkter:

* Du använder Key Vault för att lagra krypteringsnycklar, programhemligheter och certifikat, och du vill blockera åtkomsten till ditt nyckelvalv från det offentliga Internet.
* Du vill låsa åtkomsten till ditt nyckelvalv så att endast ditt program, eller en kort lista över angivna värdar, kan ansluta till ditt nyckelvalv.
* Du har ett program som körs i ditt virtuella Azure-nätverk och det här virtuella nätverket är låst för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault hämta hemligheter eller certifikat eller använda kryptografiska nycklar.

Mer information om hur Key Vault och virtuella nätverk finns i [Konfigurera Azure Key Vault och virtuella nätverk](network-security.md)

> [!NOTE]
> Key Vault brandväggar och regler för virtuella nätverk gäller endast för dataplanet för Key Vault. Key Vault åtgärder på kontrollplanet (till exempel åtgärder för att skapa, ta bort och ändra, ställa in åtkomstprinciper, ange brandväggar och regler för virtuellt nätverk) påverkas inte av brandväggar och regler för virtuellt nätverk.

## <a name="private-endpoint-connection"></a>Privat slutpunktsanslutning

Om ett behov av att helt blockera Key Vault exponering för allmänheten kan en [privat Azure-slutpunkt](../../private-link/private-endpoint-overview.md) användas. En privat Azure-slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontroll.

Vanliga scenarier för att använda Private Link för Azure-tjänster:

- **Privat åtkomst till tjänster på Azure-plattformen:** Anslut ditt virtuella nätverk till tjänster i Azure utan en offentlig IP-adress vid källan eller målet. Tjänstleverantörer kan återge sina tjänster i sina egna virtuella nätverk och konsumenter kan komma åt dessa tjänster i sitt lokala virtuella nätverk. Plattformen Private Link hanterar anslutningen mellan konsumenten och tjänsterna i Azures stamnätverk. 
 
- Lokala och peer-baserade nätverk: Få åtkomst till tjänster som körs i Azure från en lokal plats via privat ExpressRoute-peering, **VPN-tunnlar** och peer-baserade virtuella nätverk med hjälp av privata slutpunkter. Du behöver inte konfigurera offentlig peering eller gå via Internet för att nå tjänsten. Private Link är ett säkert sätt att migrera arbetsbelastningar till Azure.
 
- **Skydd mot dataläckage:** En privat slutpunkt mappas till en instans av en PaaS-resurs i stället för hela tjänsten. Konsumenter kan bara ansluta till den specifika resursen. Åtkomst till andra resurser i tjänsten blockeras. Den här mekanismen ger skydd mot dataläckagerisker. 
 
- **Global räckvidd:** Anslut privat till tjänster som körs i andra regioner. Konsumentens virtuella nätverk kan finnas i region A och kan ansluta till tjänster bakom Private Link i region B.  
 
- **Utöka till dina egna tjänster:** Aktivera samma upplevelse och funktioner för att rendera tjänsten privat till konsumenter i Azure. Genom att placera tjänsten bakom en standard Azure Load Balancer kan du aktivera den för Private Link. Konsumenten kan sedan ansluta direkt till din tjänst med hjälp av en privat slutpunkt i sitt eget virtuella nätverk. Du kan hantera anslutningsbegäranden med hjälp av ett flöde för godkännandesamtal. Azure Private Link fungerar för konsumenter och tjänster som tillhör olika Azure Active Directory klienter. 

Mer information om privata slutpunkter finns i [Key Vault med Azure Private Link](./private-link-service.md)

## <a name="example"></a>Exempel

I det här exemplet utvecklar vi ett program som använder ett certifikat för TLS/SSL, Azure Storage för att lagra data och en RSA 2 048-bitars nyckel för kryptering av data i Azure Storage. Vårt program körs på en virtuell Azure-dator (VM) (eller en VM-skalningsuppsättning). Vi kan använda ett nyckelvalv för att lagra programhemligheterna. Vi kan lagra bootstrap-certifikatet som används av programmet för att autentisera med Azure AD.

Vi behöver åtkomst till följande lagrade nycklar och hemligheter:
- **TLS/SSL-certifikat:** Används för TLS/SSL.
- **Lagringsnyckel:** Används för att få åtkomst till lagringskontot.
- **RSA 2 048-bitars** nyckel: Används för att omsluta/packa upp datakrypteringsnyckeln genom att Azure Storage.
- **Program hanterad identitet:** Används för att autentisera med Azure AD. När åtkomst till Key Vault har beviljats kan programmet hämta lagringsnyckeln och certifikatet.

Vi måste definiera följande roller för att ange vem som kan hantera, distribuera och granska vårt program:
- **Säkerhetsteam:** IT-personal från CSO-kontoret (Chief Security Officer) eller liknande deltagare. Säkerhetsteamet ansvarar för att hemligheterna skyddas på rätt sätt. Hemligheterna kan innehålla TLS/SSL-certifikat, RSA-nycklar för kryptering, anslutningssträngar och lagringskontonycklar.
- **Utvecklare och operatörer:** Den personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i det här teamet ingår inte i säkerhetspersonalen. De bör inte ha åtkomst till känsliga data som TLS/SSL-certifikat och RSA-nycklar. Endast det program som de distribuerar ska ha åtkomst till känsliga data.
- **Granskare:** Den här rollen är till för deltagare som inte är medlemmar i utvecklings- eller allmän IT-personal. De granskar användningen och underhållet av certifikat, nycklar och hemligheter för att säkerställa efterlevnad av säkerhetsstandarder.

Det finns en annan roll som ligger utanför omfånget för vårt program: prenumerationsadministratören (eller resursgruppen). Prenumerationsadministratören uppsättningar inledande åtkomstbehörigheter för säkerhetsteamet. De beviljar åtkomst till säkerhetsteamet med hjälp av en resursgrupp som har de resurser som krävs av programmet.

Vi behöver auktorisera följande åtgärder för våra roller:

**Säkerhetsteamet**
- Skapa nyckelvalv.
- Aktivera Key Vault loggning.
- Lägg till nycklar och hemligheter.
- Skapa säkerhetskopior av nycklar för haveriberedskap.
- Ange Key Vault åtkomstprinciper och tilldela roller för att bevilja behörigheter till användare och program för specifika åtgärder.
- Rulla nycklarna och hemligheterna med jämna mellanrum.

**Utvecklare och operatörer**
- Hämta referenser från säkerhetsteamet för bootstrap- och TLS/SSL-certifikat (tumavtryck), lagringsnyckel (hemlig URI) och RSA-nyckel (nyckel-URI) för omslutning/packa upp.
- Utveckla och distribuera programmet för att komma åt certifikat och hemligheter programmatiskt.

**Granskare**
- Granska Key Vault för att bekräfta korrekt användning av nycklar och hemligheter samt efterlevnad av datasäkerhetsstandarder.

I följande tabell sammanfattas åtkomstbehörigheterna för våra roller och program.

| Roll | Behörigheter på hanteringsplanet | Dataplansbehörigheter – åtkomstprinciper för valv | Dataplansbehörigheter – Azure RBAC  |
| --- | --- | --- | --- |
| Säkerhetsteamet | [Key Vault deltagare](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certifikat: alla åtgärder <br> Nycklar: alla åtgärder <br> Hemligheter: alla åtgärder | [Key Vault administratör](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Utvecklare och &nbsp; operatörer | Key Vault distribuera behörighet<br><br> **Obs!** Med den här behörigheten kan distribuerade virtuella datorer hämta hemligheter från ett nyckelvalv. | Inga | Inga |
| Granskare | Inget | Certifikat: lista <br> Nycklar: lista<br>Hemligheter: lista<br><br> **Obs!** Den här behörigheten gör det möjligt för granskare att inspektera attribut (taggar, aktiveringsdatum, förfallodatum) för nycklar och hemligheter som inte genereras i loggarna. | [Key Vault Läsare](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Storage-konto | Inget | Nycklar: get, list, wrapKey, unwrapKey <br> | [Key Vault kryptografitjänstkrypteringsanvändare](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Program | Inget | Hemligheter: hämta, lista <br> Certifikat: hämta, lista | [Key Vault läsa](../../role-based-access-control/built-in-roles.md#key-vault-reader), [Key Vault hemlig användare](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) |

De tre teamrollerna behöver åtkomst till andra resurser tillsammans med Key Vault behörigheter. För att distribuera virtuella datorer (Web Apps funktionen i Azure App Service) behöver utvecklare och operatörer distribuera åtkomst. Granskare behöver läsbehörighet till lagringskontot där Key Vault lagras.

I vårt exempel beskrivs ett enkelt scenario. Verkliga scenarier kan vara mer komplexa. Du kan justera behörigheter till ditt nyckelvalv baserat på dina behov. Vi förutsätter att säkerhetsteamet tillhandahåller nyckel- och hemliga referenser (URI:er och tumavtryck), som används av DevOps-personalen i deras program. Utvecklare och operatörer behöver ingen dataplansåtkomst. Vi har fokuserat på hur du skyddar ditt nyckelvalv.

> [!NOTE]
> Det här exemplet visar Key Vault åtkomst är låst i produktion. Utvecklare bör ha en egen prenumeration eller resursgrupp med fullständig behörighet att hantera sina valv, virtuella datorer och det lagringskonto där de utvecklar programmet.

## <a name="resources"></a>Resurser

- [Om Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Private Link](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Nästa steg

[Autentisera till Azure Key Vault](authentication.md)

[Tilldela en Key Vault åtkomstprincip](assign-access-policy-portal.md)

[Tilldela Azure-roll för åtkomst till nycklar, hemligheter och certifikat](rbac-guide.md)

[Konfigurera Key Vault-brandväggar och virtuella nätverk](network-security.md)

[Upprätta en privat länkanslutning till Key Vault](private-link-service.md)
