---
title: Blockera äldre autentisering – Azure Active Directory
description: Lär dig hur du kan förbättra din säkerhets position genom att blockera äldre autentisering med villkorlig åtkomst i Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f98e3d6c7997d9cae2737b25f4323021e29bfb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98892447"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Gör så här: blockera äldre autentisering till Azure AD med villkorlig åtkomst   

Azure Active Directory (Azure AD) stöder en mängd olika autentiseringsprotokoll, inklusive äldre autentisering, för att ge dina användare enkel åtkomst till dina molnappar. Äldre protokoll stöder dock inte Multi-Factor Authentication (MFA). MFA är i många miljöer ett gemensamt krav för att lösa identitets stölder. 

Alex Weinert, chef för identitets säkerhet på Microsoft, i den 12 mars 2020 blogg inlägget [nya verktyg för att blockera äldre autentisering i organisationen](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) , betonar varför organisationer ska blockera äldre autentisering och vilka ytterligare verktyg som Microsoft tillhandahåller för att utföra den här uppgiften:

> För att MFA ska vara effektiv måste du också blockera äldre autentisering. Detta beror på att äldre autentiseringsprotokoll, till exempel POP, SMTP, IMAP och MAPI, inte kan genomdriva MFA, vilket gör dem till önskade start punkter för att angripare angripa din organisation...
> 
>... Numren på tidigare autentisering från en analys av Azure Active Directory (Azure AD)-trafik är stark:
> 
> - Över 99 procent av angrepp vid lösen ords spridning använder äldre autentiseringsprotokoll
> - Över 97 procent av attacker som är av stor behörighet använder äldre autentisering
> - Azure AD-konton i organisationer som har inaktiverat äldre autentiserings upplevelser 67 procent färre kompromisser än de där äldre autentisering är aktiverat
>

Om din miljö är redo att blockera äldre autentisering för att förbättra din klients skydd kan du uppnå det här målet med villkorlig åtkomst. Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som blockerar äldre autentisering för din klient.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med de [grundläggande begreppen](overview.md) för villkorlig åtkomst i Azure AD.

## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de vanligaste autentiseringsprotokollen för autentisering och auktorisering, inklusive äldre autentisering. Äldre autentisering syftar på protokoll som använder grundläggande autentisering. Dessa protokoll kan vanligt vis inte tillämpa någon typ av andra Factor Authentication. Exempel på appar som baseras på äldre autentisering är:

- Äldre Microsoft Office-appar
- Appar som använder e-postprotokoll som POP, IMAP och SMTP

Autentisering med en enda faktor (till exempel användar namn och lösen ord) är inte tillräckligt för dessa dagar. Lösen orden är dåliga eftersom de är lätta att gissa och vi (människa) är felaktiga vid val av bra lösen ord. Lösen ord är också sårbara för olika attacker, t. ex. nätfiske och lösen ords spridning. Ett av de enklaste saker du kan göra för att skydda mot lösen ords hot är att implementera Multi-Factor Authentication (MFA). Med MFA, även om en angripare får till gång till en användares lösen ord, så är lösen ordet inte tillräckligt för att autentisera och komma åt data.

Hur kan du förhindra att appar använder äldre autentisering för att komma åt din klients resurser? Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan endast vissa användare och specifika nätverks platser använda appar som baseras på äldre autentisering.

Principer för villkorlig åtkomst tillämpas när den första faktorn har slutförts. Därför är villkorlig åtkomst inte avsedd som ett första rad skydd för scenarier som denial-of-service (DoS)-attacker, men kan använda signaler från dessa händelser (till exempel inloggnings risk nivån, platsen för begäran och så vidare) för att fastställa åtkomst.

## <a name="implementation"></a>Implementering

I det här avsnittet beskrivs hur du konfigurerar en princip för villkorlig åtkomst för att blockera äldre autentisering. 

### <a name="legacy-authentication-protocols"></a>Bakåtkompatibla autentiseringsprotokoll

Följande alternativ anses vara bakåtkompatibla autentiseringsprotokoll

- Autentiserad SMTP – används av POP-och IMAP-klienter för att skicka e-postmeddelanden.
- Identifiera automatiskt – används av Outlook-och EAS-klienter för att hitta och ansluta till post lådor i Exchange Online.
- Exchange ActiveSync (EAS) – används för att ansluta till post lådor i Exchange Online.
- Exchange Online PowerShell – används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Mer information finns i [ansluta till Exchange Online PowerShell med Multi-Factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) – ett programmerings gränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.
- IMAP4 – används av IMAP e-postklienter.
- MAPI över HTTP (MAPI/HTTP) – används av Outlook 2010 och senare.
- Offlineadressbok (OAB) – en kopia av adress list samlingar som hämtas och används av Outlook.
- Outlook överallt (RPC över HTTP) – används av Outlook 2016 och tidigare.
- Outlook service – används av appen e-post och kalender för Windows 10.
- POP3 – används av POP-e-postklienter.
- Repor ting Web Services – används för att hämta rapport data i Exchange Online.
- Andra klienter – andra protokoll som identifieras som användning av äldre autentisering.

