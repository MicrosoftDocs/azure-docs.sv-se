---
title: 'Snabbstart: Skapa en skiss i portalen'
description: I den här snabbstarten använder du Azure Blueprints för att skapa, definiera och distribuera artefakter via Azure Portal.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 94555b1b73fdac7368c9ac96fa682fb09afc83eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538963"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Snabbstart: Definiera och tilldela en skiss i portalen

När du lär dig hur du skapar och tilldelar skisser kan du definiera vanliga mönster för att utveckla återanvändbara och snabbt distribuerade konfigurationer baserat på ARM-mallar (Azure Resource Manager templates), princip, säkerhet med mera. I den här självstudien lär du dig att använda Azure Blueprints att utföra några av de vanliga uppgifter som rör att skapa, publicera och tilldela en skiss i din organisation. Aktiviteterna är bland annat följande:

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. I det här exemplet skapar du en ny skiss med **namnet MyBlueprint** för att konfigurera roll- och principtilldelningar för prenumerationen. Lägg sedan till en ny resursgrupp och skapa en Resource Manager och rolltilldelning för den nya resursgruppen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **Skissdefinitioner** på sidan till vänster och välj **knappen + Skapa** skiss längst upp på sidan.

   Du kan också **välja Skapa** på sidan **Komma igång** för att gå direkt till att skapa en skiss.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Skärmbild av knappen Skapa skiss på sidan Skissdefinitioner." border="false":::

1. Välj **Börja med en tom** skiss från kortet överst i listan över inbyggda skisser.

1. Ange ett **skissnamn,** till exempel **MyBlueprint**. (Använd upp till 48 bokstäver och siffror, men inga blanksteg eller specialtecken). Lämna **Skissbeskrivning** tomt för tillfället.

1. I rutan **Definitionsplats** väljer du ellipsen till [](../management-groups/overview.md) höger, väljer den hanteringsgrupp eller prenumeration där du vill spara skissen och väljer **Välj**.

1. Kontrollera att informationen är korrekt. Fälten **Skissnamn** **och Definitionsplats** kan inte ändras senare. Välj sedan **Nästa: Artefakter** längst ned på sidan **eller** fliken Artefakter högst upp på sidan.

1. Lägg till en rolltilldelning på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under **Prenumeration**. Fönstret **Lägg till** artefakt öppnas till höger i webbläsaren.

   1. Välj **Rolltilldelning** för **Artefakttyp.**

   1. Under **Roll** väljer du **Deltagare.** Lämna kryssrutan **Lägg till användare, app eller** grupp med kryssrutan som anger en dynamisk parameter.

   1. Välj **Lägg till** för att lägga till artefakten i skissen.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Skärmbild av artefaktalternativen för rolltilldelning för att lägga till en skissdefinition." border="false":::

   > [!NOTE]
   > De flesta artefakter stöder parametrar. En parameter som tilldelas ett värde när skissen skapas är en _statisk parameter_. Om parametern tilldelas under skisstilldelningen är det en _dynamisk parameter_. Mer information finns i [Skissparametrar.](./concepts/parameters.md)

1. Lägg till en principtilldelning på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under rolltilldelningsartefakten.

   1. Välj **Principtilldelning** för **Artefakttyp.**

   1. Ändra **Typ** **till Inbyggd**. I **Sök** anger du **taggen**.

   1. Ändra fokus från **Sök efter** att filtreringen ska ske. Välj **Lägg till tagg och dess standardvärde i resursgrupperna**.

   1. Välj **Lägg till** för att lägga till artefakten i skissen.

1. Välj raden för principtilldelningen Lägg **till tagg och dess standardvärde i resursgrupperna**.

1. Det fönster där du kan ange parametrar för att artefakten som en del av skissdefinitionen öppnas, så att du kan ange parametrar för alla tilldelningar (statiska parametrar) baserade på den här skissen istället för under tilldelningen (dynamiska parametrar). I det här exemplet används dynamiska parametrar under skisstilldelningen, så lämna standardinställningarna och välj **Avbryt.**

1. Lägg till en resursgrupp på prenumerationsnivå:

   1. Välj raden **+ Lägg till artefakt** under **Prenumeration**.

   1. Välj **Resursgrupp** som **Artefakttyp.**

   1. Lämna **rutorna** Artefaktvisningsnamn,  Resursgruppsnamn och Plats tomma, men se till att kryssrutan är markerad för varje parameteregenskap så att de blir dynamiska parametrar. 

   1. Välj **Lägg till** för att lägga till artefakten i skissen.

