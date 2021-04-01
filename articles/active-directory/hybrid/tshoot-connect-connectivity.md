---
title: 'Azure AD Connect: Felsök problem med Azure AD-anslutning | Microsoft Docs'
description: Förklarar hur du felsöker anslutnings problem med Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 56e9820c5e3a750a35b7271b86750df00eb4784e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677063"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Felsöka Azure AD-anslutning
Den här artikeln förklarar hur anslutningar mellan Azure AD Connect och Azure AD fungerar och hur du felsöker anslutnings problem. De här problemen visas förmodligen i en miljö med en proxyserver.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Felsöka anslutnings problem i installations guiden
Azure AD Connect använder modern autentisering (med ADAL-biblioteket) för autentisering. Installations guiden och Synkroniseringsmotorn kräver att machine.config konfigureras korrekt eftersom dessa två är .NET-program.

I den här artikeln visar vi hur Fabrikam ansluter till Azure AD via dess proxy. Proxyservern heter fabrikamproxy och använder port 8080.

Först måste vi kontrol lera att [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) är korrekt konfigurerad och att **Microsoft Azure AD Sync-tjänsten** har startats om en gång efter machine.config fil uppdateringen.
![Skärm bild som visar en del av Machine dot config-filen.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> I vissa andra Bloggar än Microsoft är det dokumenterat att ändringar ska göras i miiserver.exe.config i stället. Den här filen skrivs dock över vid varje uppgradering, så även om den fungerar under den första installationen slutar systemet att fungera vid första uppgraderingen. Av den anledningen är rekommendationen att uppdatera machine.config i stället.
>
>

Proxyservern måste också ha de webb adresser som krävs öppna. Den officiella listan är dokumenterad i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

I dessa URL: er är följande tabell det absoluta minimala alternativet för att kunna ansluta till Azure AD. Den här listan innehåller inte några valfria funktioner, till exempel tillbakaskrivning av lösen ord eller Azure AD Connect Health. Den dokumenteras här för att hjälpa till med fel sökning av den inledande konfigurationen.

| URL | Port | Beskrivning |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*. verisign.com |HTTP/80 |Används för att hämta listor över återkallade certifikat. |
| \*. entrust.net |HTTP/80 |Används för att hämta listor över återkallade certifikat för MFA. |
| \*.windows.net |HTTPS/443 |Används för att logga in på Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Används för MFA. |
| \*.microsoftonline.com |HTTPS/443 |Används för att konfigurera Azure AD-katalogen och importera/exportera data. |
| \*. crl3.digicert.com |HTTP/80 |Används för att verifiera certifikat. |
| \*. crl4.digicert.com |HTTP/80 |Används för att verifiera certifikat. |
| \*. ocsp.digicert.com |HTTP/80 |Används för att verifiera certifikat. |
| \*. www.d-trust.net |HTTP/80 |Används för att verifiera certifikat. |
| \*. root-c3-ca2-2009.ocsp.d-trust.net |HTTP/80 |Används för att verifiera certifikat. |
| \*. crl.microsoft.com |HTTP/80 |Används för att verifiera certifikat. |
| \*. oneocsp.microsoft.com |HTTP/80 |Används för att verifiera certifikat. |
| \*. ocsp.msocsp.com |HTTP/80 |Används för att verifiera certifikat. |

## <a name="errors-in-the-wizard"></a>Fel i guiden
Installations guiden använder två olika säkerhets kontexter. På sidan **Anslut till Azure AD** använder den för tillfället inloggade användare. På sidan **Konfigurera** ändras det till det [konto som kör tjänsten för Synkroniseringsmotorn](reference-connect-accounts-permissions.md#adsync-service-account). Om det uppstår ett problem visas det förmodligen redan på sidan **Anslut till Azure AD** i guiden eftersom proxykonfigurationen är global.

Följande problem är de vanligaste felen som du stöter på i installations guiden.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>Installations guiden har inte kon figurer ATS korrekt
Det här felet visas när själva guiden inte kan komma åt proxyn.
![Skärm bilden visar ett fel: det gick inte att validera autentiseringsuppgifterna.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Om det här felet visas kontrollerar du att [machine.config](how-to-connect-install-prerequisites.md#connectivity) har kon figurer ATS korrekt.
* Om det ser korrekt ut följer du stegen i [Verifiera proxyanslutningar](#verify-proxy-connectivity) för att se om problemet finns utanför guiden.

### <a name="a-microsoft-account-is-used"></a>En Microsoft-konto används
Om du använder en **Microsoft-konto** snarare än ett **skol-eller organisations** konto visas ett allmänt fel.
![Ett Microsoft-konto används](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Det går inte att nå MFA-slutpunkten
Det här felet visas om det **https://secure.aadcdn.microsoftonline-p.com** inte går att nå slut punkten och din globala administratör har MFA aktiverat.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Om det här felet visas kontrollerar du att slut punkts **Secure.aadcdn.microsoftonline-p.com** har lagts till i proxyn.

### <a name="the-password-cannot-be-verified"></a>Det går inte att verifiera lösen ordet
Om installations guiden lyckas ansluta till Azure AD, men själva lösen ordet inte kan verifieras visas det här felet: ![ felaktigt lösen ord.](./media/tshoot-connect-connectivity/badpassword.png)

* Är lösen ordet ett tillfälligt lösen ord och måste ändras? Är det faktiskt rätt lösen ord? Försök att logga in på `https://login.microsoftonline.com` (på en annan dator än den Azure AD Connect servern) och kontrol lera att kontot är användbart.

### <a name="verify-proxy-connectivity"></a>Verifiera proxy-anslutning
Du kan kontrol lera om den Azure AD Connect servern har faktisk anslutning till proxyn och Internet genom att använda en PowerShell för att se om proxyn tillåter webb förfrågningar eller inte. Kör i PowerShell-prompten `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Tekniskt det första anropet är till `https://login.microsoftonline.com` och denna URI fungerar också, men den andra URI: n är snabbare att svara.)

PowerShell använder konfigurationen i machine.config för att kontakta proxyn. Inställningarna i winhttp/netsh bör inte påverka dessa cmdletar.

Om proxyservern har kon figurer ATS korrekt bör du få status: ![ skärm bild som visar statusen slutförd när proxyservern har kon figurer ATS korrekt.](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Om du **inte kan ansluta till fjärrservern** försöker PowerShell att göra ett direkt anrop utan att använda proxyn eller så är DNS inte korrekt konfigurerat. Kontrol lera att **machine.config** -filen är korrekt konfigurerad.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Om proxyservern inte är korrekt konfigurerad får du ett fel meddelande: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fel | Feltext | Kommentar |
| --- | --- | --- |
| 403 |Förbjudet |Proxyn har inte öppnats för den begärda URL: en. Gå tillbaka till proxykonfigurationen och kontrol lera att [webb adresserna](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) har öppnats. |
| 407 |Proxyautentisering krävs |Proxyservern krävde inloggning och inget har angetts. Om proxyservern kräver autentisering måste du se till att den här inställningen är konfigurerad i machine.config. Kontrol lera också att du använder domän konton för användaren som kör guiden och för tjänst kontot. |

### <a name="proxy-idle-timeout-setting"></a>Timeout-inställning för proxy inaktivitet
När Azure AD Connect skickar en begäran om export till Azure AD kan Azure AD ta upp till 5 minuter att bearbeta begäran innan du genererar ett svar. Detta kan inträffa särskilt om det finns ett antal grupp objekt med stora grupp medlemskap som ingår i samma export förfrågan. Se till att tids gränsen för proxyns inaktivitet är större än 5 minuter. Annars kan ett tillfälligt anslutnings problem med Azure AD observeras på den Azure AD Connect servern.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Kommunikations mönstret mellan Azure AD Connect och Azure AD
Om du har följt alla dessa föregående steg och fortfarande inte kan ansluta kan du nu börja titta på nätverks loggar. Det här avsnittet används för att dokumentera ett mönster för normal och lyckad anslutning. Den visar också vanliga röda sill som kan ignoreras när du läser nätverks loggarna.

* Det finns anrop till `https://dc.services.visualstudio.com` . Du måste inte ha denna URL öppen i proxyn för att installationen ska lyckas och dessa anrop kan ignoreras.
* Du ser att DNS-matchningen listar de faktiska värdarna i DNS-namnområdet nsatc.net och andra namn områden som inte är under microsoftonline.com. Det finns dock inga webb tjänst begär Anden på de faktiska Server namnen och du behöver inte lägga till dessa URL: er i proxyn.
* Slut punkterna adminwebservice och provisioningapi är identifierings slut punkter och används för att hitta den faktiska slut punkten som ska användas. Dessa slut punkter skiljer sig åt beroende på din region.

### <a name="reference-proxy-logs"></a>Referenser för proxy
Här är en dumpning från en faktisk proxy-logg och sidan installations guide från den plats där den togs (dubbla poster till samma slut punkt har tagits bort). Det här avsnittet kan användas som referens för dina egna proxy-och nätverks loggar. De faktiska slut punkterna kan vara olika i din miljö (särskilt dessa URL: er i *kursiv stil*).

**Anslut till Azure AD**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**I**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-Relay*. microsoftonline.com:443 |

**Inledande synkronisering**

| Tid | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-Anchor*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Autentiseringsfel
I det här avsnittet beskrivs fel som kan returneras från ADAL (det autentiseringspaket som används av Azure AD Connect) och PowerShell. Fel förklaringen bör hjälpa dig att förstå dina nästa steg.

### <a name="invalid-grant"></a>Ogiltigt bidrag
Ogiltigt användar namn eller lösen ord. Mer information finns i [lösen ordet kan inte verifieras](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Okänd användar typ
Det går inte att hitta eller matcha din Azure AD-katalog. Kanske försöker du logga in med ett användar namn i en overifierad domän?

### <a name="user-realm-discovery-failed"></a>Identifiering av användar sfär misslyckades
Konfigurations problem för nätverk eller proxy. Det går inte att nå nätverket. Se [Felsöka anslutnings problem i installations guiden](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Användarens lösen ord har gått ut
Dina autentiseringsuppgifter har upphört att gälla. Ändra lösen ordet.

### <a name="authorization-failure"></a>Auktoriseringsfel
Det gick inte att auktorisera användaren för att utföra åtgärder i Azure AD.

### <a name="authentication-canceled"></a>Autentiseringen avbröts
Multi-Factor Authentication-utmaningen (MFA) avbröts.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Det gick inte att ansluta till MS online
Autentiseringen lyckades, men det har uppstått ett autentiseringsfel i Azure AD PowerShell.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Global administratörs roll för Azure AD krävs
Användaren har autentiserats. Användaren är dock inte tilldelad global administratörs roll. Så här [kan du tilldela användaren rollen som global administratör](../roles/permissions-reference.md) .

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management aktive rad
Autentiseringen lyckades. Privileged Identity Management har Aktiver ATS och du är för närvarande inte en global administratör. Mer information finns i [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Företags informationen är inte tillgänglig
Autentiseringen lyckades. Det gick inte att hämta företags information från Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Domän information otillgänglig
Autentiseringen lyckades. Det gick inte att hämta domän information från Azure AD.

### <a name="unspecified-authentication-failure"></a>Ospecificerat autentiseringsfel
Visas som ett oväntat fel i installations guiden. Kan inträffa om du försöker använda ett **Microsoft-konto** i stället för ett **skol-eller organisations konto**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Fel söknings steg för tidigare versioner.
Med versioner som börjar med build Number 1.1.105.0 (lanserades februari 2016) drogs inloggnings assistenten tillbaka. Det här avsnittet och konfigurationen ska inte längre krävas, utan behålls som referens.

För att enkel inloggnings assistenten ska fungera måste WinHTTP konfigureras. Den här konfigurationen kan göras med [**netsh**](how-to-connect-install-prerequisites.md#connectivity).
![Skärm bild som visar ett kommando tolks fönster som kör Netsh-verktyget för att ställa in en proxy.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>Inloggnings assistenten har inte kon figurer ATS korrekt
Det här felet visas när inloggnings assistenten inte kan komma åt proxyservern eller så tillåter inte proxyservern begäran.
![Skärm bild som visar ett fel: det gick inte att validera autentiseringsuppgifter, verifiera nätverks anslutningen och brand väggen eller proxyinställningarna.](./media/tshoot-connect-connectivity/nonetsh.png)

* Om det här felet visas tittar du på proxykonfigurationen i [netsh](how-to-connect-install-prerequisites.md#connectivity) och kontrollerar att den är korrekt.
  ![Skärm bild visar ett kommando tolks fönster som kör Netsh-verktyget för att Visa proxykonfigurationen.](./media/tshoot-connect-connectivity/netshshow.png)
* Om det ser korrekt ut följer du stegen i [Verifiera proxyanslutningar](#verify-proxy-connectivity) för att se om problemet finns utanför guiden.

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
