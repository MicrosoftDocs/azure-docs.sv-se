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
ms.openlocfilehash: fd92eed127ec50a3d3a86f667d9aa764b79c190a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585403"
---
# <a name="trusted-storage-for-media-services"></a>Betrodd lagring för Media Services

När du skapar ett Media Services konto måste du associera det med ett lagrings konto. Media Services kan komma åt lagrings kontot med hjälp av systemautentisering eller hanterad identitets autentisering. Media Services verifierar att Media Services kontot och lagrings kontot finns i samma prenumeration och verifierar att användaren som lägger till associationen har åtkomst till lagrings kontot med Azure Resource Manager RBAC.

>[!NOTE]
>Betrodd lagring är endast tillgängligt i API och är för närvarande inte aktiverat i Azure Portal.

## <a name="trusted-storage-with-a-firewall"></a>Betrodd lagring med en brand vägg

Men om du vill använda en brand vägg för att skydda ditt lagrings konto och aktivera betrodd lagring är autentiseringen [hanterade identiteter](concept-managed-identities.md) det bästa alternativet. Den ger Media Services åtkomst till lagrings kontot som har kon figurer ATS med en brand vägg eller en VNet-begränsning via betrodd lagrings åtkomst.

## <a name="tutorial"></a>Självstudie

Du kan läsa mer om hur du aktiverar betrodd lagring med självstudierna [Media Services Trusted Storage](tutorial-trusted-storage-rest.md) .

> [!NOTE]
> Du måste bevilja åtkomst till AMS Managed Identity Storage BLOB data Contributor för att Media Services ska kunna läsa och skriva till lagrings kontot.  Att bevilja rollen som allmän deltagare fungerar inte eftersom den inte aktiverar rätt behörigheter för data planet.

## <a name="further-reading"></a>Ytterligare läsning

Om du vill förstå metoderna för att skapa betrodd lagring med hanterade identiteter kan du läsa [hanterade identiteter och Media Services](concept-managed-identities.md).

Mer information om betrodda Microsoft-tjänster finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md#trusted-microsoft-services).

## <a name="next-steps"></a>Nästa steg

Mer information om vilka hanterade identiteter som kan utföras för dig och dina Azure-program finns i [Azure AD Managed identiteter](../../active-directory/managed-identities-azure-resources/overview.md).
