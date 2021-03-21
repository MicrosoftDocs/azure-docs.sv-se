---
title: 'Självstudie: skydda nya resurser med lås'
description: I den här självstudien använder du alternativen för resurs lås för Azure-ritningar skrivskyddade och tar inte bort för att skydda nyligen distribuerade resurser.
ms.date: 03/08/2021
ms.topic: tutorial
ms.openlocfilehash: 87da0f5a1fff2feb103b32533c8d314fb7690f80
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485749"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Självstudie: skydda nya resurser med resurs lås för Azure-ritningar

Med [resurs lås](../concepts/resource-locking.md)för Azure-ritningar kan du skydda nyligen distribuerade resurser från att manipuleras, även genom ett konto med _ägar_ rollen. Du kan lägga till det här skyddet i skiss definitionerna för resurser som skapats av en Azure Resource Manager mall (ARM-mall) artefakt. Skiss resurs låset anges under skiss tilldelningen.

I den här självstudien utför du följande steg:

> [!div class="checklist"]
> - Skapa en skiss definition
> - Markera skiss definitionen som **publicerad**
> - Tilldela din skiss definition till en befintlig prenumeration (**Ange resurs lås**)
> - Granska den nya resurs gruppen
> - Ta bort tilldelningen för att ta bort låsen

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-a-blueprint-definition"></a>Skapa en skiss definition

Börja med att skapa skiss definitionen.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **komma igång** till vänster väljer du **skapa** under **skapa en skiss**.

1. Leta upp det **tomma skiss** exemplet överst på sidan. Välj **börja med en tom skiss**.

1. Ange den här informationen på fliken **grundläggande** :

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet. I den här självstudien använder vi namnet **Locked-storageaccount**.
   - **Skiss Beskrivning**: Lägg till en beskrivning för skiss definitionen. Används **för att testa skiss resurs låsning på distribuerade resurser**.
   - **Definitions plats**: Välj knappen med tre punkter (...) och välj sedan den hanterings grupp eller prenumeration som du vill spara din skiss definition till.

1. Välj fliken **artefakter** överst på sidan eller Välj **Nästa: artefakter** längst ned på sidan.

1. Lägg till en resurs grupp på prenumerations nivån:
   1. Välj raden **Lägg till artefakt** under **prenumeration**.
   1. Välj **resurs grupp** under **artefakt typ**.
   1. Ange **visnings namnet för artefakten** till **RGtoLock**.
   1. Lämna rutorna för **resurs grupp namn** och **plats** tomma, men kontrol lera att kryss rutan är markerad för varje egenskap för att göra dem till **dynamiska parametrar**.
   1. Välj **Lägg till** för att lägga till artefakten i skissen.

1. Lägg till en mall under resurs gruppen:
   1. Välj raden **Lägg till artefakt** under posten **RGtoLock** .
   1. Välj **Azure Resource Manager mall** under **artefakt typ**, ange **artefakt visnings namnet** till **StorageAccount** och lämna **beskrivningen** tom.
   1. På fliken **mall** klistrar du in följande arm-mall i redigerings rutan. När du har klistrat in mallen väljer du **Lägg till** för att lägga till artefakten i skissen.

      > [!NOTE]
      > I det här steget definieras de resurser som ska distribueras som låses av skiss resurs låset, men innehåller inte skiss resursens lås. Skiss resursens lås anges som en parameter för skiss tilldelningen.

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
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
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

1. Välj **Spara utkast** längst ned på sidan.

Det här steget skapar skiss definitionen i den valda hanterings gruppen eller prenumerationen.

När du har skickat ett meddelande om att **skiss definitionen lyckades** visas går du till nästa steg.

## <a name="publish-the-blueprint-definition"></a>Publicera skiss definitionen

Skiss definitionen har nu skapats i din miljö. Den skapas i **utkast** läge och måste publiceras innan den kan tilldelas och distribueras.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta ritnings definitionen **Locked-storageaccount** och markera den.

