---
title: 'Snabb start: skapa en hanterings grupp med REST API'
description: I den här snabb starten använder du REST API för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ff1487bf25945c733402ddb74d1e102bea80b4b1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592508"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>Snabb start: skapa en hanterings grupp med REST API

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Om du inte redan har gjort det installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett verktyg som skickar HTTP-förfrågningar till Azure Resource Manager-baserade REST API: er. I stället kan du använda funktionen "Testa funktionen" i REST-dokumentationen eller verktyg som PowerShell: s [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) eller [Postman](https://www.postman.com).

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>Skapa i REST API

För REST API använder du [hanteringsgrupper-skapa eller uppdatera](/rest/api/resources/managementgroups/createorupdate) slut punkten för att skapa en ny hanterings grupp. I det här **exemplet är** hanterings gruppens kundexempel _contoso_.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Ingen begär ande text

Det **här** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till egenskapen **Properties. DisplayName** i begär ande texten. Om du till exempel vill skapa en hanterings grupp med **hjälp av Kundanvisningar för** _contoso_ och visnings namnet _contoso-gruppen_, använder du följande slut punkt och begär ande text:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Använd egenskapen **Properties.parent.ID** för att ange en annan hanterings grupp som överordnad.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Begärandetext

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort en hanterings grupp som skapats ovan använder du slut punkten [hanteringsgrupper-Delete](/rest/api/resources/managementgroups/delete) :

- REST API-URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- Ingen begär ande text

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)