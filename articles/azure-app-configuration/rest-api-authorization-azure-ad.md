---
title: Azure App konfiguration REST API-Azure Active Directory auktorisering
description: Använd Azure Active Directory för auktorisering mot Azure App-konfiguration med hjälp av REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092800"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory Authorization-REST API referens

När du använder Azure Active Directory (Azure AD)-autentisering hanteras auktorisering av rollbaserad åtkomst kontroll (RBAC). RBAC kräver att användare tilldelas roller för att kunna bevilja åtkomst till resurser. Varje roll innehåller en uppsättning åtgärder som användare som tilldelats rollen kan utföra.

## <a name="roles"></a>Roller

Följande roller är tillgängliga i Azure-prenumerationer som standard:

- **Azure App ägare av konfigurations data**: den här rollen ger fullständig åtkomst till alla åtgärder.
- **Azure App konfigurations data läsare**: den här rollen aktiverar Läs åtgärder.

## <a name="actions"></a>Åtgärder

Roller innehåller en lista med åtgärder som användare som tilldelats rollen kan utföra. Azure App konfiguration stöder följande åtgärder:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Den här åtgärden tillåter Läs åtkomst till konfigurations nyckel värde resurser, till exempel/kv och/Labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Den här åtgärden tillåter skriv åtkomst till konfigurations nyckel värde resurser för appar.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Den här åtgärden tillåter att konfigurations nyckel resurser för appar tas bort. Observera att om du tar bort en resurs returneras det nyckel värde som har tagits bort.

## <a name="error"></a>Fel

```http
HTTP/1.1 403 Forbidden
```

**Orsak:** Huvudobjektet som gör begäran har inte de behörigheter som krävs för att utföra den begärda åtgärden.
**Lösning:** Tilldela den roll som krävs för att utföra den begärda åtgärden till det huvud konto som gör begäran.

## <a name="managing-role-assignments"></a>Hantera roll tilldelningar

Du kan hantera roll tilldelningar med hjälp av [Azure RBAC-procedurer](../role-based-access-control/overview.md) som är standard i alla Azure-tjänster. Du kan göra detta via Azure CLI, PowerShell och Azure Portal. Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).