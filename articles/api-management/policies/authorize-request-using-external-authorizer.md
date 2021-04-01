---
title: Exempel-API Management-princip – auktorisera begäran med extern auktoriserare
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur auktoriserande begär Anden som använder extern auktoriserare kapslar in en anpassad eller äldre autentiserings-/auktoriserings logik.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076240"
---
# <a name="authorize-requests-using-external-authorizer"></a>Auktorisera begäran med extern auktoriserare

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du skyddar API-åtkomst genom att använda en extern auktoriserare som kapslar in anpassad autentisering/auktoriserings logik. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Principer för åtkomst begränsningar](../api-management-access-restriction-policies.md)
+ [Principexempel](../policy-reference.md)