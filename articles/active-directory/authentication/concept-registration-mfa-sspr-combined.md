---
title: Kombinerad registrering för SSPR och Azure AD Multi-Factor Authentication-Azure Active Directory
description: Lär dig mer om den kombinerade registrerings upplevelsen för Azure Active Directory att låta användare registrera sig för både Azure AD-Multi-Factor Authentication och återställning av lösen ord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 112ad0714c84cd3be08788b3277f52372f6d0373
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938465"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Kombinerad säkerhets informations registrering för Azure Active Directory översikt

Före kombinerad registrering har användare registrerat autentiseringsmetoder för Azure AD Multi-Factor Authentication och återställning av lösen ord för självbetjäning (SSPR) separat. Personer har förvirrat att liknande metoder användes för Multi-Factor Authentication och SSPR, men de var tvungna att registrera sig för båda funktionerna. Med kombinerad registrering kan användarna registrera sig en gång och få fördelarna med både Multi-Factor Authentication-och SSPR.

> [!NOTE]
> Från den 15 augusti 2020 aktive ras alla nya Azure AD-klienter automatiskt för kombinerad registrering. 

Den här artikeln beskriver hur kombinerad säkerhets registrering är. Information om hur du kommer igång med kombinerad säkerhets registrering finns i följande artikel:

> [!div class="nextstepaction"]
> [Aktivera kombinerad säkerhets registrering](howto-registration-mfa-sspr-combined.md)

