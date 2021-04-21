---
title: Översikt över Connected Machine-agenten
description: Den här artikeln innehåller en detaljerad översikt över Azure Arc-aktiverade serveragenten, som stöder övervakning av virtuella datorer som finns i hybridmiljöer.
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd58997a8af86a3789895bfb29bfd5803826fa6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832967"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Översikt över Azure Arc-aktiverad serveragent

Med Azure Arc-aktiverade servrar med Connected Machine-agenten kan du hantera dina Windows- och Linux-datorer som finns utanför Azure i företagsnätverket eller någon annan molnleverantör. Den här artikeln innehåller en detaljerad översikt över agenten, system- och nätverkskraven och de olika distributionsmetoderna.

>[!NOTE]
>Från och med den allmänna versionen av Azure Arc-aktiverade servrar i september 2020 kommer alla förhandsversioner av Azure Connected Machine-agenten (agenter  med versioner som är mindre än 1.0) att bli inaktuella senast den 2 februari **2021.**  Med den här tidsramen kan du uppgradera till version 1.0 eller senare innan de tidigare utgivna agenterna inte längre kan kommunicera med Azure Arc-aktiverade servertjänsten.

## <a name="agent-component-details"></a>Information om agentkomponenten

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Översikt över Arc-aktiverad serveragent." border="false":::

Paketet Azure Connected Machine agent innehåller flera logiska komponenter som paketeras tillsammans.

* Hybrid Instance Metadata Service (HIMDS) hanterar anslutningen till Azure och den anslutna datorns Azure-identitet.

* Gästkonfigurationsagenten tillhandahåller In-Guest-princip och gästkonfigurationsfunktioner, till exempel att utvärdera om datorn uppfyller nödvändiga principer.

    Observera följande beteende med Azure Policy [gästkonfiguration för](../../governance/policy/concepts/guest-configuration.md) en frånkopplad dator:

    * En principtilldelning för gästkonfiguration som riktar sig mot frånkopplade datorer påverkas inte.
    * Gästtilldelningen lagras lokalt i 14 dagar. Om Connected Machine-agenten återansluter till tjänsten inom 14-dagarsperioden tillämpas principtilldelningarna på nytt.
    * Tilldelningar tas bort efter 14 dagar och omtilldelades inte till datorn efter 14-dagarsperioden.

