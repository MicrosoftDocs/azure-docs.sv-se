---
title: Vad är registrerade Azure AD-enheter?
description: Lär dig hur Azure AD-registrerade enheter ger användarna stöd för BYOD (ta med din egen enhet) eller mobila enhets scenarier.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91256446"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-registrerade enheter

Målet med registrerade Azure AD-enheter är att ge användarna stöd för scenarierna ta med din egen enhet (BYOD) eller mobila enheter. I dessa scenarier kan en användare komma åt din organisations Azure Active Directory kontrollerade resurser med en personlig enhet.

| Azure AD har registrerats | Beskrivning |
| --- | --- |
| **Definition** | Registrerad på Azure AD utan att organisationens konto krävs för att logga in på enheten |
| **Primär mål grupp** | Gäller för alla användare med följande kriterier: |
|   | BYOD (Bring Your Own Device) |
|   | Mobila enheter |
| **Äganderätt till enhet** | Användare eller organisation |
| **Operativ system** | Windows 10, iOS, Android och MacOS |
| **Etablering** | Windows 10 – inställningar |
|   | iOS/Android – Företagsportal-eller Microsoft Authenticator-app |
|   | MacOS – Företagsportal |
| **Alternativ för enhets inloggning** | Lokala autentiseringsuppgifter för slutanvändare |
|   | Lösenord |
|   | Windows Hello |
|   | PIN-KOD |
|   | Biometrik eller mönster för andra enheter |
| **Enhetshantering** | Hantering av mobila enheter (exempel: Microsoft Intune) |
|   | Hantering av mobilprogram |
| **De viktigaste funktionerna** | SSO till moln resurser |
|   | Villkorlig åtkomst vid registrering i Intune |
|   | Villkorlig åtkomst via app Protection-princip |
|   | Aktiverar telefon inloggning med Microsoft Authenticator app |

![Azure AD-registrerade enheter](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Registrerade Azure AD-enheter är inloggade på att använda ett lokalt konto som en Microsoft-konto på en Windows 10-enhet, men har dessutom ett Azure AD-konto som är kopplat till åtkomst till organisations resurser. Åtkomst till resurser i organisationen kan ytterligare begränsas baserat på det Azure AD-konto och principer för villkorlig åtkomst som tillämpas på enhets identiteten.

Administratörer kan skydda och styra dessa Azure AD-registrerade enheter med hjälp av MDM-verktyg (Mobile Device Management) som Microsoft Intune. MDM är ett sätt att tvinga konfiguration av organisations krav, t. ex. lagrings utrymme som krypteras, lösen ords komplexitet och säkerhets program som uppdateras. 

Registrering av Azure AD kan utföras vid åtkomst till ett arbets program för första gången eller manuellt via menyn Inställningar i Windows 10. 

## <a name="scenarios"></a>Scenarier

En användare i din organisation vill ha åtkomst till verktyg för e-post, rapportera tids inaktive ring och få till gång till sina hem datorer. Din organisation har dessa verktyg bakom en princip för villkorlig åtkomst som kräver åtkomst från en Intune-kompatibel enhet. Användaren lägger till sitt organisations konto och registrerar sin hem dator med Azure AD och de nödvändiga Intune-principerna tvingas ge användarna åtkomst till sina resurser.

En annan användare vill ha åtkomst till organisationens e-post på sin personliga Android-telefon som har rotats. Företaget kräver en kompatibel enhet och har skapat en efterlevnadsprincip för Intune för att blockera alla rotade enheter. Medarbetaren har stoppats från att få åtkomst till organisations resurser på den här enheten.

## <a name="next-steps"></a>Nästa steg

- [Hantera enhetsidentiteter med hjälp av Azure-portalen](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
