---
title: Använda Key Vault-referenser
description: Lär dig att konfigurera Azure App Service och Azure Functions att använda Azure Key Vault referenser. Gör Key Vault hemligheter tillgängliga för din programkod.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 1e6f46b205790d81a3e76d2aafbcf7e13dbb5afd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815224"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Använd Key Vault referenser för App Service och Azure Functions

Det här avsnittet visar hur du arbetar med hemligheter från Azure Key Vault i ditt App Service eller Azure Functions-program utan att några kodändringar krävs. [Azure Key Vault](../key-vault/general/overview.md) är en tjänst som tillhandahåller centraliserad hemlighetshantering med fullständig kontroll över åtkomstprinciper och granskningshistorik.

## <a name="granting-your-app-access-to-key-vault"></a>Bevilja din app åtkomst till Key Vault

För att kunna läsa hemligheter från Key Vault måste du ha skapat ett valv och ge appen behörighet att komma åt det.

1. Skapa ett nyckelvalv genom att följa [Key Vault snabbstart](../key-vault/secrets/quick-create-cli.md).

1. Skapa en [system tilldelad hanterad identitet](overview-managed-identity.md) för ditt program.

   > [!NOTE] 
   > Key Vault har för närvarande endast stöd för system tilldelade hanterade identiteter. Användar tilldelade identiteter kan inte användas.

