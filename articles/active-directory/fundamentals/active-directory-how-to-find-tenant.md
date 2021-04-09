---
title: 'Så här hittar du klient-ID: t – Azure Active Directory'
description: 'Anvisningar om hur du hittar och Azure Active Directory klient-ID: t till en befintlig Azure-prenumeration.'
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96326598"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Så här hittar du Azure Active Directory klient-ID

Azure-prenumerationer har en förtroende relation med Azure Active Directory (Azure AD). Azure AD är betrott för att autentisera användare, tjänster och enheter för prenumerationen. Varje prenumeration har ett klient-ID som är associerat med den och det finns några sätt som du kan hitta klient-ID: t för din prenumeration.

## <a name="find-tenant-id-through-the-azure-portal"></a>Hitta klient-ID via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
 
1. Välj **Azure Active Directory**.

1. Välj **Egenskaper**.

1. Bläddra sedan ned till fältet **klient-ID** . Ditt klient-ID visas i rutan.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-Tenant ID-fältet klient-ID":::

## <a name="find-tenant-id-with-powershell"></a>Hitta klient-ID med PowerShell

Du kan också hitta klienten program mässigt. Använd cmdleten för att hitta klient-ID: t med Azure PowerShell `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Mer information finns i denna Azure PowerShell cmdlet-referens för [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Hitta klient-ID med CLI
Om du vill använda ett kommando rads gränssnitt för att hitta klient-ID: t kan du göra det med [Azure CLI](/cli/azure/install-azure-cli) eller [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/). 

För Azure CLI använder du en av kommandona **AZ login**, **AZ Account List** eller **AZ Account List** (Visa i följande exempel). Observera egenskapen **tenantId** för var och en av dina prenumerationer i utdata från varje kommando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Mer information finns i kommando referens för [AZ-inloggning](/cli/azure/reference-index#az_login) , referens för [AZ-konto](/cli/azure/ext/account/account) eller kommando referens för [AZ-konto klient](/cli/azure/ext/account/account/tenant) .


För Microsoft 365 CLI använder du cmdlet **-klient-ID: t** som visas i följande exempel:
 
```cli
m365 tenant id get
```

Mer information finns i kommando referensen Microsoft 365 [klient-ID get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) .


## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure AD-klient finns i [snabb start: skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Information om hur du associerar eller lägger till en prenumeration på en klient organisation finns i [associera eller lägga till en Azure-prenumeration i Azure Active Directory klient organisationen](active-directory-how-subscriptions-associated-directory.md).

- Information om hur du hittar objekt-ID finns i [hitta användar objekt-ID](/partner-center/find-ids-and-domain-names#find-the-user-object-id).