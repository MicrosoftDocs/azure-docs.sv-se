---
title: 'Snabb start: ny princip tilldelning med Java Script'
description: I den här snabb starten använder du Java Script för att skapa en Azure Policy tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91349095"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av Java Script

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar kommer du att identifiera virtuella datorer som _inte är kompatibla_.

JavaScript-biblioteket används för att hantera Azure-resurser från kommando raden eller i skript. Den här guiden förklarar hur du använder JavaScript-biblioteket för att skapa en princip tilldelning.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

- **Node.js**: [Node.js](https://nodejs.org/) version 12 eller högre krävs.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Lägg till princip biblioteken

Om du vill att Java Script ska fungera med Azure Policy måste biblioteken läggas till. Dessa bibliotek fungerar där du kan använda Java Script, inklusive [bash i Windows 10](/windows/wsl/install-win10).

1. Skapa ett nytt Node.js-projekt genom att köra följande kommando.

   ```bash
   npm init -y
   ```

1. Lägg till en referens i yargs-biblioteket.

   ```bash
   npm install yargs
   ```

1. Lägg till en referens till Azure Policy-biblioteken.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Lägg till en referens till Azure Authentication-biblioteket.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Kontrol lera att i _package.jspå_ `@azure/arm-policy` är version **3.1.0** eller högre, `@azure/arm-policyinsights` är version **3.2.0** eller högre, och `@azure/ms-rest-nodeauth` är version **3.0.5** eller högre.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabb starten skapar du en princip tilldelning och tilldelar definitionen **Granska virtuella datorer som inte använder hanterade diskar** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Den här principdefinitionen identifierar resurser som inte uppfyller villkoren i principdefinitionen.

1. Skapa en ny fil med namnet _policyAssignment.js_ och ange följande kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Ange följande kommando i terminalen:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Föregående kommandon använder följande information:

- **subID** – PRENUMERATIONS-ID för autentiserings kontext. Se till att ersätta `{subscriptionId}` med din prenumeration.
- **namn** – det unika namnet för princip tilldelnings objektet. Exemplet ovan använder _audit-VM-manageddisks_.
- **DisplayName** – visnings namn för princip tilldelningen. I det här fallet använder du _granskning av virtuella datorer utan Managed disks tilldelning_.
- **policyDefID** – princip definitions Sök vägen, baserat på vilken du använder för att skapa tilldelningen. I det här fallet är det ID: t för granskning av princip definition för _virtuella datorer som inte använder hanterade diskar_.
- **Beskrivning** – en djupare förklaring av vad principen gör eller varför den är tilldelad till det här omfånget.
- **omfång** – en omfattning avgör vilka resurser eller grupper av resurser som princip tilldelningen tillämpas på. Det kan vara ett intervall från en hanterings grupp till en enskild resurs. Se till att ersätta `{scope}` med något av följande mönster:
  - Hanterings grupp: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Prenumeration: `/subscriptions/{subscriptionId}`
  - Resursgrupp: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Klusterresursen `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetstillstånd.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Nu när din princip tilldelning har skapats kan du identifiera resurser som inte är kompatibla.

1. Skapa en ny fil med namnet _policyState.js_ och ange följande kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Ange följande kommando i terminalen:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

Ersätt `{subscriptionId}` med den prenumeration som du vill visa resultatet för princip tilldelningen med namnet audit-VM-manageddisks som vi skapade i föregående steg. En lista över andra omfattningar och sätt att sammanfatta data finns i [PolicyStates *](/javascript/api/@azure/arm-policyinsights/) metoder.

Ditt resultat liknar följande exempel:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Resultaten matchar det du ser på fliken **Resource Compliance (resurs krav** ) i en princip tilldelning i vyn Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

- Ta bort princip tilldelningen _Granska virtuella datorer utan Managed disks tilldelning_ via portalen. Princip definitionen är en inbyggd, så det finns ingen definition att ta bort.

- Kör följande kommando om du vill ta bort de installerade biblioteken från programmet.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill veta mer om att tilldela princip definitioner för att verifiera att nya resurser är kompatibla fortsätter du till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)