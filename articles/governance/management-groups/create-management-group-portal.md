---
title: 'Snabbstart: Skapa en hanteringsgrupp med portalen'
description: I den här snabbstarten använder du Azure Portal för att skapa en hanteringsgrupp för att organisera dina resurser i en resurshierarki.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 1b0f7b0d98b2cde1343325d5e4a2979e3e663b68
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535905"
---
# <a name="quickstart-create-a-management-group"></a>Snabbstart: Skapa en hanteringsgrupp

Hanteringsgrupper är containrar som hjälper dig att hantera åtkomst, policyer och efterlevnad för flera prenumerationer. Skapa dessa containrar för att skapa en effektiv hierarki som kan användas [med Azure Policy](../policy/overview.md) rollbaserade [åtkomstkontroller i Azure.](../../role-based-access-control/overview.md) Mer information om hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](overview.md)

Den första hanteringsgruppen som skapades i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs för första gången för att konfigurera tjänsten för hanteringsgrupper i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i inledande [konfiguration av hanteringsgrupper.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Alla Azure AD-användare i klientorganisationen kan skapa en hanteringsgrupp utan den tilldelade skrivbehörigheten för hanteringsgruppen om [hierarkiskydd](./how-to/protect-resource-hierarchy.md#setting---require-authorization) inte är aktiverat. Den här nya hanteringsgruppen blir underordnad rothanteringsgruppen eller standardhanteringsgruppen och skaparen får rolltilldelningen "Ägare". [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) Med hanteringsgrupptjänsten kan du göra detta så att rolltilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rothanteringsgruppen när den skapas. För att undvika att hitta de globala Azure AD-administratörerna för att börja använda hanteringsgrupper kan du skapa de första hanteringsgrupperna på rotnivå.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **Alla tjänster** Hantering +  >  **styrning.**

1. Välj **Hanteringsgrupper**.

1. Välj **+ Lägg till hanteringsgrupp.**

   :::image type="content" source="./media/main.png" alt-text="Skärmbild av sidan Hanteringsgrupper som visar underordnade hanteringsgrupper och prenumerationer.":::

1. Låt **Skapa ny vara** markerat och fyll i fältet hanteringsgrupp-ID.

   - **Id:t för hanteringsgruppen** är den unika katalogidentifierare som används för att skicka kommandon till den här hanteringsgruppen. Den här identifieraren kan inte redigeras efter skapandet eftersom den används i hela Azure-systemet för att identifiera den här gruppen. [Rothanteringsgruppen skapas](./overview.md#root-management-group-for-each-directory) automatiskt med ett ID som är det Azure Active Directory ID:t. För alla andra hanteringsgrupper tilldelar du ett unikt ID.
   - Visningsnamnsfältet är det namn som visas i Azure Portal. Ett separat visningsnamn är ett valfritt fält när du skapar hanteringsgruppen och kan ändras när som helst.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Skärmbild av alternativen för att lägga till hanteringsgrupp för att skapa en ny hanteringsgrupp.":::

1. Välj **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort den skapade hanteringsgruppen:

1. Välj **Alla tjänster** Hantering +  >  **styrning.**

1. Välj **Hanteringsgrupper**.

1. Leta upp hanteringsgruppen som skapades ovan, välj den och **välj** sedan Information bredvid namnet.
   Välj sedan **Ta bort** och bekräfta uppmaningen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en hanteringsgrupp för att organisera resurshierarkin. Hanteringsgruppen kan innehålla prenumerationer eller andra hanteringsgrupper.

Om du vill veta mer om hanteringsgrupper och hur du hanterar din resurshierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanteringsgrupper](./manage.md)
