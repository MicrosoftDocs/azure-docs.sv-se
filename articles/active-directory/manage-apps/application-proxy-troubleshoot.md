---
title: Felsöka programproxy | Microsoft Docs
description: Beskriver hur du felsöker fel i Azure AD-programproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257971"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Felsöka problem med programproxy och felmeddelanden

När du felsöker problem med programproxyn rekommenderar vi att du börjar med att granska fel söknings flödet, [Felsöka anslutnings problem med Application Proxy](application-proxy-debug-connectors.md)för att avgöra om Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet följer du fel söknings flödet i fel söknings program för [programproxy](application-proxy-debug-apps.md).

Om fel uppstår vid åtkomst till ett publicerat program eller i publicerings program, kontrollerar du följande alternativ för att se om Microsoft Azure AD programproxy fungerar korrekt:

* Öppna konsolen Windows-tjänster. Kontrol lera att **Connector-tjänsten Microsoft AAD Application Proxy** är aktive rad och körs. Du kanske också vill titta på sidan Egenskaper för tjänsten Application Proxy, som du ser i följande bild:  
  ![Microsoft AAD Application Proxy Connector Fönstret Egenskaper skärm bild](./media/application-proxy-troubleshoot/connectorproperties.png)
* Öppna Loggboken och leta efter Application Proxy Connector-händelser i **program och tjänster loggar**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**  >  **admin**.
* Om det behövs kan du använda mer detaljerade loggar genom [att aktivera sessionerna för Application Proxy Connector](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Sidan återges inte korrekt
Du kan ha problem med program åter givningen eller fungera felaktigt utan att ta emot specifika fel meddelanden. Detta kan inträffa om du har publicerat artikel Sök vägen, men programmet kräver innehåll som finns utanför den sökvägen.

Om du till exempel publicerar sökvägen `https://yourapp/app` , men programmet anropar bilder i `https://yourapp/media` , återges de inte. Se till att publicera programmet med den högsta sökvägen måste du ta med allt relevant innehåll. I det här exemplet är det `http://yourapp/` .

## <a name="connector-errors"></a>Anslutnings fel

Om registreringen Miss lyckas under installationen av anslutnings guiden, finns det två sätt att Visa orsaken till misslyckandet. Titta i händelse loggen under **program-och tjänst Logs\Microsoft\AadApplicationProxy\Connector\Admin** eller kör följande kommando i Windows PowerShell:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

När du har hittat kopplings felet från händelse loggen använder du den här tabellen med vanliga fel för att lösa problemet:

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att registrera anslutningen: kontrol lera att du har aktiverat Application Proxy i Azure-Hanteringsportal och att du har angett ditt Active Directory användar namn och lösen ord korrekt. Fel: ett eller flera fel inträffade. | Om du stängde registrerings fönstret utan att logga in på Azure AD, kör du anslutnings guiden igen och registrerar anslutningen. <br><br> Om registrerings fönstret öppnas och stängs omedelbart utan att du behöver logga in får du förmodligen det här felet. Felet uppstår när ett nätverks fel uppstår i systemet. Kontrol lera att det är möjligt att ansluta från en webbläsare till en offentlig webbplats och att portarna är öppna enligt vad som anges i [Programproxyns krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Rensa fel visas i registrerings fönstret. Kan inte fortsätta | Om du ser det här felet, stängs fönstret och du har angett fel användar namn eller lösen ord. Försök igen. |
| Det gick inte att registrera anslutningen: kontrol lera att du har aktiverat Application Proxy i Azure-Hanteringsportal och att du har angett ditt Active Directory användar namn och lösen ord korrekt. Fel: ' AADSTS50059: ingen klient identifierings information hittades i begäran eller underförstådd av angivna autentiseringsuppgifter och sökning efter tjänstens huvud namn URI har misslyckats. | Du försöker logga in med ett Microsoft-konto och inte en domän som är en del av organisations-ID: t för den katalog som du försöker få åtkomst till. Kontrol lera att administratören är en del av samma domän namn som klient domänen, till exempel om Azure AD-domänen är contoso.com bör administratören vara admin@contoso.com . |
| Det gick inte att hämta den aktuella körnings principen för att köra PowerShell-skript. | Om installationen av anslutningen Miss lyckas kontrollerar du att PowerShell-körnings principen inte är inaktive rad. <br><br>1. öppna grupprincip redigeraren.<br>2. gå till **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Windows PowerShell** och dubbelklicka på **Aktivera skript körning**.<br>3. körnings principen kan anges till antingen **inte konfigurerad** eller **aktive rad**. Om inställningen är **aktive rad**, se till att körnings principen är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller **tillåta alla skript** under alternativ. |
| Anslutningen kunde inte ladda ned konfigurationen. | Anslutningens klient certifikat, som används för autentisering, har upphört att gälla. Detta kan också inträffa om du har installerat anslutningen bakom en proxyserver. I det här fallet kan anslutningen inte komma åt Internet och kommer inte att kunna tillhandahålla program till fjärran vändare. Förnya förtroende manuellt med `Register-AppProxyConnector` cmdleten i Windows PowerShell. Om din anslutning ligger bakom en proxy är det nödvändigt att ge Internet åtkomst till anslutnings kontona "nätverks tjänster" och "lokalt system". Detta kan åstadkommas antingen genom att ge dem åtkomst till proxyservern eller genom att ställa in dem för att kringgå proxyn. |
| Det gick inte att registrera anslutningen: kontrol lera att du är program administratör för din Active Directory att registrera anslutningen. Fel: "registrerings förfrågan nekades." | Det alias som du försöker logga in med är inte en administratör på den här domänen. Din anslutning installeras alltid för den katalog som äger användarens domän. Kontrol lera att det administratörs konto som du försöker logga in med har minst program administratörs behörighet till Azure AD-klienten. |
| Anslutningen kunde inte ansluta till tjänsten på grund av nätverks problem. Anslutnings försöket försökte få åtkomst till följande URL. | Anslutningen kan inte ansluta till Application Proxy-moln tjänsten. Detta kan inträffa om du har en brand Väggs regel som blockerar anslutningen. Kontrol lera att du har tillåtit åtkomst till rätt portar och URL: er som anges i [Programproxyns krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-fel

Den här tabellen beskriver de vanligaste felen som kommer från Kerberos-installationen och konfigurationen och innehåller förslag på lösningar.

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att hämta den aktuella körnings principen för att köra PowerShell-skript. | Om installationen av anslutningen Miss lyckas kontrollerar du att PowerShell-körnings principen inte är inaktive rad.<br><br>1. öppna grupprincip redigeraren.<br>2. gå till **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Windows PowerShell** och dubbelklicka på **Aktivera skript körning**.<br>3. körnings principen kan anges till antingen **inte konfigurerad** eller **aktive rad**. Om inställningen är **aktive rad**, se till att körnings principen är inställd på att antingen **tillåta lokala skript och fjärrsignerade skript** eller **tillåta alla skript** under alternativ. |
| 12008 – Azure AD överskred det maximala antalet tillåtna Kerberos-autentiseringsförsök till backend-servern. | Det här felet kan indikera felaktig konfiguration mellan Azure AD och backend-programservern, eller ett problem i tids-och datum konfigurationen på båda datorerna. Backend-servern avböjde Kerberos-biljetten som skapats av Azure AD. Kontrol lera att Azure AD och backend-programservern är korrekt konfigurerade. Se till att tids-och datum konfigurationen på Azure AD och backend-programservern är synkroniserade. |
| 13016 – Azure AD kan inte hämta en Kerberos-biljett åt användaren eftersom det inte finns något UPN i Edge-token eller i åtkomst-cookien. | Det har uppstått ett problem med STS-konfigurationen. Korrigera UPN-anspråks konfigurationen i STS. |
| 13019 – Azure AD kan inte hämta en Kerberos-biljett åt användaren på grund av följande allmänna API-fel. | Den här händelsen kan indikera felaktig konfiguration mellan Azure AD och domänkontrollanten, eller ett problem i tids-och datum konfigurationen på båda datorerna. Domänkontrollanten avböjde Kerberos-biljetten som skapats av Azure AD. Kontrol lera att Azure AD och backend-programservern har kon figurer ATS korrekt, särskilt SPN-konfigurationen. Kontrol lera att Azure AD är domän ansluten till samma domän som domänkontrollanten för att säkerställa att domänkontrollanten upprättar förtroende med Azure AD. Se till att tids-och datum konfigurationen på Azure AD och domänkontrollanten är synkroniserade. |
| 13020 – Azure AD kan inte hämta en Kerberos-biljett åt användaren eftersom backend-serverns SPN inte har definierats. | Den här händelsen kan indikera felaktig konfiguration mellan Azure AD och domänkontrollanten, eller ett problem i tids-och datum konfigurationen på båda datorerna. Domänkontrollanten avböjde Kerberos-biljetten som skapats av Azure AD. Kontrol lera att Azure AD och backend-programservern har kon figurer ATS korrekt, särskilt SPN-konfigurationen. Kontrol lera att Azure AD är domän ansluten till samma domän som domänkontrollanten för att säkerställa att domänkontrollanten upprättar förtroende med Azure AD. Se till att tids-och datum konfigurationen på Azure AD och domänkontrollanten är synkroniserade. |
| 13022 – Azure AD kan inte autentisera användaren eftersom backend-servern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel. | Den här händelsen kan indikera felaktig konfiguration mellan Azure AD och backend-programservern, eller ett problem i tids-och datum konfigurationen på båda datorerna. Backend-servern avböjde Kerberos-biljetten som skapats av Azure AD. Kontrol lera att Azure AD och backend-programservern är korrekt konfigurerade. Se till att tids-och datum konfigurationen på Azure AD och backend-programservern är synkroniserade. Mer information finns i [Felsöka konfigurationer med Kerberos-begränsad delegering för Application Proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Slut användar fel

Den här listan innehåller fel som slutanvändarna kan stöta på när de försöker komma åt appen och fungerar inte. 

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Sidan kan inte visas på webbplatsen. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om programmet är ett IWA-program. Det angivna SPN-namnet för det här programmet kan vara felaktigt. Kontrol lera att SPN som har kon figurer ATS för det här programmet är korrekt för IWA-appar. |
| Sidan kan inte visas på webbplatsen. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om programmet är ett OWA-program. Detta kan bero på något av följande:<br><li>Det angivna tjänst namnet för det här programmet är felaktigt. Kontrol lera att SPN som har kon figurer ATS för det här programmet är korrekt.</li><li>Användaren som försökte få åtkomst till programmet använder en Microsoft-konto i stället för det riktiga företags kontot för att logga in, eller användaren är en gäst användare. Se till att användaren loggar in med sitt företags konto som matchar domänen för det publicerade programmet. Användare och gäster i Microsoft-kontot har inte åtkomst till IWA-program.</li><li>Användaren som försökte komma åt programmet har inte definierats korrekt för det här programmet på den lokala sidan. Se till att den här användaren har rätt behörigheter som definierats för det här server dels programmet på den lokala datorn. |
| Det går inte att öppna den här företags appen. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att tilldela användaren åtkomst till det här programmet. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om de använder Microsoft-konton i stället för företagets konto för att logga in. Gäst användare kan också få det här felet. Användare och gäster i Microsoft-kontot har inte åtkomst till IWA-program. Se till att användaren loggar in med sitt företags konto som matchar domänen för det publicerade programmet.<br><br>Du kanske inte har tilldelat användaren för det här programmet. Gå till fliken **program** och tilldela användaren eller användar gruppen till det här programmet under **användare och grupper**. |
| Den här företags appen är inte tillgänglig just nu. Försök igen senare... Tids gränsen för anslutningen uppnåddes. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om de inte är korrekt definierade för det här programmet på den lokala sidan. Se till att användarna har rätt behörigheter enligt definitionen för det här server dels programmet på den lokala datorn. |
| Det går inte att öppna den här företags appen. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att användaren har en licens för Azure Active Directory Premium. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om de inte explicit hade tilldelats en Premium-licens av prenumerantens administratör. Gå till fliken Active Directory **licenser** för prenumerant och kontrol lera att den här användaren eller användar gruppen har tilldelats en Premium-licens. |
| Det gick inte att hitta en server med det angivna värd namnet. | Användaren kan få det här felet när du försöker komma åt appen som du har publicerat om programmets anpassade domän inte är korrekt konfigurerad. Se till att du har överfört ett certifikat för domänen och konfigurerat DNS-posten korrekt genom att följa stegen i [arbeta med anpassade domäner i Azure AD-programproxy](application-proxy-configure-custom-domain.md) |
|Tillåts inte: den här företags appen kan inte nås eller så gick det inte att auktorisera användaren. Se till att användaren har definierats i din lokala AD och att användaren har åtkomst till appen i din lokala AD. | Detta kan vara ett problem med åtkomst till auktoriseringsinformation. mer information finns i [vissa program och API: er kräver åtkomst till auktoriseringsinformation på konto objekt]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). I en kortfattat så Jenkins lägger du till dator kontot för App proxy-anslutning till "Windows Authorization Access Group"-gruppen "Builtin" för att lösa problemet. |

## <a name="my-error-wasnt-listed-here"></a>Mitt fel visas inte här

Om du stöter på ett fel eller problem med Azure AD-programproxy som inte visas i den här fel söknings guiden, vill vi gärna höra om det. Skicka ett e-postmeddelande till vårt [feedback-team](mailto:aadapfeedback@microsoft.com) med information om det fel som du har påträffat.

## <a name="see-also"></a>Se även
* [Aktivera programproxy för Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
