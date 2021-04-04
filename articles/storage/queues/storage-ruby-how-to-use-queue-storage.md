---
title: Använda Queue Storage från ruby-Azure Storage
description: Lär dig hur du använder Azure-Queue Storage för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel som skrivits i Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587670"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Använda Queue Storage från ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

Den här guiden visar hur du utför vanliga scenarier med hjälp av tjänsten Microsoft Azure Queue Storage. Exemplen skrivs med ruby Azure-API: et. De scenarier som beskrivs är att **Infoga**, **Granska**, **Hämta** och **ta bort** Kömeddelanden, samt **skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett ruby-program

Skapa ett ruby-program. Instruktioner finns i [skapa ett ruby-program i App Service på Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring

Om du vill använda Azure Storage måste du ladda ned och använda ruby Azure-paketet, som innehåller en uppsättning bekvämlighets bibliotek som kommunicerar med lagrings REST tjänsterna.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems

1. Använd ett kommandoradsgränssnitt som PowerShell (Windows), Terminal (Mac) eller Bash (Unix).
2. Skriv `gem install Azure` in kommando fönstret för att installera symbolen och beroendena.

### <a name="import-the-package"></a>Importera paketet

Använd din text redigerare och Lägg till följande överst i ruby-filen där du tänker använda Storage:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurera en Azure Storage anslutning

Azure-modulen läser miljövariablerna `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_ACCESS_KEY` information som krävs för att ansluta till ditt Azure Storage-konto. Om de här miljövariablerna inte har angetts måste du ange konto informationen innan du använder `Azure::QueueService` med följande kod:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Du kan hämta dessa värden från ett klassiskt eller Resource Manager-baserat lagringskonto på Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Navigera till det lagrings konto som du vill använda.
3. I bladet **Inställningar** till höger klickar du på **åtkomst nycklar**.
4. I bladet **åtkomst nycklar** som visas visas åtkomst nyckeln 1 och åtkomst nyckeln 2. Du kan använda vilken av nycklarna du vill.
5. Kopiera nyckeln till Urklipp genom att klicka på kopieringsikonen.

## <a name="how-to-create-a-queue"></a>Gör så här: skapa en kö

Följande kod skapar ett `Azure::QueueService` -objekt som gör att du kan arbeta med köer.

```ruby
azure_queue_service = Azure::QueueService.new
```

Använd `create_queue()` metoden för att skapa en kö med det angivna namnet.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här gör du: infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en kö använder du `create_message()` metoden för att skapa ett nytt meddelande och lägger till det i kön.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Gör så här: granska vid nästa meddelande

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa- `peek_messages()` metoden. Som standard `peek_messages()` tittar vi på ett enskilt meddelande. Du kan också ange hur många meddelanden du vill granska.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Gör så här: ta bort nästa meddelande i kö

Du kan ta bort ett meddelande från en kö i två steg.

1. När du anropar `list_messages()` får du nästa meddelande i en kö som standard. Du kan också ange hur många meddelanden du vill hämta. Meddelanden som returneras från `list_messages()` blir osynliga för all annan kod som läser meddelanden från den här kön. Du skickar tids gränsen för synlighet i sekunder som en parameter.
2. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa `delete_message()` .

Den här två stegs processen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen. Dina kod anrop `delete_message()` direkt efter att meddelandet har bearbetats.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här gör du: ändra innehållet i ett köat meddelande

Du kan ändra innehållet i ett meddelande direkt i kön. I följande kod används `update_message()` metoden för att uppdatera ett meddelande. Metoden returnerar en tupel som innehåller pop-kvittot för Queue-meddelandet och ett UTC- `DateTime` värde som representerar när meddelandet ska visas i kön.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Gör så här: ytterligare alternativ för att avköa meddelanden

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

1. Du kan få en batch med meddelandet.
2. Du kan ange en längre eller kortare tids gräns för insikter, vilket gör att koden får mer eller mindre tid att bearbeta varje meddelande fullständigt.

I följande kod exempel används `list_messages()` metoden för att få 15 meddelanden i ett anrop. Sedan skriver den ut och tar bort varje meddelande. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Så här gör du: Hämta Kölängd

Du kan få en uppskattning av antalet meddelanden i kön. `get_queue_metadata()`Metoden returnerar ungefärligt antal meddelanden och andra metadata för kö.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Så här gör du: ta bort en kö

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du `delete_queue()` metoden i objektet köobjekt.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter.

- Besök [Azure Storage teamets blogg](/archive/blogs/windowsazurestorage/)
- Besök [Azure SDK för ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) -lagringsplatsen på GitHub

En jämförelse mellan Azure Queue Storage som diskuteras i den här artikeln och Azure Service Bus köer som diskuteras i [hur du använder Service Bus köer](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)finns i [Azure Queue Storage och Service Bus köer – jämförelse och kontrast](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
