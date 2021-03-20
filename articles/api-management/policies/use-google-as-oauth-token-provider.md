---
title: Exempel-API Management-princip – auktorisera åtkomst med Google OAuth-token
titleSuffix: Azure API Management
description: Exempel på Azure API Management-princip – visar hur du auktoriserar åtkomst till dina slut punkter med Google som en OAuth-token-Provider.
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
ms.openlocfilehash: 0f6c9fe2146414f78e90d6ade1a00045cdf3a04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078025"
---
# <a name="authorize-access-using-google-oauth-token"></a>Auktorisera åtkomst med hjälp av Google OAuth-token

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du auktoriserar åtkomst till dina slut punkter med Google som en OAuth-token-Provider. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)