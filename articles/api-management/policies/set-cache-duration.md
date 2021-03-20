---
title: Exempel-API Management-princip – ange varaktighet för svars-cache
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy-visar hur du ställer in varaktighet för svars-cache med ett maxAge-värde i Cache-Control rubrik som skickats av Server delen..
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
ms.openlocfilehash: 088dfbcbfe00357c47de4662ac31b52e83d1d8ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070987"
---
# <a name="set-response-cache-duration"></a>Ange varaktighet för svarscache

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du ställer in varaktigheten för svars cachen med ett maxAge-värde i Cache-Control rubriken som skickas av Server Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)