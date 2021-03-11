---
title: Flytta resurser mellan regioner med hjälp av PowerShell i Azure Resource förflyttare
description: Lär dig hur du flyttar resurser mellan regioner med hjälp av PowerShell i Azure Resource överfart.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584424"
---
# <a name="move-resources-across-regions-in-powershell"></a>Flytta resurser mellan regioner i PowerShell

Den här artikeln beskriver hur du flyttar Azure-resurser till en annan Azure-region med hjälp av PowerShell i [Azure Resource-arbetskraft](overview.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera krav och krav.
> * Konfigurera flytt samlingen.
> * Lägg till resurser i flyttnings samlingen och lös beroenden.
> * Förbered och flytta käll resurs gruppen. 
> * Förbered och flytta de andra resurserna.
> * Bestäm om du vill ta bort eller bekräfta flytten. 
> * Du kan också ta bort resurser i käll regionen efter flytten.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar
**Krav** | **Beskrivning**
--- | ---
**Prenumerations behörigheter** | Kontrol lera att du har *ägar* åtkomst till prenumerationen som innehåller de resurser som du vill flytta<br/><br/> **Varför behöver jag ägar åtkomst?** Första gången du lägger till en resurs för ett visst käll-och mål par i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen. Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare eller administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
**Stöd för resurs förflyttning** | [Granska](common-questions.md) regioner som stöds och andra vanliga frågor.
**Stöd för virtuella datorer** |  Kontrol lera att alla virtuella datorer som du vill flytta stöds.<br/><br/> - [Verifiera](support-matrix-move-region-azure-vm.md#windows-vm-support) stödda virtuella Windows-datorer.<br/><br/> - [Kontrol lera](support-matrix-move-region-azure-vm.md#linux-vm-support) virtuella Linux-datorer och kernel-versioner som stöds.<br/><br/> – Kontrol lera inställningarna för [beräkning](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och [nätverk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) som stöds.
**SQL-stöd** | Om du vill flytta SQL-resurser granskar du [listan SQL-krav](tutorial-move-region-sql.md#check-sql-requirements).
**Mål prenumeration** | Prenumerationen i mål regionen behöver tillräcklig kvot för att skapa de resurser som du flyttar i mål regionen. Om den inte har någon kvot [begär du ytterligare begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Avgifter för mål region** | Kontrol lera priser och avgifter som är kopplade till den mål region som du flyttar virtuella datorer till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.

### <a name="review-powershell-requirements"></a>Granska PowerShell-krav

De flesta åtgärder för att flytta resurser är desamma oavsett om du använder Azure Portal eller PowerShell, med ett par undantag.

**Åtgärd** | **PowerShell** | **Portal**
--- | --- | ---
**Skapa en flytt samling** | En flyttnings samling (en lista över alla resurser som du flyttar) skapas automatiskt. Nödvändiga identitets behörigheter tilldelas i Server delen av portalen. | Du använder PowerShell-cmdlet: ar för att:<br/><br/> – Skapa en resurs grupp för flytt samlingen och ange platsen för den.<br/><br/> – Tilldela en hanterad identitet till samlingen.<br/><br/> -Lägg till resurser i samlingen.
**Ta bort en flyttnings samling** | Du kan inte ta bort en flytt samling direkt i portalen. | Du använder en PowerShell-cmdlet för att ta bort en flyttnings samling.
**Åtgärder för resurs flyttning**<br/><br/> (Förbered, påbörja flyttning, genomförande osv.).| Enkla steg med automatisk validering av resurs förflyttning. | PowerShell-cmdlet: ar för att:<br/><br/> 1) verifiera beroenden.<br/><br/> 2) utför flytten.
**Ta bort käll resurser** | Direkt i resurs förflyttnings portalen. | PowerShell-cmdletar på resurs typ nivå.


### <a name="sample-values"></a>Exempel värden

Vi använder dessa värden i våra skript exempel:

**Inställning** | **Värde** 
--- | ---
Prenumerations-ID:t | prenumerations-ID
Käll region |  Central US
Mål region | USA, västra centrala
Resurs grupp (håller metadata för flytt av insamling) | RG-MoveCollection-demoRMS
Flytta samlings namn | PS-centrala-westcentralus-demoRMS
Resurs grupp (käll region) | PSDemoRM 
Resurs grupp (mål region) | PSDemoRM-mål
Resurs flytta tjänst plats | USA, östra 2
IdentityType | SystemAssigned
Virtuell dator som ska flyttas | PSDemoVM


## <a name="sign-into-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med Connect-AzAccount-cmdlet:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Konfigurera flytt samlingen

MoveCollection-objektet lagrar metadata och konfigurations information om resurser som du vill flytta. Om du vill konfigurera en flyttnings samling gör du följande:

- Skapa en resurs grupp för flytt samlingen.
- Registrera tjänst leverantören för prenumerationen så att du kan skapa MoveCollection-resursen.
- Skapa MoveCollection-objektet med hanterad identitet. För att MoveCollection-objektet ska få åtkomst till den prenumeration där resurs förflyttnings tjänsten finns, behöver den en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare kallad HANTERAD TJÄNSTIDENTITET (MSI)) som är betrodd av prenumerationen.
- Bevilja åtkomst till resurs förflyttnings prenumerationen för den hanterade identiteten.

### <a name="create-the-resource-group"></a>Skapa resursgruppen

Skapa en resurs grupp för metadata och konfigurations information för Move Collection enligt följande:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Utdata**:

![Mata ut text när resurs grupp har skapats](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registrera resursprovidern

1. Registrera resurs leverantören Microsoft. Migrate, så att du kan skapa MoveCollection-resursen på följande sätt:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Skapa ett MoveCollection-objekt

Skapa ett MoveCollection-objekt och tilldela en hanterad identitet enligt följande: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Utdata**:

![Utgående text efter skapande av flytta samling](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Bevilja åtkomst till den hanterade identiteten

Ge åtkomst till den hanterade identiteten till resurs förflyttnings prenumerationen enligt följande. Du måste vara prenumerations ägare.

1. Hämta identitets information från MoveCollection-objektet.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Tilldela de nödvändiga rollerna till identiteten så att Azure Resource-arbetskraften kan komma åt din prenumeration för att hjälpa till att flytta resurser.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Lägg till resurser i flyttnings samlingen

Hämta ID: na för befintliga käll resurser som du vill flytta. Skapa objektet mål resurs inställningar och Lägg sedan till resurser i flyttnings samlingen.

> [!NOTE]
> Resurser som läggs till i en flyttnings samling måste finnas i samma prenumeration, men kan finnas i olika resurs grupper.

Lägg till resurser enligt följande:

1. Hämta käll resurs-ID:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Resultat**

    ![Utgående text efter hämtning av resurs-ID](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Skapa objektet mål resurs inställningar i enlighet med den resurs som du flyttar. I vårt fall är det en virtuell dator.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Ange resurs typ och mål resurs namn för objektet.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > Vår virtuella mål dator har samma namn som den virtuella datorn i käll regionen. Du kan välja ett annat namn.

4. Lägg till käll resurserna i flyttnings samlingen med hjälp av det resurs-ID och mål inställnings objekt som du hämtade/skapade.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Utdata** ![ Skriva text när resursen har lagts till](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Verifiera och lägga till beroenden

Kontrol lera om de resurser som du har lagt till är beroende av andra resurser och Lägg till efter behov. 

1. Verifiera beroenden enligt följande:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Utdata (när beroenden finns)**

    ![Utgående text efter validering av beroenden](./media/tutorial-move-region-powershell/dependency-output.png)

2. Identitet saknas beroenden:

    - Hämta en lista över alla beroenden som saknas:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Utdata** ![ Utgående text efter hämtning av en lista med alla beroenden](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Så här hämtar du endast beroenden på första nivån (direkta beroenden för resursen):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Utdata** ![ Utgående text efter hämtning av en lista över beroenden på första nivån](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Om du vill lägga till eventuella utestående beroenden upprepar du anvisningarna ovan för att [lägga till resurser i flytt samlingen](#add-resources-to-the-move-collection)och omverifiera tills det inte finns några utestående resurser.

> [!NOTE]
> Om du av någon anledning vill ta bort resurser från resurs samlingen följer du anvisningarna i [den här artikeln](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>Lägg till käll resurs gruppen

Lägg till käll resurs gruppen som innehåller resurser som du vill flytta till flyttnings samlingen.

1. Hämta ID för resurs gruppen.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Utdata** ![ Utgående text efter hämtning av käll resurs gruppens ID](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Vi använder en resurs grupp som redan finns i mål regionen.

 
2. Använd det hämtade ID: t för att lägga till resurs gruppen i samlingen.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Utdata** ![ Utgående text efter att käll resurs gruppen har lagts till i flyttnings samlingen](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Kontrol lera beroenden för att se till att du inte har missat något när du har lagt till resurs gruppen.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Vi ser att det inte finns några utestående beroenden.

    **Utdata** ![ Utgående text efter kontroll av beroenden](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Förbereda resurser

Du måste vanligt vis förbereda resurser i käll regionen före flytten. Exempel:

- Om du vill flytta tillstånds lösa resurser, till exempel virtuella Azure-nätverk, nätverkskort, belastningsutjämnare och nätverks säkerhets grupper, kan du behöva exportera en Azure Resource Manager-mall.
- Om du vill flytta tillstånds känsliga resurser, till exempel virtuella Azure-datorer och SQL-databaser, kan du behöva starta replikering av resurser från källan till mål regionen.

I den här självstudien, eftersom vi håller på att flytta virtuella datorer, måste du förbereda käll resurs gruppen och sedan initiera och bekräfta dess flytt innan vi kan börja förbereda virtuella datorer.

> [!NOTE]
> Om du har en befintlig mål resurs grupp kan du direkt bekräfta flytten för käll resurs gruppen och hoppa över stegen Förbered och initiera flytt.

  
### <a name="prepare-the-source-resource-group"></a>Förbered käll resurs gruppen:

1. Förbered resurs gruppen:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Resultat**

    ![Utgående text efter förberedelse av käll resurs grupp](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Starta flyttningen av käll resurs gruppen.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Utgående text efter att käll resurs gruppen har flyttats](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Genomför flyttningen för käll resurs gruppen.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Resultat**

    ![Skriva text efter att käll resurs gruppen har genomförts](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Förbereda VM-resurser

När du har för berett och flyttat käll resurs gruppen kan vi förbereda VM-resurser för flytten.

1. Verifiera beroenden innan du förbereder VM-resurser.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Resultat**

    ![Skriva text efter att du har verifierat den virtuella datorn innan du förbereder den](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Hämta de beroende resurser som måste förberedas tillsammans med den virtuella datorn.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Resultat**

    ![Utgående text efter hämtning av beroende VM-resurser](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Påbörja förberedelse processen för alla beroende resurser.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Resultat**

    ![Mata ut text efter initating förberedelse av alla resurser](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Du kan ange käll resurs-ID i stället för resurs namn som indataparametrar för förberedelse-cmdleten, samt i cmdletarna initiera flyttning och incheckning. För att göra detta kör du:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Initiera flytt av VM-resurser

1. Kontrol lera att VM-resurserna är i ett *initierings* tillstånd som väntar:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Resultat**

    ![Utgående text efter kontroll av initierings status](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Påbörja flytten:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Resultat**

    ![Utgående text efter att du har påbörjat en resurs flyttning](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Ta bort eller bekräfta?

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kan ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där *initieringen väntar*. Du kan sedan starta flyttningen igen om det behövs.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där *borttagnings källan väntar*, och du kan välja om du vill ta bort den.

### <a name="discard-the-move"></a>Ta bort flytten

Ta bort flytten:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Resultat**

![Utgående text efter att flytten har tagits bort](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Genomför flyttningen

1. Genomför flyttningen på följande sätt:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Resultat**

    ![Mata in text efter att du har genomfört flytten](./media/tutorial-move-region-powershell/commit-move.png)

2. Kontrol lera att alla resurser har flyttats till mål regionen:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Alla resurser är nu i ett *väntande tillstånd för borttagnings källa* i mål regionen.

## <a name="delete-source-resources"></a>Ta bort käll resurser

När du har gjort flytten och verifierat att resurserna fungerar som förväntat i mål regionen, kan du ta bort varje käll resurs i [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [med hjälp av POWERSHELL](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)eller [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Flyttade virtuella Azure-datorer till en annan Azure-region med hjälp av PowerShell.
> * Flyttade resurser som är kopplade till virtuella datorer till en annan region.

Nu kan du prova att flytta virtuella Azure-datorer med hjälp av portalen

> [!div class="nextstepaction"]
> [Flytta virtuella Azure-datorer i portalen](./tutorial-move-region-virtual-machines.md)


