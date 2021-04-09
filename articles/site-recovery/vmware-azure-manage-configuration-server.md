---
title: Hantera konfigurations servern för haveri beredskap med Azure Site Recovery
description: Lär dig mer om vanliga aktiviteter för att hantera en lokal konfigurations Server för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
ms.author: ramamill
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 67f25c97e0b4433ed09cb8c6337afe671424ba0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048552"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Hantera konfigurationsservern för haveriberedskap för virtuella VMWare-datorer/fysiska servrar

Du konfigurerar en lokal konfigurations server när du använder [Azure Site Recovery](site-recovery-overview.md) för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure. Konfigurations servern samordnar kommunikationen mellan lokala VMware och Azure och hanterar datareplikering. I den här artikeln sammanfattas vanliga aktiviteter för att hantera konfigurations servern när den har distribuerats.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Uppdatera Windows-licens

Den licens som tillhandahölls med OVF-mallen är en utvärderings licens som är giltig i 180 dagar. Om du vill ha en oavbruten användning måste du aktivera Windows med en tillskaffad licens. Licens uppdatering kan göras antingen via en fristående nyckel eller en KMS-standardnyckel. Vägledning är tillgängligt på [kommando raden för DISM-Windows för att köra OS](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Information om hur du hämtar nycklar finns i [Konfigurera KMS-klienter](/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Åtkomst till konfigurations Server

Du kan komma åt konfigurations servern på följande sätt:

* Logga in på den virtuella datorn som den har distribuerats till och starta **Azure Site Recovery Configuration Manager** från genvägen till Skriv bordet.
* Alternativt kan du fjärrans luta till konfigurations servern från https://*ConfigurationServerName*/: 44315/. Logga in med administratörs behörighet.

## <a name="modify-vmware-server-settings"></a>Ändra inställningar för VMware-Server

1. Om du vill associera en annan VMware-Server med konfigurations servern väljer du **Lägg till vCenter Server/vSphere ESXi-Server** efter [inloggning](#access-configuration-server).
2. Ange informationen och välj sedan **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Ändra autentiseringsuppgifter för automatisk identifiering

1. Om du vill uppdatera de autentiseringsuppgifter som används för att ansluta till VMware-servern för automatisk identifiering av virtuella VMware-datorer efter [inloggning](#access-configuration-server)väljer du kontot och klickar på **Redigera**.
2. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Du kan också ändra autentiseringsuppgifterna genom att CSPSConfigtool.exe.

1. Logga in på konfigurations servern och starta CSPSConfigtool.exe
2. Välj det konto som du vill ändra och klicka på **Redigera**.
3. Ange de ändrade autentiseringsuppgifterna och klicka på **OK**

## <a name="modify-credentials-for-mobility-service-installation"></a>Ändra autentiseringsuppgifter för mobilitets tjänst installationen

Ändra de autentiseringsuppgifter som används för att automatiskt installera mobilitets tjänsten på de virtuella VMware-datorer som du aktiverar för replikering.

1. Efter [inloggning](#access-configuration-server)väljer du **Hantera autentiseringsuppgifter för virtuell dator**
2. Välj det konto som du vill ändra och klicka på **Redigera**
3. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra autentiseringsuppgifter för mobilitets tjänsten](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Du kan också ändra autentiseringsuppgifterna genom att CSPSConfigtool.exe.

1. Logga in på konfigurations servern och starta CSPSConfigtool.exe
2. Välj det konto som du vill ändra och klicka på **Redigera**
3. Ange de nya autentiseringsuppgifterna och klicka på **OK**.

## <a name="add-credentials-for-mobility-service-installation"></a>Lägg till autentiseringsuppgifter för mobilitets tjänst installationen

Om du missade att lägga till autentiseringsuppgifter under OVF-distributionen av konfigurations servern,

1. När du har [loggat in](#access-configuration-server)väljer du **Hantera autentiseringsuppgifter för virtuella datorer**.
2. Klicka på **Lägg till autentiseringsuppgifter för virtuell dator**.
    ![Skärm bild som visar fönstret Hantera autentiseringsuppgifter för virtuell dator med länken Lägg till autentiseringsuppgifter för virtuell dator.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Ange de nya autentiseringsuppgifterna och klicka på **Lägg till**.

Du kan också lägga till autentiseringsuppgifter via CSPSConfigtool.exe.

1. Logga in på konfigurations servern och starta CSPSConfigtool.exe
2. Klicka på **Lägg till**, ange de nya autentiseringsuppgifterna och klicka på **OK**.

## <a name="modify-proxy-settings"></a>Ändra proxyinställningar

Ändra proxyinställningarna som används av Configuration Server-datorn för Internet åtkomst till Azure. Om du har en process Server-dator utöver standard processervern som körs på konfigurations serverns dator ändrar du inställningarna på båda datorerna.

1. När du har [loggat in](#access-configuration-server) på konfigurations servern väljer du **Hantera anslutning**.
2. Uppdatera proxy-värdena. Välj sedan **Spara** för att uppdatera inställningarna.

## <a name="add-a-network-adapter"></a>Lägg till ett nätverkskort

Mallen Open Virtualization Format (OVF) distribuerar den virtuella datorns konfigurations server med ett enda nätverkskort.

- Du kan [lägga till ytterligare ett nätverkskort i den virtuella datorn](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), men du måste lägga till det innan du registrerar konfigurations servern i valvet.
- Om du vill lägga till ett kort när du har registrerat konfigurations servern i valvet lägger du till kortet i VM-egenskaperna. Sedan måste du registrera servern i valvet [igen](#reregister-a-configuration-server-in-the-same-vault) .

## <a name="how-to-renew-ssl-certificates"></a>Förnya SSL-certifikat

Konfigurations servern har en inbyggd webb server som dirigerar aktiviteter för mobilitets agenter på alla skyddade datorer, inbygga/skalbara process servrar och huvud mål servrar som är anslutna till den. Webb servern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrol lera förfallo datum

Förfallo datumet visas under **konfigurations serverns hälso tillstånd**. För distributioner av Configuration server före maj 2016 har certifikatet förfallo datum angetts till ett år. Om du har ett certifikat som ska upphöra att gälla inträffar följande:

- När förfallo datumet är två månader eller mindre börjar tjänsten skicka meddelanden i portalen och via e-post (om du prenumererar på Site Recovery meddelanden).
- En aviserings banderoll visas på valv resurs sidan. Välj banderollen om du vill ha mer information.
- Om du ser knappen **Uppgradera nu** , betyder det att vissa komponenter i din miljö inte har uppgraderats till 9.4. xxxx. x eller senare versioner. Uppgradera komponenterna innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="if-certificates-are-yet-to-expire"></a>Om certifikat ännu inte har gått ut

1. Du förnyar genom att öppna **Site Recovery infrastruktur**  >  **konfigurations Server** i valvet. Välj den konfigurations server som krävs.
2. Se till att alla komponenters skalbara process servrar, huvud mål servrar och mobilitets agenter på alla skyddade datorer finns på de senaste versionerna och är i anslutet tillstånd.
3. Välj nu **Förnya certifikat**.
4. Noggrant följa instruktionerna på den här sidan och klicka på OK för att förnya certifikat på vald konfigurations Server och dess associerade komponenter.

### <a name="if-certificates-have-already-expired"></a>Om certifikat redan har gått ut

1. Efter förfallo datum kan certifikat **inte förnyas från Azure Portal**. Innan du fortsätter bör du kontrol lera att alla komponenters skalbara process servrar, huvud mål servrar och mobilitets agenter på alla skyddade datorer finns på de senaste versionerna och är i anslutet tillstånd.
2. **Följ bara den här proceduren om certifikat redan har gått ut.** Logga in på konfigurations servern, gå till C enhet > program data > Site Recovery > Home > svsystems > bin och kör utförar-verktyget "RenewCerts" som administratör.
3. Ett fönster med PowerShell-körning är pop-up och utlöser förnyelse av certifikat. Det här kan ta upp till 15 minuter. Stäng inte fönstret förrän förnyelsen har slutförts.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Omregistrera en konfigurations server i samma valv

Du kan registrera om konfigurations servern i samma valv om du behöver. Om du har en ytterligare process Server-dator kan du, förutom standard processervern som körs på konfigurations servern, registrera om båda datorerna.


1. Öppna **Hantera**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar** i valvet.
2. I **servrar** väljer du **Ladda ned registrerings nyckel** för att ladda ned valv filen med autentiseringsuppgifter.
3. Logga in på Configuration Server-datorn.
4. Öppna **cspsconfigtool.exe** i **%programdata%\ASR\home\svsystems\bin**.
5. På fliken **valv registrering** väljer du **Bläddra** och letar reda på filen med valvets autentiseringsuppgifter som du laddade ned.
6. Ange information om proxyservern om det behövs. Välj **Registrera**.
7. Öppna kommando fönstret admin PowerShell och kör följande kommando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >För att **Hämta de senaste certifikaten** från konfigurations servern till den skalbara processervern kör du *kommandot \<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> ""--registermt*

8. Slutligen startar du om obengine genom att köra följande kommando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en konfigurations server med ett annat valv

> [!WARNING]
> I följande steg kopplas konfigurations servern bort från det aktuella valvet, och replikeringen av alla skyddade virtuella datorer under konfigurations servern stoppas.

1. Logga in på konfigurations servern.
2. Öppna kommando fönstret admin PowerShell och kör följande kommando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Starta webb portalen för konfigurations serverns webbläsare med hjälp av genvägen på Skriv bordet.
4. Utför registrerings stegen som liknar en ny konfigurations Server [registrering](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurations servern

Du kör samlade uppdateringar för att uppdatera konfigurations servern. Uppdateringar kan tillämpas för upp till N-4-versioner. Exempel:

- Om du kör 9,7, 9,8, 9,9 eller 9,10 kan du uppgradera direkt till 9,11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9,11 måste du först uppgradera till version 9,7. före 9,11.

Detaljerad vägledning om support policyn för Azure Site Recovery-komponenter hittar du [här](./service-updates-how-to.md#support-statement-for-azure-site-recovery).
Länkar till samlade uppdateringar för uppgradering till alla versioner av konfigurations servern finns [här](./service-updates-how-to.md#links-to-currently-supported-update-rollups).

> [!IMPORTANT]
> Med varje ny version av en Azure Site Recovery komponent som släpps anses alla versioner under "N-4" vara understödjande. Vi rekommenderar alltid att du uppgraderar till de senaste versionerna som är tillgängliga.</br>
> Detaljerad vägledning om support policyn för Azure Site Recovery-komponenter hittar du [här](./service-updates-how-to.md#support-statement-for-azure-site-recovery).

Uppgradera servern på följande sätt:

1. I valvet går du till **Hantera**  >  **Site Recovery infrastruktur**  >  **konfigurations servrar**.
2. Om det finns en uppdatering visas en länk i kolumnen **agent Version** >.
    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update2.png)
3. Hämta uppdaterings installations filen till konfigurations servern.

    ![Skärm bild som visar var du kan klicka för att ladda ned uppdaterings installations filen.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbelklicka för att köra installations programmet.
5. Installations programmet identifierar den aktuella versionen som körs på datorn. Klicka på **Ja** för att starta uppgraderingen.
6. När uppgraderingen slutförs verifierar du Server konfigurationen.

    ![Skärm bild som visar den slutförda Server validerings konfigurationen.](./media/vmware-azure-manage-configuration-server/update3.png)

7. Stäng installations programmet genom att klicka på **Slutför** .
8. För att uppgradera resten av Site Recovery-komponenterna, se vår [uppgraderings vägledning](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Uppgradera konfigurations servern/processervern från kommando raden

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurations Server

1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under konfigurations servern.
2. Ta bort [associationen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) och [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla principer för replikering från konfigurations servern.
3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenter-servrar/vSphere-värdar som är associerade med konfigurations servern.
4. Öppna **Site Recovery infrastruktur**  >  **konfigurations servrar** i valvet.
5. Välj den konfigurations server som du vill ta bort. Välj sedan **ta bort** på sidan **information** .

    ![Ta bort konfigurations Server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Ta bort med PowerShell

Du kan också ta bort konfigurations servern med hjälp av PowerShell.

1. [Installera](/powershell/azure/install-Az-ps) Azure PowerShell-modulen.
2. Logga in på ditt Azure-konto med hjälp av det här kommandot:

    `Connect-AzAccount`
3. Välj valv prenumerationen.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ange valv kontexten.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta konfigurations servern.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurations servern.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Du kan använda alternativet **-Force** i Remove-AzSiteRecoveryFabric för Tvingad borttagning av konfigurations servern.

## <a name="generate-configuration-server-passphrase"></a>Generera lösen fras för konfigurations Server

1. Logga in på konfigurations servern och öppna sedan ett kommando tolks fönster som administratör.
2. Om du vill ändra katalogen till bin-mappen kör du kommandot **CD%programdata%\ASR\home\svsystems\bin**
3. Om du vill generera lösen Frass filen kör **genpassphrase.exe-v > MobSvc. lösen fras**.
4. Lösen frasen kommer att lagras i filen som finns på **%programdata%\ASR\home\svsystems\bin\MobSvc.Passphrase**.

## <a name="refresh-configuration-server"></a>Uppdatera konfigurations Server

1. I Azure Portal navigerar du till **Recovery Services valv**  >  **Hantera**  >  **Site Recovery infrastruktur**  >  **för VMware & fysiska datorer**  >  **konfigurations servrar**
2. Klicka på den konfigurations server som du vill uppdatera.
3. På bladet med information om vald konfigurations Server klickar du på **mer**  >  **uppdaterings Server**.
4. Övervaka jobb förloppet under **Recovery Services valv**  >  **övervakning**  >  **Site Recovery jobb**.

## <a name="failback-requirements"></a>Krav för återställning efter fel

Vid skydd och återställning efter fel måste den lokala konfigurations servern köras och vara i anslutet tillstånd. För lyckad återställning efter fel måste den virtuella datorn som återställs finnas i konfigurations Server databasen.

Se till att du utför regelbundna schemalagda säkerhets kopieringar av konfigurations servern. Om en katastrof inträffar och konfigurations servern tappas bort, måste du först återställa konfigurations servern från en säkerhets kopia och kontrol lera att den återställda konfigurations servern har samma IP-adress som den har registrerats för valvet. Återställning efter fel fungerar inte om en annan IP-adress används för den återställda konfigurations servern.

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för att konfigurera haveri beredskap för [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
