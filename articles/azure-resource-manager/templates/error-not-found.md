---
title: Det gick inte att hitta resursen
description: Beskriver hur du löser fel när det inte går att hitta en resurs. Felet kan inträffa när du distribuerar en Azure Resource Manager eller när du vidtar hanteringsåtgärder.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: 5e3a72eaad99721cec9500956179a3ae9d9cf8d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762145"
---
# <a name="resolve-resource-not-found-errors"></a>Lösa fel med att resursen inte hittades

I den här artikeln beskrivs det fel som visas när det inte går att hitta en resurs under en åtgärd. Normalt visas det här felet när du distribuerar resurser. Du ser även det här felet när du utför hanteringsuppgifter Azure Resource Manager kan inte hitta den nödvändiga resursen. Om du till exempel försöker lägga till taggar till en resurs som inte finns får du det här felet.

## <a name="symptom"></a>Symptom

Det finns två felkoder som anger att resursen inte kan hittas. **NotFound-felet** returnerar ett resultat som liknar följande:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**ResourceNotFound-felet** returnerar ett resultat som liknar följande:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager måste hämta egenskaperna för en resurs, men det går inte att hitta resursen i dina prenumerationer.

## <a name="solution-1---check-resource-properties"></a>Lösning 1 – kontrollera resursegenskaper

När du får det här felet när du utför en hanteringsaktivitet kontrollerar du de värden som du anger för resursen. De tre värden som ska kontrolleras är:

* Resursnamn
* Namn på resursgrupp
* Prenumeration

Om du använder PowerShell eller Azure CLI kontrollerar du om du kör kommandot i prenumerationen som innehåller resursen. Du kan ändra prenumerationen med [Set-AzContext eller](/powershell/module/Az.Accounts/Set-AzContext) [az account set](/cli/azure/account#az_account_set). Många kommandon innehåller också en prenumerationsparameter som gör att du kan ange en annan prenumeration än den aktuella kontexten.

Om du har problem med att verifiera egenskaperna loggar du in på [portalen](https://portal.azure.com). Leta upp den resurs som du försöker använda och granska resursnamnet, resursgruppen och prenumerationen.

## <a name="solution-2---set-dependencies"></a>Lösning 2 – ange beroenden

Om du får det här felet när du distribuerar en mall kan du behöva lägga till ett beroende. Resource Manager optimerar distributionen genom att skapa resurser parallellt när det är möjligt. Om en resurs måste distribueras efter en annan resurs måste du använda **elementet dependsOn** i mallen. När du till exempel distribuerar en webbapp måste App Service plan finnas. Om du inte har angett att webbappen är beroende av App Service plan skapar Resource Manager båda resurserna på samma gång. Du får ett felmeddelande om att App Service plan resursen inte kan hittas, eftersom den inte finns ännu när du försöker ange en egenskap för webbappen. Du kan förhindra det här felet genom att ange beroendet i webbappen.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Men du vill undvika att ange beroenden som inte behövs. När du har onödiga beroenden kan du förlänga distributionens varaktighet genom att förhindra att resurser som inte är beroende av varandra distribueras parallellt. Dessutom kan du skapa cirkulära beroenden som blockerar distributionen. [Referensfunktionen](template-functions-resource.md#reference) och [list*-funktionerna](template-functions-resource.md#list) skapar ett implicit beroende på den refererade resursen när resursen distribueras i samma mall och refereras till av dess namn (inte resurs-ID). Därför kan du ha fler beroenden än de beroenden som anges i egenskapen **dependsOn.** Funktionen [resourceId](template-functions-resource.md#resourceid) skapar inte ett implicit beroende eller validerar att resursen finns. [Referensfunktionen](template-functions-resource.md#reference) och [list*-funktionerna](template-functions-resource.md#list) skapar inte ett implicit beroende när resursen refereras till av dess resurs-ID. Om du vill skapa ett implicit beroende skickar du namnet på resursen som distribueras i samma mall.

När du ser beroendeproblem måste du få insikter om ordningen på resursdistributionen. Så här visar du distributionsordningen:

1. Välj distributionshistoriken för resursgruppen.

   ![välj distributionshistorik](./media/error-not-found/select-deployment.png)

2. Välj en distribution i historiken och välj **Händelser**.

   ![välj distributionshändelser](./media/error-not-found/select-deployment-events.png)

3. Granska händelsesekvensen för varje resurs. Var uppmärksam på statusen för varje åtgärd. Följande bild visar till exempel tre lagringskonton som distribuerats parallellt. Observera att de tre lagringskontona startas samtidigt.

   ![parallell distribution](./media/error-not-found/deployment-events-parallel.png)

   Nästa bild visar tre lagringskonton som inte distribueras parallellt. Det andra lagringskontot beror på det första lagringskontot och det tredje lagringskontot är beroende av det andra lagringskontot. Det första lagringskontot startas, godkänns och slutförs innan nästa startas.

   ![sekventiell distribution](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Lösning 3 – hämta extern resurs

När du distribuerar en mall och du behöver hämta en resurs som finns i en annan prenumeration eller resursgrupp använder du [funktionen resourceId](template-functions-resource.md#resourceid). Den här funktionen returnerar för att hämta det fullständigt kvalificerade namnet på resursen.

Parametrarna för prenumeration och resursgrupp i funktionen resourceId är valfria. Om du inte anger dem används som standard den aktuella prenumerationen och resursgruppen. När du arbetar med en resurs i en annan resursgrupp eller prenumeration måste du ange dessa värden.

I följande exempel hämtar resurs-ID:t för en resurs som finns i en annan resursgrupp.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Lösning 4 – hämta hanterad identitet från resurs

Om du distribuerar en resurs som implicit skapar en hanterad identitet [måste](../../active-directory/managed-identities-azure-resources/overview.md)du vänta tills resursen har distribuerats innan du hämtar värden på den hanterade identiteten. Om du skickar det hanterade identitetsnamnet till referensfunktionen Resource Manager försöka matcha referensen innan resursen och identiteten distribueras. [](template-functions-resource.md#reference) Skicka i stället namnet på den resurs som identiteten tillämpas på. Den här metoden säkerställer att resursen och den hanterade identiteten distribueras innan Resource Manager matchar referensfunktionen.

I referensfunktionen använder du för `Full` att hämta alla egenskaper, inklusive den hanterade identiteten.

Mönstret är:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Använd inte mönstret:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Mallen kommer att misslyckas.

Om du till exempel vill hämta huvudnamns-ID:t för en hanterad identitet som tillämpas på en virtuell dator använder du:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Om du vill hämta klient-ID:t för en hanterad identitet som tillämpas på en VM-skalningsuppsättning använder du:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Lösning 5 – kontrollfunktioner

När du distribuerar en mall letar du efter uttryck som använder [referens-](template-functions-resource.md#reference) eller [listKeys-funktionerna.](template-functions-resource.md#listkeys) De värden som du anger varierar beroende på om resursen finns i samma mall, resursgrupp och prenumeration. Kontrollera att du anger de parametervärden som krävs för ditt scenario. Om resursen finns i en annan resursgrupp anger du det fullständiga resurs-ID:t. Om du till exempel vill referera till ett lagringskonto i en annan resursgrupp använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Lösning 6 – efter borttagning av resurs

När du tar bort en resurs kan det ta en kort stund när resursen fortfarande visas i portalen men inte är tillgänglig. Om du väljer resursen får du ett felmeddelande om att resursen inte hittades. Uppdatera portalen för att få den senaste vyn.

Kontakta supporten om problemet kvarstår efter [en kort stund.](https://azure.microsoft.com/support/options/)
