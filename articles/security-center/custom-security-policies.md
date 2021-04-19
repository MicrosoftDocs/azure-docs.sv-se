---
title: Skapa anpassade säkerhetsprinciper i Azure Security Center | Microsoft Docs
description: Anpassade principdefinitioner i Azure som övervakas av Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a41696ba92757550f9cbaa08ccf78d9a5da528d2
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718907"
---
# <a name="create-custom-security-initiatives-and-policies"></a>Skapa anpassade säkerhetsinitiativ och principer

För att skydda dina system och miljöer skapar Azure Security Center säkerhetsrekommendationer. Dessa rekommendationer baseras på branschens bästa praxis, som ingår i den allmänna, standardsäkerhetsprincip som tillhandahålls till alla kunder. De kan också komma Security Center kunskaper om bransch- och regelstandarder.

Med den här funktionen kan du lägga till egna *anpassade* initiativ. Du får sedan rekommendationer om din miljö inte följer de principer som du skapar. Alla anpassade initiativ som du skapar visas tillsammans med de inbyggda initiativen i instrumentpanelen för regelefterlevnad, enligt beskrivningen i självstudien [Förbättra din regelefterlevnad.](security-center-compliance-dashboard.md)

Som beskrivs i [Azure Policy dokumentationen](../governance/policy/concepts/definition-structure.md#definition-location)måste det vara en hanteringsgrupp eller en prenumeration när du anger en plats för ditt anpassade initiativ. 

> [!TIP]
> En översikt över viktiga begrepp på den här sidan finns i [Vad är säkerhetsprinciper, initiativ och rekommendationer?](security-policy-concept.md).

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Lägga till ett anpassat initiativ i din prenumeration 

1. Från Security Center sidopanelen öppnar du **sidan Säkerhetsprincip.**

1. Välj en prenumeration eller hanteringsgrupp som du vill lägga till ett anpassat initiativ till.

    [![Välja en prenumeration som du ska skapa din anpassade princip för](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Du måste lägga till anpassade standarder på prenumerationsnivå (eller högre) för att de ska kunna utvärderas och visas i Security Center. 
    >
    > När du lägger till en anpassad standard tilldelas ett initiativ *till* det omfånget. Därför rekommenderar vi att du väljer det bredaste omfånget som krävs för tilldelningen.

1. På sidan Säkerhetsprincip under Dina anpassade initiativ klickar du på Lägg **till ett anpassat initiativ.**

    [![Klicka på Lägg till ett anpassat initiativ](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Följande sida visas:

    ![Skapa eller lägga till en princip](media/custom-security-policies/create-or-add-custom-policy.png)

1. På sidan Lägg till anpassade initiativ granskar du listan över anpassade principer som redan har skapats i din organisation. Om du ser en som du vill tilldela till din prenumeration klickar du på Lägg **till**. Om det inte finns något initiativ i listan som uppfyller dina behov kan du hoppa över det här steget.

1. Så här skapar du ett nytt anpassat initiativ:

    1. Klicka **på Skapa ny**.
    1. Ange definitionens plats och namn.
    1. Välj de principer som ska inkluderas och klicka på **Lägg till**.
    1. Ange önskade parametrar.
    1. Klicka på **Spara**.
    1. På sidan Lägg till anpassade initiativ klickar du på Uppdatera. Ditt nya initiativ visas som tillgängligt.
    1. Klicka **på Lägg** till och tilldela den till din prenumeration.

    > [!NOTE]
    > För att skapa nya initiativ krävs autentiseringsuppgifter för prenumerationsägare. Mer information om Azure-roller finns i [Behörigheter i Azure Security Center](security-center-permissions.md).

    Ditt nya initiativ gäller och du kan se effekten på följande två sätt:

    * I sidofältet Security Center du Under Principefterlevnad & du **Regelefterlevnad.** Instrumentpanelen för efterlevnad öppnas för att visa ditt nya anpassade initiativ tillsammans med de inbyggda initiativen.
    
    * Du börjar få rekommendationer om din miljö inte följer de principer som du har definierat.

1. Om du vill se de resulterande rekommendationerna för din princip klickar **du på** Rekommendationer i sidofältet för att öppna sidan med rekommendationer. Rekommendationerna visas med etiketten "Anpassad" och är tillgängliga inom ungefär en timme.

    [![Anpassade rekommendationer](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurera en säkerhetsprincip i Azure Policy med hjälp av REST API

Som en del av den interna integreringen Azure Policy Azure Security Center kan du dra nytta av Azure Policy och REST API skapa principtilldelningar. I följande anvisningar får du hjälp med att skapa principtilldelningar samt anpassa befintliga tilldelningar. 

Viktiga begrepp i Azure Policy: 

- En **principdefinition** är en regel 

- Ett **initiativ** är en samling principdefinitioner (regler) 

- En **tilldelning** är en tillämpning av ett initiativ eller en princip på ett specifikt omfång (hanteringsgrupp, prenumeration osv.) 

Security Center har ett inbyggt initiativ, [Azure Security Benchmark,](https://docs.microsoft.com/security/benchmark/azure/introduction)som innehåller alla dess säkerhetsprinciper. För att Security Center principer för dina Azure-resurser bör du skapa en tilldelning för hanteringsgruppen eller prenumerationen som du vill utvärdera.

Det inbyggda initiativet har alla Security Center principer aktiverade som standard. Du kan välja att inaktivera vissa principer från det inbyggda initiativet. Om du till exempel vill tillämpa Security Center principer utom brandväggen för webbaserade program **ändrar** du värdet för principens effektparameter till **Inaktiverad**.

## <a name="api-examples"></a>API-exempel

Ersätt dessa variabler i följande exempel:

- **{scope}** anger du namnet på den hanteringsgrupp eller prenumeration som du tillämpar principen på
- **{policyAssignmentName}** anger du namnet på den relevanta principtilldelningen
- **{name}** ange ditt namn eller namnet på den administratör som godkände principändringen

Det här exemplet visar hur du tilldelar det inbyggda Security Center för en prenumeration eller hanteringsgrupp
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Det här exemplet visar hur du tilldelar det inbyggda Security Center för en prenumeration, med följande principer inaktiverade: 

- Systemuppdateringar ("systemUpdatesMonitoringEffect") 

- Säkerhetskonfigurationer ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Det här exemplet visar hur du tar bort en tilldelning:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Förbättra dina anpassade rekommendationer med detaljerad information

De inbyggda rekommendationerna som medföljer Azure Security Center innehåller information som allvarlighetsgrad och reparationsanvisningar. Om du vill lägga till den här typen av information i dina anpassade rekommendationer så att den visas i Azure Portal eller var du än kommer åt dina rekommendationer, måste du använda REST API. 

De två typer av information som du kan lägga till är:

- **RemediationDescription** – Sträng
- **Allvarlighetsgrad** – Uppräkning [Låg, Medel, Hög]

Metadata ska läggas till i principdefinitionen för en princip som är en del av det anpassade initiativet. Det bör finnas i egenskapen "securityCenter", enligt följande:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Nedan visas ett exempel på en anpassad princip, inklusive egenskapen metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Ett annat exempel på hur du använder egenskapen securityCenter finns [i det här avsnittet REST API dokumentationen](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar anpassade säkerhetsprinciper. 

Annat relaterat material finns i följande artiklar: 

- [Översikt över säkerhetsprinciper](tutorial-security-policy.md)
- [En lista över inbyggda säkerhetsprinciper](./policy-reference.md)