1. Lägg till en mall under resursgruppen:

   1. Välj **raden + Lägg till** artefakt under **posten ResourceGroup.**

   1. Välj **Azure Resource Manager för** **Artefakttyp,** ange **Visningsnamn för** artefakt till **LagringKonto** och lämna **Beskrivning** tomt.

   1. Klistra in **följande** ARM-mall på fliken Mall i redigeringsrutan. När du har klistrat in mallen väljer du **fliken** Parametrar och observerar att mallparametrarna **storageAccountType** **och location** har identifierats. Varje parameter har identifierades och fylldes i automatiskt, men konfigurerades som en dynamisk parameter.

      > [!IMPORTANT]
      > Om du importerar mallen ser du till att filen bara är JSON och inte innehåller HTML. När du pekar på en URL på GitHub ska du kontrollera att du har valt **RAW** för att hämta den rena JSON-filen och inte den som är omsluten med HTML för visning på GitHub. Ett fel inträffar om den importerade mallen inte är ren JSON.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Avmarkera kryssrutan **storageAccountType** och observera att listrutan endast innehåller värden som ingår i ARM-mallen under **allowedValues**. Markera rutan för att ställa tillbaka den till en dynamisk parameter.

   1. Välj **Lägg till** för att lägga till artefakten i skissen.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Skärmbild av de Resource Manager för mallartefakter för att lägga till en skissdefinition." border="false":::

1. Din färdiga skiss bör se ut som i det följande. Observera att varje artefakt har **_x_ av _y-parametrar_ ifyllda** i **kolumnen** Parametrar. De dynamiska parametrarna anges vid varje tilldelning av skissen.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Skärmbild av en slutförd skissdefinition med varje artefakttyp." border="false":::

1. Nu när alla planerade artefakter har lagts till väljer **du Spara** utkast längst ned på sidan.

## <a name="edit-a-blueprint"></a>Redigera en skiss

