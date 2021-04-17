---
title: 'Snabbstart: Skapa en hanteringsgrupp med Python'
description: I den här snabbstarten använder du Python för att skapa en hanteringsgrupp för att organisera dina resurser i en resurshierarki.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9aec47e067ca62f4902df2dafb6a5d6d50a26d0e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533166"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Snabbstart: Skapa en hanteringsgrupp med Python

Hanteringsgrupper är containrar som hjälper dig att hantera åtkomst, policyer och efterlevnad i flera prenumerationer. Skapa dessa containrar för att skapa en effektiv hierarki som kan användas [med Azure Policy](../policy/overview.md) och [Rollbaserade åtkomstkontroller i Azure.](../../role-based-access-control/overview.md) Mer information om hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](overview.md)

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera tjänsten för hanteringsgrupper i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i inledande [konfiguration av hanteringsgrupper.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Alla Azure AD-användare i klientorganisationen kan skapa en hanteringsgrupp utan den skrivbehörighet för hanteringsgruppen som tilldelats användaren om [hierarkiskydd](./how-to/protect-resource-hierarchy.md#setting---require-authorization) inte är aktiverat. Den här nya hanteringsgruppen blir underordnad rothanteringsgruppen eller standardhanteringsgruppen och skaparen får rolltilldelningen "Ägare". [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) Med hanteringsgrupptjänsten kan du göra det så att rolltilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rothanteringsgruppen när den skapas. För att undvika att hitta de globala Azure AD-administratörerna för att börja använda hanteringsgrupper kan vi skapa de första hanteringsgrupperna på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Lägga till Resource Graph bibliotek

Om du vill göra det möjligt för Python att hantera hanteringsgrupper måste biblioteket läggas till. Det här biblioteket fungerar överallt där Python kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10) eller lokalt installerade.

1. Kontrollera att den senaste Python-versionen är installerad (minst **3.8**). Om det inte har installerats än laddar du ned det på [Python.org](https://www.python.org/downloads/).

1. Kontrollera att den senaste versionen av Azure CLI är installerad (minst **2.5.1**). Om det inte har installerats än kan du [gå till Installera Azure CLI.](/cli/azure/install-azure-cli)

   > [!NOTE]
   > Azure CLI krävs för att Python ska kunna använda **CLI-baserad autentisering** i följande exempel. Information om andra alternativ finns i Autentisera [med hjälp av Azure-hanteringsbibliotek för Python.](/azure/developer/python/azure-sdk-authenticate)

1. Autentisera via Azure CLI.

   ```azurecli
   az login
   ```

1. Installera de bibliotek som krävs för hanteringsgrupper i valfri Python-miljö:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Om Python är installerat för alla användare måste dessa kommandon köras från en upphöjd konsol.

1. Kontrollera att biblioteken har installerats. `azure-mgmt-managementgroups` bör vara **0.2.0** eller högre, ska vara `azure-mgmt-resource` **9.0.0** eller högre och ska vara `azure-cli-core` **2.5.0** eller högre.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Skapa hanteringsgruppen

1. Skapa Python-skriptet och spara följande källa som `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Autentisera med Azure CLI med `az login` .

1. Ange följande kommando i terminalen:

   ```bash
   py mgCreate.py
   ```

Resultatet av att skapa hanteringsgruppen är utdata till konsolen som ett `LROPoller` -objekt.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade biblioteken från Python-miljön kan du göra det med hjälp av följande kommando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en hanteringsgrupp för att organisera resurshierarkin. Hanteringsgruppen kan innehålla prenumerationer eller andra hanteringsgrupper.

Om du vill veta mer om hanteringsgrupper och hur du hanterar din resurshierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanteringsgrupper](./manage.md)
