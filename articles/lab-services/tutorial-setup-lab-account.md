---
title: Konfigurera ett labbkonto med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb konto med Azure Lab Services, lägger till en labb skapare och anger Marketplace-avbildningar som ska användas av labb i labb kontot.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445074"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Självstudie: Konfigurera ett labbkonto med Azure Lab Services
I Azure Lab Services, fungerar ett labbkonto som det centrala kontot där alla din organisations labbar hanteras. I ditt labbkonto, ger du behörighet till andra att skapa labbar och konfigurera principer som gäller för alla labbar under labbkontot. I den här självstudien får du lära dig hur du skapar ett labb konto. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett labbkonto
> * Lägga till en användare i rollen Labbskapare

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **DevOps** från **Kategorier**. Välj sedan **labb tjänster**. Om du väljer Star ( `*` ) bredvid **labb tjänster** läggs den till i avsnittet **Favoriter** på den vänstra menyn. I nästa steg kan du välja **labb tjänster** under **Favoriter**.

    ![Alla tjänster – > Lab-tjänster](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På sidan **labb tjänster** väljer du **Lägg till** i verktygsfältet eller så väljer du knappen **Skapa labb konto** på sidan. 

    ![Välj Lägg till på sidan Labbkonton](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. På fliken **grundläggande** på sidan **skapa ett labb konto** gör du följande: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. För **resurs grupp** väljer du en befintlig resurs grupp eller väljer **Skapa ny**, och anger ett namn för resurs gruppen.
    4. För **plats** väljer du en plats/region där du vill skapa labb kontot. 

        ![Labb konto – sidan grunder](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Välj **Granska + skapa**.
    6. Granska sammanfattningen och välj **skapa**. 

        ![Granska + skapa – > skapa](./media/tutorial-setup-lab-account/create-button.png)    
5. När distributionen är klar expanderar du **Nästa steg** och väljer **gå till resurs**. 

    ![Sidan gå till labb konto](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Bekräfta att du ser sidan **labb konto** . 

    ![Sidan Labbkonto](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Lägga till en användare i rollen Labbskapare
Om du vill konfigurera ett klassrumslabb i ett labbkonto måste användaren vara medlem i rollen **Lab Creator** i labbkontot. Ge lärarna behörighet att skapa labb åt sina klasser genom att lägga till dem i rollen **Labbskapare**:

> [!NOTE]
> Det konto som du använde för att skapa labbkontot läggs automatiskt till i den här rollen. Om du planerar att använda samma användar konto för att skapa ett klass rums labb i den här självstudien hoppar du över det här steget. 

1. På sidan **Labbkonto** väljer du **Åtkomstkontroll (IAM)** följt av **+ Lägg till** och sedan **+ Lägg till rolltilldelning** i verktygsfältet. 

    ![Åtkomstkontroll -> Lägg till rolltilldelning, knapp](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. På sidan **Lägg till rolltilldelning** väljer du **Labbskapare** som **Roll**. Välj den användare som du vill lägga till rollen Labbskapare och välj **Spara**. 

    ![Lägg till labbuppgiftsförfattare](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du ett labbkonto. Om du vill veta mer om hur du skapar ett klass rums labb som en lärare går du vidare till nästa självstudie:

> [!div class="nextstepaction"]
> [Konfigurera ett klassrumslabb](tutorial-setup-classroom-lab.md)

