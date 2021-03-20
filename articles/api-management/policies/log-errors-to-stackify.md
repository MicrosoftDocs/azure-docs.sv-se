---
title: Exempel på API Management-princip – skicka fel till Stackify för loggning
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du lägger till en princip för fel loggning för att skicka fel till Stackify för loggning..
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
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072075"
---
# <a name="send-errors-to-stackify-for-logging"></a>Skicka fel till Stackify för loggning

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till en princip för fel loggning för att skicka fel till Stackify för loggning. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i **fel** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)