---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673319"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Hämta Azure Storage anslutnings sträng

Tidigare skapade du ett Azure Storage-konto som ska användas av Function-appen. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. Genom att Hämta inställningen till *local.settings.jsi* filen kan du använda den för att skriva till en lagrings kö i samma konto när funktionen körs lokalt. 

1. Kör följande kommando från roten i projektet och Ersätt `<app_name>` med namnet på din Function-app från föregående snabb start. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *local.settings.jspå* och leta upp värdet med namnet `AzureWebJobsStorage` , som är anslutnings strängen för lagrings kontot. Du använder namnet `AzureWebJobsStorage` och anslutnings strängen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *local.settings.jspå* innehåller hemligheter som hämtats från Azure ska du alltid utesluta den här filen från käll kontroll. Filen *. gitignore* som skapas med ett lokalt Functions-projekt utesluter filen som standard.