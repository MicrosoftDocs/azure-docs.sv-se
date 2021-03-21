---
title: 'Snabb start: skapa en hanterings grupp med Portal'
description: I den här snabb starten använder du Azure Portal för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 5a336fe4caf5d2f825a93b5c7b8f8d05f6255c39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592440"
---
# <a name="quickstart-create-a-management-group"></a>Snabb start: skapa en hanterings grupp

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **all** hantering av tjänster och  >  **styrning**.

1. Välj **hanteringsgrupper**.

1. Välj **+ Lägg till hanterings grupp**.

   :::image type="content" source="./media/main.png" alt-text="Skärm bild av sidan hanterings grupper som visar underordnade hanterings grupper och prenumerationer." border="false":::

1. Lämna **Skapa ny** markerad och fyll i fältet hanterings grupp-ID.

   - **Hanterings gruppens ID** är katalogens unika identifierare som används för att skicka kommandon i den här hanterings gruppen. Den här identifieraren kan inte redige ras när den används i hela Azure-systemet för att identifiera den här gruppen. [Rot hanterings gruppen](./overview.md#root-management-group-for-each-directory) skapas automatiskt med ett ID som är Azure Active Directory-ID: t. Tilldela ett unikt ID för alla andra hanterings grupper.
   - Fältet visnings namn är det namn som visas i Azure Portal. Ett separat visnings namn är ett valfritt fält när du skapar hanterings gruppen och kan ändras när som helst.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Skärm bild av alternativen Lägg till hanterings grupp för att skapa en ny hanterings grupp.":::

1. Välj **Spara**.

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort en hanterings grupp som skapats:

1. Välj **all** hantering av tjänster och  >  **styrning**.

1. Välj **hanteringsgrupper**.

1. Leta upp den hanterings grupp som du skapade ovan, markera den och välj sedan **information** bredvid namnet.
   Välj sedan **ta bort** och bekräfta prompten.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)