1. Välj **Publicera skiss** överst på sidan. I det nya fönstret till höger anger du **1,0** som **version**. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändrings anteckningar**, till exempel den **första versionen som publiceras för att låsa skissbaserade resurser**. Välj därefter **Publicera** längst ned på sidan.

Det här steget gör det möjligt att tilldela skissen till en prenumeration. När skiss definitionen har publicerats kan du fortfarande göra ändringar. Om du gör ändringar måste du publicera definitionen med ett nytt versions värde för att spåra skillnader mellan versioner av samma skiss definition.

När du **har slutfört ett Portal meddelande om publicerings skissen** visas går du till nästa steg.

## <a name="assign-the-blueprint-definition"></a>Tilldela skiss definitionen

När skiss definitionen har publicerats kan du tilldela den till en prenumeration i hanterings gruppen där du sparade den. I det här steget anger du parametrar för att göra varje distribution av skiss definitionen unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta ritnings definitionen **Locked-storageaccount** och markera den.

1. Välj **Tilldela skiss** överst på skissdefinitionssidan.

1. Ange parametervärden för skisstilldelningen:

   - **Grundläggande inställningar**

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen där du sparade skiss definitionen. Om du väljer fler än en prenumeration skapas en tilldelning för varje prenumeration med hjälp av de parametrar som du anger.
     - **Tilldelnings namn**: namnet fylls i i förväg baserat på skiss definitionens namn. Vi vill att den här tilldelningen ska representera låsning av den nya resurs gruppen, så ändra tilldelnings namnet till **tilldelningen-Locked-storageaccount-TestingBPLocks**.
     - **Plats**: Välj en region där du vill skapa den hanterade identiteten. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
       I den här självstudien väljer du **USA, östra 2**.
     - **Ritnings definitions version**: Välj den publicerade versionen **1,0** av skiss definitionen.

   - **Lås tilldelning**

     Välj Lås läget **skrivskyddad** skiss. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

     > [!NOTE]
     > Det här steget konfigurerar skiss resurs låset på de nyligen distribuerade resurserna.

   - **Hanterad identitet**

     Använd standard alternativet: **systemtilldelad**. Mer information finns i [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md).

   - **Artefaktparametrar**

     De parametrar som definieras i det här avsnittet gäller för den artefakt under vilken de har definierats. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. För varje artefakt anger du parametervärdet till det som visas i kolumnen **värde** .

     |Artefaktnamn|Artefakttyp|Parameternamn|Värde|Beskrivning|
     |-|-|-|-|-|
     |Resurs grupp för RGtoLock|Resursgrupp|Name|TestingBPLocks|Definierar namnet på den nya resurs grupp som skissen ska användas på.|
     |Resurs grupp för RGtoLock|Resursgrupp|Plats|USA, västra 2|Definierar platsen för den nya resurs gruppen som skissen ska användas i.|
     |StorageAccount|Resource Manager-mall|storageAccountType (StorageAccount)|Standard_GRS|SKU för lagring. Standardvärdet är _Standard_LRS_.|

1. När du har angett alla parametrar väljer du **tilldela** längst ned på sidan.

Det här steget distribuerar de definierade resurserna och konfigurerar den valda **Lås tilldelningen**. Det kan ta upp till 30 minuter att använda skiss lås.

När meddelandet **tilldelning av skiss definition lyckades** visas går du till nästa steg.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspektera resurser som distribueras av tilldelningen

Tilldelningen skapar resurs gruppen _TestingBPLocks_ och det lagrings konto som distribueras av arm-mallens artefakt. Den nya resurs gruppen och det valda lås läget visas på sidan tilldelnings information.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **tilldelade ritningar** till vänster. Använd filtren för att hitta **tilldelningen-låsta-storageaccount-TestingBPLocks** skiss tilldelning och markera den.

   Från den här sidan kan vi se att tilldelningen lyckades och att resurserna har distribuerats med det nya ritnings lås läget. Om tilldelningen uppdateras visas information om distributionen av varje definitions version i list rutan **tilldelnings åtgärd** . Du kan välja resurs gruppen för att öppna egenskaps sidan.

