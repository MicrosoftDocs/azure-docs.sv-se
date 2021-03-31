---
title: Lösen ords lös säkerhets nyckel logga in till lokala resurser – Azure Active Directory
description: Lär dig hur du aktiverar lösen ords lös säkerhets nyckel inloggning till lokala resurser med hjälp av Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: db1b559bb4f6a1f8866116c287df5b814500210b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647480"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>Aktivera lösen ords lös säkerhets nyckel inloggning till lokala resurser med Azure Active Directory 

Det här dokumentet fokuserar på att aktivera autentisering utan lösen ord till lokala resurser för miljöer med både **Azure AD-anslutna** och **hybrid Azure AD-anslutna** Windows 10-enheter. Den här funktionen ger sömlös enkel inloggning (SSO) till lokala resurser med hjälp av Microsoft-kompatibla säkerhets nycklar.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO till lokala resurser med hjälp av FIDO2-nycklar

Azure Active Directory (AD) kan utfärda Kerberos Ticket Granting Tick (TGT) för en eller flera av dina Active Directory-domäner. Med den här funktionen kan användarna logga in i Windows med moderna autentiseringsuppgifter som FIDO2-säkerhetsnycklar och komma åt traditionella Active Directory baserade resurser. Biljetter och auktorisering i Kerberos-tjänsten fortsätter att kontrol leras av dina lokala Active Directory domänkontrollanter.

Ett Azure AD Kerberos-serverobjektet skapas i din lokala Active Directory och publiceras sedan på ett säkert sätt till Azure Active Directory. Objektet är inte associerat med några fysiska servrar. Det är bara en resurs som kan användas av Azure Active Directory för att generera Kerberos-TGT för din Active Directory-domän.

