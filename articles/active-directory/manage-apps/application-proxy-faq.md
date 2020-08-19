---
title: Vanliga frågor och svar om Azure AD-programproxy | Microsoft Docs
description: Läs vanliga frågor och svar om hur du använder Azure-AD-programproxy för att publicera interna, lokala program till fjärran vändare.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: edf51dad768e8d8b5ea5dc6c1eff88f43f0f6b70
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589171"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Vanliga frågor och svar om Active Directory (Azure AD) Application Proxy

På den här sidan besvaras vanliga frågor om Azure Active Directory (Azure AD) Application Proxy.

## <a name="enabling-azure-ad-application-proxy"></a>Aktivera Azure AD-programproxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Vilken licens krävs för att använda Azure AD-programproxy?

Om du vill använda Azure AD-programproxy måste du ha en Azure AD Premium P1-eller P2-licens. Mer information om licensiering finns i [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>Vad händer med Azure AD-programproxy i min klient, om min licens upphör att gälla?
Om licensen går ut inaktive ras programproxyn automatiskt. Programinformationen sparas i upp till ett år.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Varför är knappen Aktivera programproxy nedtonad?

Se till att du har minst en Azure AD Premium P1-eller P2-licens och att en Azure AD-programproxy-anslutning är installerad. När du har installerat din första anslutning aktive ras Azure AD-programproxy-tjänsten automatiskt.

## <a name="connector-configuration"></a>Anslutnings konfiguration

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Kan Application Proxy Connector-tjänster köras i ett annat användar sammanhang än standard?

Nej, det här scenariot stöds inte. Standardinställningarna är:

- Microsoft AAD Application Proxy Connector-WAPCSvc-Network Service
- Microsoft AAD Application Proxy Connector Updater-WAPCUpdaterSvc-NT Instans\system

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Kan en gäst användare med rollen global administratör eller program administratör registrera anslutningen till klienten (gäst)?

Nej, det är för närvarande inte möjligt. Registrerings försöket görs alltid på användarens hem klient.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Mitt backend-program finns på flera webb servrar och kräver varaktighet (User session persistence). Hur kan jag få en beständig session? 

Rekommendationer finns i [hög tillgänglighet och belastnings utjämning för dina Application Proxy-kopplingar och-program](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Stöds TLS-avslutning (TLS/HTTPS-kontroll eller acceleration) på trafik från anslutnings servrarna till Azure?

Application Proxy Connector utför certifikatbaserad autentisering till Azure. TLS-avslutning (TLS/HTTPS-kontroll eller acceleration) delar denna autentiseringsmetod och stöds inte. Trafik från anslutningen till Azure måste kringgå alla enheter som utför TLS-avslutning.  

### <a name="is-tls-12-required-for-all-connections"></a>Krävs TLS 1,2 för alla anslutningar?
Ja. För att tillhandahålla den bästa krypteringen till våra kunder begränsar Application Proxy-tjänsten åtkomst till endast TLS 1,2-protokoll. Dessa ändringar uppnåddes gradvis och gällde sedan den 31 augusti 2019. Kontrol lera att alla kombinationer av klient-server och webb läsar Server har uppdaterats för att använda TLS 1,2 för att upprätthålla anslutningen till Application Proxy-tjänsten. Detta inkluderar klienter som användarna använder för att få åtkomst till program som publicerats via programproxy. Se förbereda för [TLS 1,2 i Office 365](https://docs.microsoft.com/microsoft-365/compliance/prepare-tls-1.2-in-office-365) för användbara referenser och resurser.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Kan jag placera en vidarebefordrad proxyserver mellan kopplings servrarna och backend-programservern?
Ja, det här scenariot stöds från anslutnings versionen 1.5.1526.0. Se [arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Bör jag skapa ett dedikerat konto för att registrera anslutningen med Azure AD-programproxy?

Det finns ingen anledning till. Ett globalt administratörs-eller program administratörs konto kommer att fungera. De autentiseringsuppgifter som angavs under installationen används inte efter registrerings processen. I stället utfärdas ett certifikat till anslutningen, som används för autentisering från den tidpunkten.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hur kan jag övervaka Azure AD-programproxy-anslutningens prestanda?

Det finns prestanda övervaknings räknare som installeras tillsammans med anslutnings tjänsten. Så här visar du dem:  

1. Välj **Start**, skriv "perfmon" och tryck på RETUR.
2. Välj **prestanda övervakaren** och klicka på den gröna **+** ikonen.
3. Lägg till de **Microsoft AAD Application Proxy Connector** -räknare som du vill övervaka.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Måste Azure AD-programproxy-anslutningen finnas i samma undernät som resursen?

Kopplingen måste inte finnas i samma undernät. Det behöver dock namn matchning (DNS, värd fil) till resursen och nödvändig nätverks anslutning (routning till resursen, portar som är öppna på resursen osv.). Rekommendationer finns i avsnittet [om nätverks sto pol faktorer när du använder Azure Active Directory-programproxy](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>Vilka versioner av Windows Server kan jag installera en anslutning på?
Application Proxy kräver Windows Server 2012 R2 eller senare. Det finns för närvarande en begränsning på HTTP2 för Windows Server 2019. För att kunna använda anslutningen på Windows Server 2019 måste du lägga till följande register nyckel och starta om servern:
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```


## <a name="application-configuration"></a>Tillämpningskonfiguration

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Jag får ett fel meddelande om ett ogiltigt certifikat eller möjligt fel lösen ord

När du har överfört SSL-certifikatet visas meddelandet "ogiltigt certifikat, möjligt fel lösen ord" på portalen.

Här följer några tips för att felsöka det här felet:
- Kontrol lera om det finns problem med certifikatet. Installera den på den lokala datorn. Om du inte drabbas av några problem är certifikatet lämpligt.
- Se till att lösen ordet inte innehåller några specialtecken. För testning bör lösen ordet endast innehålla tecknen 0-9, A-Z och a-z.
- Om certifikatet har skapats med Microsoft Software Key Storage Provider måste RSA-algoritmen användas.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Vad är längden på standardvärdet och "Long"-timeout för Server delen? Kan tids gränsen utökas?

Standard längden är 85 sekunder. Inställningen "Long" är 180 sekunder. Det går inte att utöka tids gränsen.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hur gör jag för att ändra landnings sidan mina program inläsningar?

På sidan program registreringar kan du ändra start sidans URL till önskad extern URL för landnings sidan. Den angivna sidan kommer att läsas in när programmet startas från Mina appar eller Office 365-portalen. Konfigurations steg finns i [Ange en anpassad start sida för publicerade appar med hjälp av Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Kan endast IIS-baserade program publiceras? Vad gäller för webb program som körs på webb servrar som inte kommer från Windows? Måste anslutningen installeras på en server med IIS installerat?

Nej, det finns inga IIS-krav för program som har publicerats. Du kan publicera webb program som körs på andra servrar än Windows Server. Men du kanske inte kan använda förautentisering med en server som inte är Windows-Server, beroende på om webb servern stöder Negotiate (Kerberos-autentisering). IIS krävs inte på den server där anslutningen är installerad.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Kan jag konfigurera Application Proxy för att lägga till HSTS-huvudet?
Programproxyn lägger inte automatiskt till HTTP Strict-Transport-Security-huvudet i HTTPS-svar, men det behåller sidhuvudet om det är i det ursprungliga svaret som skickas av det publicerade programmet. Att bevisa en inställning för att aktivera den här funktionen finns i översikten. Om du är intresse rad av en för hands version som gör det möjligt att lägga till detta i svar kan du kontakta aadapfeedback@microsoft.com Mer information.

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>När ska jag använda PrincipalsAllowedToDelegateToAccount-metoden när jag konfigurerar Kerberos-begränsad delegering (KCD)?

Metoden PrincipalsAllowedToDelegateToAccount används när anslutnings servrar finns i en annan domän än webb program tjänst kontot. Den kräver användning av resurs-baserad begränsad delegering.
Om kopplings servrarna och webb program tjänst kontot finns i samma domän, kan du använda Active Directory användare och datorer för att konfigurera Delegerings inställningarna för varje anslutnings dator konton, så att de kan delegera till mål-SPN.

Om kopplings servrarna och webb program tjänst kontot finns i olika domäner används resursbaserade delegering. Delegerings behörigheterna konfigureras på mål webb servern och webb program tjänst kontot. Den här metoden för begränsad delegering är relativt ny. Metoden introducerades i Windows Server 2012, som stöder delegering mellan domäner genom att tillåta resurs-(webb tjänst)-ägaren att styra vilka dator-och tjänst konton som kan delegera till den. Det finns inget användar gränssnitt som hjälper dig med den här konfigurationen, så du måste använda PowerShell.
Mer information finns i dokumentet [förstå Kerberos-begränsad delegering med programproxyn](https://aka.ms/kcdpaper).

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>Fungerar NTLM-autentisering med Azure AD-programproxy?

NTLM-autentisering kan inte användas som förautentisering eller enkel inloggnings metod. NTLM-autentisering kan endast användas när den kan förhandlas direkt mellan klienten och det publicerade webb programmet. Om du använder NTLM-autentisering visas vanligt vis en inloggnings uppvarning i webbläsaren.

## <a name="pass-through-authentication"></a>Direktautentisering

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Kan jag använda principer för villkorlig åtkomst för program som har publicerats med direkt autentisering?

Principer för villkorlig åtkomst tillämpas bara för förautentiserade användare i Azure AD. Direkt autentisering utlöser inte Azure AD-autentisering, så det går inte att tillämpa principer för villkorlig åtkomst. Med direktautentisering måste MFA-principer implementeras på den lokala servern, om det är möjligt, eller genom att aktivera förautentisering med Azure AD-programproxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Kan jag publicera ett webb program med autentiseringskrav för klient certifikat?

Nej, det här scenariot stöds inte eftersom Application Proxy kommer att avsluta TLS-trafik.  

## <a name="remote-desktop-gateway-publishing"></a>Publicering av fjärr skrivbords-Gateway

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hur publicerar jag Fjärrskrivbordsgateway över Azure AD-programproxy?

Se [publicera fjärr skrivbord med Azure AD-programproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Kan jag använda Kerberos-begränsad delegering (enkel inloggning på Windows-integrerad autentisering) i publicerings scenariot för fjärrskrivbordsgateway?

Nej, det här scenariot stöds inte.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Mina användare använder inte Internet Explorer 11 och scenariot för för autentisering fungerar inte för dem. Förväntas detta?

Ja, det är förväntat. Scenariot före autentisering kräver en ActiveX-kontroll som inte stöds i tredje parts webbläsare.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Stöds en webb klient för fjärr skrivbord (HTML5)?

Ja, det här scenariot är för närvarande en offentlig för hands version. Se [publicera fjärr skrivbord med Azure AD-programproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>När jag har konfigurerat scenariot för för autentisering påträffade jag att användaren måste autentisera två gånger: först i inloggnings formuläret för Azure AD och sedan i RDWeb-inloggnings formuläret. Förväntas detta? Hur kan jag minska detta till en inloggning?

Ja, det är förväntat. Om användarens dator är ansluten till Azure AD loggar användaren automatiskt in på Azure AD. Användaren behöver bara ange sina autentiseringsuppgifter i RDWeb-inloggnings formuläret.

## <a name="sharepoint-publishing"></a>SharePoint-publicering

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hur kan jag publicera SharePoint över Azure AD-programproxy?

Se [aktivera fjärråtkomst till SharePoint med Azure AD-programproxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Kan jag använda SharePoint Mobile-appen (iOS/Android) för att få åtkomst till en publicerad SharePoint-Server?

[SharePoint-mobilappen](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) stöder för närvarande inte Azure Active Directory förautentisering.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory Federation Services (AD FS) (AD FS) publicering 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Kan jag använda Azure AD-programproxy som AD FS proxy (t. ex. webbprogramproxy)?

Nej. Azure AD-programproxy har utformats för att fungera med Azure AD och uppfyller inte kraven för att fungera som en AD FS proxy.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Fungerar WebSocket-supporten för andra program än QlikSense?

För närvarande är stödet för WebSocket-protokoll fortfarande i offentlig för hands version och det kanske inte fungerar för andra program. Vissa kunder har blandat framgång med WebSocket-protokoll med andra program. Vi vill gärna höra resultatet om du testar sådana scenarier. Skicka oss din feedback på aadapfeedback@microsoft.com .

Funktioner (EventLogs, PowerShell och Fjärrskrivbordstjänster) i Windows administrations Center (WAC) eller på en webb klient för fjärr skrivbord fungerar inte via Azure AD-programproxy för närvarande.

## <a name="link-translation"></a>Länk Översättning

### <a name="does-using-link-translation-affect-performance"></a>Påverkas prestanda av länk Översättning?

Ja. Länk översättning påverkar prestanda. Application Proxy-tjänsten söker igenom programmet efter hårdkodad-länkar och ersätter dem med deras respektive publicerade externa URL: er innan de presenteras för användaren. 

För bästa prestanda rekommenderar vi att du använder identiska interna och externa URL: er genom att konfigurera [anpassade domäner](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Om du inte kan använda anpassade domäner kan du förbättra länk översättnings prestanda med hjälp av säkerhets inloggnings tillägget Mina appar eller Microsoft Edge på mobilen. Se [omdirigera hårdkodad-Länkar för appar som publicerats med Azure AD-programproxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Jokertecken

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hur gör jag för att använda jokertecken för att publicera två program med samma anpassade domän namn men med olika protokoll, ett för HTTP och ett för HTTPS?

Det här scenariot stöds inte direkt. Alternativen för det här scenariot är:

1. Publicera både HTTP-och HTTPS-URL: er som separata program med jokertecken, men ge var och en av dem en annan anpassad domän. Den här konfigurationen kommer att fungera eftersom de har olika externa URL: er.

2. Publicera HTTPS-URL: en via ett program med jokertecken. Publicera HTTP-programmen separat med dessa PowerShell-cmdletar för programproxy:
   - [Application Proxy-programhantering](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Application Proxy Connector-hantering](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
