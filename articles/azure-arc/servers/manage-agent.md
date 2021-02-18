---
title: Hantera Azure Arc-aktiverade servrar-agenten
description: I den här artikeln beskrivs de olika hanterings aktiviteter som du vanligt vis utför under livs cykeln för Azure Arc-aktiverade servrar som är anslutna till dator agenten.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 36ae081f939cbf865db7755a2f766a7ccd87d619
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587622"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Hantera och underhålla den anslutna dator agenten

Efter den första distributionen av Azure Arc-aktiverade servrar som är anslutna till dator agent för Windows eller Linux kan du behöva konfigurera om agenten, uppgradera den eller ta bort den från datorn. Du kan enkelt hantera dessa rutin underhålls aktiviteter manuellt eller via Automation, vilket minskar både drift fel och kostnader.

## <a name="before-uninstalling-agent"></a>Innan du avinstallerar agenten

Innan du tar bort den anslutna dator agenten från din ARC-aktiverade Server bör du tänka på följande för att undvika oväntade problem eller kostnader som läggs till i Azure-fakturan:

* Om du har distribuerat tillägg för virtuella Azure-datorer till en aktive rad Server och du tar bort den anslutna dator agenten eller tar bort resursen som representerar den Arc-aktiverade servern i resurs gruppen, fortsätter dessa tillägg att köras och utföra sin normala åtgärd.

* Om du tar bort resursen som representerar den Arc-aktiverade servern i resurs gruppen, men du inte avinstallerar de virtuella dator tilläggen, kommer du inte att kunna hantera de installerade VM-tilläggen när du registrerar datorn på nytt.

För servrar eller datorer som du inte längre vill hantera med Azure Arc-aktiverade servrar, är det nödvändigt att följa de här stegen för att kunna sluta hantera den:

1. Ta bort VM-tilläggen från datorn eller servern. Du följer anvisningarna nedan.

2. Koppla bort datorn från Azure-bågen med någon av följande metoder:

    * Kör `azcmagent disconnect` kommando på datorn eller servern.

    * Från den valda registrerade Arc-aktiverad servern i Azure Portal genom att välja **ta bort** från översta raden.

    * Använda [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) eller [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource). För `ResourceType` parametern använder `Microsoft.HybridCompute/machines` .

