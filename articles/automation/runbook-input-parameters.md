---
title: Konfigurera runbook-indataparametrar i Azure Automation
description: Den här artikeln beskriver hur du konfigurerar runbook-indataparametrar som gör att data kan skickas till en runbook när den startas.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95a6cc87471fcf2209d452f90e1e5f52cae122c5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831302"
---
# <a name="configure-runbook-input-parameters"></a>Konfigurera indataparametrar för Runbook

Indataparametrar för runbook ökar flexibiliteten för en runbook genom att tillåta att data skickas till den när den startas. De här parametrarna gör att runbook-åtgärder kan riktas mot specifika scenarier och miljöer. I den här artikeln beskrivs konfiguration och användning av indataparametrar i dina runbooks.

Du kan konfigurera indataparametrar för PowerShell, PowerShell Workflow, grafiska runbooks och Python-runbooks. En runbook kan ha flera parametrar med olika datatyper eller inga parametrar alls. Indataparametrar kan vara obligatoriska eller valfria och du kan använda standardvärden för valfria parametrar.

Du tilldelar värden till indataparametrarna för en runbook när du startar den. Du kan starta en runbook från Azure Portal, en webbtjänst eller PowerShell. Du kan också starta en som en underordnad runbook som anropas infogade i en annan runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurera indataparametrar i PowerShell-runbooks

PowerShell- och PowerShell Workflow-runbooks Azure Automation har stöd för indataparametrar som definieras via följande egenskaper. 

| **Egenskap** | **Beskrivning** |
|:--- |:--- |
| Typ |Krävs. Den datatyp som förväntas för parametervärdet. Alla .NET-typer är giltiga. |
| Name |Krävs. Namnet på parametern. Det här namnet måste vara unikt inom runbooken, måste börja med en bokstav och får bara innehålla bokstäver, siffror eller understreck. |
| Obligatorisk |Valfritt. Booleskt värde som anger om parametern kräver ett värde. Om du ställer in värdet på True (Sant) måste ett värde anges när runbooken startas. Om du ställer in värdet på False (Falskt) är värdet valfritt. Om du inte anger något värde för egenskapen anser `Mandatory` PowerShell att indataparametern är valfri som standard. |
| Standardvärde |Valfritt. Ett värde som används för parametern om inget indatavärde skickas när runbook startas. Runbooken kan ange ett standardvärde för alla parametrar. |

Windows PowerShell har stöd för fler attribut för indataparametrar än de som anges ovan, till exempel validering, alias och parameteruppsättningar. För närvarande Azure Automation dock endast de angivna indataparameteregenskaperna.

Vi kan till exempel titta på en parameterdefinition i en PowerShell Workflow-runbook. Den här definitionen har följande allmänna format, där flera parametrar avgränsas med kommatecken.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Nu ska vi konfigurera indataparametrarna för en PowerShell Workflow-runbook som matar ut information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resursgrupp. Denna runbook har två parametrar, som du ser i följande skärmbild: namnet på den virtuella datorn ( ) och namnet `VMName` på resursgruppen ( `resourceGroupName` ).

