---
title: Dubbel kryptering för vilodata
titleSuffix: Azure HDInsight
description: I den här artikeln beskrivs de två krypteringslager som är tillgängliga för vilodata Azure HDInsight kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 226516b1178f14789570b45b68cfdbf56f63bbd7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775159"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight dubbel kryptering för vilodata

I den här artikeln beskrivs metoder för kryptering av vilodata Azure HDInsight kluster. Datakryptering i vila avser kryptering på hanterade diskar (datadiskar, OS-diskar och temporära diskar) som är anslutna till virtuella HDInsight-klusterdatorer. 

Det här dokumentet behandlar inte data som lagras i ditt Azure Storage konto. Dina kluster kan ha ett eller flera anslutna Azure Storage-konton där krypteringsnycklarna också kan hanteras av Microsoft eller av kunden, men krypteringstjänsten skiljer sig åt. Mer information om hur Azure Storage kryptering finns i [Azure Storage för vilodata.](../storage/common/storage-service-encryption.md)

## <a name="introduction"></a>Introduktion

Det finns tre huvudsakliga hanterade diskroller i Azure: datadisken, OS-disken och den tillfälliga disken. Mer information om olika typer av hanterade diskar finns i [Introduktion till Azure Managed Disks](../virtual-machines/managed-disks-overview.md). 

HDInsight stöder flera typer av kryptering i två olika lager:

- Kryptering på serversidan (SSE) – SSE utförs av lagringstjänsten. I HDInsight används SSE för att kryptera OS-diskar och datadiskar. Det är aktiverat som standard. SSE är en layer 1-krypteringstjänst.
- Kryptering på värden med plattforms hanterad nyckel – på liknande sätt som SSE utförs den här typen av kryptering av lagringstjänsten. Den är dock endast för temporära diskar och är inte aktiverad som standard. Kryptering på värden är också en layer 1-krypteringstjänst.
- Kryptering i vila med kund hanterad nyckel – Den här typen av kryptering kan användas på data och temporära diskar. Det är inte aktiverat som standard och kräver att kunden anger sin egen nyckel via Azure Key Vault. Kryptering i vila är en layer 2-krypteringstjänst.

Dessa typer sammanfattas i följande tabell.

|Klustertyp |OS-disk (hanterad disk) |Datadisk (hanterad disk) |Temporär datadisk (lokal SSD) |
|---|---|---|---|
|Kafka, HBase med accelererade skrivningar|Layer1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard|Layer1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard, Layer2: Valfri kryptering i vila med CMK|Layer1: Valfri kryptering på värden med PMK, Layer2: Valfri kryptering i vila med CMK|
|Alla andra kluster (Spark, Interactive, Hadoop, HBase utan accelererade skrivningar)|Layer1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard|Ej tillämpligt|Layer1: Valfri kryptering på värden med PMK, Layer2: Valfri kryptering i vila med CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med kund hanterade nycklar

Kundstyrd nyckelkryptering är en process i ett steg som hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att auktorisera en hanterad identitet med Azure Key Vault och lägga till krypteringsnyckeln när du skapar klustret.

Både datadiskar och temporära diskar på varje nod i klustret krypteras med en symmetrisk datakrypteringsnyckel (DEK). DEK skyddas med nyckelkrypteringsnyckeln (KEK) från nyckelvalvet. Krypterings- och dekrypteringsprocesserna hanteras helt av Azure HDInsight.

För OS-diskar som är anslutna till klustrets virtuella datorer är endast ett lager med kryptering (PMK) tillgängligt. Vi rekommenderar att kunderna undviker att kopiera känsliga data till OS-diskar om en CMK-kryptering krävs för deras scenarier.

Om nyckelvalvsbrandväggen är aktiverad i nyckelvalvet där diskkrypteringsnyckeln lagras måste IP-adresserna för HDInsight-regionala resursprovidern för den region där klustret ska distribueras läggas till i brandväggskonfigurationen för nyckelvalvet. Detta är nödvändigt eftersom HDInsight inte är en betrodd Azure Key Vault-tjänst.

Du kan använda Azure Portal eller Azure CLI för att rotera nycklarna i nyckelvalvet på ett säkert sätt. När en nyckel roteras börjar HDInsight-klustret använda den nya nyckeln inom några minuter. Aktivera [nyckelskyddsfunktionerna för](../key-vault/general/soft-delete-overview.md) mjuk borttagning för att skydda mot utpressningstrojanscenarier och oavsiktlig borttagning. Nyckelvalv utan den här skyddsfunktionen stöds inte.

### <a name="get-started-with-customer-managed-keys"></a>Kom igång med kund hanterade nycklar

För att skapa ett kund hanterat nyckelaktiverade HDInsight-kluster går vi igenom följande steg:

1. Skapa hanterade identiteter för Azure-resurser
1. Skapa Azure Key Vault
1. Skapa nyckel
1. Skapa åtkomstprincip
1. Skapa HDInsight-kluster med kund hanterad nyckel aktiverad
1. Rotera krypteringsnyckeln

