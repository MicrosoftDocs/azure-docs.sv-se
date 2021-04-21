---
title: Konfigurera din egen nyckel för kryptering Azure Service Bus vilodata
description: Den här artikeln innehåller information om hur du konfigurerar din egen nyckel för kryptering Azure Service Bus data rest.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: de716b9f14191ba057c83a060104e64937c4192a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816016"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurera kund hanterade nycklar för kryptering Azure Service Bus vilodata med hjälp av Azure Portal
Azure Service Bus Premium tillhandahåller kryptering av vilodata med Azure Storage Service Encryption (Azure SSE). Service Bus Premium använder Azure Storage för att lagra data. Alla data som lagras med Azure Storage krypteras med Microsoft-hanterade nycklar. Om du använder en egen nyckel (kallas även Bring Your Own Key (BYOK) eller kund hanterad nyckel) krypteras data fortfarande med den Microsoft-hanterade nyckeln, men dessutom krypteras den Microsoft-hanterade nyckeln med hjälp av den kund hanterade nyckeln. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till kund hanterade nycklar som används för att kryptera Microsoft-hanterade nycklar. Aktivering av BYOK-funktionen är en konfigurationsprocess en gång i namnområdet.

Det finns vissa varningar till den kund hanterade nyckeln för kryptering på tjänstsidan. 
- Den här funktionen stöds av [Azure Service Bus Premium-nivån.](service-bus-premium-messaging.md) Den kan inte aktiveras för Service Bus namnrymder.
- Krypteringen kan bara aktiveras för nya eller tomma namnområden. Om namnområdet innehåller köer eller ämnen misslyckas krypteringsåtgärden.

Du kan använda Azure Key Vault för att hantera dina nycklar och granska din nyckelanvändning. Du kan antingen skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda Azure Key Vault-API:er för att generera nycklar. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du konfigurerar ett nyckelvalv med kund hanterade nycklar med hjälp av Azure Portal. Information om hur du skapar ett nyckelvalv med hjälp av Azure Portal finns i [Snabbstart:](../key-vault/general/quick-create-portal.md)Skapa ett Azure Key Vault med hjälp av Azure Portal .

> [!IMPORTANT]
> Användning av kund hanterade nycklar med Azure Service Bus kräver att nyckelvalvet har två obligatoriska egenskaper konfigurerade. De är:  **Mjuk borttagning** **och Rensa inte**. Dessa egenskaper är aktiverade som standard när du skapar ett nytt nyckelvalv i Azure Portal. Om du behöver aktivera dessa egenskaper i ett befintligt nyckelvalv måste du dock använda antingen PowerShell eller Azure CLI.

## <a name="enable-customer-managed-keys"></a>Aktivera kund hanterade nycklar
Om du vill aktivera kund hanterade nycklar i Azure Portal du följande steg:

