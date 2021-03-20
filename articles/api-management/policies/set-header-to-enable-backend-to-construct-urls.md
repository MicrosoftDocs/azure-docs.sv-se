---
title: Exempel på Azure API Management-princip – Lägg till en vidarebefordrad rubrik | Microsoft Docs
description: 'Exempel på Azure API Management-princip – visar hur du lägger till en vidarebefordrad rubrik i den inkommande begäran för att tillåta Server dels-API: et att skapa korrekta URL: er.'
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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078620"
---
# <a name="add-a-forwarded-header"></a>Lägg till en vidarebefordrad rubrik

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du lägger till en vidarebefordrad rubrik i den inkommande begäran för att tillåta Server dels-API: et att skapa korrekta URL: er. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="code"></a>Kod

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)