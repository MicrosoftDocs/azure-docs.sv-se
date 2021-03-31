---
title: Felsöka problem med artefakter i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du felsöker problem som inträffar när du använder artefakter på en Azure DevTest Labs virtuell dator.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483201"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Felsöka problem när du använder artefakter på en Azure DevTest Labs virtuell dator
Att tillämpa artefakter på en virtuell dator kan inte utföras av olika orsaker. Den här artikeln vägleder dig igenom några av metoderna för att identifiera möjliga orsaker.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta experterna Azure DevTest Labs (DTL) i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support.   

> [!NOTE]
> Den här artikeln gäller för virtuella Windows-datorer och icke-Windows-datorer. Även om det finns vissa skillnader kommer de att anges uttryckligen i den här artikeln.

## <a name="quick-troubleshooting-steps"></a>Snabb fel söknings steg
Kontrol lera att den virtuella datorn körs. DevTest Labs kräver att den virtuella datorn körs och att den [Microsoft Azure virtuella dator agenten (VM-agenten)](../virtual-machines/extensions/agent-windows.md) är installerad och klar.

> [!TIP]
> I **Azure Portal** navigerar du till sidan **Hantera artefakter** för den virtuella datorn för att se om den virtuella datorn är redo för att tillämpa artefakter. Du ser ett meddelande längst upp på sidan. 
> 
> Med hjälp av **Azure PowerShell**, inspektera flaggan **canApplyArtifacts**, som endast returneras när du expanderar en get-åtgärd. Se följande exempel kommando:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Sätt att felsöka 
Du kan felsöka virtuella datorer som skapats med DevTest Labs och distributions modellen för Resource Manager genom att använda någon av följande metoder:

- **Azure Portal** – bra om du snabbt behöver få ett visuellt tips om vad som kan orsaka problemet.
- **Azure PowerShell** – om du är van att använda en PowerShell-prompt kan du snabbt fråga DevTest Labs-resurser med hjälp av Azure PowerShell-cmdletar.