1. Gå till ditt Service Bus Premium-namnområdet.
2. På sidan **Inställningar** i Service Bus namnområdet väljer du **Kryptering.**
3. Välj den **kund hanterade nyckelkryptering i vila** som du ser i följande bild.

    ![Aktivera kundhanterad nyckel](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Konfigurera ett nyckelvalv med nycklar

När du har aktivera kund hanterade nycklar måste du associera den kund hanterade nyckeln med Azure Service Bus namnområdet. Service Bus stöder endast Azure Key Vault. Om du aktiverar **alternativet Kryptering med kund hanterad** nyckel i föregående avsnitt måste du importera nyckeln till Azure Key Vault. Nycklarna måste dessutom ha mjuk **borttagning och** Rensa **inte konfigurerat** för nyckeln. De här inställningarna kan konfigureras med [Hjälp av PowerShell](../key-vault/general/key-vault-recovery.md) eller [CLI.](../key-vault/general/key-vault-recovery.md)

1. Om du vill skapa ett nytt nyckelvalv följer du Azure Key Vault [Snabbstart.](../key-vault/general/overview.md) Mer information om hur du importerar befintliga nycklar finns [i Om nycklar, hemligheter och certifikat.](../key-vault/general/about-keys-secrets-certificates.md)
1. Om du vill aktivera både mjuk borttagning och rensningsskydd när du skapar ett valv använder du [kommandot az keyvault create.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Om du vill lägga till rensningsskydd i ett befintligt valv (som redan har mjuk borttagning aktiverat) använder du [kommandot az keyvault update.](/cli/azure/keyvault#az_keyvault_update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Skapa nycklar genom att följa dessa steg:
    1. Om du vill skapa en ny nyckel **väljer du Generera/importera** på **menyn Nycklar** under **Inställningar.**
        
        ![Välj knappen Generera/importera](./media/configure-customer-managed-key/select-generate-import.png)

    1. Ange **Alternativ** till **Generera** och ge nyckeln ett namn.

        ![Skapa en nyckel](./media/configure-customer-managed-key/create-key.png) 

    1. Du kan nu välja den här nyckeln för att associera Service Bus namnområdet för kryptering från listrutan. 

        ![Välj nyckel från nyckelvalvet](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > För redundans kan du lägga till upp till 3 nycklar. Om en av nycklarna har upphört att gälla eller inte är tillgänglig används de andra nycklarna för kryptering.
        
    1. Fyll i informationen för nyckeln och klicka på **Välj**. Detta aktiverar kryptering av den Microsoft-hanterade nyckeln med din nyckel (kund hanterad nyckel). 


    > [!IMPORTANT]
    > Om du vill använda en kundhanteringsnyckel tillsammans med geo-haveriberedskap kan du läsa det här avsnittet. 
    >
    > Om du vill aktivera kryptering av Microsoft-hanterad nyckel med en kund hanterad nyckel konfigureras en åtkomstprincip för den Service Bus hanterade identiteten på det angivna Azure KeyVault. [](../key-vault/general/security-features.md) Detta säkerställer kontrollerad åtkomst till Azure KeyVault från Azure Service Bus namnområdet.
    >
    > På grund av detta:
    > 
    >   * Om [geo-haveriberedskap](service-bus-geo-dr.md) redan har aktiverats för Service Bus och du vill aktivera kundhanteringsnyckel ska du 
    >     * Bryt parkopplingen
    >     * [Konfigurera åtkomstprincipen för](../key-vault/general/assign-access-policy-portal.md) den hanterade identiteten för både den primära och sekundära namnrymden till nyckelvalvet.
    >     * Konfigurera kryptering på det primära namnområdet.
    >     * Koppla om de primära och sekundära namnrymderna.
    > 
    >   * Om du vill aktivera geo-dr på en Service Bus där kund hanterad nyckel redan har ställts in, kan du -
    >     * [Konfigurera åtkomstprincipen för den](../key-vault/general/assign-access-policy-portal.md) hanterade identiteten för det sekundära namnområdet till nyckelvalvet.
    >     * Koppla ihop de primära och sekundära namnrymderna.


## <a name="rotate-your-encryption-keys"></a>Rotera dina krypteringsnycklar

Du kan rotera nyckeln i nyckelvalvet med hjälp av rotationsmekanismen för Azure Key Vaults. Aktiverings- och förfallodatum kan också anges för att automatisera nyckelrotation. Tjänsten Service Bus identifierar nya nyckelversioner och börjar använda dem automatiskt.

## <a name="revoke-access-to-keys"></a>Återkalla åtkomst till nycklar

Om du återkallar åtkomsten till krypteringsnycklarna rensas inte data från Service Bus. Data kan dock inte nås från det Service Bus namnområdet. Du kan återkalla krypteringsnyckeln via åtkomstprincipen eller genom att ta bort nyckeln. Läs mer om åtkomstprinciper och att skydda ditt nyckelvalv [från Säker åtkomst till ett nyckelvalv.](../key-vault/general/security-features.md)

När krypteringsnyckeln har återkallats blir Service Bus tjänsten i det krypterade namnområdet oanvändbar. Om åtkomsten till nyckeln är aktiverad eller om den borttagna nyckeln återställs väljer Service Bus-tjänsten nyckeln så att du kan komma åt data från den krypterade Service Bus namnområdet.

## <a name="caching-of-keys"></a>Cachelagring av nycklar
Instansen Service Bus av sina angivna krypteringsnycklar var femte minut. Den cachelagrar och använder dem till nästa avsökning, vilket är efter 5 minuter. Så länge som minst en nyckel är tillgänglig är köer och ämnen tillgängliga. Om alla nycklar i listan inte är tillgängliga när de avsöks blir alla köer och ämnen otillgängliga. 

Här finns mer information: 

- Var 5:e minut avsöker Service Bus tjänst alla kund hanterade nycklar som anges i namnområdets post:
    - Om en nyckel har roterats uppdateras posten med den nya nyckeln.
    - Om en nyckel har återkallats tas nyckeln bort från posten.
    - Om alla nycklar har återkallats anges namnområdets krypteringsstatus till **Återkallad.** Data kan inte nås från den Service Bus namnområdet.. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Använda Resource Manager för att aktivera kryptering
Det här avsnittet visar hur du utför följande uppgifter med **hjälp Azure Resource Manager mallar**. 

1. Skapa  ett Premium Service Bus namnområde med en **hanterad tjänstidentitet**.
2. Skapa ett **nyckelvalv** och ge tjänstidentiteten åtkomst till nyckelvalvet. 
3. Uppdatera Service Bus med nyckelvalvsinformationen (nyckel/värde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Skapa ett Premium Service Bus namnområde med hanterad tjänstidentitet
Det här avsnittet visar hur du skapar ett Azure Service Bus med hanterad tjänstidentitet med hjälp av en Azure Resource Manager mall och PowerShell. 

1. Skapa en Azure Resource Manager för att skapa ett namnområde Service Bus premiumnivå med en hanterad tjänstidentitet. Namnge filen: **CreateServiceBusPremiumNamespace.jspå**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Skapa en mallparameterfil med **namnet:CreateServiceBusPremiumNamespaceParams.jspå**. 

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>` – Namnet på Service Bus namnområdet
    > - `<Location>` – Plats för Service Bus namnområdet

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Kör följande PowerShell-kommando för att distribuera mallen för att skapa ett premium-Service Bus-namnområde. Hämta sedan ID:t för Service Bus för att använda det senare. Ersätt `{MyRG}` med namnet på resursgruppen innan du kör kommandot.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Ge Service Bus för namnområdesidentitet åtkomst till nyckelvalvet

1. Kör följande kommando för att skapa ett nyckelvalv **med rensningsskydd och** **mjuk borttagning** aktiverat. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OR)
    
    Kör följande kommando för att uppdatera ett **befintligt nyckelvalv.** Ange värden för resursgrupps- och nyckelvalvsnamn innan du kör kommandot. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Ange åtkomstprincipen för nyckelvalvet så att den hanterade identiteten för Service Bus kan komma åt nyckelvärdet i nyckelvalvet. Använd ID:t för Service Bus namnområdet från föregående avsnitt. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Kryptera data i Service Bus med kund hanterad nyckel från nyckelvalvet
Du har gjort följande hittills: 

1. Skapat ett Premium-namnområde med en hanterad identitet.
2. Skapa ett nyckelvalv och bevilja den hanterade identiteten åtkomst till nyckelvalvet. 

I det här steget uppdaterar du Service Bus med nyckelvalvsinformation. 

1. Skapa en JSON-fil **UpdateServiceBusNamespaceWithEncryption.jspå** med följande innehåll: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Skapa en mallparameterfil: **UpdateServiceBusNamespaceWithEncryptionParams.jspå**.

    > [!NOTE]
    > Ersätt följande värden: 
    > - `<ServiceBusNamespaceName>` – Namnet på Service Bus namnområdet
    > - `<Location>` – Plats för Service Bus namnområdet
    > - `<KeyVaultName>` – Namnet på ditt nyckelvalv
    > - `<KeyName>` – Namnet på nyckeln i nyckelvalvet  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:
- [Service Bus översikt](service-bus-messaging-overview.md)
- [Key Vault översikt](../key-vault/general/overview.md)