---
title: Skapa en delad integration runtime med egen värd med PowerShell
description: Lär dig hur du skapar en delad integration runtime med egen värd i Azure Data Factory, så att flera data fabriker kan komma åt integrerings körningen.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389441"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Skapa en delad integration runtime med egen värd i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här guiden visar hur du skapar en delad integration runtime med egen värd i Azure Data Factory. Sedan kan du använda den delade integrerings körningen med egen värd i en annan data fabrik.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Skapa en delad integration runtime med egen värd i Azure Data Factory

Du kan återanvända en befintlig egen värd för integrerings körning som du redan har konfigurerat i en data fabrik. Med den här återkopplingen kan du skapa en länkad egen värd för integrerings körning i en annan data fabrik genom att referera till en befintlig delad IR med egen värd.

Om du vill se en introduktion och demonstration av den här funktionen kan du titta på följande 12-minuters video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologi

- **Delad IR**: en ursprunglig, egen IR-IR som körs på en fysisk infrastruktur.  
- **Länkad IR**: en IR som refererar till en annan delad IR. Den länkade IR-filen är en logisk IR och använder infrastrukturen för en annan delad IR-anslutning med egen värd.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Skapa en delad IR med egen värd med Azure Data Factory användar gränssnitt

Om du vill skapa en delad IR med egen värd med Azure Data Factory användar gränssnitt kan du utföra följande steg:

