---
title: Snabbstart – Använda Azure CLI för att skapa en Service Bus-kö | Microsoft Docs
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att skapa ett Service Bus-namnområde och en kö i det namn området.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95810632"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Använd Azure CLI för att skapa ett Service Bus-namnområde och en kö
Den här snabb starten visar hur du skapar ett Service Bus-namnområde och en kö med hjälp av Azure CLI. Det visar också hur du får autentiseringsuppgifter för auktorisering som ett klient program kan använda för att skicka/ta emot meddelanden till/från kön. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Förutsättningar
Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto][free account] innan du börjar.

I den här snabb starten använder du Azure Cloud Shell som du kan starta när du har loggat in på Azure Portal. Mer information om Azure Cloud Shell finns i [Översikt över Azure Cloud Shell](../cloud-shell/overview.md). Du kan också [Installera](/cli/azure/install-azure-cli) och använda Azure PowerShell på din dator. 

## <a name="provision-resources"></a>Etablera resurser
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Starta Azure Cloud Shell genom att välja ikonen som visas i följande bild. Växla till **bash** -läge om Cloud Shell är i **PowerShell** -läge. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Starta Cloud Shell":::
3. Kör följande kommando för att skapa en Azure-resurs grupp. Uppdatera resurs gruppens namn och plats om du vill. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Kör följande kommando för att skapa ett namn område för Service Bus meddelande tjänst.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Kör följande kommando för att skapa en kö i namn området som du skapade i föregående steg. I det här exemplet `ContosoRG` är resurs gruppen du skapade i föregående steg. `ContosoSBusNS` är namnet på Service Bus namn området som skapas i den resurs gruppen. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Kör följande kommando för att hämta den primära anslutnings strängen för namn området. Du använder den här anslutnings strängen för att ansluta till kön och skicka och ta emot meddelanden. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
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

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

