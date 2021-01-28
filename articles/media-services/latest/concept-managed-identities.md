---
title: Hanterade identiteter och betrodd lagring
description: Media Services kan användas med hanterade identiteter för att aktivera betrodd lagring.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: inhenkel
ms.openlocfilehash: 291508a6beaa687b3a10f55df4591ce601ab51a0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956182"
---
# <a name="managed-identities-and-trusted-storage-with-media-services"></a>Hanterade identiteter och betrodd lagring med Media Services

Media Services kan användas med [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för att aktivera betrodd lagring. När du skapar ett Media Services konto måste du associera det med ett lagrings konto. Media Services kan komma åt lagrings kontot med hjälp av systemautentisering. Media Services verifierar att Media Services kontot och lagrings kontot finns i samma prenumeration och verifierar att användaren som lägger till associationen har åtkomst till lagrings kontot med Azure Resource Manager RBAC.

## <a name="trusted-storage"></a>Betrodd lagring

Men om du vill använda en brand vägg för att skydda ditt lagrings konto måste du använda hanterad identitets autentisering. Den ger Media Services åtkomst till lagrings kontot som har kon figurer ATS med en brand vägg eller en VNet-begränsning via betrodd lagrings åtkomst.  Mer information om betrodda Microsoft-tjänster finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="media-services-managed-identity-scenarios"></a>Medie tjänster hanterade identitets scenarier

Det finns för närvarande två scenarier där hanterad identitet kan användas med Media Services:

- Använd den hanterade identiteten för det Media Services kontot för att komma åt lagrings konton.

- Använd den hanterade identiteten för Media Services kontot för att komma åt Key Vault för åtkomst till kund nycklar.

I följande två avsnitt beskrivs skillnaderna i de två scenarierna.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Använd den hanterade identiteten för det Media Services kontot för att komma åt lagrings konton

1. Skapa ett Media Services-konto med en hanterad identitet.
1. Bevilja den hanterade identitetens huvud åtkomst till ett lagrings konto som du äger.
1. Media Services kan sedan komma åt lagrings kontot för din räkning med hjälp av den hanterade identiteten.

### <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Använd den hanterade identiteten för Media Services kontot för att komma åt Key Vault för åtkomst till kund nycklar

1. Skapa ett Media Services-konto med en hanterad identitet.
1. Bevilja den hanterade identitetens huvud åtkomst till en Key Vault som du äger.
1. Konfigurera Media Services kontot så att det använder sig av den kund nyckelbaserade konto krypteringen.
1. Media Services åtkomst till Key Vault för din räkning med hjälp av den hanterade identiteten.

Mer information om kund hanterade nycklar och Key Vault finns i [ta med din egen nyckel (Kundhanterade nycklar) med Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Självstudier

De här självstudierna innehåller båda scenarierna ovan.

- [Använd Azure Portal för att använda Kundhanterade nycklar eller BYOK med Media Services](tutorial-byok-portal.md)
- [Använd kund hanterade nycklar eller BYOK med Media Services REST API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Nästa steg

Mer information om vilka hanterade identiteter som kan utföras för dig och dina Azure-program finns i [Azure AD Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md).