![Mitt konto visar registrerad säkerhets information för en användare](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

Innan du aktiverar den nya upplevelsen kan du läsa den här administratörs säkra dokumentationen och den användar fokuserade dokumentationen så att du förstår funktionerna och effekterna av den här funktionen. Basera din utbildning på [användar dokumentationen](../user-help/security-info-setup-signin.md) för att förbereda dina användare för den nya upplevelsen och hjälpa till att säkerställa en lyckad distribution.

Den kombinerade säkerhets informations registreringen i Azure AD är för närvarande inte tillgänglig för nationella moln som Azure Germany eller Azure Kina 21Vianet. Den är tillgänglig för Azure amerikanska myndigheter.

> [!IMPORTANT]
> Användare som har Aktiver ATS för både den ursprungliga för hands versionen och den förbättrade kombinerade registrerings upplevelsen ser det nya beteendet. Användare som är aktiverade för båda upplevelserna ser bara mitt konto upplevelse. *Mitt konto* överensstämmer med utseendet och känslan av kombinerad registrering och ger en sömlös upplevelse för användarna. Användarna kan se mitt konto genom att gå till [https://myaccount.microsoft.com](https://myaccount.microsoft.com) .
>
> Du kan stöta på ett fel meddelande när du försöker få åtkomst till alternativet för säkerhets information, till exempel "det går inte att logga in". Bekräfta att du inte har någon konfigurations-eller grup princip objekt som blockerar cookies från tredje part i webbläsaren.

*Mina konto* sidor är lokaliserade baserat på språk inställningarna för datorn som kommer åt sidan. Microsoft lagrar det senaste språket som används i webbläsarens cacheminne, så efterföljande försök att komma åt sidorna fortsätter att återges på det senast använda språket. Sidorna återges igen om du rensar cacheminnet.

Om du vill tvinga ett speciellt språk kan du lägga till `?lng=<language>` i slutet av webb adressen, där `<language>` är koden för det språk som du vill återge.

![Konfigurera SSPR eller andra säkerhets verifierings metoder](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metoder som är tillgängliga i kombinerad registrering

Kombinerad registrering stöder följande autentiseringsmetoder och åtgärder:

| Metod | Registrera dig | Ändra | Ta bort |
| --- | --- | --- | --- |
| Microsoft Authenticator | Ja (högst 5) | Inga | Ja |
| Annan Authenticator-app | Ja (högst 5) | Inga | Ja |
| Maskinvaru-token | Inga | Inga | Ja |
| Telefon | Ja | Ja | Ja |
| Alternativ telefon | Ja | Ja | Ja |
| Arbetstelefon | Ja | Ja | Ja |
| E-post | Ja | Ja | Ja |
| Säkerhetsfrågor | Ja | Inga | Ja |
| Applösenord | Ja | Inga | Ja |
| FIDO2 säkerhets nycklar<br />*Endast hanterat läge från sidan [säkerhets information](https://mysignins.microsoft.com/security-info)*| Ja | Ja | Ja |

> [!NOTE]
> Applösenord är bara tillgängliga för användare som har Aktiver ATS för Multi-Factor Authentication. Applösenord är inte tillgängliga för användare som har Aktiver ATS för Multi-Factor Authentication via en princip för villkorlig åtkomst.

Användare kan ange ett av följande alternativ som standard Multi-Factor Authentication metod:

- Microsoft Authenticator – meddelande.
- Authenticator-app eller maskinvaru-token – kod.
- Telefonsamtal.
- Textmeddelande.

När vi fortsätter att lägga till fler autentiseringsmetoder till Azure AD är dessa metoder tillgängliga i kombinerad registrering.

## <a name="combined-registration-modes"></a>Kombinerade registrerings lägen

Det finns två lägen för kombinerad registrering: avbryta och hantera.

- **Avbrotts läge** är en guide som liknar erfarenhet, som presenteras för användare när de registrerar eller uppdaterar sina säkerhets uppgifter vid inloggning.
- **Hanterings läget** är en del av användar profilen och gör att användarna kan hantera sina säkerhets uppgifter.

För båda lägena kan användare som tidigare har registrerat en metod som kan användas för Multi-Factor Authentication måste utföra Multi-Factor Authentication innan de kan komma åt sina säkerhets uppgifter. Användarna måste bekräfta sin information innan de fortsätter att använda sina tidigare registrerade metoder. 

### <a name="interrupt-mode"></a>Avbrotts läge

Kombinerad registrering respekterar både Multi-Factor Authentication-och SSPR-principer, om båda har Aktiver ATS för din klient. Dessa principer styr huruvida en användare avbryts för registrering under inloggning och vilka metoder som är tillgängliga för registrering.

Följande är exempel scenarier där användare kan uppmanas att registrera eller uppdatera sin säkerhets information:

- *Multi-Factor Authentication registrering framtvingas genom identitets skydd:* Användarna uppmanas att registrera sig under inloggningen. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- *Multi-Factor Authentication registrering tillämpas via per användare-Multi-Factor Authentication:* Användarna uppmanas att registrera sig under inloggningen. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- *Multi-Factor Authentication registreringen tillämpas via villkorlig åtkomst eller andra principer:* Användarna uppmanas att registrera sig när de använder en resurs som kräver Multi-Factor Authentication. De registrerar Multi-Factor Authentication metoder och SSPR-metoder (om användaren är aktive rad för SSPR).
- *SSPR-registrering framtvingas:* Användarna uppmanas att registrera sig under inloggningen. De registrerar endast SSPR-metoder.
- *Tvingande SSPR-uppdatering:* Användare måste granska sina säkerhets uppgifter med ett intervall som angetts av administratören. Användarna visar sin information och kan bekräfta den aktuella informationen eller göra ändringar om det behövs.

När registreringen är aktive rad visas det minsta antal metoder som krävs för att uppfylla både Multi-Factor Authentication-och SSPR-principer, från de flesta till minst säkra.

Tänk på följande exempel scenario:

- En användare har Aktiver ATS för SSPR. SSPR-principen krävde två metoder för att återställa och har aktiverat mobil kod, e-post och telefon.
- Den här användaren krävs för att registrera två metoder.
   - Användaren visas som standard autentiserare.
   - Användaren kan välja att registrera e-post i stället för Authenticator-appen eller telefon.

Följande flödes diagram beskriver vilka metoder som visas för en användare när du avbryter registreringen under inloggningen:

![Kombinerat flödes schema för säkerhets information](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Om du har både Multi-Factor Authentication-och SSPR aktiverat rekommenderar vi att du tillämpar Multi-Factor Authentication registrering.

Om SSPR-principen kräver att användare granskar sin säkerhets information med jämna mellanrum, avbryts användarna under inloggningen och alla registrerade metoder visas. De kan bekräfta den aktuella informationen om den är uppdaterad, eller så kan de göra ändringar om de behöver det. Användarna måste utföra Multi-Factor Authentication vid åtkomst till den här sidan.

### <a name="manage-mode"></a>Hantera läge

Användare kan komma åt hanterings läget genom att gå till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) eller genom att välja **säkerhets information** från mitt konto. Därifrån kan användarna lägga till metoder, ta bort eller ändra befintliga metoder, ändra standard metoden med mera.

## <a name="key-usage-scenarios"></a>Scenarier för nyckel användning

### <a name="set-up-security-info-during-sign-in"></a>Konfigurera säkerhetsinformation under inloggning

En administratör har tvingat registrering.

En användare har inte konfigurerat all nödvändig säkerhets information och går till Azure Portal. När du har angett användar namn och lösen ord uppmanas användaren att ställa in säkerhets information. Användaren följer sedan stegen som visas i guiden för att konfigurera den säkerhets information som krävs. Om inställningarna tillåter det kan användaren välja att ställa in andra metoder än de som visas som standard. När du har slutfört guiden granskar användarna de metoder de har ställt in och deras standard metod för Multi-Factor Authentication. För att slutföra installationen bekräftar användaren informationen och fortsätter till Azure Portal.

### <a name="set-up-security-info-from-my-account"></a>Konfigurera säkerhets information från mitt konto

En administratör har inte tvingat registrering.

En användare som ännu inte har konfigurerat all nödvändig säkerhets information går till [https://myaccount.microsoft.com](https://myaccount.microsoft.com) . Användaren väljer **säkerhets information** i den vänstra rutan. Därifrån väljer användaren att lägga till en metod, väljer någon av de tillgängliga metoderna och följer stegen för att ställa in den metoden. När du är färdig ser användaren den metod som har angetts på sidan säkerhets information.

### <a name="delete-security-info-from-my-account"></a>Ta bort säkerhets information från mitt konto

En användare som tidigare har konfigurerat minst en metod navigerar till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Användaren väljer att ta bort en av de tidigare registrerade metoderna. När du är färdig ser användaren inte längre metoden på sidan säkerhets information.

### <a name="change-the-default-method-from-my-account"></a>Ändra standard metod från mitt konto

En användare som tidigare har konfigurerat minst en metod som kan användas för Multi-Factor Authentication navigerar till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Användaren ändrar den aktuella standard metoden till en annan standard metod. När du är färdig ser användaren den nya standard metoden på sidan säkerhets information.

## <a name="next-steps"></a>Nästa steg

För att komma igång, se självstudierna för att [Aktivera självbetjäning för återställning av lösen ord](tutorial-enable-sspr.md) och [aktivera Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Lär dig hur du [aktiverar kombinerad registrering i din klient organisation](howto-registration-mfa-sspr-combined.md) eller [tvinga användare att registrera autentiseringsmetoder på nytt](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Du kan också granska de [tillgängliga metoderna för Azure AD Multi-Factor Authentication och SSPR](concept-authentication-methods.md).
