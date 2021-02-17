---
title: Säker åtkomst till ett nyckelvalv
description: Åtkomst modell för Azure Key Vault, inklusive Active Directory autentiserings-och resurs slut punkter.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: 552aad3e3b41bcfd55d1b57a53d8dff2080a6210
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100534710"
---
# <a name="secure-access-to-a-key-vault"></a>Säker åtkomst till ett nyckelvalv

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina nyckel valv genom att bara tillåta behöriga program och användare. Den här artikeln ger en översikt över Key Vault åtkomst modell. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckel valv.

Mer information om Key Vault finns i [About Azure Key Vault](overview.md); Mer information om vad som kan lagras i ett nyckel valv finns i [om nycklar, hemligheter och certifikat](about-keys-secrets-certificates.md).

## <a name="access-model-overview"></a>Översikt över åtkomst modell

Åtkomst till ett nyckel valv styrs via två gränssnitt: **hanterings planet** och **data planet**. Hanterings planet är den plats där du hanterar Key Vault sig själv. Åtgärder i det här planet innefattar att skapa och ta bort nyckel valv, Hämta Key Vault egenskaper och uppdatera åtkomst principer. Data planet är den plats där du arbetar med de data som lagras i ett nyckel valv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Båda planerna använder [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) för autentisering. För auktorisering använder hanterings planet [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) och data planet använder en [Key Vault åtkomst princip](./assign-access-policy-portal.md) och [Azure RBAC för Key Vault data Plans åtgärder](./rbac-guide.md).

För att få åtkomst till ett nyckel valv i något av planerna måste alla anropare (användare eller program) ha korrekt autentisering och auktorisering. Autentisering upprättar identiteten för anroparen. Auktorisering avgör vilka åtgärder som anroparen kan köra. Autentisering med Key Vault fungerar tillsammans med [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md), som ansvarar för att autentisera identiteten för ett specifikt **säkerhets objekt**.

Ett säkerhets objekt är ett objekt som representerar en användare, grupp, tjänst eller ett program som begär åtkomst till Azure-resurser. Azure tilldelar ett unikt **objekt-ID** till varje säkerhets objekt.

* Ett säkerhets objekt för **användare** identifierar en person som har en profil i Azure Active Directory.

* En **grupp** säkerhets objekt identifierar en uppsättning användare som skapats i Azure Active Directory. Alla roller eller behörigheter som tilldelas gruppen beviljas till alla användare i gruppen.

* Ett **huvud namn för tjänsten** är en typ av säkerhets objekt som identiteter ett program eller en tjänst, vilket är att säga en kod del i stället för en användare eller grupp. Ett objekt-ID för tjänstens huvud namn kallas för **klient-ID** och fungerar som sitt användar namn. Tjänstens huvud namn är **klient hemlighet** eller **certifikat** som fungerar som sitt lösen ord. Många Azure-tjänster stöder tilldelning av [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) med automatisk hantering av **klient-ID** och **certifikat**. Hanterad identitet är det säkraste och rekommenderade alternativet för autentisering i Azure.

Mer information om autentisering till Key Vault finns i [autentisera till Azure Key Vault](authentication.md)

## <a name="key-vault-authentication-options"></a>Key Vault autentiserings alternativ

När du skapar ett nyckel valv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla anropare i båda planerna måste registreras i den här klienten och autentiseras för åtkomst till nyckel valvet. I båda fallen kan program komma åt Key Vault på tre sätt:

- **Endast program**: programmet representerar ett huvud namn för tjänsten eller en hanterad identitet. Den här identiteten är det vanligaste scenariot för program som regelbundet behöver komma åt certifikat, nycklar eller hemligheter från nyckel valvet. För att det här scenariot ska fungera `objectId` måste programmet anges i åtkomst principen och `applicationId` får _inte_ anges eller måste vara `null` .
- **Endast användare**: användaren får åtkomst till nyckel valvet från alla program som är registrerade i klienten. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. För att det här scenariot ska fungera `objectId` måste användaren anges i åtkomst principen och `applicationId` får _inte_ anges eller måste vara `null` .
- **Program-Plus-användare** (kallas ibland _sammansatt identitet_): användaren krävs åtkomst till nyckel valvet från ett särskilt program _och_ programmet måste använda OBO-flödet för att personifiera användaren. För att det här scenariot ska `applicationId` fungera `objectId` måste både och anges i åtkomst principen. `applicationId`Identifierar det program som krävs och `objectId` identifierar användaren. Det här alternativet är för närvarande inte tillgängligt för data planet Azure RBAC (för hands version).

