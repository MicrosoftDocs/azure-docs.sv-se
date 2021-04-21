---
title: Konfigurera din egen nyckel för kryptering Azure Event Hubs vilodata
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering Azure Event Hubs data rest.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c608cd53c8ec71f219f015bab557d2b9b143d1c5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752020"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurera kund hanterade nycklar för kryptering Azure Event Hubs vilodata med hjälp av Azure Portal
Azure Event Hubs kryptering av vilodata med Azure Storage Service Encryption (Azure SSE). Tjänsten Event Hubs använder Azure Storage för att lagra data. Alla data som lagras med Azure Storage krypteras med Microsoft-hanterade nycklar. Om du använder en egen nyckel (kallas även Bring Your Own Key (BYOK) eller kund hanterad nyckel) krypteras data fortfarande med den Microsoft-hanterade nyckeln, men dessutom krypteras den Microsoft-hanterade nyckeln med hjälp av den kund hanterade nyckeln. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till kund hanterade nycklar som används för att kryptera Microsoft-hanterade nycklar. Aktivering av BYOK-funktionen är en konfigurationsprocess en gång i namnområdet.

> [!NOTE]
> - BYOK-funktionen stöds av Event Hubs [dedikerade kluster för en](event-hubs-dedicated-overview.md) klientorganisation. Det kan inte aktiveras för standardnamnrymder Event Hubs namnområden.
> - Krypteringen kan bara aktiveras för nya eller tomma namnområden. Om namnområdet innehåller händelsehubbb misslyckas krypteringsåtgärden.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckelanvändning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault-API:er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du konfigurerar ett nyckelvalv med kund hanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckelvalv med hjälp av Azure Portal finns i [Snabbstart:](../key-vault/general/quick-create-portal.md)Skapa ett Azure Key Vault med hjälp av Azure Portal .

> [!IMPORTANT]
> Användning av kund hanterade nycklar med Azure Event Hubs kräver att nyckelvalvet har två obligatoriska egenskaper konfigurerade. De är:  **Mjuk borttagning** **och Rensa inte**. Dessa egenskaper är aktiverade som standard när du skapar ett nytt nyckelvalv i Azure Portal. Om du behöver aktivera dessa egenskaper i ett befintligt nyckelvalv måste du dock använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera kund hanterade nycklar
Om du vill aktivera kund hanterade nycklar i Azure Portal du följande steg:

1. Gå till ditt Event Hubs Dedicated kluster.
1. Välj det namnområde som du vill aktivera BYOK för.
1. På sidan **Inställningar** i det Event Hubs namnområdet väljer du **Kryptering.** 
1. Välj den **kund hanterade nyckelkryptering i vila** som du ser i följande bild. 

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckelvalv med nycklar
När du har aktivera kund hanterade nycklar måste du associera den kund hanterade nyckeln med Azure Event Hubs namnområdet. Event Hubs stöder endast Azure Key Vault. Om du aktiverar **alternativet Kryptering med kund hanterad** nyckel i föregående avsnitt måste du importera nyckeln till Azure Key Vault. Nycklarna måste dessutom ha mjuk **borttagning och** Rensa **inte konfigurerat** för nyckeln. De här inställningarna kan konfigureras med [Hjälp av PowerShell](../key-vault/general/key-vault-recovery.md) eller [CLI.](../key-vault/general/key-vault-recovery.md)

