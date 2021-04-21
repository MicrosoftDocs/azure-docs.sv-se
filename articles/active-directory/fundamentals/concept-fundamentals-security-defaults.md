---
title: Azure Active Directory standardinställningar för säkerhet
description: Standardprinciper för säkerhet som skyddar organisationer mot vanliga attacker i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: efa88e1be5c5df5dd09cb5a97c8ece352496ccdb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769705"
---
# <a name="what-are-security-defaults"></a>Vad är standardinställningar för säkerhet?

Det kan vara svårt att hantera säkerheten eftersom vanliga identitetsrelaterade attacker som lösenordsattacker, återuppspelning och nätfiske blir allt populärare. Standardinställningarna för säkerhet gör det enklare att skydda din organisation från dessa attacker med förkonfigurerade säkerhetsinställningar:

- Kräva att alla användare registrerar sig för Azure AD Multi-Factor Authentication.
- Kräva att administratörer utför multifaktorautentisering.
- Blockera äldre autentiseringsprotokoll.
- Kräva att användare utför multifaktorautentisering vid behov.
- Skydda privilegierade aktiviteter som åtkomst till Azure Portal.

![Skärmbild av Azure Portal med växlingsknappen för att aktivera standardinställningar för säkerhet](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Mer information om varför standardinställningar för säkerhet görs tillgängliga finns i Alex Weinerts blogginlägg [Introduktion till standardinställningar för säkerhet.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)

## <a name="availability"></a>Tillgänglighet

Microsoft gör standardvärdena för säkerhet tillgängliga för alla. Målet är att se till att alla organisationer har en grundläggande säkerhetsnivå aktiverad utan extra kostnad. Du aktiverar standardinställningarna för säkerhet i Azure Portal. Om din klientorganisation skapades den 22 oktober 2019 eller senare är det möjligt att säkerhetsstandarder redan är aktiverade i din klientorganisation. För att skydda alla våra användare distribueras standardinställningar för säkerhet till alla nya klienter som skapas.

### <a name="whos-it-for"></a>Vem är det för?

- Om du är en organisation som vill öka din säkerhetsstatus men inte vet hur eller var du ska börja är standardvärdena för säkerhet något för dig.
- Om du är en organisation som använder den kostnadsfria nivån Azure Active Directory licensiering är standardvärdena för säkerhet något för dig.

### <a name="who-should-use-conditional-access"></a>Vem ska använda villkorlig åtkomst?

- Om du är en organisation som för närvarande använder principer för villkorsstyrd åtkomst för att samla signaler, fatta beslut och genomdriva organisationsprinciper, är säkerhetsstandarder förmodligen inte rätt för dig. 
- Om du är en organisation Azure Active Directory Premium licenser är standardinställningarna för säkerhet förmodligen inte rätt för dig.
- Om din organisation har komplexa säkerhetskrav bör du överväga villkorlig åtkomst.

## <a name="policies-enforced"></a>Framtvingade principer

### <a name="unified-multi-factor-authentication-registration"></a>Registrering av enhetlig multifaktorautentisering

Alla användare i din klientorganisation måste registrera sig för multifaktorautentisering (MFA) i form av Azure AD Multi-Factor Authentication. Användarna har 14 dagar på sig att registrera sig för Azure AD Multi-Factor Authentication med hjälp av Microsoft Authenticator appen. När de 14 dagarna har passerat kan användaren inte logga in förrän registreringen har slutförts. En användares 14-dagars period börjar efter den första lyckade interaktiva inloggningen efter aktivering av standardvärden för säkerhet.

### <a name="protecting-administrators"></a>Skydda administratörer

Användare med privilegierad åtkomst har ökad åtkomst till din miljö. På grund av den kraft som dessa konton har bör du behandla dem med särskild försiktighet. En vanlig metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av kontoverifiering för inloggning. I Azure AD kan du få en starkare kontoverifiering genom att kräva multifaktorautentisering.

När registreringen med Azure AD Multi-Factor Authentication är klar krävs följande nio Azure AD-administratörsroller för att utföra ytterligare autentisering varje gång de loggar in:

- Global administratör
- SharePoint-administratör
- Exchange-administratör
- Administratör för villkorsstyrd åtkomst
- Säkerhetsadministratör
- Supportadministratör
- Faktureringsadministratör
- Användaradministratör
- Autentiseringsadministratör

> [!WARNING]
> Se till att din katalog har minst två konton med tilldelade behörigheter som global administratör. Detta hjälper om en global administratör är utelåst. Mer information finns i artikeln Hantera konton [för åtkomst vid akutfall i Azure AD.](../roles/security-emergency-access.md)

### <a name="protecting-all-users"></a>Skydda alla användare

Vi tenderar att tro att administratörskonton är de enda konton som behöver extra lager av autentisering. Administratörer har bred åtkomst till känslig information och kan göra ändringar i prenumerationsomfattande inställningar. Angripare riktar sig dock ofta till slutanvändare. 

När angriparen har fått åtkomst kan de begära åtkomst till privilegierad information åt den ursprungliga kontoinnehavaren. De kan till och med ladda ned hela katalogen för att utföra en nätfiskeattack mot hela organisationen. 

En vanlig metod för att förbättra skyddet för alla användare är att kräva en starkare form av kontoverifiering, till exempel Multi-Factor Authentication, för alla. När användarna har slutfört registreringen av Multi-Factor Authentication uppmanas de att autentisera sig vid behov. Användarna tillfrågas främst när de autentiseras med en ny enhet eller ett nytt program, eller när de utför kritiska roller och uppgifter. Den här funktionen skyddar alla program som är registrerade med Azure AD, inklusive SaaS-program.

### <a name="blocking-legacy-authentication"></a>Blockera äldre autentisering

Azure AD stöder olika autentiseringsprotokoll, inklusive äldre autentisering, för att ge användarna enkel åtkomst till dina molnappar. *Äldre autentisering* är en term som refererar till en autentiseringsbegäran som görs av:

- Klienter som inte använder modern autentisering (till exempel en Office 2010-klient).
- Alla klienter som använder äldre e-postprotokoll som IMAP, SMTP eller POP3.

Idag kommer de flesta kvarvarande inloggningsförsök från äldre autentisering. Äldre autentisering stöder inte Multi-Factor Authentication. Även om du har en multifaktorautentiseringsprincip aktiverad i din katalog kan en angripare autentisera med hjälp av ett äldre protokoll och kringgå Multi-Factor Authentication. 

När standardvärdena för säkerhet har aktiverats i klientorganisationen blockeras alla autentiseringsförfrågningar som görs av ett äldre protokoll. Standardvärden för säkerhet blockerar Exchange Active Sync grundläggande autentisering.

> [!WARNING]
> Innan du aktiverar standardinställningar för säkerhet bör du se till att dina administratörer inte använder äldre autentiseringsprotokoll. Mer information finns i [Så här flyttar du från äldre autentisering.](concept-fundamentals-block-legacy-authentication.md)

- [Konfigurera en enhet eller ett program med flera funktioner för att skicka e-post med Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Att Azure Resource Manager hantera dina tjänster är en mycket privilegierad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klientorganisationen, till exempel tjänstinställningar och prenumerationsfakturering. Enfaktorautentisering är sårbar för olika attacker som nätfiske och lösenordsattacker. 

Det är viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer. Du verifierar deras identitet genom att kräva ytterligare autentisering innan du tillåter åtkomst.

När du aktiverar standardinställningar för säkerhet i din klientorganisation måste alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI slutföra ytterligare autentisering. Den här principen gäller för alla användare som har Azure Resource Manager åtkomst, oavsett om de är administratör eller användare. 

> [!NOTE]
> Före 2017 har Exchange Online-klienter modern autentisering inaktiverat som standard. För att undvika risken för en inloggningsloop vid autentisering via dessa klienter måste du [aktivera modern autentisering](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Synkroniseringskontot Azure AD Connect från standardinställningarna för säkerhet och uppmanas inte att registrera sig för eller utföra multifaktorautentisering. Organisationer bör inte använda det här kontot för andra ändamål.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande ytterligare överväganden gäller distribution av standardinställningar för säkerhet.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Dessa standardinställningar för kostnadsfri säkerhet tillåter registrering och användning av Azure AD Multi-Factor Authentication med hjälp **av Microsoft Authenticator app med meddelanden.** Villkorlig åtkomst tillåter användning av alla autentiseringsmetoder som administratören väljer att aktivera.

| Metod | Standardinställningar för säkerhet | Villkorlig åtkomst |
| --- | --- | --- |
| Meddelande via mobilapp | X | X |
| Verifieringskod från mobilapp eller maskinvarutoken | X** | X |
| SMS till telefon |   | X |
| Ring till telefon |   | X |
| Applösenord |   | X** |

- ** Användare kan använda verifieringskoder från Microsoft Authenticator appen men kan bara registrera sig med aviseringsalternativet.
- Applösenord är endast tillgängliga i MFA per användare med äldre autentiseringsscenarier endast om de har aktiverats av administratörer.

### <a name="disabled-mfa-status"></a>Inaktiverad MFA-status

Om din organisation tidigare använder Azure AD Multi-Factor Authentication per användare ska du inte  oroa dig för att inte se användare med statusen Aktiverad eller **Framtvingad** om du tittar på statussidan Multi-Factor Auth. **Inaktiverad** är lämplig status för användare som använder standardinställningar för säkerhet eller villkorsstyrd åtkomst baserat på Azure AD Multi-Factor Authentication.

### <a name="conditional-access"></a>Villkorlig åtkomst

Du kan använda villkorsstyrd åtkomst för att konfigurera principer som liknar standardvärden för säkerhet, men med mer kornighet, inklusive användar exkludering, som inte är tillgängliga i standardinställningarna för säkerhet. Om du använder villkorsstyrd åtkomst och har principer för villkorsstyrd åtkomst aktiverade i din miljö är standardvärdena för säkerhet inte tillgängliga för dig. Om du har en licens som tillhandahåller villkorsstyrd åtkomst men inte har några principer för villkorlig åtkomst aktiverade i din miljö, är du välkommen att använda standardinställningar för säkerhet tills du aktiverar principer för villkorlig åtkomst. Mer information om Azure AD-licensiering finns på [prissättningssidan för Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

![Varningsmeddelande om att du kan ha standardinställningar för säkerhet eller villkorlig åtkomst, inte båda](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Här är stegvisa guider om hur du kan använda villkorsstyrd åtkomst för att konfigurera motsvarande principer för dessa principer som är aktiverade som säkerhetsstandard:

- [Kräv MFA för administratörer](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Kräv MFA för alla användare](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Kräv Azure AD MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – kräver Azure AD Identity Protection del av Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Aktivera standardinställningar för säkerhet

Så här aktiverar du standardinställningar för säkerhet i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com)   som säkerhetsadministratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory**   >  **Egenskaper**.
1. Välj **Hantera standardinställningar för säkerhet.**
1. Ställ in **Aktivera standardinställningar för säkerhet** på **Ja.**
1. Välj **Spara**.

## <a name="disabling-security-defaults"></a>Inaktivera standardinställningar för säkerhet

Organisationer som väljer att implementera principer för villkorsstyrd åtkomst som ersätter standardvärden för säkerhet måste inaktivera standardvärden för säkerhet. 

![Varningsmeddelande inaktiverar standardvärden för säkerhet för att aktivera villkorlig åtkomst](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Så här inaktiverar du standardinställningar för säkerhet i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com)   som säkerhetsadministratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory**   >  **Egenskaper**.
1. Välj **Hantera standardinställningar för säkerhet.**
1. Ställ **in Aktivera standardinställningar för säkerhet** på **Nej.**
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)
