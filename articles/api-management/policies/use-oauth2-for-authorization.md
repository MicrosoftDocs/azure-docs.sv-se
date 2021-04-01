---
title: Exempel på Azure API Management-princip – använda OAuth2 för auktorisering mellan gateway och Server del
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy-visar hur du använder OAuth2 för auktorisering mellan gatewayen och en server del. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen.
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076104"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Använda OAuth2 för auktorisering mellan gatewayen och en server del

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du använder OAuth2 för auktorisering mellan gatewayen och en server del. Det visar hur du får en åtkomsttoken från AAD och vidarebefordrar den till serverdelen. 

Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

Följande skript använder egenskaper som visas i {{Property}}. Information om egenskaper och hur du använder dem i API Management-principer finns i [det här](../api-management-howto-properties.md) avsnittet.
 
## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)