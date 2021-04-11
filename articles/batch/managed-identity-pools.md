---
title: Konfigurera hanterade identiteter i batch-pooler
description: Lär dig hur du aktiverar användarspecifika hanterade identiteter för batch-pooler och hur du använder hanterade identiteter i noderna.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7fab213ac1545c0bff9b74bc46504717b6038e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950169"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Konfigurera hanterade identiteter i batch-pooler

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) eliminerar behovet av utvecklare som behöver hantera autentiseringsuppgifter genom att tillhandahålla en identitet för Azure-resursen i Azure Active Directory (Azure AD) och använda den för att hämta Azure Active Directory (Azure AD)-token.

I det här avsnittet beskrivs hur du aktiverar användarspecifika hanterade identiteter för batch-pooler och hur du använder hanterade identiteter i noderna.

> [!IMPORTANT]
> Stöd för Azure Batch pooler med användare tilldelade hanterade identiteter finns för närvarande i en offentlig för hands version för följande regioner: USA, västra 2, södra centrala USA, östra USA, US Gov, Arizona och US Gov, Virginia.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Börja med att [skapa din användarspecifika hanterade identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) i samma klient organisation som batch-kontot. Den här hanterade identiteten behöver inte finnas i samma resurs grupp eller ens i samma prenumeration.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Skapa en batch-pool med användare tilldelade hanterade identiteter

När du har skapat en eller flera användarspecifika hanterade identiteter kan du skapa en batch-pool med den hanterade identiteten med hjälp av [batch .net-hanterings biblioteket](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Pooler måste konfigureras med [konfiguration av virtuell dator](nodes-and-pools.md#virtual-machine-configuration) för att kunna använda hanterade identiteter.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Det finns för närvarande inte stöd för att skapa pooler med hanterade identiteter med [klient biblioteket för batch .net](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Använd användare som tilldelats hanterade identiteter i batch-noder

När du har skapat dina pooler kan användarnas tilldelade hanterade identiteter komma åt poolens noder via SSH (Secure Shell) eller fjärr skrivbord (RDP). Du kan också konfigurera aktiviteter så att de hanterade identiteterna direkt kan komma åt [Azure-resurser som har stöd för hanterade identiteter](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

I batch-noderna kan du hämta hanterade identitets-token och använda dem för att autentisera via Azure AD-autentisering via [Azure-instance metadata service](../virtual-machines/windows/instance-metadata-service.md).

För Windows är PowerShell-skriptet för att hämta en åtkomsttoken att autentisera:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

För Linux är bash-skriptet:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Mer information finns i [så här använder du hanterade identiteter för Azure-resurser på en virtuell Azure-dator för att få en åtkomsttoken](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).
- Lär dig hur du använder [Kundhanterade nycklar med användar hanterade identiteter](batch-customer-managed-key.md).
- Lär dig hur du [aktiverar automatisk certifikat rotation i en batch-pool](automatic-certificate-rotation.md).
