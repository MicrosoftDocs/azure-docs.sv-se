---
title: Kända problem och fel sökning för Hybrid FIDO2 säkerhets nycklar – Azure Active Directory
description: Lär dig mer om kända problem och hur du felsöker lösen ords lösa hybrid FIDO2 för säkerhets nycklar med hjälp av Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648788"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Fel sökning för Hybrid distributioner av FIDO2-säkerhetsnycklar i Azure AD 

Den här artikeln beskriver vanliga frågor om hybrid Azure AD-anslutna enheter och lösen ords lös inloggning till lokal resurser. Med den här funktionen för lösen ords skydd kan du aktivera Azure AD-autentisering på Windows 10-enheter för Hybrid Azure AD-anslutna enheter med hjälp av FIDO2-säkerhetsnycklar. Användare kan logga in på Windows på sina enheter med moderna autentiseringsuppgifter som FIDO2-nycklar och komma åt traditionella Active Directory Domain Services (AD DS) baserade resurser med en sömlös enkel inloggning (SSO) till sina lokal-resurser.

Följande scenarier för användare i en hybrid miljö stöds:

* Logga in på Hybrid Azure AD-anslutna enheter med FIDO2-säkerhetsnycklar och få SSO-åtkomst till lokal-resurser.
* Logga in på Azure AD-anslutna enheter med FIDO2-säkerhetsnycklar och få SSO-åtkomst till lokal-resurser.

Information om hur du kommer igång med FIDO2-säkerhetsnycklar och hybrid åtkomst till lokala resurser finns i följande artiklar:

