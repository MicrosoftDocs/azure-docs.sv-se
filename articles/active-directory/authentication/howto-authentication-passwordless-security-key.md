---
title: Inloggning med lösenordslös säkerhetsnyckel – Azure Active Directory
description: Aktivera inloggning med lösenordslös säkerhetsnyckel till Azure AD med hjälp av FIDO2-säkerhetsnycklar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a88bb7904143f69a0eea84ea291c65e3244c70a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765871"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Aktivera inloggning med lösenordslös säkerhetsnyckel 

För företag som använder lösenord i dag och har en delad datormiljö är säkerhetsnycklar ett sömlöst sätt för arbetare att autentisera sig utan att ange ett användarnamn eller lösenord. Säkerhetsnycklar ger bättre produktivitet för arbetare och bättre säkerhet.

Det här dokumentet fokuserar på aktivering av säkerhetsnyckelbaserad lösenordsfri autentisering. I slutet av den här artikeln kommer du att kunna logga in på webbaserade program med ditt Azure AD-konto med hjälp av en FIDO2-säkerhetsnyckel.

## <a name="requirements"></a>Krav

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Aktivera [kombinerad registrering av säkerhetsinformation](concept-registration-mfa-sspr-combined.md)
- Kompatibla [FIDO2-säkerhetsnycklar](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN kräver Windows 10 version 1903 eller senare**

Om du vill använda säkerhetsnycklar för att logga in på webbappar och tjänster måste du ha en webbläsare som stöder WebAuthN-protokollet. Dessa inkluderar Microsoft Edge, Chrome, Firefox och Safari.

## <a name="prepare-devices"></a>Förbereda enheter

För Azure AD-anslutna enheter är den bästa upplevelsen Windows 10 version 1903 eller senare.

Hybrid Azure AD-anslutna enheter måste köra Windows 10 version 2004 eller senare.

## <a name="enable-passwordless-authentication-method"></a>Aktivera lösenordsfri autentiseringsmetod

### <a name="enable-the-combined-registration-experience"></a>Aktivera den kombinerade registreringsupplevelsen

Registreringsfunktioner för lösenordslösa autentiseringsmetoder förlitar sig på den kombinerade registreringsfunktionen. Följ stegen i artikeln Aktivera [kombinerad registrering av säkerhetsinformation för](howto-registration-mfa-sspr-combined.md)att aktivera kombinerad registrering.

### <a name="enable-fido2-security-key-method"></a>Aktivera FIDO2-säkerhetsnyckelmetod

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory säkerhet**  >    >  **autentiseringsmetoder**  >  **autentiseringsmetodprincip**.
1. Under metoden **FIDO2-säkerhetsnyckel** väljer du följande alternativ:
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – Alla användare eller Välj användare
1. **Spara** konfigurationen.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Användarregistrering och hantering av FIDO2-säkerhetsnycklar

1. Bläddra till [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Logga in om du inte redan gjort det.
1. Klicka **på Säkerhetsinformation.**
   1. Om användaren redan har minst en Azure AD Multi-Factor Authentication-metod registrerad kan han eller hon omedelbart registrera en FIDO2-säkerhetsnyckel.
   1. Om de inte har minst en registrerad Azure AD Multi-Factor Authentication-metod måste de lägga till en.
1. Lägg till en FIDO2-säkerhetsnyckel genom att klicka **på Lägg till metod** och välja **Säkerhetsnyckel.**
1. Välj **USB-enhet** eller **NFC-enhet.**
1. Ha nyckeln redo och välj **Nästa.**
1. En ruta visas och användaren uppmanas att skapa/ange en PIN-kod för din säkerhetsnyckel och sedan utföra den gest som krävs för nyckeln, antingen biometrik eller pekskärm.
1. Användaren kommer att returneras till den kombinerade registreringsupplevelsen och uppmanas att ange ett beskrivande namn för nyckeln så att användaren kan identifiera vilken som om den har flera. Klicka på **Nästa**.
1. Slutför **processen genom** att klicka på Klar.

## <a name="sign-in-with-passwordless-credential"></a>Logga in med lösenordslösa autentiseringsuppgifter

I exemplet nedan har en användare redan etablerat sin FIDO2-säkerhetsnyckel. Användaren kan välja att logga in på webben med sin FIDO2-säkerhetsnyckel i en webbläsare som stöds Windows 10 version 1903 eller senare.

![Inloggningsnyckel för säkerhetsnyckel Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem med den här funktionen kan du dela via Windows Feedbackhubben med följande steg:

1. Starta **Feedbackhubben** och kontrollera att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: Säkerhet och sekretess
   - Underkategori: FIDO
1. Om du vill samla in loggar använder du alternativet **för att återskapa mitt problem.**

## <a name="known-issues"></a>Kända problem

### <a name="security-key-provisioning"></a>Etablering av säkerhetsnyckel

Administratörsetablering och avetablering av säkerhetsnycklar är inte tillgängligt.

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>Cachelagrad inloggning på Hybrid Azure AD-anslutna enheter

Cachelagrad inloggning med FIDO2-nycklar misslyckas på Azure AD-anslutna hybridenheter på Windows 10 version 20H2. Det innebär att användarna inte kan logga in när det inte går att nå den lokala domänkontrollanten. Detta håller på att undersökas.

### <a name="upn-changes"></a>UPN-ändringar

Vi arbetar med att stödja en funktion som tillåter UPN-ändringar på Azure AD-anslutna hybridenheter och Azure AD-anslutna enheter. Om en användares UPN ändras kan du inte längre ändra FIDO2-säkerhetsnycklar för att ta hänsyn till ändringen. Lösningen är att återställa enheten och användaren måste omregistrera sig.

## <a name="next-steps"></a>Nästa steg

[FIDO2-säkerhetsnyckel Windows 10 logga in](howto-authentication-passwordless-security-key-windows.md)

[Aktivera FIDO2-autentisering till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhetsregistrering](../devices/overview.md)

[Läs mer om Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
