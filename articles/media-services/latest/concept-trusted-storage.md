---
title: Betrodd lagring för Media Services
description: Autentisering med hanterade identiteter ger Media Services åtkomst till det lagrings konto som har kon figurer ATS med en brand vägg eller en VNet-begränsning via betrodd lagrings åtkomst.
keywords: betrodd lagring, hanterade identiteter
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 59c1eb7936bc113f8935d6fa2ad378c6994c3ca9
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99408515"
---
# <a name="trusted-storage-for-media-services"></a>Betrodd lagring för Media Services

När du skapar ett Media Services konto måste du associera det med ett lagrings konto. Media Services kan komma åt lagrings kontot med hjälp av systemautentisering. Media Services verifierar att Media Services kontot och lagrings kontot finns i samma prenumeration och verifierar att användaren som lägger till associationen har åtkomst till lagrings kontot med Azure Resource Manager RBAC.

Men om du vill använda en brand vägg för att skydda ditt lagrings konto och aktivera betrodd lagring måste du använda [hanterade identiteter](concept-managed-identities.md) . Den ger Media Services åtkomst till lagrings kontot som har kon figurer ATS med en brand vägg eller en VNet-begränsning via betrodd lagrings åtkomst.

Om du vill förstå metoderna för att skapa betrodd lagring med hanterade identiteter kan du läsa [hanterade identiteter och Media Services](concept-managed-identities.md).

Mer information om kund hanterade nycklar och Key Vault finns i [ta med din egen nyckel (Kundhanterade nycklar) med Media Services](concept-use-customer-managed-keys-byok.md)

Mer information om betrodda Microsoft-tjänster finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="tutorials"></a>Självstudier

De här självstudierna innehåller båda scenarierna ovan.

- [Använd Azure Portal för att använda Kundhanterade nycklar eller BYOK med Media Services](tutorial-byok-portal.md)
- [Använd kund hanterade nycklar eller BYOK med Media Services REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Nästa steg

Mer information om vilka hanterade identiteter som kan utföras för dig och dina Azure-program finns i [Azure AD Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md).