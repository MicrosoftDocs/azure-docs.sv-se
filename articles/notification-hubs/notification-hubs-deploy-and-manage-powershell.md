---
title: Distribuera och hantera Notification Hubs med PowerShell
description: Skapa och hantera Notification Hubs med hjälp av PowerShell för Automation
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454993"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Distribuera och hantera Notification Hub med PowerShell

## <a name="overview"></a>Översikt

Den här artikeln visar hur du använder skapa och hantera Azure Notification Hubs med hjälp av PowerShell. Följande vanliga automatiserings aktiviteter visas i den här artikeln.

- Skapa en meddelandehubb
- Ange autentiseringsuppgifter

Om du också behöver skapa ett nytt Service Bus-namnområde för dina Notification Hub, se [hantera Service Bus med PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md).

Hantering av Notification Hub stöds inte direkt av de cmdletar som ingår i Azure PowerShell. Den bästa metoden från PowerShell är att referera till Microsoft.Azure.NotificationHubs.dll sammansättning. Sammansättningen distribueras med [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Azure är en prenumerations-baserad plattform. Mer information om hur du skaffar en prenumeration finns i [köp alternativ], [medlems erbjudanden]eller [kostnads fri utvärdering].
- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell].
- En allmän förståelse för PowerShell-skript, NuGet-paket och .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inklusive en referens till .NET-sammansättningen för Service Bus

Hantering av Azure-Notification Hubs ingår ännu inte i PowerShell-cmdletar i Azure PowerShell. Om du vill etablera aviserings hubbar kan du använda .NET-klienten som finns i [Microsoft Azure Notification Hubs NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Kontrol lera först att skriptet kan hitta **Microsoft.Azure.NotificationHubs.dll** sammansättning, som installeras som ett NuGet-paket i ett Visual Studio-projekt. Skriptet utför följande steg för att vara flexibelt:

1. Anger sökvägen då den anropades.
2. Bläddrar i sökvägen tills den hittar en mapp med namnet `packages` . Den här mappen skapas när du installerar NuGet-paket för Visual Studio-projekt.
3. Söker rekursivt i `packages` mappen efter en sammansättning med namnet `Microsoft.Azure.NotificationHubs.dll` .
4. Refererar till sammansättningen så att typerna kan användas senare.

Så här implementerar du de här stegen i ett PowerShell-skript:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Skapa `NamespaceManager` klassen

Om du vill etablera Notification Hubs skapar du en instans av klassen [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) från SDK: n.

Du kan använda cmdleten [Get-AzureSBAuthorizationRule] som ingår i Azure PowerShell för att hämta en auktoriseringsregel som används för att ange en anslutnings sträng. En referens till `NamespaceManager` instansen lagras i `$NamespaceManager` variabeln. `$NamespaceManager` används för att etablera en Notification Hub.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Etablering av en ny Notification Hub

Om du vill etablera en ny Notification Hub använder du [.NET-API: et för Notification Hubs].

I den här delen av skriptet ställer du in fyra lokala variabler.

1. `$Namespace`: Ange namnet på det namn område där du vill skapa en Notification Hub.
2. `$Path`: Ange den här sökvägen till namnet på den nya Notification Hub.  Till exempel "MyHub".
3. `$WnsPackageSid`: Ange detta till paket-SID för Windows-appen från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ange den hemliga nyckeln för Windows-appen från [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Dessa variabler används för att ansluta till ditt namn område och skapa ett nytt meddelande nav som kon figurer ATS för att hantera Windows Notification Services-meddelanden (WNS) med WNS-autentiseringsuppgifter för en Windows-app. Information om hur du hämtar paket-SID och hemlig nyckel finns i själv studie kursen [komma igång med Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- Skript utdraget använder `NamespaceManager` objektet för att kontrol lera om Notification Hub identifieras av `$Path` finns.
- Om den inte finns skapar skriptet `NotificationHubDescription` med WNS-autentiseringsuppgifter och skickar det till `NamespaceManager` klass `CreateNotificationHub` metoden.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Ytterligare resurser

- [Hantera Service Bus med PowerShell](../service-bus-messaging/service-bus-manage-with-ps.md)
- [Så här skapar du Service Bus köer, ämnen och prenumerationer med hjälp av ett PowerShell-skript](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [Så här skapar du ett Service Bus-namnområde och en Event Hub med ett PowerShell-skript](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

Vissa färdiga skript är också tillgängliga för nedladdning:

- [Service Bus PowerShell-skript](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Köp alternativ]: https://azure.microsoft.com/pricing/purchase-options/
[Medlems erbjudanden]: https://azure.microsoft.com/pricing/member-offers/
[Kostnadsfri utvärdering]: https://azure.microsoft.com/pricing/free-trial/
[Installera och konfigurera Azure PowerShell]: /powershell/azure/
[.NET API för Notification Hubs]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