1. I den egen värdbaserade IR-filen som ska delas väljer du **bevilja behörighet till en annan data fabrik** och på sidan "installation av integration runtime" väljer du den data fabrik som du vill skapa den länkade IR-filen i.
      
    ![Knapp för att bevilja behörighet på fliken Delning](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Observera att du kan dela "resurs-ID" för den egen värd-IR som ska delas.
         
3. I data fabriken som behörigheterna har beviljats skapar du en ny lokal IR-anslutning (länkad) och anger resurs-ID: t.
      
    ![Knapp för att skapa en integration runtime med egen värd](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Knapp för att skapa en länkad integration runtime med egen värd](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Rutor för namn och resurs-ID](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Skapa en delad IR med egen värd med Azure PowerShell

För att skapa en delad IR med egen värd med Azure PowerShell kan du utföra följande steg: 
1. Skapa en datafabrik. 
1. Skapa Integration Runtime med egen värd.
1. Dela den egna värdbaserade integrerings körningen med andra data fabriker.
1. Skapa en länkad integration Runtime.
1. Återkalla delningen.

### <a name="prerequisites"></a>Förutsättningar 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar. 

- **Azure PowerShell**. Följ instruktionerna i [installera Azure PowerShell på Windows med PowerShellGet](/powershell/azure/install-az-ps). Du använder PowerShell för att köra ett skript för att skapa en integration runtime med egen värd som kan delas med andra data fabriker. 

> [!NOTE]  
> Om du vill ha en lista över Azure-regioner där Data Factory för närvarande är tillgängligt väljer du de regioner som intresserar dig för  [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta Windows PowerShell ISE.

1. Skapa variabler. Kopiera och klistra in följande skript. Ersätt variablerna, till exempel **SubscriptionName** och **ResourceGroupName**, med faktiska värden: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Logga in och välj en prenumeration. Lägg till följande kod i skriptet för att logga in och välj din Azure-prenumeration:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Skapa en resurs grupp och en data fabrik.

    > [!NOTE]  
    > Det här är valfritt. Hoppa över det här steget om du redan har en data fabrik. 

    Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resurs grupp med namnet `myResourceGroup` på WestEurope-platsen: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Skapa en datafabrik genom att köra följande kommandon: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

> [!NOTE]  
> Det här är valfritt. Hoppa över det här steget om du redan har den egen värdbaserade integrerings körningen som du vill dela med andra data fabriker.

Kör följande kommando för att skapa en integration runtime med egen värd:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Hämta nyckeln för integration runtime-autentisering och registrera en nod

Kör följande kommando för att hämta autentiseringsnyckel för integration runtime med egen värd:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Svaret innehåller en autentiseringsnyckel för den här integration runtime med egen värd. Du använder den här nyckeln när du registrerar integration runtime-noden.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installera och registrera integration runtime med egen värd

1. Hämta installations programmet för den egen värdbaserade integrerings körningen från [Azure Data Factory integration runtime](https://aka.ms/dmg).

2. Kör installations programmet för att installera den lokala integrationen på en lokal dator.

3. Registrera den nya egen värdbaserade integreringen med den autentiseringsnyckel som du hämtade i föregående steg.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Dela integration runtime med egen värd med en annan data fabrik

#### <a name="create-another-data-factory"></a>Skapa en annan data fabrik

> [!NOTE]  
> Det här är valfritt. Hoppa över det här steget om du redan har den data fabrik som du vill dela med. Men för att lägga till eller ta bort roll tilldelningar till andra data fabriker måste du ha `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörighet, till exempel [administratör för användar åtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) eller [ägare](../role-based-access-control/built-in-roles.md#owner).

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Bevilja behörighet

Bevilja behörighet till den data fabrik som behöver åtkomst till den integration runtime som du har skapat och registrerat med egen värd.

> [!IMPORTANT]  
> Hoppa inte över det här steget!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Skapa en länkad egen värd för integration runtime

Kör följande kommando för att skapa en länkad egen värd för integration Runtime:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nu kan du använda den här länkade integrerings körningen i en länkad tjänst. Den länkade integrerings körningen använder den delade integrerings körningen för att köra aktiviteter.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Återkalla integration runtime-delning från en data fabrik

Om du vill återkalla åtkomsten till en data fabrik från den delade integrerings körningen kör du följande kommando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Om du vill ta bort den befintliga länkade integration runtime kör du följande kommando mot den delade integrerings körningen:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>Övervakning

#### <a name="shared-ir"></a>Delad IR

![Alternativ för att hitta en delad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Övervaka en delad integrerings körning](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Länkad IR

![Alternativ för att hitta en länkad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Övervaka en länkad integrerings körning](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Kända begränsningar för IR-delning med egen värd

* Data fabriken där en länkad IR skapas måste ha en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md). Som standard har data fabriker som skapats i Azure Portal-eller PowerShell-cmdlets en implicit skapad hanterad identitet. Men när en data fabrik skapas via en Azure Resource Manager mall eller SDK, måste du ange egenskapen **Identity** explicit. Den här inställningen säkerställer att Resource Manager skapar en data fabrik som innehåller en hanterad identitet.

* Den Data Factory .NET SDK som stöder den här funktionen måste vara version 1.1.0 eller senare.

* Om du vill bevilja behörighet behöver du ägar rollen eller den ärvda ägar rollen i data fabriken där den delade IR-filen finns.

* Delnings funktionen fungerar endast för data fabriker inom samma Azure AD-klient.

* För Azure AD- [gäst användare](../active-directory/governance/manage-guest-access-with-access-reviews.md)är Sök funktionen i användar gränssnittet, som visar alla data fabriker med hjälp av nyckelordet search, inte fungerar. Men så länge gäst användaren är ägare till data fabriken kan du dela IR-filen utan Sök funktionen. För den hanterade identiteten för den data fabrik som behöver dela IR-filen anger du den hanterade identitet i rutan **tilldela behörighet** och väljer **lägg till** i Data Factory gränssnittet.

  > [!NOTE]
  > Den här funktionen är endast tillgänglig i Data Factory v2.


### <a name="next-steps"></a>Nästa steg

- Granska [integration runtime-koncept i Azure Data Factory](./concepts-integration-runtime.md).

- Lär dig hur du [skapar en integration runtime med egen värd i Azure Portal](./create-self-hosted-integration-runtime.md).