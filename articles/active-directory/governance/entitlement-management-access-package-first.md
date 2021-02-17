---
title: Självstudie – hantera åtkomst till resurser i hantering av Azure AD-berättigande
description: Stegvis själv studie kurs om hur du skapar ditt första Access-paket med hjälp av Azure Portal i Azure Active Directory hantering av rättigheter.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbc653c8b698cb7b439b54b0a77d2b8cd4042a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547408"
---
# <a name="tutorial-manage-access-to-resources-in-azure-ad-entitlement-management"></a>Självstudie: hantera åtkomst till resurser i hantering av Azure AD-berättigande

Att hantera åtkomst till alla resurser som krävs av de anställda, till exempel grupper, program och platser, är en viktig funktion för organisationer. Du vill ge anställda den rätta åtkomst nivån som de behöver för att vara produktiv och ta bort deras åtkomst när den inte längre behövs.

I den här självstudien arbetar du för Sparbanken-bank som IT-administratör. Du har blivit ombedd att skapa ett paket med resurser för en marknadsförings kampanj som interna användare kan använda för självbetjänings förfrågan. Begär Anden kräver inte godkännande och användarens åtkomst upphör att gälla efter 30 dagar. I den här självstudien är marknadsförings kampanjens resurser bara medlemmar i en enda grupp, men det kan vara en samling av grupper, program eller SharePoint Online-webbplatser.