1. Om du vill skapa ett nytt nyckelvalv följer du Azure Key Vault [Snabbstart.](../key-vault/general/overview.md) Mer information om hur du importerar befintliga nycklar finns [i Om nycklar, hemligheter och certifikat.](../key-vault/general/about-keys-secrets-certificates.md)
1. Om du vill aktivera både mjuk borttagning och rensningsskydd när du skapar ett valv använder du [kommandot az keyvault create.](/cli/azure/keyvault#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensningsskydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du [kommandot az keyvault update.](/cli/azure/keyvault#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel **väljer du Generera/importera** på **menyn Nycklar** under **Inställningar.**
        
        ![Välj knappen Generera/importera](./media/configure-customer-managed-key/select-generate-import.png)
    1. Ange **Alternativ** till **Generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 
    1. Du kan nu välja den här nyckeln för att associera Event Hubs namnområdet för kryptering från listrutan. 

        ![Välj nyckel från nyckelvalvet](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Fyll i informationen för nyckeln och klicka på **Välj**. Detta aktiverar kryptering av den Microsoft-hanterade nyckeln med din nyckel (kund hanterad nyckel). 


## <a name="rotate-your-encryption-keys"></a>Rotera dina krypteringsnycklar
Du kan rotera nyckeln i nyckelvalvet med hjälp av rotationsmekanismen för Azure Key Vaults. Aktiverings- och förfallodatum kan också anges för att automatisera nyckelrotation. Tjänsten Event Hubs identifierar nya nyckelversioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar
Om du återkallar åtkomsten till krypteringsnycklarna rensas inte data från Event Hubs. Data kan dock inte nås från den Event Hubs namnområdet. Du kan återkalla krypteringsnyckeln via åtkomstprincipen eller genom att ta bort nyckeln. Läs mer om åtkomstprinciper och att skydda ditt nyckelvalv [från Säker åtkomst till ett nyckelvalv.](../key-vault/general/security-overview.md)

När krypteringsnyckeln har återkallats kommer Event Hubs tjänsten i det krypterade namnområdet att bli oanvändbar. Om åtkomsten till nyckeln är aktiverad eller om borttagningsnyckeln har återställts väljer Event Hubs-tjänsten nyckeln så att du kan komma åt data från den krypterade Event Hubs namnområdet.

## <a name="set-up-diagnostic-logs"></a>Konfigurera diagnostikloggar 
Om du ställer in diagnostikloggar för BYOK-aktiverade namnrymder får du nödvändig information om åtgärderna. Dessa loggar kan aktiveras och senare strömmas till en händelsehubb eller analyseras via logganalys eller strömmas till lagring för att utföra anpassade analyser. Mer information om diagnostikloggar finns i [Översikt över Azure-diagnostikloggar.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Aktivera användarloggar
Följ de här stegen om du vill aktivera loggar för kund hanterade nycklar.

1. I Azure Portal navigerar du till namnområdet där BYOK är aktiverat.
1. Välj **Diagnostikinställningar** under **Övervakning.**

    ![Välj diagnostikinställningar](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Välj **+Lägg till diagnostikinställning**. 

    ![Välj Lägg till diagnostikinställning](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Ange ett **namn** och välj var du vill strömma loggarna.
1. Välj **CustomerManagedKeyUserLogs** och **Spara**. Den här åtgärden aktiverar loggarna för BYOK i namnområdet.

    ![Välj alternativet användarloggar för kund hanterad nyckel](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Loggschema 
Alla loggar lagras i JSON-format (JavaScript Object Notation). Varje post har strängfält som använder det format som beskrivs i följande tabell. 

| Name | Beskrivning |
| ---- | ----------- | 
| TaskName | Beskrivning av uppgiften som misslyckades. |
| ActivityId | Internt ID som används för spårning. |
| category | Definierar klassificeringen av uppgiften. Om nyckeln från nyckelvalvet till exempel inaktiveras, skulle det vara en informationskategori eller om en nyckel inte kan packas upp kan den hamna under fel. |
| resourceId | Azure Resource Manager resurs-ID |
| keyVault | Fullständigt namn på nyckelvalvet. |
| key | Nyckelnamnet som används för att kryptera Event Hubs namnområdet. |
| version | Den version av nyckeln som används. |
| operation | Den åtgärd som utförs på nyckeln i nyckelvalvet. Du kan till exempel inaktivera/aktivera nyckeln, omsluta eller packa upp |
| kod | Den kod som är associerad med åtgärden. Exempel: Felkod, 404, innebär att nyckeln inte hittades. |
| meddelande | Felmeddelande som är associerat med åtgärden |

Här är ett exempel på loggen för en kund hanterad nyckel:

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

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använda Resource Manager för att aktivera kryptering
Det här avsnittet visar hur du utför följande uppgifter med **hjälp Azure Resource Manager mallar**. 

1. Skapa ett **Event Hubs med en** hanterad tjänstidentitet.
2. Skapa ett **nyckelvalv** och ge tjänstidentiteten åtkomst till nyckelvalvet. 
3. Uppdatera Event Hubs med nyckelvalvsinformationen (nyckel/värde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Skapa ett Event Hubs kluster och namnområde med hanterad tjänstidentitet
Det här avsnittet visar hur du skapar ett Azure Event Hubs med hanterad tjänstidentitet med hjälp av en Azure Resource Manager mall och PowerShell. 

1. Skapa en Azure Resource Manager för att skapa en Event Hubs med en hanterad tjänstidentitet. Namnge filen: **CreateEventHubClusterAndNamespace.jspå**: 

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
2. Skapa en mallparameterfil med **namnet:CreateEventHubClusterAndNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>` – Namnet på ditt Event Hubs kluster    
    > - `<EventHubsNamespaceName>` – Namnet på Event Hubs namnområdet
    > - `<Location>` – Plats för Event Hubs namnområdet

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
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa Event Hubs ett namnområde. Hämta sedan ID:t för Event Hubs för att använda det senare. Ersätt `{MyRG}` med namnet på resursgruppen innan du kör kommandot.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Ge Event Hubs för namnområdesidentitet åtkomst till nyckelvalvet

1. Kör följande kommando för att skapa ett nyckelvalv **med rensningsskydd och** **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OR)    
    
    Kör följande kommando för att uppdatera ett **befintligt nyckelvalv.** Ange värden för resursgrupps- och nyckelvalvsnamn innan du kör kommandot. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange åtkomstprincipen för nyckelvalvet så att den hanterade identiteten Event Hubs namnområdet kan komma åt nyckelvärdet i nyckelvalvet. Använd ID:t för Event Hubs namnområdet från föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i Event Hubs med kund hanterad nyckel från nyckelvalvet
Du har gjort följande hittills: 

1. Skapat ett premiumnamnområde med en hanterad identitet.
2. Skapa ett nyckelvalv och bevilja den hanterade identiteten åtkomst till nyckelvalvet. 

I det här steget uppdaterar du Event Hubs med nyckelvalvsinformation. 

1. Skapa en JSON-fil **CreateEventHubClusterAndNamespace.jspå** med följande innehåll: 

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

2. Skapa en mallparameterfil: **UpdateEventHubClusterAndNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<EventHubsClusterName>` – Namnet på ditt Event Hubs kluster.        
    > - `<EventHubsNamespaceName>` – Namnet på Event Hubs namnområdet
    > - `<Location>` – Plats för Event Hubs namnområdet
    > - `<KeyVaultName>` – Namnet på ditt nyckelvalv
    > - `<KeyName>` – Namnet på nyckeln i nyckelvalvet

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
3. Kör följande PowerShell-kommando för att distribuera Resource Manager mall. Ersätt `{MyRG}` med namnet på resursgruppen innan du kör kommandot . 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Felsöka
Som bästa praxis bör du alltid aktivera loggar som visas i föregående avsnitt. Det hjälper till att spåra aktiviteter när BYOK-kryptering är aktiverat. Det hjälper också till att minska omfånget för problemen.

Nedan visas vanliga felkoder att söka efter när BYOK-kryptering har aktiverats.

| Action | Felkod | Resulterande datatillstånd |
| ------ | ---------- | ----------------------- | 
| Ta bort omslutnings-/uppackningsbehörighet från ett nyckelvalv | 403 |    Otillgängliga |
| Ta bort AAD-rollmedlemskap från ett AAD-huvudnamn som beviljat behörigheten wrap/unwrap | 403 |  Otillgängliga |
| Ta bort en krypteringsnyckel från nyckelvalvet | 404 | Otillgängliga |
| Ta bort nyckelvalvet | 404 | Otillgänglig (förutsätter att mjuk borttagning är aktiverat, vilket är en obligatorisk inställning.) |
| Ändra förfalloperioden för krypteringsnyckeln så att den redan har upphört att gälla | 403 |   Otillgängliga  |
| Ändra NBF (inte tidigare) så att nyckelkrypteringsnyckeln inte är aktiv | 403 | Otillgängliga  |
| Välja alternativet **Tillåt MSFT-tjänster** för nyckelvalvsbrandväggen eller på annat sätt blockera nätverksåtkomst till nyckelvalvet som har krypteringsnyckeln | 403 | Otillgängliga |
| Flytta nyckelvalvet till en annan klientorganisation | 404 | Otillgängliga |  
| Tillfälligt nätverksproblem eller DNS/AAD/MSI-avbrott |  | Tillgänglig med hjälp av cachelagrad datakrypteringsnyckel |

> [!IMPORTANT]
> Om du vill aktivera geo-dr på ett namnområde som använder BYOK-kryptering måste det sekundära namnområdet för parkoppling finnas i ett dedikerat kluster och ha en system tilldelad hanterad identitet aktiverad. Mer information finns i [Hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Översikt över Event Hubs](event-hubs-about.md)
- [Key Vault översikt](../key-vault/general/overview.md)