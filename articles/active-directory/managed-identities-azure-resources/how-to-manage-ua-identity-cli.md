---
title: Hantera användar tilldelad hanterad identitet – Azure CLI – Azure AD
description: Stegvisa instruktioner om hur du skapar, listar och tar bort en användar tilldelad hanterad identitet med hjälp av Azure CLI.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784883"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Skapa, visa eller ta bort en användar tilldelad hanterad identitet med hjälp av Azure CLI


Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera mot tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, listar och tar bort en användar tilldelad hanterad identitet med hjälp av Azure CLI.

Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du se [Vad är hanterade identiteter för Azure-resurser?](overview.md). Mer information om system-tilldelade och användar tilldelade hanterade identitetstyper finns i [Hanterade identitetstyper.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Om du vill ändra användarbehörigheter när du använder ett huvudnamn för apptjänsten med hjälp av CLI måste du ange ytterligare behörigheter för tjänstens huvudnamn i Azure AD Graph API eftersom delar av CLI utför GET-begäranden mot Graph API. Annars kan du få meddelandet "Otillräcklig behörighet för att slutföra åtgärden". Om du vill göra detta måste du gå till Appregistrering i Azure Active Directory, välja din app, klicka på API-behörigheter, rulla nedåt och välja Azure Active Directory Graph. Därifrån väljer du Programbehörigheter och lägger sedan till lämpliga behörigheter. 

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Om du vill skapa en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Använd kommandot [az identity create](/cli/azure/identity#az_identity_create) för att skapa en användar tilldelad hanterad identitet. Parametern anger resursgruppen där den användar tilldelade hanterade identiteten ska `-g` skapas, och `-n` parametern anger dess namn. Ersätt `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` parametervärdena och med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Visa en lista över användar tilldelade hanterade identiteter

Om du vill visa/läsa en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen](../../role-based-access-control/built-in-roles.md#managed-identity-operator) Hanterad identitetsoperatör [eller Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Om du vill visa en lista över användar tilldelade hanterade identiteter använder du [kommandot az identity list.](/cli/azure/identity#az_identity_list) Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

I json-svaret har användar tilldelade hanterade identiteter värdet `"Microsoft.ManagedIdentity/userAssignedIdentities"` som returneras för nyckeln, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användar tilldelad hanterad identitet

Om du vill ta bort en användar tilldelad hanterad identitet måste ditt konto ha [rolltilldelningen Hanterad](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) identitetsdeltagare.

Om du vill ta bort en användar tilldelad hanterad identitet använder du [kommandot az identity delete.](/cli/azure/identity#az_identity_delete)  Parametern -n anger dess namn och parametern -g anger den resursgrupp där den användar tilldelade hanterade identiteten skapades. Ersätt `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` parametervärdena och med dina egna värden:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Om du tar bort en användar tilldelad hanterad identitet tas referensen inte bort från alla resurser som den har tilldelats till. Ta bort dem från VM/VMSS med `az vm/vmss identity remove` kommandot

## <a name="next-steps"></a>Nästa steg

En fullständig lista över Azure CLI-identitetskommandon finns i [az identity](/cli/azure/identity).

Information om hur du tilldelar en användar tilldelad hanterad identitet till en virtuell Azure-dator finns i Konfigurera hanterade identiteter för Azure-resurser på en virtuell [Azure-dator med Hjälp](qs-configure-cli-windows-vm.md#user-assigned-managed-identity) av Azure CLI
