---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80132220"
---
Om en användare inte har den åtkomst som krävs för att tillämpa taggar, kan du **tilldela rollen till** användaren. Mer information finns i [Självstudier: ge en användare åtkomst till Azure-resurser med RBAC och Azure Portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Om du vill visa taggarna för en resurs eller en resurs grupp letar du efter befintliga taggar i översikten. Om du inte tidigare har tillämpar taggar är listan tom.

   ![Visa taggar för resurs eller resurs grupp](./media/resource-manager-tag-resources/view-tags.png)

1. Om du vill lägga till en tagg väljer **du klicka här om du vill lägga till taggar**.

1. Ange ett namn och ett värde.

   ![Lägg till tagg](./media/resource-manager-tag-resources/add-tag.png)

1. Fortsätt att lägga till taggar efter behov. Välj **Spara** när du är klar.

   ![Spara Taggar](./media/resource-manager-tag-resources/save-tags.png)

1. Taggarna visas nu i översikten.

   ![Visa Taggar](./media/resource-manager-tag-resources/view-new-tags.png)

1. Om du vill lägga till eller ta bort en tagg väljer du **ändra**.

1. Om du vill ta bort en tagg väljer du pappers korgs ikonen. Välj sedan **Spara**.

   ![Ta bort tagg](./media/resource-manager-tag-resources/delete-tag.png)

För att tilldela taggar till flera resurser:

1. I en lista över resurser markerar du kryss rutan för de resurser som du vill tilldela taggen. Välj sedan **tilldela Taggar**.

   ![Välj flera resurser](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Lägg till namn och värden. Välj **Spara** när du är klar.

   ![Välj tilldela](./media/resource-manager-tag-resources/select-assign.png)

Visa alla resurser med en tagg:

1. Sök efter **taggar** på Azure Portal-menyn. Välj den från de tillgängliga alternativen.

   ![Sök efter tagg](./media/resource-manager-tag-resources/find-tags-general.png)

1. Välj taggen för att visa resurser.

   ![Välj tagg](./media/resource-manager-tag-resources/select-tag.png)

1. Alla resurser med den taggen visas.

   ![Visa resurser efter tagg](./media/resource-manager-tag-resources/view-resources-by-tag.png)
