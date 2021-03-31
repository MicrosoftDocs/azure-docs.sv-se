---
title: 'Självstudie: hantera frågor i Azure Portal'
description: I den här självstudien skapar du en resurs diagram fråga och delar den nya frågan med andra i Azure Portal.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: 7240a843cfa041137b28284e396c8a4b62a81926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916686"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Självstudie: skapa och dela en Azure Resource Graph-fråga i Azure Portal

Med Azure Resource Graph Explorer kan du spara dina resurs diagram frågor direkt i Azure Portal. Det finns två typer av frågor: _privata_ och _delade_. En privat fråga sparas i Azure Portal inställningar. En delad fråga är en Azure Resource Manager-resurs som kan hanteras med rollbaserad åtkomst kontroll i Azure (Azure RBAC) och skyddas med resurs lås. Båda typerna av frågor är krypterade i vila.

Genom att spara frågor i Azure Portal sparar du tiden som du kan lägga på på annat sätt för dina favorit frågor eller vanliga frågor. När du delar frågor hjälper du ditt team att realisera målen för konsekvens och effektivitet genom upprepning.

I den här självstudien utför du följande aktiviteter:

> [!div class="checklist"]
> - Skapa och ta bort en privat fråga
> - Skapa en delad fråga
> - Identifiera delade frågor
> - Ta bort en delad fråga

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-and-delete-a-private-query"></a>Skapa och ta bort en privat fråga

Privata frågor är tillgängliga och endast synliga för det konto som skapar dem. När de har sparats i ett kontos Azure Portal inställningar kan de skapas, användas och tas bort endast inifrån Azure Portal. En privat fråga är inte en Resource Manager-resurs. Följ dessa steg om du vill skapa en ny privat fråga:

1. Välj **alla tjänster** på menyn portal eller Använd Azure Search-rutan längst upp på alla sidor. Sök efter och välj sedan **resurs diagram Utforskaren**.

