---
title: 'Snabb start: skapa en hanterings grupp med Azure CLI'
description: I den här snabb starten använder du Azure CLI för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe38882bd3b025635662e228ae919a24b03dee78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592474"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>Snabb start: skapa en hanterings grupp med Azure CLI

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Den här snabb starten kräver att du kör Azure CLI version 2.0.76 eller senare för att installera och använda CLI lokalt. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>Skapa i Azure CLI

För Azure CLI använder du kommandot [AZ Account Management-Group Create](/cli/azure/account/management-group#az_account_management_group_create) för att skapa en ny hanterings grupp. I det här exemplet är hanterings gruppens **namn** _contoso_.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**Namnet** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till **visnings namn** parametern. Om du till exempel vill skapa en hanterings grupp med GroupName för Contoso och visnings namnet "contoso Group", använder du följande kommando:

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Om du vill ange en annan hanterings grupp som överordnad använder du den **överordnade** parametern och anger namnet på den överordnade gruppen.

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort en hanterings grupp som skapats ovan använder du kommandot [AZ Account Management-Group Delete](/cli/azure/account/management-group#az_account_management_group_delete) :

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)