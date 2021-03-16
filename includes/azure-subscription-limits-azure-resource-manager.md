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
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471023"
---
| Resurs | Gräns |
| --- | --- |
| Prenumerationer [som är kopplade till en Azure Active Directory-klient](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Obegränsat |
| [Medadministratörer](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per prenumeration |Obegränsat |
| [Resurs grupper](../articles/azure-resource-manager/management/overview.md) per prenumeration |980 |
| Storlek på Azure Resource Manager API-begäran |4 194 304 byte |
| Taggar per prenumeration<sup>1</sup> |50 |
| Beräkningar av unika Taggar per prenumeration<sup>1</sup> | 80 000 |
| [Distributioner på prenumerations nivå](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per plats | 800<sup>2</sup> |

<sup>1</sup> Du kan använda upp till 50 Taggar direkt till en prenumeration. Prenumerationen kan dock innehålla ett obegränsat antal taggar som tillämpas på resurs grupper och resurser i prenumerationen. Antalet Taggar per resurs eller resurs grupp är begränsat till 50. Resource Manager returnerar en [lista med unikt taggnamn och värden](/rest/api/resources/tags) i prenumerationen endast om antalet taggar är 80 000 eller mindre. Du kan fortfarande hitta en resurs efter tagg när antalet överskrider 80 000.

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner som inte längre behövs från historiken. Ta bort distributioner på prenumerations nivå genom att använda [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) eller [AZ Deployment del Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
