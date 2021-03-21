---
title: Identitets leverantör för Microsoft-konto (MSA) i Azure AD
description: Använd Azure AD för att aktivera en extern användare (gäst) för att logga in på dina Azure AD-appar med sina Microsoft-konto (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693268"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Microsoft-konto (MSA) identitets leverantör för externa identiteter (för hands version)

> [!NOTE]
> Microsoft Account Identity Provider är en offentlig för hands version av Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Dina B2B-gäst-användare kan använda sina egna personliga Microsoft-konton för B2B-samarbete utan ytterligare konfiguration. Gäst användare kan lösa in B2B-samarbets inbjudningar eller slutföra dina registreringar av användar flöden med hjälp av sin personliga Microsoft-konto.

Microsoft-konton konfigureras av en användare för att få åtkomst till kundorienterade Microsoft-produkter och moln tjänster, till exempel Outlook, OneDrive, Xbox LIVE eller Microsoft 365. Kontot skapas och lagras i Microsoft konsument identitets konto systemet som körs av Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Gäst inloggning med Microsoft-konton

Microsoft-kontot är tillgängligt i listan över externa identiteter identitets leverantörer som standard. Ingen ytterligare konfiguration krävs för att gäst användare ska kunna logga in med sina Microsoft-konto med hjälp av inbjudan eller ett självbetjänings registrerings användar flöde.

![Microsoft-konto i listan med identitets leverantörer](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Microsoft-konto i Inbjudnings flödet

När du [bjuder in en gäst användare](add-users-administrator.md) till B2B-samarbete kan du ange deras Microsoft-konto som den e-postadress som de ska använda för att logga in.

![Bjud in med en Microsoft-konto](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Microsoft-konto i självbetjänings registrering av användar flöden

Microsoft-kontot är ett alternativ för en identitetsprovider för dina självbetjänings registrerings användar flöden. Användarna kan registrera sig för dina program med sina egna Microsoft-konton. Först måste du aktivera självbetjänings [registrering](self-service-sign-up-user-flow.md) för din klient. Sedan kan du skapa ett användar flöde för programmet och välja Microsoft-konto som ett av inloggnings alternativen.

![Microsoft-konto i ett användar flöde för självbetjänings registrering](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Nästa steg

- [Lägg till Azure Active Directory B2B-samarbets användare](add-users-administrator.md)
- [Lägga till självbetjänings registrering i en app](self-service-sign-up-user-flow.md)