1. På fliken **fråga 1** på sidan för Azure Resource Graph-Utforskaren anger du följande fråga:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Välj **Kör fråga** för att se frågeresultaten i det nedre fönstret.

   Mer information om den här frågan finns i [samples – räkna virtuella datorer efter typ av operativ system](../samples/starter.md#count-os).

1. Välj **Spara** eller **Spara som**, ange **antal virtuella datorer efter operativ system** som namn, lämna typen som **privat fråga** och välj sedan **Spara** längst ned i fönstret **Spara fråga** . Flikens rubrik ändras från **fråga 1** för att **räkna virtuella datorer efter operativ system**.

1. Flytta bort från Azure Resource Graph Explorer i Azure Portal och återgå sedan till den. Observera att den sparade frågan inte längre visas och att fliken **fråga 1** har returnerats.

1. Välj **öppna en fråga**. Kontrol lera att typen är en **privat fråga**. Det sparade namnet **antal virtuella datorer per operativ system** visas nu i listan **frågenamn** . När du väljer rubrik länken för den sparade frågan läses den in på en ny flik med frågans namn.

   > [!NOTE] 
   > När en sparad fråga är öppen och fliken visar sitt namn, kan du välja knappen **Spara** om du vill uppdatera den med alla ändringar som har gjorts. Om du vill skapa en ny sparad fråga från den här öppna frågan väljer du **Spara som** och fortsätter som om du sparar en helt ny fråga.

1. Om du vill ta bort den sparade frågan väljer du **öppna en fråga** igen och kontrollerar att fältet **typ** är inställt på **privat fråga**. `Count VMs by OS`Välj **ta bort** (pappers korgs ikon) på raden i den sparade frågan. I bekräftelse dialog rutan väljer du **Ja** för att slutföra borttagningen av frågan.
   Stäng sedan fönstret **öppna en fråga** .

## <a name="create-a-shared-query"></a>Skapa en delad fråga

Till skillnad från en privat fråga är en delad fråga en Resource Manager-resurs. Detta faktum innebär att frågan sparas i en resurs grupp, kan hanteras och kontrol leras med Azure RBAC och kan även skyddas med resurs lås. Som en resurs kan alla som har rätt behörigheter Se och använda den. Följ dessa steg om du vill skapa en ny delad fråga:

1. Välj **alla tjänster** på menyn portal eller Använd Azure Search-rutan längst upp på alla sidor för att söka efter och välja **resurs diagram Utforskaren**.

1. På fliken **fråga 1** på sidan för Azure Resource Graph-Utforskaren anger du följande fråga:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Välj **Kör fråga** för att se frågeresultaten i det nedre fönstret.

   Mer information om den här frågan finns i [samples – räkna virtuella datorer efter typ av operativ system](../samples/starter.md#count-os).

1. Välj **Spara** eller **Spara som**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Spara den nya frågan med knappen Spara" border="false":::

1. I fönstret **Spara fråga** anger du **antal virtuella datorer per OS** som namn.

1. Ändra typen till **delad fråga**, ange beskrivningen till **antal virtuella datorer efter OS-typ** och ange en **prenumeration** för att ange var frågans resurs skapas.

1. Låt kryss rutan **Publicera till resurs-Graph-fråga resurs grupp** vara markerad och **resurs grupps platsen** är inställd på **(USA) västra centrala USA**.

1. Välj **Spara** längst ned i fönstret **Spara fråga** . Flikens rubrik ändras från **fråga 1** för att **räkna virtuella datorer efter operativ system**. Första gången resurs gruppen **Resource-Graph-frågar** används, tar det längre tid än förväntat att spara när resurs gruppen skapas.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Spara den nya frågan som en delad fråga" border="false":::

   > [!NOTE] 
   > Du kan avmarkera kryss rutan **Publicera till resurs-fråga resurs grupp** om du vill ange namnet på en befintlig resurs grupp för att spara den delade frågan i. Om du använder standard resurs gruppen för frågor blir det enklare att upptäcka delade frågor. Det gör även syftet med den här resurs gruppen tydligare. Du kan dock välja att välja en befintlig resurs grupp av säkerhets skäl baserat på befintliga behörigheter.

1. Flytta bort från Azure Resource Graph Explorer i Azure Portal och återgå sedan till den. Observera att den sparade frågan inte längre visas och att fliken **fråga 1** har returnerats.

1. Välj **öppna en fråga**. Kontrol lera att typen har angetts till en **delad fråga** och kombinationen av **prenumeration** och **resurs grupp** matchning där du sparade frågan. Objektet **antal sparade virtuella datorer per operativ system** visas nu i listan över **frågenamn** . Välj rubrik länken för den sparade frågan för att läsa in den på en ny flik med frågans namn. Som en delad fråga visas en ikon på fliken bredvid rubriken som visar den som delad.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Visa den delade frågan med ikonen" border="false":::

   > [!NOTE] 
   > När en sparad fråga är öppen och fliken visar sitt namn, uppdaterar knappen **Spara** med de ändringar som har gjorts. Om du vill skapa en ny sparad fråga väljer du **Spara som** och fortsätter som om du sparar en helt ny fråga.

## <a name="discover-shared-queries"></a>Identifiera delade frågor

Eftersom en delad fråga är en Resource Manager-resurs finns det flera sätt att hitta en:

- Från resurs diagram Utforskaren. Välj **öppna en fråga** och ange typen till en **delad fråga**.
- Från Portal sidan resurs diagram frågor.
- Från resurs gruppen som den delade frågan sparades i.
- Via en fråga till resurs diagram.

### <a name="view-resource-graph-queries"></a>Visa resurs diagram frågor

På sidan Azure Portal visar sidan för resurs diagram frågor delade frågor som det inloggade kontot har åtkomst till. På den här sidan kan du filtrera efter namn, prenumeration, resurs grupp och andra egenskaper för resurs diagram frågan. Du kan också tagga, exportera och ta bort resurs diagram frågor med hjälp av det här gränssnittet.

Om du väljer en av frågorna öppnas sidan resurs diagram fråga. Precis som andra Resource Manager-resurser erbjuder den här sidan en interaktiv översikt tillsammans med aktivitets loggen, åtkomst kontrollen och taggarna. Du kan också använda ett resurs lås direkt från den här sidan.

Gå till sidan för resurs diagram frågor från Portal-menyn genom att välja **alla tjänster** eller genom att använda Azure Search-rutan längst upp på alla sidor. Sök efter och välj **resurs diagram Utforskaren**.

### <a name="list-resource-groups-resources"></a>Visa resurs grupp resurser

Resurs diagram frågan visas bredvid andra resurser som ingår i en resurs grupp.
Om du väljer resurs diagram frågan öppnas sidan för frågan. Meny alternativen tre punkter och snabb menyer (utlösta genom att högerklicka) fungerar på samma sätt som på sidan resurs diagram fråga.

### <a name="query-resource-graph"></a>Fråga resurs diagram

Du kan hitta resurs diagram frågor via en fråga till resurs diagram. Följande resurs diagram fråga begränsar efter typ `Microsoft.ResourceGraph/queries` och används sedan `project` för att bara visa en lista över namnet, tiden som har ändrats och själva frågan:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Köra en delad fråga

En delad fråga för resurs diagram kan köras med `{{shared-query-uri}}` syntaxen (för hands version). Mer information finns i [syntax för delad fråga](../concepts/query-language.md#shared-query-syntax).

## <a name="delete-a-shared-query"></a>Ta bort en delad fråga

Om en delad fråga inte längre behövs tar du bort den. Genom att ta bort en delad fråga tar du bort motsvarande Resource Manager-resurs. Alla instrument paneler som resultat diagrammet var fästa i visar nu ett fel meddelande. När fel meddelandet visas använder du knappen **ta bort från instrument panelen** för att rensa instrument panelen.

Du kan ta bort en delad fråga via följande gränssnitt:
- Sidan frågor för resurs diagram
- Sidan resurs diagram fråga
- Sidan **öppna en fråga** i resurs diagram Utforskaren
- Sidan resurs grupper

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här självstudien tar du bort privata och delade frågor som du har skapat om du inte längre vill ha dem.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat privata och delade frågor. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](../concepts/query-language.md)