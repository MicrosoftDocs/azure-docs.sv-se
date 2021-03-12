---
title: 'Hitta identitets objekt-ID: n för autentisering – Azure API för FHIR'
description: 'Den här artikeln förklarar hur du hittar identitets objekt-ID: n som krävs för att konfigurera autentisering för Azure API för FHIR'
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 706d7e081743f2bab1f593e00dc792f218a000ea
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019601"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Hitta identitets objekt-ID: n för konfiguration av autentisering

I den här artikeln får du lära dig hur du hittar identitets objekt-ID: n som krävs när du konfigurerar Azure-API: t för FHIR att [använda en extern eller sekundär Active Directory-klient](configure-local-rbac.md) för data planet.

## <a name="find-user-object-id"></a>Hitta användar objekt-ID

Om du har en användare med användar namn `myuser@contoso.com` kan du hitta användarna `ObjectId` med hjälp av följande PowerShell-kommando:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@contoso.com'").ObjectId
```

Du kan också använda Azure CLI:

```azurecli-interactive
az ad user show --id myuser@contoso.com --query objectId --out tsv
```

## <a name="find-service-principal-object-id"></a>Hitta objekt-ID för tjänstens huvud namn

Anta att du har registrerat en [tjänst klient app](register-service-azure-ad-client-app.md) och vill tillåta att den här tjänst klienten får åtkomst till Azure-API: t för FHIR. du hittar objekt-ID: t för klient tjänstens huvud namn med följande PowerShell-kommando:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

där `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` är tjänstens klient program-ID. Alternativt kan du använda `DisplayName` -tjänst klienten:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

Om du använder Azure CLI kan du använda:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX --query objectId --out tsv
```

## <a name="find-a-security-group-object-id"></a>Hitta ett objekt-ID för säkerhets grupp

Om du vill hitta objekt-ID: t för en säkerhets grupp kan du använda följande PowerShell-kommando:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Där `mygroup` är namnet på den grupp som du är intresse rad av.

Om du använder Azure CLI kan du använda:

```azurecli-interactive
az ad group show --group "mygroup" --query objectId --out tsv
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du hittar identitets objekt-ID: n som krävs för att konfigurera Azure API för FHIR att använda en extern eller sekundär Azure Active Directory klient. Läs vidare om hur du använder objekt-ID: n för att konfigurera lokala RBAC-inställningar:
 
>[!div class="nextstepaction"]
>[Konfigurera lokala RBAC-inställningar](configure-local-rbac.md)
