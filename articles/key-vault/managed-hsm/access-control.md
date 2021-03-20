---
title: Åtkomst kontroll för Azure Managed HSM
description: 'Hantera åtkomst behörigheter för Azure-hanterad HSM och nycklar. Omfattar autentiserings-och auktoriserings modellen för hanterad HSM och hur du skyddar din HSM: er.'
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 0c0a0c5f62f92aaf195e207dfd505ffb017d924e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653908"
---
# <a name="managed-hsm-access-control"></a>Åtkomstkontroll i Managed HSM

> [!NOTE]
> Key Vault Resource provider stöder två resurs typer: **valv** och **hanterade HSM: er**. Åtkomst kontroll som beskrivs i den här artikeln gäller endast **hanterade HSM: er**. Mer information om åtkomst kontroll för hanterad HSM finns i [ge åtkomst till Key Vault nycklar, certifikat och hemligheter med en rollbaserad åtkomst kontroll i Azure](../general/rbac-guide.md).

Azure Key Vault hanterad HSM är en moln tjänst som skyddar krypterings nycklar. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina hanterade HSM: er genom att bara tillåta behöriga program och användare att komma åt dem. Den här artikeln ger en översikt över den hanterade HSM-modellen för åtkomst kontroll. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina hanterade HSM: er.

## <a name="access-control-model"></a>Åtkomstkontrollmodell

Åtkomst till en hanterad HSM styrs via två gränssnitt: **hanterings planet** och **data planet**. Hanterings planet är den plats där du hanterar HSM. Åtgärder i det här planet innefattar att skapa och ta bort hanterade HSM: er och hämta hanterade HSM-egenskaper. Data planet är den plats där du arbetar med de data som lagras i en hanterad HSM – som är HSM-backade krypterings nycklar. Du kan lägga till, ta bort, ändra och använda nycklar för att utföra kryptografiska åtgärder, hantera roll tilldelningar för att kontrol lera åtkomsten till nycklarna, skapa en fullständig HSM-säkerhetskopiering, återställa fullständig säkerhets kopiering och hantera säkerhets domän från data Plans gränssnittet.

För att få åtkomst till en hanterad HSM i något av planerna måste alla anropare ha korrekt autentisering och auktorisering. Autentisering upprättar identiteten för anroparen. Auktorisering avgör vilka åtgärder som anroparen kan köra. En anropare kan vara vilken som helst av de [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som definierats i Azure Active Directory-användare, grupp, tjänstens huvud namn eller hanterad identitet.

Båda planerna använder Azure Active Directory för autentisering. För auktorisering använder de olika system på följande sätt
- Hanterings planet använder Azure-rollbaserad åtkomst kontroll--Azure RBAC--ett auktoriserings system som bygger på Azure Azure Resource Manager 
- Data planet använder en hanterad HSM-nivå RBAC (hanterad HSM lokal RBAC)--ett behörighets system som implementeras och tillämpas på den hanterade HSM-nivån.

När en hanterad HSM skapas innehåller beställaren även en lista över data Plans administratörer (alla [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) stöds). Endast de här administratörerna kan komma åt det hanterade HSM-dataplanet för att utföra nyckel åtgärder och hantera roll tilldelningar för data planet (hanterad HSM lokal RBAC).

Behörighets modellen för båda planen använder samma syntax, men de tillämpas på olika nivåer och roll tilldelningar använder olika omfång. Hanterings planet Azure RBAC tillämpas av Azure Resource Manager medan dataplan-hanterad HSM-lokal RBAC upprätthålls av hanterad HSM.

> [!IMPORTANT]
> Beviljande av en hanterings plan för säkerhets objekt till en hanterad HSM ger dem inte åtkomst till data planet för att komma åt nycklar eller roll tilldelningar för data planet hanterad HSM-lokal RBAC). Den här isoleringen är avsiktlig för att förhindra oavsiktlig expandering av behörigheter som påverkar åtkomst till nycklar som lagras i hanterad HSM.

Till exempel kan en prenumerations administratör (eftersom han/hon har behörigheten "deltagare" för alla resurser i prenumerationen) ta bort en hanterad HSM i sin prenumeration, men om de inte har åtkomst till data planet som uttryckligen beviljas genom hanterad HSM lokal RBAC, kan de inte få åtkomst till nycklar eller hantera roll tilldelning i den hanterade HSM för att ge sig själva eller andra åtkomst till data

## <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

När du skapar en hanterad HSM i en Azure-prenumeration associeras den automatiskt med Azure Active Directory-innehavaren av prenumerationen. Alla anropare i båda planerna måste registreras i den här klienten och autentiseras för åtkomst till hanterad HSM.

