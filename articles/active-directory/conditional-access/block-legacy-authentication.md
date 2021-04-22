---
title: Blockera äldre autentisering – Azure Active Directory
description: Lär dig hur du förbättrar din säkerhetsstatus genom att blockera äldre autentisering med villkorlig åtkomst i Azure AD.
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
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861813"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Anvisningar: Blockera föråldrad autentisering för Azure AD med villkorsstyrd åtkomst   

För att ge användarna enkel åtkomst till dina molnappar stöder Azure Active Directory (Azure AD) en mängd olika autentiseringsprotokoll, inklusive äldre autentisering. Äldre protokoll stöder dock inte multifaktorautentisering (MFA). MFA är i många miljöer ett vanligt krav för att hantera identitetsstöld. 

Alex Weinert, chef för Identity Security på Microsoft, i blogginlägget från [](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) den 12 mars 2020 Nya verktyg för att blockera äldre autentisering i din organisation betonar varför organisationer ska blockera äldre autentisering och vilka andra verktyg Som Microsoft tillhandahåller för att utföra den här uppgiften:

> För att MFA ska vara effektivt måste du också blockera äldre autentisering. Det beror på att äldre autentiseringsprotokoll som POP, SMTP, IMAP och MAPI inte kan tillämpa MFA, vilket gör dem till prioriterade startpunkter för angripare som angriper din organisation...
> 
>... Siffrorna för äldre autentisering från en analys av Azure Active Directory(Azure AD)-trafik är stark:
> 
> - Över 99 procent av lösenordsattackerna använder äldre autentiseringsprotokoll
> - Mer än 97 procent av fyllningsattackerna för autentiseringsuppgifter använder äldre autentisering
> - Azure AD-konton i organisationer som har inaktiverat äldre autentisering upplever 67 procent färre komprometteringar än de där äldre autentisering är aktiverat
>

Om din miljö är redo att blockera äldre autentisering för att förbättra klientorganisationens skydd kan du uppnå det här målet med villkorlig åtkomst. Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som blockerar äldre autentisering för din klientorganisation. Kunder utan licenser som inkluderar villkorsstyrd åtkomst kan använda [standardinställningar för säkerhet](../fundamentals/concept-fundamentals-security-defaults.md)) för att blockera äldre autentisering.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med de [grundläggande begreppen för](overview.md) villkorsstyrd åtkomst i Azure AD.

## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de mest använda autentiserings- och auktoriseringsprotokollen, inklusive äldre autentisering. Äldre autentisering avser protokoll som använder grundläggande autentisering. Dessa protokoll kan vanligtvis inte framtvinga någon typ av autentisering med andra faktor. Exempel för appar som baseras på äldre autentisering är:

- Äldre Microsoft Office appar
- Appar som använder e-postprotokoll som POP, IMAP och SMTP

Enkelfaktorautentisering (till exempel användarnamn och lösenord) räcker inte för dessa dagar. Lösenord är dåliga eftersom de är lätta att gissa och vi (människor) är dåliga på att välja bra lösenord. Lösenord är också sårbara för olika attacker, till exempel nätfiske och lösenordsattacker. En av de enklaste sakerna du kan göra för att skydda mot lösenordshot är att implementera multifaktorautentisering (MFA). Med MFA räcker inte enbart lösenordet för att autentisera och komma åt data, även om en angripare får tillgång till en användares lösenord.

Hur kan du förhindra att appar som använder äldre autentisering kommer åt klientorganisationens resurser? Rekommendationen är att blockera dem med en princip för villkorsstyrd åtkomst. Om det behövs tillåter du endast att vissa användare och specifika nätverksplatser använder appar som baseras på äldre autentisering.

Principer för villkorlig åtkomst tillämpas när den första faktorautentisering har slutförts. Villkorlig åtkomst är därför inte avsett som ett förstalinje försvar för scenarier som DoS-attacker (Denial-of-Service), men kan använda signaler från dessa händelser (till exempel risknivån för inloggning, platsen för begäran och så vidare) för att fastställa åtkomsten.

## <a name="implementation"></a>Implementering

I det här avsnittet beskrivs hur du konfigurerar en princip för villkorlig åtkomst för att blockera äldre autentisering. 

### <a name="legacy-authentication-protocols"></a>Äldre autentiseringsprotokoll

Följande alternativ betraktas som äldre autentiseringsprotokoll