* Tilläggsagenten hanterar VM-tillägg, inklusive installation, avinstallation och uppgradering. Tillägg laddas ned från Azure och kopieras till `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` mappen i Windows och för Linux till `/opt/GC_Ext/downloads` . I Windows installeras tillägget på följande `%SystemDrive%\Packages\Plugins\<extension>` sökväg, och i Linux installeras tillägget till `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Instansmetadata

Metadatainformation om den anslutna datorn samlas in när Connected Machine-agenten har registrerats på Arc-aktiverade servrar. Specifikt:

* Namn, typ och version för operativsystemet
* Datornamn
* Datorns fullständiga domännamn (FQDN)
* Agentversion för ansluten dator
* Fullständigt domännamn (FQDN) för Active Directory och DNS
* UUID (BIOS-ID)
* Pulsslag för ansluten datoragent
* Agentversion för ansluten dator
* Offentlig nyckel för hanterad identitet
* Status och information om principefterlevnad (om du Azure Policy gästkonfigurationsprinciper)

Följande metadatainformation begärs av agenten från Azure:

* Resursplats (region)
* ID för virtuell dator
* Taggar
* Azure Active Directory certifikat för hanterad identitet
* Principtilldelningar för gästkonfiguration
* Tilläggsbegäranden – installera, uppdatera och ta bort.

## <a name="download-agents"></a>Ladda ned agenter

Du kan ladda ned Azure Connected Machine agent-paketet för Windows och Linux från de platser som anges nedan.

* [Windows-Windows Installer från](https://aka.ms/AzureConnectedMachineAgent) Microsoft Download Center.

* Linux-agentpaketet distribueras från Microsofts [paketdatabas med](https://packages.microsoft.com/) det föredragna paketformatet för distributionen (. RPM eller . DEB).

Den Azure Connected Machine agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina krav. Mer information finns [här.](manage-agent.md)

## <a name="prerequisites"></a>Förutsättningar

### <a name="supported-environments"></a>Miljöer som stöds

Arc-aktiverade servrar stöder installation av connected machine-agenten på alla fysiska servrar och virtuella datorer *som* finns utanför Azure. Detta omfattar virtuella datorer som körs på plattformar som VMware, Azure Stack HCI och andra molnmiljöer. Arc-aktiverade servrar stöder inte installation av agenten på virtuella datorer som körs i Azure, eller virtuella datorer som körs på Azure Stack Hub eller Azure Stack Edge eftersom de redan är modellerade som virtuella Azure-datorer.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande versioner av Windows- och Linux-operativsystemet stöds officiellt för Azure Connected Machine agenten:

- Windows Server 2008 R2, Windows Server 2012 R2 och senare (inklusive Server Core)
- Ubuntu 16.04 och 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Linux-värdnamnet eller Windows-datornamnet kan inte använda något av de reserverade orden eller varumärkena i namnet, annars misslyckas försök att registrera den anslutna datorn med Azure. En [lista över reserverade](../../azure-resource-manager/templates/error-reserved-resource-name.md) ord finns i Lösa fel med reserverade resursnamn.

### <a name="required-permissions"></a>Behörigheter som krävs

* Om du vill publicera datorer är du medlem **i Azure Connected Machine onboarding-** [eller deltagarrollen](../../role-based-access-control/built-in-roles.md#contributor) i resursgruppen.

* Om du vill läsa, ändra och ta bort en dator är du **medlem Azure Connected Machine rollen Resursadministratör** i resursgruppen.

* Om du vill välja en resursgrupp i  listrutan när du använder metoden Generera [](../../role-based-access-control/built-in-roles.md#reader) skript är du åtminstone medlem i rollen Läsare för den resursgruppen.

### <a name="azure-subscription-and-service-limits"></a>Azure-prenumeration och tjänstbegränsningar

Innan du konfigurerar dina datorer Azure Arc-aktiverade servrar [](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) bör [](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) du granska Azure Resource Manager-prenumerationsgränser och resursgruppsgränser för att planera för antalet datorer som ska anslutas.

Azure Arc-aktiverade servrar stöder upp till 5 000 datorinstanser i en resursgrupp.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1.2-protokoll

För att säkerställa säkerheten för data under överföring till Azure rekommenderar vi starkt att du konfigurerar datorn så att den använder Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och även om de fortfarande fungerar för att tillåta bakåtkompatibilitet rekommenderas **de inte**.

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner förlitar [sig ofta på OpenSSL](https://www.openssl.org) för stöd för TLS 1.2. | Kontrollera [OpenSSL-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows Server 2012 R2 och senare | Stöds och aktiveras som standard. | Bekräfta att du fortfarande använder [standardinställningarna](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Nätverkskonfiguration

Connected Machine-agenten för Linux och Windows kommunicerar utgående säkert till Azure Arc TCP-port 443. Om datorn ansluter via en brandvägg eller proxyserver för att kommunicera via Internet kan du läsa följande för att förstå kraven för nätverkskonfiguration.

> [!NOTE]
> Arc-aktiverade servrar stöder inte användning av [en Log Analytics-gateway](../../azure-monitor/agents/gateway.md) som proxy för Connected Machine-agenten.
>

Om den utgående anslutningen begränsas av brandväggen eller proxyservern kontrollerar du att URL:erna nedan inte blockeras. När du bara tillåter de IP-intervall eller domännamn som krävs för att agenten ska kunna kommunicera med tjänsten måste du tillåta åtkomst till följande tjänsttaggar och URL:er.

Tjänsttaggar:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Webbadresser:

| Agentresurs | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Gästkonfiguration|
|`*.his.arc.azure.com`|Hybrididentitetstjänst|
|`www.office.com`|Office 365|

Förhandsversionsagenter (version 0.11 och tidigare) kräver också åtkomst till följande URL:er:

| Agentresurs | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Gästkonfiguration|
|`*-agentservice-prod-1.azure-automation.net`|Gästkonfiguration|

En lista över IP-adresser för varje tjänsttagg/region finns i JSON-filen – [Azure IP-intervall och tjänsttaggar – offentligt moln.](https://www.microsoft.com/download/details.aspx?id=56519) Microsoft publicerar veckovisa uppdateringar som innehåller varje Azure-tjänst och de IP-intervall som används. Den här informationen i JSON-filen är den aktuella listan med tidpunkter för ip-intervallen som motsvarar varje tjänsttagg. IP-adresserna kan komma att ändras. Om IP-adressintervall krävs för brandväggskonfigurationen ska **AzureCloud-tjänsttaggen** användas för att tillåta åtkomst till alla Azure-tjänster. Inaktivera inte säkerhetsövervakning eller inspektion av dessa URL:er, tillåt dem på samma sätt som med annan Internettrafik.

Mer information finns i Översikt [över tjänsttaggar.](../../virtual-network/service-tags-overview.md)

### <a name="register-azure-resource-providers"></a>Registrera Azure-resursproviders

Azure Arc-aktiverade servrar är beroende av följande Azure-resursproviders i din prenumeration för att kunna använda den här tjänsten:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Om de inte är registrerade kan du registrera dem med följande kommandon:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Du kan också registrera resursproviders i Azure Portal genom att följa stegen under [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Installation och konfiguration

Du kan ansluta datorer i din hybridmiljö direkt med Azure med hjälp av olika metoder beroende på dina behov. I följande tabell visas varje metod för att avgöra vilken som fungerar bäst för din organisation.

> [!IMPORTANT]
> Connected Machine-agenten kan inte installeras på en virtuell Azure Windows-dator. Om du försöker göra det identifierar installationen detta och återställs.

| Metod | Beskrivning |
|--------|-------------|
| Interaktivt | Installera agenten manuellt på ett eller ett litet antal datorer genom att följa stegen i [Ansluta datorer från Azure Portal](onboard-portal.md).<br> Från Azure Portal kan du generera ett skript och köra det på datorn för att automatisera installations- och konfigurationsstegen för agenten.|
| I stor skala | Installera och konfigurera agenten för flera datorer efter Connect machines using a Service Principal (Anslut [datorer med tjänstens huvudnamn).](onboard-service-principal.md)<br> Den här metoden skapar ett huvudnamn för tjänsten för att ansluta datorer icke-interaktivt.|
| I stor skala | Installera och konfigurera agenten för flera datorer enligt metoden [Using Windows PowerShell DSC](onboard-dsc.md).<br> Den här metoden använder tjänstens huvudnamn för att ansluta datorer icke-interaktivt med PowerShell DSC. |

## <a name="connected-machine-agent-technical-overview"></a>Teknisk översikt över Connected Machine-agenten

### <a name="windows-agent-installation-details"></a>Installationsinformation för Windows-agenten

Connected Machine-agenten för Windows kan installeras med någon av följande tre metoder:

* Dubbelklicka på filen `AzureConnectedMachineAgent.msi` .
* Manuellt genom att köra `AzureConnectedMachineAgent.msi` Windows Installer-paketet från kommandogränssnittet.
* Från en PowerShell-session med hjälp av en skriptad metod.

När du har installerat Connected Machine-agenten för Windows tillämpas följande systemomfattande konfigurationsändringar.

* Följande installationsmappar skapas under installationen.

    |Mapp |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Standardinstallationssökväg som innehåller agentens supportfiler.|
    |%ProgramData%\AzureConnectedMachineAgent |Innehåller agentkonfigurationsfilerna.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Innehåller de köpta token.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Innehåller agentkonfigurationsfilen `agentconfig.json` som registrerar registreringsinformationen för tjänsten.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Installationssökväg som innehåller gästkonfigurationsagentfilerna. |
    |%ProgramData%\GuestConfig |Innehåller (tillämpade) principer från Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Tilläggen laddas ned från Azure och kopieras här.|

* Följande Windows-tjänster skapas på måldatorn under installationen av agenten.

    |Tjänstnamn |Visningsnamn |Processnamn |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Den här tjänsten implementerar Azure Instance Metadata Service (IMDS) för att hantera anslutningen till Azure och den anslutna datorns Azure-identitet.|
    |GCArcService |Arc-tjänst för gästkonfiguration |gc_service |Övervakar önskad tillståndskonfiguration för datorn.|
    |ExtensionService |Gästkonfigurationstilläggstjänst | gc_service |Installerar de tillägg som krävs för datorn.|

* Följande miljövariabler skapas under agentinstallationen.

    |Name |Standardvärde |Beskrivning |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Det finns flera tillgängliga loggfiler för felsökning. De beskrivs i följande tabell.

    |Loggas |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Innehåller information om agenttjänsten (HIMDS) och interaktion med Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Innehåller utdata från azcmagent tool-kommandon när det utförliga argumentet (-v) används.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Innehåller information om DSC-tjänstaktiviteten,<br> i synnerhet anslutningen mellan HIMDS-tjänsten och Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Innehåller information om DSC-tjänstens telemetri och utförlig loggning.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Innehåller information om tilläggsagentkomponenten.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|Registrerar information från det installerade tillägget.|

* De lokala programmen för **hybridagenttillägg för säkerhetsgruppen** skapas.

* Under avinstallationen av agenten tas inte följande artefakter bort.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent och underkataloger
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Installationsinformation för Linux-agenten

Connected Machine-agenten för Linux tillhandahålls i önskat paketformat för distributionen (. RPM eller . DEB) som finns på Microsoft-paketdatabasen . [](https://packages.microsoft.com/) Agenten installeras och konfigureras med shell-skriptpaketet [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

När du har installerat Connected Machine-agenten för Linux tillämpas följande konfigurationsändringar för hela systemet.

* Följande installationsmappar skapas under installationen.

    |Mapp |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |Standardinstallationssökväg som innehåller agentens supportfiler.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Installationssökväg som innehåller gästkonfigurationsagentfilerna.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Innehåller de köpta token.|
    |/var/lib/GuestConfig |Innehåller (tillämpade) principer från Azure.|
    |/opt/GC_Ext/downloads|Tillägg laddas ned från Azure och kopieras här.|

* Följande daemons skapas på måldatorn under installationen av agenten.

    |Tjänstnamn |Visningsnamn |Processnamn |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Service |himds |Den här tjänsten implementerar Tjänsten Azure Instance Metadata (IMDS) för att hantera anslutningen till Azure och den anslutna datorns Azure-identitet.|
    |gcad.servce |GC Arc-tjänsten |gc_linux_service |Övervakar datorns önskade tillståndskonfiguration. |
    |extd.service |Tilläggstjänst |gc_linux_service | Installerar de tillägg som krävs för datorn.|

* Det finns flera loggfiler för felsökning. De beskrivs i följande tabell.

    |Loggas |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Innehåller information om agenttjänsten (HIMDS) och interaktion med Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Innehåller utdata från azcmagent tool-kommandon när det utförliga argumentet (-v) används.|
    |/opt/logs/dsc.log |Innehåller information om DSC-tjänstaktiviteten,<br> i synnerhet anslutningen mellan tjänsten himds och Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Innehåller information om DSC-tjänstens telemetri och utförlig loggning.|
    |/var/lib/GuestConfig/ext_mgr_logs |Innehåller information om tilläggsagentkomponenten.|
    |/var/lib/GuestConfig/extension_logs|Innehåller information från det installerade tillägget.|

* Följande miljövariabler skapas under agentinstallationen. Dessa variabler anges i `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Standardvärde |Beskrivning |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Följande artefakter tas inte bort under avinstallationen av agenten.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Nästa steg

* Om du vill börja Azure Arc-aktiverade servrar följer du artikeln [Ansluta hybriddatorer](onboard-portal.md)till Azure från Azure Portal .

* Felsökningsinformation finns i guiden Felsöka [Connected Machine-agenten.](troubleshoot-agent-onboard.md)
