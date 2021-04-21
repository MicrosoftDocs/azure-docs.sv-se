---
title: Så här använder du Role-Based Access Control i Azure API Management | Microsoft Docs
description: Lär dig hur du använder de inbyggda rollerna och skapar anpassade roller i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef71591d6d5a26aa737db4e7cb547c8b2c39d92a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812182"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Så här använder du rollbaserad åtkomstkontroll i Azure API Management

Azure API Management använder rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att möjliggöra mer information om åtkomsthantering för API Management-tjänster och entiteter (till exempel API:er och principer). Den här artikeln ger dig en översikt över de inbyggda och anpassade rollerna i API Management. Mer information om åtkomsthantering i Azure Portal finns [i Kom igång med åtkomsthantering i Azure Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Inbyggda roller

API Management för närvarande tre inbyggda roller och kommer att lägga till ytterligare två roller inom en snar framtid. Dessa roller kan tilldelas i olika omfång, inklusive prenumeration, resursgrupp och enskild API Management instans. Om du till exempel tilldelar rollen "API Management Service Reader" till en användare på resursgruppsnivå, har användaren läsbehörighet till alla API Management instanser i resursgruppen. 

Följande tabell innehåller korta beskrivningar av de inbyggda rollerna. Du kan tilldela dessa roller med hjälp av Azure Portal eller andra verktyg, inklusive Azure [PowerShell,](../role-based-access-control/role-assignments-powershell.md) [Azure CLI](../role-based-access-control/role-assignments-cli.md)och [REST API](../role-based-access-control/role-assignments-rest.md). Mer information om hur du tilldelar inbyggda roller finns i Tilldela [Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser.](../role-based-access-control/role-assignments-portal.md)

| Roll          | Läsåtkomst<sup>[1]</sup> | Skrivåtkomst<sup>[2]</sup> | Tjänstskapande, borttagning, skalning, VPN och anpassad domänkonfiguration | Åtkomst till den äldre utgivarportalen | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management tjänstdeltagare | ✓ | ✓ | ✓ | ✓ | Superanvändare. Har fullständig CRUD-åtkomst API Management tjänster och entiteter (till exempel API:er och principer). Har åtkomst till den äldre utgivarportalen. |
| API Management Service Reader | ✓ | | || Har skrivskyddade åtkomst till API Management och entiteter. |
| API Management Service Operator | ✓ | | ✓ | | Kan hantera API Management tjänster, men inte entiteter.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kan hantera API Management entiteter, men inte tjänster.|
| API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kan hantera utvecklarportalen. Skrivskyddade åtkomst till tjänster och entiteter.|

<sup>[1] Läsåtkomst till API Management och entiteter (till exempel API:er och principer).</sup>

<sup>[2] Skriv åtkomst till API Management tjänster och entiteter förutom följande åtgärder: skapande, borttagning och skalning av instanser; VPN-konfiguration; och anpassad domänkonfiguration.</sup>

<sup>\* Rollen Tjänstredigerare blir tillgänglig när vi har migrerat alla administratörsgränssnitt från den befintliga utgivarportalen till Azure Portal. Content Manager-rollen blir tillgänglig när utgivarportalen har omstrukturerats för att endast innehålla funktioner som rör hantering av utvecklarportalen.</sup>  

## <a name="custom-roles"></a>Anpassade roller

Om ingen av de inbyggda rollerna uppfyller dina specifika behov kan anpassade roller skapas för att ge mer detaljerad åtkomsthantering för API Management entiteter. Du kan till exempel skapa en anpassad roll som har skrivskyddade åtkomst till en API Management-tjänst, men som bara har skrivåtkomst till ett specifikt API. Mer information om anpassade roller finns i [Anpassade roller i Azure RBAC.](../role-based-access-control/custom-roles.md) 

> [!NOTE]
> För att kunna se en API Management-instans i Azure Portal måste en anpassad roll innehålla ```Microsoft.ApiManagement/service/read``` åtgärden.

När du skapar en anpassad roll är det enklare att börja med en av de inbyggda rollerna. Redigera attributen för att **lägga** till Åtgärder, **NotActions** eller **AssignableScopes** och spara sedan ändringarna som en ny roll. Följande exempel börjar med rollen "API Management Service Reader" och skapar en anpassad roll med namnet "Calculator API Editor". Du kan tilldela den anpassade rollen till ett specifikt API. Den här rollen har därför bara åtkomst till det API:et. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Artikeln [Azure Resource Manager resursprovideråtgärder](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) innehåller en lista över behörigheter som kan beviljas på API Management nivå.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nästa steg

Mer information om Role-Based Access Control i Azure finns i följande artiklar:
  * [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
  * [Tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)
  * [Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Åtgärder för resursprovidern Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
