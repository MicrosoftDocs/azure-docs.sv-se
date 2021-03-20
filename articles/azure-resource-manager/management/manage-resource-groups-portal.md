---
title: Hantera resurs grupper – Azure Portal
description: Använd Azure Portal för att hantera dina resurs grupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resurs grupper.
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093696"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Hantera Azure Resource Manager resurs grupper med hjälp av Azure Portal

Lär dig hur du använder [Azure Portal](https://portal.azure.com) med [Azure Resource Manager](overview.md) för att hantera Azures resurs grupper. Information om hur du hanterar Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure Portal](manage-resources-portal.md).

Andra artiklar om att hantera resurs grupper:

- [Hantera Azure-resurs grupper med hjälp av Azure CLI](manage-resources-cli.md)
- [Hantera Azure-resurs grupper med hjälp av Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Vad är en resurs grupp?

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg vanligt vis till resurser som delar samma livs cykel i samma resurs grupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att dina data lagras inom en viss region.


## <a name="create-resource-groups"></a>Skapa resurs grupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **resurs grupper**

    ![Lägg till resurs grupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Välj **Lägg till**.
4. Ange följande värden:

   - **Prenumeration**: Välj din Azure-prenumeration. 
   - **Resurs grupp**: Ange ett nytt resurs grupps namn. 
   - **Region**: Välj en Azure-plats, till exempel **Central USA**.

     ![Skapa resurs grupp](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Välj **Granska + skapa**
6. Välj **Skapa**. Det tar några sekunder att skapa en resurs grupp.
7. Välj **Uppdatera** på den översta menyn för att uppdatera resurs grupps listan och välj sedan den nya resurs gruppen som har skapats för att öppna den. Eller Välj **meddelande**(klock ikonen) ovanifrån och välj sedan **gå till resurs grupp** för att öppna den nyligen skapade resurs gruppen

    ![Gå till resurs grupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista resurs grupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Om du vill visa en lista över resurs grupper väljer du **resurs grupper**

    ![Bläddra bland resurs grupper](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Om du vill anpassa informationen som visas för resurs grupperna väljer du **Redigera kolumner**. Följande skärm bild visar de ytterligare kolumner som du kan lägga till i visningen:

## <a name="open-resource-groups"></a>Öppna resurs grupper

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Resursgrupper**.
3. Välj den resurs grupp som du vill öppna.

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

1. Öppna den resurs grupp som du vill ta bort.  Se [Öppna resurs grupper](#open-resource-groups).
2. Välj **Ta bort resursgrupp**.

    ![ta bort Azure-resurs grupp](./media/manage-resource-groups-portal/delete-group.png)

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](delete-resource-group.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resurs grupp

När du har skapat en Resource Manager-mall kan du använda Azure Portal för att distribuera dina Azure-resurser. Information om hur du skapar en mall finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../templates/quickstart-create-templates-use-the-portal.md). Information om hur du distribuerar en mall med hjälp av portalen finns i [distribuera resurser med Resource Manager-mallar och Azure Portal](../templates/deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resurs grupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resurs grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Lås resurs grupper

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs. 

1. Öppna den resurs grupp som du vill låsa.  Se [Öppna resurs grupper](#open-resource-groups).
2. I det vänstra fönstret väljer du **Lås**.
3. Om du vill lägga till ett lås i resurs gruppen väljer du **Lägg till**.
4. Ange **Lås namn**, **Lås typ** och **anteckningar**. Lås typerna är **skrivskyddade** och **ta bort**.

    ![Lås Azure-resurs grupp](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Mer information finns i [låsa resurser för att förhindra oväntade ändringar](lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resurs grupper

Du kan använda taggar för resurs grupper och resurser för att logiskt organisera dina till gångar. Mer information finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportera resurs grupper till mallar

Information om hur du exporterar mallar finns i [en och flera-resurs-export till mall-portalen](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resurs grupper

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) är hur du hanterar åtkomst till resurser i Azure. Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](../templates/template-syntax.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](../index.yml).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).
