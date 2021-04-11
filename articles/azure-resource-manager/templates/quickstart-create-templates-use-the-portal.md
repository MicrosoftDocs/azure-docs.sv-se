---
title: Distribuera mall – Azure Portal
description: Lär dig hur du skapar din första Azure Resource Manager mall (ARM-mall) med hjälp av Azure Portal och hur du distribuerar den.
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.custom: contperfq1
ms.openlocfilehash: 279dde9c4cd7b19086d29d3ba31a32c60a8cdf0b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612933"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Snabb start: skapa och distribuera ARM-mallar med hjälp av Azure Portal

Lär dig hur du genererar en Azure Resource Manager mall (ARM-mall) med hjälp av Azure Portal och hur du redigerar och distribuerar mallen från portalen. ARM-mallar är JSON-filer som definierar de resurser som du behöver distribuera för din lösning. Information om de begrepp som är kopplade till att distribuera och hantera dina Azure-lösningar finns i [Översikt över mall-distribution](overview.md).

När du har slutfört självstudien kan du distribuera ett Azure Storage-konto. Samma process kan användas till att distribuera andra Azure-resurser.

![Snabb starts Portal diagram för Resource Manager-mall](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="generate-a-template-using-the-portal"></a>Generera en mall med hjälp av portalen

Att skapa en ARM-mall från grunden är inte en enkel uppgift, särskilt om du är nybörjare på Azure-distributionen och du inte är bekant med JSON-formatet. Med hjälp av Azure-portalen kan du konfigurera en resurs, till exempel ett Azure Storage-konto. Innan du distribuerar resursen kan du exportera konfigurationen till en mall. Du kan spara mallen och använda den igen senare.

Många erfarna mallar för utvecklare använder den här metoden för att skapa mallar när de försöker distribuera Azure-resurser som de inte är bekanta med. Mer information om hur du exporterar mallar med hjälp av portalen finns i [Exportera resurs grupper till mallar](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Det andra sättet att hitta en fungerande mall är från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/).

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com) och loggar in.
1. I menyn i Azure-portalen väljer du **Skapa en resurs**.

    ![Välj Skapa en resurs från Azure Portal-menyn](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Skriv **lagrings konto** i sökrutan och tryck sedan på **[ENTER]**.
1. Välj nedpilen bredvid **skapa** och välj sedan **lagrings konto**.

    ![Skapa ett Azure Storage-konto](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)

1. Ange följande information:

    |Name|Värde|
    |----|----|
    |**Resursgrupp**|Välj **Skapa ny** och ge resursgruppen ett namn. På skärmbilden är namnet på resursgruppen *mystorage1016rg*. En resursgrupp är en container för Azure-resurser. Med resursgrupper är det enklare att hantera Azure-resurser. |
    |**Namn**|Ge lagringskontot ett unikt namn. Lagrings kontots namn måste vara unikt i hela Azure och det får bara innehålla gemena bokstäver och siffror. Namnet måste innehålla mellan 3 och 24 tecken. Om du får ett fel meddelande om att "lagrings konto namnet" mystorage1016 "redan är upptaget" kan **&lt; du prova att använda ditt namn>lagring &lt; dagens datum i MMDD>**, till exempel **johndolestorage1016**. Mer information finns i [namngivnings regler och begränsningar](/azure/architecture/best-practices/resource-naming).|

    Du kan använda standardvärdena för resten av egenskaperna.

    ![Skapa en konfiguration för Azure-lagringskonto med hjälp av Azure-portalen](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Vissa av de exporterade mallarna kräver vissa ändringar innan du kan distribuera dem.

1. Välj **Granska + skapa** längst ned på skärmen. Välj inte **skapa** i nästa steg.
1. Välj **Ladda ned en mall för automatisering** längst ned på skärmen. Portalen visar den genererade mallen:

    ![Generera en mall från portalen](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Huvudfönsterrutan visar mallen. Det är en JSON-fil med sex element på den översta nivån – `schema`, `contentVersion`, `parameters`, `variables`, `resources` och `output`. Mer information finns i [förstå strukturen och syntaxen för ARM-mallar](./template-syntax.md)

    Det finns nio definierade parametrar. En av dem heter **storageAccountName**. Den andra markerade delen på den föregående skärmbilden visar hur den här parametern ska anges i mallen. I nästa avsnitt kan du redigera mallen för att använda ett genererat namn för lagringskontot.

    En Azure-resurs har definierats i mallen. Typen är `Microsoft.Storage/storageAccounts` . Ta en titt på hur resursen definieras och definitions strukturen.
1. Välj **Ladda ned** överst på skärmen.
1. Öppna den hämtade ZIP-filen och spara **template.jspå** datorn. I nästa avsnitt använder du ett malldistributionsverktyg för att redigera mallen.
1. Välj fliken **Parameter** för att se de värden som du angav för parametrarna. Anteckna dessa värden, eftersom du behöver dem i nästa avsnitt när du distribuerar mallen.

    ![Skärm bild som visar fliken parameter som visar de värden som du har angett.](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Med både mallfilen och parameter filen kan du skapa en resurs, i den här självstudien, ett Azure Storage-konto.

## <a name="edit-and-deploy-the-template"></a>Redigera och distribuera mallen

Du kan använda Azure-portalen för att utföra viss grundläggande redigering av mallen. I den här snabbstarten använder du portalverktyget *Malldistribution*. *Malldistribution* används i den här självstudien, vilket innebär att du kan slutföra hela självstudien med ett gränssnitt – Azure-portalen. Om du vill redigera en mer komplex mall kan du överväga att använda [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), som ger rikare redigerings funktioner.

> [!IMPORTANT]
> Mall distributionen tillhandahåller ett gränssnitt för att testa enkla mallar. Vi rekommenderar inte att du använder den här funktionen i produktion. Lagra i stället dina mallar i ett Azure Storage-konto eller en käll kods lagrings plats som GitHub.

Azure kräver att varje Azure-tjänst har ett unikt namn. Distributionen kan misslyckas om du anger namnet på ett lagringskonto som redan finns. För att undvika det här problemet ändrar du mallen så att den använder ett mallfunktionsanrop `uniquestring()` för att generera ett unikt lagringskontonamn.

1. Från Azure Portal-menyn i sökrutan skriver du **Deploy** och väljer sedan **distribuera en anpassad mall**.

    ![Azure Resource Manager-mallbibliotek](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)

1. Välj **Bygg en egen mall i redigeraren**.
1. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in template.json som du laddade ned i det sista avsnittet.
1. Gör följande tre ändringar i mallen:

    ![Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Ta bort **storageAccountName** -parametern som visas på föregående skärm bild.
   - Lägg till en variabel med namnet **storageAccountName** som visas på föregående skärm bild:

      ```json
      "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
      ```

      Här används två mallfunktioner: `concat()` och `uniqueString()`.
   - Uppdatera namnelementet för resursen **Microsoft.Storage/storageAccounts** för att använda den nyligen definierade variabeln i stället för parametern:

      ```json
      "name": "[variables('storageAccountName')]",
      ```

      Den slutliga mallen bör se ut så här:

      ```json
      {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "location": {
            "type": "string"
          },
          "accountType": {
            "type": "string"
          },
          "kind": {
            "type": "string"
          },
          "accessTier": {
            "type": "string"
          },
          "minimumTlsVersion": {
            "type": "string"
          },
          "supportsHttpsTrafficOnly": {
            "type": "bool"
          },
          "allowBlobPublicAccess": {
            "type": "bool"
          },
          "allowSharedKeyAccess": {
            "type": "bool"
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
        },
        "resources": [
          {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[parameters('location')]",
            "properties": {
              "accessTier": "[parameters('accessTier')]",
              "minimumTlsVersion": "[parameters('minimumTlsVersion')]",
              "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
              "allowBlobPublicAccess": "[parameters('allowBlobPublicAccess')]",
              "allowSharedKeyAccess": "[parameters('allowSharedKeyAccess')]"
            },
            "dependsOn": [],
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "[parameters('kind')]",
            "tags": {}
          }
        ],
        "outputs": {}
      }
      ```

1. Välj **Spara**.
1. Ange följande värden:

    |Name|Värde|
    |----|----|
    |**Resursgrupp**|Välj det resurs grupps namn som du skapade i det sista avsnittet. |
    |**Region**|Välj en plats för resursgruppen. Välj till exempel **USA, centrala**. |
    |**Plats**|Välj en plats för lagrings kontot. Välj till exempel **USA, centrala**. |
    |**Kontotyp**|Ange **Standard_LRS** för den här snabbstarten. |
    |**Variant**|Ange **StorageV2** för den här snabbstarten. |
    |**Åtkomst nivå**|Ange **Frekvent** för den här snabbstarten. |
    |**Lägsta TLS-version**|Ange **TLS1_0**. |
    |**Endast stöd för HTTPS-trafik**| Välj **true** för den här snabbstarten. |
    |**Tillåt offentlig BLOB-åtkomst**| Välj **false** (falskt) för den här snabbstarten. |
    |**Tillåt åtkomst till delad nyckel**| Välj **true** för den här snabbstarten. |

1. Välj **Granska + skapa**.
1. Välj **Skapa**.
1. Välj klockikonen (meddelanden) längst upp på skärmen för att se distributionsstatus. **Distribution pågår** ska nu visas. Vänta tills distributionen är klar.

    ![Distributionsmeddelande för Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Välj **Gå till resursgrupp** i meddelandefönstret. Du bör se en skärm som liknar följande:

    ![Resursgrupp för distribution av Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Du kan se att distributionen lyckades och att det bara finns ett lagringskonto i resursgruppen. Lagringskontots namn är en unik sträng som genereras av mallen. Mer information om hur du använder Azure Storage-konton finns i [Snabbstart: Ladda upp, ladda ned och lista blobar med Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. I Azure-portalen väljer du **Resursgrupp** på den vänstra menyn.
1. Ange resursgruppens namn i fältet **Filtrera efter namn**.
1. Välj resursgruppens namn.  Du bör se lagringskontot i resursgruppen.
1. Välj **Ta bort resursgrupp** på menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du genererar en mall från Azure-portalen och hur du distribuerar mallen med hjälp av portalen. Den mall som användes i den här snabbstarten är en enkel mall med en Azure-resurs. När mallen är komplex är det enklare att använda Visual Studio Code eller Visual Studio för att utveckla mallen. Mer information om hur du utvecklar mallar finns i vår nya nybörjar-serien:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)
