---
title: Fel med RequestDisallowedByPolicy
description: Beskriver orsaken till RequestDisallowedByPolicy-felet när du distribuerar resurser med Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75477673"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy-fel med Azures resurs princip

Den här artikeln beskriver orsaken till RequestDisallowedByPolicy-felet, och innehåller även en lösning för det här felet.

## <a name="symptom"></a>Symptom

Under distributionen kan du få ett **RequestDisallowedByPolicy** -fel som hindrar dig från att skapa resurserna. I följande exempel visas felet:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Felsökning

Använd följande metod för att hämta information om principen som blockerade distributionen:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I PowerShell anger du princip identifieraren som `Id` parameter för att hämta information om principen som blockerade distributionen.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Ange namnet på princip definitionen i Azure CLI:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Lösning

För säkerhet eller efterlevnad kan dina prenumerations administratörer tilldela principer som begränsar hur resurser distribueras. Din prenumeration kan till exempel ha en princip som förhindrar att du skapar offentliga IP-adresser, nätverks säkerhets grupper, User-Defined vägar eller routningstabeller. Fel meddelandet i avsnittet **symptom** visar namnet på principen.
För att lösa det här problemet granskar du resurs principerna och avgör hur du distribuerar resurser som överensstämmer med dessa principer.

Mer information finns i följande artiklar:

- [Vad är Azure Policy?](../../governance/policy/overview.md)
- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)