![Diagram som visar scenario översikten.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Access-paket med en grupp som en resurs
> * Tillåt en användare i din katalog att begära åtkomst
> * Visa hur en intern användare kan begära åtkomst paketet

En steg-för-steg-demonstration av processen att distribuera Azure Active Directory hantering av rättigheter, inklusive att skapa ditt första Access-paket, finns i följande videoklipp:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda hantering av Azure AD-rättigheter måste du ha en av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licens

Mer information finns i [licens krav](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Steg 1: konfigurera användare och grupp

En resurs katalog har en eller flera resurser att dela. I det här steget skapar du en grupp med namnet **marknadsförings resurser** i katalogen Sparbanken som är mål resursen för rättighets hantering. Du skapar också en intern begär Ande.

**Nödvändig roll:** Global administratör eller användar administratör

![Skapa användare och grupper](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör eller användar administratör.  

1. Klicka på **Azure Active Directory** i det vänstra navigerings fältet.

1. Skapa eller konfigurera följande två användare. Du kan använda dessa namn eller olika namn. **Admin1** kan vara den användare som du för närvarande är inloggad som.

    | Name | Katalogroll |
    | --- | --- |
    | **Admin1** | Global administratör<br/>\- eller -<br/>Användaradministratör |
    | **Requestor1** | User |

1. Skapa en Azure AD-säkerhetsgrupp med namnet **marknadsförings resurser** med en **tilldelad** medlemskaps typ.

    Den här gruppen är mål resursen för hantering av rättigheter. Gruppen ska vara tom för medlemmar att starta.

## <a name="step-2-create-an-access-package"></a>Steg 2: skapa ett Access-paket

Ett *Access-paket* är ett paket med resurser som ett team eller projekt behöver och som styrs av principer. Åtkomst paket definieras i behållare som kallas *kataloger*. I det här steget skapar du ett åtkomst paket för **marknadsförings kampanjen** i den **allmänna** katalogen.

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

![Skapa ett åtkomstpaket](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klicka på **Azure Active Directory** i Azure Portal i det vänstra navigerings fältet.

2. På den vänstra menyn klickar du på **identitets styrning**

3. Klicka på **åtkomst paket** på den vänstra menyn.  Om du ser **åtkomst nekad**, se till att det finns en Azure AD Premium P2-licens i din katalog.

4. Klicka på **nytt Access-paket**.

    ![Hantering av rättigheter i Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

5. På fliken **grundläggande** anger du åtkomst paketet för namn **marknadsförings kampanjen** och beskrivningen **åtkomst till resurser för kampanjen**.

6. Lämna List rutan **katalog** inställd på **Allmänt**.

    ![Nytt åtkomst paket – fliken grunder](./media/entitlement-management-access-package-first/basics.png)

7. Klicka på **Nästa** för att öppna fliken **resurs roller** .

    På den här fliken väljer du de resurser och resurs rollen som ska ingå i åtkomst paketet.

8. Klicka på **grupper och team**.

9. I fönstret Välj grupper letar du reda på och väljer gruppen **marknadsförings resurser** som du skapade tidigare.

     Som standard visas grupper i den allmänna katalogen. När du väljer en grupp utanför den allmänna katalogen, som du kan se om du markerar kryss rutan **Se alla** , kommer den att läggas till i den allmänna katalogen.

    ![Skärm bild som visar fliken "nytt åtkomst paket – resurs roller" och "Välj grupper".](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Klicka på **Välj** för att lägga till gruppen i listan.

11. I list rutan **roll** väljer du **medlem**.

    ![Nytt Access-paket – fliken resurs roller](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!IMPORTANT]
    >De roll tilldelnings grupper som läggs till i ett Access-paket anges med hjälp av den under typ **som kan tilldelas roller**. Se [skapa en roll tilldelnings bara grupp](../roles/groups-create-eligible.md) i Azure Active Directory för mer information om grupper som kan tilldelas till Azure AD-roller. Tänk på att när en grupp som kan tilldelas roll finns i en åtkomst paket katalog, kan administrativa användare som kan hantera hantering av rättigheter, inklusive globala administratörer, användar administratörer och katalog ägare i katalogen, kontrol lera åtkomst paketen i katalogen, så att de kan välja vem som kan läggas till i dessa grupper. Om du inte ser en roll tilldelnings bara grupp som du vill lägga till, eller om du inte kan lägga till den, kontrollerar du att du har den nödvändiga rollen för Azure AD-rollen och rättighets hanterings rollen för att utföra den här åtgärden. Du kan behöva be någon med de roller som krävs lägga till resursen i katalogen. Mer information finns i de [roller som krävs för att lägga till resurser i en katalog](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

    >[!NOTE]
    > När du använder [dynamiska grupper](../enterprise-users/groups-create-rule.md) kan du inte se andra roller som är tillgängliga förutom ägare. Det här är avsiktligt.
    > ![Översikt över scenario](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Klicka på **Nästa** för att öppna fliken **förfrågningar** .

    På den här fliken skapar du en princip för begäran. En *princip* definierar regler eller guardrails för åtkomst till ett Access-paket. Du skapar en princip som tillåter en speciell användare i resurs katalogen att begära det här åtkomst paketet.

13. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare i din katalog** och klickar sedan på **vissa användare och grupper**.

    ![Nytt åtkomst paket – fliken begär Anden](./media/entitlement-management-access-package-first/requests.png)

14. Klicka på **Lägg till användare och grupper**.

15. I fönstret Välj användare och grupper väljer du den **Requestor1** -användare som du skapade tidigare.

    ![Nytt åtkomst paket – fliken begär Anden – Välj användare och grupper](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Klicka på **Välj**.

17. Rulla ned till avsnitten **godkännande** och **Aktivera förfrågningar** .

18. Lämna **Kräv godkännande** inställt på **Nej**.

19. För **Aktivera begär Anden** klickar du på **Ja** för att aktivera det här åtkomst paketet så snart det skapas.

    ![Nytt åtkomst paket – fliken begär Anden om godkännande och aktivering](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Klicka på **Nästa** för att öppna fliken **livs cykel** .

21. I avsnittet **förfallo datum** kan du ange att **åtkomst paket tilldelningar ska gå ut** till **antalet dagar**.

22. Ange **tilldelningar upphör att gälla efter** **30** dagar.

    ![Nytt Access-paket – fliken livs cykel](./media/entitlement-management-access-package-first/lifecycle.png)

23. Klicka på **Nästa** för att öppna fliken **Granska + skapa** .

    ![Nytt åtkomst paket – Granska + fliken Skapa](./media/entitlement-management-access-package-first/review-create.png)

    Efter en liten stund bör du se ett meddelande om att Access-paketet har skapats.

24. I den vänstra menyn i marknadsförings kampanjens åtkomst paket klickar du på **Översikt**.

25. Kopiera **länken till min åtkomst Portal**.

    Du kommer att använda den här länken för nästa steg.

    ![Översikt över åtkomst paket – länken min åtkomst Portal](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Steg 3: begär åtkomst

I det här steget utför du stegen som den **interna begär ande** och begär åtkomst till åtkomst paketet. Begär Anden skickar sina förfrågningar med hjälp av en plats som kallas min åtkomst Portal. På portalen My Access kan du skicka begär Anden om åtkomst till paket, se de åtkomst paket som de redan har åtkomst till och visa sin begär ande historik.

**Nödvändig roll:** Intern begär Ande

1. Logga ut från Azure Portal.

1. I ett nytt webbläsarfönster navigerar du till länken min åtkomst portal som du kopierade i föregående steg.

1. Logga in på portalen My Access som **Requestor1**.

    Du bör se åtkomst paketet för **marknadsförings kampanjen** .

1. Om det behövs klickar du på pilen i kolumnen **Beskrivning** för att visa information om Access-paketet.

    ![Åtkomst till Portal – åtkomst paket](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicka på bock markeringen för att välja paketet.

1. Klicka på **begär åtkomst** för att öppna fönstret begär åtkomst.

    ![Min åtkomst Portal – knappen begär åtkomst](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. I rutan **affärs justering** skriver du den motivering **Jag arbetar med den nya marknadsförings kampanjen**.

    ![Min åtkomst Portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicka på **Skicka**.

1. På den vänstra menyn klickar du på **begär ande historik** för att kontrol lera att din begäran har skickats.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Steg 4: kontrol lera att åtkomst har tilldelats

I det här steget bekräftar du att den **interna begär ande** har tilldelats åtkomst paketet och att de nu är medlem i gruppen **marknadsförings resurser** .

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Logga ut från min åtkomst Portal.

1. Logga in på [Azure Portal](https://portal.azure.com) som **admin1**.

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. Klicka på **åtkomst paket** på den vänstra menyn.

1. Sök efter och klicka på **marknadsförings kampanjens** åtkomst paket.

1. I den vänstra menyn klickar du på **begär Anden**.

    Du bör se Requestor1 och den inledande principen med statusen **levererad**.

1. Klicka på begäran om du vill visa information om begäran.

    ![Åtkomst paket – information om begäran](./media/entitlement-management-access-package-first/request-details.png)

1. Klicka på **Azure Active Directory** i det vänstra navigerings fältet.

1. Klicka på **grupper** och öppna gruppen **marknadsförings resurser** .

1. Klicka på **medlemmar**.

    Du bör se **Requestor1** som visas som en medlem.

    ![Medlemmar i marknadsförings resurser](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Steg 5: Rensa resurser

I det här steget ska du ta bort de ändringar du gjort och ta bort åtkomst paketet för **marknadsförings kampanjen** .

**Nödvändig roll:**  Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Öppna **marknadsförings kampanjens** åtkomst paket.

1. Klicka på **tilldelningar**.

1. För **Requestor1** klickar du på ellipsen (**...**) och klickar sedan på **ta bort åtkomst**. I meddelandet som visas klickar du på **Ja**.

    Efter en liten stund ändras statusen från levererat till upphört att gälla.

1. Klicka på **resurs roller**.

1. För **marknadsförings resurser** klickar du på ellipsen (**...**) och klickar sedan på **ta bort resurs roll**. I meddelandet som visas klickar du på **Ja**.

1. Öppna listan med åtkomst paket.

1. För **marknadsförings kampanj** klickar du på ellipsen (**...**) och klickar sedan på **ta bort**. I meddelandet som visas klickar du på **Ja**.

1. I Azure Active Directory tar du bort alla användare som du har skapat, till exempel **Requestor1** och **admin1**.

1. Ta bort gruppen **marknadsförings resurser** .

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel och lär dig mer om vanliga scenario steg i hantering av rättigheter.
> [!div class="nextstepaction"]
> [Vanliga scenarier](entitlement-management-scenarios.md)