- Autentiserad SMTP – Används av POP- och IMAP-klienter för att skicka e-postmeddelanden.
- Automatisk identifiering – Används av Outlook- och EAS-klienter för att hitta och ansluta till postlådor i Exchange Online.
- Exchange ActiveSync (EAS) – Används för att ansluta till postlådor i Exchange Online.
- Exchange Online PowerShell – Används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Anvisningar finns i Ansluta [till Exchange Online PowerShell med multifaktorautentisering.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- Exchange Web Services (EWS) – Ett programmeringsgränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.
- IMAP4 – används av IMAP-e-postklienter.
- MAPI över HTTP (MAPI/HTTP) – används av Outlook 2010 och senare.
- Offline Address Book (OAB) – En kopia av samlingar med adresslistor som hämtas och används av Outlook.
- Outlook Anywhere (RPC över HTTP) – Används av Outlook 2016 och tidigare.
- Outlook-tjänsten – Används av e-post- och kalenderappen för Windows 10.
- POP3 – används av POP-e-postklienter.
- Reporting Web Services – Används för att hämta rapportdata i Exchange Online.
- Andra klienter – Andra protokoll som identifieras som använder äldre autentisering.

Mer information om dessa autentiseringsprotokoll och -tjänster finns [i Rapporter om inloggningsaktiviteter i Azure Active Directory portalen.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

### <a name="identify-legacy-authentication-use"></a>Identifiera användning av äldre autentisering

Innan du kan blockera äldre autentisering i din katalog måste du först förstå om användarna har appar som använder äldre autentisering och hur de påverkar din övergripande katalog. Azure AD-inloggningsloggar kan användas för att förstå om du använder äldre autentisering.

1. Gå till **Azure Portal**  >  **Azure Active Directory**  >  **inloggningar**.
1. Lägg till kolumnen Klientapp om den inte visas genom att klicka på Columns Client App   >  **(Klientapp för kolumner).**
1. **Lägga till filter**  >  **Client App** > alla äldre autentiseringsprotokoll. Välj utanför filtreringsdialogrutan för att tillämpa dina val och stänga dialogrutan.
1. Om du har [](../reports-monitoring/concept-all-sign-ins.md)aktiverat den nya förhandsversionen av inloggningsaktivitetsrapporterna upprepar du ovanstående steg även på fliken Användarloggar **(inte interaktiva).**

Filtrering visar bara inloggningsförsök som har gjorts av äldre autentiseringsprotokoll. Om du klickar på varje enskilt inloggningsförsök visas mer information. Fältet **Klientapp** under fliken **Grundläggande information** anger vilket äldre autentiseringsprotokoll som användes.

Dessa loggar visar vilka användare som fortfarande är beroende av äldre autentisering och vilka program som använder äldre protokoll för att göra autentiseringsbegäranden. För användare som inte visas i de här loggarna och som har bekräftats att de inte använder äldre autentisering implementerar du en princip för villkorlig åtkomst endast för dessa användare.

## <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

Det finns två sätt att använda principer för villkorlig åtkomst för att blockera äldre autentisering.

- [Direkt blockering av äldre autentisering](#directly-blocking-legacy-authentication)
- [Indirekt blockering av äldre autentisering](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Direkt blockering av äldre autentisering

Det enklaste sättet att blockera äldre autentisering i hela organisationen är genom att konfigurera en princip för villkorsstyrd åtkomst som gäller specifikt för äldre autentiseringsklienter och blockerar åtkomst. När du tilldelar användare och program till principen ska du undanta användare och tjänstkonton som fortfarande behöver logga in med äldre autentisering. Konfigurera villkoret för klientappar genom att välja **Exchange ActiveSync-klienter** och **Andra klienter.** Om du vill blockera åtkomst för dessa klientappar konfigurerar du åtkomstkontrollerna till Blockera åtkomst.

![Klientappvillkor som konfigurerats för att blockera äldre autentisering](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Indirekt blockering av äldre autentisering

Även om din organisation inte är redo att blockera äldre autentisering i hela organisationen bör du se till att inloggningar som använder äldre autentisering inte kringgår principer som kräver beviljandekontroller, till exempel att kräva multifaktorautentisering eller kompatibla/Hybrid Azure AD-anslutna enheter. Äldre autentiseringsklienter har inte stöd för att skicka MFA, enhetsefterlevnad eller ansluta statusinformation till Azure AD under autentiseringen. Tillämpa därför principer med beviljandekontroller för alla klientprogram så att äldre autentiseringsbaserade inloggningar som inte uppfyller beviljandekontrollerna blockeras. Med allmän tillgänglighet för villkoret för klientappar i augusti 2020 gäller nyligen skapade principer för villkorlig åtkomst för alla klientappar som standard.

![Standardkonfiguration för klientappars villkor](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Det här bör du veta

Om åtkomst blockeras **med andra** klienter blockeras även Exchange Online PowerShell och Dynamics 365 med hjälp av grundläggande autentisering.

Om du konfigurerar **en princip för Andra** klienter blockeras hela organisationen från vissa klienter som SPConnect. Det här blocket beror på att äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för större Office-program som äldre Office-klienter.

Det kan ta upp till 24 timmar innan principen har verkställts.

Du kan välja alla tillgängliga beviljandekontroller för **villkoret Andra** klienter. Slutanvändarupplevelsen är dock alltid densamma – blockerad åtkomst.

### <a name="sharepoint-online-and-b2b-guest-users"></a>SharePoint Online- och B2B-gästanvändare

För att blockera B2B-användaråtkomst via äldre autentisering till SharePoint Online måste organisationer inaktivera äldre autentisering på SharePoint med hjälp av PowerShell-kommandot och ange `Set-SPOTenant` `-LegacyAuthProtocolsEnabled` parametern till `$false` . Mer information om hur du anger den här parametern finns i SharePoint [PowerShell-referensdokumentet om Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Nästa steg

- [Fastställa påverkan med läget endast för villkorlig åtkomstrapport](howto-conditional-access-insights-reporting.md)
- Om du inte är bekant med att konfigurera principer för villkorsstyrd åtkomst ännu kan du se Kräv [MFA för](../authentication/tutorial-enable-azure-mfa.md) specifika appar med Azure Active Directory villkorlig åtkomst för ett exempel.
- Mer information om stöd för modern autentisering finns i Så här fungerar modern autentisering för [Office 2013- och Office 2016-klientappar](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Konfigurera en enhet eller ett program med flera funktioner för att skicka e-post med Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)