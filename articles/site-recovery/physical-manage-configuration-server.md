---
title: Hantera konfigurations servern för fysiska servrar i Azure Site Recovery
description: Den här artikeln beskriver hur du hanterar den Azure Site Recovery konfigurations servern för haveri beredskap för fysiska servrar till Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017448"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Hantera konfigurations servern för haveri beredskap för fysiska servrar

Du konfigurerar en lokal konfigurations server när du använder tjänsten [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för fysiska servrar till Azure. Konfigurations servern samordnar kommunikationen mellan lokala datorer och Azure och hanterar datareplikering. I den här artikeln sammanfattas vanliga aktiviteter för att hantera konfigurations servern efter att den har distribuerats.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

I tabellen sammanfattas kraven för distribution av den lokala konfigurations servern.

| **Komponent** | **Krav** |
| --- |---|
| Processorkärnor| 8 |
| RAM | 16 GB|
| Antal diskar | 3, inklusive OS-disken, cache-disk för processerver och lagrings enhet för återställning efter fel |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| Operativsystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | Engelska (USA)|
| VMware vSphere PowerCLI-version | Krävs inte|
| Windows Server-roller | Aktivera inte följande roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grup principer| Aktivera inte dessa grup principer: <br> -Förhindra åtkomst till kommando tolken <br> -Förhindra åtkomst till verktyg för redigering av registret <br> – Förtroende logik för bifogade filer <br> – Aktivera skript körning <br> [Läs mer](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | -Ingen befintlig standard webbplats <br> -Aktivera  [Anonym autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Aktivera [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) -inställning  <br> -Ingen befintlig webbplats/program som lyssnar på port 443<br>|
| Typ av nätverkskort | VMXNET3 (när den distribueras som en virtuell VMware-dator) |
| IP-adresstyp | Statisk |
| Internetåtkomst | Servern behöver åtkomst till följande URL: er: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> -*. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (krävs inte för skalbara process servrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|

## <a name="download-the-latest-installation-file"></a>Hämta den senaste installations filen

Den senaste versionen av konfigurations serverns installations fil finns på Site Recovery Portal. Dessutom kan den hämtas direkt från [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Logga in på Azure Portal och bläddra till Recovery Services-valvet.
2. Bläddra till **Site Recovery infrastruktur**  >  **konfigurations servrar** (under för VMware & fysiska datorer).
3. Klicka på knappen **+ servrar** .
4. På sidan **Lägg till Server** klickar du på knappen Ladda ned för att ladda ned registrerings nyckeln. Du behöver den här nyckeln under installationen av konfigurations servern för att registrera den med Azure Site Recovery-tjänsten.
5. Klicka på länken **hämta Microsoft Azure Site Recovery Unified setup** för att ladda ned den senaste versionen av konfigurations servern.

   ![Hämtnings sida](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installera och registrera servern

1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar** väljer du **Installera konfigurations servern och processervern**.

    ![Innan du börjar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.
4. I **Internet inställningar** anger du hur providern som körs på konfigurations servern ansluter till Azure Site Recovery via Internet. Kontrol lera att du har tillåtit de nödvändiga URL: erna.

    - Om du vill ansluta till den proxyserver som är konfigurerad på datorn väljer **du Anslut för att Azure Site Recovery med hjälp av en proxyserver**.
    - Om du vill att providern ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyn kräver autentisering, eller om du vill använda en anpassad proxy för anslutnings tjänsten, väljer du **Anslut med anpassade proxyinställningar** och anger adressen, porten och autentiseringsuppgifterna.
     ![Brandvägg](./media/physical-manage-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Förutsättningar](./media/physical-manage-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Om du gör det kontrollerar installations programmet att PowerCLI 6,0 har installerats.
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/physical-manage-configuration-server/combined-wiz8.png)
10. I **Val av nätverk** väljer du först det nätverkskort som den inbyggda processervern använder för identifiering och push-installation av mobilitets tjänsten på käll datorer. Välj sedan det nätverkskort som konfigurations servern använder för anslutning med Azure. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Val av nätverk](./media/physical-manage-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.


När registreringen är klar visas servern på bladet **Inställningar**  >  **servrar** i valvet.


## <a name="install-from-the-command-line"></a>Installera från kommando raden

Kör installations filen på följande sätt:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exempel användning
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametrar

|Parameternamn| Typ | Beskrivning| Värden|
|-|-|-|-|
| /ServerMode|Obligatorisk|Anger om både konfigurations- och processervrar eller endast processervern ska installeras|CS<br>PS|
|/InstallLocation|Obligatorisk|Den mapp där komponenterna installeras| Vilken mapp på datorn som helst|
|/MySQLCredsFilePath|Obligatorisk|Filsökvägen till platsen där autentiseringsuppgifterna för MySQL-servern lagras|Filen ska vara i det format som anges nedan|
|/VaultCredsFilePath|Obligatorisk|Sökvägen för valvautentiseringsfilen|Giltig sökväg|
|/EnvType|Obligatorisk|Typ av miljö som du vill skydda |VMware<br>NonVMware|
|/PSIP|Obligatorisk|Nätverkskortets IP-adress används för överföring av replikeringsdata| Vilken giltig IP-adress som helst|
|/CSIP|Obligatorisk|Nätverkskortets IP-adress som konfigurationsservern lyssnar på| Vilken giltig IP-adress som helst|
|/PassphraseFilePath|Obligatorisk|Den fullständiga sökvägen till platsen för lösenfrasfilen|Giltig sökväg|
|/BypassProxy|Valfritt|Anger att konfigurationsservern ansluter till Azure utan en proxyserver|För att få det här värdet från Venu|
|/ProxySettingsFilePath|Valfritt|Proxy-inställningar (standardproxy kräver autentisering, eller en anpassad proxy)|Filen ska vara i det format som anges nedan|
|DataTransferSecurePort|Valfritt|Portnumret på PSIP ska användas för replikeringsdata| Giltigt portnummer (standardvärdet är 9433)|
|/SkipSpaceCheck|Valfritt|Hoppa över utrymmeskontroll för cachedisk| |
|/AcceptThirdpartyEULA|Obligatorisk|När du flaggar innebär det att du godkänner licensavtalet från tredje part| |
|/ShowThirdpartyEULA|Valfritt|Visar licensavtalet (EULA) från tredje part. Om detta anges som indata ignoreras alla andra parametrar| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Skapa fil indata för MYSQLCredsFilePath

Parametern MySQLCredsFilePath använder en fil som indata. Skapa filen med följande format och skicka den som MySQLCredsFilePath-parameter för indata.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Skapa fil indata för ProxySettingsFilePath
ProxySettingsFilePath-parametern använder en fil som indata. Skapa filen med följande format och skicka den som ProxySettingsFilePath-parameter för indata.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Ändra proxyinställningar

Du kan ändra proxyinställningarna för Configuration Server-datorn på följande sätt:

1. Logga in på konfigurations servern.
2. Starta cspsconfigtool.exe med hjälp av genvägen på Skriv bordet.
3. Klicka på fliken **valv registrering** .
4. Hämta en ny valv registrerings fil från portalen och ange den som indata för verktyget.

   ![registrera-konfiguration-Server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange den nya informationen om proxy och klicka på knappen **Registrera** .
6. Öppna ett admin PowerShell-kommando fönster.
7. Kör följande kommando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Om du har ytterligare process servrar som är anslutna till konfigurations servern måste du [Korrigera proxyinställningarna på alla skalbara process servrar](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) i distributionen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Omregistrera en konfigurations server med samma valv
1. Logga in på konfigurations servern.
2. Starta cspsconfigtool.exe med hjälp av genvägen på Skriv bordet.
3. Klicka på fliken **valv registrering** .
4. Ladda ned en ny registrerings fil från portalen och ange den som indata för verktyget.
      ![registrera-konfiguration-Server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange information om proxyservern och klicka på knappen **Registrera** .  
6. Öppna ett admin PowerShell-kommando fönster.
7. Kör följande kommando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Om du har flera processerver måste du [Registrera om dem](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en konfigurations server med ett annat valv

> [!WARNING]
> I följande steg kopplas konfigurations servern bort från det aktuella valvet, och replikeringen av alla skyddade virtuella datorer under konfigurations servern stoppas.

1. Logga in på konfigurations servern
2. Kör kommandot från en administratörs kommando tolk:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starta cspsconfigtool.exe med hjälp av genvägen på Skriv bordet.
4. Klicka på fliken **valv registrering** .
5. Ladda ned en ny registrerings fil från portalen och ange den som indata för verktyget.
6. Ange information om proxyservern och klicka på knappen **Registrera** .  
7. Öppna ett admin PowerShell-kommando fönster.
8. Kör följande kommando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uppgradera en konfigurations Server

Du kör samlade uppdateringar för att uppdatera konfigurations servern. Uppdateringar kan tillämpas för upp till N-4-versioner. Exempel:

- Om du kör 9,7, 9,8, 9,9 eller 9,10 – kan du uppgradera direkt till 9,11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9,11 måste du först uppgradera till version 9,7. före 9,11.

Länkar till samlade uppdateringar för uppgradering till alla versioner av konfigurations servern finns på [sidan med wiki-uppdateringar](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern på följande sätt:

1. Hämta uppdaterings installations filen till konfigurations servern.
2. Dubbelklicka för att köra installations programmet.
3. Installations programmet identifierar den aktuella versionen som körs på datorn.
4. Bekräfta genom att klicka på **OK** och kör uppgraderingen. 


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurations Server

> [!WARNING]
> Kontrol lera att du har följande innan du börjar inaktivera konfigurations servern.
> 1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under den här konfigurations servern.
> 2. Ta bort [associationen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) och [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla principer för replikering från konfigurations servern.
> 3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenter-servrar/vSphere-värdar som är associerade med konfigurations servern.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Ta bort konfigurations servern från Azure Portal
1. I Azure Portal bläddrar du till **Site Recovery infrastruktur**  >  **konfigurations servrar** från menyn valv.
2. Klicka på den konfigurations server som du vill inaktivera.
3. Klicka på knappen **ta bort** på sidan information om konfigurations servern.
4. Bekräfta borttagningen av servern genom att klicka på **Ja** .

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Avinstallera konfigurations servern och dess beroenden
> [!TIP]
>   Om du planerar att återanvända konfigurations servern med Azure Site Recovery igen kan du gå vidare till steg 4 direkt

1. Logga in på konfigurations servern som administratör.
2. Öppna kontroll panelen > program > avinstallera program
3. Avinstallera programmen i följande ordning:
   * Microsoft Azure Recovery Services agent
   * Microsoft Azure Site Recovery mobilitets tjänsten/huvud mål servern
   * Microsoft Azure Site Recovery Provider
   * Microsoft Azure Site Recovery konfigurations Server/Processerver
   * Microsoft Azure beroenden för Site Recovery konfigurations servern
   * MySQL Server 5,5
4. Kör följande kommando från och administratörs kommando tolken.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Ta bort eller avregistrera en konfigurations Server (PowerShell)

1. [Installera](/powershell/azure/install-Az-ps) Azure PowerShell modul
2. Logga in på Azure-kontot med hjälp av kommandot
    
    `Connect-AzAccount`
3. Välj den prenumeration som valvet finns under

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Nu har du konfigurerat din valv kontext
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Hämta Välj konfigurations Server

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurations servern

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Alternativet **-Force** i Remove-AzSiteRecoveryFabric kan användas för att tvinga borttagning/borttagning av konfigurations servern.

## <a name="renew-tlsssl-certificates"></a>Förnya TLS/SSL-certifikat
Konfigurations servern har en inbyggd webb server som dirigerar aktiviteter för mobilitets tjänsten, processervern och huvud mål servrarna som är anslutna till den. Webb servern använder ett TLS/SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrol lera förfallo datum

För distributioner av Configuration server före maj 2016 har certifikatet förfallo datum angetts till ett år. Om du har ett certifikat som ska upphöra att gälla inträffar följande:

- När förfallo datumet är två månader eller mindre börjar tjänsten skicka meddelanden i portalen och via e-post (om du prenumererar på Azure Site Recovery meddelanden).
- En aviserings banderoll visas på valv resurs sidan. Klicka på banderollen för mer information.
- Om du ser knappen **Uppgradera nu** , betyder det att det finns vissa komponenter i din miljö som inte har uppgraderats till 9.4. xxxx. x eller senare versioner. Uppgradera komponenter innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna **Site Recovery infrastruktur**  >  **konfigurations Server** i valvet och klicka på konfigurations servern som krävs.
2. Förfallo datumet visas under **konfigurations serverns hälsa**
3. Klicka på **Förnya certifikat**. 




## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för att konfigurera haveri beredskap för [fysiska servrar](./physical-azure-disaster-recovery.md) till Azure.