1. Välj resurs gruppen **TestingBPLocks** .

1. Välj **åtkomst kontroll sidan (IAM)** till vänster. Välj sedan fliken **roll tilldelningar** .

   Här ser vi att roll tilldelningen _Locked-storageaccount-TestingBPLocks_ har _ägar_ rollen. Den har rollen eftersom den här rollen användes för att distribuera och låsa resurs gruppen.

1. Välj fliken **neka tilldelningar** .

   Skiss tilldelningen skapade en [neka-tilldelning](../../../role-based-access-control/deny-assignments.md) på den distribuerade resurs gruppen för att tvinga det **skrivskyddade** utkast låset. Neka-tilldelningen hindrar någon med lämpliga rättigheter på fliken **roll tilldelningar** från att vidta vissa åtgärder. Neka-tilldelningen påverkar _alla huvud konton_.

   Information om hur du undantar ett huvud konto från en neka-tilldelning finns i [resurs låsning](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment).

1. Välj neka tilldelning och välj sedan sidan **nekade behörigheter** till vänster.

   Neka-tilldelningen förhindrar alla åtgärder med **\* Åtgärds konfigurationen *_ och _*** , men den tillåter Läs åtkomst genom att exkludera **\* /Read** via **NotActions**.

1. I Azure Portal dynamiska länkar väljer du **TestingBPLocks-Access Control (IAM)**. Välj sedan sidan **Översikt** till vänster och sedan knappen **ta bort resurs grupp** . Ange namnet **TestingBPLocks** för att bekräfta borttagningen och välj sedan **ta bort** längst ned i fönstret.

   Det **gick inte att ta bort resurs gruppen TestingBPLocks** . Felet anger att även om ditt konto har behörighet att ta bort resurs gruppen nekas åtkomst av skiss tilldelningen. Kom ihåg att vi valde lås läget **skrivskyddad** skiss under skiss tilldelningen. Skiss låset förhindrar ett konto med behörighet, till och med _ägare_, från att ta bort resursen. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

De här stegen visar att våra distribuerade resurser nu skyddas med skiss lås som förhindrar oönskad borttagning, även från ett konto som har behörighet att ta bort resurserna.

## <a name="unassign-the-blueprint"></a>Ta bort tilldelningen av skissen

Det sista steget är att ta bort skiss definitionens tilldelning. Om du tar bort tilldelningen tas inte de associerade artefakterna bort.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **tilldelade ritningar** till vänster. Använd filtren för att hitta **tilldelningen-låsta-storageaccount-TestingBPLocks** skiss tilldelning och markera den.

1. Välj **ta bort tilldelning av skiss** högst upp på sidan. Läs varningen i bekräftelse dialog rutan och välj sedan **OK**.

   När skiss tilldelningen tas bort, tas även Skissernas lås bort. Resurserna kan återigen tas bort av ett konto med rätt behörighet.

1. Välj **resurs grupper** på Azure-menyn och välj sedan **TestingBPLocks**.

1. Välj sidan **åtkomst kontroll (IAM)** till vänster och välj sedan fliken **roll tilldelningar** .

Säkerheten för resurs gruppen visar att skiss tilldelningen inte längre har _ägar_ åtkomst.

När du **har tagit bort skiss tilldelningen** visas ett Portal meddelande i nästa steg.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort följande resurser när du är klar med den här självstudien:

- _TestingBPLocks_ för resurs grupp
- Skiss definitionen är _låst – storageaccount_

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skyddar nya resurser som distribueras med Azure-ritningar. Om du vill veta mer om Azure-ritningar kan du fortsätta till ritnings livs cykel artikeln.

> [!div class="nextstepaction"]
> [Lär dig mer om skiss livs cykeln](../concepts/lifecycle.md)