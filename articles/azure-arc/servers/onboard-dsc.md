---
title: Installera den anslutna dator agenten med Windows PowerShell DSC
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med hjälp av Azure Arc-aktiverade servrar med Windows PowerShell DSC.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: c0ae9c97afe14559aa36c1b8387f07897aa4c43b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587646"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Så här installerar du den anslutna dator agenten med hjälp av Windows PowerShell DSC

Med hjälp av [Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) kan du automatisera program varu installation och konfiguration för en Windows-dator. Den här artikeln beskriver hur du använder DSC för att installera Azure Arc-aktiverade servrar som är anslutna till dator agenten på Hybrid Windows-datorer.

## <a name="requirements"></a>Krav

- Windows PowerShell version 4,0 eller senare

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC-modulen

- Ett huvud namn för tjänsten som ansluter datorerna till Azure Arc-aktiverade servrar icke-interaktivt. Följ stegen i avsnittet [skapa ett huvud namn för tjänsten för onboarding i skala](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) om du inte redan har skapat ett tjänst huvud namn för Arc-aktiverade servrar.

## <a name="install-the-connectedmachine-dsc-module"></a>Installera ConnectedMachine DSC-modulen

1. Om du vill installera modulen manuellt kan du ladda ned käll koden och packa upp innehållet i projekt katalogen till `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Eller kör följande kommando för att installera från PowerShell-galleriet med hjälp av PowerShellGet (i PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Bekräfta installationen genom att köra följande kommando och se till att du ser de tillgängliga Azure-resurserna för Azure Connected Machine.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   I utdata bör du se något som liknar följande:

   ![Bekräftelse av installations exempel för ansluten Machine DSC-modul](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och Anslut till Azure

Resurserna i den här modulen är utformade för att hantera konfigurationen av Azure-anslutna dator agenter. Det finns också ett PowerShell `AzureConnectedMachineAgent.ps1` -skript som finns i `AzureConnectedMachineDsc\examples` mappen. Den använder community-resurser för att automatisera nedladdning och installation och upprätta en anslutning till Azure-bågen. Det här skriptet utför liknande steg som beskrivs i [ansluta hybrid datorer till Azure från Azure Portal](onboard-portal.md) artikeln.

Om datorn behöver kommunicera via en proxyserver till tjänsten måste du köra ett kommando som beskrivs [här](manage-agent.md#update-or-remove-proxy-settings)när du har installerat agenten. Detta anger proxyserverns system miljö variabel `https_proxy` . I stället för att köra kommandot manuellt kan du utföra det här steget med DSC genom att använda [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) -modulen.

>[!NOTE]
>För att DSC ska kunna köras måste Windows konfigureras för att ta emot PowerShell-fjärrkommandon även när du kör en localhost-konfiguration. För att enkelt konfigurera din miljö på rätt sätt kan du bara köra `Set-WsManQuickConfig -Force` i en upphöjd PowerShell-Terminal.
>

Konfigurations dokument (MOF-filer) kan tillämpas på datorn med hjälp av `Start-DscConfiguration` cmdleten.

Följande är de parametrar du skickar till PowerShell-skriptet som ska användas.

- `TenantId`: Den unika identifieraren (GUID) som representerar din dedikerade instans av Azure AD.

- `SubscriptionId`: Prenumerations-ID (GUID) för den Azure-prenumeration som du vill ha datorerna i.

- `ResourceGroup`: Namnet på den resurs grupp där du vill att dina anslutna datorer ska tillhöra.

- `Location`: Se [Azure-regioner som stöds](overview.md#supported-regions). Den här platsen kan vara samma eller olika, som resurs gruppens plats.

- `Tags`: Sträng mat ris med taggar som ska tillämpas på den anslutna dator resursen.

- `Credential`: Ett PowerShell-Credential med **ApplicationId** och **lösen ord** som används för att registrera datorer i skala med hjälp av ett [huvud namn för tjänsten](onboard-service-principal.md).

1. I en PowerShell-konsol navigerar du till mappen där du sparade `.ps1` filen.

2. Kör följande PowerShell-kommandon för att kompilera MOF-dokumentet (information om hur du kompilerar DSC-konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Då skapas en `localhost.mof file` i en ny mapp med namnet `C:\dsc` .

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Lägga till i befintliga konfigurationer

Den här resursen kan läggas till i befintliga DSC-konfigurationer för att representera en slutpunkt-till-slutpunkt-konfiguration för en dator. Du kanske exempelvis vill lägga till den här resursen i en konfiguration som anger säkra inställningar för operativ systemet.

[CompositeResource](https://www.powershellgallery.com/packages/compositeresource) -modulen från PowerShell-galleriet kan användas för att skapa en [sammansatt resurs](/powershell/scripting/dsc/resources/authoringResourceComposite) av exempel konfigurationen för att ytterligare förenkla kombinera konfigurationer.

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md)och mycket mer.

* Läs mer om den [Log Analytics agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).