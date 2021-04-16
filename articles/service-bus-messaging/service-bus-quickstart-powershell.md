---
title: Använda Azure PowerShell för att skapa en Service Bus-kö
description: I den här snabbstarten får du lära dig hur du skapar Service Bus en namnrymd och en kö i namnområdet med hjälp av Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537093"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Använd Azure PowerShell för att skapa Service Bus en namnrymd och en kö
Den här snabbstarten visar hur du skapar Service Bus en namnrymd och en kö med hjälp av Azure PowerShell. Den visar också hur du hämtar autentiseringsuppgifter som ett klientprogram kan använda för att skicka/ta emot meddelanden till/från kön. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration för att slutföra den här snabbstarten. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar. 

I den här snabbstarten använder du Azure Cloud Shell som du kan starta efter att ha loggat in på Azure Portal. Mer information om Azure Cloud Shell finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md). Du kan också [installera](/powershell/azure/install-Az-ps) och använda Azure PowerShell på datorn. 


## <a name="provision-resources"></a>Etablera resurser
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Starta Azure Cloud Shell genom att välja ikonen som visas i följande bild: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Starta Cloud Shell":::
3. Växla från Bash Cloud Shell PowerShell längst **ned** i **fönstret.** 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Växla till PowerShell-läge":::    
4. Kör följande kommando för att skapa en Azure-resursgrupp. Uppdatera resursgruppens namn och plats om du vill. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Kör följande kommando för att skapa Service Bus namnområdet för meddelanden. I det här exemplet `ContosoRG` är resursgruppen som du skapade i föregående steg. `ContosoSBusNS` är namnet på den Service Bus som skapats i den resursgruppen. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Kör följande för att skapa en kö i namnområdet som du skapade i föregående steg. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Hämta den primära anslutningssträngen för namnområdet. Du använder den här anslutningssträngen för att ansluta till kön och skicka och ta emot meddelanden. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Anteckna anslutningssträngen och könamnet. Du använder dem för att skicka och ta emot meddelanden. 


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du Service Bus ett namnområde och en kö i namnområdet. Information om hur du skickar/tar emot meddelanden till/från kön finns i någon av följande snabbstarter i **avsnittet Skicka och ta emot** meddelanden. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
