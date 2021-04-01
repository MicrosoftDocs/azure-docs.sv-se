---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 5c2959a1bf6225c164f8538c3c437e464d834b96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027845"
---
## <a name="create-a-ruby-application"></a>Skapa ett ruby-program
Instruktioner finns i [skapa ett ruby-program på Azure](/previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Om du vill använda Service Bus laddar du ned och använder Azure ruby-paketet, som innehåller en uppsättning av bekvämlighets bibliotek som kommunicerar med lagrings REST tjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv "gem install Azure" i kommando fönstret för att installera symbolen och beroendena.

### <a name="import-the-package"></a>Importera paketet
Använd din favorit text redigerare och Lägg till följande längst upp i ruby-filen där du vill använda lagring:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus anslutning
Använd följande kod för att ange värden för namn området, namnet på nyckeln, nyckeln, undertecknaren och värden:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ange värdet för namn området till det värde som du skapade i stället för hela URL: en. Använd till exempel **"yourexamplenamespace"**, inte "yourexamplenamespace.ServiceBus.Windows.net".

När du arbetar med flera namn rymder kan du skicka nyckeln och dess namn till konstruktorn när du skapar `SharedAccessSigner` objekt

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```