> [!TIP]
> Mer information om hur du granskar artefakt körningar i en virtuell dator finns i [diagnostisera artefakt fel i labbet](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Symptom, orsaker och potentiella lösningar 

### <a name="artifact-appears-to-stop-responding"></a>Artefakt verkar sluta svara

En artefakt verkar sluta svara tills en fördefinierad tids gräns upphör att gälla och artefakten markeras som **misslyckad**.

När en artefakt verkar låsa sig måste du först avgöra var den fastnar. En artefakt kan blockeras vid något av följande steg under körningen:

- **Under den första begäran**. DevTest Labs skapar en Azure Resource Manager-mall för att begära att det anpassade skript tillägget (CSE) används. I bakgrunden utlöses därför en resurs grupps distribution. När ett fel inträffar på den här nivån får du information i **aktivitets loggarna** för resurs gruppen för den virtuella datorn i fråga.  
    - Du kan komma åt aktivitets loggen från navigerings fältet i Labbets virtuella dator sida. När du väljer det kan du se en post för **att antingen tillämpa artefakter på den virtuella datorn** (om åtgärden tillämpa artefakter utlöstes direkt) eller **lägga till eller ändra virtuella datorer** (om åtgärden tillämpa artefakter var en del av processen för att skapa virtuella datorer).
    - Leta efter fel under dessa poster. Ibland märks felet inte i enlighet med detta och du måste undersöka varje post.
    - När du undersöker informationen om varje post ser du till att granska innehållet i JSON-nyttolasten. Du kan se ett fel längst ned i dokumentet.
- **Vid försök att köra artefakten**. Det kan bero på nätverks-eller lagrings problem. Mer information finns i respektive avsnitt längre fram i den här artikeln. Det kan också inträffa på grund av hur skriptet har skapats. Exempel:
    - Ett PowerShell-skript har **obligatoriska parametrar**, men det går inte att skicka ett värde till det, antingen på grund av att du tillåter att användaren lämnar den tom eller eftersom du inte har ett standardvärde för egenskapen i artifactfile.jsi definitions filen. Skriptet slutar svara eftersom det väntar på användarindata.
    - Ett PowerShell-skript **kräver indata från användaren** som en del av körningen. Skripten måste skrivas för att fungera tyst utan att användaren behöver vidta några åtgärder.
- **Det tar lång tid för VM-agenten att bli redo**. När den virtuella datorn startas eller när det anpassade skript tillägget först installeras för att betjäna begäran om att tillämpa artefakter, kan den virtuella datorn behöva uppgradera VM-agenten eller vänta tills VM-agenten initieras. Det kan finnas tjänster där VM-agenten är beroende av att det tar lång tid att initiera. I sådana fall kan du läsa [Översikt över Azure Virtual Machine agent](../virtual-machines/extensions/agent-windows.md) för ytterligare fel sökning.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Så här kontrollerar du om artefakten verkar sluta svara på grund av skriptet

1. Logga in på den virtuella datorn i fråga.
2. Kopiera skriptet lokalt på den virtuella datorn eller leta upp det på den virtuella datorn under `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . Det är den plats där artefakt skripten laddas ned.
3. Använd en upphöjd kommando tolk och kör skriptet lokalt, vilket ger samma parameter värden som används för att orsaka problemet.
4. Kontrol lera om skriptet lider av oönskade beteenden. I så fall, begär du en uppdatering av artefakten (om den kommer från den offentliga lagrings platsen). eller gör korrigeringarna själv (om det är från din privata lagrings platsen).

> [!TIP]
> Du kan åtgärda problem med artefakter som finns i vår [offentliga lagrings platsen](https://github.com/Azure/azure-devtestlab) och skicka ändringarna för granskning och godkännande. Se avsnittet **bidrag** i [Readme.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) -dokumentet.
> 
> Information om hur du skriver dina egna artefakter finns i [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) -dokument.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Så här kontrollerar du om artefakten verkar sluta svara på grund av VM-agenten:
1. Logga in på den virtuella datorn i fråga.
2. Använd Utforskaren navigera till **C:\WindowsAzure\logs**.
3. Leta upp och öppna filen **WaAppAgent. log**.
4. Leta efter poster som visar när VM-agenten startar och när initieringen slutförs (d.v.s. det första pulsslaget skickas). Prioritera nyare poster eller mer specifikt runt den tids period som du upplever problemet för.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    I det här exemplet kan du se att start tiden för VM-agenten tog 10 minuter och 20 sekunder eftersom ett pulsslag har skickats. Orsaken till det här fallet var att OOBE-tjänsten tog lång tid att starta.

> [!TIP]
> Allmän information om Azure-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Lagrings fel
DevTest Labs kräver åtkomst till Labbets lagrings konto som skapas för att cachelagra artefakter. När DevTest Labs tillämpar en artefakt, kommer den att läsa artefakt konfigurationen och dess filer från de konfigurerade databaserna. Som standard konfigurerar DevTest Labs åtkomst till den **offentliga artefakten lagrings platsen**.

Beroende på hur en virtuell dator har kon figurer ATS kanske den inte har direkt åtkomst till den här lagrings platsen. Därför cachelagrar DevTest Labs artefakterna i ett lagrings konto som skapas när labbet först initieras.

Om åtkomst till det här lagrings kontot blockeras på något sätt, som när trafik blockeras från den virtuella datorn till Azure Storage tjänsten, kan det hända att du ser ett fel som liknar följande:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Ovanstående fel visas i avsnittet **distributions meddelande** på sidan **artefakt resultat** under **Hantera artefakter**. Den visas också i **aktivitets loggarna** under resurs gruppen för den aktuella virtuella datorn.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>För att säkerställa kommunikation till den Azure Storage tjänsten blockeras inte:

- **Sök efter tillagda nätverks säkerhets grupper (NSG)**. Det kan bero på att en prenumerations princip har lagts till där NSG: er konfigureras automatiskt i alla virtuella nätverk. Det kan också påverka Labbets virtuella standard nätverk, om det används eller något annat virtuellt nätverk som kon figurer ATS i ditt labb, som används för att skapa virtuella datorer.
- **Kontrol lera standard Labbets lagrings konto** (det vill säga det första lagrings konto som skapades när labbet skapades, vars namn vanligt vis börjar med bokstaven "a" och slutar med ett fyrsiffrigt tal som är en siffra \<labname\> ).
    1. Navigera till Labbets resurs grupp.
    2. Leta upp resursen av typen **lagrings konto**, vars namn matchar konventionen.
    3. Navigera till lagrings konto sidan som kallas **brand väggar och virtuella nätverk**.
    4. Kontrol lera att den är inställd på **alla nätverk**. Om alternativet **valda nätverk** är markerat kontrollerar du att Labbets virtuella nätverk som används för att skapa virtuella datorer läggs till i listan.

Mer djupgående fel sökning finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../storage/common/storage-network-security.md).

> [!TIP]
> **Kontrol lera regler för nätverks säkerhets grupper**. Använd [kontrol lera IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) för att bekräfta att en regel i en nätverks säkerhets grupp blockerar trafik till eller från en virtuell dator. Du kan också granska effektiva säkerhets grupps regler för att säkerställa att NSG-regeln för **inkommande trafik finns** . Mer information finns i [använda effektiva säkerhets regler för att felsöka trafik flöde för virtuella datorer](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Andra fel källor
Det finns andra mindre frekventa möjliga fel källor. Var noga med att utvärdera var och en för att se om den gäller för ditt ärende. Här är en av dem: 

- **En privat lagrings platsen har gått ut**. När den har upphört att gälla visas inte artefakten och skript som refererar till artefakter från en lagrings plats med en utgången privat åtkomsttoken kommer att Miss förfaller.

## <a name="next-steps"></a>Nästa steg
Om ingen av dessa fel har inträffat och du fortfarande inte kan tillämpa artefakter, kan du skicka en support incident till Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
