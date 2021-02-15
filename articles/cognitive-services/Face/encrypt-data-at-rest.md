---
title: Ansikts tjänst kryptering av vilande data
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för ansikte och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524573"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Ansikts tjänst kryptering av vilande data

Ansikts tjänsten krypterar automatiskt dina data när de sparas i molnet. Kryptering av ansikts tjänst skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar [fråge formuläret för Customer-Managed nyckel](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med ansikts tjänsten måste du skapa en ny ansikts resurs och välja E0 som pris nivå. När din ansikte-resurs med E0-pris nivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över tjänster som stöder CMK finns i [kund hanterade nycklar för Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Vad är Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulär för begäran om Cognitive Services Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)