Varje steg beskrivs i något av följande avsnitt i detalj.

### <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

Skapa en användar tilldelad hanterad identitet för att autentisera Key Vault.

Specifika [steg finns i Skapa en användar tilldelad](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) hanterad identitet. Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [Hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md). Se till att spara resurs-ID:t för den hanterade identiteten när du lägger till det Key Vault åtkomstprincipen.

### <a name="create-azure-key-vault"></a>Skapa Azure Key Vault

Skapa ett nyckelvalv. Se [Skapa Azure Key Vault](../key-vault/general/quick-create-portal.md) för specifika steg.

HDInsight stöder endast Azure Key Vault. Om du har ett eget nyckelvalv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att mjuk borttagning måste vara **aktiverat för nyckelvalvet.** Mer information om hur du importerar befintliga nycklar finns [i Om nycklar, hemligheter och certifikat.](../key-vault/general/about-keys-secrets-certificates.md)

### <a name="create-key"></a>Skapa nyckel

1. Gå till Inställningar Nycklar + Generera/importera **från ditt** nya  >    >  **nyckelvalv.**

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Generera en ny nyckel i Azure Key Vault":::

1. Ange ett namn och välj sedan **Skapa**. Behåll **standardnyckeltypen** **RSA.**

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="genererar nyckelnamn":::

1. När du återgår till **sidan** Nycklar väljer du den nyckel som du skapade.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="key vault key list":::

1. Välj version för att öppna **sidan Nyckelversion.** När du använder en egen nyckel för HDInsight-klusterkryptering måste du ange nyckel-URI:t. Kopiera **nyckelidentifieraren** och spara den någonstans tills du är redo att skapa klustret.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="hämta nyckelidentifierare":::

### <a name="create-access-policy"></a>Skapa åtkomstprincip

1. Från det nya nyckelvalvet går du till **Inställningar**  >  **Åtkomstprinciper**  >  **+ Lägg till åtkomstprincip**.

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Skapa en ny Azure Key Vault åtkomstprincip":::

1. På sidan **Lägg till åtkomstprincip** anger du följande information:

    |Egenskap |Beskrivning|
    |---|---|
    |Nyckelbehörigheter|Välj **Get** **(Hämta) , Unwrap Key (Packa upp** **nyckel) och Wrap Key (Omsluta nyckel).**|
    |Hemliga behörigheter|Välj **Hämta,** **Ange** och Ta **bort.**|
    |Välj huvudnamn|Välj den användar tilldelade hanterade identiteten som du skapade tidigare.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Ange Välj huvudnamn för Azure Key Vault åtkomstprincip":::

1. Välj **Lägg till**.

1. Välj **Spara**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Spara Azure Key Vault åtkomstprincip":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Skapa kluster med kund hanterad nyckeldiskkryptering

Nu är du redo att skapa ett nytt HDInsight-kluster. Kund hanterade nycklar kan bara tillämpas på nya kluster när klustret skapas. Kryptering kan inte tas bort från kund hanterade nyckelkluster, och kund hanterade nycklar kan inte läggas till i befintliga kluster.