* [Lösenordsfria säkerhetsnycklar](howto-authentication-passwordless-security-key.md)
* [Lösenordsfritt Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Lösenordsfritt lokalt](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>Kända problem

* [Användare kan inte logga in med FIDO2-säkerhetsnycklar eftersom Windows Hello FACET är för snabbt och är standard mekanismen för inloggning](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Användare kan inte använda FIDO2 säkerhets nycklar direkt efter att de har skapat en hybrid Azure AD-ansluten dator](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Användare kan inte få SSO till min NTLM-nätverks resurs efter att ha loggat in med en säkerhets nyckel för FIDO2 och ta emot en fråga om autentiseringsuppgifter](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Användare kan inte logga in med FIDO2-säkerhetsnycklar eftersom Windows Hello FACET är för snabbt och är standard mekanismen för inloggning

Windows Hello Face är den bästa upplevelsen för en enhet där en användare har registrerats. FIDO2 säkerhets nycklar är avsedda att användas på delade enheter eller där registrering av Windows Hello för företag är en barriär.

Om Windows Hello Face hindrar användarna från att försöka använda inloggnings scenariot för FIDO2-säkerhetsnyckeln kan användarna inaktivera Hello Face-inloggning genom att ta bort ansikts registrering i **inställningar > Sign-In alternativ**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Användare kan inte använda FIDO2 säkerhets nycklar direkt efter att de har skapat en hybrid Azure AD-ansluten dator

Efter processen för domän anslutning och omstart vid en ren installation av en hybrid Azure AD-ansluten dator måste du logga in med ett lösen ord och vänta tills principen har synkroniserats innan du kan använda för att använda en FIDO2 säkerhets nyckel för att logga in.

Det här beteendet är en känd begränsning för domänanslutna enheter och är inte särskilt unik för FIDO2 säkerhets nycklar.

Använd kommandot för att kontrol lera aktuell status `dsregcmd /status` . Kontrol lera att både *AzureAdJoined* och *DomainJoined* visar *Ja*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Användare kan inte få SSO till min NTLM-nätverks resurs efter att ha loggat in med en säkerhets nyckel för FIDO2 och ta emot en fråga om autentiseringsuppgifter

Kontrol lera att tillräckligt många domänkontrollanter har korrigerats för att svara i tid för att betjäna din resurs förfrågan. Om du vill kontrol lera om du kan se en server som kör funktionen granskar du utdata från `nltest /dsgetdc:<dc name> /keylist /kdc`

Om du kan se en DOMÄNKONTROLLANT med den här funktionen kan användarens lösen ord ha ändrats sedan de loggade in, eller så finns det ett annat problem. Samla in loggar enligt beskrivningen i följande avsnitt för Microsoft Support-teamet för fel sökning.

## <a name="troubleshoot"></a>Felsöka

Det finns två områden för att felsöka [problem med Windows-klienter](#windows-client-issues)eller [distributions problem](#deployment-issues).

### <a name="windows-client-issues"></a>Problem med Windows-klienter

Utför följande steg för att samla in data som hjälper till att felsöka problem med att logga in på Windows eller komma åt lokala resurser från Windows 10-enheter:

1. Öppna appen för **feedback Hub** . Se till att ditt namn finns längst ned till vänster i appen och välj sedan **skapa ett nytt feedback-objekt**.

    För typ av feedback-objekt väljer du *problem*.

1. Välj kategorin *säkerhet och sekretess* , sedan *Fido* -underkategorin.
1. Markera kryss rutan för *Skicka bifogade filer och diagnostik till Microsoft tillsammans med min feedback*.
1. Välj *Återskapa mina problem* och starta sedan *avbildningen*.
1. Lås och lås upp datorn med säkerhets nyckeln FIDO2. Försök att låsa upp med andra autentiseringsuppgifter om problemet uppstår.
1. Gå tillbaka till **feedback Hub**, Välj **Avbryt fångst** och skicka din feedback.
1. Gå till *feedback* -sidan, sedan fliken *min feedback* . Välj din nyligen skickade feedback.
1. Välj knappen *dela* i det övre högra hörnet för att få en länk till feedbacken. Ta med den här länken när du öppnar ett support ärende eller svarar på teknikern som tilldelats ett befintligt support ärende.

Följande händelse loggar och register nyckel information samlas in:

**Registernycklar**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Diagnostikinformation**

* Live kernel-dump
* Samla in information om AppX-paket
* UIF-kontext-filer

**Händelseloggar**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Distributions problem

Om du vill felsöka problem med att distribuera Azure AD Kerberos-servern använder du den nya PowerShell-modulen som ingår i Azure AD Connect.

#### <a name="viewing-the-logs"></a>Visa loggarna

Azure AD Kerberos-serverns PowerShell-cmdletar använder samma loggning som standard Azure AD Connects guiden. Utför följande steg om du vill visa information om eller fel information från cmdletarna:

1. Bläddra till på Azure AD Connect-servern `C:\ProgramData\AADConnect\` . Den här mappen är dold som standard.
1. Öppna och visa den senaste `trace-*.log` filen som finns i katalogen.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Visa Azure AD Kerberos-Server objekt

Om du vill visa Azure AD Kerberos-serverns objekt och kontrol lera att de är i rätt ordning, utför du följande steg:

1. Öppna PowerShell på Azure AD Connect-servern och gå till `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Kör följande PowerShell-kommandon för att Visa Azure AD Kerberos-servern från både Azure AD och den lokala AD DS.

    Ersätt *Corp.contoso.com* med namnet på din lokala AD DS-domän.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Kommandot matar ut egenskaperna för Azure AD Kerberos-servern från både Azure AD och den lokala AD DS. Granska egenskaperna för att kontrol lera att allt är i rätt ordning. Använd tabellen nedan för att kontrol lera egenskaperna.

Den första uppsättningen egenskaper är från objekten i den lokala AD DS-miljön. Den andra halvan (de egenskaper som börjar med * Cloud * *) kommer från Kerberos-serverobjektet i Azure AD:

| Egenskap           | Beskrivning  |
|--------------------|--------------|
| Id                 | Unikt ID för AD DS *-* domänkontrollantens objekt. |
| DomainDnsName      | DNS-domännamnet för AD DS-domänen. |
| ComputerAccount    | Objektet dator konto för objektet Azure AD Kerberos-Server (DOMÄNKONTROLLANT). |
| UserAccount        | Det inaktiverade objektet User Account som innehåller Azure AD Kerberos-serverns TGT-krypterings nyckel. Kontots DN är *CN = krbtgt_AzureAD, CN = Users, <Domain-DN>* |
| Version         | Nyckel versionen av Azure AD Kerberos-serverns TGT-krypterings nyckel. Versionen tilldelas när nyckeln skapas. Versionen ökas sedan varje gång nyckeln roteras. Ökningarna baseras på metadata för replikering och kommer troligen att vara större än ett.<br /><br /> Den ursprungliga *versionen av versionen* kan till exempel vara *192272*. Första gången som nyckeln roterades kan versionen gå vidare till *212621*.<br /><br /> Det är viktigt att kontrol lera att *versionen* för det lokala objektet och *CloudKeyVersion* för moln objekt är desamma. |
| KeyUpdatedOn       | Det datum och den tid som Azure AD Kerberos-servern TGT-krypteringsnyckeln uppdaterades eller skapades. |
| KeyUpdatedFrom     | Den DOMÄNKONTROLLANT där Azure AD Kerberos-servern TGT-krypteringsnyckeln senast uppdaterades. |
| CloudId            | ID från Azure AD *-* objektet. Måste matcha ovanstående *ID* . |
| CloudDomainDnsName | *DomainDnsName* från Azure AD-objektet. Måste matcha *DomainDnsName* ovan. |
| CloudKeyVersion    | *Versionen* från Azure AD-objektet. Måste stämma överens med ovanstående *version* . |
| CloudKeyUpdatedOn  | *KeyUpdatedOn* från Azure AD-objektet. Måste matcha *KeyUpdatedOn* ovan. |

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med FIDO2-säkerhetsnycklar och hybrid åtkomst till lokala resurser finns i följande artiklar:

* [Lösenordsfria FIDO2-säkerhetsnycklar](howto-authentication-passwordless-security-key.md)
* [Lösenordsfritt Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Lösenordsfritt lokalt](howto-authentication-passwordless-security-key-on-premises.md)
