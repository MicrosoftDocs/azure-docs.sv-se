---
title: Konfigurera din egen nyckel för kryptering av Azure Event Hubs-data i vila
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering av Azure Event Hubs data rest.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c9d1ac1c3a3387600fed80939598baafe658054b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100595991"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurera Kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila med hjälp av Azure Portal
Azure Event Hubs tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Tjänsten Event Hubs använder Azure Storage för att lagra data. Alla data som lagras med Azure Storage krypteras med hjälp av Microsoft-hanterade nycklar. Om du använder din egen nyckel (kallas även Bring Your Own Key (BYOK) eller kundhanterad nyckel) krypteras data fortfarande med hjälp av den Microsoft-hanterade nyckeln, men dessutom krypteras den Microsoft-hanterade nyckeln med hjälp av den Kundhanterade nyckeln. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till Kundhanterade nycklar som används för kryptering av Microsoft-hanterade nycklar. Att aktivera funktionen BYOK är en tids inställnings process i namn området.

> [!NOTE]
> - BYOK-funktionen stöds av [Event Hubs dedikerade kluster med en enda klient](event-hubs-dedicated-overview.md) . Det går inte att aktivera den för standard Event Hubs-namnområden.
> - Kryptering kan bara aktive ras för nya eller tomma namn områden. Om namn området innehåller händelse nav, kommer krypterings åtgärden att Miss läge.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckel användning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du konfigurerar ett nyckel valv med Kundhanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: skapa ett Azure Key Vault med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

> [!IMPORTANT]
> Att använda Kundhanterade nycklar med Azure Event Hubs kräver att nyckel valvet har två obligatoriska egenskaper konfigurerade. De är:  **mjuk borttagning** och **Rensa inte**. De här egenskaperna är aktiverade som standard när du skapar ett nytt nyckel valv i Azure Portal. Men om du behöver aktivera dessa egenskaper i ett befintligt nyckel valv måste du använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera Kundhanterade nycklar
Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Navigera till Event Hubs Dedicated klustret.
1. Välj det namn område som du vill aktivera BYOK på.
1. På sidan **Inställningar** i Event Hubs namn området väljer du **kryptering**. 
1. Välj den **Kundhanterade nyckel krypteringen i vila** , som visas i följande bild. 

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckel valv med nycklar
När du har aktiverat Kundhanterade nycklar måste du associera kundens hanterade nyckel med ditt Azure Event Hubs-namnområde. Event Hubs stöder endast Azure Key Vault. Om du aktiverar alternativet för **kryptering med kundhanterad nyckel** i föregående avsnitt måste nyckeln importeras till Azure Key Vault. Nycklarna måste också ha **mjuk borttagning** och **Rensa inte** konfigurerad för nyckeln. Dessa inställningar kan konfigureras med hjälp av [PowerShell](../key-vault/general/key-vault-recovery.md) eller [CLI](../key-vault/general/key-vault-recovery.md).