![Hämtar biljett beviljande biljetter (TGT) från Azure AD och AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Användaren loggar in på Windows 10-enheten med en säkerhets nyckel för FIDO2 och autentiserar till Azure AD.
1. Azure AD kontrollerar katalogen för en Kerberos-server nyckel som matchar användarens lokala AD-domän.
   1. Azure AD genererar en Kerberos-TGT för användarens lokala AD-domän. TGT innehåller bara användarens SID. Inga auktoriseringsdata ingår i TGT.
1. TGT returneras till klienten tillsammans med sin Azure AD Primary Refresh-token (PRT).
1. Klient datorn kontaktar en lokal AD-domänkontrollant och handlar om en del TGT för en fullständigt utformad TGT.
1. Klient datorn har nu en Azure AD-PRT och en fullständig Active Directory TGT och har åtkomst till både moln resurser och lokala resurser.

## <a name="requirements"></a>Krav

Organisationer måste utföra stegen för att [Aktivera lösen ords lös säkerhets nyckel för att logga in på Windows 10-enheter](howto-authentication-passwordless-security-key.md) innan stegen i den här artikeln slutförs.

Organisationer måste också uppfylla följande program varu krav.

- Enheter måste köra Windows 10 version 2004 eller senare.
- Du måste ha version 1.4.32.0 eller senare av [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Mer information om tillgängliga alternativ för Azure AD hybrid-autentisering finns i [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../hybrid/choose-ad-authn.md) och [Välj vilken Installations typ som ska användas för Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Windows Server-domän kontrol Lanterna måste ha följande korrigeringsfiler installerade:
    - För Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - För Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Scenarier som stöds

Scenariot stöder enkel inloggning (SSO) i båda följande scenarier:

- För moln resurser som Microsoft 365 och andra SAML-aktiverade program.
- För lokala resurser och Windows-Integrated autentisering på webbplatser. Resurserna kan omfatta webbplatser och SharePoint-webbplatser som kräver IIS-autentisering och/eller resurser som använder NTLM-autentisering.

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte:

- Windows Server Active Directory Domain Services (AD DS)-domänanslutna (endast lokala enheter) distribution.
- RDP-, VDI-och Citrix-scenarier med hjälp av en säkerhets nyckel.
- S/MIME med hjälp av en säkerhets nyckel.
- "Kör som" med hjälp av en säkerhets nyckel.
- Logga in på en server med hjälp av säkerhets nyckel.

## <a name="create-kerberos-server-object"></a>Skapa Kerberos-serverobjektet

Administratörer använder PowerShell-verktyg från sin Azure AD Connect-Server för att skapa ett Azure AD Kerberos-serverobjektet i sina lokala kataloger. Kör följande steg i varje domän och skog i din organisation som innehåller Azure AD-användare:

1. Uppgradera till den senaste versionen av Azure AD Connect. Anvisningarna förutsätter att du redan har konfigurerat Azure AD Connect som stöd för din hybrid miljö.
1. På Azure AD Connect-servern öppnar du en upphöjd PowerShell-prompt och navigerar till `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Kör följande PowerShell-kommandon för att skapa ett nytt Azure AD Kerberos-serverobjektet i både din lokala Active Directory domän och Azure Active Directory klient organisation.

> [!NOTE]
> Ersätt `contoso.corp.com` i följande exempel med ditt lokala Active Directory domän namn.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visa och verifiera Azure AD Kerberos-servern

Du kan visa och verifiera den nyligen skapade Azure AD Kerberos-servern med följande kommando:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Det här kommandot matar ut egenskaperna för Azure AD Kerberos-servern. Du kan granska egenskaperna för att kontrol lera att allt är i rätt ordning.

| Egenskap | Beskrivning |
| --- | --- |
| ID | Unikt ID för AD DS DC-objektet. Detta ID kallas ibland "fack" eller "gren-ID". |
| DomainDnsName | Active Directory-domänens DNS-domännamn. |
| ComputerAccount | Objektet dator konto för objektet Azure AD Kerberos-Server (DOMÄNKONTROLLANT). |
| UserAccount | Det inaktiverade objektet User Account som innehåller Azure AD Kerberos-serverns TGT-krypterings nyckel. Kontots DN är `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Version | Nyckel versionen av Azure AD Kerberos-serverns TGT-krypterings nyckel. Versionen tilldelas när nyckeln skapas. Versionen ökas sedan varje gång nyckeln roteras. Ökningarna baseras på metadata för replikering och troligen större än ett. Den ursprungliga *versionen av versionen* kan till exempel vara *192272*. Första gången som nyckeln roterades kan versionen gå vidare till *212621*. Det är viktigt att kontrol lera att *versionen* för det lokala objektet och *CloudKeyVersion* för moln objekt är desamma. |
| KeyUpdatedOn | Det datum och den tid som Azure AD Kerberos-servern TGT-krypteringsnyckeln uppdaterades eller skapades. |
| KeyUpdatedFrom | Den DOMÄNKONTROLLANT där Azure AD Kerberos-servern TGT-krypteringsnyckeln senast uppdaterades. |
| CloudId | ID från Azure AD-objektet. Måste matcha ovanstående ID. |
| CloudDomainDnsName | *DomainDnsName* från Azure AD-objektet. Måste matcha *DomainDnsName* ovan. |
| CloudKeyVersion | *Versionen* från Azure AD-objektet. Måste stämma överens med ovanstående *version* . |
| CloudKeyUpdatedOn | *KeyUpdatedOn* från Azure AD-objektet. Måste matcha *KeyUpdatedOn* ovan. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotera Azure AD Kerberos-Server nyckeln

Dina KRBTGT-nycklar för Azure AD Kerberos-Server måste roteras regelbundet. Vi rekommenderar att du följer samma schema som du använder för att rotera alla andra Active Directory-domän-snabbkommandons KRBTGT-nycklar.

> [!WARNING]
> Det finns andra verktyg som kan rotera KRBTGT-nycklarna, men du måste använda de verktyg som beskrivs i det här dokumentet för att rotera KRBTGT-nycklarna för Azure AD Kerberos-servern. Detta säkerställer att nycklarna uppdateras i både lokala AD och Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Ta bort Azure AD Kerberos-servern

Om du vill återställa scenariot och ta bort Azure AD Kerberos-servern från både lokala Active Directory och Azure Active Directory kör du följande kommando:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenarier med flera skogar och flera domäner

Azure AD Kerberos-serverobjektet representeras i Azure AD som ett *KerberosDomain* -objekt. Varje lokal Active Directory domän representeras som ett enda *KerberosDomain* -objekt i Azure AD.

Din organisation har till exempel en Active Directory skog med två domäner `contoso.com` och `fabrikam.com` . Om du väljer att tillåta Azure AD att utfärda Kerberos-TGT för hela skogen finns det två *KerberosDomain* -objekt i Azure AD. Ett *KerberosDomain* -objekt för `contoso.com` , och ett för `fabrikam.com` . Om du har flera Active Directory-skogar finns det ett *KerberosDomain* -objekt för varje domän i varje skog.

Du måste köra stegen för att [skapa ett Kerberos-serverobjektet](#create-kerberos-server-object) i varje domän och skog i din organisation som innehåller Azure AD-användare.

## <a name="known-behavior"></a>Känt beteende

Logga in med FIDO är blockerad om ditt lösen ord har upphört att gälla. Förväntat att användaren återställer sitt lösen ord innan de kan logga in med FIDO.

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem med den här funktionen kan du dela via Windows Feedback Hub-appen med hjälp av följande steg:

1. Starta **feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: säkerhet och sekretess
   - Under kategori: FIDO
1. Om du vill avbilda loggar använder du alternativet för att **återskapa problemet**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-this-work-in-my-on-premises-environment"></a>Fungerar det här i min lokala miljö?

Den här funktionen fungerar inte för en ren lokal Active Directory Domain Services (AD DS)-miljö.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Min organisation kräver två Factor Authentication för att få åtkomst till resurser. Vad kan jag göra för att stödja det här kravet?

Säkerhets nycklar kommer i en mängd olika form faktorer. Kontakta enhets tillverkaren för att diskutera hur deras enheter kan aktive ras med en PIN-kod eller bio metrisk som en andra faktor.

### <a name="can-admins-set-up-security-keys"></a>Administratörer kan konfigurera säkerhets nycklar?

Vi arbetar på den här funktionen för allmän tillgänglighet (GA) för den här funktionen.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Var kan jag hitta kompatibla säkerhets nycklar?

[FIDO2 säkerhets nycklar](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Vad gör jag om jag tappar bort min säkerhets nyckel?

Du kan ta bort nycklar från Azure Portal genom att gå till sidan **säkerhets information** och ta bort säkerhets nyckeln.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Jag kan inte använda FIDO direkt efter att jag har skapat en hybrid Azure AD-ansluten dator

Om du installerar en hybrid Azure AD-ansluten dator efter domän anslutning och omstart måste du logga in med ett lösen ord och vänta tills principen har synkroniserats innan du kan använda FIDO för att logga in.

- Kontrol lera din aktuella status genom att skriva `dsregcmd /status` i ett kommando fönster och kontrol lera att både *AzureAdJoined* och *DomainJoined* visar *Ja*.
- Den här fördröjningen är en känd begränsning för domänanslutna enheter och är inte FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Jag kan inte hämta SSO till min NTLM-nätverks resurs efter att ha loggat in med FIDO och få en varning om autentiseringsuppgifter

Kontrol lera att det finns tillräckligt många domänkontrollanter som ska svara i tid för att betjäna din resurs förfrågan. Om du vill kontrol lera om du kan se en domänkontrollant som kör funktionen granskar du utdata från `nltest /dsgetdc:contoso /keylist /kdc` .

## <a name="next-steps"></a>Nästa steg

[Läs mer om lösen ords avläsande](concept-authentication-passwordless.md)
