---
title: Felsöka Azure AD MFA NPS-tillägget – Azure Active Directory
description: Få hjälp med att lösa problem med NPS-tillägget för Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: fa3a2366f007ff1481e7c84f049e606586392037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742960"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Lös fel meddelanden från NPS-tillägget för Azure AD Multi-Factor Authentication

Om du stöter på fel med NPS-tillägget för Azure AD Multi-Factor Authentication kan du använda den här artikeln för att uppnå en snabbare lösning. Loggar för NPS-tillägg finns i Loggboken under **anpassade vyer**  >  **Server roller**  >  **nätverks policy och åtkomst tjänster** på den server där NPS-tillägget är installerat.

## <a name="troubleshooting-steps-for-common-errors"></a>Fel söknings steg för vanliga fel

| Felkod | Felsökningsanvisningar |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Kontakta supporten](#contact-microsoft-support)och ange en lista över steg för att samla in loggar. Ange så mycket information som du kan om vad som hände innan felet, inklusive klient-ID och User Principal Name (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Det kan finnas ett problem med hur klient certifikatet har installerats eller associerats med din klient. Följ anvisningarna i [fel sökning av MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka problem med klient certifikat. |
| **ESTS_TOKEN_ERROR** | Följ anvisningarna i [FELSÖKA MFA NPS-tillägget](howto-mfa-nps-extension.md#troubleshooting) för att undersöka problem med klient certifikat och ADAL-token. |
| **HTTPS_COMMUNICATION_ERROR** | NPS-servern kan inte ta emot svar från Azure AD MFA. Kontrol lera att brand väggarna är öppna i båda riktningarna för trafik till och från https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | På den server som kör NPS-tillägget kontrollerar du att du kan komma åt  `https://adnotifications.windowsazure.com` och `https://login.microsoftonline.com/` . Felsök anslutningen på servern om dessa platser inte läser in. |
| **NPS-tillägg för Azure AD MFA:** <br> NPS-tillägget för Azure AD MFA utför endast sekundär autentisering för RADIUS-begäranden i AccessAccept-tillstånd. Begäran mottagen för användar-username med svars status AccessReject, ignorerar begäran. | Det här felet återspeglar vanligt vis ett autentiseringsfel i AD eller att NPS-servern inte kan ta emot svar från Azure AD. Kontrol lera att brand väggarna är öppna i båda riktningarna för trafik till och från `https://adnotifications.windowsazure.com` och `https://login.microsoftonline.com` med portarna 80 och 443. Det är också viktigt att kontrol lera att på fliken fjärråtkomst för nätverks åtkomst behörigheter, inställningen är inställd på "kontrol lera åtkomst via NPS-nätverks princip". Det här felet kan också utlösas om användaren inte har tilldelats en licens. |
| **REGISTRY_CONFIG_ERROR** | En nyckel saknas i registret för programmet, vilket kan bero på att [PowerShell-skriptet](howto-mfa-nps-extension.md#install-the-nps-extension) inte kördes efter installationen. Fel meddelandet ska innehålla den nyckel som saknas. Kontrol lera att du har nyckeln under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> RADIUS-begäran saknar obligatoriskt RADIUS-userName\Identifier-attribut. Verifiera att NPS tar emot RADIUS-begäranden | Det här felet återspeglar vanligt vis ett installations problem. NPS-tillägget måste vara installerat på NPS-servrar som kan ta emot RADIUS-begäranden. NPS-servrar som installeras som beroenden för tjänster som RDG och RRAS får inte RADIUS-förfrågningar. NPS-tillägget fungerar inte när de installeras via sådana installationer och fel sedan det inte går att läsa informationen från autentiseringsbegäran. |
| **REQUEST_MISSING_CODE** | Kontrol lera att lösen ords krypterings protokollet mellan NPS-och NAS-servrarna stöder den sekundära autentiseringsmetoden som du använder. **PAP** stöder alla autentiseringsmetoder för Azure AD MFA i molnet: telefonsamtal, envägs textmeddelande, mobilapp och verifierings kod för mobilapp. **CHAPv2** -och **EAP** -support för telefonsamtal och aviseringar för mobilapp. |
| **USERNAME_CANONICALIZATION_ERROR** | Kontrol lera att användaren finns i den lokala Active Directory-instansen och att NPS-tjänsten har behörighet att komma åt katalogen. Om du använder förtroenden mellan skogar kan du [kontakta supporten](#contact-microsoft-support) om du vill ha mer hjälp. |

### <a name="alternate-login-id-errors"></a>Alternativa inloggnings-ID-fel

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: userObjectSid-sökningen misslyckades | Kontrol lera att användaren finns i den lokala Active Directory-instansen. Om du använder förtroenden mellan skogar kan du [kontakta supporten](#contact-microsoft-support) om du vill ha mer hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: alternativ LoginId-sökning misslyckades | Kontrol lera att LDAP_ALTERNATE_LOGINID_ATTRIBUTE har angetts till ett [giltigt Active Directory-attribut](/windows/win32/adschema/attributes-all). <br><br> Om LDAP_FORCE_GLOBAL_CATALOG har angetts till true, eller om LDAP_LOOKUP_FORESTS har kon figurer ATS med ett värde som inte är tomt, kontrollerar du att du har konfigurerat en global katalog och att attributet AlternateLoginId har lagts till. <br><br> Om LDAP_LOOKUP_FORESTS har kon figurer ATS med ett värde som inte är tomt kontrollerar du att värdet är korrekt. Om det finns fler än ett skogs namn måste namnen skiljas åt med semikolon, inte blank steg. <br><br> Om de här stegen inte löser problemet kan du [kontakta supporten](#contact-microsoft-support) om du vill ha mer hjälp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fel: alternativt LoginId-värde är tomt | Kontrol lera att attributet AlternateLoginId har kon figurer ATS för användaren. |

## <a name="errors-your-users-may-encounter"></a>Fel som användarna kan stöta på

| Felkod | Felmeddelande | Felsökningsanvisningar |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Anroparens klient har inte behörighet att göra autentisering för användaren | Kontrol lera om klient domänen och domänen för User Principal Name (UPN) är desamma. Kontrol lera till exempel att user@contoso.com försöker autentisera till contoso-klienten. UPN representerar en giltig användare för klienten i Azure. |
| **AuthenticationMethodNotConfigured** | Den angivna autentiseringsmetoden har inte kon figurer ATS för användaren | Låt användaren lägga till eller verifiera sina verifierings metoder enligt anvisningarna i [Hantera dina inställningar för](../user-help/multi-factor-authentication-end-user-manage-settings.md)tvåstegsverifiering. |
| **AuthenticationMethodNotSupported** | Den angivna autentiseringsmetoden stöds inte. | Samla in alla loggar som innehåller det här felet och [kontakta supporten](#contact-microsoft-support). När du kontaktar supporten anger du användar namnet och den sekundära verifierings metoden som utlöste felet. |
| **BecAccessDenied** | MSODS BEC-anrop returnerade åtkomst nekad, antagligen är användar namnet inte definierat i klient organisationen | Användaren finns i Active Directory lokalt men synkroniseras inte i Azure AD via AD Connect. Eller så saknas användaren för klienten. Lägg till användaren i Azure AD och Lägg till deras verifierings metoder enligt anvisningarna i [Hantera dina inställningar för](../user-help/multi-factor-authentication-end-user-manage-settings.md)tvåstegsverifiering. |
| **InvalidFormat** eller **StrongAuthenticationServiceInvalidParameter** | Telefonnumret är i ett format som inte kan tolkas | Be användaren att rätta sina verifierings telefonnummer. |
| **InvalidSession** | Den angivna sessionen är ogiltig eller kan ha upphört att gälla | Sessionen tog över tre minuter att slutföra. Kontrol lera att användaren anger verifierings koden eller svarar på appens avisering inom tre minuter efter att autentiseringsbegäran initierats. Om detta inte löser problemet kontrollerar du att det inte finns några nätverks fördröjningar mellan klienten, NAS-servern, NPS-servern och Azure AD MFA-slutpunkten.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Ingen autentiseringsmetod för standard har kon figurer ATS för användaren | Låt användaren lägga till eller verifiera sina verifierings metoder enligt anvisningarna i [Hantera dina inställningar för](../user-help/multi-factor-authentication-end-user-manage-settings.md)tvåstegsverifiering. Kontrol lera att användaren har valt en autentiseringsmetod som är standard och har konfigurerat metoden för sitt konto. |
| **OathCodePinIncorrect** | Fel kod och PIN-kod har angetts. | Det här felet förväntas inte i NPS-tillägget. Om din användare påträffar detta, kan du [kontakta supporten](#contact-microsoft-support) för fel söknings hjälpen. |
| **ProofDataNotFound** | Ingen bevis information har kon figurer ATS för den angivna autentiseringsmetoden. | Låt användaren försöka med en annan verifieringsmetod eller Lägg till en ny verifierings metod enligt anvisningarna i [Hantera dina inställningar för](../user-help/multi-factor-authentication-end-user-manage-settings.md)tvåstegsverifiering. Om användaren fortsätter att se det här felet när du har bekräftat att deras verifierings metod är korrekt, [kontaktar du supporten](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Fel kod och PIN-kod har angetts. (OneWaySMS) | Det här felet förväntas inte i NPS-tillägget. Om din användare påträffar detta, kan du [kontakta supporten](#contact-microsoft-support) för fel söknings hjälpen. |
| **TenantIsBlocked** | Klienten är blockerad | [Kontakta supporten](#contact-microsoft-support) med *klient-ID: t* från sidan med Azure AD-egenskaper i Azure Portal. |
| **UserNotFound** | Det gick inte att hitta den angivna användaren | Klienten visas inte längre som aktiv i Azure AD. Kontrol lera att din prenumeration är aktiv och att du har de appar som krävs från första part. Kontrol lera också att klienten i certifikat ämnet är som förväntat och att certifikatet fortfarande är giltigt och registrerat under tjänstens huvud namn. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Meddelanden som användarna kan stöta på som inte är fel

Ibland kan användarna få meddelanden från Multi-Factor Authentication eftersom deras autentiseringsbegäran misslyckades. Dessa är inte fel i produktens konfiguration, men är avsiktliga varningar som förklarar varför en autentiseringsbegäran nekades.

| Felkod | Felmeddelande | Rekommenderade åtgärder |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Felaktig kod entered\OATH kod | Användaren angav fel kod. Försök igen genom att begära en ny kod eller logga in igen. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximalt tillåten kod försök har nåtts | Användaren klarade inte verifierings utmaningen för många gånger. Beroende på dina inställningar kan de behöva avblockeras av en administratör nu.  |
| **SMSAuthFailedWrongCodeEntered** | Felaktig kod har angetts/textmeddelandets eng ång slö sen ord | Användaren angav fel kod. Försök igen genom att begära en ny kod eller logga in igen. |

## <a name="errors-that-require-support"></a>Fel som kräver stöd

Om du stöter på något av dessa fel rekommenderar vi att du [kontaktar supporten](#contact-microsoft-support) för diagnostisk hjälp. Det finns ingen standard uppsättning med steg som kan användas för att åtgärda dessa fel. När du kontaktar supporten ska du se till att ta med så mycket information som möjligt om de steg som ledde till ett fel och din klient information.

| Felkod | Felmeddelande |
| ---------- | ------------- |
| **InvalidParameter** | Begäran får inte vara null |
| **InvalidParameter** | ObjectId får inte vara null eller tomt för ReplicationScope:{0} |
| **InvalidParameter** | Längden på företags namn \{ 0} \ är längre än den högsta tillåtna längden {1} |
| **InvalidParameter** | UserPrincipalName får inte vara null eller tomt |
| **InvalidParameter** | Det angivna TenantId-formatet är inte i rätt format |
| **InvalidParameter** | SessionId får inte vara null eller tomt |
| **InvalidParameter** | Det gick inte att matcha några ProofData från Request eller MSODS. ProofData är okänd |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Nästa steg

### <a name="troubleshoot-user-accounts"></a>Felsöka användar konton

Om dina användare har [problem med tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-troubleshoot.md), kan de själv diagnostisera problem.

### <a name="health-check-script"></a>Skript för hälso kontroll

[Hälso kontroll skriptet för Azure AD MFA NPS-tillägget](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) utför en grundläggande hälso kontroll vid fel sökning av NPS-tillägget. Kör skriptet och välj alternativ 3.

### <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du behöver ytterligare hjälp kan du kontakta en support tekniker via [Azure Multi-Factor Authentication-Server Support](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det bra om du kan inkludera så mycket information om problemet som möjligt. Information som du kan ange innehåller sidan där du såg felet, den speciella felkoden, det ID som identifierades för den användare som såg felet och fel söknings loggar.

Om du vill samla in fel söknings loggar för support diagnostik använder du följande steg på NPS-servern:

1. Öppna Registereditorn och bläddra till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa ange **VERBOSE_LOG** till **Sant**
2. Öppna en administratörs kommando tolk och kör följande kommandon:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Återskapa problemet

4. Stoppa spårningen med följande kommandon:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Öppna Registereditorn och bläddra till HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa ange **VERBOSE_LOG** till **falskt**
6. Zip-innehållet i mappen C:\NPS och koppla den zippade filen till support ärendet.