1. Om du vill skapa ett nytt nyckel valv följer du [snabb](../key-vault/general/overview.md)starten för Azure Key Vault. Mer information om hur du importerar befintliga nycklar finns i [om nycklar, hemligheter och certifikat](../key-vault/general/about-keys-secrets-certificates.md).
1. Om du vill aktivera både mjuk borttagning och tömning av skydd när du skapar ett valv använder du kommandot AZ-kommandot för att [skapa](/cli/azure/keyvault#az-keyvault-create) ett valv.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensnings skydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du kommandot AZ-kommando för att [Uppdatera](/cli/azure/keyvault#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.
        
        ![Välj knappen generera/importera](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 
    1. Nu kan du välja den här nyckeln för att associera med Event Hubs namn området för kryptering i list rutan. 

        ![Välj nyckel från Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Fyll i informationen om nyckeln och klicka på **Välj**. Detta möjliggör kryptering av den Microsoft-hanterade nyckeln med din nyckel (kundhanterad nyckel). 


## <a name="rotate-your-encryption-keys"></a>Rotera dina krypterings nycklar
Du kan rotera din nyckel i nyckel valvet med hjälp av rotations funktionen för Azure Key Vault. Aktiverings-och utgångs datum kan också ställas in för att automatisera nyckel rotationen. Tjänsten Event Hubs identifierar nya nyckel versioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar
Om du återkallar åtkomst till krypterings nycklarna rensas inte data från Event Hubs. Men det går inte att komma åt data från namn området Event Hubs. Du kan återkalla krypterings nyckeln via åtkomst principen eller genom att ta bort nyckeln. Läs mer om åtkomst principer och skydda nyckel valvet från [säker åtkomst till ett nyckel valv](../key-vault/general/secure-your-key-vault.md).

När krypterings nyckeln har återkallats går Event Hubss tjänsten på det krypterade namn området inte att fungera. Om åtkomsten till nyckeln är aktive rad eller om borttagnings nyckeln återställs, kommer Event Hubs-tjänsten att välja nyckeln så att du kan komma åt data från namn området krypterad Event Hubs.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar 
Att ställa in diagnostikloggar för BYOK-aktiverade namn områden ger dig den information som krävs för åtgärderna. Dessa loggar kan aktive ras och senare strömmas till en Event Hub eller analyseras via Log Analytics eller strömmas till lagring för att utföra anpassad analys. Mer information om diagnostiska loggar finns i [Översikt över Azure Diagnostic-loggar](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Aktivera användar loggar
Följ dessa steg om du vill aktivera loggar för Kundhanterade nycklar.

1. I Azure Portal navigerar du till det namn område där BYOK är aktiverat.
1. Välj **diagnostikinställningar** under **övervakning**.

    ![Välj diagnostikinställningar](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Välj **+ Lägg till diagnostisk inställning**. 

    ![Välj Lägg till diagnostisk inställning](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Ange ett **namn** och välj var du vill strömma loggarna till.
1. Välj **CustomerManagedKeyUserLogs** och **Spara**. Den här åtgärden aktiverar loggarna för BYOK i namn området.

    ![Välj kund hanterat alternativ för nyckel användar loggar](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Logg schema 
Alla loggar lagras i JavaScript Object Notation (JSON)-format. Varje post innehåller sträng fält som använder det format som beskrivs i följande tabell. 

| Name | Beskrivning |
| ---- | ----------- | 
| /TN | Beskrivning av uppgiften som misslyckades. |
| ActivityId | Internt ID som används för spårning. |
| category | Definierar klassificeringen för uppgiften. Om nyckeln från ditt nyckel valv till exempel är inaktive rad, är det en informations kategori eller om en nyckel inte kan vara unwrap, kan den falla under fel. |
| resourceId | Resurs-ID för Azure Resource Manager |
| keyVault | Fullständigt namn på nyckel valvet. |
| key | Nyckel namnet som används för att kryptera Event Hubs-namnområdet. |
| version | Den nyckel version som används. |
| operation | Åtgärden som utförs på nyckeln i ditt nyckel valv. Du kan till exempel inaktivera/aktivera nyckeln, radbrytas eller packa upp |
| kod | Den kod som är kopplad till åtgärden. Exempel: felkod, 404 innebär att nyckeln inte hittades. |
| meddelande | Ett fel meddelande som är kopplat till åtgärden |

Här är ett exempel på loggen för en kundhanterad nyckel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använd Resource Manager-mall för att aktivera kryptering
I det här avsnittet visas hur du utför följande uppgifter med hjälp av **Azure Resource Manager mallar**. 

1. Skapa ett **Event Hubs-namnområde** med en hanterad tjänst identitet.
2. Skapa ett **nyckel valv** och ge tjänst identitets åtkomst till nyckel valvet. 
3. Uppdatera Event Hubs-namnrymden med nyckel valvs informationen (nyckel/värde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Skapa ett Event Hubs kluster och ett namn område med hanterad tjänst identitet
I det här avsnittet visas hur du skapar ett Azure Event Hubs-namnområde med hanterad tjänst identitet med hjälp av en Azure Resource Manager mall och PowerShell. 

1. Skapa en Azure Resource Manager-mall för att skapa ett Event Hubs-namnområde med en hanterad tjänst identitet. Ge filen namnet: **CreateEventHubClusterAndNamespace.jspå**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Skapa en mall med namnet: **CreateEventHubClusterAndNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>` – Namnet på ditt Event Hubs-kluster    
    > - `<EventHubsNamespaceName>` – Namnet på din Event Hubs namn område
    > - `<Location>` – Plats för Event Hubs namn området

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa en Event Hubs namnrymd. Hämta sedan ID: t för Event Hubs namn området om du vill använda det senare. Ersätt `{MyRG}` med namnet på resurs gruppen innan du kör kommandot.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Bevilja Event Hubs namn rymds identitets åtkomst till Key Vault

1. Kör följande kommando för att skapa ett nyckel valv med **rensnings skydd** och **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    ELLER    
    
    Kör följande kommando för att uppdatera ett **befintligt nyckel valv**. Ange värden för resurs gruppen och nyckel valvs namnen innan du kör kommandot. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange åtkomst principen för nyckel valv så att den hanterade identiteten för Event Hubs namn området kan komma åt nyckel värden i nyckel valvet. Använd ID: t för Event Hubs namn området i föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i Event Hubs namnrymd med kundhanterad nyckel från Key Vault
Du har utfört följande steg: 

1. Skapade ett Premium-namnområde med en hanterad identitet.
2. Skapa ett nyckel valv och bevilja åtkomst till hanterad identitet till nyckel valvet. 

I det här steget ska du uppdatera Event Hubs-namnrymden med Key Vault-information. 

1. Skapa en JSON-fil med namnet **CreateEventHubClusterAndNamespace.jspå** med följande innehåll: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Skapa en mall för mallparameter: **UpdateEventHubClusterAndNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>` – Namnet på Event Hubs klustret.        
    > - `<EventHubsNamespaceName>` – Namnet på din Event Hubs namn område
    > - `<Location>` – Plats för Event Hubs namn området
    > - `<KeyVaultName>` – Namn på ditt nyckel valv
    > - `<KeyName>` – Namnet på nyckeln i nyckel valvet

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Kör följande PowerShell-kommando för att distribuera Resource Manager-mallen. Ersätt `{MyRG}` med namnet på din resurs grupp innan du kör kommandot. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Felsöka
Vi rekommenderar att du alltid aktiverar loggar som visas i föregående avsnitt. Det hjälper till att spåra aktiviteter när BYOK-kryptering är aktiverat. Det hjälper också att lösa problemen.

Nedan visas vanliga felkoder som du kan titta efter när BYOK-kryptering är aktiverat.

| Action | Felkod | Resulterande data tillstånd |
| ------ | ---------- | ----------------------- | 
| Ta bort behörigheten wrap/unwrap från ett nyckel valv | 403 |    Otillgänglig |
| Ta bort AAD-rollens medlemskap från ett AAD-huvud som beviljats behörigheten wrap/unwrap | 403 |  Otillgänglig |
| Ta bort en krypterings nyckel från nyckel valvet | 404 | Otillgänglig |
| Ta bort nyckel valvet | 404 | Otillgänglig (Detta förutsätter att mjuk borttagning är aktiverat, vilket är en obligatorisk inställning.) |
| Ändra förfallo perioden på krypterings nyckeln så att den redan har gått ut | 403 |   Otillgänglig  |
| Ändra NBF (inte före) så att nyckel krypterings nyckeln inte är aktiv | 403 | Otillgänglig  |
| Att välja alternativet **Tillåt MSFT-tjänster** för Key Vault-brandväggen eller på annat sätt blockera nätverks åtkomst till nyckel valvet som har krypterings nyckeln | 403 | Otillgänglig |
| Flytta nyckel valvet till en annan klient | 404 | Otillgänglig |  
| Tillfälligt nätverks problem eller DNS/AAD/MSI-avbrott |  | Tillgänglig med krypterings nyckel för cachelagrad data |

> [!IMPORTANT]
> Om du vill aktivera geo-DR på ett namn område som använder BYOK-krypteringen måste det sekundära namn området för länkning vara i ett dedikerat kluster och måste ha en systemtilldelad hanterad identitet aktive rad. Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Event Hubs](event-hubs-about.md)
- [Översikt över Key Vault](../key-vault/general/overview.md)