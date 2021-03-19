---
title: Villkorlig åtkomst – kräver kompatibla enheter – Azure Active Directory
description: Skapa en anpassad princip för villkorlig åtkomst för att kräva kompatibla enheter
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89049136"
---
# <a name="conditional-access-require-compliant-devices"></a>Villkorlig åtkomst: Kräv kompatibla enheter

Organisationer som har distribuerat Microsoft Intune kan använda den information som returneras från sina enheter för att identifiera enheter som uppfyller kraven för efterlevnad, till exempel:

* Kräver en PIN-kod för att låsa upp
* Kräver enhets kryptering
* Kräver en lägsta eller högsta operativ system version
* En enhet som kräver en enhet är inte jailbrokade eller rotad

Den här policyn för efterlevnad vidarebefordras till Azure AD där villkorlig åtkomst kan fatta beslut om att bevilja eller blockera åtkomst till resurser. Mer information om efterlevnadsprinciper för enheter finns i artikeln [Ange regler för enheter för att tillåta åtkomst till resurser i din organisation med hjälp av Intune](/intune/protect/device-compliance-get-started)

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Följande steg hjälper dig att skapa en princip för villkorlig åtkomst för att kräva att enheter som har åtkomst till resurser markeras som kompatibla med organisationens efterlevnadsprinciper för Intune.

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
1. Under **tilldelningar** väljer **du användare och grupper**
   1. Under **Inkludera** väljer du **alla användare**.
   1. Under **exkludera** väljer **du användare och grupper** och väljer organisationens nödfalls åtkomst eller Bryt glas konton. 
   1. Välj **Klar**.
1. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **alla molnappar**.
   1. Om du måste utesluta vissa program från principen kan du välja dem från fliken **exkludera** under **Välj exkluderade molnappar** och välj **Välj**.
   1. Välj **Klar**.
1. Under **villkor**  >  **klient program (för hands version)**  >  **väljer du de klient program som den här principen ska gälla för**. lämna alla standardvärden markerade och välj **Slutför**.
1. Under **åtkomst kontroller**  >  **tilldelar** väljer **du Kräv att enheten ska markeras som kompatibel**.
   1. Välj **Välj**.
1. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
1. Välj **skapa** för att skapa för att aktivera principen.

> [!NOTE]
> Du kan registrera dina nya enheter i Intune även om du väljer **Kräv att enheten ska markeras som kompatibel** för **alla användare** och **alla molnappar** med stegen ovan. **Kräv att enheten markeras som kompatibel** kontroll blockerar inte Intune-registrering. 

### <a name="known-behavior"></a>Känt beteende

På Windows 7, iOS, Android, macOS och vissa tredjeparts webbläsare i Azure AD identifierar enheten med ett klient certifikat som är etablerad när enheten har registrerats med Azure AD. När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Slutanvändaren måste välja det här certifikatet innan de kan fortsätta att använda webbläsaren.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

[Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)

[Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)

[Enhetsefterlevnadsprinciper kan användas med Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
