---
title: ta med fil
description: ta med fil
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: d285b48606485fbd7f53511a15be1e2a31791829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244679"
---
> [!NOTE]
> Kommandot `az webapp up` utför följande åtgärder:
>
>- Skapa en standard [resurs grupp](/cli/azure/group#az-group-create).
>
>- Skapa en standard [plan för App Service](/cli/azure/appservice/plan#az-appservice-plan-create).
>
>- [Skapa en app](/cli/azure/webapp#az-webapp-create) med det angivna namnet.
>
>- [Zip-distribuera](../articles/app-service/deploy-zip.md) filer från den aktuella arbetskatalogen till appen.
>