Mer information om dessa autentiseringsprotokoll och tjänster finns i rapporter om [inloggnings aktiviteter i Azure Active Directory-portalen](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identifiera användning av äldre autentisering

Innan du kan blockera äldre autentisering i din katalog måste du först förstå om dina användare har appar som använder äldre autentisering och hur de påverkar den övergripande katalogen. Inloggnings loggar för Azure AD kan användas för att förstå om du använder äldre autentisering.

1. Navigera till **Azure Portal**  >  **Azure Active Directory**  >  **inloggningar**.
1. Lägg till kolumnen klient program om den inte visas genom att klicka på **kolumner**  >  **klient program**.
1. **Lägg till filter**  >  **Klient program** > väljer alla bakåtkompatibla autentiseringsprotokoll. Välj utanför filtrerings dialog rutan för att tillämpa dina val och stänga dialog rutan.
1. Om du har aktiverat [förhands granskningen av rapportens nya inloggnings aktiviteter](../reports-monitoring/concept-all-sign-ins.md)upprepar du ovanstående steg även på fliken **användar inloggning (icke-interaktiv)** .

Vid filtrering visas bara inloggnings försök som gjorts av äldre autentiseringsprotokoll. Om du klickar på varje enskilt inloggnings försök visas ytterligare information. Fältet **klient app** på fliken **grundläggande information** visar vilket äldre autentiseringsprotokoll som användes.

I dessa loggar anges vilka användare som fortfarande är beroende av tidigare autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i dessa loggar och som bekräftas att inte använda äldre autentisering, implementera endast en princip för villkorlig åtkomst för dessa användare.

## <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

Det finns två sätt att använda principer för villkorlig åtkomst för att blockera äldre autentisering.

- [Blockera äldre autentisering direkt](#directly-blocking-legacy-authentication)
- [Indirekt blockera äldre autentisering](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Blockera äldre autentisering direkt

Det enklaste sättet att blockera äldre autentisering i hela organisationen är genom att konfigurera en princip för villkorlig åtkomst som gäller specifikt för äldre autentiserings klienter och blockerar åtkomst. När du tilldelar användare och program till principen ska du se till att undanta användare och tjänst konton som fortfarande behöver logga in med äldre autentisering. Konfigurera villkoret för klient program genom att välja **Exchange ActiveSync-klienter** och **andra klienter**. Konfigurera åtkomst kontroller för att blockera åtkomst för att blockera åtkomst för dessa klient program.

![Villkor för klient program har kon figurer ATS för att blockera äldre autentisering](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Indirekt blockera äldre autentisering

Även om din organisation inte är redo att blockera äldre autentisering i hela organisationen, bör du se till att inloggningar som använder äldre autentisering inte kringgår principer som kräver beviljande kontroller, till exempel kräver Multi-Factor Authentication eller kompatibla/hybrid Azure AD-anslutna enheter. Under autentiseringen har äldre autentiserings klienter inte stöd för att skicka MFA, enhetskompatibilitet eller ansluta statusinformation till Azure AD. Använd därför principer för att bevilja kontroller till alla klient program så att äldre autentiserings inloggningar som inte kan uppfylla tilldelnings kontrollerna blockeras. Med den allmänna tillgängligheten för klient program villkor i augusti 2020, gäller nyligen skapade principer för villkorlig åtkomst för alla klient program som standard.

![Standard konfiguration för klient program villkor](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Det här bör du veta

Att blockera åtkomst med **andra klienter** blockerar också Exchange Online PowerShell och Dynamics 365 med Basic auth.

Att konfigurera en princip för **andra klienter** blockerar hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för större Office-program som äldre Office-klienter.

Det kan ta upp till 24 timmar innan principen börjar gälla.

Du kan välja alla tillgängliga beviljade kontroller för villkoret **andra klienter** . slut användar upplevelsen är dock alltid samma blockerad åtkomst.

### <a name="sharepoint-online-and-b2b-guest-users"></a>SharePoint Online-och B2B-gäst användare

För att blockera B2B-användare via äldre autentisering till SharePoint Online måste organisationer inaktivera äldre autentisering i SharePoint med hjälp av `Set-SPOTenant` PowerShell-kommandot och ange `-LegacyAuthProtocolsEnabled` parametern till `$false` . Mer information om hur du anger den här parametern finns i referens dokumentet för SharePoint PowerShell gällande [Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Nästa steg

- [Bestäm inverkan med endast rapport läge för villkorlig åtkomst](howto-conditional-access-insights-reporting.md)
- Om du inte är bekant med att konfigurera principer för villkorlig åtkomst ännu kan du läsa [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](../authentication/tutorial-enable-azure-mfa.md) för ett exempel.
- Mer information om stöd för modern autentisering finns i [så här fungerar modern autentisering för office 2013 och office 2016-klient program](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Konfigurera en multifunktions enhet eller ett program för att skicka e-post med hjälp av Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)