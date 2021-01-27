---
title: Använda parametrar för att skapa dynamiska modeller
description: Lär dig mer om statiska och dynamiska parametrar och hur du använder dem för att skapa säkra och dynamiska ritningar.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 5dbf7ec02e89eac791ec3e17202a5ab13a04b81d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918542"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Skapa dynamiska modeller via parametrar

En fullständigt definierad skiss med olika artefakter, till exempel resurs grupper, Azure Resource Manager mallar (ARM-mallar), principer eller roll tilldelningar, erbjuder snabb skapande och konsekvent skapande av objekt i Azure. För att möjliggöra flexibel användning av dessa återanvändbara design mönster och behållare stöder Azure-ritningar parametrar. Parametern skapar flexibilitet, både under definition och tilldelning, för att ändra egenskaperna för de artefakter som distribueras av skissen.

Ett enkelt exempel är resurs gruppens artefakt. När en resurs grupp skapas, har den två nödvändiga värden som måste anges: namn och plats. När du lägger till en resurs grupp i din skiss, om det inte finns några parametrar, definierar du namnet och platsen för varje användning av skissen. Den här upprepningen skulle orsaka att varje användning av skissen skapar artefakter i samma resurs grupp. Resurser i den resurs gruppen skulle bli duplicerade och orsaka en konflikt.

> [!NOTE]
> Det är inte ett problem för två olika skisser att inkludera en resurs grupp med samma namn.
> Om det redan finns en resurs grupp som ingår i en skiss fortsätter skissen att skapa relaterade artefakter i den resurs gruppen. Detta kan orsaka en konflikt som två resurser med samma namn och resurs typ får inte finnas i en prenumeration.

Lösningen på det här problemet är parametrar. Med Azure-ritningar kan du definiera värdet för varje egenskap för artefakten under tilldelning till en prenumeration. Parametern gör det möjligt att återanvända en skiss som skapar en resurs grupp och andra resurser inom en enda prenumeration utan att ha någon konflikt.

## <a name="blueprint-parameters"></a>Skissparametrar

