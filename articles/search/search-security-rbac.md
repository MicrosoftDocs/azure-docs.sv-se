---
title: Ange Azure-roller för administrativ åtkomst till Azure
titleSuffix: Azure Cognitive Search
description: Rollbaserad åtkomst kontroll i Azure (Azure RBAC) i Azure Portal för att kontrol lera och delegera administrativa uppgifter för Azure Kognitiv sökning Management.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519507"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Ange Azure-roller för administrativ åtkomst till Azure Kognitiv sökning

Azure tillhandahåller en [Global rollbaserad verifierings modell](../role-based-access-control/role-assignments-portal.md) för alla tjänster som hanteras via portalen eller Resource Manager-API: er. Rollerna ägare, deltagare och läsare bestämmer vilken *tjänst administrations* nivå som Active Directory användare, grupper och säkerhets objekt som tilldelas till varje roll. 

> [!Note]
> Det finns ingen rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att skydda innehåll på tjänsten. Du kan antingen använda en Admin API-nyckel eller API-nyckel för autentiserade begär anden till själva tjänsten. För identitets-baserad åtkomst över Sök resultat kan du skapa säkerhets filter för att trimma resultat efter identitet, ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [säkerhets filter](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Hanterings aktiviteter efter roll

För Azure Kognitiv sökning associeras roller med behörighets nivåer som stöder följande hanterings aktiviteter:

| Roll | Uppgift |
| --- | --- |
| Ägare |Skapa eller ta bort tjänsten eller ett objekt på tjänsten, inklusive API-nycklar, index, indexerare, data källor för indexerare och index scheman.<p>Visa tjänst status, inklusive antal och lagrings storlek.<p>Lägg till eller ta bort roll medlemskap (endast ägaren kan hantera roll medlemskap).<p>Prenumerations administratörer och tjänst ägare har automatiskt medlemskap i rollen ägare. |
| Deltagare | Samma åtkomst nivå som ägare, minus hantering av Azure-roller. En deltagare kan till exempel skapa eller ta bort objekt, eller Visa och återskapa [API-nycklar](search-security-api-keys.md), men kan inte ändra roll medlemskap.<br><br>[Search service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) motsvarar den inbyggda rollen som är den allmänna rollen. |
| Läsare |Visa service Essentials, till exempel tjänstens slut punkt, prenumeration, resurs grupp, region, nivå och kapacitet. Du kan också Visa tjänst mått, till exempel genomsnittliga frågor per sekund, på fliken övervakning. Medlemmar i den här rollen kan inte Visa index, indexerare, data källa eller färdigheter-information. Detta inkluderar användnings data för dessa objekt, till exempel hur många index som finns på tjänsten. |

Roller beviljar inte åtkomst behörighet till tjänstens slut punkt. Sök tjänst åtgärder, till exempel index hantering, index ifyllning och frågor om Sök data, kontrol leras via API-nycklar, inte roller. Mer information finns i [Hantera API-nycklar](search-security-api-keys.md).

## <a name="permissions-table"></a>Behörighets tabell

I följande tabell sammanfattas de åtgärder som tillåts i Azure Kognitiv sökning och vilken nyckel som låser upp åtkomsten till en viss åtgärd.

Azure RBAC-behörigheter gäller för Portal åtgärder och service hantering (skapa, ta bort eller ändra en tjänst eller dess API-nycklar). API-nycklar skapas när en tjänst finns och tillämpas på innehålls åtgärder på tjänsten. För innehålls relaterade åtgärder i portalen, till exempel att skapa eller ta bort objekt, interagerar en Azure RBAC-ägare eller deltagare att interagera med tjänsten med en underförstådd administrations-API-nyckel.

| Åtgärd | Kontrollerat av |
|-----------|-------------------------|
| Skapa en tjänst | Azure RBAC-behörigheter: ägare eller deltagare |
| Skala en tjänst | Azure RBAC-behörigheter: ägare eller deltagare|
| Ta bort en tjänst | Azure RBAC-behörigheter: ägare eller deltagare |
| Hantera administratörs-eller frågeinställningar | Azure RBAC-behörigheter: ägare eller deltagare|
| Visa tjänst information i portalen eller ett hanterings-API | Azure RBAC-behörigheter: ägare, deltagare eller läsare  |
| Visa objekt information och mått i portalen eller ett hanterings-API | Azure RBAC-behörigheter: ägare eller deltagare |
| Skapa, ändra och ta bort objekt på tjänsten: <br>Index och komponent delar (inklusive analys definitioner, bedömnings profiler, CORS-alternativ), indexerare, data källor, synonymer, förslag | Administratörs nyckel om du använder ett API, en Azure RBAC-ägare eller deltagare om du använder portalen |
| Fråga ett index | Administratör eller fråga om du använder ett API, en Azure RBAC-ägare eller deltagare om du använder portalen |
| Fråga system information om objekt, t. ex. returnera statistik, antal och listor över objekt | Administratörs nyckel om du använder ett API, en Azure RBAC-ägare eller deltagare om du använder portalen |

## <a name="next-steps"></a>Nästa steg

+ [Hantera med hjälp av PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering i Azure Kognitiv sökning](search-performance-optimization.md)
+ [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md).
