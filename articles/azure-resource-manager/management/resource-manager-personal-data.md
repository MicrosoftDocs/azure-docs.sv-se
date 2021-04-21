---
title: Personuppgifter
description: Lär dig hur du hanterar personliga data som är Azure Resource Manager åtgärder.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 9087d3e46f38aab3de7774ea341ebd9cbc2d7d1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785963"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Hantera personliga data som är associerade med Azure Resource Manager

För att undvika att känslig information exponeras kan du ta bort all personlig information som du har angett i distributioner, resursgrupper eller taggar. Azure Resource Manager tillhandahåller åtgärder som gör att du kan hantera personliga data som du kan ha angett i distributioner, resursgrupper eller taggar.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Ta bort personliga data i distributionshistoriken

För distributioner Resource Manager kvar parametervärden och statusmeddelanden i distributionshistoriken. Dessa värden bevaras tills du tar bort distributionen från historiken. Om du vill se om du har angett personliga data i dessa värden visar du en lista över distributionerna. Om du hittar personliga data tar du bort distributionerna från historiken.

Om du **vill visa en lista** över distributioner i historiken använder du:

* [Lista efter resursgrupp](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

Om du **vill ta bort distributioner** från historiken använder du:

* [Ta bort](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Ta bort personliga data i resursgruppnamn

Namnet på resursgruppen finns kvar tills du tar bort resursgruppen. Om du vill se om du har angett personliga data i namnen listar du resursgrupperna. Om du hittar personuppgifter flyttar [du resurserna till](move-resource-group-and-subscription.md) en ny resursgrupp och tar bort resursgruppen med personliga data i namnet.

Om du vill **visa en lista över resursgrupper** använder du:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az_group_list)

Om du vill **ta bort resursgrupper** använder du:

* [Ta bort](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az_group_delete)

## <a name="delete-personal-data-in-tags"></a>Ta bort personliga data i taggar

Taggnamn och värden bevaras tills du tar bort eller ändrar taggen. Om du vill se om du har angett personliga data i taggarna listar du taggarna. Om du hittar personliga data tar du bort taggarna.

Om du vill **visa en** lista över taggar använder du:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az_tag_list)

Om du vill **ta bort** taggar använder du:

* [Ta bort](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az_tag_delete)

## <a name="next-steps"></a>Nästa steg
* En översikt över Azure Resource Manager finns i [avsnittet Vad är Resource Manager?](overview.md)