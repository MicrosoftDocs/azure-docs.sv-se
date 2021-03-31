---
title: Lägg till Azure Automation runbooks i Site Recovery återställnings planer
description: Lär dig hur du utökar återställnings planer med Azure Automation för haveri beredskap med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: ramamill
ms.openlocfilehash: a141280338632fdad7053cbbe76c8bdf2797443d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89424879"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Lägga till Azure Automation Runbook-rutiner i återställningsplaner

I den här artikeln beskrivs hur du integrerar Azure Automation runbooks för att utöka [Azure Site Recovery](site-recovery-overview.md) återställnings planer. Vi visar dig hur du automatiserar grundläggande uppgifter som annars skulle behöva åtgärdas manuellt och hur du konverterar en multi-Step-återställning till en åtgärd med enkel klickning.

## <a name="recovery-plans"></a>Återställningsplaner 

Du kan använda återställnings planer när du växlar över lokala datorer eller virtuella Azure-datorer. Med återställnings planer kan du definiera en systematisk återställnings process som definierar hur datorer växlar över och hur de startar och återställer efter redundansväxlingen. 

Det kan vara komplicerat att återställa stora appar. Återställnings planer hjälper till att införa order så att återställningen konsekvent är korrekt, repeterbar och automatiserad. Du kan automatisera uppgifter i en återställnings plan med hjälp av skript, samt Azure Automation runbooks. Vanliga exempel kan vara att konfigurera inställningar på en virtuell Azure-dator efter redundansväxlingen eller konfigurera om en app som körs på den virtuella datorn.

- [Läs mer](recovery-plan-overview.md) om återställnings planer.
- [Läs mer](../automation/automation-runbook-types.md) om Azure Automation runbooks.



## <a name="runbooks-in-recovery-plans"></a>Runbooks i återställnings planer

Du lägger till ett Azure Automation konto och Runbooks i en återställnings plan. Runbooken anropas när återställnings planen körs.

- Automation-kontot kan finnas i valfri Azure-region och måste vara i samma prenumeration som Site Recovery-valvet. 
- En Runbook kan köras i en återställnings plan under redundansväxling från en primär plats till en sekundär, eller under återställning efter fel från den sekundära platsen till den primära.
- Runbooks i en återställnings plan körs seriellt, en efter en annan, i uppsättnings ordningen.
- Om Runbooks i en återställnings plan konfigurerar virtuella datorer att starta i olika grupper, fortsätter återställnings planen bara att gälla när Azure rapporterar alla virtuella datorer som körs.
- Återställnings planer fortsätter att köras, även om ett skript Miss lyckas.

### <a name="recovery-plan-context"></a>Kontext för återställnings plan

När ett skript körs injiceras en kontext för återställnings planen till runbooken. Kontexten innehåller de variabler som sammanfattas i tabellen.

| **Variabelnamn** | **Beskrivning** |
| --- | --- |
| RecoveryPlanName |Namn på återställnings plan. Används i åtgärder baserat på namnet. |
| FailoverType |Anger om det är ett test-eller produktions fel. 
| FailoverDirection | Anger om återställningen är till en primär eller sekundär plats. |
| GroupID |Identifierar grupp numret i återställnings planen när planen körs. |
| VmMap |En matris med alla virtuella datorer i gruppen. |
| VMMap-nyckel |En unik nyckel (GUID) för varje virtuell dator. |
| SubscriptionId |ID för Azure-prenumerationen där den virtuella datorn skapades. |
| ResourceGroupName | Namnet på resurs gruppen där den virtuella datorn finns.
| CloudServiceName |Namnet på Azure-molnet som den virtuella datorn skapades under. |
| RoleName |Namnet på den virtuella Azure-datorn. |
| RecoveryPointId|Tidsstämpeln för VM-återställningen. |

>[!Note]
>Värdet för variabeln "FailoverDirection" blir "PrimaryToSecondary" vid växling vid fel och "SecondaryToPrimary" i händelse av återställning efter fel.

