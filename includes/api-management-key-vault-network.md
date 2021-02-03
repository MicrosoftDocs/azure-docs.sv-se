---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491027"
---
#### <a name="requirements-for-key-vault-firewall"></a>Krav för Key Vault brand vägg

Om [Key Vault brand vägg](../articles/key-vault/general/network-security.md) är aktive rad i nyckel valvet, är följande ytterligare krav:

* Du måste använda den API Management instansens **systemtilldelade** hanterade identitet för att få åtkomst till nyckel valvet.
* I Key Vault brand vägg aktiverar du alternativet **Tillåt att betrodda Microsoft-tjänster kringgår den här brand väggen** .

#### <a name="virtual-network-requirements"></a>Krav för virtuellt nätverk

Om API Management-instansen distribueras i ett virtuellt nätverk kan du också konfigurera följande nätverks inställningar:

* Aktivera en [tjänst slut punkt](../articles/key-vault/general/overview-vnet-service-endpoints.md) för att Azure Key Vault i API Management-undernätet.
* Konfigurera en regel för nätverks säkerhets grupp (NSG) för att tillåta utgående trafik till AzureKeyVault-och AzureActiveDirectory [-tjänstetaggar](../articles/virtual-network/service-tags-overview.md). 

Mer information finns i nätverks konfigurations information i [Anslut till ett virtuellt nätverk](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).