Från och med november 2020-versionen har HDInsight stöd för att skapa kluster med både versionsbaserade och versions less-nyckel-URI:er. Om du skapar klustret med en versions mindre nyckel-URI försöker HDInsight-klustret utföra automatisk nyckelrotation när nyckeln uppdateras i ditt Azure Key Vault. Om du skapar klustret med en versionsindelade nyckel-URI måste du utföra en manuell nyckelrotation som beskrivs i [Rotera krypteringsnyckeln](#rotating-the-encryption-key).

För kluster som skapats före november 2020-versionen måste du utföra nyckelrotation manuellt med hjälp av versionsnyckelns URI.

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

När klustret skapas kan du antingen använda en versionsnyckel eller en versionslös nyckel på följande sätt:

- **Versioned** – När klustret skapas anger du den fullständiga **nyckelidentifieraren**, inklusive nyckelversionen. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Versionslös** – Ange bara nyckelidentifieraren när klustret **skapas.** Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Du måste också tilldela den hanterade identiteten till klustret.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Skapa ett nytt kluster":::

#### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du använder Azure CLI för att skapa ett nytt Apache Spark kluster med diskkryptering aktiverat. Mer information finns i [Azure CLI az hdinsight create](/cli/azure/hdinsight#az_hdinsight_create). Parametern `encryption-key-version` är valfri.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar

I följande exempel visas hur du använder en Azure Resource Manager för att skapa ett nytt Apache Spark kluster med diskkryptering aktiverat. Mer information finns i Vad [är ARM-mallar?](../azure-resource-manager/templates/overview.md). Resource Manager-mallegenskapen `diskEncryptionKeyVersion` är valfri.

I det här exemplet används PowerShell för att anropa mallen.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Innehållet i resurshanteringsmallen, `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Rotera krypteringsnyckeln

Du kan ändra krypteringsnycklarna som används i klustret som körs med hjälp Azure Portal eller Azure CLI. För den här åtgärden måste klustret ha åtkomst till både den aktuella nyckeln och den avsedda nya nyckeln, annars misslyckas rotationsnyckelåtgärden. För kluster som skapats efter november 2020-versionen kan du välja om du vill att den nya nyckeln ska ha en version eller inte. För kluster som skapats före november 2020-versionen måste du använda en versionsnyckel när du roterar krypteringsnyckeln.

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att rotera nyckeln behöver du URI:en för basnyckelvalvet. När du har gjort det går du till avsnittet egenskaper för HDInsight-kluster i portalen och klickar på Ändra nyckel **under** URL **för diskkrypteringsnyckel.** Ange i den nya nyckel-URL:en och skicka för att rotera nyckeln.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="rotera diskkrypteringsnyckeln":::

#### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du roterar diskkrypteringsnyckeln för ett befintligt HDInsight-kluster. Mer information finns i [Azure CLI az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Vanliga frågor och svar om kund hanterad nyckelkryptering

**Hur kommer HDInsight-klustret åt mitt nyckelvalv?**

HDInsight får åtkomst till din Azure Key Vault instans med hjälp av den hanterade identitet som du associerar med HDInsight-klustret. Den här hanterade identiteten kan skapas innan eller när klustret skapas. Du måste också ge den hanterade identiteten åtkomst till nyckelvalvet där nyckeln lagras.

**Är den här funktionen tillgänglig för alla kluster i HDInsight?**

Kundstyrd nyckelkryptering är tillgänglig för alla klustertyper utom Spark 2.1 och 2.2.

**Kan jag använda flera nycklar för att kryptera olika diskar eller mappar?**

Nej, alla hanterade diskar och resursdiskar krypteras med samma nyckel.

**Vad händer om klustret förlorar åtkomsten till nyckelvalvet eller nyckeln?**

Om klustret förlorar åtkomst till nyckeln visas varningar i Apache Ambari-portalen. I det här tillståndet **misslyckas åtgärden Ändra** nyckel. När nyckelåtkomsten har återställts försvinner Ambari-varningar och åtgärder som nyckelrotation kan utföras.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="Ambari-avisering för nyckelåtkomst":::

**Hur återställer jag klustret om nycklarna tas bort?**

Eftersom endast "mjuk borttagning" aktiverade nycklar stöds bör klustret återfå åtkomsten till nycklarna om nycklarna återställs i nyckelvalvet. Information om hur Azure Key Vault en nyckel finns [i Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) [eller az-keyvault-key-recover](/cli/azure/keyvault/key#az_keyvault_key_recover).


**Kommer de nya noderna att stödja kund hanterade nycklar sömlöst om ett kluster skalas upp?**

Ja. Klustret behöver åtkomst till nyckeln i nyckelvalvet under uppskalningen. Samma nyckel används för att kryptera både hanterade diskar och resursdiskar i klustret.

**Är kund hanterade nycklar tillgängliga på min plats?**

Kund hanterade HDInsight-nycklar är tillgängliga i alla offentliga moln och nationella moln.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Kryptering på värden med plattformsbaserade nycklar

### <a name="enable-in-the-azure-portal"></a>Aktivera i Azure Portal

Kryptering på värden kan aktiveras när klustret skapas i Azure Portal.

> [!Note]
> När kryptering på värden är aktiverat kan du inte lägga till program i HDInsight-klustret från Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Aktivera kryptering på värden.":::

Det här alternativet aktiverar [kryptering på värden för virtuella](../virtual-machines/disks-enable-host-based-encryption-portal.md) HDInsight-datorer och temporära datadiskar med PMK. Kryptering på värden stöds endast på [vissa VM SKU:er](../virtual-machines/disks-enable-host-based-encryption-portal.md) i begränsade regioner och HDInsight stöder [följande nodkonfiguration och SKU:er.](./hdinsight-supported-node-configuration.md)

Information om rätt VM-storlek för ditt HDInsight-kluster finns i Välja [rätt VM-storlek för ditt Azure HDInsight kluster.](hdinsight-selecting-vm-size.md) Standard-VM-SKU:n för Zookeeper-noden när kryptering på värden är aktiverad är DS2V2.

### <a name="enable-using-powershell"></a>Aktivera med PowerShell

Följande kodfragment visar hur du kan skapa ett nytt Azure HDInsight kluster som har kryptering på värden aktiverad med hjälp av PowerShell. Den använder parametern `-EncryptionAtHost $true` för att aktivera funktionen.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Aktivera med Azure CLI

Följande kodfragment visar hur du kan skapa ett nytt Azure HDInsight kluster som har kryptering på värden aktiverad med hjälp av Azure CLI. Den använder parametern `--encryption-at-host true` för att aktivera funktionen.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Key Vault finns i [Vad är Azure Key Vault](../key-vault/general/overview.md).
* [Översikt över företagssäkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
