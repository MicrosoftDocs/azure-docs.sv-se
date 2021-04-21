---
title: ta med fil
description: ta med fil
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800262"
---
| Resurs | Gräns |
| --- | --- |
| Hanteringsgrupper per Azure AD-klientorganisation | 10 000 |
| Prenumerationer per hanteringsgrupp | Obegränsad. |
| Nivåer i hierarkin för hanteringsgrupp | Rotnivå plus 6 nivåer<sup>1</sup> |
| Direkt överordnad hanteringsgrupp per hanteringsgrupp | En |
| [Distributioner på hanteringsgruppsnivå](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per plats | 800<sup>2</sup> |

<sup>1</sup> De 6 nivåerna inkluderar inte prenumerationsnivån.

<sup>2</sup> Om du når gränsen på 800 distributioner tar du bort distributioner från historiken som inte längre behövs. Om du vill ta bort distributioner på hanteringsgruppsnivå använder du [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) [eller az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
