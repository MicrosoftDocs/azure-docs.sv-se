---
title: Azure Managed HSM-åtkomstkontroll
description: Hantera åtkomstbehörigheter för Azure Managed HSM och nycklar. Omfattar autentiserings- och auktoriseringsmodellen för Managed HSM och hur du skyddar dina HSM:er.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750292"
---
# <a name="managed-hsm-access-control"></a>Åtkomstkontroll i Managed HSM

> [!NOTE]
> Key Vault har stöd för två resurstyper: **valv och** **hanterade HSM:er.** Åtkomstkontroll som beskrivs i den här artikeln gäller endast för **hanterade HSM:er.** Mer information om åtkomstkontroll för hanterad HSM finns i Ge åtkomst till Key Vault, certifikat och hemligheter med en [rollbaserad åtkomstkontroll i Azure.](../general/rbac-guide.md)

Azure Key Vault Managed HSM är en molntjänst som skyddar krypteringsnycklar. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina hanterade HSM:er genom att endast tillåta auktoriserade program och användare att komma åt dem. Den här artikeln innehåller en översikt över managed HSM-modellen för åtkomstkontroll. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina hanterade HSM:er.

## <a name="access-control-model"></a>Åtkomstkontrollmodell

Åtkomst till en hanterad HSM styrs via två gränssnitt: **hanteringsplanet** och **dataplanet**. Hanteringsplanet är den plats där du hanterar själva HSM:en. Åtgärder i det här planet omfattar att skapa och ta bort hanterade HSM:er och hämta hanterade HSM-egenskaper. Dataplanet är den plats där du arbetar med data som lagras i en hanterad HSM – det vill säga HSM-backade krypteringsnycklar. Du kan lägga till, ta bort, ändra och använda nycklar för att utföra kryptografiska åtgärder, hantera rolltilldelningar för att styra åtkomsten till nycklarna, skapa en fullständig HSM-säkerhetskopiering, återställa fullständig säkerhetskopiering och hantera säkerhetsdomänen från dataplanets gränssnitt.

