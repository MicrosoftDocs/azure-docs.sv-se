---
title: Så här hittar du ditt klient-ID – Azure Active Directory
description: Anvisningar om hur du hittar och Azure Active Directory klientorganisations-ID till en befintlig Azure-prenumeration.
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
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764359"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Så här hittar du ditt Azure Active Directory-ID

Azure-prenumerationer har en förtroenderelation med Azure Active Directory (Azure AD). Azure AD är betrott för att autentisera användare, tjänster och enheter för prenumerationen. Varje prenumeration har ett associerat klient-ID och det finns några olika sätt att hitta klientorganisations-ID:t för din prenumeration.

## <a name="find-tenant-id-through-the-azure-portal"></a>Hitta klientorganisations-ID via Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
 
1. Välj **Azure Active Directory**.

1. Välj **Egenskaper**.

1. Rulla sedan ned till fältet **Klientorganisations-ID.** Ditt klientorganisations-ID finns i rutan.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory – Egenskaper – Klientorganisations-ID – fältet Klientorganisations-ID":::

## <a name="find-tenant-id-with-powershell"></a>Hitta klientorganisations-ID med PowerShell

Du kan också hitta klientorganisationen programmatiskt. Använd cmdleten för att Azure PowerShell klientorganisations-ID:t. `Get-AzTenant`

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Mer information finns i den här Azure PowerShell cmdlet-referensen [för Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Hitta klientorganisations-ID med CLI
Om du vill använda ett kommandoradsgränssnitt för att hitta klient-ID:t kan du göra det med [Azure CLI](/cli/azure/install-azure-cli) eller [Microsoft 365 CLI.](https://pnp.github.io/cli-microsoft365/) 

För Azure CLI använder du något av kommandona **az login**, az **account list** eller az account **tenant list** enligt följande exempel. Observera egenskapen **tenantId** för var och en av dina prenumerationer i utdata från varje kommando.

```azurecli-interactive
az login
az account list
az account tenant list
```

Mer information finns i [az login command](/cli/azure/reference-index#az_login) reference, az [account](/cli/azure/ext/account/account) command reference eller az [account tenant](/cli/azure/ext/account/account/tenant) command reference.


För Microsoft 365 CLI använder du **cmdlet-klientorganisations-ID:t** som visas i följande exempel:
 
```cli
m365 tenant id get
```

Mer information finns i hämta kommandoreferensen Microsoft 365 [klientorganisations-ID.](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/)


## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure [AD-klient finns i Snabbstart: Skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Information om hur du associerar eller lägger till en prenumeration till en klientorganisation finns i Associera eller lägga till en [Azure-prenumeration till din Azure Active Directory klientorganisation.](active-directory-how-subscriptions-associated-directory.md)

- Information om hur du hittar objekt-ID finns i [Hitta användarobjektets ID.](/partner-center/find-ids-and-domain-names#find-the-user-object-id)
