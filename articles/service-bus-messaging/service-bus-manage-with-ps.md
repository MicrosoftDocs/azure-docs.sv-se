---
title: Använd PowerShell för att hantera Azure Service Bus-resurser | Microsoft Docs
description: Den här artikeln förklarar hur du använder Azure PowerShell modul för att skapa och hantera Service Bus entiteter (namn områden, köer, ämnen, prenumerationer).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b6439deb2b86c2ea5b50fe3bdbad89a0875b2acc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065751"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Använd PowerShell för att hantera Service Bus-resurser

Microsoft Azure PowerShell är en skript miljö som du kan använda för att styra och automatisera distributionen och hanteringen av Azure-tjänster. Den här artikeln beskriver hur du använder [PowerShell-modulen Service Bus Resource Manager](/powershell/module/az.servicebus) för att etablera och hantera Service Bus entiteter (namn områden, köer, ämnen och prenumerationer) med hjälp av en lokal Azure PowerShell-konsol eller-skript.

Du kan också hantera Service Bus entiteter med Azure Resource Manager-mallar. Mer information finns i artikeln [skapa Service Bus resurser med Azure Resource Manager-mallar](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar behöver du följande krav:

* En Azure-prenumeration. Mer information om hur du skaffar en prenumeration finns i [köp alternativ][purchase options], [medlems erbjudanden][member offers]eller [kostnads fritt konto][free account].
* En dator med Azure PowerShell. Instruktioner finns i [Kom igång med Azure PowerShell-cmdletar](/powershell/azure/get-started-azureps).
* En allmän förståelse för PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="get-started"></a>Kom igång

Det första steget är att använda PowerShell för att logga in på ditt Azure-konto och din Azure-prenumeration. Följ instruktionerna i [Kom igång med Azure PowerShell cmdlets](/powershell/azure/get-started-azureps) för att logga in på ditt Azure-konto och hämta och få åtkomst till resurserna i din Azure-prenumeration.

## <a name="provision-a-service-bus-namespace"></a>Etablera ett Service Bus-namnområde

När du arbetar med Service Bus-namnområden kan du använda cmdletarna [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)och [set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) .

I det här exemplet skapas några lokala variabler i skriptet. `$Namespace` och `$Location` .

* `$Namespace` är namnet på Service Bus namn området som vi vill arbeta med.
* `$Location` identifierar data centret där vi etablerar namn området.
* `$CurrentNamespace` lagrar referens namn området som vi hämtar (eller skapar).

I ett faktiskt skript `$Namespace` och `$Location` kan skickas som parametrar.

Den här delen av skriptet gör följande:

1. Försöker hämta ett Service Bus-namnområde med det angivna namnet.
2. Om namn området hittas rapporteras vad som hittades.
3. Om namn området inte hittas skapas namn området och sedan hämtas det nyligen skapade namn området.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Skapa en auktoriseringsregel för namn område

I följande exempel visas hur du hanterar auktoriseringsregler för namn områden med cmdletarna [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)och [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) .

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Skapa en kö

Om du vill skapa en kö eller ett ämne utför du en namn områdes kontroll med hjälp av skriptet i föregående avsnitt. Skapa sedan kön:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Ändra köegenskaper

När du har kört skriptet i föregående avsnitt kan du använda cmdleten [set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) för att uppdatera egenskaperna för en kö, som i följande exempel:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Etablering av andra Service Bus entiteter

Du kan använda [Service Bus PowerShell-modulen](/powershell/module/az.servicebus) för att etablera andra entiteter, till exempel ämnen och prenumerationer. Dessa cmdlet: ar liknar de cmdlets för att skapa kö som visas i föregående avsnitt.

## <a name="next-steps"></a>Nästa steg

- Mer [information finns i dokumentationen för](/powershell/module/az.servicebus)den fullständiga Service Bus Resource Manager PowerShell-modulen. Den här sidan visar alla tillgängliga cmdletar.
- Information om hur du använder Azure Resource Manager-mallar finns i artikeln [skapa Service Bus resurser med Azure Resource Manager mallar](service-bus-resource-manager-overview.md).
- Information om [Service Bus .net-hanterings bibliotek](service-bus-management-libraries.md).

Det finns några alternativa sätt att hantera Service Bus entiteter, enligt beskrivningen i dessa blogg inlägg:

* [Så här skapar du Service Bus köer, ämnen och prenumerationer med hjälp av ett PowerShell-skript](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [Så här skapar du ett Service Bus-namnområde och en Event Hub med ett PowerShell-skript](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)
* [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/