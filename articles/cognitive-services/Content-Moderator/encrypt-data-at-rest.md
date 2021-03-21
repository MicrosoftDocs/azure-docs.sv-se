---
title: Content Moderator kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Content Moderator kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524553"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator kryptering av data i vila

Content Moderator krypterar dina data automatiskt när de sparas i molnet, vilket hjälper till att uppfylla organisationens säkerhets-och efterlevnads mål.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar in [formuläret för begäran om Content Moderator Customer-Managed nyckel](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med Content Moderator tjänsten måste du skapa en ny Content Moderator resurs och välja E0 som pris nivå. När din Content Moderator-resurs med pris nivån E0 har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Aktivera data kryptering för Content Moderator-teamet

Om du vill aktivera data kryptering för Content Moderator granska teamet går du till [snabb start: prova Content moderator på webben](quick-start.md#create-a-review-team).  

> [!NOTE]
> Du måste ange ett _resurs-ID_ med Content moderator pris nivå för E0.

## <a name="next-steps"></a>Nästa steg

* En fullständig lista över tjänster som stöder CMK finns i [kund hanterade nycklar för Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Vad är Azure Key Vault](../../key-vault/general/overview.md)?
* [Formulär för begäran om Cognitive Services Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)