Programmet autentiseras med Azure Active Directory innan du anropar något av planerna. Programmet kan använda alla [autentiseringsmetoder som stöds](../../active-directory/develop/authentication-vs-authorization.md) baserat på program typen. Programmet hämtar en token för en resurs i planet för att få åtkomst. Resursen är en slut punkt i hanterings-eller data planet, baserat på Azure-miljön. Programmet använder token och skickar en REST API begäran till hanterad HSM-slutpunkt. Läs mer i [hela autentiserings flödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Det finns flera fördelar med att använda en enda autentiseringsmekanism för båda planerna:

- Organisationer kan styra åtkomsten centralt till alla hanterade HSM: er i organisationen.
- Om en användare lämnar förlorar de direkt åtkomst till alla hanterade HSM: er i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure Active Directory, till exempel för att aktivera Multi-Factor Authentication för extra säkerhet.

## <a name="resource-endpoints"></a>Resurs slut punkter

Säkerhets objekt får åtkomst till planen genom slut punkter. Åtkomst kontrollerna för de två planerna fungerar oberoende av varandra. Om du vill ge åtkomst till ett program för att använda nycklar i en hanterad HSM beviljar du data Plans åtkomst genom att använda hanterad HSM lokalt RBAC. För att ge en användare åtkomst till hanterad HSM-resurs för att skapa, läsa, ta bort, flytta den hanterade HSM: er och redigera andra egenskaper och taggar som du använder Azure RBAC.

I följande tabell visas slut punkterna för hanterings-och data planen.

| Åtkomst &nbsp; plan | Slutpunkter för åtkomst | Operations | Åtkomstkontrollmekanismer |
| --- | --- | --- | --- |
| Hanteringsplanet | **EAN**<br> management.azure.com:443<br> | Skapa, läsa, uppdatera, ta bort och flytta hanterade HSM: er<br>Ange hanterade HSM-Taggar | Azure RBAC |
| Dataplanet | **EAN**<br> &lt;HSM-name &gt; . managedhsm.Azure.net:443<br> | **Nycklar**: dekryptera, kryptera,<br> Packa upp, radbryta, verifiera, signera, Hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera, återställa, rensa<br/><br/> **Data Plans roll – hantering (hanterad HSM lokal RBAC)**_: lista roll definitioner, tilldela roller, ta bort roll tilldelningar <br/> <br/> , definiera anpassade roller_* säkerhets kopiering/återställning **: säkerhets kopiering, återställning, <br/> <br/> kontrol lera status säkerhets kopiering/återställning drift** säkerhets domän * *: Hämta och ladda upp säkerhets domän | Hanterad HSM lokal RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>Hanterings plan och Azure RBAC

I hanterings planet använder du Azure RBAC för att auktorisera de åtgärder som en anropare kan utföra. I Azure RBAC-modellen har varje Azure-prenumeration en instans av Azure Active Directory. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder Azure Resource Manager distributions modell. Om du vill bevilja åtkomst använder du [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [Azure Resource Manager REST-API: er](/rest/api/authorization/roleassignments).

Du skapar ett nyckel valv i en resurs grupp och hanterar åtkomst med hjälp av Azure Active Directory. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du beviljar åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad `key vault Contributor` roll till användaren vid en bestämd omfattning. Följande omfattnings nivåer kan tilldelas en Azure-roll:

- **Hanterings grupp**: en Azure-roll som tilldelas på prenumerations nivån gäller för alla prenumerationer i hanterings gruppen.
- **Prenumeration**: en Azure-roll som tilldelas på prenumerations nivån gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp**: en Azure-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs**: en Azure-roll som är tilldelad en angiven resurs gäller resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [Azure RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Data plan och hanterad HSM lokalt RBAC

Du beviljar en säkerhets objekts åtkomst för att köra särskilda nyckel åtgärder genom att tilldela en roll. För varje roll tilldelning måste du ange en roll och omfattning som tilldelningen gäller för. För hanterade HSM-lokala RBAC är två omfattningar tillgängliga.

- **"/" eller "/Keys"**: område för HSM-nivå. Säkerhets objekt som har tilldelats en roll i det här omfånget kan utföra de åtgärder som definierats i rollen för alla objekt (nycklar) i den hanterade HSM.
- **"/Keys/ &lt; Key-name &gt; "**: Key Level-omfattning. Säkerhets objekt som har tilldelats en roll i det här omfånget kan utföra de åtgärder som definierats i den här rollen för alla versioner av den angivna nyckeln.

## <a name="next-steps"></a>Nästa steg

- En vägledning för att komma igång med en administratör finns i [Vad är hanterad HSM?](overview.md).
- En själv studie kurs om roll hantering finns i [hanterad HSM lokal RBAC](role-management.md)
- Mer information om användnings loggning för hanterad HSM-loggning finns i [hanterad HSM-loggning](logging.md).