---
title: Metodtips för mallar
description: Beskriver rekommenderade metoder för att redigera Azure Resource Manager mallar (ARM-mallar). Innehåller förslag på hur du undviker vanliga problem när du använder mallar.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: c0b26c300a9474cc5db0b1a7b732c4416a9e6f5f
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696354"
---
# <a name="arm-template-best-practices"></a>Metod tips för ARM-mall

Den här artikeln visar hur du använder rekommenderade metoder när du skapar din Azure Resource Manager-mall (ARM-mall). Dessa rekommendationer hjälper dig att undvika vanliga problem när du använder en ARM-mall för att distribuera en lösning.

## <a name="template-limits"></a>Mall gränser

Begränsa storleken på din mall till 4 MB och varje parameter fil till 64 KB. Gränsen på 4 MB gäller för mallens slutliga tillstånd när den har utökats med iterativa resurs definitioner och värden för variabler och parametrar.

Du är också begränsad till:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal exemplar)
* 64-utmatnings värden
* 24 576 tecken i ett mall uttryck

Du kan överskrida vissa begränsningar för mallar genom att använda en kapslad mall. Mer information finns i [använda länkade och kapslade mallar när du distribuerar Azure-resurser](linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden i ett objekt. Mer information finns i [objekt som parametrar](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

## <a name="resource-group"></a>Resursgrupp

När du distribuerar resurser till en resurs grupp lagrar resurs gruppen metadata om resurserna. Metadata lagras i resurs gruppens plats.

Om resurs gruppens region är tillfälligt otillgänglig, kan du inte uppdatera resurser i resurs gruppen eftersom metadata inte är tillgängliga. Resurserna i andra regioner fungerar fortfarande som förväntat, men du kan inte uppdatera dem. Du kan minimera risken genom att leta upp resurs gruppen och resurserna i samma region.

## <a name="parameters"></a>Parametrar

Informationen i det här avsnittet kan vara till hjälp när du arbetar med [parametrar](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Allmänna rekommendationer för parametrar

* Minimera din användning av parametrar. Använd i stället variabler eller litterala värden för egenskaper som inte behöver anges under distributionen.

* Använd kamel notation-fall för parameter namn.

* Använd parametrar för inställningar som varierar beroende på miljö, t. ex. SKU, storlek eller kapacitet.

* Använd parametrar för resurs namn som du vill ange för enkel identifiering.

* Ange en beskrivning av varje parameter i metadata.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Definiera standardvärden för parametrar som inte är känsliga. Genom att ange ett standardvärde är det enklare att distribuera mallen och användare av din mall ser ett exempel på ett lämpligt värde. Alla standardvärden för en parameter måste vara giltiga för alla användare i standard distributions konfigurationen.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Om du vill ange en valfri parameter ska du inte använda en tom sträng som standardvärde. Använd i stället ett litteralt värde eller ett språk uttryck för att skapa ett värde.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* Använd `allowedValues` sparsamhet. Använd bara det när du måste se till att vissa värden inte ingår i de tillåtna alternativen. Om du använder `allowedValues` för ett för stort kan du blockera giltiga distributioner genom att inte hålla din lista uppdaterad.

* När ett parameter namn i mallen matchar en parameter i PowerShell-distributions kommandot, löser Resource Manager denna namngivnings konflikt genom att lägga till postfix- **FromTemplate** till Template-parametern. Om du till exempel inkluderar en parameter med namnet **ResourceGroupName** i din mall, står den i konflikt med parametern **ResourceGroupName** i cmdleten [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Under distributionen uppmanas du att ange ett värde för **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Säkerhets rekommendationer för parametrar

* Använd alltid parametrar för användar namn och lösen ord (eller hemligheter).

* Använd `securestring` för alla lösen ord och hemligheter. Om du skickar känsliga data i ett JSON-objekt använder du `secureObject` typen. Det går inte att läsa mallparametrar med säker sträng eller säkra objekt typer efter resurs distributionen.

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* Ange inte standardvärden för användar namn, lösen ord eller något värde som kräver en `secureString` typ.

* Ange inte standardvärden för egenskaper som ökar programmets angrepps yta.

### <a name="location-recommendations-for-parameters"></a>Plats rekommendationer för parametrar

* Använd en parameter för att ange plats för resurser och ange standardvärdet till `resourceGroup().location` . Genom att ange en plats parameter kan användare av mallen ange en plats där de har behörighet att distribuera resurser.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* Ange inte `allowedValues` för parametern location. De platser som du anger kanske inte är tillgängliga i alla moln.

* Använd värdet för parametern location för resurser som troligen är på samma plats. Den här metoden minimerar antalet gånger som användarna uppmanas att ange plats information.

* För resurser som inte är tillgängliga på alla platser använder du en separat parameter eller anger ett värde för en literal plats.

## <a name="variables"></a>Variabler

Följande information kan vara till hjälp när du arbetar med [variabler](template-variables.md):

* Använd kamel notation-fall för variabel namn.

* Använd variabler för värden som du behöver använda mer än en gång i en mall. Om ett värde bara används en gång, gör det ett hårdkodat värde att göra mallen lättare att läsa.

* Använd variabler för värden som du skapar i ett komplext arrangemang av mall-funktioner. Mallen är lättare att läsa när det komplexa uttrycket visas i variabler.

* Du kan inte använda funktionen [Reference](template-functions-resource.md#reference) i `variables` avsnittet i mallen. `reference`Funktionen härleder sitt värde från resursens körnings tillstånd. Variablerna löses dock vid den inledande parsningen av mallen. Konstruera värden som behöver `reference` funktionen direkt i eller- `resources` `outputs` avsnittet i mallen.

* Inkludera variabler för resurs namn som måste vara unika.

* Använd en [kopierings slinga i variabler](copy-variables.md) för att skapa ett upprepat mönster av JSON-objekt.

* Ta bort oanvända variabler.

## <a name="api-version"></a>API-version

Ange `apiVersion` egenskapen till en hårdkodad API-version för resurs typen. När du skapar en ny mall rekommenderar vi att du använder den senaste API-versionen för en resurs typ. Information om vilka värden som är tillgängliga finns i [referens för mallar](/azure/templates/).

När din mall fungerar som förväntat, rekommenderar vi att du fortsätter använda samma API-version. Genom att använda samma API-version behöver du inte bekymra dig om att bryta ändringar som kan införas i senare versioner.

Använd inte en parameter för API-versionen. Resurs egenskaper och-värden kan variera beroende på API-version. IntelliSense i en kod redigerare kan inte fastställa rätt schema när API-versionen har angetts till en parameter. Om du skickar i en API-version som inte matchar egenskaperna i mallen kommer distributionen att Miss lyckas.

Använd inte variabler för API-versionen. Använd i synnerhet inte [providers-funktionen](template-functions-resource.md#providers) för att dynamiskt hämta API-versioner under distributionen. Den dynamiskt hämtade API-versionen kanske inte matchar egenskaperna i mallen.

## <a name="resource-dependencies"></a>Resursberoenden

När du bestämmer vilka [beroenden](define-resource-dependency.md) som ska anges använder du följande rikt linjer:

* Använd `reference` funktionen och skicka i resurs namnet för att ange ett implicit beroende mellan resurser som behöver dela en egenskap. Lägg inte till ett explicit `dependsOn` element när du redan har definierat ett implicit beroende. Den här metoden minskar risken för onödiga beroenden. Ett exempel på hur du anger ett implicit beroende finns i [referens-och list funktioner](define-resource-dependency.md#reference-and-list-functions).

* Ange en underordnad resurs som beroende av dess överordnade resurs.

* Resurser med [villkors elementet](conditional-resource-deployment.md) inställt på false tas automatiskt bort från beroende ordningen. Ange beroenden som om resursen alltid har distribuerats.

* Låt beroenden överlappa utan att ange dem explicit. Den virtuella datorn är till exempel beroende av ett virtuellt nätverks gränssnitt och det virtuella nätverks gränssnittet är beroende av ett virtuellt nätverk och offentliga IP-adresser. Den virtuella datorn distribueras därför efter alla tre resurser, men anger inte uttryckligen den virtuella datorn som beroende av alla tre resurserna. Den här metoden klargör beroende ordningen och gör det lättare att ändra mallen senare.

* Om ett värde kan fastställas före distributionen kan du försöka distribuera resursen utan ett beroende. Om ett konfigurations värde till exempel behöver namnet på en annan resurs kanske du inte behöver ett beroende. Den här vägledningen fungerar inte alltid eftersom vissa resurser kontrollerar att den andra resursen finns. Lägg till ett beroende om du får ett fel meddelande.

## <a name="resources"></a>Resurser

Följande information kan vara till hjälp när du arbetar med [resurser](template-syntax.md#resources):

* Om du vill hjälpa andra deltagare att förstå syftet med resursen anger du `comments` för varje resurs i mallen.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* Om du använder en *offentlig slut punkt* i mallen (till exempel en offentlig Azure Blob Storage-slutpunkt) ska du *inte hårdkoda* namn området. Använd `reference` funktionen för att dynamiskt hämta namn området. Du kan använda den här metoden för att distribuera mallen till olika miljöer för offentliga namn områden utan att manuellt ändra slut punkten i mallen. Ange API-versionen till samma version som du använder för lagrings kontot i mallen.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Om lagrings kontot har distribuerats i samma mall som du skapar och namnet på lagrings kontot inte delas med en annan resurs i mallen, behöver du inte ange namn området för providern eller `apiVersion` när du refererar till resursen. I följande exempel visas den förenklade syntaxen.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   Du kan också referera till ett befintligt lagrings konto som finns i en annan resurs grupp.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* Tilldela endast offentliga IP-adresser till en virtuell dator när ett program kräver det. Om du vill ansluta till en virtuell dator (VM) för fel sökning eller för hanterings-och administrations syfte använder du inkommande NAT-regler, en virtuell nätverksgateway eller en hopp.

     Mer information om hur du ansluter till virtuella datorer finns i:

   * [Köra virtuella datorer för en arkitektur på N-nivå i Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av PowerShell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Tillåt extern åtkomst till din virtuella Linux-dator med hjälp av Azure CLI](../../virtual-machines/linux/nsg-quickstart.md)

* `domainNameLabel`Egenskapen för offentliga IP-adresser måste vara unik. `domainNameLabel`Värdet måste vara mellan 3 och 63 tecken långt och följer reglerna som anges i detta reguljära uttryck: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Eftersom `uniqueString` funktionen genererar en sträng som är 13 tecken lång, `dnsPrefixString` är parametern begränsad till 50 tecken.

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* När du lägger till ett lösen ord till ett anpassat skript tillägg, använder du `commandToExecute` egenskapen i `protectedSettings` egenskapen.

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > För att säkerställa att hemligheter krypteras när de skickas som parametrar till virtuella datorer och tillägg, använder du `protectedSettings` egenskapen för relevanta tillägg.

## <a name="use-test-toolkit"></a>Använd test Toolkit

ARM-mallens test verktyg är ett skript som kontrollerar om mallen använder rekommenderade metoder. När mallen inte är kompatibel med rekommenderade metoder returnerar den en lista med varningar med föreslagna ändringar. Test Toolkit kan hjälpa dig att lära dig hur du implementerar bästa praxis i mallen.

När du har slutfört mallen kan du köra test Toolkit för att se om det finns några sätt att förbättra implementeringen. Mer information finns i [använda test verktyg för arm-mall](test-toolkit.md).

## <a name="next-steps"></a>Nästa steg

* Information om mallens struktur finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Rekommendationer om hur du skapar mallar som fungerar i alla moln miljöer i Azure finns i [utveckla arm-mallar för moln konsekvens](templates-cloud-consistency.md).
