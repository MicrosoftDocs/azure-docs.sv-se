---
title: Använda Azure Queue Storage från PowerShell – Azure Storage
description: Utföra åtgärder på Azure Queue Storage via PowerShell. Med Azure Queue Storage kan du lagra ett stort antal meddelanden som är tillgängliga via HTTP/HTTPS.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1436114a070dff7120a7de10937cbfa58644d305
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276254"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>Använda Azure Queue Storage från PowerShell

Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden som kan nås från var som helst i världen via HTTP eller HTTPS. Detaljerad information finns i [Introduktion till Azure Queue Storage](storage-queues-introduction.md). Den här instruktions artikeln beskriver vanliga Queue Storage åtgärder. Lär dig att:

> [!div class="checklist"]
>
> - Skapa en kö
> - Hämta en kö
> - Lägg till ett meddelande
> - Läsa ett meddelande
> - Ta bort ett meddelande
> - Ta bort en kö

Den här instruktions guiden kräver Azure PowerShell ( `Az` ) modul v 0,7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den aktuella installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

Det finns inga PowerShell-cmdletar för data planet för köer. Om du vill utföra data Plans åtgärder, till exempel lägga till ett meddelande, läsa ett meddelande och ta bort ett meddelande, måste du använda klient biblioteket för .NET-lagring som det visas i PowerShell. Du skapar ett meddelande objekt och sedan kan du använda kommandon som `AddMessage` till exempel för att utföra åtgärder på meddelandet. Den här artikeln visar hur du gör.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. Den här övningen kommer att använda `eastus` . Lagra detta i variabeln `location` för framtida bruk.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Lagra resurs grupps namnet i en variabel för framtida användning. I det här exemplet ska vi skapa en resursgrupp med namnet `howtoqueuesrg` i regionen `eastus`.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett allmänt standard lagrings konto med lokalt redundant lagring (LRS) med [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Hämta lagrings konto kontexten som definierar det lagrings konto som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Skapa en kö

I följande exempel upprättas först en anslutning till Azure Storage med hjälp av lagrings kontots kontext, som innehåller lagrings kontots namn och dess åtkomst nyckel. Sedan anropas [New-AzStorageQueue-](/powershell/module/az.storage/new-azstoragequeue) cmdleten för att skapa en kö med namnet `howtoqueue` .

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Information om namngivnings konventioner för Azure Queue Storage finns i [namngivnings köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

## <a name="retrieve-a-queue"></a>Hämta en kö

Du kan fråga efter och hämta en speciell kö eller en lista över alla köer i ett lagrings konto. Följande exempel visar hur du hämtar alla köer i lagrings kontot och en speciell kö. båda kommandona använder cmdleten [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) .

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Åtgärder som påverkar faktiska meddelanden i kön använder klient biblioteket för .NET-lagring som det visas i PowerShell. Om du vill lägga till ett meddelande i en kö skapar du en ny instans av objektet meddelande, [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) klass. Anropa sedan [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) metoden. En `CloudQueueMessage` kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris.

Följande exempel visar hur du lägger till ett meddelande i kön.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Om du använder [Azure Storage Explorer](https://storageexplorer.com)kan du ansluta till ditt Azure-konto och Visa köerna i lagrings kontot och öka detalj nivån i en kö för att visa meddelandena i kön.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Läs ett meddelande från kön och ta sedan bort det

Meddelanden läses in i bästa möjliga försök från första och följande. Detta är inte garanterat. När du läser meddelandet från kön blir det osynligt för alla andra processer som tittar på kön. Detta säkerställer att om din kod inte kan bearbeta meddelandet på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen.

Denna **timeout för insikt** definierar hur länge meddelandet förblir osynligt innan det blir tillgängligt igen för bearbetning. Standardvärdet är 30 sekunder.

Koden läser ett meddelande från kön i två steg. När du anropar [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) -metoden får du nästa meddelande i kön. Ett meddelande som returnerades från `GetMessage` blir osynligt för all annan kod som läser meddelanden från den här kön. Om du vill slutföra borttagningen av meddelandet från kön anropar du- [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) metoden.

I följande exempel läser du igenom de tre meddelandena i kön och väntar sedan 10 sekunder (tids gräns för insynlighet). Sedan kan du läsa de tre meddelandena igen och ta bort meddelandena när du har läst dem genom att anropa `DeleteMessage` . Om du försöker läsa kön när meddelandena har tagits bort `$queueMessage` returneras den som `$null` .

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Ta bort en kö

Anropa cmdleten om du vill ta bort en kö och alla meddelanden som finns i den `Remove-AzStorageQueue` . I följande exempel visas hur du tar bort den aktuella kön som används i den här övningen med hjälp av `Remove-AzStorageQueue` cmdleten.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen om du vill ta bort alla till gångar som du har skapat i den här övningen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar den bort det lagrings konto som skapats och själva resurs gruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig om grundläggande Queue Storage hantering med PowerShell, inklusive hur du:

> [!div class="checklist"]
>
> - Skapa en kö
> - Hämta en kö
> - Lägg till ett meddelande
> - Läs nästa meddelande
> - Ta bort ett meddelande
> - Ta bort en kö

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-cmdletar

- [Storage PowerShell cmdletar](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

- [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