För att få åtkomst till en hanterad HSM i endera plan måste alla anropare ha rätt autentisering och auktorisering. Autentiseringen upprättar anroparens identitet. Auktorisering avgör vilka åtgärder som anroparen kan köra. En anropare kan vara något av de säkerhetsobjekt som definieras i Azure Active Directory – användare, grupp, [tjänstens](../../role-based-access-control/overview.md#security-principal) huvudnamn eller hanterad identitet.

Båda plan använder Azure Active Directory för autentisering. För auktorisering använder de olika system på följande sätt
- Hanteringsplanet använder rollbaserad åtkomstkontroll i Azure – Azure RBAC – ett auktoriseringssystem som bygger på Azure Azure Resource Manager 
- Dataplanet använder en hanterad RBAC på HSM-nivå (managed HSM local RBAC) – ett auktoriseringssystem som implementeras och framtvingas på hanterad HSM-nivå.

När en hanterad HSM skapas tillhandahåller beställaren även en lista över [dataplansadministratörer (alla säkerhetsobjekt](../../role-based-access-control/overview.md#security-principal) stöds). Endast dessa administratörer kan komma åt det hanterade HSM-dataplanet för att utföra nyckelåtgärder och hantera rolltilldelningar för dataplanet (hanterad HSM lokal RBAC).

Behörighetsmodellen för båda plan använder samma syntax, men de tillämpas på olika nivåer och rolltilldelningar använder olika omfång. Hanteringsplanet Azure RBAC tillämpas av Azure Resource Manager medan dataplanet Hanterad HSM lokal RBAC framtvingas av den hanterade HSM:en.

> [!IMPORTANT]
> Att bevilja en säkerhetsobjekthanteringsplan åtkomst till en hanterad HSM ger dem inte någon åtkomst till dataplanet för att få åtkomst till nycklar eller rolltilldelningar för dataplanet Hanterad HSM lokal RBAC). Den här isoleringen är avsiktlig för att förhindra oavsiktlig utökning av privilegier som påverkar åtkomsten till nycklar som lagras i Managed HSM.

Till exempel kan en prenumerationsadministratör (eftersom de har behörigheten Deltagare till alla resurser i prenumerationen) ta bort en hanterad HSM i prenumerationen, men om de inte har dataplansåtkomst som specifikt beviljats via hanterad HSM lokal RBAC kan de inte få åtkomst till nycklar eller hantera rolltilldelning i den hanterade HSM:n för att bevilja sig själva eller andra åtkomst till dataplanet.

## <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

När du skapar en hanterad HSM i en Azure-prenumeration associeras den automatiskt Azure Active Directory klientorganisationen för prenumerationen. Alla anropare i båda planen måste vara registrerade i den här klientorganisationen och autentiseras för att få åtkomst till den hanterade HSM:en.

Programmet autentiserar med Azure Active Directory innan något av planerna anropas. Programmet kan använda valfri [autentiseringsmetod som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på programtypen. Programmet hämtar en token för en resurs i planet för att få åtkomst. Resursen är en slutpunkt i hanterings- eller dataplanet, baserat på Azure-miljön. Programmet använder token och skickar en REST API till Managed HSM-slutpunkten. Om du vill veta mer kan du granska [hela autentiseringsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Användningen av en enda autentiseringsmekanism för båda plan har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla hanterade HSM:er i organisationen.
- Om en användare lämnar förlorar de omedelbart åtkomst till alla hanterade HSM:er i organisationen.
- Organisationer kan anpassa autentisering med hjälp av alternativen i Azure Active Directory, till exempel för att aktivera multifaktorautentisering för ökad säkerhet.

## <a name="resource-endpoints"></a>Resursslutpunkter

Säkerhetsobjekt får åtkomst till planen via slutpunkter. Åtkomstkontrollerna för de två planen fungerar oberoende av varandra. Om du vill ge ett program åtkomst att använda nycklar i en hanterad HSM beviljar du dataplansåtkomst med hjälp av hanterad HSM lokal RBAC. Om du vill ge en användare åtkomst till en hanterad HSM-resurs för att skapa, läsa, ta bort, flytta de hanterade HSM:erna och redigera andra egenskaper och taggar som du använder med Azure RBAC.

I följande tabell visas slutpunkterna för hanterings- och dataplan.

| &nbsp;Åtkomstplan | Slutpunkter för åtkomst | Operations | Åtkomstkontrollmekanismer |
| --- | --- | --- | --- |
| Hanteringsplanet | **Globala:**<br> management.azure.com:443<br> | Skapa, läsa, uppdatera, ta bort och flytta hanterade HSM:er<br>Ange hanterade HSM-taggar | Azure RBAC |
| Dataplanet | **Globala:**<br> &lt;hsm-name &gt; .managedhsm.azure.net:443<br> | **Nycklar:** dekryptera, kryptera,<br> unwrap, wrap, verify, sign, get, list, update, create, import, delete, backup, restore, purge<br/><br/> **Rollhantering för dataplan (hanterad HSM lokal RBAC):** lista rolldefinitioner, tilldela roller, ta bort rolltilldelningar,_<br/> <br/>_ definiera anpassade roller *Säkerhetskopiering/återställning:**säkerhetskopiering, återställning, <br/> <br/> kontrollera statussäkerhetskopiering/återställningsåtgärder** Säkerhetsdomän**: ladda ned och ladda upp säkerhetsdomän | Hanterad lokal HSM-RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>Hanteringsplanet och Azure RBAC

I hanteringsplanet använder du Azure RBAC för att auktorisera de åtgärder som en anropare kan köra. I Azure RBAC-modellen har varje Azure-prenumeration en instans av Azure Active Directory. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder den Azure Resource Manager distributionsmodellen. Om du vill bevilja åtkomst [använder du Azure Portal,](https://portal.azure.com/) [Azure CLI,](/cli/azure/install-classic-cli) [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [rest Azure Resource Manager-API:erna](/rest/api/authorization/roleassignments).

Du skapar ett nyckelvalv i en resursgrupp och hanterar åtkomst med hjälp av Azure Active Directory. Du ger användare eller grupper möjlighet att hantera nyckelvalven i en resursgrupp. Du beviljar åtkomsten på en specifik omfångsnivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckelvalv tilldelar du en `key vault Contributor` fördefinierad roll till användaren i ett specifikt omfång. Följande omfångsnivåer kan tilldelas till en Azure-roll:

- **Hanteringsgrupp:** En Azure-roll som tilldelats på prenumerationsnivå gäller för alla prenumerationer i den hanteringsgruppen.
- **Prenumeration:** En Azure-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser i prenumerationen.
- **Resursgrupp:** En Azure-roll som tilldelats på resursgruppsnivå gäller för alla resurser i den resursgruppen.
- **Specifik resurs:** En Azure-roll som tilldelats för en specifik resurs gäller för den resursen. I det här fallet är resursen ett specifikt nyckelvalv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Azure RBAC: Inbyggda roller.](../../role-based-access-control/built-in-roles.md)

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Dataplanet och hanterad HSM lokal RBAC

Du ger säkerhetsobjekt åtkomst för att köra specifika nyckelåtgärder genom att tilldela en roll. För varje rolltilldelning måste du ange en roll och ett omfång som tilldelningen gäller för. Två omfång är tillgängliga för lokal RBAC för Managed HSM.

- **"/" eller "/keys"**: HSM-nivåomfång. Säkerhetsobjekt som tilldelats en roll i det här omfånget kan utföra de åtgärder som definierats i rollen för alla objekt (nycklar) i den hanterade HSM:en.
- **"/keys/ &lt; key-name &gt; "**: Nyckelnivåomfång. Säkerhetsobjekt som har tilldelats en roll i det här omfånget kan endast utföra de åtgärder som definierats i den här rollen för alla versioner av den angivna nyckeln.

## <a name="next-steps"></a>Nästa steg

- En komma igång-självstudie för en administratör finns i [Vad är Hanterad HSM?](overview.md)
- En självstudiekurs om rollhantering finns i [Lokal RBAC för hanterad HSM](role-management.md)
- Mer information om användningsloggning för Managed HSM-loggning finns i [Hanterad HSM-loggning](logging.md)