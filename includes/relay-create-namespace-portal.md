---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76020978"
---
1. Logga in på [Azure-portalen][Azure portal].
1. Välj **Skapa en resurs**. Välj sedan **integration**  >  **Relay**. Om du inte ser **Relay** i listan väljer du **Visa alla** i det övre högra hörnet.
1. Välj **skapa** och ange ett namn för namn området i fältet **namn** . Azure Portal kontrollerar om namnet är tillgängligt.
1. Välj en Azure-prenumeration där du vill skapa namn området.
1. För [resurs grupp](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)väljer du en befintlig resurs grupp där namn området ska placeras eller skapar en ny.  
1. Välj land eller region där namn området ska vara värd.

    ![Skapa namnområde][create-namespace]

1. Välj **Skapa**. Azure Portal skapar ditt namn område och aktiverar det. Efter ett par minuter etableras resurser för ditt konto i systemet.

### <a name="get-management-credentials"></a>Få autentiseringsuppgifter för hantering

1. Välj **alla resurser** och välj sedan namnet på den nyligen skapade namn rymden.
1. Välj **principer för delad åtkomst**.  
1. Under **Principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.
1. Under **SAS-princip: RootManageSharedAccessKey** väljer du **kopierings** knappen bredvid **primär anslutnings sträng**. Den här åtgärden kopierar anslutnings strängen till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
1. Upprepa föregående steg för att kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
