---
title: Villkorlig åtkomst – kombinerad säkerhetsinformation – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för registrering av säkerhetsinformation
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765655"
---
# <a name="conditional-access-securing-security-info-registration"></a>Villkorsstyrd åtkomst: Säkra registrering av säkerhetsinformation

Att skydda när och hur användare registrerar sig för Azure AD Multi-Factor Authentication och lösenordsåterställning via självbetjäning är möjligt med användaråtgärder i en princip för villkorsstyrd åtkomst. Den här funktionen är tillgänglig för organisationer som har aktiverat [den kombinerade registreringen](../authentication/concept-registration-mfa-sspr-combined.md). Med den här funktionen kan organisationer hantera registreringsprocessen precis som alla program i en princip för villkorsstyrd åtkomst och använda den fullständiga kraften i villkorsstyrd åtkomst för att skydda upplevelsen. 

Vissa organisationer tidigare kan ha använt betrodd nätverksplats eller enhetsefterlevnad som ett sätt att skydda registreringsupplevelsen. Med tillägg av [Tillfällig åtkomstkod](../authentication/howto-authentication-temporary-access-pass.md) i Azure AD kan administratörer etablera tidsbegränsade autentiseringsuppgifter till sina användare som gör att de kan registrera sig från valfri enhet eller plats. Tillfällig åtkomstkod autentiseringsuppgifter uppfyller kraven för villkorsstyrd åtkomst för multifaktorautentisering.

## <a name="create-a-policy-to-secure-registration"></a>Skapa en princip för säker registrering

Följande princip gäller för de valda användarna som försöker registrera med hjälp av den kombinerade registreringsupplevelsen. Principen kräver att användarna utför multifaktorautentisering eller använder Tillfällig åtkomstkod autentiseringsuppgifter.

1. I den **Azure Portal** bläddrar du till **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Välj **Ny princip.**
1. I Namn anger du ett namn för den här principen. Till exempel kombinerad **registrering av säkerhetsinformation med TAP**.
1. Under **Tilldelningar** väljer du **Användare och grupper och** väljer de användare och grupper som du vill att principen ska gälla för.
   1. Under **Inkludera** väljer du **Alla användare.**

      > [!WARNING]
      > Användare måste vara aktiverade för den [kombinerade registreringen](../authentication/howto-registration-mfa-sspr-combined.md).

   1. Under **Exkludera**.
      1. Välj **Alla gästanvändare och externa användare.**
      
         > [!NOTE]
         > Tillfällig åtkomstkod fungerar inte för gästanvändare.

      1. Välj **Användare och grupper** och välj organisationens konto för åtkomst vid akutfall eller nödfall. 
1. Under **Molnappar eller åtgärder** väljer du **Användaråtgärder** och markerar **Registrera säkerhetsinformation.**
1. Under **Åtkomstkontroller Bevilja**  >  .
   1. Välj **Bevilja åtkomst**.
   1. Välj **Kräv multifaktorautentisering**.
   1. Klicka på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Skapa**.

Administratörer måste nu utfärda autentiseringsuppgifter Tillfällig åtkomstkod nya användare så att de kan uppfylla kraven för att multifaktorautentisering ska kunna registreras. Steg för att utföra den här uppgiften finns i avsnittet [Skapa en Tillfällig åtkomstkod i Azure AD-portalen](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass).

Organisationer kan välja att kräva andra beviljandekontroller utöver eller i stället för **Kräv multifaktorautentisering** i steg 6b. När du väljer flera kontroller måste du välja  lämplig alternativknapp för att kräva alla eller **en** av de valda kontrollerna när du gör den här ändringen.

### <a name="guest-user-registration"></a>Registrering av gästanvändare

För [gästanvändare](../external-identities/what-is-b2b.md) som behöver registrera sig för multifaktorautentisering i din [](concept-conditional-access-conditions.md#locations) katalog kan du välja att blockera registrering utanför betrodda nätverksplatser med hjälp av följande guide.

1. I den **Azure Portal** bläddrar du till **Azure Active Directory**  >  **Security**  >  **Conditional Access**.
1. Välj **Ny princip.**
1. I Namn anger du ett namn för den här principen. Till exempel kombinerad **registrering av säkerhetsinformation på betrodda nätverk.**
1. Under **Tilldelningar** väljer du **Användare och grupper och** väljer de användare och grupper som du vill att principen ska gälla för.
   1. Under **Inkludera** väljer du **Alla gästanvändare och externa användare.**
1. Under **Molnappar eller åtgärder** väljer du **Användaråtgärder** och markerar **Registrera säkerhetsinformation.**
1. Under   >  **Villkorsplatser**.
   1. Konfigurera **Ja.**
   1. Ta **med valfri plats.**
   1. Undanta **Alla betrodda platser.**
   1. Välj **Klar** på bladet Platser.
   1. Välj **Klar** på bladet Villkor.
1. Under **Åtkomstkontroller Bevilja**  >  .
   1. Välj **Blockera åtkomst.**
   1. Klicka sedan på **Välj**.
1. Ange **Aktivera princip** till **På**.
1. Välj sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorsstyrd åtkomst](concept-conditional-access-policy-common.md)

[Fastställa påverkan med endast läge för villkorlig åtkomstrapport](howto-conditional-access-insights-reporting.md)

[Simulera inloggningsbeteende med hjälp av verktyget för villkorsstyrd What If åtkomst](troubleshoot-conditional-access-what-if.md)

[Kräv att användare bekräftar autentiseringsinformation](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
