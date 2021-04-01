---
title: Exempel-API Management-princip – Lägg till ett sidhuvud som innehåller korrelations-ID
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du lägger till en rubrik som innehåller ett korrelations-ID till den inkommande begäran.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 922565d26274aee12c9397c08c19330b4fce00e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076308"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Lägga till en rubrik som innehåller ett korrelations-id

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till en rubrik som innehåller ett korrelations-ID till den inkommande begäran. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)