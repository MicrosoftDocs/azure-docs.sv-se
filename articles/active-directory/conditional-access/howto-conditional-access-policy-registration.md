---
title: Villkorlig åtkomst – kombinerad säkerhets information – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för registrering av säkerhets information
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/24/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 711d4bdf2be2ad3158c12e4690a70fb83fe7a846
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559510"
---
# <a name="conditional-access-securing-security-info-registration"></a>Villkorsstyrd åtkomst: Säkra registrering av säkerhetsinformation

Att skydda när och hur användare registrerar sig för Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning är möjligt med användar åtgärder i en princip för villkorlig åtkomst. Den här funktionen är tillgänglig för organisationer som har aktiverat den [kombinerade registreringen](../authentication/concept-registration-mfa-sspr-combined.md). Den här funktionen gör det möjligt för organisationer att behandla registrerings processen som vilket program som helst i en princip för villkorlig åtkomst och använda den fullständiga kraften i villkorlig åtkomst för att skydda upplevelsen. 

Vissa organisationer tidigare kan ha använt betrott nätverks plats eller enhets efterlevnad som ett sätt att säkra registrerings upplevelsen. Med att lägga till [tillfälliga åtkomst pass](../authentication/howto-authentication-temporary-access-pass.md) i Azure AD kan administratörer etablera tidsbegränsade autentiseringsuppgifter för sina användare så att de kan registrera sig från valfri enhet eller plats. Tillfälliga åtkomst passets autentiseringsuppgifter uppfyller kraven för villkorlig åtkomst för Multi-Factor Authentication.

## <a name="create-a-policy-to-secure-registration"></a>Skapa en princip för säker registrering

Följande princip gäller för de valda användarna, som försöker registrera sig med den kombinerade registrerings upplevelsen. Principen kräver att användare utför Multi-Factor Authentication eller använder tillfälliga autentiseringsuppgifter för åtkomst pass.

1. I **Azure Portal** bläddrar du till **Azure Active Directory**  >  **säkerhet** för  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. I namn anger du ett namn för den här principen. Till exempel **kombinerad säkerhets informations registrering med tryck**.
1. Under **tilldelningar** väljer du **användare och grupper** och väljer de användare och grupper som du vill att den här principen ska tillämpas på.
   1. Under **Inkludera** väljer du **alla användare**.

      > [!WARNING]
      > Användare måste aktive ras för den [kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Under **undanta**.
      1. Välj **alla gäst-och externa användare**.
      
         > [!NOTE]
         > Tillfälligt åtkomst pass fungerar inte för gäst användare.

      1. Välj **användare och grupper** och välj organisationens nödfalls åtkomst eller Bryt glass konton. 
1. Under **molnappar eller åtgärder** väljer du **användar åtgärder**, markera **Registrera säkerhets information**.
1. Under **åtkomst kontroller**  >  **beviljar**.
   1. Välj **Bevilja åtkomst**.
   1. Välj **Kräv multifaktorautentisering**.
   1. Klicka på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Skapa**.

Administratörer måste nu utfärda autentiseringsuppgifter för tillfälliga åtkomst pass till nya användare så att de kan uppfylla kraven för Multi-Factor Authentication för registrering. Steg för att utföra den här uppgiften finns i avsnittet [skapa ett tillfälligt åtkomst pass i Azure AD-portalen](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass-in-the-azure-ad-portal).

Organisationer kan välja att kräva andra beviljande kontroller förutom för eller i stället för att **kräva Multi-Factor Authentication** i steg 6b. När du markerar flera kontroller måste du välja lämplig alternativ knapp för att kräva att **alla** eller **någon** av de valda kontrollerna ändras när du gör den här ändringen.

### <a name="guest-user-registration"></a>Registrering av gäst användare

För [gäst användare](../external-identities/what-is-b2b.md) som behöver registrera sig för Multi-Factor Authentication i din katalog kan du välja att blockera registrering från utanför [betrodda nätverks platser](concept-conditional-access-conditions.md#locations) med hjälp av följande guide.

1. I **Azure Portal** bläddrar du till **Azure Active Directory**  >  **säkerhet** för  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. I namn anger du ett namn för den här principen. Till exempel **kombinerad säkerhets informations registrering på betrodda nätverk**.
1. Under **tilldelningar** väljer du **användare och grupper** och väljer de användare och grupper som du vill att den här principen ska tillämpas på.
   1. Under **Inkludera** väljer du **alla gäst-och externa användare**.
1. Under **molnappar eller åtgärder** väljer du **användar åtgärder**, markera **Registrera säkerhets information**.
1. Under **villkor**  >  **platser**.
   1. Konfigurera **Ja**.
   1. Ta med **vilken plats som helst**.
   1. Undanta **alla betrodda platser**.
   1. Välj **gör** på bladet platser.
   1. Välj **färdig** på bladet villkor.
1. Under **åtkomst kontroller**  >  **beviljar**.
   1. Välj **blockera åtkomst**.
   1. Klicka sedan på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
