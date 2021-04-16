---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531636"
---
* **En offentlig [IPv4-adress](../articles/virtual-network/public-ip-addresses.md#standard)av standard-SKU:n** om klienten använder API-version 2021-01-01-preview eller senare. Den offentliga IP-adressresursen krävs när det virtuella nätverket konfigureras för antingen extern eller intern åtkomst. Med ett internt virtuellt nätverk används den offentliga IP-adressen endast för hanteringsåtgärder. Läs mer om [IP-adresser för API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * IP-adressen måste finnas i samma region och prenumeration som API Management instansen och det virtuella nätverket.

  * IP-adressens värde tilldelas som den virtuella offentliga IPv4-adressen för den API Management instansen i den regionen. 

  * När du byter från ett externt till internt virtuellt nätverk (eller vice versa), ändrar undernät i nätverket eller uppdaterar tillgänglighetszoner för API Management-instansen måste du konfigurera en annan offentlig IP-adress. 

  > [!IMPORTANT]
  > För närvarande använder Azure Portal API-version 2021-01-01 för att skapa eller uppdatera en API Management instans. Du kan ange den här API-versionen med Azure Resource Manager mall eller API Management REST API. Azure CLI och Azure PowerShell stöder för närvarande API-version 2020-12-01.
