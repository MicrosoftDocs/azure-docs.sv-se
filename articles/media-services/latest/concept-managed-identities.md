---
title: Hanterade identiteter
description: Media Services kan användas med Azure Managed identiteter.
keywords: ''
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: 0bbfb54d6ba7483e96633bdf05bb580e5517d216
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277750"
---
# <a name="managed-identities"></a>Hanterade identiteter

En vanlig utmaning för utvecklare är hanteringen av hemligheter och autentiseringsuppgifter för att skydda kommunikationen mellan olika tjänster. I Azure eliminerar Managed identiteter behovet av utvecklare som behöver hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure AD och använda den för att hämta Azure Active Directory (Azure AD)-token.

Det finns för närvarande två scenarier där hanterade identiteter kan användas med Media Services:

- Använd den hanterade identiteten för det Media Services kontot för att komma åt lagrings konton.

- Använd den hanterade identiteten för Media Services kontot för att komma åt Key Vault för åtkomst till kund nycklar.

I följande två avsnitt beskrivs stegen i de två scenarierna.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-storage-accounts"></a>Använd den hanterade identiteten för det Media Services kontot för att komma åt lagrings konton

1. Skapa ett Media Services-konto med en hanterad identitet.
1. Bevilja den hanterade identitetens huvud åtkomst till ett lagrings konto som du äger.
1. Media Services kan sedan komma åt lagrings kontot för din räkning med hjälp av den hanterade identiteten.

## <a name="use-the-managed-identity-of-the-media-services-account-to-access-key-vault-to-access-customer-keys"></a>Använd den hanterade identiteten för Media Services kontot för att komma åt Key Vault för åtkomst till kund nycklar

1. Skapa ett Media Services-konto med en hanterad identitet.
1. Bevilja den hanterade identitetens huvud åtkomst till en Key Vault som du äger.
1. Konfigurera Media Services kontot så att det använder sig av den kund nyckelbaserade konto krypteringen.
1. Media Services åtkomst till Key Vault för din räkning med hjälp av den hanterade identiteten.

Mer information om kund hanterade nycklar och Key Vault finns i [ta med din egen nyckel (Kundhanterade nycklar) med Media Services](concept-use-customer-managed-keys-byok.md)

## <a name="tutorials"></a>Självstudier

De här självstudierna innehåller båda scenarierna ovan.

- [Använd Azure Portal för att använda Kundhanterade nycklar eller BYOK med Media Services](security-customer-managed-keys-portal-tutorial.md)
- [Använd kund hanterade nycklar eller BYOK med Media Services REST API](security-customer-managed-keys-rest-postman-tutorial.md).

## <a name="next-steps"></a>Nästa steg

Mer information om vilka hanterade identiteter som kan utföras för dig och dina Azure-program finns i [Azure AD Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md).
