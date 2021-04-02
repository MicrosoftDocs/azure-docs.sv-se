---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027950"
---
[Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil. [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) -klassen parsar konfigurations inställningar. Den tolkar inställningar för klient program som körs på Skriv bordet, på en mobil enhet, i en virtuell Azure-dator eller i en Azure-moln tjänst.

Lägg till följande direktiv för att referera till `CloudConfigurationManager` paketet `using` :

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Här följer ett exempel som visar hur du kan hämta en anslutningssträng från en konfigurationsfil:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Du måste inte använda Azure Configuration Manager. Du kan också använda ett API som .NET Framework [ConfigurationManager-klass](/dotnet/api/system.configuration.configurationmanager).