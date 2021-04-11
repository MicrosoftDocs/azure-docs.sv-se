---
title: Distribuera lokalt Azure AD-lösenord för lösen ords skydd
description: Lär dig hur du planerar och distribuerar Azure AD Password Protection i en lokal Active Directory Domain Servicess miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5134c0eeaf3ebd1467b35cf825aa64bd1b445bd5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010587"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planera och distribuera lokala Azure Active Directory lösen ords skydd

Användare skapar ofta lösen ord som använder vanliga lokala ord som skolan, idrotts lag eller berömda person. Dessa lösen ord är lätta att gissa sig till och är svaga mot ordbaserade attacker. För att kunna använda starka lösen ord i din organisation har Azure Active Directory (Azure AD) lösen ords skydd en lista med globala och anpassade lösen ord. En begäran om lösen ords ändring Miss lyckas om det finns en matchning i dessa förbjudna lösen ords listor.

Om du vill skydda din lokala Active Directory Domain Services (AD DS)-miljö kan du installera och konfigurera Azure AD Password Protection så att den fungerar med din lokal DC. Den här artikeln visar hur du installerar och registrerar Azure AD Password Protection proxy service och Azure AD Password Protection DC-agenten i din lokala miljö.

Mer information om hur du arbetar med Azure AD Password Protection i en lokal miljö finns i [så här tillämpar du Azure AD Password Protection för Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Distributions strategi

Följande diagram visar hur de grundläggande komponenterna i Azure AD Password Protection fungerar tillsammans i en lokal Active Directory miljö:

![Så här fungerar Azure AD Password Protection-komponenter tillsammans](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Det är en bra idé att granska hur program varan fungerar innan du distribuerar den. Mer information finns i [Konceptuell översikt över lösen ords skydd i Azure AD](concept-password-ban-bad-on-premises.md).

Vi rekommenderar att du startar distributioner i *gransknings* läge. Gransknings läget är standard inställningen för första, där lösen ord kan fortsätta att anges. Lösen ord som skulle blockeras registreras i händelse loggen. När du har distribuerat proxyservrarna och DC-agenterna i gransknings läge, övervakar du den effekt som lösen ords principen kommer att ha på användare när principen tillämpas.

I gransknings fasen ser många organisationer till att följande situationer gäller:

* De behöver förbättra befintliga operativa processer för att använda säkrare lösen ord.
* Användarna använder ofta oskyddade lösen ord.
* De måste informera användarna om den kommande ändringen av säkerheten, eventuellt påverka dem och hur man väljer säkrare lösen ord.

Det är också möjligt att använda starkare lösen ords verifiering för att påverka din befintliga Active Directory distribution av domänkontrollantens distributions automatisering. Vi rekommenderar att minst en befordran av DOMÄNKONTROLLANTen och en degradering av en DOMÄNKONTROLLANT sker under utvärderings perioden för gransknings perioden för att hjälpa till att få ut sådana problem. Mer information finns i följande artiklar:

* [Ntdsutil.exe kan inte ange ett svagt lösen ord för reparations läge för katalog tjänster](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Befordran av replik på domänkontrollant Miss lyckas på grund av ett svagt lösen ord för reparations läge för katalog tjänster](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Degradering av domänkontrollanten Miss lyckas på grund av ett svagt lokalt administratörs lösen ord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

När funktionen har körts i gransknings läge under en rimlig period, kan du växla konfigurationen från *granskning* *till att kräva säkrare* lösen ord. Extra övervakning under den här tiden är en bra idé.

Det är viktigt att Observera att Azure AD Password Protection bara kan verifiera lösen ord under ändring av lösen ord eller ange åtgärder. Lösen ord som har godkänts och lagrats i Active Directory före distributionen av Azure AD-lösenordet verifieras aldrig och fortsätter att fungera i befintligt skick. Med tiden kommer alla användare och konton att börja använda Azure AD Password Protection – verifierade lösen ord eftersom deras befintliga lösen ord upphör att gälla normalt. Konton som kon figurer ATS med "lösen ordet upphör aldrig att gälla" är undantagna från detta.

### <a name="multiple-forest-considerations"></a>Överväganden för flera skogar

Det finns inga ytterligare krav för att distribuera lösen ords skydd i Azure AD över flera skogar.

Varje skog konfigureras oberoende av varandra, enligt beskrivningen i följande avsnitt för att [distribuera lokal Azure AD Password Protection](#download-required-software). Varje Azure AD-proxy för lösen ords skydd har endast stöd för domänkontrollanter från den skog som den är ansluten till.

Azure AD-programvaran för lösen ords skydd i alla skogar är inte medvetna om lösen ords skydds program som distribueras i andra skogar, oavsett Active Directory Trust-konfigurationer.

### <a name="read-only-domain-controller-considerations"></a>Överväganden för skrivskyddade domänkontrollanter

Lösen ords ändring eller uppsättning av händelser bearbetas inte och sparas inte på skrivskyddade domänkontrollanter (RODC). De vidarebefordras istället till skrivbara domänkontrollanter. Du behöver inte installera Azure AD Password Protection DC Agent-programvara på skrivskyddade domänkontrollanter.

Dessutom har det inte stöd för att köra proxy-tjänsten för lösen ords skydd för Azure AD på en skrivskyddad domänkontrollant.

### <a name="high-availability-considerations"></a>Överväganden för hög tillgänglighet

Den huvudsakliga angelägenheten för lösen ords skydd är tillgänglighet för Azure AD-proxyservrar för lösen ords skydd när domänkontrollanten i en skog försöker ladda ned nya principer eller andra data från Azure. Varje DC-agent i Azure AD Password Protection använder en enkel algoritm för resursallokering med fast Robin när du bestämmer vilken proxyserver som ska anropas. Agenten hoppar över proxyservrar som inte svarar.

För de flesta fullständigt anslutna Active Directory-distributioner som har felfri replikering av både katalog-och SYSVOL-mappar, räcker det med två Azure AD-proxyservrar för lösen ords skydd för att säkerställa tillgängligheten. Den här konfigurationen resulterar i en tids nedladdning av nya principer och andra data. Du kan distribuera ytterligare Azure AD-proxyservrar om du vill.

Designen av Azure AD Password Protection DC Agent-programvaran minimerar de vanliga problem som är associerade med hög tillgänglighet. DC-agenten i Azure AD Password Protection hanterar en lokal cache med den senast hämtade lösen ords principen. Även om alla registrerade proxyservrar blir otillgängliga fortsätter Azure AD Password Protection DC-agenterna att tillämpa sin cachelagrade lösen ords princip.

En rimlig uppdaterings frekvens för lösen ords principer i en stor distribution är vanligt vis dagar, inte timmar eller mindre. Det innebär att proxyservrarna inte kraftigt påverkar lösen ords skyddet i Azure AD.

## <a name="deployment-requirements"></a>Distributionskrav

Information om licensiering finns i [licens krav för Azure AD Password Protection](concept-password-ban-bad.md#license-requirements).

Följande grundläggande krav gäller:

* Alla datorer, inklusive domänkontrollanter, som har Azure AD Password Protection-komponenter installerade måste ha Universal C-körningsmiljön installerat.
    * Du kan få körnings miljön genom att se till att du har alla uppdateringar från Windows Update. Eller så kan du hämta det i ett OS-enskilt uppdaterings paket. Mer information finns i [Uppdatera för Universal C runtime i Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Du behöver ett konto som har Active Directory domän administratörs behörighet i skogs rots domänen för att registrera Windows Server Active Directory-skogen med Azure AD.
* Key Distribution Service måste vara aktive rad på alla domänkontrollanter i domänen som kör Windows Server 2012 och senare versioner. Som standard är den här tjänsten aktive rad via manuell utlösare start.

* Nätverks anslutningen måste finnas mellan minst en domänkontrollant i varje domän och minst en server som är värd för proxy tjänsten för lösen ords skydd i Azure AD. Den här anslutningen måste tillåta att domänkontrollanten får åtkomst till RPC Endpoint Mapper port 135 och RPC-serverport på proxyservern.
    * Som standard är RPC-Server porten en dynamisk RPC-port från intervallet (49152-65535), men den kan konfigureras för att [använda en statisk port](#static).
* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste ha nätverks åtkomst till följande slut punkter:

    |**Slutpunkt**|**Syfte**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Autentiseringsbegäranden|
    |`https://enterpriseregistration.windows.net`|Funktioner för lösen ords skydd i Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD Password Protection DC-agent

Följande krav gäller för Azure AD Password Protection DC-agenten:

* Alla datorer där Azure AD Password Protection DC Agent-programvaran ska installeras måste köra Windows Server 2012 eller senare, inklusive Windows Server Core-versioner.
    * Active Directory domän eller skog behöver inte finnas på Windows Server 2012-domän funktions nivå (DFL) eller skogens funktions nivå (FFL). Som vi nämnt i [design principer](concept-password-ban-bad-on-premises.md#design-principles)finns det ingen minsta DFL eller FFL som krävs för att antingen DC-agenten eller proxy-programvaran ska kunna köras.
* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste ha .NET-4.7.2 installerat.
    * Om .NET-4.7.2 inte redan har installerats kan du hämta och köra installations programmet som finns på [.NET Framework 4.7.2 Offline Installer för Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Alla Active Directory domäner som kör Azure AD Password Protection DC-agenttjänsten måste använda Distributed File System replikering (DFSR) för SYSVOL-replikering.
   * Om din domän inte redan använder DFSR måste du migrera innan du installerar lösen ords skyddet för Azure AD. Mer information finns i [migreringsguiden för SYSVOL-replikering: FRS till DFS Replication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD Password Protection DC Agent-programvaran installeras för närvarande på domänkontrollanter i domäner som fortfarande använder FRS (den föregående tekniken till DFSR) för SYSVOL-replikering, men program varan fungerar inte korrekt i den här miljön.
    >
    > Ytterligare negativa sido effekter är enskilda filer som Miss lyckas att replikera, och SYSVOL-återställnings procedurer visas som slutförda men tyst Miss lyckas för att replikera alla filer.
    >
    > Migrera din domän för att använda DFSR så snart som möjligt, både för DFSR: s inbyggda fördelar och för att avblockera distributionen av lösen ords skydd i Azure AD. Framtida versioner av program varan kommer att inaktive ras automatiskt när de körs i en domän som fortfarande använder FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Proxy för Azure AD Password Protection-proxy

Följande krav gäller för proxy för Azure AD Password Protection-proxy:

* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste köra Windows Server 2012 R2 eller senare, inklusive Windows Server Core-versioner.

    > [!NOTE]
    > Distribution av Azure AD-tjänsten för lösen ords skydd är ett obligatoriskt krav för att distribuera Azure AD Password Protection även om domänkontrollanten kan ha utgående direkt Internet anslutning.

* Alla datorer där Azure AD Password Protection-proxytjänsten ska installeras måste ha .NET-4.7.2 installerat.
    * Om .NET-4.7.2 inte redan har installerats kan du hämta och köra installations programmet som finns på [.NET Framework 4.7.2 Offline Installer för Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Alla datorer som är värdar för tjänsten Azure AD Password Protection proxy måste konfigureras för att ge domän kontrol Lanterna möjlighet att logga in på proxy-tjänsten. Den här funktionen styrs via privilegie tilldelningen "åtkomst till den här datorn från nätverket".
* Alla datorer som är värdar för tjänsten Azure AD Password Protection proxy måste konfigureras för att tillåta utgående TLS 1,2 HTTP-trafik.
* Ett *globalt administratörs* konto eller *säkerhets administratörs* konto för att registrera Azure AD-proxy för lösen ords skydd och skogen med Azure AD.
* Nätverks åtkomst måste vara aktiverat för den uppsättning portar och URL: er som anges i [installations procedurerna för programproxy-miljön](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Krav för Microsoft Azure AD Connect agent Updater

Uppdaterings tjänsten för Microsoft Azure AD Connect Agent installeras sida vid sida med Azure AD-proxyn för lösen ords skydd. Ytterligare konfiguration krävs för att Microsoft Azure AD Connect agent Updater-tjänsten ska kunna fungera:

* Om din miljö använder en HTTP-proxyserver följer du rikt linjerna som anges i [arbeta med befintliga lokala proxyservrar](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* Tjänsten Microsoft Azure AD Connect agent uppdaterar också de TLS 1,2-steg som anges i [TLS-krav](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Azure AD Password Protection-proxy och Azure AD-programproxy installera olika versioner av tjänsten Microsoft Azure AD Connect agent Updater, vilket är orsaken till innehållet i programproxyn. Dessa olika versioner är inkompatibla när de installeras sida vid sida och gör så att agent uppdaterings tjänsten inte kan kontakta Azure för program uppdateringar, så du bör aldrig installera Azure AD Password Protection proxy och Application Proxy på samma dator.

## <a name="download-required-software"></a>Hämta nödvändig program vara

Det finns två obligatoriska installations program för en lokal distribution av lösen ords skydd i Azure AD:

* Azure AD Password Protection DC-agent (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD-proxy för lösen ords skydd (*AzureADPasswordProtectionProxySetup.exe*)

Hämta båda installations program från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Installera och konfigurera proxyservern

Proxy tjänsten Azure AD Password Protection är vanligt vis på en medlems server i din lokala AD DS-miljö. När tjänsten Azure AD Password Protection-proxy har installerats kommunicerar Azure AD med Azure AD för att underhålla en kopia av listan över globala och kund förbjudna lösen ord för din Azure AD-klient.

I nästa avsnitt installerar du Azure AD Password Protection DC-agenter på domänkontrollanter i din lokala AD DS-miljö. De här DC-agenterna kommunicerar med proxyservern för att hämta de senaste förbjudna lösen ords listorna för användning vid bearbetning av lösen ords ändrings händelser i domänen.

Välj en eller flera servrar som ska vara värdar för Azure AD Password Protection proxy-tjänsten. Följande överväganden gäller för Server (erna):

* Varje sådan tjänst kan bara tillhandahålla lösen ords principer för en enda skog. Värddatorn måste vara ansluten till en domän i skogen.
* Du kan installera proxy-tjänsten i antingen rot-eller underordnade domäner eller en kombination av dessa.
* Du behöver en nätverks anslutning mellan minst en DOMÄNKONTROLLANT i varje domän i skogen och en proxyserver för lösen ords skydd.
* Du kan köra tjänsten Azure AD Password Protection proxy på en domänkontrollant för testning, men den domänkontrollanten kräver sedan Internet anslutning. Den här anslutningen kan vara en säkerhets risk. Vi rekommenderar den här konfigurationen endast för testning.
* Vi rekommenderar minst två proxyservrar för Azure AD-lösenordsautentisering per skog för redundans, enligt vad som anges i föregående avsnitt om [överväganden för hög tillgänglighet](#high-availability-considerations).
* Det finns inte stöd för att köra proxy tjänsten Azure AD Password Protection på en skrivskyddad domänkontrollant.

Slutför följande steg för att installera proxy-tjänsten för lösen ords skydd i Azure AD:

1. Om du vill installera proxy-tjänsten för Azure AD Password Protection kör du `AzureADPasswordProtectionProxySetup.exe` programinstallationen.

    Program varu installationen kräver ingen omstart och kan automatiseras med hjälp av standard-MSI-procedurer, som i följande exempel:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Tjänsten Windows-brandväggen måste köras innan du installerar `AzureADPasswordProtectionProxySetup.exe` paketet för att undvika ett installations fel.
    >
    > Om Windows-brandväggen har kon figurer ATS för att inte köras, är lösningen att tillfälligt aktivera och köra brand Väggs tjänsten under installationen. Proxy-programvaran har inget speciellt beroende av Windows-brandväggen efter installationen.
    >
    > Om du använder en brand vägg från en tredje part måste den fortfarande konfigureras för att uppfylla distributions kraven. Detta inkluderar att tillåta inkommande åtkomst till port 135 och proxy RPC-Server porten. Mer information finns i föregående avsnitt om [distributions krav](#deployment-requirements).

1. Programmet Azure AD Password Protection-proxy innehåller en ny PowerShell-modul `AzureADPasswordProtection` . Följande steg kör olika cmdlets från den här PowerShell-modulen.

    Om du vill använda den här modulen öppnar du ett PowerShell-fönster som administratör och importerar den nya modulen enligt följande:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Använd följande PowerShell-kommando för att kontrol lera att proxy tjänsten Azure AD Password Protection körs:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Resultatet bör visa **statusen** *körs*.

1. Proxy-tjänsten körs på datorn, men saknar autentiseringsuppgifter för att kommunicera med Azure AD. Registrera proxyservern för lösen ords skydd för Azure AD med Azure AD med hjälp av `Register-AzureADPasswordProtectionProxy` cmdleten.

    Denna cmdlet kräver antingen *Global administratörs* -eller *säkerhets administratörs* behörighet för din Azure-klient. Denna cmdlet måste också köras med ett konto med lokal administratörs behörighet.

    När det här kommandot har slutförts en gång kommer ytterligare anrop att lyckas, men är onödigt.

    `Register-AzureADPasswordProtectionProxy`Cmdleten stöder följande tre autentiseringsläge. De två första lägena har stöd för Azure AD Multi-Factor Authentication men det tredje läget.

    > [!TIP]
    > Det kan finnas en märkbar fördröjning före slut för ande första gången den här cmdleten körs för en viss Azure-klient. Om inte ett haveri rapporteras behöver du inte bekymra dig om den här fördröjningen.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande autentiseringsläge. Dessutom kan det här läget Miss förväntas om förbättrad säkerhets konfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera proxyservern och sedan aktivera den igen.

     * Autentiseringsläge för enhets kod:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        När du uppmanas till det följer du länken för att öppna en webbläsare och anger koden för autentisering.

     * Tyst (lösenordsbaserad) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget Miss lyckas om Azure AD Multi-Factor Authentication krävs för ditt konto. I så fall använder du något av de föregående två autentiseringsläget eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under försättsblad av Azure AD Password Protection) har kon figurer ATS för globalt Kräv MFA. För att undvika det här kravet kan du använda ett annat konto som har stöd för MFA med något av de tidigare två autentiseringsläget, eller så kan du också tillfälligt minska kraven för MFA-krav för Azure Device Registration.
        >
        > Om du vill göra den här ändringen söker du efter och väljer **Azure Active Directory** i Azure Portal och väljer sedan **enheter > enhets inställningar**. Ange **Kräv Multi-factor auth för att ansluta enheter** till *Nej*. Se till att konfigurera om den här inställningen igen till *Ja* när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast i test syfte.

    Du behöver för närvarande inte ange parametern *-ForestCredential* , som är reserverad för framtida funktioner.

    Registreringen av proxyn för lösen ords skydd i Azure AD krävs bara en gång under tjänstens livs längd. Därefter utför Azure AD Password Protection-proxytjänsten automatiskt alla andra nödvändiga underhåll.

1. Registrera nu den lokala Active Directory skogen med de autentiseringsuppgifter som krävs för att kommunicera med Azure med hjälp av `Register-AzureADPasswordProtectionForest` PowerShell-cmdleten.

    > [!NOTE]
    > Om flera Azure AD-proxyservrar för lösen ords skydd är installerade i din miljö spelar det ingen roll vilken proxyserver du använder för att registrera skogen.

    Cmdleten kräver antingen *Global administratör* eller *säkerhets administratörs* behörighet för din Azure-klient. Det kräver också lokala Active Directory företags administratörs behörighet. Du måste också köra denna cmdlet med ett konto med lokal administratörs behörighet. Det Azure-konto som används för att registrera skogen kan skilja sig från det lokala Active Directory kontot.
    
    Det här steget körs en gång per skog.

    `Register-AzureADPasswordProtectionForest`Cmdleten stöder följande tre autentiseringsläge. De två första lägena har stöd för Azure AD Multi-Factor Authentication men det tredje läget.

    > [!TIP]
    > Det kan finnas en märkbar fördröjning före slut för ande första gången den här cmdleten körs för en viss Azure-klient. Om inte ett haveri rapporteras behöver du inte bekymra dig om den här fördröjningen.

     * Interaktivt autentiseringsläge:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Det här läget fungerar inte på Server Core-operativsystem. Använd i stället något av följande två autentiseringsläge. Dessutom kan det här läget Miss förväntas om förbättrad säkerhets konfiguration i Internet Explorer är aktiverat. Lösningen är att inaktivera den konfigurationen, registrera skogen och sedan aktivera den igen.  

     * Autentiseringsläge för enhets kod:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        När du uppmanas till det följer du länken för att öppna en webbläsare och anger koden för autentisering.

     * Tyst (lösenordsbaserad) autentiseringsläge:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Det här läget Miss lyckas om Azure AD Multi-Factor Authentication krävs för ditt konto. I så fall använder du något av de föregående två autentiseringsläget eller använder i stället ett annat konto som inte kräver MFA.
        >
        > Du kan också se MFA krävs om Azure Device Registration (som används under försättsblad av Azure AD Password Protection) har kon figurer ATS för globalt Kräv MFA. För att undvika det här kravet kan du använda ett annat konto som har stöd för MFA med något av de tidigare två autentiseringsläget, eller så kan du också tillfälligt minska kraven för MFA-krav för Azure Device Registration.
        >
        > Om du vill göra den här ändringen söker du efter och väljer **Azure Active Directory** i Azure Portal och väljer sedan **enheter > enhets inställningar**. Ange **Kräv Multi-factor auth för att ansluta enheter** till *Nej*. Se till att konfigurera om den här inställningen igen till *Ja* när registreringen är klar.
        >
        > Vi rekommenderar att MFA-kraven kringgås endast i test syfte.

       De här exemplen fungerar bara om den för tillfället inloggade användaren också är en Active Directory domän administratör för rot domänen. Om detta inte är fallet kan du ange alternativa domänautentiseringsuppgifter via parametern *-ForestCredential* .

    Registrering av Active Directorys skogen krävs bara en gång i skogens livs längd. Därefter utför Azure AD Password Protection DC-agenterna i skogen automatiskt alla andra nödvändiga underhåll. När `Register-AzureADPasswordProtectionForest` körningen har lyckats för en skog lyckas ytterligare anrop av cmdleten, men är onödigt.
    
    För `Register-AzureADPasswordProtectionForest` att lyckas måste minst en domänkontrollant som kör Windows Server 2012 eller senare vara tillgänglig i Azure AD-proxyservern för lösen ords skydd. Azure AD Password Protection DC Agent-programvaran behöver inte installeras på några domänkontrollanter innan det här steget.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurera proxy-tjänsten för att kommunicera via en HTTP-proxy

Om din miljö kräver att en särskild HTTP-proxy används för att kommunicera med Azure använder du följande steg för att konfigurera Azure AD-tjänsten för lösen ords skydd.

Skapa en *AzureADPasswordProtectionProxy.exe.config* -fil i `%ProgramFiles%\Azure AD Password Protection Proxy\Service` mappen. Ta med följande innehåll:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Om HTTP-proxyn kräver autentisering lägger du till *useDefaultCredentials* -taggen:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

I båda fallen ersätter du `http://yourhttpproxy.com:8080` med adressen och porten för din aktuella HTTP-proxyserver.

Om din HTTP-proxy har kon figurer ATS för att använda en auktoriseringsprincip måste du bevilja åtkomst till Active Directory dator kontot för den dator som är värd för proxyservern för lösen ords skydd.

Vi rekommenderar att du stoppar och startar om tjänsten Azure AD Password Protection proxy när du har skapat eller uppdaterat *AzureADPasswordProtectionProxy.exe.config* -filen.

Proxy-tjänsten stöder inte användning av autentiseringsuppgifter för att ansluta till en HTTP-proxy.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurera proxy-tjänsten att lyssna på en angiven port

Azure AD Password Protection DC Agent-programvaran använder RPC över TCP för att kommunicera med proxy-tjänsten. Som standard lyssnar proxy tjänsten Azure AD Password Protection på en tillgänglig dynamisk RPC-slutpunkt. Du kan konfigurera tjänsten så att den lyssnar på en angiven TCP-port, om det behövs på grund av nätverkets topologi eller brand Väggs krav i din miljö.

<a id="static" /></a>Om du vill konfigurera tjänsten så att den körs under en statisk port använder du `Set-AzureADPasswordProtectionProxyConfiguration` cmdleten på följande sätt:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Du måste stoppa och starta om tjänsten Azure AD Password Protection proxy för att ändringarna ska börja gälla.

Om du vill konfigurera tjänsten så att den körs under en dynamisk port använder du samma procedur men anger *StaticPort* tillbaka till noll:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Du måste stoppa och starta om tjänsten Azure AD Password Protection proxy för att ändringarna ska börja gälla.

Proxy-tjänsten för lösen ords skydd i Azure AD kräver en manuell omstart efter en ändring i Port konfigurationen. Du behöver inte starta om tjänsten Azure AD Password Protection DC agent på domänkontrollanter när du har gjort dessa konfigurations ändringar.

Om du vill fråga efter den aktuella konfigurationen av tjänsten använder du `Get-AzureADPasswordProtectionProxyConfiguration` cmdleten som visas i följande exempel

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Följande exempel på utdata visar att proxy tjänsten Azure AD Password Protection använder en dynamisk port:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Installera DC-agenttjänsten

Om du vill installera tjänsten Azure AD Password Protection DC agent kör du `AzureADPasswordProtectionDCAgentSetup.msi` paketet.

Du kan automatisera programinstallationen med hjälp av standard-MSI-procedurer, som du ser i följande exempel:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

`/norestart`Flaggan kan utelämnas om du vill att installations programmet ska starta om datorn automatiskt.

Program varu installationen eller avinstallationen kräver en omstart. Detta krav beror på att lösen ords filter-DLL: er bara läses in eller tas bort av en omstart.

Installationen av lokal Azure AD Password Protection slutförs när DC-agentens program vara har installerats på en domänkontrollant och datorn startas om. Ingen annan konfiguration krävs eller är möjlig. Lösen ords ändrings händelser mot lokal DCs använder de konfigurerade förbjudna lösen ords listorna från Azure AD.

Om du vill aktivera lokal Azure AD Password Protection från Azure Portal eller konfigurera anpassade förbjudna lösen ord, se [Aktivera lösen ords skydd i Azure AD](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Du kan installera Azure AD Password Protection DC-agenten på en dator som ännu inte är en domänkontrollant. I det här fallet startar tjänsten och körs men förblir inaktiv tills datorn uppgraderas till en domänkontrollant.

## <a name="upgrading-the-proxy-service"></a>Uppgradera proxy-tjänsten

Proxy tjänsten Azure AD Password Protection stöder automatisk uppgradering. Vid automatisk uppgradering används tjänsten Microsoft Azure AD Connect agent Updateer, som installeras sida vid sida med proxy-tjänsten. Automatisk uppgradering är aktiverat som standard och kan aktive ras eller inaktive ras med `Set-AzureADPasswordProtectionProxyConfiguration` cmdleten.

Den aktuella inställningen kan frågas med hjälp av `Get-AzureADPasswordProtectionProxyConfiguration` cmdleten. Vi rekommenderar att inställningen för automatisk uppgradering alltid är aktive rad.

`Get-AzureADPasswordProtectionProxy`Cmdleten kan användas för att fråga program varu versionen av alla installerade proxyservrar för Azure AD-lösenordet i en skog.

### <a name="manual-upgrade-process"></a>Manuell uppgraderings process

Du kan utföra en manuell uppgradering genom att köra den senaste versionen av `AzureADPasswordProtectionProxySetup.exe` program varu installations programmet. Den senaste versionen av program varan finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Du behöver inte avinstallera den aktuella versionen av proxyn för lösen ords skydd för Azure AD – installations programmet utför en uppgradering på plats. Ingen omstart krävs när du uppgraderar proxy-tjänsten. Program uppgraderingen kan automatiseras med hjälp av standard-MSI-procedurer, till exempel `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Uppgradera DC-agenten

När en nyare version av Azure AD Password Protection DC Agent-programvaran är tillgänglig utförs uppgraderingen genom att den senaste versionen av `AzureADPasswordProtectionDCAgentSetup.msi` program varu paketet körs. Den senaste versionen av program varan finns på [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Du behöver inte avinstallera den aktuella versionen av program varan för DC-agenten. installations programmet utför en uppgradering på plats. En omstart krävs alltid vid uppgradering av DC Agent-programvaran – detta krav orsakas av Windows-funktioner.

Program uppgraderingen kan automatiseras med hjälp av standard-MSI-procedurer, till exempel `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

Du kan utelämna `/norestart` flaggan om du hellre vill att installations programmet ska starta om datorn automatiskt.

`Get-AzureADPasswordProtectionDCAgent`Cmdleten kan användas för att fråga program varu versionen av alla installerade DC-agenter för lösen ords skydd i Azure AD i en skog.

## <a name="next-steps"></a>Nästa steg

Nu när du har installerat de tjänster som du behöver för Azure AD Password Protection på dina lokala servrar [aktiverar du lokal Azure AD Password Protection i Azure Portal](howto-password-ban-bad-on-premises-operations.md) för att slutföra distributionen.
