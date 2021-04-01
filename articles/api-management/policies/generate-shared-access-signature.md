---
title: Exempel på API Management-princip – generera signatur för delad åtkomst
titleSuffix: Azure API Management
description: Exempel på Azure API Management policy – visar hur du genererar signaturen för delad åtkomst med hjälp av uttryck och vidarebefordrar begäran till Azure Storage med Rewrite-URI-princip.
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
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078433"
---
# <a name="generate-shared-access-signature"></a>Generera signatur för delad åtkomst

Den här artikeln visar ett exempel på en Azure API Management-princip som visar hur du genererar [signaturen för delad åtkomst](../../storage/common/storage-sas-overview.md) med hjälp av uttryck och vidarebefordrar begäran till Azure Storage med en Rewrite-URI-princip. Om du vill ange eller redigera en princip kod följer du stegen som beskrivs i [Ange eller redigera en princip](../set-edit-policies.md). Fler exempel finns i [princip exempel](../policy-reference.md).

## <a name="policy"></a>Policy

Klistra in koden i det **inkommande** blocket.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Nästa steg

Läs mer om APIM-principer:

+ [Omvandlingsprinciper](../api-management-transformation-policies.md)
+ [Principexempel](../policy-reference.md)