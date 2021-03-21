---
title: Vanliga frågor och svar om Azure Active Directory enhets hantering | Microsoft Docs
description: Vanliga frågor och svar om Azure Active Directory enhets hantering.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 509d267dcdaef58a9792609a60c8a9401e90e867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199718"
---
# <a name="azure-active-directory-device-management-faq"></a>Vanliga frågor och svar om Azure Active Directory enhets hantering

## <a name="general-faq"></a>Allmänna vanliga frågor och svar

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>F: Jag registrerade enheten nyligen. Varför kan jag inte se enheten under min användar information i Azure Portal? Eller varför har enhets ägaren marker ATS som ej tillämpligt för Hybrid Azure Active Directory (Azure AD) anslutna enheter?

**A:** Windows 10-enheter som är hybrid Azure AD-anslutna visas inte under **användar enheter**.
Använd vyn **alla enheter** i Azure Portal. Du kan också använda en PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice) -cmdlet.

Endast följande enheter visas under **användar enheter**:

- Alla personliga enheter som inte är hybrid Azure AD-anslutna. 
- Alla enheter som inte kommer från Windows 10 eller Windows Server 2016.
- Alla enheter som inte är Windows-enheter. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>F: Hur gör jag för att vet du vad klientens status för enhets registrering är?

**A:** I Azure Portal går du till **alla enheter**. Sök efter enheten med enhets-ID: t. Kontrol lera värdet under kolumnen kopplings typ. Ibland kan enheten återställas eller avbildas om. Det är därför viktigt att även kontrol lera enhetens registrerings status på enheten:

- För Windows 10-och Windows Server 2016-enheter eller senare kör du `dsregcmd.exe /status` .
- För äldre OS-versioner kör du `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` .

