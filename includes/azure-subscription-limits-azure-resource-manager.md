---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799887"
---
| Resurs | Gräns |
| --- | --- |
| Prenumerationer [som är associerade med en Azure Active Directory klientorganisation](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Obegränsat |
| [Medadministratörer](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per prenumeration |Obegränsat |
| [Resursgrupper](../articles/azure-resource-manager/management/overview.md) per prenumeration |980 |
| Azure Resource Manager storlek för API-begäran |4 194 304 byte |
| Taggar per prenumeration<sup>1</sup> |50 |
| Unika taggberäkningar per prenumeration<sup>1</sup> | 80 000 |
| [Distributioner på prenumerationsnivå](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per plats | 800<sup>2</sup> |

<sup>1</sup> Du kan använda upp till 50 taggar direkt för en prenumeration. Prenumerationen kan dock innehålla ett obegränsat antal taggar som tillämpas på resursgrupper och resurser i prenumerationen. Antalet taggar per resurs eller resursgrupp är begränsat till 50. Resource Manager returnerar en lista med unika [taggnamn](/rest/api/resources/tags) och värden i prenumerationen endast när antalet taggar är 80 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överskrider 80 000.

<sup>2</sup> Om du når gränsen på 800 distributioner tar du bort distributioner som inte längre behövs från historiken. Om du vill ta bort distributioner på prenumerationsnivå använder [du Remove-AzDeployment eller](/powershell/module/az.resources/Remove-AzDeployment) [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete).
