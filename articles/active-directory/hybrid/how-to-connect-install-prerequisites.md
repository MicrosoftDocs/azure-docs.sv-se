---
title: 'Azure AD Connect: krav och maskin vara | Microsoft Docs'
description: I den här artikeln beskrivs förutsättningarna och maskin varu kraven för Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e758933b80efbf36dc263b7bd7d2d3c45a59a9f8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426798"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Förhandskrav för Azure AD Connect
I den här artikeln beskrivs kraven och maskin varu kraven för Azure Active Directory (Azure AD) Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect finns det några saker du behöver.

### <a name="azure-ad"></a>Azure AD
* Du behöver en Azure AD-klient. Du får ett med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). Du kan använda någon av följande portaler för att hantera Azure AD Connect:
  * [Azure Portal](https://portal.azure.com).
  * [Office-portalen](https://portal.office.com).
* [Lägg till och verifiera den domän](../fundamentals/add-custom-domain.md) som du planerar att använda i Azure AD. Om du till exempel planerar att använda contoso.com för dina användare, se till att den här domänen har verifierats och att du inte använder contoso.onmicrosoft.com standard domän.
* En Azure AD-klient tillåter som standard 50 000-objekt. När du verifierar din domän ökar gränsen till 300 000 objekt. Om du behöver ännu fler objekt i Azure AD kan du öppna ett support ärende om du vill öka gränsen ytterligare. Om du behöver fler än 500 000 objekt behöver du en licens, till exempel Microsoft 365, Azure AD Premium eller Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Förbered dina lokala data
* Använd [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) för att identifiera fel som dubbletter och formateringsfel i din katalog innan du synkroniserar till Azure AD och Microsoft 365.
* Granska [valfria Sync-funktioner som du kan aktivera i Azure AD](how-to-connect-syncservice-features.md)och utvärdera vilka funktioner du bör aktivera.

### <a name="on-premises-active-directory"></a>Lokalt Active Directory
* Den Active Directory schema versionen och skogens funktions nivå måste vara Windows Server 2003 eller senare. Domän kontrol Lanterna kan köra alla versioner så länge som schema versionen och kraven på skogs nivå är uppfyllda.
* Om du planerar att använda tillbakaskrivning av funktions *lösen ord* måste domän kontrol Lanterna vara på Windows Server 2012 eller senare.
* Domänkontrollanten som används av Azure AD måste vara skrivbar. Det *finns inte stöd* för att använda en skrivskyddad DOMÄNKONTROLLANT (RODC) och Azure AD Connect inte utföra några Skriv omdirigeringar.
* Att använda lokala skogar eller domäner med hjälp av "prickad" (namnet innehåller en punkt ".") NetBIOS-namn *stöds inte*.
* Vi rekommenderar att du [aktiverar Active Directory pappers korgen](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>PowerShell-körningsprincip
Azure Active Directory Connect kör signerade PowerShell-skript som en del av installationen. Kontrol lera att körnings principen för PowerShell tillåter körning av skript.

Den rekommenderade körnings principen under installationen är "RemoteSigned".

Mer information om hur du anger körnings principen för PowerShell finns i [set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Azure AD Connect Server
Azure AD Connect servern innehåller kritiska identitets data. Det är viktigt att administrativ åtkomst till den här servern är korrekt skyddad. Följ rikt linjerna för att [skydda privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Azure AD Connect-servern måste behandlas som en nivå 0-komponent som dokumenteras i [Active Directory administrativ nivå modell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 

Mer information om hur du skyddar din Active Directory-miljö finns i [metod tips för att skydda Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Installationskrav

- Azure AD Connect måste vara installerat på en domänansluten Windows Server 2012 eller senare. 
- Azure AD Connect kan inte installeras på Small Business Server eller Windows Server Essentials före 2019 (Windows Server Essentials 2019 stöds). Servern måste använda Windows Server standard eller bättre. 
- Den Azure AD Connect servern måste ha ett fullständigt GUI installerat. Det finns inte stöd för att installera Azure AD Connect på Windows Server Core. 
- Den Azure AD Connect servern får inte ha PowerShell-avskrifts grupprincip aktiverat om du använder Azure AD Connect guiden för att hantera Active Directory Federation Services (AD FS) (AD FS) konfiguration. Du kan aktivera PowerShell-avskriftning om du använder guiden Azure AD Connect för att hantera konfigurationen av synkronisering. 
- Om AD FS distribueras: 
    - Servrarna där AD FS eller Webbprogramproxy installeras måste vara Windows Server 2012 R2 eller senare. Windows Remote Management måste vara aktiverat på dessa servrar för fjärrinstallation. 
    - Du måste konfigurera TLS/SSL-certifikat. Mer information finns i [Hantera SSL/TLS-protokoll och chiffersviter för AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) och [Hantera SSL-certifikat i AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Du måste konfigurera namn matchning. 
- Om dina globala administratörer har MFA aktiverat måste URL: en https://secure.aadcdn.microsoftonline-p.com  vara i listan över betrodda platser. Du uppmanas att lägga till den här platsen i listan över betrodda platser när du uppmanas att ange en MFA-utmaning och inte har lagts till tidigare. Du kan använda Internet Explorer för att lägga till den på dina betrodda platser.
- Om du planerar att använda Azure AD Connect Health för synkronisering bör du se till att kraven för Azure AD Connect Health också uppfylls. Mer information finns i [Azure AD Connect Health Agent installation](how-to-connect-health-agent-install.md).

#### <a name="harden-your-azure-ad-connect-server"></a>Skärp din Azure AD Connect-Server 
Vi rekommenderar att du skärper Azure AD Connect-servern för att minska säkerhets attack ytan för den här viktiga komponenten i din IT-miljö. Genom att följa de här rekommendationerna kan du undvika vissa säkerhets risker för din organisation.

- Behandla Azure AD Connect samma som en domänkontrollant och andra nivå 0-resurser. Mer information finns i [Active Directory administrativ nivå modell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Begränsa administrativ åtkomst till Azure AD Connect-servern till endast domän administratörer eller andra tätt kontrollerade säkerhets grupper.
- Skapa ett [dedikerat konto för all personal med privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access). Administratörer bör inte surfa på webben, kontrol lera sin e-post och utföra dagliga produktivitets uppgifter med mycket privilegierade konton.
- Följ rikt linjerna för att [skydda privilegie rad åtkomst](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Neka användningen av NTLM-autentisering med AADConnect-servern. Här följer några sätt att göra detta: [Begränsa NTLM på AADConnect-servern](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) och [Begränsa NTLM på en domän](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Se till att varje dator har ett unikt lokalt administratörs lösen ord. Mer information finns i [lokal administratör lösen ords lösning (upphörde)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) kan konfigurera unika slumpmässiga lösen ord på varje arbets Station och Server lagra dem i Active Directory som skyddas av en ACL. Endast berättigade behöriga användare kan läsa eller begära återställning av lösen ord för det lokala administratörs kontot. Du kan få en uppgång för användning på arbets stationer och servrar från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=46899). Ytterligare vägledning för att använda en miljö med fördröjning och privilegierade åtkomst arbets stationer (Paw) finns i [operativa standarder som baseras på principen om ren källa](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implementera dedikerade [arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) för all personal med privilegie rad åtkomst till din organisations informations system. 
- Följ dessa [rikt linjer](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) för att minska angrepps ytan i Active Directorys miljön.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Som standard installeras en SQL Server 2012 Express-LocalDB (en låg version av SQL Server Express). SQL Server Express har en storleks gräns på 10 GB som gör att du kan hantera cirka 100 000 objekt. Om du behöver hantera en större mängd katalog objekt pekar du på installations guiden till en annan installation av SQL Server. SQL Server-installationens typ kan påverka [prestandan för Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Om du använder en annan installation av SQL Server gäller dessa krav:
  * Azure AD Connect stöder alla versioner av SQL Server från 2012 (med de senaste service pack) till SQL Server 2019. Azure SQL Database *stöds inte* som en databas.
  * Du måste använda en Skift läges okänslig SQL-sortering. Dessa sorteringar identifieras med ett \_ CI_ i sitt namn. Användning av en Skift läges känslig sortering som identifieras av \_ CS_ i deras namn *stöds inte*.
  * Du kan bara ha en Sync-motor per SQL-instans. Det *finns inte stöd* för att dela en SQL-instans med FIM/MIM Sync, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* Du måste ha ett globalt administratörs konto för Azure AD för den Azure AD-klient som du vill integrera med. Kontot måste vara ett *skol-eller organisations konto* och får inte vara ett *Microsoft-konto*.
* Om du använder [Express inställningar](reference-connect-accounts-permissions.md#express-settings-installation) eller uppgraderar från DirSync måste du ha ett företags administratörs konto för din lokala Active Directory.
* Om du använder installations Sök vägen för anpassade inställningar har du fler alternativ. Mer information finns i [anpassade installations inställningar](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Anslutning
* Den Azure AD Connect servern behöver DNS-matchning för både intranätet och Internet. DNS-servern måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkter.
* Azure AD Connect kräver nätverks anslutning till alla konfigurerade domäner
* Om du har brand väggar i intranätet och du behöver öppna portar mellan Azure AD Connect-servrar och domän kontrol Lanterna, se [Azure AD Connect portar](reference-connect-ports.md) för mer information.
* Om din proxy eller brand vägg begränsar vilka URL: er som kan nås, måste URL: erna som dokumenteras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) öppnas. Se även [Safelist Azure Portal URL: er på brand väggen eller proxyservern](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Om du använder Microsoft-molnet i Tyskland eller Microsoft Azure Government molnet kan du läsa om [Azure AD Connect Sync service-instanser](reference-connect-instances.md) för URL: er.
* Azure AD Connect (version 1.1.614.0 och efter) som standard använder TLS 1,2 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om TLS 1,2 inte är tillgängligt i det underliggande operativ systemet går Azure AD Connect stegvis tillbaka till äldre protokoll (TLS 1,1 och TLS 1,0).
* Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om du vill ändra till TLS 1,2 följer du stegen i [Aktivera tls 1,2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för att ansluta till Internet måste följande inställning i **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** -filen läggas till för installations guiden och Azure AD Connect synkronisering för att kunna ansluta till Internet och Azure AD. Texten måste anges längst ned i filen. I den här koden representerar *&lt; PROXYADDRESS &gt;* den faktiska proxy-IP-adressen eller värd namnet.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Om proxyservern kräver autentisering måste [tjänst kontot](reference-connect-accounts-permissions.md#adsync-service-account) finnas i domänen. Använd installations Sök vägen för anpassade inställningar för att ange ett [anpassat tjänst konto](how-to-connect-install-custom.md#install-required-components). Du behöver också en annan ändring i machine.config. Med den här ändringen i machine.config svarar installations guiden och synkroniseringen av autentiseringsbegäranden från proxyservern. I alla installations guide sidor, förutom sidan **Konfigurera** , används den inloggade användarens autentiseringsuppgifter. På sidan **Konfigurera** i slutet av installations guiden växlas kontexten till det [tjänst konto](reference-connect-accounts-permissions.md#adsync-service-account) som du skapade. Avsnittet machine.config bör se ut så här:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Om proxykonfigurationen görs i en befintlig installation måste **Microsoft Azure AD Sync-tjänsten** startas om en gång för att Azure AD Connect läsa proxykonfigurationen och uppdatera beteendet. 
* När Azure AD Connect skickar en webbegäran till Azure AD som en del av Active Directory-synkroniseringen kan det ta upp till 5 minuter för Azure AD att svara. Det är vanligt att proxyservrarna har timeout-konfiguration för inaktivitet. Se till att konfigurationen är minst 6 minuter eller mer.

Mer information finns i MSDN om [default proxy-elementet](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Mer information om problem med anslutningen finns i [Felsöka anslutnings problem](tshoot-connect-connectivity.md).

### <a name="other"></a>Övrigt
Valfritt: Använd ett test användar konto för att verifiera synkroniseringen.

## <a name="component-prerequisites"></a>Komponent krav
### <a name="powershell-and-net-framework"></a>PowerShell och .NET Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller en senare version som är installerad på servern. Vidta följande åtgärder, beroende på din Windows Server-version:

* Windows Server 2012 R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET Framework 4.5.1 och senare versioner erbjuds genom Windows Update. Kontrol lera att du har installerat de senaste uppdateringarna för Windows Server på kontroll panelen.
* Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell är tillgänglig i Windows Management Framework 4,0, som finns på [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 och senare versioner finns på [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1,2 för Azure AD Connect
Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikationen mellan Sync-databasmotorn och Azure AD. Du kan konfigurera .NET-program att använda TLS 1,2 som standard på-servern. Mer information om TLS 1,2 finns i [Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Kontrol lera att du har installerat .NET 4.5.1 Hotfix för ditt operativ system. Mer information finns i [Microsoft Security Advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358). Du kanske har den här snabb korrigeringen eller också har en senare version installerad på servern redan.

1. För alla operativ system anger du register nyckeln och startar om servern.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Om du även vill aktivera TLS 1,2 mellan Synkroniseringsmotorn och en fjärran sluten SQL Server kontrollerar du att du har de versioner som krävs installerade för [TLS 1,2-stöd för Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>DCOM-krav på synkroniseringstjänsten
Under installationen av synkroniseringstjänsten Azure AD Connect söker efter följande register nyckel:

- HKEY_LOCAL_MACHINE: Software\Microsoft\Ole

Under den här register nyckeln kontrollerar Azure AD Connect om följande värden finns och är skadade: 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Krav för Federations installation och konfiguration
### <a name="windows-remote-management"></a>Windows Remote Management
När du använder Azure AD Connect för att distribuera AD FS eller Webbprogramproxy (WAP) kontrollerar du följande krav:

* Om mål servern är domänansluten kontrollerar du att Windows Remote Managed är aktiverat.
  * Använd kommandot i ett upphöjd PowerShell-kommando fönster `Enable-PSRemoting –force` .
* Om mål servern är en icke-domänansluten WAP-dator finns det ett par ytterligare krav:
  * På mål datorn (WAP-dator):
    * Se till att tjänsten Windows Remote Management/WS-Management (WinRM) körs via snapin-modulen tjänster.
    * Använd kommandot i ett upphöjd PowerShell-kommando fönster `Enable-PSRemoting –force` .
  * På den dator där guiden körs (om mål datorn är icke-domänansluten eller är en domän som inte är betrodd):
    * Använd kommandot i ett upphöjd PowerShell-kommando fönster `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * I Server hanteraren:
      * Lägg till en DMZ WAP-värd i en resurspool. I Server hanteraren väljer du **Hantera**  >  **Lägg till servrar** och använder sedan fliken **DNS** .
      * På fliken **Serverhanteraren alla servrar** högerklickar du på WAP-servern och väljer **Hantera som**. Ange lokala autentiseringsuppgifter (inte domän) för WAP-datorn.
      * För att verifiera Fjärran sluten PowerShell-anslutning, på fliken **Serverhanteraren alla servrar** , högerklickar du på WAP-servern och väljer **Windows PowerShell**. En fjärrpowershell-session bör vara öppen för att säkerställa att fjärranslutna PowerShell-sessioner kan upprättas.

### <a name="tlsssl-certificate-requirements"></a>Krav för TLS/SSL-certifikat
* Vi rekommenderar att du använder samma TLS/SSL-certifikat på alla noder i din AD FS-server grupp och alla Webbprogramproxy-servrar.
* Certifikatet måste vara ett X509-certifikat.
* Du kan använda ett självsignerat certifikat på Federations servrar i en test labb miljö. För en produktions miljö rekommenderar vi att du hämtar certifikatet från en offentlig certifikat utfärdare.
  * Om du använder ett certifikat som inte är offentligt betrott ser du till att certifikatet som är installerat på varje Webbprogramproxy är betrott på både den lokala servern och på alla Federations servrar.
* Identiteten för certifikatet måste matcha Federations tjänstens namn (till exempel sts.contoso.com).
  * Identiteten är antingen ett alternativt namn för certifikat mottagare (SAN) av typen dNSName eller, om det inte finns några SAN-poster, anges ämnes namnet som ett eget namn.
  * Det finns flera SAN-poster i certifikatet som tillhandahållit en av dem matchar Federations tjänstens namn.
  * Om du planerar att använda Workplace Join krävs ytterligare ett SAN-nätverk med värdet **enterpriseregistration.** följt av organisationens suffix User Principal Name (UPN), till exempel enterpriseregistration.contoso.com.
* Certifikat som baseras på CryptoAPI Next-Generation (CNG)-nycklar och nyckel lagrings leverantörer (KSP: er) stöds inte. Därför måste du använda ett certifikat som baseras på en kryptografiprovider (CSP) och inte en KSP.
* Jokertecken stöds.

### <a name="name-resolution-for-federation-servers"></a>Namn matchning för Federations servrar
* Konfigurera DNS-poster för AD FS namn (till exempel sts.contoso.com) för både intranätet (din interna DNS-Server) och extra nätet (offentlig DNS via din domän registrator). För DNS-posten för intranätet kontrollerar du att du använder en post och inte CNAME-poster. Användning av en post krävs för att Windows-autentisering ska fungera korrekt från en domänansluten dator.
* Om du distribuerar fler än en AD FS server eller Webbprogramproxy, se till att du har konfigurerat belastningsutjämnaren och att DNS-posterna för AD FS namn (till exempel sts.contoso.com) pekar på belastningsutjämnaren.
* För att Windows-integrerad autentisering ska fungera för webb läsar program med Internet Explorer i intranätet kontrollerar du att AD FS namn (till exempel sts.contoso.com) läggs till i zonen Intranät i Internet Explorer. Detta krav kan styras via grupprincip och distribueras till alla domänanslutna datorer.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect stöd komponenter
Azure AD Connect installerar följande komponenter på den server där Azure AD Connect är installerat. Den här listan är för en grundläggande Express installation. Om du väljer att använda en annan SQL Server på sidan **Installera Synchronization Services** installeras inte SQL Express LocalDB lokalt.

* Azure AD Connect Health
* Microsoft SQL Server 2012 kommando rads verktyg
* Microsoft SQL Server 2012 Express-LocalDB
* Microsoft SQL Server 2012 Native Client
* Omdistributions paket för Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Maskin varu krav för Azure AD Connect
I följande tabell visas minimi kraven för Azure AD Connect Sync-datorn.

| Antal objekt i Active Directory | Processor | Minne | Hård disk storlek |
| --- | --- | --- | --- |
| Färre än 10 000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50000 – 100000 |1,6 GHz |16 GB |100 GB |
| För 100 000 eller fler objekt krävs en fullständig version av SQL Server | | | |
| 100000 – 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600 000 |1,6 GHz |32 GB |450 GB |
| Mer än 600 000 |1,6 GHz |32 GB |500 GB |

Minimi kraven för datorer som kör AD FS-eller Webbprogramproxy-servrar är:

* PROCESSOR: Dual Core 1,6 GHz eller snabbare
* Minne: 2 GB eller högre
* Virtuell Azure-dator: a2-konfiguration eller högre

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