**A:** Information om fel sökning finns i följande artiklar:
- [Felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md)
- [Felsöka hybrid Azure Active Directory anslutna Windows 10-och Windows Server 2016-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure Active Directory anslutna enheter med äldre versioner](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>F: Jag ser enhets posten under användar informationen i Azure Portal. Och jag ser det tillstånd som har registrerats på enheten. Har jag konfigurerat rätt att använda villkorlig åtkomst?

**A:** Enhetens anslutnings tillstånd, som visas efter **deviceID**, måste matcha status i Azure AD och uppfylla eventuella utvärderings villkor för villkorlig åtkomst. Mer information finns i [Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>F: Varför ser mina användare ett fel meddelande om att "din organisation har tagit bort enheten" eller "din organisation har inaktiverat enheten" på sina Windows 10-enheter?

**A:** På Windows 10-enheter som är anslutna till eller har registrerats med Azure AD utfärdas användare en [Primary Refresh token (PRT)](concept-primary-refresh-token.md) som aktiverar enkel inloggning. Giltigheten för PRT baseras på själva enhetens giltighet. Användarna ser det här meddelandet om enheten antingen tas bort eller inaktive ras i Azure AD utan att åtgärden initieras från själva enheten. En enhet kan tas bort eller inaktive ras i Azure AD något av följande scenarier: 

- Användaren inaktiverar enheten från portalen Mina appar. 
- En administratör (eller användare) tar bort eller inaktiverar enheten i Azure Portal eller med hjälp av PowerShell
- Endast ansluten till Azure AD: en administratör tar bort enhets-OU: en utanför Sync-omfånget, vilket innebär att enheterna tas bort från Azure AD
- Uppgradera Azure AD Connect till version 1.4. xx. x. Att [förstå Azure AD Connect 1.4. xx. x och enheten försvinner](../hybrid/reference-connect-device-disappearance.md).


Se nedan om hur dessa åtgärder kan korrigeras.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>F: Jag har inaktiverat eller tagit bort enheten i Azure Portal eller med hjälp av Windows PowerShell. Men det lokala läget på enheten säger att det fortfarande är registrerat. Vad ska jag göra?

**A:** Den här åtgärden är avsiktligt. I det här fallet har enheten inte åtkomst till resurser i molnet. Administratörer kan utföra den här åtgärden för inaktuella, borttappade eller stulna enheter för att förhindra obehörig åtkomst. Om åtgärden utfördes oavsiktligt måste du återaktivera eller registrera om enheten enligt beskrivningen nedan

- Om enheten har inaktiverats i Azure AD kan en administratör med tillräcklig behörighet aktivera den från Azure AD-portalen  
  > [!NOTE]
  > Om du synkroniserar enheter med hjälp av Azure AD Connect kommer hybrid Azure AD-anslutna enheter automatiskt att aktive ras igen under nästa synkronisering. Så om du behöver inaktivera en hybrid Azure AD-ansluten enhet måste du inaktivera den från din lokala AD

 - Om enheten tas bort i Azure AD måste du registrera enheten på nytt. Om du vill registrera om måste du utföra en manuell åtgärd på enheten. Se nedan för instruktioner för omregistrering baserat på enhetens tillstånd. 

      Gör så här för att registrera om hybrid Azure AD-anslutna Windows 10-och Windows Server 2016/2019-enheter:

      1. Öppna kommando tolken som administratör.
      1. Ange `dsregcmd.exe /debug /leave`.
      1. Logga ut och logga in för att utlösa den schemalagda aktivitet som registrerar enheten igen med Azure AD. 

      Utför följande steg för äldre versioner av Windows OS som är hybrid Azure AD-anslutna:

      1. Öppna kommando tolken som administratör.
      1. Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Utför följande steg för Azure AD-anslutna enheter Windows 10-enheter:

      1. Öppna kommando tolken som administratör
      1. Retur `dsregcmd /forcerecovery` (Obs! Du måste vara administratör för att utföra den här åtgärden).
      1. Klicka på Logga in i dialog rutan som öppnas och fortsätter med inloggnings processen.
      1. Logga ut och logga in igen på enheten för att slutföra återställningen.

      Utför följande steg för Azure AD-registrerade Windows 10-enheter:

      1. Gå till **Inställningar**  >  **konton**  >  **åtkomst till arbete eller skola**. 
      1. Välj kontot och välj **Koppla från**.
      1. Klicka på "+ Anslut" och registrera enheten igen genom att gå igenom inloggnings processen.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>F: Varför visas dubbla enhets poster i Azure Portal?

**En**

- För Windows 10 och Windows Server 2016 kan upprepade försök att koppla från och återansluta samma enhet orsaka dubbla poster. 
- Varje Windows-användare som använder **Lägg till arbets-eller skol konto** skapar en ny enhets post med samma enhets namn.
- För äldre Windows OS-versioner som är anslutna till en lokal Azure Directory-domän, skapar automatisk registrering en ny enhets post med samma enhets namn för varje domän användare som loggar in på enheten. 
- En Azure AD-ansluten dator som rensas, installeras om och återansluts med samma namn visas som en annan post med samma enhets namn.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>F: har Windows 10-enhets registrering i Azure AD stöd för TPM i FIPS-läge?

**A:** Registrering av Windows 10-enheter stöds bara för FIPS-kompatibla TPM 2,0 och stöds inte för TPM 1,2. Om dina enheter har FIPS-kompatibel TPM 1,2 måste du inaktivera dem innan du fortsätter med Azure AD Join eller hybrid Azure AD Join. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPM eftersom det är beroende av TPM-tillverkaren. Kontakta maskin varans OEM för support. 

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverat i Azure Portal?**

**A:** Det tar upp till en timme för en återkallning att tillämpas från den tidpunkt då Azure AD-enheten markeras som inaktive rad.

>[!NOTE] 
>För registrerade enheter rekommenderar vi att du rensar enheten för att se till att användarna inte har åtkomst till resurserna. Mer information finns i [Vad är enhets registrering?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>F: Varför finns det enheter som marker ATS som "väntar" i kolumnen registrerad i Azure Portal?

**A**: väntar betyder att enheten inte är registrerad. Det här läget anger att en enhet har synkroniserats med Azure AD Connect från en lokal AD och är redo för enhets registrering. De här enheterna har KOPPLINGs typen inställd på "hybrid Azure AD-ansluten". Läs mer om [hur du planerar hybrid Azure Active Directory Join-implementering](hybrid-azuread-join-plan.md).

>[!NOTE]
>En enhet kan också ändras från att ha ett registrerat tillstånd till "väntar"
>* Om en enhet tas bort från Azure AD först och synkroniseras igen från en lokal AD.
>* Om en enhet tas bort från ett Sync-omfång på Azure AD Connect och läggs tillbaka.
>
>I båda fallen måste du omregistrera enheten manuellt på var och en av dessa enheter. Om du vill kontrol lera om enheten har registrerats tidigare kan du [Felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md).

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>F: Jag kan inte lägga till fler än 3 Azure AD-användarkonton under samma användarsession på en Windows 10-enhet, varför?

**A**: Azure AD har lagt till stöd för flera Azure AD-konton i Windows 10 1803-versionen. Windows 10 begränsar dock antalet Azure AD-konton på en enhet till 3 för att begränsa storleken på token-begäranden och aktivera tillförlitlig enkel inloggning (SSO). När 3 konton har lagts till ser användarna ett fel för efterföljande konton. Den ytterligare problem informationen på fel skärmen ger följande meddelande som anger varför-"Lägg till konto-åtgärd blockeras eftersom konto gränsen har nåtts". 

---
## <a name="azure-ad-join-faq"></a>Vanliga frågor och svar om Azure AD Join

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>F: Hur gör jag för att koppla från en Azure AD-ansluten enhet lokalt på enheten?

**A:** För rena Azure AD-anslutna enheter kontrollerar du att du har ett lokalt administratörs konto offline eller skapar ett. Du kan inte logga in med autentiseringsuppgifter för Azure AD-användare. Gå sedan till **Inställningar**  >  **konton**  >  **åtkomst till arbete eller skola**. Välj ditt konto och välj **Koppla från**. Följ anvisningarna och ange autentiseringsuppgifter för lokal administratör när du uppmanas till det. Slutför processen för att inte ansluta genom att starta om enheten.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>F: kan mina användare logga in på Azure AD-anslutna enheter som tas bort eller inaktive ras i Azure AD?

**S:** Ja. Windows har ett cachelagrat användar namn och lösen ord som gör det möjligt för användare som loggat in tidigare att komma åt Skriv bordet snabbt utan att nätverks anslutningen är ansluten. 

När en enhet tas bort eller inaktive ras i Azure AD är den inte känd för Windows-enheten. Användare som har loggat in tidigare fortsätter att ha åtkomst till Skriv bordet med det cachelagrade användar namnet och lösen ordet. Men när enheten tas bort eller inaktive ras kan användarna inte komma åt resurser som skyddas av enhets villkorliga åtkomst. 

Användare som inte har loggat in tidigare kan inte komma åt enheten. Det finns inget cachelagrat användar namn och lösen ord aktiverat för dem. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>F: kan en inaktive rad eller borttagen användare logga in på en Azure AD-ansluten enhet

**A:** Ja, men bara under en begränsad tid. När en användare tas bort eller inaktive ras i Azure AD är det inte direkt känt för Windows-enheten. Användare som har loggat in tidigare kan komma åt Skriv bordet med det cachelagrade användar namnet och lösen ordet. 

Normalt är enheten medveten om användar statusen på mindre än fyra timmar. Sedan blockerar Windows de användarnas åtkomst till Skriv bordet. När användaren tas bort eller inaktive ras i Azure AD återkallas alla deras tokens. Därför kan de inte komma åt några resurser. 

Borttagna eller inaktiverade användare som inte har loggat in tidigare kan inte komma åt en enhet. Det finns inget cachelagrat användar namn och lösen ord aktiverat för dem. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför har mina användare problem med Azure AD-anslutna enheter när de har ändrat sitt UPN?

**A:** UPN-ändringar stöds för närvarande inte fullt ut på Azure AD-anslutna enheter. Därför Miss lyckas autentiseringen med Azure AD efter att deras UPN-ändringar ändrats. Det innebär att användare har SSO-och villkorliga åtkomst problem på sina enheter. För tillfället måste användarna logga in i Windows via panelen "annan användare" med hjälp av sitt nya UPN för att lösa det här problemet. Vi arbetar för närvarande med att lösa det här problemet. Användare som loggar in med Windows Hello för företag möter dock inte det här problemet. 

UPN-ändringar stöds med Windows 10 2004 Update. Användare på enheter med den här uppdateringen får inga problem efter att ha ändrat sina UPN

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>F: mina användare kan inte söka skrivare från Azure AD-anslutna enheter. Hur kan jag aktivera utskrift från dessa enheter?

**A:** Information om hur du distribuerar skrivare för Azure AD-anslutna enheter finns i [distribuera Windows Server Hybrid Cloud Print med förautentisering](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Du behöver en lokal Windows Server för att distribuera hybrid moln utskrift. Den molnbaserade utskrifts tjänsten är för närvarande inte tillgänglig. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>F: Hur gör jag för att ansluta till en fjärran sluten Azure AD-enhet?

**A:** Se [ansluta till en fjärransluten Azure Active Directory-ansluten dator](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>F: Varför ser jag att mina användare *inte kan komma dit*?

**A:** Konfigurerade du vissa regler för villkorlig åtkomst för att kräva ett specifikt enhets tillstånd? Om enheten inte uppfyller villkoren blockeras användarna och de ser meddelandet. Utvärdera princip reglerna för villkorlig åtkomst. Kontrol lera att enheten uppfyller villkoren för att undvika meddelandet.

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>F: Varför får inte några av mina användare några Azure AD Multi-Factor Authentication-frågor på Azure AD-anslutna enheter?

**A:** En användare kan ansluta till eller registrera en enhet med Azure AD med hjälp av Multi-Factor Authentication. Sedan blir själva enheten en betrodd andra faktor för den användaren. När samma användare loggar in på enheten och använder ett program, anser Azure AD enheten som en andra faktor. Den gör det möjligt för användaren att sömlöst komma åt program utan ytterligare Multi-Factor Authentication-prompter. 

Detta beteende:

- Är tillämpligt på Azure AD-anslutna och registrerade Azure AD-enheter – men inte för anslutna hybrid Azure AD-enheter.
- Gäller inte för någon annan användare som loggar in på enheten. Alla andra användare som har åtkomst till enheten får en Multi-Factor Authentication-utmaning. De kan sedan komma åt program som kräver Multi-Factor Authentication.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>F: Varför får jag ett *användar namn eller lösen ord är felaktigt* meddelande för en enhet som jag precis har anslutit till Azure AD?

**A:** Vanliga orsaker till det här scenariot är följande:

- Autentiseringsuppgifterna för användaren är inte längre giltiga.
- Datorn kan inte kommunicera med Azure Active Directory. Kontrol lera om det finns problem med nätverks anslutningen.
- Federerade inloggningar kräver att Federations servern stöder WS-Trust slut punkter som är aktiverade och tillgängliga. 
- Direkt autentisering har Aktiver ATS. Det tillfälliga lösen ordet måste ändras när du loggar in.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>F: Varför visas *Hoppsan... ett fel uppstod!* dialog ruta när jag försöker att ansluta till Azure AD till min dator?

**A:** Det här felet uppstår när du konfigurerar Azure Active Directory-registrering med Intune. Kontrol lera att den användare som försöker använda Azure AD Join har rätt Intune-licens tilldelad. Mer information finns i [Konfigurera registrering för Windows-enheter](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>F: Varför gick det inte att ansluta till Azure AD till en dator, men jag fick ingen fel information?

**A:** En sannolik orsak är att du har loggat in på enheten med det lokala inbyggda administratörs kontot. Skapa ett annat lokalt konto innan du använder Azure Active Directory anslutning för att slutföra installationen. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What är MS-Organization-P2P-Access-certifikaten som finns på våra Windows 10-enheter?

**A:** MS-Organization-P2P-Access-certifikat utfärdas av Azure AD till både Azure AD-anslutna och hybrid Azure AD-anslutna enheter. Dessa certifikat används för att aktivera förtroende mellan enheter i samma klient organisation för scenarier med fjärr skrivbord. Ett certifikat utfärdas till enheten och ett annat utfärdas till användaren. Enhets certifikatet finns i `Local Computer\Personal\Certificates` och är giltigt i en dag. Certifikatet förnyas (genom att utfärda ett nytt certifikat) om enheten fortfarande är aktiv i Azure AD. Användar certifikatet finns i `Current User\Personal\Certificates` och det här certifikatet är också giltigt i en dag, men det utfärdas på begäran när en användare försöker använda en fjärrskrivbordssession till en annan Azure AD-ansluten enhet. Den förnyas inte när den upphör att gälla. Båda dessa certifikat utfärdas med hjälp av certifikatet MS-Organization-P2P-Access som finns i `Local Computer\AAD Token Issuer\Certificates` . Certifikatet utfärdas av Azure AD vid enhets registrering. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why ser jag flera utgångna certifikat som utfärdats av MS-Organization-P2P-Access på våra Windows 10-enheter? Hur kan jag ta bort dem?

**A:** Ett problem som identifierats i Windows 10 version 1709 och lägre WHERE upphör ande MS-Organization-P2P-Access-certifikat finns kvar i dator arkivet på grund av kryptografiska problem. Användarna kan stöta på problem med nätverks anslutningen, om du använder VPN-klienter (till exempel Cisco AnyConnect) som inte kan hantera det stora antalet certifikat som har upphört att gälla. Det här problemet har åtgärd ATS i Windows 10 1803-versionen för att automatiskt ta bort eventuella utgångna MS-Organization-P2P-Access-certifikat. Du kan lösa problemet genom att uppdatera dina enheter till Windows 10 1803. Om du inte kan uppdatera kan du ta bort dessa certifikat utan någon negativ påverkan.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Vanliga frågor och svar om hybrid Azure AD-anslutning

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>F: Hur gör jag för att koppla från en hybrid Azure AD-ansluten enhet lokalt på enheten?

**A:** För Hybrid Azure AD-anslutna enheter ser du till att inaktivera automatisk registrering. Den schemalagda aktiviteten registrerar inte enheten igen. Öppna sedan en kommando tolk som administratör och ange `dsregcmd.exe /debug /leave` . Eller kör det här kommandot som ett skript över flera enheter för att koppla från i bulk.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>F: var kan jag hitta felsöknings information för att diagnostisera problem med att ansluta till hybrid Azure AD?

**A:** Information om fel sökning finns i följande artiklar:

- [Felsöka hybrid Azure Active Directory anslutna Windows 10-och Windows Server 2016-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure Active Directory anslutna enheter med äldre versioner](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>F: Varför visas en dubblett av Azure AD-registrerade poster för min Windows 10 hybrid Azure AD-anslutna enhet i listan Azure AD-enheter?

**A:** När användarna lägger till sina konton i appar på en domänansluten enhet kan de uppmanas att **lägga till kontot i Windows?** Om de anger **Ja** i frågan registreras enheten med Azure AD. Förtroende typen är markerad som Azure AD registrerad. När du har aktiverat hybrid Azure AD Join i din organisation får enheten även hybrid Azure AD-anslutning. Sedan visas två enhets tillstånd för samma enhet. 

Hybrid Azure AD Join prioriteras över Azure AD-registrerat tillstånd. Enheten betraktas som hybrid Azure AD-ansluten för all autentisering och utvärdering av villkorlig åtkomst. Du kan på ett säkert sätt ta bort den registrerade enhets posten i Azure AD från Azure AD-portalen. Lär dig att [undvika eller rensa upp det här dubbla läget på Windows 10-datorn](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför har mina användare problem med Windows 10 hybrid Azure AD-anslutna enheter när de har ändrat sitt UPN?

**A:** För närvarande stöds inte UPN-ändringar fullt ut med hybrid Azure AD-anslutna enheter. Användare kan logga in på enheten och komma åt sina lokala program, men autentisering med Azure AD Miss lyckas efter en UPN-ändring. Det innebär att användare har SSO-och villkorliga åtkomst problem på sina enheter. För tillfället måste du koppla från enheten från Azure AD (kör "dsregcmd/Leave" med utökade privilegier) och återansluta (sker automatiskt) för att lösa problemet. Vi arbetar för närvarande med att lösa det här problemet. Användare som loggar in med Windows Hello för företag möter dock inte det här problemet. 

UPN-ändringar stöds med Windows 10 2004 Update. Användare på enheter med den här uppdateringen får inga problem efter att ha ändrat sina UPN

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>F: gör Windows 10 hybrid Azure AD-anslutna enheter kräver detaljerad information till domänkontrollanten för att få åtkomst till moln resurser?

**A:** Nej, förutom när användarens lösen ord ändras. När Windows 10 hybrid Azure AD Join har slutförts, och användaren har loggat in minst en gång, behöver inte enheten ha sikter för att få åtkomst till moln resurser. Windows 10 kan få enkel inloggning till Azure AD-program från var som helst med en Internet anslutning, förutom när ett lösen ord ändras. Användare som loggar in med Windows Hello för företag fortsätter att komma igång med enkel inloggning till Azure AD-program även efter en lösen ords ändring, även om de inte har en detaljerad linje för sin domänkontrollant. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>F: Vad händer om en användare ändrar sitt lösen ord och försöker logga in på deras Windows 10 hybrid Azure AD-anslutna enhet utanför företags nätverket?

**A:** Om ett lösen ord ändras utanför företags nätverket (till exempel med hjälp av Azure AD SSPR), kommer användaren att logga in med det nya lösen ordet att Miss sen. För Hybrid Azure AD-anslutna enheter är lokala Active Directory den primära utfärdaren. När en enhet inte har en detaljerad syn på domänkontrollanten kan du inte verifiera det nya lösen ordet. Användaren måste därför upprätta en anslutning till domänkontrollanten (antingen via VPN eller vara i företags nätverket) innan de kan logga in på enheten med det nya lösen ordet. Annars kan de bara logga in med sitt gamla lösen ord på grund av cachelagrat inloggnings funktion i Windows. Det gamla lösen ordet är dock inaktiverat av Azure AD under Tokenbegäran och därmed förhindrar enkel inloggning och Miss lyckas alla enhets principer för villkorlig åtkomst. Det här problemet uppstår inte om du använder Windows Hello för företag. 

---

## <a name="azure-ad-register-faq"></a>Vanliga frågor om Azure AD-register

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>F: Hur gör jag för att ta bort ett registrerat Azure AD-tillstånd för en enhet lokalt?

**En** 
- För registrerade enheter med Windows 10 Azure AD går du till **Inställningar**  >  **konton**  >  **åtkomst till arbete eller skola**. Välj ditt konto och välj **Koppla från**. Enhets registrering är per användar profil i Windows 10.
- För iOS och Android kan du använda Microsoft Authenticator program **Inställningar**  >  **enhets registrering** och välja **avregistrera enheten**.
- För macOS kan du använda Microsoft Intune Företagsportal programmet för att avregistrera enheten från hanteringen och ta bort eventuell registrering. 

För Windows 10-enheter kan den här processen automatiseras med [verktyget för borttagning av Workplace Join (WPJ)](https://download.microsoft.com/download/8/e/f/8ef13ae0-6aa8-48a2-8697-5b1711134730/WPJCleanUp.zip)

> [!NOTE]
> Det här verktyget tar bort alla SSO-konton på enheten. Efter den här åtgärden går alla program förlorade SSO-tillstånd och enheten avregistreras från hanterings verktyg (MDM) och avregistreras från molnet. Nästa gång ett program försöker logga in uppmanas användarna att lägga till kontot igen.

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>F: Hur kan jag blockera användare från att lägga till ytterligare arbets konton (registrerade Azure AD) på mina företags Windows 10-enheter?

**A:** Aktivera följande register för att hindra användarna från att lägga till ytterligare arbets konton till din företags domän, Azure AD-ansluten eller hybrid Azure AD-anslutna Windows 10-enheter. Den här principen kan även användas för att blockera domänanslutna datorer från att oavsiktligt få Azure AD registrerat med samma användar konto. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>F: kan jag registrera Android-eller iOS BYOD-enheter?

**A:** Ja, men endast med Azure Device Registration service och för Hybrid kunder. Den stöds inte med den lokala registrerings tjänsten för enheter i Active Directory Federation Services (AD FS) (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>F: Hur kan jag registrera en macOS-enhet?

**A:** Utför följande steg:

1.    [Skapa en policy för efterlevnad](/intune/compliance-policy-create-mac-os)
1.    [Definiera en princip för villkorlig åtkomst för macOS-enheter](../conditional-access/overview.md) 

**!**

- Användare som ingår i principen för villkorlig åtkomst behöver en [version av Office som stöds för MacOS](../conditional-access/concept-conditional-access-conditions.md) för att komma åt resurser. 
- Under det första försöket till åtkomst uppmanas användarna att registrera enheten med hjälp av företags portalen.

---
## <a name="next-steps"></a>Nästa steg

- Läs mer om [registrerade Azure AD-enheter](concept-azure-ad-register.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join-hybrid.md)
