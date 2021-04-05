---
title: Personuppgifter
description: Lär dig hur du hanterar person uppgifter som är associerade med Azure Resource Manager åtgärder.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 1e531f7cd9992536bcc191637111761c5bbdefa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693693"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Hantera personliga data som är associerade med Azure Resource Manager

Undvik att exponera känslig information genom att ta bort all personlig information som du kan ha tillhandahållit i distributioner, resurs grupper eller taggar. Azure Resource Manager tillhandahåller åtgärder som låter dig hantera personliga data som du kan ha tillhandahållit i distributioner, resurs grupper eller taggar.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Ta bort personliga data i distributions historik

Vid distributioner behåller Resource Manager parameter värden och status meddelanden i distributions historiken. Dessa värden behålls tills du tar bort distributionen från historiken. Om du vill se om du har angett personliga data i dessa värden, visar du distributionerna. Om du hittar personliga data tar du bort distributionerna från historiken.

Om du vill visa en lista över **distributioner** i historiken använder du:

* [Lista efter resurs grupp](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [AZ distributions grupp lista](/cli/azure/deployment/group#az_deployment_group_list)

Om du vill ta bort **distributioner** från historiken använder du:

* [Ta bort](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [ta bort AZ distributions grupp](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Ta bort personliga data i resurs grupp namn

Namnet på resurs gruppen finns kvar tills du tar bort resurs gruppen. Om du vill se om du har angett personliga data i namnen, visar du resurs grupperna. Om du hittar personliga data [flyttar du resurserna](move-resource-group-and-subscription.md) till en ny resurs grupp och tar bort resurs gruppen med personliga data i namnet.

Om du vill visa **resurs grupper** använder du:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [lista över AZ-grupper](/cli/azure/group#az-group-list)

Om du vill ta bort **resurs grupper** använder du:

* [Ta bort](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Ta bort personliga data i Taggar

Taggarnas namn och värden finns kvar tills du tar bort eller ändrar taggen. Om du vill se om du har angett personliga data i taggarna visar du taggarna. Ta bort taggarna om du hittar personliga data.

Om du vill visa **taggar** använder du:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [AZ tag-lista](/cli/azure/tag#az-tag-list)

Om du vill ta bort **taggar** använder du:

* [Ta bort](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [ta bort AZ-tagg](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Nästa steg
* En översikt över Azure Resource Manager finns i [Vad är Resource Manager?](overview.md)