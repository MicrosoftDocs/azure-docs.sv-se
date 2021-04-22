---
title: Implementera synkronisering av lösenordshashar Azure AD Connect synkronisering | Microsoft Docs
description: Innehåller information om hur synkronisering av lösenordshashar fungerar och hur du ställer in.
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
ms.openlocfilehash: ee22ba3816e667bc58247fa81142e54587124fd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865305"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implement password hash synchronization with Azure AD Connect sync (Implementera synkronisering av lösenordshash med Azure AD Connect-synkronisering)
Den här artikeln innehåller information som du behöver för att synkronisera dina användarlösenord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory-instans (Azure AD).

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar hash-synkronisering
Active Directory-domäntjänsten lagrar lösenord i form av en hash-värderepresentation av det faktiska användarlösenordet. Ett hash-värde är ett resultat av en enkelvägs matematisk funktion *(hash-algoritmen*). Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. 

Om du vill synkronisera lösenordet Azure AD Connect sync ditt lösenordshashar från lokal Active Directory instansen. Extra säkerhetsbearbetning tillämpas på lösenordshashar innan den synkroniseras till Azure Active Directory autentiseringstjänsten. Lösenord synkroniseras per användare och i kronologisk ordning.

Det faktiska dataflödet för synkroniseringen av lösenordshashar liknar synkroniseringen av användardata. Lösenord synkroniseras dock oftare än standardfönstret för katalogsynkronisering för andra attribut. Synkroniseringen av lösenordshashar körs varannan minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar ett lösenord skriver det över det befintliga molnlösenordet.

Första gången du aktiverar funktionen för synkronisering av lösenordshashar utför den en inledande synkronisering av lösenorden för alla användare i omfånget. Du kan inte uttryckligen definiera en delmängd av användarlösenord som du vill synkronisera. Om det finns flera anslutningsappar är det dock möjligt att inaktivera synkronisering av lösenordshashar för vissa anslutningsappar, men inte för andra med hjälp av cmdleten [Set-ADSyncAADPasswordSyncConfiguration.](../../active-directory-domain-services/tutorial-configure-password-hash-sync.md)

När du ändrar ett lokalt lösenord synkroniseras det uppdaterade lösenordet, oftast på bara några minuter.
Funktionen för synkronisering av lösenordshashar försöker automatiskt igen misslyckade synkroniseringsförsök. Om ett fel inträffar under ett försök att synkronisera ett lösenord loggas ett fel i loggboken.

Synkroniseringen av ett lösenord påverkar inte den användare som för närvarande är inloggad.
Din aktuella molntjänstsession påverkas inte omedelbart av en synkroniserad lösenordsändring som sker när du är inloggad på en molntjänst. Men när molntjänsten kräver att du autentiserar igen måste du ange ditt nya lösenord.

En användare måste ange sina företagsautentiseringsuppgifter en andra gång för att autentisera till Azure AD, oavsett om de är inloggade i företagets nätverk eller inte. Det här mönstret kan dock minimeras om användaren markerar kryssrutan Jag vill vara inloggad (KMSI) vid inloggning. Det här valet anger en sessionscookie som kringgår autentisering i 180 dagar. KMSI-beteendet kan aktiveras eller inaktiveras av Azure AD-administratören. Dessutom kan du minska lösenordsuppfrågarna genom att aktivera sömlös [SSO](how-to-connect-sso.md), som automatiskt loggar in användare när de är på sina företagsenheter som är anslutna till företagsnätverket.

> [!NOTE]
> Lösenordssynkronisering stöds endast för objekttypsanvändaren i Active Directory. Det stöds inte för objekttypen iNetOrgPerson.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Detaljerad beskrivning av hur synkronisering av lösenordshashar fungerar

I följande avsnitt beskrivs på djupet hur synkronisering av lösenordshashar fungerar mellan Active Directory och Azure AD.

