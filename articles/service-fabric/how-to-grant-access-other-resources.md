---
title: Ge program åtkomst till andra Azure-resurser
description: I den här artikeln förklaras hur du beviljar åtkomst till hanterade identiteter Service Fabric program till andra Azure-resurser som stöder Azure Active Directory-baserad autentisering.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: c7560294fbf6d122396b6a5a8ffd3ee93bc89048
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97507463"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Bevilja åtkomst till Azure-resurser Service Fabric programmets hanterade identitet

Innan programmet kan använda den hanterade identiteten för att komma åt andra resurser måste behörigheter beviljas till den identiteten på den skyddade Azure-resursen som används. Att bevilja behörigheter är vanligt vis en hanterings åtgärd på "kontroll planet" i Azure-tjänsten som äger den skyddade resursen som dirigerats via Azure Resource Manager, vilket tvingar fram en tillgänglig rollbaserad åtkomst kontroll.

De exakta stegen varierar beroende på vilken typ av Azure-resurs som används, samt vilket språk/vilken klient som ska användas för att bevilja behörigheter. Resten av artikeln förutsätter en tilldelad identitet som tilldelats programmet och innehåller flera typiska exempel för din bekvämlighet, men det är inte på något sätt en fullständig hänvisning till det här ämnet. Läs dokumentationen för respektive Azure-tjänster för uppdaterade instruktioner om hur du beviljar behörigheter.  

## <a name="granting-access-to-azure-storage"></a>Bevilja åtkomst till Azure Storage
Du kan använda det Service Fabric programmets hanterade identitet (användar tilldelning i det här fallet) för att hämta data från en Azure Storage-blob. Ge identiteten behörighet som krävs i Azure Portal med följande steg:

1. Navigera till lagrings kontot
2. Klicka på länken åtkomstkontroll (IAM) i vänstra panelen.
3. valfritt Kontrol lera befintlig åtkomst: Välj system-eller användardefinierad hanterad identitet i find-kontrollen; Välj lämplig identitet i resultat listan
4. Klicka på + Lägg till roll tilldelning ovanpå sidan för att lägga till en ny roll tilldelning för programmets identitet.
Under roll i list rutan väljer du Storage BLOB data Reader.
5. I nästa listruta under tilldela åtkomst till väljer du `User assigned managed identity` .
6. Kontrollera sedan att rätt prenumeration är inställd i listrutan Prenumeration. Under Resursgrupper väljer du Alla resursgrupper.
7. Under Välj väljer du den UAI som motsvarar Service Fabric programmet och klickar sedan på Spara.

Stöd för systemtilldelade Service Fabric hanterade identiteter omfattar inte integrering i Azure Portal; om programmet använder en tilldelad identitet måste du hitta först klient-ID: t för programmets identitet och sedan upprepa stegen ovan, men välja `Azure AD user, group, or service principal` alternativet i kontrollen Sök.

## <a name="granting-access-to-azure-key-vault"></a>Bevilja åtkomst till Azure Key Vault
På samma sätt som med lagring kan du använda den hanterade identiteten för ett Service Fabric program för att få åtkomst till ett Azure Key Vault. Stegen för att bevilja åtkomst i Azure Portal liknar de som anges ovan och upprepas inte här. Se bilden nedan om du vill ha skillnader.

![Key Vault åtkomst princip](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

I följande exempel visas hur du beviljar åtkomst till ett valv via en mall distribution. Lägg till kodfragmenten nedan som en annan post under `resources` mallens element. Exemplet visar åtkomst beviljande för både tilldelade och systemtilldelade identitets typer – Välj lämplig.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
Och för systemtilldelade hanterade identiteter:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Mer information finns i [valv-uppdatera åtkomst princip](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
