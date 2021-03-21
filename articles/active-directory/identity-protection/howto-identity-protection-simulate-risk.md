---
title: Simulera risk identifieringar i Azure AD Identity Protection
description: Lär dig hur du simulerar risk identifieringar i identitets skydd
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835908"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulera risk identifieringar i identitets skydd

Administratörer kan vilja simulera risker i sin miljö för att kunna utföra följande objekt:

- Fyll i data i identitets skydds miljön genom att simulera risk identifiering och sårbarheter.
- Konfigurera riskfyllda principer för villkorlig åtkomst och testa effekten av dessa principer.

Den här artikeln innehåller steg för att simulera följande risk identifierings typer:

- Anonym IP-adress (lätt)
- Okända inloggnings egenskaper (måttlig)
- Ovanlig Travel (svår)

Andra risk identifieringar kan inte simuleras på ett säkert sätt.

Mer information om varje risk identifiering hittar du i artikeln, [Vad är en risk](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Anonym IP-adress

Du måste använda följande procedur för att utföra följande steg:

- [Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en) för att simulera anonyma IP-adresser. Du kan behöva använda en virtuell dator om organisationen begränsar användningen av webbläsaren Tor.
- Ett test konto som ännu inte har registrerats för Azure AD Multi-Factor Authentication.

**Utför följande steg för att simulera en inloggning från en anonym IP-adress**:

1. Använd [Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en)och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Ange autentiseringsuppgifterna för det konto som du vill ska visas i rapporten **inloggningar från anonyma IP-adresser** .

Inloggningen visas på instrument panelen för identitets skydd inom 10-15 minuter. 

## <a name="unfamiliar-sign-in-properties"></a>Obekanta inloggningsegenskaper

För att simulera okända platser måste du logga in från en plats och enheten ditt test konto har inte loggat in från tidigare.

I proceduren nedan används en nyligen skapad:

- VPN-anslutning för att simulera ny plats.
- Virtuell dator för att simulera en ny enhet.

För att utföra följande procedur måste du använda ett användar konto som har:

- Minst en 30-dagars inloggnings historik.
- Azure AD-Multi-Factor Authentication aktiverat.

**Utför följande steg för att simulera en inloggning från en okänd plats**:

1. När du loggar in med ditt test konto fungerar inte Multi-Factor Authentication-utmaningen (MFA) genom att inte skicka MFA-utmaningen.
2. Använd din nya VPN och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com) och ange autentiseringsuppgifterna för ditt test konto.

Inloggningen visas på instrument panelen för identitets skydd inom 10-15 minuter.

## <a name="atypical-travel"></a>Ovanlig resa

Det är svårt att simulera ovanlig-rese villkoret eftersom algoritmen använder maskin inlärning för att använda falsk positiv positiv, till exempel ovanlig resor från bekanta enheter, eller inloggningar från VPN-nätverk som används av andra användare i katalogen. Dessutom kräver algoritmen inloggnings historiken 14 dagar och 10 inloggningar av användaren innan risk identifieringen börjar genereras. På grund av komplexa Machine Learning-modeller och över regler, finns det en risk att följande steg inte leder till en risk identifiering. Du kanske vill replikera de här stegen för flera Azure AD-konton för att simulera denna identifiering.

**Utför följande steg för att simulera en ovanlig rese risk identifiering**:

1. Använd din standard webbläsare och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Ange autentiseringsuppgifterna för det konto som du vill skapa en ovanlig för rese risk identifiering för.
3. Ändra användar agenten. Du kan ändra användar agenten i Microsoft Edge från Utvecklarverktyg (F12).
4. Ändra din IP-adress. Du kan ändra din IP-adress med hjälp av ett VPN, ett Tor-tillägg eller skapa en ny virtuell dator i Azure i ett annat data Center.
5. Logga in för att [https://myapps.microsoft.com](https://myapps.microsoft.com) använda samma autentiseringsuppgifter som tidigare och inom några minuter efter den tidigare inloggningen.

Inloggningen visas på instrument panelen för identitets skydd inom 2-4 timmar.

## <a name="testing-risk-policies"></a>Testa risk principer

Det här avsnittet innehåller anvisningar för att testa användaren och de inloggnings risk principer som skapas i artikeln, [How to: Configure and Enable risk policies](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Princip för användarrisk

Utför följande steg för att testa en säkerhets princip för användar risk:

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **Översikt**.
1. Välj **Konfigurera användar risk princip**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
      1. **Villkor**  -  **Användar risk** Microsofts rekommendation är att ställa in det här alternativet på **hög**.
   1. Under **kontroller**
      1. **Åtkomst** – Microsofts rekommendation är att **tillåta åtkomst** och **Kräv lösen ords ändring**.
   1. **Tillämpa princip**  -  **Av**
   1. **Spara** – den här åtgärden kommer att gå tillbaka till **översikts** sidan.
1. Öka risken för ett test konto genom att till exempel simulera en av risk identifieringarna några gånger.
1. Vänta några minuter och kontrol lera sedan att risken har förhöjds för din användare. Om inte, simulerar du fler risk identifieringar för användaren.
1. Gå tillbaka till risk principen och ange **tillämpa principen** på **på** och **Spara** princip ändringen.
1. Nu kan du testa användar riskbaserade villkorlig åtkomst genom att logga in med en användare med en upphöjd risk nivå.

### <a name="sign-in-risk-security-policy"></a>Säkerhets princip för inloggnings risk

Gör så här för att testa en princip för inloggnings risk:

1. Navigera till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  **säkerhets**  >  **Översikt**.
1. Välj **Konfigurera inloggnings risk princip**.
   1. Under **tilldelningar**
      1. **Användare** – Välj **alla användare** eller **Välj personer och grupper** om du vill begränsa distributionen.
         1. Du kan också välja att exkludera användare från principen.
      1. **Villkor**  -  **Inloggnings risk** Microsofts rekommendation är att ställa in det här alternativet på **medel och över**.
   1. Under **kontroller**
      1. **Åtkomst** – Microsofts rekommendation är att **tillåta åtkomst** och **kräva Multi-Factor Authentication**.
   1. **Tillämpa princip**  -  **På**
   1. **Spara** – den här åtgärden kommer att gå tillbaka till **översikts** sidan.
1. Nu kan du testa inloggnings risk baserad villkorlig åtkomst genom att logga in med en riskfylld anslutning (till exempel med hjälp av Tor webbläsare). 

## <a name="next-steps"></a>Nästa steg

- [Vad är risker?](concept-identity-protection-risks.md)

- [Gör så här: Konfigurera och aktivera risk principer](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
