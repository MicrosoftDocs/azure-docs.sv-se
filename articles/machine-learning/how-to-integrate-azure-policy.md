---
title: Granska och hantera efterlevnad av principer
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Policy för att använda inbyggda principer för Azure Machine Learning för att se till att dina arbets ytor är kompatibla med dina krav.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544374"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Granska och hantera Azure Machine Learning med Azure Policy

[Azure policy](../governance/policy/index.yml) är ett styrnings verktyg som gör att du kan se till att Azure-resurserna är kompatibla med dina principer. Med Azure Machine Learning kan du tilldela följande principer:

| Policy | Beskrivning |
| ----- | ----- |
| **Kundhanterad nyckel** | Granska eller tillämpa om arbets ytorna måste använda en kundhanterad nyckel. |
| **Privat länk** | Granska eller tillämpa om arbets ytor använder en privat slut punkt för att kommunicera med ett virtuellt nätverk. |
| **Privat slut punkt** | Konfigurera Azure Virtual Network-undernätet där den privata slut punkten ska skapas. |
| **Privat DNS zon** | Konfigurera den privata DNS-zon som ska användas för den privata länken. |
| **Användartilldelad hanterad identitet** | Granska eller tillämpa om arbets ytor använder en användardefinierad hanterad identitet. |

Principer kan ställas in i olika omfång, t. ex. på prenumerations-eller resurs grupps nivå. Mer information finns i Azure Policy- [dokumentationen](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Inbyggda principer

Azure Machine Learning innehåller en uppsättning principer som du kan använda för vanliga scenarier med Azure Machine Learning. Du kan tilldela dessa princip definitioner till din befintliga prenumeration eller använda dem som grund för att skapa egna anpassade definitioner. En fullständig lista över de inbyggda principerna för Azure Machine Learning finns i [inbyggda principer för Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Använd följande steg för att visa de inbyggda princip definitionerna som är relaterade till Azure Machine Learning:

1. Gå till __Azure policy__ i [Azure Portal](https://portal.azure.com).
1. Välj __definitioner__.
1. I __typ__ väljer du _inbyggd_ och för __kategori__ väljer du __Machine Learning__.

Härifrån kan du välja princip definitioner för att visa dem. När du visar en definition kan du använda länken __tilldela__ för att tilldela principen till en bestämd omfattning och konfigurera parametrarna för principen. Mer information finns i [tilldela en princip – Portal](../governance/policy/assign-policy-portal.md).

Du kan också tilldela principer med hjälp av [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)och [mallar](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Arbets ytans kryptering med kundhanterad nyckel

Kontrollerar om en arbets yta ska krypteras med en kundhanterad nyckel eller en Microsoft-hanterad nyckel för att kryptera mått och metadata. Mer information om hur du använder kundhanterad nyckel finns i avsnittet [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) i artikeln om data kryptering.

Om du vill konfigurera den här principen anger du bedömnings parametern till __audit__ eller __Deny__. Om det är inställt på __granskning__ kan du skapa en arbets yta utan en kundhanterad nyckel och en varnings händelse skapas i aktivitets loggen.

Om principen är inställd på __neka__ kan du inte skapa en arbets yta om den inte anger en kundhanterad nyckel. Försök att skapa en arbets yta utan Kundhanterade nycklar resulterar i ett fel som liknar `Resource 'clustername' was disallowed by policy` och skapar ett fel i aktivitets loggen. Princip identifieraren returneras också som en del av det här felet.

## <a name="workspace-should-use-private-link"></a>Arbets ytan bör använda privat länk

Anger om en privat Azure-länk ska användas i en arbets yta för att kommunicera med Azure Virtual Network. Mer information om hur du använder privat länk finns i [Konfigurera privat länk för en arbets yta](how-to-configure-private-link.md).

Om du vill konfigurera den här principen anger du bedömnings parametern till __audit__ eller __Deny__. Om det är inställt på __granskning__ kan du skapa en arbets yta utan att använda en privat länk och en varnings händelse skapas i aktivitets loggen.

Om principen är inställd på __neka__ kan du inte skapa en arbets yta om den inte använder en privat länk. Om du försöker skapa en arbets yta utan en privat länk uppstår ett fel. Felet loggas också i aktivitets loggen. Princip identifieraren returneras som en del av det här felet.

## <a name="workspace-should-use-private-endpoint"></a>Arbets ytan bör använda privat slut punkt

Konfigurerar en arbets yta för att skapa en privat slut punkt inom det angivna under nätet för ett Azure-Virtual Network.

Om du vill konfigurera den här principen anger du parametern __DeployIfNotExists__. Ange __privateEndpointSubnetID__ till under nätets Azure Resource Manager-ID.
## <a name="workspace-should-use-private-dns-zones"></a>Arbets ytan bör använda privata DNS-zoner

Konfigurerar en arbets yta att använda en privat DNS-zon och åsidosätter standard-DNS-matchningen för en privat slut punkt.

Om du vill konfigurera den här principen anger du parametern __DeployIfNotExists__. Ange __privateDnsZoneId__ till Azure Resource Manager-ID för den privata DNS-zon som ska användas. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>Arbets ytan bör använda användardefinierad hanterad identitet

Kontrollerar om en arbets yta skapas med en systemtilldelad hanterad identitet (standard) eller en tilldelad hanterad identitet. Den hanterade identiteten för arbets ytan används för att få åtkomst till associerade resurser som Azure Storage, Azure Container Registry, Azure Key Vault och Azure Application insikter. Mer information finns i [använda hanterade identiteter med Azure Machine Learning](how-to-use-managed-identities.md).

Om du vill konfigurera den här principen anger du bedömnings parametern till __Granska__, __neka__ eller __inaktive rad__. Om det är inställt på __granskning__ kan du skapa en arbets yta utan att ange en användardefinierad hanterad identitet. En systemtilldelad identitet används och en varnings händelse skapas i aktivitets loggen.

Om principen är inställd på __neka__ kan du inte skapa en arbets yta om du inte anger en tilldelad identitet under skapande processen. Försök att skapa en arbets yta utan att ange en tilldelad identitet resulterar i ett fel. Felet loggas också i aktivitets loggen. Princip identifieraren returneras som en del av det här felet.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om Azure Policy](../governance/policy/overview.md)
* [Inbyggda principer för Azure Machine Learning](policy-reference.md)
* [Arbeta med säkerhets principer med Azure Security Center](../security-center/tutorial-security-policy.md)