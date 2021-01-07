---
title: Installera anslutningens hälso agenter i Azure Active Directory
description: I den här Azure AD Connect Health artikeln beskrivs agent installation för Active Directory Federation Services (AD FS) (AD FS) och för synkronisering.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ff6f96c62936d8bf66b908060b91f8cad7989763
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962604"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health Agent installation

I den här artikeln får du lära dig hur du installerar och konfigurerar de Azure Active Directory (Azure AD) Connect Health-agenterna. Information om hur du hämtar agenterna finns i [följande instruktioner](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Krav

I följande tabell visas kraven för att använda Azure AD Connect Health.

| Krav | Beskrivning |
| --- | --- |
| Azure AD Premium har installerats. |Azure AD Connect Health är en funktion i Azure AD Premium. Mer information finns i [Registrera dig för Azure AD Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Om du vill starta en kostnads fri 30-dagars utvärderings version läser du [starta en utvärderings version](https://azure.microsoft.com/trial/get-started-active-directory/). |
| Du är global administratör i Azure AD. |Som standard kan endast globala administratörer installera och konfigurera hälso agenter, få åtkomst till portalen och utföra åtgärder i Azure AD Connect Health. Mer information finns i [Administrera Azure AD-katalogen](../fundamentals/active-directory-whatis.md). <br /><br /> Genom att använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du ge andra användare i organisationen åtkomst till Azure AD Connect Health. Mer information finns i [Azure RBAC för Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Viktigt**: Använd ett arbets-eller skol konto för att installera agenterna. Du kan inte använda en Microsoft-konto. Mer information finns i [Registrera dig för Azure som en organisation](../fundamentals/sign-up-organization.md). |
| Azure AD Connect Health agenten installeras på varje mål server. | Hälso agenter måste installeras och konfigureras på mål servrar så att de kan ta emot data och tillhandahålla funktioner för övervakning och analys. <br /><br />Om du till exempel vill hämta data från din Active Directory Federation Services (AD FS)-infrastruktur (AD FS) måste du installera agenten på AD FS-servern och Web Application Proxy-servern. På samma sätt måste du installera agenten på domän kontrol Lanterna för att kunna hämta data från din lokala Azure AD Domain Services-infrastruktur (Azure AD DS).  |
| Azure-tjänstens slut punkter har utgående anslutning. | Under installation och körning kräver agenten anslutning till Azure AD Connect Health-tjänstens slutpunkter. Om brand väggar blockerar utgående anslutning lägger du till [slut punkterna för utgående anslutningar](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) i listan över tillåtna. |
|Utgående anslutningar baseras på IP-adresser. | Information om brand Väggs filtrering baserat på IP-adresser finns i avsnittet om [Azure IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).|
| TLS-kontroll för utgående trafik filtreras eller inaktive ras. | Det kan hända att agent registrerings steget eller data överförings åtgärderna Miss söker om TLS-kontroll eller uppsägning av utgående trafik på nätverks lagret. Mer information finns i [Konfigurera TLS-kontroll](/previous-versions/tn-archive/ee796230(v=technet.10)). |
| Brand Väggs portar på servern kör-agenten. |Agenten kräver att följande brand Väggs portar är öppna så att de kan kommunicera med Azure AD Connect Health tjänst slut punkter: <br /><li>TCP-port 443</li><li>TCP-port 5671</li> <br />Den senaste versionen av agenten kräver inte port 5671. Uppgradera till den senaste versionen så att endast port 443 krävs. Mer information finns i [hybrid identitet krävs portar och protokoll](./reference-connect-ports.md). |
| Tillåt angivna webbplatser om förbättrad säkerhet i Internet Explorer är aktiverat.  |Om förbättrad säkerhet i Internet Explorer är aktiverat kan du tillåta följande webbplatser på den server där du installerar agenten:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Federations servern för din organisation som är betrodd av Azure AD (till exempel https: \/ /STS.contoso.com)</li> <br />Mer information finns i [så här konfigurerar du Internet Explorer](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing). Om du har en proxyserver i nätverket kan du se den anteckning som visas i slutet av den här tabellen.|
| PowerShell version 4,0 eller senare är installerat. | Windows Server 2012 innehåller PowerShell-version 3,0. Den här versionen räcker *inte* för agenten.</br></br> Windows Server 2012 R2 och senare innehåller en tillräckligt ny version av PowerShell.|
|FIPS (Federal Information Processing Standard) är inaktiverat.|Azure AD Connect Healths agenter stöder inte FIPS.|

> [!IMPORTANT]
> Windows Server Core stöder inte installation av Azure AD Connect Health agenten.


> [!NOTE]
> Om du har en mycket låst och begränsad miljö måste du lägga till fler webb adresser än de som visas i tabell listan för förbättrad säkerhet i Internet Explorer. Lägg också till URL: er som anges i tabellen i nästa avsnitt.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Utgående anslutning till Azure-tjänstens slutpunkter

Under installationen och körningen behöver agenten anslutning till Azure AD Connect Health tjänstens slut punkter. Om brand väggar blockerar utgående anslutning kontrollerar du att URL: erna i följande tabell inte blockeras som standard. 

Inaktivera inte säkerhets övervakning eller inspektion av dessa URL: er. Låt dem i stället tillåta dem som att tillåta annan Internet trafik. 

Dessa URL: er tillåter kommunikation med Azure AD Connect Health tjänst slut punkter. Senare i den här artikeln får du lära dig hur du [kontrollerar utgående anslutning](#test-connectivity-to-azure-ad-connect-health-service) med hjälp av `Test-AzureADConnectHealthConnectivity` .

| Domän miljö | Nödvändiga Azure-tjänsteslutpunkter |
| --- | --- |
| Allmän offentlig | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;. servicebus.windows.net-port: 5671 (den här slut punkten krävs inte i den senaste versionen av agenten.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (den här slut punkten används endast i identifierings syfte under registreringen.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Tyskland | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.Office.de (den här slut punkten används endast i identifierings syfte under registreringen.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.Office.com (den här slut punkten används endast i identifierings syfte under registreringen.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Installera agenten

Så här hämtar och installerar du Azure AD Connect Health Agent: 

* Kontrol lera att du uppfyller [kraven](how-to-connect-health-agent-install.md#requirements) för Azure AD Connect Health.
* Kom igång med Azure AD Connect Health för AD FS:
    * [Hämta Azure AD Connect Health Agent för AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Se [installations anvisningarna](#install-the-agent-for-ad-fs).
* Kom igång med att använda Azure AD Connect Health för synkronisering:
    * [Hämta och installera den senaste versionen av Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). Hälso agenten för synkronisering installeras som en del av Azure AD Connect installationen (version 1.0.9125.0 eller senare).
* Kom igång med Azure AD Connect Health för Azure AD DS:
    * [Ladda ned Azure AD Connect Health Agent för Azure AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Se [installations anvisningarna](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Installera agenten för AD FS

> [!NOTE]
> Din AD FS server bör vara en annan än din Sync-Server. Installera inte AD FS-agenten på din Sync-Server.
>

Innan du installerar agenten ska du kontrol lera att namnet på AD FSs serverns värdnamn är unikt och inte finns i AD FSs tjänsten.
Starta Agent installationen genom att dubbelklicka på *exe* -filen som du laddade ned. I det första fönstret väljer du **Installera**.

![Skärm bild som visar installations fönstret för Azure AD Connect Health AD FS agenten.](./media/how-to-connect-health-agent-install/install1.png)

När installationen är klar väljer du **Konfigurera nu**.

![Skärm bild som visar bekräftelse meddelandet för installationen av Azure AD Connect Health AD FS-agenten.](./media/how-to-connect-health-agent-install/install2.png)

Ett PowerShell-fönster öppnas för att starta agent registrerings processen. När du uppmanas att logga in loggar du in med ett Azure AD-konto som har behörighet att registrera agenten. Som standard har det globala administratörs kontot behörighet.

![Skärm bild som visar inloggnings fönstret för Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

När du har loggat in fortsätter PowerShell. När den är klar kan du stänga PowerShell. Konfigurationen är klar.

I det här läget bör Agent tjänsterna starta automatiskt så att agenten kan ladda upp nödvändiga data till moln tjänsten på ett säkert sätt.

Om du inte har uppfyllt alla nödvändiga komponenter visas varningar i PowerShell-fönstret. Se till att slutföra [kraven](how-to-connect-health-agent-install.md#requirements) innan du installerar agenten. Följande skärm bild visar ett exempel på dessa varningar.

![Skärm bild som visar Azure AD Connect Health AD FS konfigurerar skript.](./media/how-to-connect-health-agent-install/install4.png)

Kontrol lera att agenten har installerats genom att leta efter följande tjänster på servern. Dessa tjänster bör köras om du har slutfört konfigurationen. Annars stoppas de tills konfigurationen är klar.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Skärm bild som visar Azure AD Connect Health AD FS-tjänster.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Aktivera granskning för AD FS

> [!NOTE]
> Det här avsnittet gäller endast för AD FS-servrar. Du behöver inte följa de här stegen på Web Application Proxy-servrar.
>

Funktionen för användnings analys behöver samla in och analysera data. Den Azure AD Connect Health agenten behöver informationen i AD FS gransknings loggarna. Dessa loggar är inte aktiverade som standard. Använd följande procedurer för att aktivera AD FS granskning och för att hitta AD FS gransknings loggar på dina AD FS-servrar.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Så här aktiverar du granskning för AD FS i Windows Server 2012 R2

1. Öppna **Serverhanteraren** på Start skärmen och öppna sedan **lokal säkerhets princip**. Eller öppna **Serverhanteraren** i verktygsfältet och välj sedan **verktyg/lokal säkerhets princip**.
2. Gå till mappen *säkerhets-lokala Principer\tilldelning Rights Assignment* . Dubbelklicka sedan på **generera säkerhets granskningar**.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS-tjänstkontot visas. Om den inte visas väljer du **Lägg till användare eller grupp** och lägger till den i listan. Välj sedan **OK**.
4. Om du vill aktivera granskning öppnar du ett kommando tolks fönster med förhöjd behörighet. Kör följande kommando: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Stäng **Lokal säkerhetsprincip**.
    >[!Important]
    >Följande steg krävs endast för primära AD FS-servrar. 
1. Öppna snapin-modulen **AD FS-hantering**. (I **Serverhanteraren** väljer du **verktyg**  >  **AD FS hantering**.)
1. I fönstret **åtgärder** väljer du **Redigera federationstjänst egenskaper**.
1. I dialog rutan **federationstjänst egenskaper** väljer du fliken **händelser** .
1. Markera kryss rutorna **lyckade granskningar och misslyckade granskningar** och välj sedan **OK**.
1. Om du vill aktivera utförlig loggning via PowerShell använder du följande kommando: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Så här aktiverar du granskning för AD FS i Windows Server 2016

1. Öppna **Serverhanteraren** på Start skärmen och öppna sedan **lokal säkerhets princip**. Eller öppna **Serverhanteraren** i verktygsfältet och välj sedan **verktyg/lokal säkerhets princip**.
2. Gå till mappen *säkerhets-lokala Principer\tilldelning Rights Assignment* och dubbelklicka sedan på **generera säkerhets granskningar**.
3. På fliken **Lokal säkerhetsinställning** kontrollerar du att AD FS-tjänstkontot visas. Om den inte visas väljer du **Lägg till användare eller grupp** och lägger till AD FS tjänst kontot i listan. Välj sedan **OK**.
4. Om du vill aktivera granskning öppnar du ett kommando tolks fönster med förhöjd behörighet. Kör följande kommando: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Stäng **Lokal säkerhetsprincip**.
    >[!Important]
    >Följande steg krävs endast för primära AD FS-servrar.
1. Öppna snapin-modulen **AD FS-hantering**. (I **Serverhanteraren** väljer du **verktyg**  >  **AD FS hantering**.)
1. I fönstret **åtgärder** väljer du **Redigera federationstjänst egenskaper**.
1. I dialog rutan **federationstjänst egenskaper** väljer du fliken **händelser** .
1. Markera kryss rutorna **lyckade granskningar och misslyckade granskningar** och välj sedan **OK**. Lyckade granskningar och misslyckade granskningar ska vara aktiverade som standard.
1. Öppna ett PowerShell-fönster och kör följande kommando: 

    `Set-AdfsProperties -AuditLevel Verbose`

Gransknings nivån "Basic" är aktive rad som standard. Mer information finns i [AD FS gransknings förbättring i Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Så här hittar du AD FS-granskningsloggar

1. Öppna **Loggboken**.
2. Gå till **Windows-loggar** och välj sedan **säkerhet**.
3. Välj **filtrera aktuella loggar** till höger.
4. För **händelse källor** väljer du **AD FS granskning**.

    Mer information om gransknings loggar finns i [åtgärder frågor](reference-connect-health-faq.md#operations-questions).

    ![Skärm bild som visar det aktuella logg fönstret Filtrera. I fältet "händelse källor", "AD FS granskning" är markerat.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> En grupprincip kan inaktivera AD FS-granskning. Om AD FS granskning har inaktiverats är användnings analyser om inloggnings aktiviteter inte tillgängliga. Se till att du inte har någon grup princip som inaktiverar AD FS granskning.
>


## <a name="install-the-agent-for-sync"></a>Installera agenten för synkronisering

Azure AD Connect Health agenten för synkronisering installeras automatiskt i den senaste versionen av Azure AD Connect. Om du vill använda Azure AD Connect för synkronisering [laddar du ned den senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) och installerar den.

Kontrollera att agenten har installerats genom att leta efter följande tjänster på servern. Om du har slutfört konfigurationen bör tjänsterna redan köras. Annars stoppas tjänsterna tills konfigurationen är klar.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![Skärm bild som visar Azure AD Connect Health som körs för Sync Services på servern.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Kom ihåg att du måste ha Azure AD Premium att använda Azure AD Connect Health. Om du inte har Azure AD Premium kan du inte slutföra konfigurationen i Azure Portal. Mer information finns i [kraven](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Registrera Azure AD Connect Health manuellt för synkronisering

Om Azure AD Connect Health för registrering av Sync-agenten Miss lyckas efter att du har installerat Azure AD Connect kan du använda ett PowerShell-kommando för att registrera agenten manuellt.

> [!IMPORTANT]
> Använd endast det här PowerShell-kommandot om agent registreringen Miss lyckas när du har installerat Azure AD Connect.
>
>

Registrera Azure AD Connect Health-agenten manuellt för synkronisering med hjälp av följande PowerShell-kommando. Azure AD Connect Health-tjänsterna startar efter att agenten har registrerats.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Kommandot stöder följande parametrar:

* **AttributeFiltering**: `$true` (standard) om Azure AD Connect inte synkroniserar standardattributet och har anpassats för användning av en filtrerad attribut uppsättning. Annars använder du `$false` .
* **StagingMode**: `$false` (standard) om Azure AD Connect servern *inte* är i mellanlagrings läge. Om servern är konfigurerad för att vara i mellanlagrings läge använder du `$true` .

När du uppmanas att autentisera använder du samma globala administratörs konto (till exempel admin@domain.onmicrosoft.com ) som du använde för att konfigurera Azure AD Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Installera agenten för Azure AD DS

Starta Agent installationen genom att dubbelklicka på *exe* -filen som du laddade ned. I det första fönstret väljer du **Installera**.

![Skärm bild som visar installations fönstret Azure AD Connect Health Agent för AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

När installationen är klar väljer du **Konfigurera nu**.

![Skärm bild som visar fönstret som avslutar installationen av Azure AD Connect Health Agent för Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Ett kommando tolks fönster öppnas. PowerShell körs `Register-AzureADConnectHealthADDSAgent` . När du uppmanas till det loggar du in på Azure.

![Skärm bild som visar inloggnings fönstret för Azure AD Connect Health agenten för Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

När du har loggat in fortsätter PowerShell. När den är klar kan du stänga PowerShell. Konfigurationen är klar.

I det här läget bör tjänsterna startas automatiskt, så att agenten kan övervaka och samla in data. Om du inte har uppfyllt alla krav som beskrivs i föregående avsnitt visas varningar i PowerShell-fönstret. Se till att slutföra [kraven](how-to-connect-health-agent-install.md#requirements) innan du installerar agenten. Följande skärm bild visar ett exempel på dessa varningar.

![Skärm bild som visar en varning för den Azure AD Connect Health agenten för Azure AD DS-konfigurationen.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Kontrol lera att agenten har installerats genom att leta efter följande tjänster på domänkontrollanten:

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

Dessa tjänster bör köras om du har slutfört konfigurationen. Annars stoppas de tills konfigurationen är klar.

![Skärm bild som visar de tjänster som körs på domänkontrollanten.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Installera agenten snabbt på flera servrar

1. Skapa ett användar konto i Azure AD. Skydda det med ett lösen ord.
2. Tilldela **ägar** rollen för det lokala Azure AD-kontot i Azure AD Connect Health med hjälp av portalen. Följ [de här stegen](how-to-connect-health-operations.md#manage-access-with-azure-rbac). Tilldela rollen till alla tjänst instanser. 
3. Hämta MSI-filen för *. exe* i den lokala domänkontrollanten för installationen.
4. Kör följande skript. Ersätt parametrarna med det nya användar kontot och lösen ordet. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

När du är klar kan du ta bort åtkomsten för det lokala kontot genom att göra en eller flera av följande uppgifter: 
* Ta bort roll tilldelningen för det lokala kontot för Azure AD Connect Health.
* Rotera lösen ordet för det lokala kontot. 
* Inaktivera det lokala Azure AD-kontot.
* Ta bort det lokala Azure AD-kontot.  

## <a name="register-the-agent-by-using-powershell"></a>Registrera agenten med hjälp av PowerShell

När du har installerat rätt agent *setup.exe* -fil kan du registrera agenten med hjälp av följande PowerShell-kommandon, beroende på roll. Öppna ett PowerShell-fönster och kör lämpligt kommando:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> Om du vill registrera dig för suveräna moln använder du följande kommando rader:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


De här kommandona godkänns `Credential` som en parameter för att slutföra registreringen på ett inaktivt sätt eller för att slutföra registreringen på en dator som kör Server Core. Tänk på följande:
* Du kan avbilda `Credential` i en PowerShell-variabel som skickas som en parameter.
* Du kan ange en Azure AD-identitet som har behörighet att registrera agenter och som inte *har multifaktorautentisering* aktiverat.
* Globala administratörer har som standard behörighet att registrera agenter. Du kan också tillåta mindre privilegierade identiteter för det här steget. Mer information finns i [Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac).

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Konfigurera Azure AD Connect Health agenter att använda HTTP-proxy

Du kan konfigurera Azure AD Connect Health agenter så att de fungerar med en HTTP-proxy.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` stöds inte. Agenten använder System.Net i stället för Windows HTTP-tjänster för att göra webb förfrågningar.
> * Den konfigurerade HTTP-proxyservern används för att skicka krypterade HTTPS-meddelanden.
> * Autentiserade proxyservrar (med HTTPBasic) stöds inte.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Ändra agentens proxykonfiguration

Om du vill konfigurera Azure AD Connect Health agenten så att den använder en HTTP-proxy kan du:
* Importera befintliga proxyinställningar.
* Ange proxyadresser manuellt.
* Rensa den befintliga proxykonfigurationen.

> [!NOTE]
> Om du vill uppdatera proxyinställningarna måste du starta om alla Azure AD Connect Health Agent tjänster. Kör följande kommando:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importera befintliga proxyinställningar

Du kan importera inställningar för Internet Explorer HTTP-proxy så att Azure AD Connect Health agenter kan använda inställningarna. Kör följande PowerShell-kommando på alla servrar som kör hälso agenten:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Du kan importera inställningar för WinHTTP-proxy så att Azure AD Connect Health agenter kan använda dem. Kör följande PowerShell-kommando på alla servrar som kör hälso agenten:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Ange proxyadresser manuellt

Du kan ange en proxyserver manuellt. Kör följande PowerShell-kommando på alla servrar som kör hälso agenten:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Här är ett exempel: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

I det här exemplet:
* `address`Inställningen kan vara ett server namn som kan matchas av DNS eller en IPv4-adress.
* Du kan utelämna `port` . Om du gör det är 443 standard porten.

#### <a name="clear-the-existing-proxy-configuration"></a>Rensa den befintliga proxykonfigurationen

Du kan rensa den befintliga proxykonfigurationen genom att köra följande kommando:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Läsa de aktuella proxyinställningarna

Du kan läsa de aktuella inställningarna för proxy genom att köra följande kommando:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testa anslutningen till Azure AD Connect Health tjänsten

Ibland kan Azure AD Connect Health-agenten förlora anslutningen till Azure AD Connect Healths tjänsten. Orsaker till den här anslutnings förlusten kan omfatta nätverks problem, behörighets problem och andra problem.

Om agenten inte kan skicka data till Azure AD Connect Health tjänsten under mer än två timmar visas följande avisering i portalen: "Hälsotjänst data är inte uppdaterade". 

Du kan ta reda på om den berörda Azure AD Connect Health agenten kan ladda upp data till Azure AD Connect Health-tjänsten genom att köra följande PowerShell-kommando:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

Rollparametern har för närvarande följande värden:

* ADFS
* Synkronisera
* ADDS

> [!NOTE]
> Om du vill använda anslutnings verktyget måste du först registrera agenten. Om du inte kan slutföra Agent registreringen kontrollerar du att du uppfyller alla [krav](how-to-connect-health-agent-install.md#requirements) för Azure AD Connect Health. Anslutningen testas som standard under agent registreringen.
>
>

## <a name="next-steps"></a>Nästa steg

Kolla in följande relaterade artiklar:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health åtgärder](how-to-connect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
* [Använda Azure AD Connect Health med Azure AD DS](how-to-connect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](reference-connect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](reference-connect-health-version-history.md)
