---
title: Tal tjänst kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för tal service.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: aa0fe33dff0161767b74546aad49003d8fc70c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015264"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Tal tjänst kryptering av data i vila

Tal tjänsten krypterar automatiskt dina data när de sparas i molnet. Tal tjänst kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering

Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data skyddas som standard och du behöver inte ändra din kod eller dina program för att utnyttja krypteringen.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

När du använder Custom Speech och anpassad röst kan tal tjänsten lagra följande data i molnet:  

* Tal spårnings data – endast om du aktiverar spårning för din anpassade slut punkt
* Överförda utbildningar och test data

Som standard lagras dina data i Microsofts lagring och din prenumeration använder Microsoft-hanterade krypterings nycklar. Du kan också välja att förbereda ditt eget lagrings konto. Åtkomst till butiken hanteras av den hanterade identiteten och tal tjänsten kan inte direkt komma åt dina egna data, till exempel tal spårnings data, anpassnings tränings data och anpassade modeller.

Mer information om hanterad identitet finns i [Vad är hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Ta med din egen lagring (BYOS) för anpassning och loggning

Om du vill begära åtkomst för att ta med din egen lagring kan du fylla i och skicka in tjänsten [Speech service – ta med din egen lagring (BYOS)](https://aka.ms/cogsvc-cmk). När du har godkänt måste du skapa ditt eget lagrings konto för att lagra de data som krävs för anpassning och loggning. När du lägger till ett lagrings konto aktiverar tal tjänst resursen en systemtilldelad hanterad identitet. När systemtilldelad hanterad identitet har Aktiver ATS kommer den här resursen att registreras med Azure Active Directory (AAD). När den hanterade identiteten har registrerats får den åtkomst till lagrings kontot. Du kan lära dig mer om hanterade identiteter här. Mer information om hanterad identitet finns i [Vad är hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Om du inaktiverar systemtilldelade hanterade identiteter kommer åtkomst till lagrings kontot att tas bort. Detta leder till att de delar av tal tjänsten som kräver åtkomst till lagrings kontot slutar fungera.  

Tal tjänsten har för närvarande inte stöd för Customer Lockbox. Kund information kan dock lagras med BYOS, så att du kan uppnå liknande data kontroller för [Customer lockbox](../../security/fundamentals/customer-lockbox-overview.md). Tänk på att tal tjänst data är kvar och bearbetas i den region där tal resursen skapades. Detta gäller för alla data i vila och data under överföring. När du använder anpassnings funktioner som Custom Speech och anpassad röst överförs all kund information, lagras och bearbetas i samma region där din BYOS (om den används) och en röst tjänst resurs finns.

> [!IMPORTANT]
> Microsoft använder **inte** kund information för att förbättra sina tal modeller. Om slut punkts loggningen är inaktive rad och inga anpassningar används, lagras även inga kund uppgifter. 

## <a name="next-steps"></a>Nästa steg

* [Speech service – Hämta ditt eget lagrings formulär (BYOS)](https://aka.ms/cogsvc-cmk)
* [Vad är hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md).