Med hjälp av REST API kan parametrar skapas i själva skissen. Parametrarna skiljer sig från parametrarna för var och en av de artefakter som stöds. När en parameter skapas i skissen kan den användas av artefakterna i den ritningen. Ett exempel kan vara prefixet för namngivning av resurs gruppen. Artefakten kan använda skiss parametern för att skapa en "mest dynamisk" parameter. Eftersom parametern även kan definieras under tilldelningen, så ger det här mönstret en konsekvens som kan följa namngivnings regler. Anvisningar finns i [Konfigurera statiska parametrar-parameter för skiss nivå](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Använda secureString-och secureObject-parametrar

Även om en ARM _-mall har_ stöd för parametrar av typerna **secureString** och **SecureObject** , kräver Azure-ritningar att var och en är ansluten till en Azure Key Vault. Det här säkerhets måttet förhindrar den osäkra metoden att lagra hemligheter tillsammans med skissen och uppmuntrar sysselsättning av säkra mönster. Azure-ritningar har stöd för det här säkerhets måttet och identifierar inkluderingen av antingen en säker parameter i en ARM-mall _artefakt_. Tjänsten meddelar sedan under tilldelningen för följande Key Vault egenskaper per identifierad säker parameter:

- Resurs-ID för Key Vault
- Key Vault hemligt namn
- Key Vault hemlig version

Om en **tilldelad hanterad identitet** används för skiss tilldelningen _måste_ den refererade Key Vault finnas i samma prenumeration som skiss definitionen är tilldelad.

Om en **användardefinierad hanterad identitet** används för skiss tilldelningen _kan_ det finnas referenser till Key Vault i en centraliserad prenumeration. Den hanterade identiteten måste beviljas lämpliga rättigheter på Key Vault innan skiss tilldelningen.

> [!IMPORTANT]
> I båda fallen måste Key Vault ha **åtkomst till Azure Resource Manager för att mallar ska kunna distribueras** på sidan **åtkomst principer** . Instruktioner för hur du aktiverar den här funktionen finns i [Key Vault-aktivera mall distribution](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment).

Mer information om Azure Key Vault finns i [Key Vault översikt](../../../key-vault/general/overview.md).

## <a name="parameter-types"></a>Parametertyper

### <a name="static-parameters"></a>Statiska parametrar

Ett parameter värde som definieras i definitionen av en skiss kallas en **statisk parameter**, eftersom varje användning av skissen distribuerar artefakten med det statiska värdet. I resurs grupp exemplet, men det inte är meningsfullt för namnet på resurs gruppen, kan det vara bra för platsen. Sedan skapar varje tilldelning av skissen resurs gruppen, oavsett om den anropas under tilldelningen, på samma plats. Den här flexibiliteten gör att du kan vara selektiv i vad du definierar som krävs jämfört med vad som kan ändras under tilldelningen.

#### <a name="setting-static-parameters-in-the-portal"></a>Ange statiska parametrar i portalen

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Välj en befintlig skiss och välj sedan **Redigera skiss** eller Välj **+ skapa skiss** och fyll i informationen på fliken **grundläggande** .

1. Välj **Nästa: artefakter** eller Välj fliken **artefakter** .

1. Artefakter som läggs till i skissen med parameter alternativ visar **X av Y-parametrar** som är ifyllda i kolumnen **parametrar** . Välj artefakt raden för att redigera artefakt parametrarna.

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="Skärm bild av en skiss definition och X av Y-parametrarna som har fyllts i markerat." border="false":::

1. Sidan **Redigera artefakt** visar värde alternativ som är lämpliga för den valda artefakten. Varje parameter i artefakten har en rubrik, ett värde-ruta och en kryss ruta. Ange att rutan ska avmarkeras för att göra den till en **statisk parameter**. I exemplet nedan är endast _plats_ en **statisk parameter** eftersom den är omarkerad och _resurs gruppens namn_ är markerat.

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="Skärm bild av statiska parametrar i en skiss artefakt." border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>Ange statiska parametrar från REST API

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourMG}` – Ersätt med namnet på din hanteringsgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

##### <a name="blueprint-level-parameter"></a>Skiss nivå parameter

När du skapar en skiss genom REST API är det möjligt att skapa [skiss parametrar](#blueprint-parameters). Det gör du genom att använda följande REST API URI-och Body-format:

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

När du har skapat en parameter på skiss nivå kan den användas för artefakter som läggs till i den ritningen.
Följande REST API exempel skapar en roll tilldelnings artefakt på skissen och använder parametern skiss nivå.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

I det här exemplet använder **principalIds** -egenskapen parametern **Owners** skiss nivå med värdet `[parameters('owners')]` . Att ange en parameter i en artefakt med en skiss nivå parameter är fortfarande ett exempel på en **statisk parameter**. Det går inte att ange parametern skiss nivå under skiss tilldelningen och får samma värde för varje tilldelning.

##### <a name="artifact-level-parameter"></a>Parameter för artefakt nivå

Att skapa **statiska parametrar** i en artefakt liknar varandra, men tar ett rakt värde i stället för att använda `parameters()` funktionen. I följande exempel skapas två statiska parametrar, **TagName** och **tagValue**. Värdet för varje anges direkt och använder inte ett funktions anrop.

- REST API-URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Dynamiska parametrar

Motsatsen till en statisk parameter är en **dynamisk parameter**. Den här parametern är inte definierad i skissen, utan definieras i stället vid varje tilldelning av skissen. I resurs grupps exemplet är användningen av en **dynamisk parameter** begriplig för resurs gruppens namn. Det ger ett annat namn för varje tilldelning av skissen. En lista över skiss funktioner finns i referensen till [skiss funktioner](../reference/blueprint-functions.md) .

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Ange dynamiska parametrar i portalen

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på den skiss som du vill tilldela. Välj **tilldela skiss** eller Välj den skiss som du vill tilldela och Använd sedan knappen **tilldela skiss** .

1. På sidan **tilldela skiss** hittar du avsnittet **artefakt parametrar** . Varje artefakt med minst en **dynamisk parameter** visar artefakten och konfigurations alternativen. Ange de värden som krävs för parametrarna innan du tilldelar skissen. I exemplet nedan är _namn_ en **dynamisk parameter** som måste definieras för fullständig skiss tilldelning.

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="Skärm bild av inställning av dynamiska parametrar under skiss tilldelningen." border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Ange dynamiska parametrar från REST API

Att ange **dynamiska parametrar** under tilldelningen görs genom att ange värdet direkt. I stället för att använda en funktion, t. ex. [parametrar ()](../reference/blueprint-functions.md#parameters), är det angivna värdet en lämplig sträng. Artefakter för en resurs grupp definieras med egenskaperna "Mallnamn", **namn** och **plats** . Alla andra parametrar för den inkluderade artefakten definieras under **parametrar** med **\<name\>** nyckel paret och.  Om skissen har kon figurer ATS för en dynamisk parameter som inte anges under tilldelningen kommer tilldelningen att Miss förväntas.

- REST API-URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Begärandetext

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Nästa steg

- Se listan över [skiss funktioner](../reference/blueprint-functions.md).
- Mer information om [livscykeln för en skiss](./lifecycle.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](./sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](./resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).