3. [Avinstallera agenten](#remove-the-agent) från datorn eller servern genom att följa stegen nedan.

## <a name="renaming-a-machine"></a>Byta namn på en dator

När du ändrar namnet på Linux-eller Windows-datorn som är ansluten till Azure Arc-aktiverade servrar, identifieras inte det nya namnet automatiskt eftersom resurs namnet i Azure inte kan ändras. Precis som med andra Azure-resurser måste du ta bort resursen och återskapa den för att kunna använda det nya namnet.

För Arc-aktiverade servrar måste du ta bort de virtuella dator tilläggen innan du byter namn på datorn innan du fortsätter.

> [!NOTE]
> När de installerade tilläggen fortsätter att köras och utföra sin normala åtgärd när proceduren har slutförts kan du inte hantera dem. Om du försöker distribuera om tilläggen på datorn kan det uppstå oförutsägbara beteenden.

> [!WARNING]
> Vi rekommenderar att du undviker att byta namn på datorns dator namn och bara utföra den här proceduren om det är absolut nödvändigt.

1. Granska de VM-tillägg som är installerade på datorn och anteckna konfigurationen, med hjälp av [Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) eller med [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed).

2. Ta bort VM-tillägg som är installerade från [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension), med hjälp av [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)eller med [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

3. Använd **azcmagent** -verktyget med från [kopplings](manage-agent.md#disconnect) parametern för att koppla bort datorn från Azure-bågen och ta bort dator resursen från Azure. Om du kopplar från datorn från Arc-aktiverade servrar tas inte den anslutna dator agenten bort, och du behöver inte ta bort agenten som en del av den här processen. Du kan köra det här manuellt när du är inloggad interaktivt eller automatiserar användningen av samma huvud namn för tjänsten som du använde för att publicera flera agenter eller med [en åtkomsttoken för Microsoft Identity Platform.](../../active-directory/develop/access-tokens.md) Om du inte använde ett huvud namn för tjänsten för att registrera datorn med Azure Arc-aktiverade servrar, kan du läsa följande [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) för att skapa ett huvud namn för tjänsten.

4. Byt namn på datorernas dator namn.

5. Registrera den anslutna dator agenten på nytt med ARC-aktiverade servrar. Kör `azcmagent` verktyget med [Connect](manage-agent.md#connect) -parametern slutför det här steget.

6. Distribuera om de VM-tillägg som ursprungligen distribuerades till datorn från Arc-aktiverade servrar. Om du har distribuerat Azure Monitor for VMs-agenten (insikter) eller Log Analytics agent med hjälp av en Azure-princip distribueras agenterna om efter nästa [utvärderings cykel](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="upgrading-agent"></a>Uppgraderar agent

Den Azure-anslutna dator agenten uppdateras regelbundet för att åtgärda fel korrigeringar, stabilitets förbättringar och nya funktioner. [Azure Advisor](../../advisor/advisor-overview.md) identifierar resurser som inte använder den senaste versionen av dator agenten och rekommenderar att du uppgraderar till den senaste versionen. Det meddelar dig när du väljer den Arc-aktiverade servern genom att presentera en banderoll på sidan **Översikt** eller när du använder Advisor via Azure Portal.

Azure Connected Machine agent för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina behov.

I följande tabell beskrivs de metoder som stöds för att utföra agent uppgraderingen.

| Operativsystem | Uppgraderingsmetod |
|------------------|----------------|
| Windows | Manuellt<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows-agent

Uppdaterings paketet för den anslutna dator agenten för Windows är tillgängligt från:

* Microsoft Update

* [Microsoft Update katalog](https://www.catalog.update.microsoft.com/Home.aspx)

* [Windows agent Windows Installer-paketet](https://aka.ms/AzureConnectedMachineAgent) från Microsoft Download Center.

Agenten kan uppgraderas efter olika metoder för att stödja hanteringen av program uppdaterings processen. Utanför hämtningen från Microsoft Update kan du ladda ned och köra manuellt från kommando tolken, från ett skript eller någon annan Automation-lösning eller från användar gränssnitts guiden genom att köra `AzureConnectedMachine.msi` .

> [!NOTE]
> * Du måste ha *Administratörs* behörighet för att kunna uppgradera agenten.
> * Om du vill uppgradera manuellt måste du först hämta och kopiera installations paketet till en mapp på mål servern eller från en delad nätverksmapp. 

Om du inte känner till kommando rads alternativen för Windows Installer-paket kan du läsa mer om kommando rads alternativ i [msiexec](/windows/win32/msi/standard-installer-command-line-options) och [kommando rads alternativ i msiexec](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Uppgradera med installations guiden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör **AzureConnectedMachineAgent.msi** för att starta installations guiden.

Installations guiden identifierar om en tidigare version finns och utför sedan automatiskt en uppgradering av agenten. När uppgraderingen är klar stängs installations guiden automatiskt.

#### <a name="to-upgrade-from-the-command-line"></a>Uppgradera från kommando raden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando för att uppgradera agenten tyst och skapa en installations logg fil i `C:\Support\Logs` mappen.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-Agent

Om du vill uppdatera agenten på en Linux-dator till den senaste versionen, omfattar den två kommandon. Ett kommando för att uppdatera det lokala paket indexet med listan över senaste tillgängliga paket från databaserna och ett kommando för att uppgradera det lokala paketet.

Du kan hämta det senaste agent paketet från Microsofts [paket lagrings plats](https://packages.microsoft.com/).

> [!NOTE]
> Om du vill uppgradera agenten måste du ha behörighet som *rot* åtkomst eller med ett konto som har utökade rättigheter med sudo.

#### <a name="upgrade-ubuntu"></a>Uppgradera Ubuntu

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    apt update
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    apt upgrade
    ```

Åtgärder för kommandot [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , till exempel installation och borttagning av paket, loggas i `/var/log/dpkg.log` logg filen.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uppgradera Red Hat/CentOS/Amazon Linux

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    yum check-update
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    yum update
    ```

Åtgärder för kommandot [yum](https://access.redhat.com/articles/yum-cheat-sheet) , till exempel installation och borttagning av paket, loggas i `/var/log/yum.log` logg filen. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uppgradera SUSE Linux Enterprise

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    zypper refresh
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    zypper update
    ```

Åtgärder för kommandot [zypper](https://en.opensuse.org/Portal:Zypper) , till exempel installation och borttagning av paket, loggas i `/var/log/zypper.log` logg filen.

## <a name="about-the-azcmagent-tool"></a>Om verktyget Azcmagent

Azcmagent-verktyget (Azcmagent.exe) används för att konfigurera Azure Arc-aktiverade servrar som är anslutna till dator agent under installationen, eller ändra den inledande konfigurationen för agenten efter installationen. Azcmagent.exe tillhandahåller kommando rads parametrar för att anpassa agenten och visa dess status:

* **Anslut** – för att ansluta datorn till Azure-bågen

* **Koppla** från-koppla bort datorn från Azure-bågen

* **Visa** agent status och dess konfigurations egenskaper (resurs gruppens namn, PRENUMERATIONS-ID, version osv.), som kan hjälpa dig att felsöka ett problem med agenten. Inkludera `-j` parametern för att generera resultaten i JSON-format.

* **Loggar** – skapar en zip-fil i den aktuella katalogen som innehåller loggar för att hjälpa dig vid fel sökning.

* **Version** – visar den anslutna dator agent versionen.

* **-h eller--help** – visar tillgängliga kommando rads parametrar

    Om du till exempel vill visa detaljerad hjälp för parametern **Connect** skriver du `azcmagent connect -h` . 

* **-v eller--utförlig** -Aktivera utförlig loggning

Du kan utföra en **anslutning** och koppla **från** manuellt när du är inloggad interaktivt eller automatisera med samma tjänst huvud namn som du använde för att publicera flera agenter eller med [en åtkomsttoken för Microsoft Identity Platform.](../../active-directory/develop/access-tokens.md) Om du inte använde ett huvud namn för tjänsten för att registrera datorn med Azure Arc-aktiverade servrar, kan du läsa följande [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) för att skapa ett huvud namn för tjänsten.

>[!NOTE]
>Du måste ha *rot* åtkomst behörighet på Linux-datorer för att kunna köra **azcmagent**.

### <a name="connect"></a>Ansluta

Den här parametern anger en resurs i Azure Resource Manager som representerar datorn som skapas i Azure. Resursen är i den angivna prenumerations-och resurs gruppen och data om datorn lagras i den Azure-region som anges av `--location` inställningen. Standard resurs namnet är datorns värdnamn om inget anges.

Ett certifikat som motsvarar systemets tilldelade identitet för datorn laddas ned och lagras lokalt. När det här steget har slutförts börjar den Azure Connected Machine-Metadata Service och gäst konfigurations agenten att synkronisera med Azure Arc-aktiverade servrar.

Om du vill ansluta med ett huvud namn för tjänsten kör du följande kommando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Kör följande kommando för att ansluta med hjälp av en åtkomsttoken:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Kör följande kommando för att ansluta till dina förhöjda inloggade autentiseringsuppgifter (interaktiva):

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Koppla från

Den här parametern anger en resurs i Azure Resource Manager som representerar datorn som tas bort i Azure. Agenten tas inte bort från datorn. du avinstallerar agenten separat. När datorn har kopplats från, om du vill omregistrera den med Azure Arc-aktiverade servrar, använder du `azcmagent connect` så att en ny resurs skapas för den i Azure.

> [!NOTE]
> Om du har distribuerat ett eller flera Azure VM-tillägg till din ARC-aktiverade Server och du tar bort dess registrering i Azure installeras tilläggen fortfarande. Det är viktigt att förstå att beroende på vilket tillägg som installeras, är det aktivt att utföra sin funktion. Datorer som är avsedda att dras tillbaka eller som inte längre hanteras av Arc-aktiverade servrar bör först ta bort tilläggen innan de tar bort registreringen från Azure.

Kör följande kommando för att koppla från med ett huvud namn för tjänsten:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Kör följande kommando för att koppla från med hjälp av en åtkomsttoken:

`azcmagent disconnect --access-token <accessToken>`

Kör följande kommando för att koppla bort med dina förhöjda inloggade autentiseringsuppgifter (interaktiva):

`azcmagent disconnect`

## <a name="remove-the-agent"></a>Ta bort agenten

Utför någon av följande metoder för att avinstallera den Windows-eller Linux-anslutna dator agenten från datorn. Att ta bort agenten avregistrerar inte datorn med ARC-aktiverade servrar eller tar bort de virtuella Azure-tilläggen installerade. Avregistrera datorn och ta bort de installerade VM-tilläggen separat när du inte längre behöver hantera datorn i Azure och de här stegen bör utföras innan du avinstallerar agenten.

### <a name="windows-agent"></a>Windows-agent

Följande metoder tar bort agenten, men tar inte bort mappen *C:\Program Files\AzureConnectedMachineAgent* på datorn.

#### <a name="uninstall-from-control-panel"></a>Avinstallera från kontroll panelen

1. Om du vill avinstallera Windows-agenten från datorn gör du följande:

    a. Logga in på datorn med ett konto som har administratörs behörighet.  
    b. Välj **program och funktioner** på **kontroll panelen**.  
    c. I **program och funktioner** väljer du **Azure Connected Machine agent**, väljer **Avinstallera** och väljer sedan **Ja**.  

    >[!NOTE]
    > Du kan också köra installations guiden för agenten genom att dubbelklicka på installations paketet för **AzureConnectedMachineAgent.msi** .

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommando raden

Om du vill avinstallera agenten manuellt från kommando tolken eller använda en automatiserad metod, till exempel ett skript, kan du använda följande exempel. Först måste du hämta produkt koden, som är en GUID som är huvud-ID för programpaketet, från operativ systemet. Avinstallationen utförs med hjälp av Msiexec.exe kommando rad- `msiexec /x {Product Code}` .

1. Öppna Registereditorn.

2. `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`Leta upp och kopiera produkt kod-GUID under register nyckel.

3. Du kan sedan avinstallera agenten med hjälp av Msiexec med hjälp av följande exempel:

   * Från kommando rads typ:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Du kan utföra samma steg med PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-Agent

> [!NOTE]
> Om du vill avinstallera agenten måste du ha behörighet som *rot* åtkomst eller med ett konto som har utökade rättigheter med sudo.

Om du vill avinstallera Linux-agenten är kommandot som ska användas beroende av operativ systemet Linux.

- Kör följande kommando för Ubuntu:

    ```bash
    sudo apt purge azcmagent
    ```

- Kör följande kommando för RHEL, CentOS och Amazon Linux:

    ```bash
    sudo yum remove azcmagent
    ```

- Kör följande kommando för SLES:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Avregistrera datorn

Om du planerar att sluta hantera datorn med stöd tjänster i Azure utför du följande steg för att avregistrera datorn med ARC-aktiverade servrar. Du kan utföra de här stegen antingen innan eller efter att du har tagit bort den anslutna dator agenten från datorn.

1. Öppna Azure Arc-aktiverade servrar genom att gå till [Azure Portal](https://aka.ms/hybridmachineportal).

2. Välj datorn i listan, Välj ellipsen (..**.**) och välj sedan **ta bort**.

## <a name="update-or-remove-proxy-settings"></a>Uppdatera eller ta bort proxyinställningar

Om du vill konfigurera agenten för att kommunicera med tjänsten via en proxyserver eller ta bort den här konfigurationen efter distributionen, eller Använd någon av följande metoder för att slutföra uppgiften.

> [!NOTE]
> Arc-aktiverade servrar stöder inte användning av en [Log Analytics Gateway](../../azure-monitor/agents/gateway.md) som proxy för den anslutna dator agenten.
>

### <a name="windows"></a>Windows

Kör följande kommando för att ange miljövariabeln för proxyservern:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Om du vill konfigurera agenten att sluta kommunicera via en proxyserver kör du följande kommando för att ta bort miljövariabeln proxyserver och startar om Agent tjänsten:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Ange proxyservern genom att köra följande kommando från den katalog som du laddade ned agent installations paketet till:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Om du vill konfigurera agenten att sluta kommunicera via en proxyserver kör du följande kommando för att ta bort proxykonfigurationen:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md)och mycket mer.

* Läs mer om den [Log Analytics agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill samla in operativ system och data för övervakning av arbets belastning, hantera dem med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).