I följande exempel visas en Sammanhangs variabel:

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Om du vill få åtkomst till alla virtuella datorer i VMMap i en slinga kan du använda följande kod:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Aman- [Sharma finns ett](http://harvestingclouds.com) användbart exempel på ett [kontext skript för återställnings plan](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/).



## <a name="before-you-start"></a>Innan du börjar

- Om du inte har använt Azure Automation kan du [Registrera dig](https://azure.microsoft.com/services/automation/) och [Hämta exempel skript](https://azure.microsoft.com/documentation/scripts/).
- Se till att Automation-kontot har följande moduler:
    - AzureRM. Profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Alla moduler måste vara kompatibla versioner. Det enklaste sättet är att alltid använda de senaste versionerna av alla moduler.



## <a name="customize-the-recovery-plan"></a>Anpassa återställnings planen

1. I valvet väljer du **återställnings planer (Site Recovery)**
2. Klicka på **+ återställnings plan** för att skapa en återställnings plan. [Läs mer](site-recovery-create-recovery-plans.md). Om du redan har en återställnings plan väljer du att öppna den.
3. På sidan återställnings plan klickar du på **Anpassa**.

    ![Klicka på knappen anpassa](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Klicka på ellipserna (...) bredvid **grupp 1: starta**  >  **Lägg till post-åtgärd**.
3. I **Infoga åtgärd** kontrollerar du att **skript** är markerat och anger ett namn för skriptet (**Hello World**).
4. Ange ett Automation-konto och välj en Runbook. Klicka på **OK** om du vill spara skriptet. Skriptet läggs till i **grupp 1: post-steg**.


## <a name="reuse-a-runbook-script"></a>Återanvänd ett Runbook-skript

Du kan använda ett enda Runbook-skript i flera återställnings planer genom att använda externa variabler. 

- Du använder [Azure Automation variabler](../automation/shared-resources/variables.md) för att lagra parametrar för att köra en återställnings plan.
- Genom att lägga till återställnings plan namnet som ett prefix till variabeln kan du skapa enskilda variabler för varje återställnings plan. Använd sedan variablerna som parametrar.
- Du kan ändra en parameter utan att ändra skriptet, men ändå ändra hur skriptet fungerar.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Använda en enkel sträng variabel i ett Runbook-skript

I det här exemplet tar ett skript sig in i en nätverks säkerhets grupp (NSG) och tillämpar det på de virtuella datorerna i en återställnings plan. 

1. För att skriptet ska kunna identifiera vilken återställnings plan som körs använder du den här kontexten för återställnings plan:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Observera NSG namn och resurs grupp. Du använder dessa variabler som indata för återställnings plan skript. 
1. I Automation-kontots till gångar. skapa en variabel för att lagra NSG namn. Lägg till ett prefix till variabel namnet med namnet på återställnings planen.

    ![Skapa en variabel för NSG Name](media/site-recovery-runbook-automation-new/var1.png)

2. Skapa en variabel för att lagra resurs grupps namnet för NSG-resursen. Lägg till ett prefix till variabel namnet med namnet på återställnings planen.

    ![Skapa ett NSG resurs grupp namn](media/site-recovery-runbook-automation-new/var2.png)


3.  Använd den här referens koden i skriptet för att hämta variabel värden:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Använd variablerna i runbooken för att tillämpa NSG på nätverks gränssnittet för den misslyckade virtuella datorn:

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Skapa oberoende variabler för varje återställnings plan så att du kan återanvända skriptet. Lägg till ett prefix med hjälp av namnet på återställnings planen. 

För ett komplett skript från slut punkt till slut punkt för det här scenariot, granska [skriptet](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Använd en komplex variabel för att lagra mer information

I vissa fall kanske du inte kan skapa separata variabler för varje återställnings plan. Överväg ett scenario där du vill att ett enda skript ska tilldela en offentlig IP-adress på vissa virtuella datorer. I ett annat scenario kanske du vill använda olika NSG: er på olika virtuella datorer (inte på alla virtuella datorer). Tänk på följande:

- Du kan göra ett skript som kan återanvändas för alla återställnings planer.
- Varje återställnings plan kan ha ett variabel antal virtuella datorer.
- Till exempel har en SharePoint-återställning två frontend-sidor. Ett grundläggande LOB-program (Line-of-Business) har bara en klient del.
- I det här scenariot kan du inte skapa separata variabler för varje återställnings plan.

I följande exempel skapar vi en [komplex variabel](/powershell/module/servicemanagement/azure.service/set-azureautomationvariable) i Azure Automation-kontot.

Vi gör detta genom att ange flera värden med hjälp av Azure PowerShell.

1. Logga in på din Azure-prenumeration i PowerShell:

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Om du vill lagra parametrarna skapar du den komplexa variabeln med namnet på återställnings planen:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. I den här komplexa variabeln är **VMDETAILS** VM-ID: t för den skyddade virtuella datorn. Om du vill hämta VM-ID: t i Azure Portal visar du VM-egenskaperna. Följande skärm bild visar en variabel som lagrar information om två virtuella datorer:

    ![Använd VM-ID: t som GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Använd den här variabeln i din Runbook. Om det angivna virtuella dator-GUID: et finns i återställnings planens kontext använder du NSG på den virtuella datorn:

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. I din Runbook går du igenom de virtuella datorerna i återställnings planens kontext. Kontrol lera om den virtuella datorn finns i **$VMDetailsObj**. Om den finns får du åtkomst till egenskaperna för variabeln för att tillämpa NSG:

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Du kan använda samma skript för olika återställnings planer. Ange olika parametrar genom att lagra värdet som motsvarar en återställnings plan i olika variabler.

## <a name="sample-scripts"></a>Exempelskript

Om du vill distribuera exempel skript till ditt Automation-konto klickar du på knappen **distribuera till Azure** .

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Den här videon innehåller ett annat exempel. Det visar hur du återställer ett WordPress-program på två nivåer till Azure:


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om ett [Azure Automation kör som-konto](../automation/manage-runas-account.md)
- Granska [Azure Automation exempel skript](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team).
- [Läs mer](site-recovery-failover.md) om att köra redundans.