![Automation PowerShell-arbetsflöde](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

I den här parameterdefinitionen är indataparametrarna enkla parametrar av typen sträng.

Observera att PowerShell- och PowerShell Workflow-runbooks stöder alla enkla typer och komplexa typer, till exempel `Object` eller `PSCredential` för indataparametrar. Om din runbook har en objektindataparameter måste du använda en PowerShell-hashtabell med namn/värde-par för att skicka in ett värde. Du kan till exempel ha följande parameter i en runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

I det här fallet kan du skicka följande värde till parametern .

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> När du inte skickar ett värde till en valfri Strängparameter med ett null-standardvärde är värdet för parametern en tom sträng i stället för Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurera indataparametrar i grafiska runbooks

För att illustrera konfigurationen av indataparametrar för en grafisk runbook ska vi skapa en runbook som matar ut information om virtuella datorer, antingen en enskild virtuell dator eller alla virtuella datorer i en resursgrupp. Mer information finns i [Min första grafiska runbook.](./learn/automation-tutorial-runbook-graphical.md)

En grafisk runbook använder dessa huvudsakliga Runbook-aktiviteter:

* Konfiguration av Kör som-kontot i Azure för autentisering med Azure. 
* Definition av en [Get-AzVM-cmdlet](/powershell/module/az.compute/get-azvm) för att hämta egenskaper för virtuella datorer.
* Användning av [write-output-aktiviteten för](/powershell/module/microsoft.powershell.utility/write-output) att mata ut namnen på den virtuella datorn. 

Aktiviteten `Get-AzVM` definierar två indata, VM-namnet och resursgruppens namn. Eftersom dessa namn kan vara olika varje gång runbooken startar måste du lägga till indataparametrar i din runbook för att acceptera dessa indata. Se Grafisk [redigering i Azure Automation](automation-graphical-authoring-intro.md).

Följ dessa steg för att konfigurera indataparametrarna.

1. Välj den grafiska runbooken på sidan Runbooks och klicka sedan på **Redigera.**
2. I den grafiska redigeraren klickar du på **knappen Indata och** utdata och sedan på Lägg till **indata** för att öppna fönstret Indataparameter för Runbook.

   ![Automation grafisk runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Kontrollen Indata och Utdata visar en lista över indataparametrar som har definierats för runbooken. Här kan du antingen lägga till en ny indataparameter eller redigera konfigurationen av en befintlig indataparameter. Om du vill lägga till en  ny parameter för runbooken klickar du på Lägg till [indata](automation-graphical-authoring-intro.md)för att öppna bladet **för Runbook-indataparametern,** där du kan konfigurera parametrar med hjälp av egenskaperna som definierats i Grafisk redigering i Azure Automation .

    ![Lägg till nya indata](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Skapa två parametrar med följande egenskaper som ska användas av `Get-AzVM` aktiviteten och klicka sedan på **OK.**

   * Parameter 1:
        * **Namn**  --  **VMName**
        * **Typ** – Sträng
        * **Obligatoriskt**  --  **Nej**

   * Parameter 2:
        * **Namn**  --  **resourceGroupName**
        * **Typ** – Sträng
        * **Obligatoriskt**  --  **Nej**
        * **Standardvärde**  --  **Anpassad**
        * Anpassat standardvärde – Namnet på resursgruppen som innehåller de virtuella datorerna

5. Visa parametrarna i indata- och utdatakontrollen. 
6. Klicka **på OK** igen och sedan på **Spara.**
7. Klicka **på Publicera** för att publicera din runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurera indataparametrar i Python-runbooks

Till skillnad från PowerShell, PowerShell Workflow och grafiska runbooks tar Python-runbooks inte namngivna parametrar. Runbook-redigeraren parsar alla indataparametrar som en matris med argumentvärden. Du kan komma åt matrisen genom att `sys` importera modulen till Python-skriptet och sedan använda `sys.argv` matrisen. Observera att det första elementet i matrisen, `sys.argv[0]` , är namnet på skriptet. Därför är den första faktiska indataparametern `sys.argv[1]` .

Ett exempel på hur du använder indataparametrar i en Python-runbook finns i [Min första Python-runbook i Azure Automation](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Tilldela värden till indataparametrar i runbooks

I det här avsnittet beskrivs flera sätt att skicka värden till indataparametrar i runbooks. Du kan tilldela parametervärden när du:

* [Starta en Runbook](#start-a-runbook-and-assign-parameters)
* [Testa en Runbook](#test-a-runbook-and-assign-parameters)
* [Länka ett schema för runbooken](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Skapa en webhook för runbooken](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Starta en runbook och tilldela parametrar

En runbook kan startas på många sätt: via Azure Portal, med en webhook, med PowerShell-cmdlets, med REST API eller med SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Starta en publicerad runbook med hjälp Azure Portal och tilldela parametrar

När du [startar runbooken](start-runbooks.md#start-a-runbook-with-the-azure-portal) i Azure Portal öppnas **bladet Starta runbook** och du kan ange värden för de parametrar som du har skapat.

![Börja använda portalen](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

I etiketten under indatarutan kan du se de egenskaper som har angetts för att definiera parameterattribut, till exempel obligatoriskt eller valfritt, typ, standardvärde. Hjälpballongen bredvid parameternamnet definierar också den viktiga information som behövs för att fatta beslut om parameterindatavärden. 

> [!NOTE]
> Strängparametrar stöder tomma värden av typen String. Om `[EmptyString]` du anger i rutan för indataparametern skickar du en tom sträng till parametern. Strängparametrar stöder inte heller Null. Om du inte skickar något värde till en strängparameter tolkar PowerShell det som Null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Starta en publicerad runbook med PowerShell-cmdlets och tilldela parametrar

* **Azure Resource Manager cmdlets:** Du kan starta en Automation-runbook som har skapats i en resursgrupp med hjälp [av Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Cmdlets för den klassiska Azure-distributionsmodellen:** Du kan starta en Automation-runbook som har skapats i en standardresursgrupp med hjälp [av Start-AzureAutomationRunbook.](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> När du startar en runbook med PowerShell-cmdlets skapas en `MicrosoftApplicationManagementStartedBy` standardparameter, , med värdet `PowerShell` . Du kan visa den här parametern i fönstret Jobbinformation.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Starta en runbook med hjälp av en SDK och tilldela parametrar

* **Azure Resource Manager metod:** Du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C#-kodfragment för att starta en runbook i ditt Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplats.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Metod för klassisk Azure-distributionsmodell:** Du kan starta en runbook med hjälp av SDK för ett programmeringsspråk. Nedan visas ett C#-kodfragment för att starta en runbook i ditt Automation-konto. Du kan visa all kod på vår [GitHub-lagringsplats.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Starta den här metoden genom att skapa en ordlista för att lagra `VMName` runbook-parametrarna  `resourceGroupName` och deras värden. Starta sedan runbooken. Nedan visas C#-kodfragmentet för anrop av metoden som definieras ovan.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Starta en runbook med hjälp REST API och tilldela parametrar

Du kan skapa och starta ett runbook-jobb Azure Automation REST API med hjälp av `PUT` metoden med följande begärande-URI: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

Ersätt följande parametrar i URI:en för begäran:

* `subscriptionId`: Ditt Azure-prenumerations-ID.  
* `resourceGroupName`: Namnet på resursgruppen för Automation-kontot.
* `automationAccountName`: Namnet på Automation-kontot som finns i den angivna molntjänsten.  
* `jobName`: GUID för jobbet. GUID:er i PowerShell kan skapas med hjälp av `[GUID]::NewGuid().ToString()*` .

Om du vill skicka parametrar till runbook-jobbet använder du begärandetexten. Den tar följande information, som anges i JSON-format:

* Runbook-namn: Obligatoriskt. Namnet på runbooken som jobbet ska starta.  
* Runbook-parametrar: Valfritt. En ordlista med parameterlistan i formatet (namn, värde), där namnet är av typen Sträng och värde kan vara val annat giltigt JSON-värde.

Om du vill starta **den Get-AzureVMTextual-runbook** som skapades tidigare med och som parametrar använder du följande `VMName` `resourceGroupName` JSON-format för begärandetexten.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Http-statuskoden 201 returneras om jobbet har skapats. Mer information om svarshuvuden och svarstexten finns i [Skapa ett runbook-jobb med hjälp av REST API](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testa en runbook och tilldela parametrar

När du [testar utkastversionen av din runbook med](./manage-runbooks.md) hjälp av testalternativet öppnas sidan Test. Använd den här sidan för att konfigurera värden för de parametrar som du har skapat.

![Testa och tilldela parametrar](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Länka ett schema till en runbook och tilldela parametrar

Du kan [länka ett schema](./shared-resources/schedules.md) till din runbook så att runbooken startar vid en viss tidpunkt. Du tilldelar indataparametrar när du skapar schemat, och runbooken använder dessa värden när den startas enligt schemat. Du kan inte spara schemat förrän alla obligatoriska parametervärden har angetts.

![Schemalägga och tilldela parametrar](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Skapa en webhook för en runbook och tilldela parametrar

Du kan skapa en [webhook för](automation-webhooks.md) din runbook och konfigurera indataparametrar för runbook. Du kan inte spara webhooken förrän alla obligatoriska parametervärden har angetts.

![Skapa webhook och tilldela parametrar](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

När du kör en runbook med hjälp av en webhook skickas den fördefinierade indataparametern tillsammans med `[WebhookData](automation-webhooks.md)` de indataparametrar som du definierar. 

![WebhookData-parameter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Skicka ett JSON-objekt till en runbook

Det kan vara användbart att lagra data som du vill skicka till en runbook i en JSON-fil. Du kan till exempel skapa en JSON-fil som innehåller alla parametrar som du vill skicka till en runbook. Om du vill göra detta måste du konvertera JSON-koden till en sträng och sedan konvertera strängen till ett PowerShell-objekt innan du skicka den till runbooken.

I det här avsnittet används ett exempel där ett PowerShell-skript anropar [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) för att starta en PowerShell-runbook och skicka innehållet i JSON-filen till runbooken. PowerShell-runbooken startar en virtuell Azure-dator genom att hämta parametrarna för den virtuella datorn från JSON-objektet.

### <a name="create-the-json-file"></a>Skapa JSON-filen

Skriv följande kod i en textfil och spara den somtest.js **på** någonstans på den lokala datorn.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Skapa runbooken

Skapa en ny PowerShell-runbook med **namnet Test-Json** i Azure Automation. Se [Min första PowerShell-runbook.](./learn/automation-tutorial-runbook-textual-powershell.md)

Om du vill acceptera JSON-data måste runbooken ta ett -objekt som en indataparameter. Runbooken kan sedan använda de egenskaper som definierats i JSON-filen.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Spara och publicera denna runbook i ditt Automation-konto.

### <a name="call-the-runbook-from-powershell"></a>Anropa runbooken från PowerShell

Nu kan du anropa runbooken från den lokala datorn med hjälp av Azure PowerShell. 

1. Logga in på Azure enligt bilden. Därefter uppmanas du att ange dina autentiseringsuppgifter för Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >För PowerShell-runbooks `Add-AzAccount` och är alias för `Add-AzureRMAccount` `Connect-AzAccount` . Observera att dessa alias inte är tillgängliga för grafiska runbooks. En grafisk runbook kan bara använda sig `Connect-AzAccount` själv.

1. Hämta innehållet i den sparade JSON-filen och konvertera den till en sträng. `JsonPath` anger sökvägen där du sparade JSON-filen.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Konvertera stränginnehållet i till `$json` ett PowerShell-objekt.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Skapa en hash-tabell för parametrarna för `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Observera att du anger värdet för till `Parameters` Det PowerShell-objekt som innehåller värdena från JSON-filen.
1. Starta runbooken.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Nästa steg

* Information om hur du förbereder en runbook för text [finns i Redigera text-runbooks i Azure Automation](automation-edit-textual-runbook.md).
* Information om hur du förbereder en grafisk runbook [finns i Author graphical runbooks in Azure Automation](automation-graphical-authoring-intro.md).
