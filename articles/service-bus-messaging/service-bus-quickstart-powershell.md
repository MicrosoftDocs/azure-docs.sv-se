---
title: Använda Azure PowerShell för att skapa en Service Bus-kö
description: I den här snabb starten får du lära dig hur du skapar ett Service Bus-namnområde och en kö i namn området med hjälp av Azure PowerShell.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95799110"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Använd Azure PowerShell för att skapa ett Service Bus-namnområde och en kö
Den här snabb starten visar hur du skapar ett Service Bus-namnområde och en kö med hjälp av Azure PowerShell. Det visar också hur du får autentiseringsuppgifter för auktorisering som ett klient program kan använda för att skicka/ta emot meddelanden till/från kön. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har en Azure-prenumeration för att slutföra den här snabb starten. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto][] innan du börjar. 

I den här snabb starten använder du Azure Cloud Shell som du kan starta när du har loggat in på Azure Portal. Mer information om Azure Cloud Shell finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md). Du kan också [Installera](/powershell/azure/install-Az-ps) och använda Azure PowerShell på din dator. 


## <a name="provision-resources"></a>Etablera resurser
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Starta Azure Cloud Shell genom att välja ikonen som visas i följande bild: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Starta Cloud Shell":::
3. Växla från **bash** till **PowerShell** i fönstret längst ned Cloud Shell. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Växla till PowerShell-läge":::    
4. Kör följande kommando för att skapa en Azure-resurs grupp. Uppdatera resurs gruppens namn och plats om du vill. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Kör följande kommando för att skapa ett namn område för Service Bus meddelande tjänst. I det här exemplet `ContosoRG` är resurs gruppen du skapade i föregående steg. `ContosoSBusNS` är namnet på Service Bus namn området som skapas i den resurs gruppen. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Kör följande för att skapa en kö i namn området som du skapade i föregående steg. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Hämta den primära anslutnings strängen för namn området. Du använder den här anslutnings strängen för att ansluta till kön och skicka och ta emot meddelanden. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Anteckna anslutnings strängen och köns namn. Du kan använda dem för att skicka och ta emot meddelanden. 


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en Service Bus namnrymd och en kö i namn området. Information om hur du skickar och tar emot meddelanden till/från kön finns i följande snabb starter i avsnittet **skicka och ta emot meddelanden** . 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[kostnads fritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

