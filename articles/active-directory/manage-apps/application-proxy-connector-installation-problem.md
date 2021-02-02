---
title: Problem med att installera anslutningsappen för programproxyagenten
description: Så här felsöker du problem som du kan stöta på när du installerar Application Proxy-agentens koppling för Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253672"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Problem med att installera anslutningsappen för programproxyagenten

Microsoft Azure Active Directory Application Proxy Connector är en intern domän komponent som använder utgående anslutningar för att upprätta anslutningen från molnets tillgängliga slut punkt till den interna domänen.

## <a name="general-problem-areas-with-connector-installation"></a>Allmänna problemområden med anslutnings installation

När installationen av en anslutning Miss lyckas är rotor saken vanligt vis något av följande områden. **Se till att du startar om anslutningen som en markör för eventuell fel sökning.**

1.  **Anslutning** – för att slutföra installationen måste den nya anslutningen registrera och upprätta framtida förtroende egenskaper. Detta görs genom att ansluta till Azure Active Directory-programproxy moln tjänsten.

2.  **Förtroende etablering** – den nya anslutningen skapar ett självsignerat certifikat och registrerar sig för moln tjänsten.

3.  **Autentisering av administratören** – under installationen måste användaren ange administratörsautentiseringsuppgifter för att slutföra anslutnings installationen.

> [!NOTE]
> Installations loggarna för Connector finns i mappen% TEMP% och kan hjälpa dig att ge ytterligare information om vad som orsakar ett installations fel.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Kontrol lera anslutningen till tjänsten Cloud Application Proxy och sidan Microsoft-inloggning

**Mål:** Kontrol lera att anslutnings datorn kan ansluta till registrerings slut punkten för programproxyn och sidan Microsoft-inloggning.

1.  På kopplings servern kör du ett port test genom att använda [telnet](/windows-server/administration/windows-commands/telnet) eller något annat port test verktyg för att kontrol lera att portarna 443 och 80 är öppna.

2.  Om någon av dessa portar inte lyckas kontrollerar du att brand väggen eller backend-proxyn har åtkomst till de nödvändiga domänerna och portarna i, [förbereder din lokala miljö](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Öppna en webbläsare (separat flik) och gå till följande webb sida: `https://login.microsoftonline.com` , se till att du kan logga in på sidan.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Verifiera stöd för maskin-och Server dels komponenter för certifikat för programproxy-förtroende

**Mål:** Kontrol lera att anslutnings datorn, backend-proxyn och brand väggen kan stödja certifikatet som skapats av anslutnings tjänsten för framtida förtroende och att certifikatet är giltigt.

>[!NOTE]
>Anslutningen försöker skapa ett SHA512-certifikat som stöds av TLS 1.2. Om datorn eller Server dels brand väggen och proxyn inte stöder TLS 1.2, Miss lyckas installationen.
>
>

**Granska de krav som krävs:**

1.  Kontrol lera att datorn har stöd för TLS 1.2 – alla Windows-versioner efter 2012 R2 ska ha stöd för TLS 1,2. Om din kopplings dator är från en version av 2012 R2 eller tidigare kontrollerar du att följande KB är installerade på datorn: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Kontakta nätverks administratören och be om att kontrol lera att backend-proxyn och brand väggen inte blockerar SHA512 för utgående trafik.

**Så här verifierar du klient certifikatet:**

Verifiera tumavtrycket för det aktuella klient certifikatet. Du hittar certifikat arkivet i `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

De möjliga **IsInUserStore** -värdena är **True** och **false**. Värdet **True** betyder att det automatiskt förnyade certifikatet lagras i den personliga behållaren i användar certifikat arkivet för nätverks tjänsten. Värdet **false** betyder att klient certifikatet skapades under installationen eller registreringen som initierades av Register-AppProxyConnector kommandot och lagras i den personliga behållaren i certifikat arkivet på den lokala datorn.

Om värdet är **Sant** följer du dessa steg för att verifiera certifikatet:
1. Ladda ned [PsTools.zip](/sysinternals/downloads/pstools)
2. Extrahera [PsExec](/sysinternals/downloads/psexec) från paketet och kör **PsExec-i-u "NT authority\network service" cmd.exe** från en upphöjd kommando tolk.
3. Kör **certmgr. msc** i den nyligen visade kommando tolken
4. Expandera den personliga behållaren i hanterings konsolen och klicka på certifikat
5. Leta upp certifikatet som utfärdats av **connectorregistrationca.msappproxy.net**

Om värdet är **false** följer du dessa steg för att verifiera certifikatet:
1. Kör **certlm. msc**
2. Expandera den personliga behållaren i hanterings konsolen och klicka på certifikat
3. Leta upp certifikatet som utfärdats av **connectorregistrationca.msappproxy.net**

**Så här förnyar du klient certifikatet:**

Om en anslutning inte är ansluten till tjänsten under flera månader kan dess certifikat vara inaktuella. Haverit vid certifikat förnyelsen leder till ett utgånget certifikat. Detta gör att kopplings tjänsten upphör att fungera. Händelse 1000 registreras i administratörs loggen för anslutningen:

"Det gick inte att registrera anslutningen: anslutnings förtroende certifikatet har upphört att gälla. Kör PowerShell-cmdleten Register-AppProxyConnector på den dator där anslutningen körs för att registrera anslutningen på nytt. "

I det här fallet måste du avinstallera och installera om anslutningen för att utlösa registreringen eller köra följande PowerShell-kommandon:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Om du vill veta mer om kommandot Register-AppProxyConnector kan du läsa [skapa ett skript för obevakad installation för Azure AD-programproxy-anslutningen](./application-proxy-register-connector-powershell.md)

## <a name="verify-admin-is-used-to-install-the-connector"></a>Verifiera att administratören används för att installera anslutningen

**Mål:** Kontrol lera att den användare som försöker installera anslutningen är administratör med rätt autentiseringsuppgifter. För närvarande måste användaren minst ha en program administratör för att installationen ska lyckas.

**Så här kontrollerar du att autentiseringsuppgifterna är korrekta:**

Anslut till `https://login.microsoftonline.com` och Använd samma autentiseringsuppgifter. Kontrol lera att inloggningen har slutförts. Du kan kontrol lera användar rollen genom att gå till **Azure Active Directory**  - &gt; **användare och grupper**  - &gt; **alla användare**. 

Välj ditt användar konto och sedan "katalog roll" på den resulterande menyn. Kontrol lera att den valda rollen är "program administratör". Om du inte kan komma åt någon av sidorna tillsammans med de här stegen har du inte den roll som krävs.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
