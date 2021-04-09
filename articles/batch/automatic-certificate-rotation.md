---
title: Aktivera automatisk certifikat rotation i en batch-pool
description: Du kan skapa en batch-pool med en hanterad identitet och ett certifikat som automatiskt kommer att förnyas.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962576"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Aktivera automatisk certifikat rotation i en batch-pool

 Du kan skapa en batch-pool med ett certifikat som automatiskt kommer att förnyas. För att göra det måste poolen skapas med en [användardefinierad hanterad identitet](managed-identity-pools.md) som kommer att ha åtkomst till certifikatet i [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> Stöd för Azure Batch pooler med användare tilldelade hanterade identiteter finns för närvarande i en offentlig för hands version för följande regioner: USA, västra 2, södra centrala USA, östra USA, US Gov, Arizona och US Gov, Virginia.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Börja med att [skapa din användarspecifika hanterade identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) i samma klient organisation som batch-kontot. Den här hanterade identiteten behöver inte finnas i samma resurs grupp eller ens i samma prenumeration.

Se till att notera **klient-ID: t** för den användarspecifika hanterade identiteten. Du behöver det här värdet senare.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Skärm bild som visar klient-ID för en användardefinierad hanterad identitet i Azure Portal.":::

## <a name="create-your-certificate"></a>Skapa ditt certifikat

Därefter måste du skapa ett certifikat och lägga till det i Azure Key Vault. Om du inte redan har skapat ett nyckel valv måste du göra det först. Instruktioner finns i [snabb start: Ange och hämta ett certifikat från Azure Key Vault med hjälp av Azure Portal](../key-vault/certificates/quick-create-portal.md).

När du skapar ditt certifikat måste du ange **livs längds åtgärds typ** för att förnya automatiskt och ange antalet dagar efter vilket certifikatet ska förnyas.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Skärm bild av skärmen skapa certifikat i Azure Portal.":::

När ditt certifikat har skapats noterar du den **hemliga identifieraren**. Du behöver det här värdet senare.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Skärm bild som visar den hemliga identifieraren för ett certifikat.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Lägg till en åtkomst princip i Azure Key Vault

I ditt nyckel valv tilldelar du en Key Vault åtkomst princip som ger din användarspecifika hanterade identitet åtkomst till hemligheter och certifikat. Detaljerade anvisningar finns i [tilldela en Key Vault åtkomst princip med hjälp av Azure Portal](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Skapa en batch-pool med en användardefinierad hanterad identitet

Skapa en batch-pool med din hanterade identitet med hjälp av [batch .net-hanterings biblioteket](/dotnet/api/overview/azure/batch#management-library). Mer information finns i [Konfigurera hanterade identiteter i batch-pooler](managed-identity-pools.md).

I följande exempel används batch Management-REST API för att skapa en pool. Se till att använda certifikatets **hemliga ID** för `observedCertificates` och din hanterade identitets **klient-ID** för och `msiClientId` Ersätt exempel data nedan.

REST API-URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Begärandetext

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Verifiera certifikatet

För att bekräfta att certifikatet har distribuerats loggar du in på Compute-noden. Du bör se utdata som liknar följande:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).
- Lär dig hur du använder [Kundhanterade nycklar med användar hanterade identiteter](batch-customer-managed-key.md).
