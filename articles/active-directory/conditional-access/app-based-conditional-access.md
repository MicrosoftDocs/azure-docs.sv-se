---
title: Godkända klient program med villkorlig åtkomst – Azure Active Directory
description: Lär dig hur du kräver godkända klient program för Cloud app-åtkomst med villkorlig åtkomst i Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 122cc6a2be17cb35e77b638a60fc5fa4f035c0d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91266147"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Gör så här: Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst

Användarna använder regelbundet sina mobila enheter för både personliga och arbetsrelaterade uppgifter. När du ser till att personalen kan vara produktiv, vill organisationer även förhindra data förlust från potentiellt osäkra program. Med villkorlig åtkomst kan organisationer begränsa åtkomsten till godkända (moderna autentisering kapabel)-klient program.

Den här artikeln visar två scenarier för att konfigurera principer för villkorlig åtkomst för resurser som Microsoft 365, Exchange Online och SharePoint Online.

- [Scenario 1: Microsoft 365 appar kräver en godkänd klient app](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klient app](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

I villkorlig åtkomst kallas denna funktion för att kräva en godkänd klient app. En lista över godkända klient program finns i [godkända klient program krav](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> För att kunna kräva godkända klient program för iOS-och Android-enheter måste dessa enheter först registreras i Azure AD.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Scenario 1: Microsoft 365 appar kräver en godkänd klient app

I det här scenariot har contoso beslutat att användare som använder mobila enheter har åtkomst till alla Microsoft 365-tjänster så länge de använder godkända klient program, t. ex. Outlook Mobile, OneDrive och Microsoft Teams. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande tre steg för att kräva att en godkänd klient app används på mobila enheter.

**Steg 1: princip för Android-och iOS-baserade moderna autentiserade klienter som kräver användning av ett godkänt klient program vid åtkomst till Exchange Online.**

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Office 365**.
1. Under **villkor** väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **villkor** väljer du **klient program (för hands version)**.
   1. **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver godkänd klient app** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 2: Konfigurera en Azure AD-princip för villkorlig åtkomst för Exchange Online med ActiveSync (EAS)**

1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Office 365 Exchange Online**.
1. Under **villkor**:
   1. **Klient program (för hands version)**:
      1. **Konfigurera** till **Ja**.
      1. Välj **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver godkänd klient app** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram.**

Läs artikeln [så här skapar och tilldelar du skydds principer för appar](/intune/apps/app-protection-policies)för att skapa skydds principer för Android och iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online och SharePoint Online kräver en godkänd klient app

I det här scenariot har contoso beslutat att användare bara får komma åt e-post och SharePoint-data på mobila enheter så länge de använder en godkänd klient app som Outlook Mobile. Alla användare har redan loggat in med autentiseringsuppgifter för Azure AD och har tilldelade licenser som innehåller Azure AD Premium P1 eller P2 och Microsoft Intune.

Organisationer måste utföra följande tre steg för att kräva att en godkänd klient app används på mobila enheter och Exchange ActiveSync-klienter.

**Steg 1: princip för Android-och iOS-baserade moderna autentiserade klienter som kräver användning av ett godkänt klient program vid åtkomst till Exchange Online och SharePoint Online.**

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Office 365 Exchange Online** och **Office 365 SharePoint Online**.
1. Under **villkor** väljer du **enhets plattformar**.
   1. **Konfigurera** till **Ja**.
   1. Ta med **Android** och **iOS**.
1. Under **villkor** väljer du **klient program (för hands version)**.
   1. **Konfigurera** till **Ja**.
   1. Välj **Mobilappar och skrivbordsklienter** och **Moderna autentiseringsklienter**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver godkänd klient app** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 2: princip för Exchange ActiveSync-klienter som kräver användning av en godkänd klient app.**

1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare** eller de enskilda **användare och grupper** som du vill tillämpa principen på. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Office 365 Exchange Online**.
1. Under **villkor**:
   1. **Klient program (för hands version)**:
      1. **Konfigurera** till **Ja**.
      1. Välj **mobilappar och skriv bords klienter** och **Exchange ActiveSync-klienter**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver godkänd klient app** och väljer **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa och aktivera din princip.

**Steg 3: Konfigurera Intune-appens skydds princip för iOS-och Android-klientprogram.**

Läs artikeln [så här skapar och tilldelar du skydds principer för appar](/intune/apps/app-protection-policies)för att skapa skydds principer för Android och iOS. 

## <a name="next-steps"></a>Nästa steg

[Vad är villkorsstyrd åtkomst?](overview.md)

[Komponenter för villkorlig åtkomst](concept-conditional-access-policies.md)

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