1. Skapa en [åtkomstprincip i Key Vault](../key-vault/general/security-features.md#privileged-access) för den programidentitet som du skapade tidigare. Aktivera behörigheten "Hämta" för den här principen. Konfigurera inte det "auktoriserade programmet" eller inställningarna `applicationId` eftersom det inte är kompatibelt med en hanterad identitet.

### <a name="access-network-restricted-vaults"></a>Åtkomst till nätverksbegränsade valv

> [!NOTE]
> Linux-baserade program kan för närvarande inte matcha hemligheter från ett nätverksbegränsat nyckelvalv om inte appen finns i ett [App Service-miljön](./environment/intro.md).

Om valvet har konfigurerats [med nätverksbegränsningar](../key-vault/general/overview-vnet-service-endpoints.md)måste du också se till att programmet har nätverksåtkomst.

1. Kontrollera att programmet har utgående nätverksfunktioner konfigurerade, enligt beskrivningen [i App Service nätverksfunktionerna](./networking-features.md) [och Azure Functions nätverksalternativen](../azure-functions/functions-networking-options.md).

2. Kontrollera att valvets konfigurationskonton för nätverket eller undernätet som appen kommer åt det via.

> [!IMPORTANT]
> Åtkomst till ett valv via integrering av virtuella nätverk är för närvarande [inkompatibel med automatiska uppdateringar för hemligheter utan en angiven version](#rotation).

## <a name="reference-syntax"></a>Referenssyntax

En Key Vault referens har formen `@Microsoft.KeyVault({referenceString})` , där `{referenceString}` ersätts med något av följande alternativ:

> [!div class="mx-tdBreakAll"]
> | Referenssträng                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | SecretUri **ska** vara den fullständiga dataplans-URI:en för en hemlighet i Key Vault, om du vill, inklusive en version, t.ex. `https://myvault.vault.azure.net/secrets/mysecret/` eller `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName=_vaultName_; SecretName=_secretName_; SecretVersion=_secretVersion_ | **VaultName är** obligatoriskt och bör namnet på din Key Vault resurs. **SecretName krävs** och ska vara namnet på målhemligheten. SecretVersion **är** valfritt, men om det finns anger den version av hemligheten som ska användas. |

En fullständig referens skulle till exempel se ut så här:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Du kan också:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotation

> [!IMPORTANT]
> [Åtkomst till ett valv via integrering av virtuella nätverk är för](#access-network-restricted-vaults) närvarande inkompatibel med automatiska uppdateringar för hemligheter utan en angiven version.

Om en version inte anges i referensen använder appen den senaste versionen som finns i Key Vault. När nyare versioner blir tillgängliga, till exempel med en rotationshändelse, uppdateras appen automatiskt och börjar använda den senaste versionen inom en dag. Alla konfigurationsändringar som görs i appen kommer att orsaka en omedelbar uppdatering av de senaste versionerna av alla refererade hemligheter.

## <a name="source-application-settings-from-key-vault"></a>Källprograminställningar från Key Vault

Key Vault-referenser kan användas som värden [](configure-common.md#configure-app-settings)för programinställningar, så att du kan behålla hemligheter i Key Vault stället för platskonfigurationen. Programinställningarna krypteras säkert i vila, men om du behöver funktioner för hemlighetshantering bör de gå in på Key Vault.

Om du vill Key Vault en referens för en programinställning anger du referensen som värdet för inställningen. Din app kan referera till hemligheten via dess nyckel som vanligt. Inga kodändringar krävs.

> [!TIP]
> De flesta programinställningar som använder Key Vault referenser ska markeras som fackinställningar, eftersom du bör ha separata valv för varje miljö.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

När du automatiserar resursdistributioner via Azure Resource Manager-mallar kan du behöva sekvensera dina beroenden i en viss ordning för att den här funktionen ska fungera. Observera att du måste definiera programinställningarna som en egen resurs i stället för att använda en `siteConfig` egenskap i platsdefinitionen. Det beror på att platsen måste definieras först så att den system tilldelade identiteten skapas med den och kan användas i åtkomstprincipen.

Ett exempel på en pseudomall för en funktionsapp kan se ut så här:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> I det här exemplet beror källkontrolldistributionen på programinställningarna. Detta är vanligtvis osäkert eftersom uppdateringen av appinställningen beter sig asynkront. Men eftersom vi har inkluderat `WEBSITE_ENABLE_SYNC_UPDATE_SITE` programinställningen är uppdateringen synkron. Det innebär att källkontrolldistributionen endast startar när programinställningarna har uppdaterats fullständigt.

## <a name="troubleshooting-key-vault-references"></a>Felsöka Key Vault referenser

Om en referens inte matchas korrekt används referensvärdet i stället. Det innebär att för programinställningar skapas en miljövariabel vars värde har `@Microsoft.KeyVault(...)` syntaxen . Detta kan göra att programmet kastar fel, eftersom det förväntade sig en hemlighet av en viss struktur.

Detta beror oftast på en felaktig konfiguration av Key Vault [åtkomstprincip .](#granting-your-app-access-to-key-vault) Men det kan också bero på att en hemlighet inte längre finns eller ett syntaxfel i själva referensen.

Om syntaxen är korrekt kan du visa andra orsaker till fel genom att kontrollera den aktuella lösningsstatusen i portalen. Gå till Programinställningar och välj "Redigera" för referensen i fråga. Under inställningskonfigurationen bör du se statusinformation, inklusive eventuella fel. Frånvaron av dessa innebär att referenssyntaxen är ogiltig.

Du kan också använda en av de inbyggda detektorerna för att få ytterligare information.

### <a name="using-the-detector-for-app-service"></a>Använda detektorn för App Service

1. Navigera till din app i portalen.
2. Välj **Diagnostisera och lösa problem**.
3. Välj **Tillgänglighet och prestanda och** välj **Webbappen är nere.**
4. Leta **Key Vault diagnostik för programinställningar** och klicka på Mer **information.**


### <a name="using-the-detector-for-azure-functions"></a>Använda detektorn för Azure Functions

1. Navigera till din app i portalen.
2. Gå till **Plattformsfunktioner.**
3. Välj **Diagnostisera och lösa problem**.
4. Välj **Tillgänglighet och prestanda och** välj **Funktionsappen är nere eller rapporterar fel.**
5. Klicka på **Key Vault diagnostik för programinställningar.**
