---
title: Ändra signaturens hash-algoritm för Microsoft 365 förtroende för förlitande part – Azure
description: Den här sidan innehåller rikt linjer för att ändra SHA-algoritmen för Federations förtroende med Microsoft 365.
keywords: SHA1, SHA256, M365, Federation, aadconnect, ADFS, AD FS, Change SHA, Federation Trust, förtroende för förlitande part
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf9347d4d14e6583febd4ffaf0447e912133b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89660922"
---
# <a name="change-signature-hash-algorithm-for-microsoft-365-relying-party-trust"></a>Ändra signaturens hash-algoritm för Microsoft 365 förtroende för förlitande part
## <a name="overview"></a>Översikt
Active Directory Federation Services (AD FS) (AD FS) signerar dess tokens till Microsoft Azure Active Directory för att säkerställa att de inte kan manipuleras. Den här signaturen kan baseras på SHA1 eller SHA256. Azure Active Directory stöder nu token som signerats med en SHA256-algoritm och vi rekommenderar att du ställer in algoritmen för tokensignering på SHA256 för den högsta säkerhets nivån. I den här artikeln beskrivs de steg som krävs för att ställa in algoritmen för tokensignering på nivån säkrare SHA256.

>[!NOTE]
>Microsoft rekommenderar att SHA256 används som algoritm för signering av token eftersom det är säkrare än SHA1 men SHA1 fortfarande är ett alternativ som stöds.

## <a name="change-the-token-signing-algorithm"></a>Ändra algoritmen för Token-signering
När du har angett signeringsalgoritmen med någon av de två processerna nedan, AD FS signera token för Microsoft 365 förlitande parts förtroende med SHA256. Du behöver inte göra några ytterligare konfigurations ändringar och den här ändringen påverkar inte möjligheten att komma åt Microsoft 365 eller andra Azure AD-program.

### <a name="ad-fs-management-console"></a>AD FS hanterings konsol
1. Öppna AD FS hanterings konsolen på den primära AD FS servern.
2. Expandera noden AD FS och klicka på **förtroenden för förlitande part**.
3. Högerklicka på ditt Microsoft 365/Azure-förtroende för förlitande part och välj **Egenskaper**.
4. Välj fliken **Avancerat** och välj SHA256 för säker hash-algoritm.
5. Klicka på **OK**.

![SHA256--MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-cmdletar
1. Öppna PowerShell under administratörs behörighet på en AD FS-server.
2. Ange den säkra hash-algoritmen med hjälp av cmdleten **set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Läs även
* [Reparera Microsoft 365 förtroende med Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