I alla typer av åtkomst autentiserar programmet med Azure AD. Programmet använder en [autentiseringsmetod som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på program typen. Programmet hämtar en token för en resurs i planet för att ge åtkomst. Resursen är en slut punkt i hanterings-eller data planet, baserat på Azure-miljön. Programmet använder token och skickar en REST API begäran till Key Vault. Läs mer i [hela autentiserings flödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Modellen för en enda mekanism för autentisering till båda planerna har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckel valv i organisationen.
- Om en användare lämnar, förlorar de direkt åtkomst till alla nyckel valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera Multi-Factor Authentication för extra säkerhet.

## <a name="resource-endpoints"></a>Resurs slut punkter

Program får åtkomst till planen via slut punkter. Åtkomst kontrollerna för de två planerna fungerar oberoende av varandra. För att ge en program åtkomst till att använda nycklar i ett nyckel valv beviljar du data Plans åtkomst med hjälp av en Key Vault åtkomst princip eller Azure RBAC (för hands version). För att ge en användare Läs behörighet till Key Vault egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat) ger du åtkomst till hanterings planet med Azure RBAC.

I följande tabell visas slut punkterna för hanterings-och data planen.

| Åtkomst &nbsp; plan | Slutpunkter för åtkomst | Operations | Mekanism för åtkomst &nbsp; kontroll |
| --- | --- | --- | --- |
| Hanteringsplanet | **EAN**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckel valv<br><br>Ange Key Vault åtkomst principer<br><br>Ange Key Vault Taggar | Azure RBAC |
| Dataplanet | **EAN**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: kryptera, dekryptera, wrapKey, unwrapKey, signera, verifiera, Hämta, lista, skapa, uppdatera, importera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br> Certifikat: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, Hämta, lista, skapa, importera, uppdatera, ta bort, återställa, säkerhetskopiera, återställa, rensa<br><br>  Hemligheter: Hämta, lista, ange, ta bort, återställa, säkerhetskopiera, Återställ, rensa | Key Vault åtkomst princip eller Azure RBAC (för hands version)|

## <a name="management-plane-and-azure-rbac"></a>Hanterings plan och Azure RBAC

I hanterings planet använder du [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) för att auktorisera de åtgärder som en anropare kan utföra. I Azure RBAC-modellen har varje Azure-prenumeration en instans av Azure AD. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder Azure Resource Manager distributions modell.

Du skapar ett nyckel valv i en resurs grupp och hanterar åtkomst med hjälp av Azure AD. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du beviljar åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad [Key Vault deltagar](../../role-based-access-control/built-in-roles.md#key-vault-contributor) roll till användaren vid en speciell omfattning. Följande omfattnings nivåer kan tilldelas en Azure-roll:

- **Prenumeration**: en Azure-roll som tilldelas på prenumerations nivån gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp**: en Azure-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs**: en Azure-roll som är tilldelad en angiven resurs gäller resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md). 

Du måste ha `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörighet, till exempel [administratör för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) eller [ägare](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Om en användare har `Contributor` behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör noggrant kontrol lera vem som har `Contributor` roll åtkomst till dina nyckel valv. Se till att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Data plan och åtkomst principer

Du kan bevilja åtkomst till data planet genom att ange Key Vault åtkomst principer för ett nyckel valv. Om du vill ange dessa åtkomst principer måste en användare, grupp eller ett program ha `Key Vault Contributor` behörighet till hanterings planet för nyckel valvet.

Du beviljar en användare, grupp eller program åtkomst för att köra specifika åtgärder för nycklar eller hemligheter i ett nyckel valv. Key Vault stöder upp till 1 024 åtkomst princip poster för ett nyckel valv. Om du vill ge data plan åtkomst till flera användare skapar du en Azure AD-säkerhetsgrupp och lägger till användare i gruppen.

Du kan se en fullständig lista över valv och hemliga åtgärder här: [Key Vault åtgärds referens](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> Key Vault åtkomst principer beviljar behörigheter separat till nycklar, hemligheter och certifikat.  Åtkomst behörigheter för nycklar, hemligheter och certifikat finns på valv nivån. 

Mer information om hur du använder åtkomst principer för nyckel valv finns i [tilldela en princip för Key Vault åtkomst](assign-access-policy-portal.md)

> [!IMPORTANT]
> Key Vault åtkomst principer tillämpas på valv nivån. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckel valvet.
Key Vault åtkomst principer stöder inte detaljerade behörigheter på objekt nivå som en speciell nyckel, hemlighet eller certifikat. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Data plan och Azure RBAC (för hands version)

Rollbaserad åtkomst kontroll i Azure är en alternativ modell för att kontrol lera åtkomsten till Azure Key Vault data plan, som kan aktive ras för enskilda nyckel valv. Azure RBAC-behörighets modellen är exklusiv och en gång har angetts är valv åtkomst principerna inaktiva. Azure Key Vault definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att komma åt nycklar, hemligheter eller certifikat.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, nyckel valvet eller en enskild nyckel, hemlighet eller certifikat. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Viktiga fördelar med att använda Azure RBAC-behörighet över valv åtkomst principer är centraliserad hantering av åtkomst kontroll och dess integrering med [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). Privileged Identity Management tillhandahåller tidsbaserad och godkännande-baserad roll aktivering för att minimera riskerna med alltför stora, onödiga eller felanvända åtkomst behörigheter för resurser som du bryr dig om.

Mer information om Key Vault data plan med Azure RBAC finns i [Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Brandväggar och virtuella nätverk

För ett extra säkerhets lager kan du konfigurera brand väggar och virtuella nätverks regler. Du kan konfigurera Key Vault brand väggar och virtuella nätverk för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) som standard. Du kan bevilja åtkomst till trafik från specifika [virtuella Azure-nätverk](../../virtual-network/virtual-networks-overview.md) och offentliga Internet IP-adressintervall, så att du kan bygga en säker nätverks gränser för dina program.

Här följer några exempel på hur du kan använda tjänst slut punkter:

* Du använder Key Vault för att lagra krypterings nycklar, program hemligheter och certifikat, och du vill blockera åtkomst till ditt nyckel valv från det offentliga Internet.
* Du vill låsa åtkomsten till ditt nyckel valv så att endast ditt program eller en kort lista med angivna värdar kan ansluta till ditt nyckel valv.
* Du har ett program som körs i det virtuella Azure-nätverket och det här virtuella nätverket är låst för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat, eller använda kryptografiska nycklar.

Mer information om Key Vault brand vägg och virtuella nätverk finns i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](network-security.md)

> [!NOTE]
> Key Vault brand väggar och regler för virtuella nätverk gäller endast för Key Vaultens data plan. Key Vault kontroll Plans åtgärder (t. ex. skapa, ta bort och ändra åtgärder, inställning av åtkomst principer, inställning av brand väggar och virtuella nätverks regler) påverkas inte av brand väggar och virtuella nätverks regler.

## <a name="private-endpoint-connection"></a>Anslutning till privat slutpunkt

Om det skulle vara nödvändigt att helt blockera Key Vault exponering för allmänheten kan en [privat Azure-slutpunkt](../../private-link/private-endpoint-overview.md) användas. En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta nivån av granularitet i åtkomst kontroll.

Vanliga scenarier för att använda privat länk för Azure-tjänster:

- **Privat åtkomst till tjänster på Azure-plattformen**: Anslut ditt virtuella nätverk till tjänster i Azure utan en offentlig IP-adress på källan eller målet. Tjänste leverantörer kan återge sina tjänster i sitt eget virtuella nätverk och konsumenter kan komma åt dessa tjänster i sitt lokala virtuella nätverk. Den privata länk plattformen hanterar anslutningen mellan konsumenter och tjänster över Azures stamnät nätverk. 
 
- **Lokala och peer-baserade nätverk**: få åtkomst till tjänster som körs i Azure från lokala ExpressRoute privata peering, VPN-tunnlar och peer-baserade virtuella nätverk med privata slut punkter. Du behöver inte konfigurera offentlig peering eller gå igenom Internet för att komma åt tjänsten. Privat länk är ett säkert sätt att migrera arbets belastningar till Azure.
 
- **Skydd mot data läckage**: en privat slut punkt mappas till en instans av en PaaS-resurs i stället för hela tjänsten. Konsumenter kan bara ansluta till den angivna resursen. Åtkomst till någon annan resurs i tjänsten blockeras. Den här mekanismen ger skydd mot data läcker risker. 
 
- **Global räckvidd**: Anslut privat till tjänster som körs i andra regioner. Användarens virtuella nätverk kan vara i region A och det kan ansluta till tjänster bakom privat länk i region B.  
 
- **Utöka till dina egna tjänster**: Aktivera samma upplevelse och funktionalitet för att återge din tjänst privat för konsumenter i Azure. Genom att placera tjänsten bakom en standard Azure Load Balancer kan du aktivera den för privat länk. Konsumenten kan sedan ansluta direkt till tjänsten med hjälp av en privat slut punkt i ett eget virtuellt nätverk. Du kan hantera anslutnings begär Anden med ett samtals flöde för godkännande. En privat Azure-länk fungerar för konsumenter och tjänster som tillhör olika Azure Active Directory klienter. 

Mer information om privata slut punkter finns i [Key Vault med Azures privata länk](./private-link-service.md)

## <a name="example"></a>Exempel

I det här exemplet utvecklar vi ett program som använder ett certifikat för TLS/SSL, Azure Storage att lagra data och en RSA 2 048-bitars nyckel för kryptering av data i Azure Storage. Vårt program körs på en virtuell Azure-dator (eller en virtuell dators skalnings uppsättning). Vi kan använda ett nyckel valv för att lagra program hemligheterna. Vi kan lagra start certifikatet som används av programmet för att autentisera med Azure AD.

Vi behöver åtkomst till följande lagrade nycklar och hemligheter:
- **TLS/SSL-certifikat**: används för TLS/SSL.
- **Lagrings nyckel**: används för att komma åt lagrings kontot.
- **RSA 2 048-bitars nyckel**: används för data krypterings nyckeln wrap/unwrap i Azure Storage.
- **Programhanterad identitet**: används för att autentisera med Azure AD. När åtkomsten till Key Vault beviljas kan programmet Hämta lagrings nyckeln och certifikatet.

Vi måste definiera följande roller för att ange vem som kan hantera, distribuera och granska vårt program:
- **Säkerhets team**: IT-personal från byråns skydds chef (säkerhets tjänsteman) eller liknande bidrags givare. Säkerhets teamet ansvarar för rätt säkerhet. Hemligheterna kan omfatta TLS/SSL-certifikat, RSA-nycklar för kryptering, anslutnings strängar och lagrings konto nycklar.
- **Utvecklare och operatörer**: personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i det här teamet är inte en del av säkerhets personalen. De bör inte ha till gång till känsliga data som TLS/SSL-certifikat och RSA-nycklar. Endast det program som de distribuerar bör ha åtkomst till känsliga data.
- **Granskare**: den här rollen är för deltagare som inte är medlemmar i utvecklings-eller allmän IT-personal. De granskar användningen och underhållet av certifikat, nycklar och hemligheter för att säkerställa efterlevnaden av säkerhets standarder.

Det finns en annan roll som ligger utanför omfånget för programmet: prenumerationen (eller resurs gruppens) administratör. Prenumerations administratören konfigurerar inledande åtkomst behörighet för säkerhets teamet. De ger åtkomst till säkerhets teamet genom att använda en resurs grupp som har de resurser som krävs av programmet.

Vi måste auktorisera följande åtgärder för våra roller:

**Säkerhetsteamet**
- Skapa nyckel valv.
- Aktivera Key Vault loggning.
- Lägg till nycklar och hemligheter.
- Skapa säkerhets kopior av nycklar för haveri beredskap.
- Ange Key Vault åtkomst principer och tilldela roller för att bevilja behörigheter till användare och program för vissa åtgärder.
- Lyft nycklar och hemligheter med jämna mellanrum.

**Utvecklare och operatörer**
- Hämta referenser från säkerhets teamet för bootstrap-och TLS/SSL-certifikat (tumavtrycken), lagrings nyckel (hemlig URI) och RSA-nyckel (nyckel-URI) för figursatta/unwrap.
- Utveckla och distribuera programmet för att komma åt certifikat och hemligheter program mässigt.

**Granskare**
- Granska Key Vault loggar för att bekräfta korrekt användning av nycklar och hemligheter och efterlevnad av data säkerhets standarder.

I följande tabell sammanfattas åtkomst behörigheterna för våra roller och program.

| Roll | Behörigheter på hanteringsplanet | Data Plans behörigheter – valv åtkomst principer | Data Plans behörigheter – Azure RBAC  |
| --- | --- | --- | --- |
| Säkerhetsteamet | [Key Vault deltagare](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certifikat: alla åtgärder <br> Nycklar: alla åtgärder <br> Hemligheter: alla åtgärder | [Key Vault administratör](../../role-based-access-control/built-in-roles.md#key-vault-administrator) |
| Utvecklare och &nbsp; operatörer | Key Vault distributions behörighet<br><br> **Obs!** den här behörigheten gör att distribuerade virtuella datorer kan hämta hemligheter från ett nyckel valv. | Inga | Inga |
| Granskare | Inget | Certifikat: lista <br> Nycklar: lista<br>Hemligheter: lista<br><br> **Obs!** den här behörigheten gör det möjligt för granskare att inspektera attribut (Taggar, aktiverings datum, förfallo datum) för nycklar och hemligheter som inte genereras i loggarna. | [Key Vault läsare](../../role-based-access-control/built-in-roles.md#key-vault-reader) |
| Azure Storage-konto | Inget | Nycklar: get, list, wrapKey, unwrapKey <br> | [Krypterings användare för Key Vault krypterings tjänsten](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user) |
| Program | Inget | Hemligheter: get, list <br> Certifikat: Hämta, lista | [Key Vault hemlig användare](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user) [Key Vault läsaren](../../role-based-access-control/built-in-roles.md#key-vault-reader) |

De tre team rollerna behöver åtkomst till andra resurser tillsammans med Key Vault behörigheter. Utvecklare och operatörer behöver distribuera åtkomst för att distribuera virtuella datorer (eller Web Apps-funktionen i Azure App Service). Granskare behöver Läs behörighet till lagrings kontot där Key Vaults loggarna lagras.

Vårt exempel beskriver ett enkelt scenario. Scenarier med real tid kan vara mer komplexa. Du kan justera behörigheterna till ditt nyckel valv baserat på dina behov. Vi förmodade att säkerhets teamet tillhandahåller nyckel-och hemlighet-referenser (URI: er och tumavtrycken), som används av DevOps-Personalen i sina program. Utvecklare och operatörer kräver ingen åtkomst till data planet. Vi fokuserar på hur du skyddar ditt nyckel valv.

> [!NOTE]
> I det här exemplet visas hur Key Vault åtkomst är låst i produktion. Utvecklare bör ha sin egen prenumeration eller resurs grupp med fullständig behörighet för att hantera sina valv, virtuella datorer och lagrings kontot där de utvecklar programmet.

## <a name="resources"></a>Resurser

- [Om Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure RBAC](../../role-based-access-control/overview.md)
- [Private Link](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Nästa steg

[Autentisera till Azure Key Vault](authentication.md)

[Tilldela en princip för Key Vault åtkomst](assign-access-policy-portal.md)

[Tilldela Azure-rollen åtkomst till nycklar, hemligheter och certifikat](rbac-guide.md)

[Konfigurera Key Vault-brandväggar och virtuella nätverk](network-security.md)

[Upprätta en anslutning till en privat länk till Key Vault](private-link-service.md)