![Detaljerat lösenordsflöde](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Varannan minut begär synkroniseringsagenten för lösenordshashar på AD Connect-servern lagrade lösenordshashar (unicodePwd-attributet) från en domänkontrollant.  Den här begäran sker via [standardreplikeringsprotokollet MS-DRSR](/openspecs/windows_protocols/ms-drsr/f977faaa-673e-4f66-b9bf-48c640241d47) som används för att synkronisera data mellan nic:ar. Tjänstkontot måste ha Replikera katalogändringar och Replikera katalogändringar Alla AD-behörigheter (beviljas som standard vid installation) för att kunna hämta lösenordshashar.
2. Innan domänkontrollanten skickas krypterar den MD4-lösenordshashar genom att använda en nyckel som är [en MD5-hash](https://www.rfc-editor.org/rfc/rfc1321.txt) för RPC-sessionsnyckeln och ett salt. Resultatet skickas sedan till synkroniseringsagenten för lösenordshashar via RPC. Domänkontrollanten skickar också saltet till synkroniseringsagenten med hjälp av DC-replikeringsprotokollet, så att agenten kan dekryptera kuvertet.
3. När synkroniseringsagenten för lösenordshashar har det krypterade kuvertet använder den [MD5CryptoServiceProvider](/dotnet/api/system.security.cryptography.md5cryptoserviceprovider) och saltet för att generera en nyckel för att dekryptera mottagna data tillbaka till det ursprungliga MD4-formatet. Synkroniseringsagenten för lösenordshashar har aldrig åtkomst till lösenordet i klartext. Synkroniseringsagenten för lösenordshashar använder MD5 endast för replikeringsprotokollkompatibilitet med domänkontrollanten och används endast lokalt mellan domänkontrollanten och synkroniseringsagenten för lösenordshashar.
4. Synkroniseringsagenten för lösenordshashar expanderar den binära lösenordshasharen på 16 byte till 64 byte genom att först konvertera hashen till en 32 byte hexadecimal sträng och sedan konvertera den här strängen tillbaka till binär med UTF-16-kodning.
5. Synkroniseringsagenten för lösenordshashar lägger till ett salt per användare som består av ett salt på 10 byte i binärfilen med 64 byte för att ytterligare skydda den ursprungliga hashen.
6. Synkroniseringsagenten för lösenordshashar kombinerar sedan MD4-hashen plus saltet per användare och matar in den i [PBKDF2-funktionen.](https://www.ietf.org/rfc/rfc2898.txt) 1 000 iterationer av [den nyckelbaserade hashalgoritmen HMAC-SHA256](/dotnet/api/system.security.cryptography.hmacsha256) används. 
7. Synkroniseringsagenten för lösenordshashar tar den resulterande 32 byte-hashen, sammanfogar både saltet per användare och antalet SHA256-iterationer till den (för användning av Azure AD) och överför sedan strängen från Azure AD Connect till Azure AD via TLS.</br> 
8. När en användare försöker logga in på Azure AD och anger sitt lösenord körs lösenordet via samma MD4+salt+PBKDF2+HMAC-SHA256-process. Om den resulterande hashen matchar den hash som lagras i Azure AD har användaren angett rätt lösenord och autentiserats.

> [!NOTE]
> Den ursprungliga MD4-hashen överförs inte till Azure AD. I stället överförs SHA256-hashen för den ursprungliga MD4-hashen. Om hashen som lagras i Azure AD hämtas kan den därför inte användas i en lokal pass-the-hash-attack.

### <a name="security-considerations"></a>Säkerhetsöverväganden

När du synkroniserar lösenord exponeras inte versionen av ditt lösenord i klartext för synkroniseringsfunktionen för lösenordshashar, för Azure AD eller någon av de associerade tjänsterna.

Användarautentisering sker mot Azure AD i stället för mot organisationens egen Active Directory-instans. SHA256-lösenordsdata som lagras i Azure AD – en hash av den ursprungliga MD4-hashen – är säkrare än vad som lagras i Active Directory. Eftersom denna SHA256-hash inte kan dekrypteras kan den inte återställas till organisationens Active Directory-miljö och presenteras som ett giltigt användarlösenord i en pass-the-hash-attack.

### <a name="password-policy-considerations"></a>Överväganden för lösenordsprincip

Det finns två typer av lösenordsprinciper som påverkas av aktivering av synkronisering av lösenordshashar:

* Princip för lösenordskomplexitet
* Förfalloprincip för lösenord

#### <a name="password-complexity-policy"></a>Princip för lösenordskomplexitet

När synkronisering av lösenordshashar är aktiverat åsidosätter principerna för lösenordskomplexitet i din lokal Active Directory instans komplexitetsprinciper i molnet för synkroniserade användare. Du kan använda alla giltiga lösenord från din lokal Active Directory för att få åtkomst till Azure AD-tjänster.

> [!NOTE]
> Lösenord för användare som skapas direkt i molnet omfattas fortfarande av lösenordsprinciper som definierats i molnet.

#### <a name="password-expiration-policy"></a>Förfalloprincip för lösenord

Om en användare ingår i omfånget för synkronisering av lösenordshashar är lösenordet för molnkontot som standard inställt på *Upphör aldrig.*

Du kan fortsätta att logga in på dina molntjänster med ett synkroniserat lösenord som har upphört att gälla i din lokala miljö. Ditt molnlösenord uppdateras nästa gång du ändrar lösenordet i den lokala miljön.

##### <a name="enforcecloudpasswordpolicyforpasswordsyncedusers"></a>EnforceCloudPasswordPolicyForPasswordSyncedUsers

Om det finns synkroniserade användare som bara interagerar med Azure AD-integrerade tjänster och även måste följa en förfalloprincip för lösenord, kan du tvinga dem att följa din förfalloprincip för Azure AD-lösenord genom att aktivera *funktionen EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

När *EnforceCloudPasswordPolicyForPasswordSyncedUsers* är inaktiverat (vilket är standardinställningen) anger Azure AD Connect attributet PasswordPolicies för synkroniserade användare till "DisablePasswordExpiration". Detta görs varje gång en användares lösenord synkroniseras och instruerar Azure AD att ignorera användarens förfalloprincip för lösenord i molnet. Du kan kontrollera värdet för attributet med hjälp av Azure AD PowerShell-modulen med följande kommando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`

Om du vill aktivera funktionen EnforceCloudPasswordPolicyForPasswordSyncedUsers kör du följande kommando med hjälp av MSOnline PowerShell-modulen enligt nedan. Du måste skriva ja för parametern Aktivera enligt nedan:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

När den är aktiverad går Azure AD inte till varje synkroniserad användare för att ta `DisablePasswordExpiration` bort värdet från attributet PasswordPolicies. I stället tas värdet bort från PasswordPolicies vid nästa synkronisering av lösenordshashar för varje användare vid nästa lösenordsändring i `DisablePasswordExpiration` lokala AD.

Vi rekommenderar att du aktiverar EnforceCloudPasswordPolicyForPasswordSyncedUsers innan du aktiverar synkronisering av lösenordshashar, så att den första synkroniseringen av lösenordshashar inte lägger till värdet i `DisablePasswordExpiration` attributet PasswordPolicies för användarna.

Standardprincipen för Azure AD-lösenord kräver att användarna ändrar sina lösenord var 90:e dag. Om principen i AD också är 90 dagar ska de två principerna matcha. Men om AD-principen inte är 90 dagar kan du uppdatera Azure AD-lösenordsprincipen så att den matchar med hjälp Set-MsolPasswordPolicy PowerShell-kommandot.

Azure AD stöder en separat förfalloprincip för lösenord per registrerad domän.

Varning: Om det finns synkroniserade konton som behöver ha lösenord som inte upphör att gälla i Azure AD måste du uttryckligen lägga till värdet i `DisablePasswordExpiration` attributet PasswordPolicies för användarobjektet i Azure AD.  Du kan göra detta genom att köra följande kommando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Det Set-MsolPasswordPolicy PowerShell-kommandot fungerar inte på federerade domäner. 

#### <a name="synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Synkronisera tillfälliga lösenord och tvinga fram lösenordsändring vid nästa inloggning

Det är vanligt att tvinga en användare att ändra sitt lösenord under den första inloggningen, särskilt när en återställning av administratörslösenord sker.  Det kallas ofta att ange ett "tillfälligt" lösenord och slutförs genom att markera flaggan "Användaren måste ändra lösenord vid nästa inloggning" för ett användarobjekt i Active Directory (AD).
  
Funktionen för tillfälliga lösenord hjälper till att säkerställa att överföringen av ägarskapet för autentiseringssuppgifter slutförs vid första användning, för att minimera hur lång tid som mer än en person har kunskap om autentiseringspersonen.

För att stödja tillfälliga lösenord i Azure AD för synkroniserade användare kan du aktivera funktionen *ForcePasswordChangeOnLogOn* genom att köra följande kommando på din Azure AD Connect server:

`Set-ADSyncAADCompanyFeature -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Att tvinga en användare att ändra sitt lösenord vid nästa inloggning kräver en lösenordsändring på samma gång.  Azure AD Connect hämtar inte flaggan för framtrng av lösenordsändring på egen hand. Det är ett tillägg till den identifierade lösenordsändringen som inträffar under synkroniseringen av lösenordshashar.

> [!CAUTION]
> Du bör bara använda den här funktionen när SSPR och tillbakaskrivning av lösenord har aktiverats för klienten.  Detta innebär att om en användare ändrar sitt lösenord via SSPR, synkroniseras det till Active Directory.

#### <a name="account-expiration"></a>Kontots förfallodatum

Om din organisation använder attributet accountExpires som en del av kontohanteringen synkroniseras inte det här attributet till Azure AD. Därför är ett Utgånget Active Directory-konto i en miljö som konfigurerats för synkronisering av lösenordshashar fortfarande aktivt i Azure AD. Om kontot har upphört att gälla bör en arbetsflödesåtgärd utlösa ett PowerShell-skript som inaktiverar användarens Azure AD-konto (använd cmdleten [Set-AzureADUser).](/powershell/module/azuread/set-azureaduser) Omvänt bör Azure AD-instansen aktiveras när kontot är aktiverat.

### <a name="overwrite-synchronized-passwords"></a>Skriva över synkroniserade lösenord

En administratör kan återställa lösenordet manuellt med hjälp av Windows PowerShell.

I det här fallet åsidosätter det nya lösenordet ditt synkroniserade lösenord och alla lösenordsprinciper som definierats i molnet tillämpas på det nya lösenordet.

Om du ändrar ditt lokala lösenord igen synkroniseras det nya lösenordet till molnet och åsidosätter det manuellt uppdaterade lösenordet.

Synkroniseringen av ett lösenord påverkar inte den Azure-användare som är inloggad. Din aktuella molntjänstsession påverkas inte omedelbart av en synkroniserad lösenordsändring som inträffar när du är inloggad på en molntjänst. KMSI utökar varaktigheten för den här skillnaden. När molntjänsten kräver att du autentiserar igen måste du ange ditt nya lösenord.

### <a name="additional-advantages"></a>Ytterligare fördelar

- I allmänhet är synkronisering av lösenordshashar enklare att implementera än en federationstjänst. Det kräver inte några ytterligare servrar och eliminerar beroendet av en federationstjänst med hög tillgång för att autentisera användare.
- Synkronisering av lösenordshashar kan också aktiveras utöver federation. Den kan användas som reserv om federationstjänsten upplever ett avbrott.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Synkroniseringsprocess för lösenordshashar för Azure AD Domain Services

Om du använder Azure AD Domain Services för att tillhandahålla äldre autentisering för program och tjänster som behöver använda Kerberos, LDAP eller NTLM är vissa ytterligare processer en del av synkroniseringsflödet för lösenordshashar. Azure AD Connect använder följande ytterligare process för att synkronisera lösenordshashar till Azure AD för användning i Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en Azure AD DS domän för att synkronisera objekt tillbaka till Azure AD.
>
> Azure AD Connect synkroniserar endast äldre lösenordshashar när du aktiverar Azure AD DS för din Azure AD-klientorganisation. Följande steg används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller enkla LDAP-bindningar rekommenderar vi att du inaktiverar NTLM-synkronisering av lösenordshashar för Azure AD DS. Mer information finns i Inaktivera [svaga chiffersviter och HASH-synkronisering av NTLM-autentiseringsuppgifter.](../../active-directory-domain-services/secure-your-domain.md)

1. Azure AD Connect hämtar den offentliga nyckeln för klientorganisationens instans av Azure AD Domain Services.
1. När en användare ändrar sitt lösenord lagrar den lokala domänkontrollanten resultatet av lösenordsändringen (hash-koderna) i två attribut:
    * *unicodePwd för* NTLM-lösenordshashar.
    * *supplementalCredentials för* Kerberos-lösenordshashar.
1. Azure AD Connect identifierar lösenordsändringar via katalogreplikeringskanalen (attributändringar behöver replikeras till andra domänkontrollanter).
1. För varje användare vars lösenord har ändrats Azure AD Connect följande steg:
    * Genererar en slumpmässig 256-bitars symmetrisk AES-nyckel.
    * Genererar en slumpmässig initieringsvektor som behövs för den första krypteringsomgången.
    * Extraherar Kerberos-lösenordshasher *från attributen supplementalCredentials.*
    * Kontrollerar Azure AD Domain Services *syncNtlmPasswords-inställningen.*
        * Om den här inställningen är inaktiverad genererar en slumpmässig NTLM-hash med hög entropi (skiljer sig från användarens lösenord). Denna hash kombineras sedan med de exakta Kerberos-lösenordshashar från *attributet supplementalCrendetials* till en datastruktur.
        * Om aktiverad kombineras värdet för *unicodePwd-attributet* med extraherade Kerberos-lösenordshasher från *attributet supplementalCredentials* till en datastruktur.
    * Krypterar den enskilda datastrukturen med hjälp av den symmetriska AES-nyckeln.
    * Krypterar den symmetriska AES-nyckeln med hjälp av klientens Azure AD Domain Services offentliga nyckeln.
1. Azure AD Connect överför den krypterade symmetriska AES-nyckeln, den krypterade datastrukturen som innehåller lösenordshashar och initieringsvektorn till Azure AD.
1. Azure AD lagrar den krypterade symmetriska AES-nyckeln, den krypterade datastrukturen och initieringsvektorn för användaren.
1. Azure AD skickar den krypterade symmetriska AES-nyckeln, den krypterade datastrukturen och initieringsvektorn med hjälp av en intern synkroniseringsmekanism över en krypterad HTTP-session till Azure AD Domain Services.
1. Azure AD Domain Services hämtar den privata nyckeln för klientorganisationens instans från Azure Key Vault.
1. För varje krypterad datauppsättning (som representerar en enskild användares lösenordsändring) Azure AD Domain Services utför följande steg:
    * Använder sin privata nyckel för att dekryptera den symmetriska AES-nyckeln.
    * Använder den symmetriska AES-nyckeln med initieringsvektorn för att dekryptera den krypterade datastruktur som innehåller lösenordshashar.
    * Skriver Kerberos-lösenordshashar som den tar emot Azure AD Domain Services domänkontrollanten. Hashvärdena sparas i *användarobjektets supplementalCredentials-attribut* som krypteras till Azure AD Domain Services domänkontrollantens offentliga nyckel.
    * Azure AD Domain Services skriver den NTLM-lösenordshashar som togs emot Azure AD Domain Services domänkontrollanten. Hashen sparas i användarobjektets *unicodePwd-attribut* som krypteras till Azure AD Domain Services domänkontrollantens offentliga nyckel.

## <a name="enable-password-hash-synchronization"></a>Aktivera hashsynkronisering för lösenord

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra federationstekniker) till synkronisering av lösenordshashar rekommenderar vi starkt att du följer vår detaljerade distributionsguide som publiceras [här.](https://aka.ms/adfstophsdpdownload)

När du installerar Azure AD Connect med hjälp av **alternativet Expressinställningar** aktiveras synkronisering av lösenordshashar automatiskt. Mer information finns i Komma [igång med att Azure AD Connect med standardinställningar.](how-to-connect-install-express.md)

Om du använder anpassade inställningar när du Azure AD Connect är synkronisering av lösenordshashar tillgänglig på användarens inloggningssida. Mer information finns i [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

![Aktivera synkronisering av lösenordshash](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synkronisering av lösenordshashar och FIPS
Om servern har låsts enligt FIPS (Federal Information Processing Standard) inaktiveras MD5.

**Utför följande steg för att aktivera MD5 för synkronisering av lösenordshashar:**

1. Gå till %programfiles%\Microsoft Azure AD Sync\Bin.
2. Öppna miiserver.exe.config.
3. Gå till noden configuration/runtime i slutet av filen.
4. Lägg till följande nod: `<enforceFIPSPolicy enabled="false"/>`
5. Spara ändringarna.

Det här kodfragmentet bör se ut så här:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Information om säkerhet och FIPS finns i [Azure AD-synkronisering av lösenordshashar, kryptering och FIPS-kompatibilitet.](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshoot-password-hash-synchronization"></a>Felsöka synkronisering av lösenordshashar
Om du har problem med synkronisering av lösenordshashar kan du gå till [Felsöka synkronisering av lösenordshashar.](tshoot-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Hämta en stegvis distributionsplan för att migrera från ADFS till synkronisering av lösenordshashar](https://aka.ms/authenticationDeploymentPlan)
