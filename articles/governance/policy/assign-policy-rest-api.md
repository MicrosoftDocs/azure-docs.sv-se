---
title: 'Snabb start: ny princip tilldelning med REST API'
description: I den här snabb starten använder du REST API för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219985"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med REST API

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är _inkompatibla_ med principtilldelningen.

REST API används för att skapa och hantera Azure-resurser. Den här guiden använder REST API för att skapa en princip tilldelning och identifiera icke-kompatibla resurser i Azure-miljön.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Om du inte redan har gjort det installerar du [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett verktyg som skickar HTTP-förfrågningar till Azure Resource Manager-baserade REST API: er. Du kan också använda funktionen "Testa funktionen" i REST-dokumentationen eller verktyg som PowerShell: s [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) eller [Postman](https://www.postman.com).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

Kör följande kommando för att skapa en ny principtilldelning:

   - REST API-URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Begärandetext

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

Den föregående slut punkten och begär ande texten använder följande information:

REST API-URI:
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara ett intervall från en hanterings grupp till en enskild resurs. Se till att ersätta `{scope}` med något av följande mönster:
  - Hanterings grupp: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Prenumeration: `/subscriptions/{subscriptionId}`
  - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Klusterresursen `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Namn** – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.

Brödtext i begäran:
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du _granskning av virtuella datorer utan Managed disks tilldelning_.
- **Beskrivning** – en djupare förklaring av vad principen gör eller varför den är tilldelad till det här omfånget.
- **policyDefinitionId** – princip Definitions-ID: t, baserat på vilket du använder för att skapa tilldelningen. I det här fallet är det ID: t för granskning av princip definition för _virtuella datorer som inte använder hanterade diskar_.
- **nonComplianceMessages** – ange det meddelande som visas när en resurs nekas på grund av inkompatibilitet eller inte har utvärderats vara inkompatibel. Mer information finns i [tilldelnings meddelanden som inte uppfyller kraven](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Om du vill visa de resurser som inte är kompatibla med den här nya tilldelningen kör du följande kommando för att hämta resurs-ID: n för de icke-kompatibla resurserna som ska matas in i en JSON-fil:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Resultatet är jämförbart med det du vanligtvis skulle se under **Icke-kompatibla resurser** i Azure-portalens vy.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Ersätt `{scope}` med det omfång som du använde när du först skapade princip tilldelningen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)
