---
title: ta med fil
description: ta med fil
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738971"
---
| Resurs | Gräns |
| --- | --- |
| Hanterings grupper per Azure AD-klient | 10 000 |
| Prenumerationer per hanterings grupp | Många. |
| Nivåer för hanterings gruppens hierarki | Rot nivå plus 6 nivåer<sup>1</sup> |
| Direkt överordnad hanterings grupp per hanterings grupp | En |
| [Distributioner av hanterings grupp nivå](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per plats | 800<sup>2</sup> |

<sup>1</sup> 6 nivåer omfattar inte prenumerations nivån.

<sup>2</sup> Om du når gränsen på 800-distributioner tar du bort distributioner från den historik som inte längre behövs. Ta bort distributioner på hanterings grupps nivå genom att använda [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) eller [AZ Deployment mg Delete](/cli/azure/deployment/mg#az-deployment-mg-delete).