I [Skapa en skiss](#create-a-blueprint)har du inte lagt till någon beskrivning eller rolltilldelning i den nya resursgruppen. Du kan åtgärda båda genom att följa dessa steg:

1. Välj **Skissdefinitioner** till vänster på sidan.

1. I listan över skisser högerklickar du på den skiss som du skapade tidigare och väljer **Redigera skiss.**

1. I **Skissbeskrivning** anger du information om skissen och de artefakter som ingår i den. I det här fallet anger du något i följande exempel: Den här skissen anger taggprincip och rolltilldelning för prenumerationen, skapar en ResourceGroup och distribuerar en resursmall och rolltilldelning **till den ResourceGroup.**

1. Välj **Nästa: Artefakter** längst ned på sidan **eller** fliken Artefakter högst upp på sidan.

1. Lägg till en rolltilldelning under resursgruppen:

   1. Välj **raden + Lägg till** artefakt direkt under **posten ResourceGroup.**

   1. Välj **Rolltilldelning** för **Artefakttyp.**

   1. Under **Roll** väljer **du** Ägare och avmarkerar kryssrutan under rutan Lägg **till användare, app eller** grupp.

   1. Sök efter och välj en användare, app eller grupp att lägga till. Den här artefakten använder en statisk parameter som ställs in på samma sätt i alla tilldelningar av den här skissen.

   1. Välj **Lägg till** för att lägga till artefakten i skissen.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Skärmbild av de andra artefaktalternativen för rolltilldelning för att lägga till en skissdefinition." border="false":::

1. Din färdiga skiss bör se ut som i det följande. Observera att den nyligen tillagda rolltilldelningen **visar 1 av 1 parametrar ifyllda**. Det innebär att det är en statisk parameter.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Skärmbild av den andra slutförda skissdefinitionen med den ytterligare rolltilldelningsartefakten." border="false":::

1. Välj **Spara utkast** nu när det har uppdaterats.

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när alla de planerade artefakterna har lagts till i skissen är det dags att publicera den.
Publicering gör skissen tillgänglig för att tilldelas till en prenumeration.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. I listan över skisser högerklickar du på den skiss som du skapade tidigare och väljer **Publicera skiss.**

1. I fönstret som öppnas anger du en **Version** (bokstäver, siffror och bindestreck med en maximal längd på 20 tecken), till exempel **v1**. Du kan också ange text i **Ändra anteckningar,** till exempel **Publicera först.**

1. Välj **Publicera** längst ned på sidan.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

När en skiss har publicerats kan den tilldelas till en prenumeration. Tilldela skissen som du skapade till en av prenumerationerna i hierarkin för hanteringsgruppen. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. I listan över skisser högerklickar du på den skiss som du skapade tidigare (eller väljer ellipsen) och väljer **Tilldela skiss.**

1. På sidan **Tilldela skiss** går du till **listrutan** Prenumeration och väljer de prenumerationer som du vill distribuera skissen till.

   Om det finns Enterprise-erbjudanden som stöds tillgängliga från [Azure-fakturering](../../cost-management-billing/index.yml) aktiveras länken **Skapa nytt** under rutan **Prenumeration**. Följ de här stegen:

   1. Välj länken **Skapa nytt** för att skapa en ny prenumeration i stället för att välja befintliga.

   1. Ange ett **Visningsnamn** för den nya prenumerationen.

   1. Välj det **tillgängliga** erbjudandet i listrutan.

   1. Använd ellipsen för att [välja den hanteringsgrupp](../management-groups/overview.md) som prenumerationen ska vara underordnad.

   1. Välj **Skapa** längst ned på sidan.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Skärmbild av fönstret Skapa en prenumeration och alternativen för den nya prenumerationen." border="false":::

      > [!IMPORTANT]
      > Den nya prenumerationen skapas omedelbart efter att du har valt **Skapa**.

   > [!NOTE]
   > En tilldelning skapas för varje prenumeration som du väljer. Du kan göra ändringar i en enskild prenumerationstilldelning vid ett senare tillfälle utan att framtvinga ändringar för resten av de valda prenumerationerna.

1. För **Tilldelningsnamn** anger du ett unikt namn för den här tilldelningen.

1. I **Plats** väljer du en region för det hanterade identitets- och prenumerationsdistributionsobjektet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

1. Låt **listrutan Skissdefinitionsversion** välja Publicerade **versioner** på **posten v1.** (Standard är den senast publicerade versionen.)

1. Låt standardvärdet **Lås inte** vara för **Lås tilldelning**. Mer information finns i [Resurslåsning för skisser.](./concepts/resource-locking.md)

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Skärmbild av alternativen Låsningstilldelning och hanterad identitet för skisstilldelningen." border="false":::

1. Under **Hanterad** identitet lämnar du standardvärdet **System tilldelad**.

1. För prenumerationsnivåns rolltilldelning **[Användargrupp eller programnamn]: Deltagare**, så sök efter och välj en användare, en app eller grupp.

1. För principtilldelningen på prenumerationsnivå anger du **Taggnamn** **till CostCenter** och **Taggvärde** till **ContosoIT.**

1. För **ResourceGroup** anger du **namnet** **StorageAccount och** **platsen** **USA, östra 2** i listrutan.

   > [!NOTE]
   > För varje artefakt som du lade till under resursgruppen under skissdefinitionen dras artefakten in så att den överensstämmer med den resursgrupp eller det objekt som du ska distribuera den med.
   > Artefakter som antingen inte tar parametrar eller inte har några parametrar som ska definieras vid tilldelningen visas endast för kontextuell information.

1. I ARM-mallen **StorageAccount** väljer **Standard_GRS** för **parametern storageAccountType.**

1. Läs informationsrutan längst ned på sidan och välj sedan **Tilldela**.

## <a name="track-deployment-of-a-blueprint"></a>Spåra distributionen av en skiss

När en skiss har tilldelats till en eller flera prenumerationer händer två saker:

- Skissen läggs till på sidan **Tilldelade skisser** för varje prenumeration.
- Processen för att distribuera alla artefakter som definieras av skissen börjar.

Nu när skissen har tilldelats till en prenumeration kontrollerar du förloppet för distributionen:

1. Välj **Tilldelade skisser** på sidan till vänster.

1. I listan över skisser högerklickar du på den skiss som du tilldelade tidigare och väljer **Visa tilldelningsinformation.**

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Skärmbild av snabbmenyn för skisstilldelning med alternativet Visa tilldelningsinformation valt." border="false":::

1. På sidan **Skisstilldelning** kontrollerar du att alla artefakter har distribuerats och att det inte uppstod några fel under distributionen. Om det har uppstått fel kan [du gå till Felsöka skisser](./troubleshoot/general.md) för att ta reda på vad som gick fel.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Om du inte längre behöver en skisstilldelning tar du bort den från en prenumeration. Skissen kan ha ersatts av en nyare skiss med uppdaterade mönster, principer och design. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Följ dessa steg om du vill ta bort en skisstilldelning:

1. Välj **Tilldelade skisser** på sidan till vänster.

1. I listan över skisser väljer du den skiss som du vill ta bort tilldelning av. Välj sedan **knappen Ta bort tilldelning** av skiss längst upp på sidan.

1. Läs bekräftelsemeddelandet och välj sedan **OK.**

### <a name="delete-a-blueprint"></a>Ta bort en skiss

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Högerklicka på skissen som du vill ta bort och välj Ta **bort skiss.** Välj sedan **Ja** i bekräftelsedialogrutan.

> [!NOTE]
> Om du tar bort en skiss i den här metoden tas även alla publicerade versioner av den valda skissen bort.
> Om du vill ta bort en  enskild version öppnar du skissen, väljer fliken Publicerade versioner, väljer den version som du vill ta bort och väljer sedan **Ta bort den här versionen.** Du kan inte heller ta bort en skiss förrän du har tagit bort alla skisstilldelningar för skissdefinitionen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, tilldelat och tagit bort en skiss med Azure Portal. Om du vill veta mer Azure Blueprints kan du fortsätta till artikeln om livscykeln för en skiss.

> [!div class="nextstepaction"]
> [Lär dig mer om livscykeln för en skiss](./concepts/lifecycle.md)
