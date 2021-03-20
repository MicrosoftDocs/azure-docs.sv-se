---
title: Implementera hash-synkronisering av lösen ord med Azure AD Connect Sync | Microsoft Docs
description: Innehåller information om hur Lösenordssynkronisering för hash fungerar och hur du konfigurerar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47d7d541ed7d9805641ffdfde381d482c8700006
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858747"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implement password hash synchronization with Azure AD Connect sync (Implementera synkronisering av lösenordshash med Azure AD Connect-synkronisering)
Den här artikeln innehåller information som du behöver för att synkronisera dina användar lösen ord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory-instans (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar hash-synkronisering
Den Active Directory domän tjänsten lagrar lösen ord i form av en hash-värde representation av det faktiska användar lösen ordet. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion ( *hash-algoritmen*). Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. 

För att synkronisera ditt lösen ord extraherar Azure AD Connect sync din lösen ords-hash från den lokala Active Directory-instansen. Extra säkerhets bearbetning tillämpas på lösen ordets hash innan den synkroniseras med Azure Active Directory-Autentiseringstjänsten. Lösen ord synkroniseras per användare och i kronologisk ordning.

Det faktiska data flödet för processen för synkronisering av lösen ords-hash liknar synkroniseringen av användar data. Lösen ord synkroniseras dock oftare än standard fönstret för katalog synkronisering för andra attribut. Processen för synkronisering av lösen ord för hash körs var 2: e minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar ett lösen ord skriver det över det befintliga moln lösen ordet.

Första gången du aktiverar funktionen för hash-synkronisering av lösen ord utför den en inledande synkronisering av lösen orden för alla användare i omfånget. Du kan inte uttryckligen definiera en delmängd av användar lösen ord som du vill synkronisera. Men om det finns flera kopplingar är det möjligt att inaktivera hash-synkronisering av lösen ord för vissa anslutningar, men inte andra som använder cmdleten [set-ADSyncAADPasswordSyncConfiguration](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md) .

När du ändrar ett lokalt lösen ord, synkroniseras det uppdaterade lösen ordet, oftast på bara några minuter.
Funktionen för synkronisering av lösen ord för hash-meddelanden misslyckade försök att synkronisera automatiskt. Om ett fel inträffar under ett försök att synkronisera ett lösen ord loggas ett fel i logg boken.

Synkroniseringen av ett lösen ord påverkar inte den användare som för tillfället är inloggad.
Den aktuella Cloud Service-sessionen påverkas inte omedelbart av en synkroniserad lösen ords ändring som inträffar, medan du är inloggad i en moln tjänst. Men när moln tjänsten kräver att du autentiserar igen måste du ange ditt nya lösen ord.

En användare måste ange sina företags uppgifter en andra gång för att autentisera till Azure AD, oavsett om de är inloggade i företagets nätverk. Det här mönstret kan minimeras, men om användaren markerar kryss rutan Håll mig inloggad (KMSI avgör) vid inloggning. Det här alternativet anger en sessions-cookie som hoppar över autentisering i 180 dagar. KMSI avgör-beteende kan aktive ras eller inaktive ras av Azure AD-administratören. Dessutom kan du minska lösen ords prompten genom att aktivera [sömlös SSO](how-to-connect-sso.md), som automatiskt signerar användare i när de befinner sig på företagets enheter som är anslutna till företagets nätverk.

> [!NOTE]
> Lösenordssynkronisering stöds endast för objekt typ användaren i Active Directory. Det finns inte stöd för objekt typen iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Detaljerad beskrivning av hur hash-synkronisering av lösen ord fungerar

I följande avsnitt beskrivs i djup hur synkroniseringen av lösen ord för hash fungerar mellan Active Directory och Azure AD.

![Detaljerat lösen flöde](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Varannan minut begär agenten för Lösenordssynkronisering på AD Connect-servern lagrade lösenords-hashvärden (attributet unicodePwd) från en DOMÄNKONTROLLANT.  Den här begäran är via standard [-MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) som används för att synkronisera data mellan domänkontrollanter. Tjänst kontot måste ha replikerade katalog ändringar och replikera katalogen ändrar alla AD-behörigheter (beviljas som standard vid installation) för att hämta lösen ordets hash-värden.
2. Innan du skickar krypterar DOMÄNKONTROLLANTen MD4 Password hash med hjälp av en nyckel som är en [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) -hash av RPC-sessionsnyckeln och en salt. Den skickar sedan resultatet till agenten för Lösenordssynkronisering via RPC. DOMÄNKONTROLLANTen skickar också saltet till synkroniseringstjänsten med hjälp av DC-replikeringstjänsten, så att agenten kan dekryptera kuvertet.
3. När agenten för Lösenordssynkronisering har krypterat kuvert används [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) och salt för att generera en nyckel för att dekryptera mottagna data till ursprungligt MD4-format. Agenten för Lösenordssynkronisering har aldrig åtkomst till lösen ordet för klartext. Password hash Sync-agentens användning av MD5 är strikt för replikering av autentiseringsprotokoll med DOMÄNKONTROLLANTen och används bara lokalt mellan DOMÄNKONTROLLANTen och agenten för Lösenordssynkronisering.
4. Agenten för Lösenordssynkronisering expanderar den 16-bytes binära lösen ords-hashen till 64 byte genom att först konvertera hashen till en hexadecimal sträng på 32 byte och sedan konvertera den här strängen till binär med UTF-16-kodning.
5. Agenten för Lösenordssynkronisering lägger till ett per användare-salt, som består av en längd på 10 bytes, till 64 byte-binärfilen för att ytterligare skydda den ursprungliga hashen.
6. Agenten för synkronisering av lösen ords-hash kombinerar sedan MD4-hash plus per användarens salt och indata indata i funktionen [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) . 1000 iterationer av den förkonfigurerade hash-algoritmen för [HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) används. 
7. Agenten för Lösenordssynkronisering tar den resulterande 32-byte-hashen och sammanfogar både per användarens salt och antalet SHA256-iterationer (används av Azure AD) och skickar sedan strängen från Azure AD Connect till Azure AD över TLS.</br> 
8. När en användare försöker logga in på Azure AD och anger sitt lösen ord, körs lösen ordet genom samma MD4 + salt + PBKDF2 + HMAC-SHA256 process. Om den resulterande hashen matchar hashen som lagras i Azure AD har användaren angett rätt lösen ord och autentiseras.

> [!NOTE]
> Den ursprungliga MD4-hashen överförs inte till Azure AD. I stället överförs SHA256-hashen för den ursprungliga MD4-hashen. Det innebär att om hashen som lagras i Azure AD hämtas, kan den inte användas i en lokal pass-The-hash-attack.

### <a name="security-considerations"></a>Säkerhetsöverväganden

Vid synkronisering av lösen ord exponeras inte den rena text versionen av ditt lösen ord för funktionen för synkronisering av lösen ord, till Azure AD eller någon av de associerade tjänsterna.

Användarautentisering sker mot Azure AD snarare än gentemot organisationens egna Active Directory instans. De SHA256-lösenords data som lagras i Azure AD – en hash av den ursprungliga MD4-hashen – är säkrare än vad som lagras i Active Directory. Eftersom den här SHA256-hashen inte kan dekrypteras går den inte att återgå till organisationens Active Directory miljö och visas som ett giltigt användar lösen ord i en pass-The-hash-attack.

### <a name="password-policy-considerations"></a>Överväganden för lösen ords principer

Det finns två typer av lösen ords principer som påverkas av att du aktiverar synkronisering av lösen ords-hash:

* Princip för lösen ords komplexitet
* Princip för förfallo datum för lösen ord

#### <a name="password-complexity-policy"></a>Princip för lösen ords komplexitet

När Lösenordssynkronisering är aktiverat åsidosätter komplexitets principerna för lösen ord i den lokala Active Directory-instansen komplexitets principer i molnet för synkroniserade användare. Du kan använda alla giltiga lösen ord från din lokala Active Directory-instans för att få åtkomst till Azure AD-tjänster.

> [!NOTE]
> Lösen ord för användare som skapas direkt i molnet omfattas fortfarande av lösen ords principer som definieras i molnet.

#### <a name="password-expiration-policy"></a>Princip för förfallo datum för lösen ord

Om en användare är inom omfånget för synkronisering av lösen ords-hash är moln kontots lösen ord inställt på att *aldrig upphöra att gälla*.

Du kan fortsätta att logga in på dina moln tjänster genom att använda ett synkroniserat lösen ord som har upphört att gälla i din lokala miljö. Ditt moln lösen ord uppdateras nästa gången du ändrar lösen ordet i den lokala miljön.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Om det finns synkroniserade användare som bara interagerar med Azure AD-integrerade tjänster och måste också uppfylla en princip för lösen ords giltighet, kan du tvinga dem att följa din Azure AD-princip för lösen ords giltighet genom att aktivera funktionen *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

När *EnforceCloudPasswordPolicyForPasswordSyncedUsers* är inaktiverat (vilket är standardinställningen) anger Azure AD Connect attributet PasswordPolicies för synkroniserade användare till "DisablePasswordExpiration". Detta görs varje gång en användares lösen ord synkroniseras och instruerar Azure AD att ignorera lösen ordets giltighets princip för lösen ord för den användaren. Du kan kontrol lera värdet för attributet med hjälp av Azure AD PowerShell-modulen med följande kommando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Om du vill aktivera funktionen EnforceCloudPasswordPolicyForPasswordSyncedUsers kör du följande kommando med hjälp av MSOnline PowerShell-modulen som visas nedan. Du skulle behöva skriva Ja för parametern aktivera enligt nedan:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

När den är aktive rad går Azure AD inte till varje synkroniserad användare för att ta bort `DisablePasswordExpiration` värdet från PasswordPolicies-attributet. I stället `DisablePasswordExpiration` tas värdet bort från PasswordPolicies under nästa lösen ords-hash-synkronisering för varje användare vid nästa lösen ords ändring i lokal AD.

Vi rekommenderar att du aktiverar EnforceCloudPasswordPolicyForPasswordSyncedUsers innan du aktiverar synkronisering av lösen ords-hash, så att den inledande synkroniseringen av lösen ords-hashar inte lägger till `DisablePasswordExpiration` värdet i PasswordPolicies-attributet för användarna.

Standard lösen ords principen för Azure AD kräver att användare ändrar sina lösen ord var 90: e dag. Om din princip i AD också är 90 dagar ska de två principerna matcha. Men om AD-principen inte är 90 dagar kan du uppdatera lösen ords principen för Azure AD så att den matchar med hjälp av Set-MsolPasswordPolicy PowerShell-kommandot.

Azure AD har stöd för en separat princip för förfallo datum för lösen ord per registrerad domän.

Varningar: om det finns synkroniserade konton som måste ha lösen ord som inte upphör att gälla i Azure AD måste du uttryckligen lägga till `DisablePasswordExpiration` värdet i PasswordPolicies-attributet för användarobjektet i Azure AD.  Det kan du göra genom att köra följande kommando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> PowerShell-kommandot Set-MsolPasswordPolicy fungerar inte på federerade domäner. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Synkronisera tillfälliga lösen ord och "tvinga lösen ords ändring vid nästa inloggning"

Det är vanligt att tvinga en användare att ändra sina lösen ord under sin första inloggning, särskilt efter att administratörs lösen ordet har återställts.  Det kallas vanligt vis att ange ett "tillfälligt" lösen ord och slutförs genom att markera flaggan "användaren måste byta lösen ord vid nästa inloggning" på ett användar objekt i Active Directory (AD).
  
Funktionen för temporära lösen ord hjälper till att säkerställa att överföringen av ägarskapet för autentiseringsuppgiften har slutförts vid första användningen, för att minimera tiden i vilken mer än en person har kännedom om den autentiseringsuppgiften.

Om du vill ha stöd för tillfälliga lösen ord i Azure AD för synkroniserade användare kan du aktivera funktionen *ForcePasswordChangeOnLogOn* genom att köra följande kommando på Azure AD Connect-servern:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Att tvinga en användare att ändra sina lösen ord vid nästa inloggning kräver en lösen ords ändring samtidigt.  Azure AD Connect hämtar inte flaggan påtvinga ändring av lösen ord fristående. Det kompletterar den identifierade lösen ords ändringen som sker under synkroniseringen av lösen ords-hash.

> [!CAUTION]
> Du bör endast använda den här funktionen när tillbakaskrivning av SSPR och lösen ord är aktiverade på klienten.  Detta är så att om en användare ändrar sitt lösen ord via SSPR, kommer den att synkroniseras till Active Directory.

#### <a name="account-expiration"></a>Kontots förfallo datum

Om din organisation använder attributet accountExpires som en del av användar konto hantering, synkroniseras inte det här attributet med Azure AD. Det innebär att ett utgånget Active Directory-konto i en miljö som kon figurer ATS för synkronisering av lösen ords-hash fortfarande är aktivt i Azure AD. Vi rekommenderar att om kontot har upphört att gälla ska en arbets flödes åtgärd utlösa ett PowerShell-skript som inaktiverar användarens Azure AD-konto (Använd cmdleten [set-AzureADUser](/powershell/module/azuread/set-azureaduser) ). När kontot är påslaget måste Azure AD-instansen vara aktive rad.

### <a name="overwrite-synchronized-passwords"></a>Skriv över synkroniserade lösen ord

En administratör kan manuellt återställa ditt lösen ord med hjälp av Windows PowerShell.

I det här fallet åsidosätter det nya lösen ordet det synkroniserade lösen ordet och alla lösen ords principer som definierats i molnet tillämpas på det nya lösen ordet.

Om du ändrar ditt lokala lösen ord igen synkroniseras det nya lösen ordet till molnet och åsidosätter det manuellt uppdaterade lösen ordet.

Synkroniseringen av ett lösen ord påverkar inte den Azure-användare som är inloggad. Den aktuella Cloud Service-sessionen påverkas inte direkt av en synkroniserad lösen ords ändring som inträffar när du är inloggad i en moln tjänst. KMSI avgör utökar varaktigheten för den här skillnaden. När moln tjänsten kräver att du autentiserar igen måste du ange ditt nya lösen ord.

### <a name="additional-advantages"></a>Ytterligare fördelar

- I allmänhet är Lösenordssynkronisering enklare att implementera än en Federations tjänst. Det krävs inga ytterligare servrar och eliminerar beroendet av en Federations tjänst med hög tillgänglighet för att autentisera användare.
- Hash-synkronisering av lösen ord kan också aktive ras utöver Federation. Den kan användas som reserv om Federations tjänsten upplever ett avbrott.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Synkronisering av lösenords-hash för Azure AD Domain Services

Om du använder Azure AD Domain Services för att tillhandahålla äldre autentisering för program och tjänster som behöver använda Kerberos, LDAP eller NTLM, ingår vissa ytterligare processer i flödet för Lösenordssynkronisering. Azure AD Connect använder ytterligare följande process för att synkronisera Password-hashar till Azure AD för användning i Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.
>
> Azure AD Connect synkroniserar bara äldre lösen ords-hashar när du aktiverar Azure AD DS för Azure AD-klienten. Följande steg används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller enkla LDAP-bindningar, rekommenderar vi att du inaktiverar hash-synkronisering av NTLM-lösenord för Azure AD DS. Mer information finns i [inaktivera svaga chiffersviter och hash-synkronisering av NTLM-autentiseringsuppgifter](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect hämtar den offentliga nyckeln för klient Azure AD Domain Servicess instansen.
1. När en användare ändrar sitt lösen ord lagrar den lokala domänkontrollanten resultatet av lösen ords ändringen (hash-värden) i två attribut:
    * *unicodePwd* för NTLM-lösenords-hash.
    * *supplementalCredentials* för Kerberos-lösenords-hash.
1. Azure AD Connect identifierar lösen ords ändringar via Directory Replication-kanal (attributändringar som behöver replikeras till andra domänkontrollanter).
1. För varje användare vars lösen ord har ändrats utför Azure AD Connect följande steg:
    * Genererar en slumpmässig AES 256-bitars symmetrisk nyckel.
    * Genererar en slumpmässig initierings vektor som krävs för den första avrundade krypteringen.
    * Extraherar Kerberos-lösenords-hashvärden från *supplementalCredentials* -attributen.
    * Kontrollerar inställningen för *SyncNtlmPasswords* Azure AD Domain Services av säkerhets konfiguration.
        * Om den här inställningen är inaktive rad genererar en slumpmässig, hög entropi NTLM-hash (skiljer sig från användarens lösen ord). Denna hash kombineras sedan med exakta Kerberos-lösenords-hashvärden från attributet *supplementalCrendetials* till en data struktur.
        * Om aktive rad kombineras värdet för attributet *unicodePwd* med de extraherade Kerberos-lösenords-hasharna från attributet *supplementalCredentials* till en data struktur.
    * Krypterar den enkla data strukturen med hjälp av den symmetriska AES-nyckeln.
    * Krypterar den symmetriska AES-nyckeln med klientens Azure AD Domain Services offentliga nyckel.
1. Azure AD Connect skickar den krypterade AES symmetriska nyckeln, den krypterade data strukturen som innehåller lösen ordets hash-värden och initierings vektorn till Azure AD.
1. Azure AD lagrar den krypterade AES symmetriska nyckeln, den krypterade data strukturen och initierings vektorn för användaren.
1. Azure AD push-överför den krypterade AES symmetriska nyckeln, den krypterade data strukturen och initierings vektorn med hjälp av en intern synkronisering över en krypterad HTTP-session till Azure AD Domain Services.
1. Azure AD Domain Services hämtar den privata nyckeln för klient organisationens instans från Azure Key Vault.
1. För varje krypterad data uppsättning (som representerar en enskild användares lösen ords ändring) utför Azure AD Domain Services följande steg:
    * Använder den privata nyckeln för att dekryptera den symmetriska AES-nyckeln.
    * Använder den symmetriska AES-nyckeln med initierings vektorn för att dekryptera den krypterade data strukturen som innehåller lösen ordets hash-värden.
    * Skriver de Kerberos-lösenords-hashar som den tar emot till Azure AD Domain Services domänkontrollanten. Hasharna sparas i användarobjektet *supplementalCredentials* -attribut som är krypterade till den Azure AD Domain Services domänkontrollantens publika nyckel.
    * Azure AD Domain Services skriver det NTLM-lösenord som det fick till Azure AD Domain Services domänkontrollanten. Hashen sparas i objektet för attributet *unicodePwd* som är krypterat till den Azure AD Domain Services domänkontrollantens publika nyckel.

## <a name="enable-password-hash-synchronization"></a>Aktivera hashsynkronisering för lösenord

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra Federations tekniker) till synkronisering av lösen ords-hash rekommenderar vi starkt att du följer vår detaljerade distributions guide publicerad [här](https://aka.ms/adfstophsdpdownload).

När du installerar Azure AD Connect med alternativet **Express-inställningar** aktive ras hash-synkronisering av lösen ord automatiskt. Mer information finns i [komma igång med Azure AD Connect med hjälp av Express inställningar](how-to-connect-install-express.md).

Om du använder anpassade inställningar när du installerar Azure AD Connect är hash-synkronisering av lösen ord tillgängligt på inloggnings sidan för användare. Mer information finns i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

![Aktivera synkronisering av lösenordshash](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Password-hash-synkronisering och FIPS
Om servern har låsts enligt Federal Information Processing Standard (FIPS) inaktive ras MD5.

**Gör så här för att aktivera MD5 för synkronisering av lösen ords-hash:**

1. Gå till%programfiles%\Azure AD Sync\Bin.
2. Öppna miiserver.exe.config.
3. Gå till noden konfiguration/körning i slutet av filen.
4. Lägg till följande nod: `<enforceFIPSPolicy enabled="false"/>`
5. Spara ändringarna.

För referens är det här kodfragmentet det som ska se ut så här:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Information om säkerhet och FIPS finns i [Azure AD Password hash Sync, kryptering och FIPS-kompatibilitet](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Felsöka hash-synkronisering av lösen ord
Om du har problem med att synkronisera hash-synkronisering, se [Felsöka Lösenordssynkronisering för lösen ord](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Få en stegvis distributions plan för migrering från ADFS till hash-synkronisering av lösen ord](https://aka.ms/authenticationDeploymentPlan)
