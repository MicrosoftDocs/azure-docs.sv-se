---
title: Det gick inte att hitta resursen
description: Beskriver hur du löser fel när det inte går att hitta en resurs. Felet kan uppstå när du distribuerar en Azure Resource Manager-mall eller när du tar hanterings åtgärder.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 40f3c2ac61b6a6d5fdbcefdf62761668b013b1db
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526254"
---
# <a name="resolve-resource-not-found-errors"></a>Fel vid matchning av resurs hittades inte

I den här artikeln beskrivs det fel som visas när det inte går att hitta en resurs under en åtgärd. Normalt visas det här felet när du distribuerar resurser. Du ser även det här felet när du utför hanterings aktiviteter och Azure Resource Manager inte kan hitta den nödvändiga resursen. Om du till exempel försöker lägga till taggar till en resurs som inte finns får du det här felet.

## <a name="symptom"></a>Symptom

Det finns två felkoder som anger att resursen inte kan hittas. **Notfound** -felet returnerar ett resultat som liknar:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**ResourceNotFound** -felet returnerar ett resultat som liknar:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Orsak

Resource Manager måste hämta egenskaperna för en resurs, men det går inte att hitta resursen i dina prenumerationer.

## <a name="solution-1---check-resource-properties"></a>Lösning 1 – kontrol lera resurs egenskaper

När du får det här felet när du utför en hanterings uppgift kontrollerar du de värden som du anger för resursen. De tre värdena som ska kontrol leras är:

* Resursnamn
* Namn på resursgrupp
* Prenumeration

Om du använder PowerShell eller Azure CLI kontrollerar du om du kör kommandot i den prenumeration som innehåller resursen. Du kan ändra prenumerationen med [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) eller [AZ Account set](/cli/azure/account#az-account-set). Många kommandon innehåller också en prenumerations parameter som gör att du kan ange en annan prenumeration än den aktuella kontexten.

Om du har problem med att verifiera egenskaperna loggar du in på [portalen](https://portal.azure.com). Hitta resursen som du försöker använda och granska resurs namnet, resurs gruppen och prenumerationen.

## <a name="solution-2---set-dependencies"></a>Lösning 2 – ange beroenden

Om du får det här felet när du distribuerar en mall kan du behöva lägga till ett beroende. Resource Manager optimerar distributionen genom att skapa resurser parallellt, när så är möjligt. Om en resurs måste distribueras efter en annan resurs måste du använda **dependsOn** -elementet i mallen. Om du till exempel distribuerar en webbapp måste App Service plan finnas. Om du inte har angett att webbappen är beroende av App Service plan, skapar Resource Manager båda resurserna på samma gång. Du får ett fel meddelande om att det inte finns någon App Service plan resurs, eftersom den inte finns ännu vid försök att ange en egenskap för webbappen. Du undviker det här felet genom att ange beroendet i webbappen.

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

Men du vill undvika att ange beroenden som inte behövs. När du har onödiga beroenden kan du förlänga varaktigheten för distributionen genom att förhindra att resurser som inte är beroende av varandra distribueras parallellt. Dessutom kan du skapa cirkulära beroenden som blockerar distributionen. Funktionen [Reference](template-functions-resource.md#reference) och [list *](template-functions-resource.md#list) Functions skapar ett implicit beroende av den refererade resursen när resursen distribueras i samma mall och refereras till av sitt namn (inte resurs-ID). Det kan därför finnas fler beroenden än de beroenden som anges i egenskapen **dependsOn** . Funktionen [resourceId](template-functions-resource.md#resourceid) skapar inte ett implicit beroende eller kontrollerar att resursen finns. Funktionen [Reference](template-functions-resource.md#reference) och [list *](template-functions-resource.md#list) Functions skapar inte ett implicit beroende när resursen refereras till av resurs-ID: t. Om du vill skapa ett implicit beroende skickar du namnet på den resurs som har distribuerats i samma mall.

När du ser beroende problem måste du få insikt i ordningen på resurs distributionen. Så här visar du ordningen på distributions åtgärder:

1. Välj distributions historik för resurs gruppen.

   ![Välj distributions historik](./media/error-not-found/select-deployment.png)

2. Välj en distribution från historiken och välj **händelser**.

   ![Välj distributions händelser](./media/error-not-found/select-deployment-events.png)

3. Granska händelse ordningen för varje resurs. Var uppmärksam på status för varje åtgärd. Följande bild visar till exempel tre lagrings konton som distribueras parallellt. Observera att de tre lagrings kontona startas samtidigt.

   ![parallell distribution](./media/error-not-found/deployment-events-parallel.png)

   Nästa bild visar tre lagrings konton som inte distribueras parallellt. Det andra lagrings kontot är beroende av det första lagrings kontot och det tredje lagrings kontot är beroende av det andra lagrings kontot. Det första lagrings kontot har startats, godkänts och slutförts innan nästa påbörjas.

   ![sekventiell distribution](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Lösning 3 – Hämta extern resurs

När du distribuerar en mall och behöver skaffa en resurs som finns i en annan prenumeration eller resurs grupp, använder du [resourceId-funktionen](template-functions-resource.md#resourceid). Den här funktionen returnerar för att hämta det fullständigt kvalificerade namnet på resursen.

Parametrarna för prenumeration och resurs grupp i resourceId-funktionen är valfria. Om du inte anger dem används den aktuella prenumerationen och resurs gruppen som standard. När du arbetar med en resurs i en annan resurs grupp eller prenumeration ska du se till att du anger dessa värden.

I följande exempel hämtas resurs-ID: t för en resurs som finns i en annan resurs grupp.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Lösning 4 – Hämta hanterad identitet från resurs

Om du distribuerar en resurs som implicit skapar en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md)måste du vänta tills resursen har distribuerats innan du hämtar värden för den hanterade identiteten. Om du skickar namnet på den hanterade identiteten till [referens](template-functions-resource.md#reference) funktionen försöker Resource Manager matcha referensen innan resursen och identiteten distribueras. Skicka i stället namnet på den resurs som identiteten tillämpas på. Den här metoden säkerställer att resursen och den hanterade identiteten distribueras innan Resource Manager löser referens funktionen.

I funktionen Reference använder `Full` du för att hämta alla egenskaper, inklusive den hanterade identiteten.

Mönstret är:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Använd inte mönstret:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Mallen kommer inte att fungera.

Om du till exempel vill hämta ägar-ID för en hanterad identitet som tillämpas på en virtuell dator använder du:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Eller så använder du för att hämta klient-ID för en hanterad identitet som tillämpas på en skalnings uppsättning för virtuella datorer:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Lösning 5 – kontrol lera funktioner

När du distribuerar en mall ska du söka efter uttryck som använder [referens](template-functions-resource.md#reference) -eller [listnycklar](template-functions-resource.md#listkeys) -funktionerna. Värdena som du anger varierar beroende på om resursen finns i samma mall, resurs grupp och prenumeration. Kontrol lera att du har angett de parameter värden som krävs för ditt scenario. Om resursen finns i en annan resurs grupp anger du det fullständiga resurs-ID: t. Om du till exempel vill referera till ett lagrings konto i en annan resurs grupp använder du:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```
