---
title: Custom Vision kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för Custom Vision och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652266"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision kryptering av data i vila

Azure Custom Vision krypterar automatiskt dina data när de sparas i molnet. Custom Vision kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter 11 maj 2020. Om du vill använda CMK med Custom Vision måste du skapa en ny Custom Vision resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över tjänster som stöder CMK finns i [kund hanterade nycklar för Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Vad är Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulär för begäran om Cognitive Services Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)