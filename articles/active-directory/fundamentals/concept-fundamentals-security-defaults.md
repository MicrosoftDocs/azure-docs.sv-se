---
title: Azure Active Directory säkerhets inställningar
description: Säkerhets standard principer som skyddar organisationer från vanliga attacker i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 9c912b7122a40ae39bcbb703e1387af25fff2bc8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029914"
---
# <a name="what-are-security-defaults"></a>Vad är säkerhets inställningar?

Det kan vara svårt att hantera säkerhet med vanliga identitetsbaserade attacker, t. ex. Spray, uppspelning och nätfiske som blir mer populära. Säkerhets standarder gör det lättare att skydda din organisation från dessa attacker med förkonfigurerade säkerhets inställningar:

- Kräver att alla användare registreras för Azure AD Multi-Factor Authentication.
- Kräver att administratörer utför Multi-Factor Authentication.
- Blockerar bakåtkompatibla autentiseringsprotokoll.
- Kräver att användarna utför Multi-Factor Authentication vid behov.
- Skydda privilegierade aktiviteter som till gång till Azure Portal.

![Skärm bild av Azure Portal med växla för att aktivera säkerhets inställningar](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
Mer information om varför säkerhets inställningar görs tillgängliga finns i blogg inlägget Alex Weinert, och [vi presenterar säkerhets inställningar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="availability"></a>Tillgänglighet

Microsoft skapar säkerhets inställningar som är tillgängliga för alla. Målet är att säkerställa att alla organisationer har en grundläggande säkerhets nivå som är aktive rad utan extra kostnad. Du aktiverar säkerhets inställningarna i Azure Portal. Om din klient skapades den 22 oktober 2019 är det möjligt att säkerhets standardvärdena redan har Aktiver ATS i din klient organisation. I ett arbete för att skydda alla våra användare kommer säkerhets inställningarna att distribueras till alla nya klienter som skapats.

### <a name="whos-it-for"></a>Vem är det för?

- Om du är en organisation som vill öka din säkerhets position, men inte vet hur eller var du ska starta, är säkerhets inställningarna för dig.
- Om du är en organisation som använder den kostnads fria nivån av Azure Active Directory-licensiering, är säkerhets inställningarna för dig.

### <a name="who-should-use-conditional-access"></a>Vem ska använda villkorlig åtkomst?

- Om du är en organisation som för närvarande använder principer för villkorlig åtkomst för att ta fram signaler, fatta beslut och tillämpa organisations principer, är säkerhets inställningarna förmodligen inte rätt för dig. 
- Om du är en organisation med Azure Active Directory Premium licenser är säkerhets inställningarna förmodligen inte rätt för dig.
- Om din organisation har komplexa säkerhets krav bör du överväga villkorlig åtkomst.

## <a name="policies-enforced"></a>Tillämpade principer

### <a name="unified-multi-factor-authentication-registration"></a>Enhetlig Multi-Factor Authentication registrering

Alla användare i din klient organisation måste registrera sig för Multi-Factor Authentication (MFA) i form av Azure AD-Multi-Factor Authentication. Användare har 14 dagar på sig att registrera sig för Azure AD Multi-Factor Authentication med hjälp av Microsoft Authenticator-appen. När 14 dagar har passerat kan användaren inte logga in förrän registreringen är klar. En användares 14-dagars period börjar efter sin första lyckade interaktiva inloggning efter att ha aktiverat säkerhets inställningarna.

### <a name="protecting-administrators"></a>Skydda administratörer

Användare med privilegie rad åtkomst har ökat åtkomst till din miljö. På grund av den kraft de här kontona har måste du behandla dem med särskild omsorg. En gemensam metod för att förbättra skyddet av privilegierade konton är att kräva en starkare form av konto verifiering för inloggning. I Azure AD kan du få en bättre konto verifiering genom att kräva Multi-Factor Authentication.

När registreringen med Azure AD Multi-Factor Authentication är färdig krävs följande nio Azure AD-administratörs roller för att utföra ytterligare autentisering varje gång de loggar in:

- Global administratör
- SharePoint-administratör
- Exchange-administratör
- Administratör för villkorlig åtkomst
- Säkerhetsadministratör
- Support administratör
- Faktureringsadministratör
- Användaradministratör
- Administratör för autentisering

### <a name="protecting-all-users"></a>Skydda alla användare

Vi tenderar att tro att administratörs konton är de enda konton som behöver extra lager av autentisering. Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningarna för hela prenumerationen. Men angripare ofta riktar sig till slutanvändare. 

När angripare får åtkomst kan de begära åtkomst till privilegie rad information å den ursprungliga konto innehavarens vägnar. De kan till och med Ladda ned hela katalogen för att utföra en nätfiske-attack på hela organisationen. 

En gemensam metod för att förbättra skyddet för alla användare är att kräva en starkare form av konto verifiering, till exempel Multi-Factor Authentication, för alla. När användarna har slutfört Multi-Factor Authentication registreringen uppmanas de att ange ytterligare autentisering när det behövs. Den här funktionen skyddar alla program som är registrerade i Azure AD, inklusive SaaS-program.

### <a name="blocking-legacy-authentication"></a>Blockerar äldre autentisering

För att ge dina användare enkel åtkomst till dina molnappar stöder Azure AD olika autentiseringsprotokoll, inklusive äldre autentisering. *Äldre autentisering* är en term som hänvisar till en autentiseringsbegäran som görs av:

- Klienter som inte använder modern autentisering (till exempel en Office 2010-klient).
- Alla klienter som använder äldre e-postprotokoll som IMAP, SMTP eller POP3.

Idag kommer majoriteten av att kompromissa inloggnings försök komma från äldre autentisering. Äldre autentisering stöder inte Multi-Factor Authentication. Även om du har aktiverat en Multi-Factor Authentication princip i din katalog kan en angripare autentisera med hjälp av ett äldre protokoll och kringgå Multi-Factor Authentication. 

När säkerhets inställningarna har Aktiver ATS i din klient kommer alla autentiseringsbegäranden som gjorts av ett äldre protokoll att blockeras. Säkerhets standardvärden blockerar Exchange Active Sync grundläggande autentisering.

> [!WARNING]
> Innan du aktiverar säkerhets inställningarna kontrollerar du att administratörerna inte använder äldre autentiseringsprotokoll. Mer information finns i [så här flyttar du bort från äldre autentisering](concept-fundamentals-block-legacy-authentication.md).

- [Konfigurera en multifunktions enhet eller ett program för att skicka e-post med hjälp av Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>Skydda privilegierade åtgärder

Organisationer använder en mängd olika Azure-tjänster som hanteras via Azure Resource Manager API, inklusive:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegie rad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klienten, till exempel tjänst inställningar och fakturering av prenumerationer. Autentisering med en faktor är utsatt för en rad olika attacker, t. ex. nätfiske och lösen ords sprayning. 

Det är viktigt att du verifierar identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer. Du verifierar identiteten genom att kräva ytterligare autentisering innan du tillåter åtkomst.

När du har aktiverat säkerhets inställningarna i din klient, måste alla användare som har åtkomst till Azure Portal, Azure PowerShell eller Azure CLI slutföra ytterligare autentisering. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är en administratör eller en användare. 

> [!NOTE]
> Exchange Online-klienter före 2017 har modern autentisering inaktive rad som standard. För att undvika risken för en inloggnings slinga vid autentisering via dessa klienter måste du [Aktivera modern autentisering](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

> [!NOTE]
> Kontot för Azure AD Connect-synkronisering exkluderas från säkerhets inställningarna och kommer inte att uppmanas att registrera sig för eller utföra Multi-Factor Authentication. Organisationer bör inte använda det här kontot för andra orsaker.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Följande ytterligare överväganden är relaterade till distribution av säkerhets inställningar.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Dessa kostnads fria säkerhets standarder tillåter registrering och användning av Azure AD Multi-Factor Authentication att **endast använda Microsoft Authenticator-appen med hjälp av meddelanden**. Villkorlig åtkomst tillåter användning av alla autentiseringsmetoder som administratören väljer att aktivera.

| Metod | Standardinställningar för säkerhet | Villkorlig åtkomst |
| --- | --- | --- |
| Meddelande via mobilapp | X | X |
| Verifierings kod från mobilapp eller maskinvaru-token | X * * | X |
| Textmeddelande till telefon |   | X |
| Ring till telefon |   | X |
| Applösenord |   | X * * _ |

- _ * Användare kan använda verifierings koder från Microsoft Authenticator-appen, men kan bara registreras med meddelande alternativet.
- * * _ Applösenord är bara tillgängliga i MFA per användare med äldre autentiseringar endast om de aktive ras av administratörer.

### <a name="disabled-mfa-status"></a>Inaktiverat MFA-status

Om din organisation är en tidigare användare av per-baserad Azure AD-Multi-Factor Authentication ska du inte varnas för att inte se användare i a _ *Enabled** eller **framtvingad** status om du tittar på sidan Multi-factor auth-status. **Disabled** är rätt status för användare som använder säkerhets standarder eller villkorlig åtkomst baserat Azure AD Multi-Factor Authentication.

### <a name="conditional-access"></a>Villkorlig åtkomst

Du kan använda villkorlig åtkomst för att konfigurera principer som liknar säkerhets inställningarna, men med mer detaljerad information, inklusive användar undantag, som inte är tillgängliga i säkerhets inställningarna. Om du använder villkorlig åtkomst och har principer för villkorlig åtkomst aktive rad i din miljö är säkerhets inställningar inte tillgängliga för dig. Om du har en licens som ger villkorlig åtkomst men inte har några principer för villkorlig åtkomst aktive rad i din miljö, är du välkommen att använda säkerhets inställningar tills du aktiverar principer för villkorlig åtkomst. Mer information om Azure AD-licensiering finns på [prissättnings sidan för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Varnings meddelande om att du kan ha säkerhets inställningar eller villkorlig åtkomst inte båda](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Här följer stegvisa guider om hur du kan använda villkorlig åtkomst för att konfigurera likvärdiga principer för de principer som aktive ras av säkerhets inställningar:

- [Kräv MFA för administratörer](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [Kräv MFA för Azure-hantering](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Blockera äldre autentisering](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [Kräv MFA för alla användare](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Kräv Azure AD MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – kräver Azure AD Identity Protection del av Azure AD Premium P2.

## <a name="enabling-security-defaults"></a>Aktivera säkerhets inställningar

Så här aktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com)   som säkerhets administratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory**   >  **Egenskaper**.
1. Välj **hantera säkerhets inställningar**.
1. Ställ in **Aktivera säkerhets inställningar** växla till **Ja**.
1. Välj **Spara**.

## <a name="disabling-security-defaults"></a>Inaktivera säkerhets inställningar

Organisationer som väljer att implementera principer för villkorlig åtkomst som ersätter säkerhets standarder måste inaktivera säkerhets inställningar. 

![Varnings meddelande inaktivera säkerhets inställningar för att aktivera villkorlig åtkomst](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Så här inaktiverar du säkerhets inställningar i din katalog:

1. Logga in på [Azure Portal](https://portal.azure.com)   som säkerhets administratör, administratör för villkorlig åtkomst eller global administratör.
1. Bläddra till **Azure Active Directory**   >  **Egenskaper**.
1. Välj **hantera säkerhets inställningar**.
1. Ställ in **Aktivera säkerhets standardvärden** växla till **Nej**.
1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)