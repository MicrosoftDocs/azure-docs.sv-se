---
title: Double Encryption för vilande data
titleSuffix: Azure HDInsight
description: Den här artikeln beskriver de två krypterings lager som är tillgängliga för data i vila på Azure HDInsight-kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 3d4f9e3be02a64efa058ea1f84a3e261cb6166fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867125"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight Double Encryption för vilande data

Den här artikeln beskriver metoder för kryptering av data i vila i Azure HDInsight-kluster. Data kryptering i vila avser kryptering på hanterade diskar (data diskar, OS-diskar och temporära diskar) som är anslutna till virtuella HDInsight-kluster datorer. 

Det här dokumentet hanterar inte data som lagras i ditt Azure Storage-konto. Dina kluster kan ha ett eller flera kopplade Azure Storage konton där krypterings nycklarna också kan hanteras av Microsoft eller kund, men krypterings tjänsten skiljer sig åt. Mer information om Azure Storage kryptering finns i [Azure Storage kryptering av data i vila](../storage/common/storage-service-encryption.md).

## <a name="introduction"></a>Introduktion

Det finns tre huvudsakliga hanterade disk roller i Azure: data disken, OS-disken och den temporära disken. Mer information om olika typer av hanterade diskar finns i [Introduktion till Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

HDInsight stöder flera typer av kryptering i två olika lager:

- Server Side Encryption (SSE)-SSE utförs av lagrings tjänsten. I HDInsight används SSE för att kryptera OS-diskar och data diskar. Den är aktive rad som standard. SSE är en krypterings tjänst på nivå 1.
- Kryptering på värden med hjälp av plattforms hanterad nyckel, liknande SSE, utförs den här typen av kryptering av lagrings tjänsten. Det är dock bara för temporära diskar och är inte aktiverat som standard. Kryptering på värden är också en krypterings tjänst på nivå 1.
- Kryptering i vila med kund hanterad nyckel – den här typen av kryptering kan användas på data och temporära diskar. Den är inte aktive rad som standard och kräver att kunden tillhandahåller sin egen nyckel via Azure Key Vault. Kryptering i vila är en krypterings tjänst på nivå 2.

De här typerna sammanfattas i följande tabell.

|Kluster typ |OS-disk (hanterad disk) |Data disk (hanterad disk) |Temporär data disk (lokal SSD) |
|---|---|---|---|
|Kafka, HBase med accelererade skrivningar|LAYER1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard|LAYER1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard, layer2: Valfri kryptering i vila med CMK|LAYER1: Valfri kryptering på värden som använder PMK, layer2: Valfri kryptering i vila med CMK|
|Alla andra kluster (Spark, Interactive, Hadoop, HBase utan accelererade skrivningar)|LAYER1: [SSE-kryptering](../virtual-machines/managed-disks-overview.md#encryption) som standard|Ej tillämpligt|LAYER1: Valfri kryptering på värden som använder PMK, layer2: Valfri kryptering i vila med CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Kryptering i vila med Kundhanterade nycklar

Kundhanterad nyckel kryptering är en enda stegs process som hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att auktorisera en hanterad identitet med Azure Key Vault och lägga till krypterings nyckeln när du skapar klustret.

Både data diskar och temporära diskar på varje nod i klustret krypteras med en symmetrisk data krypterings nyckel (DEK). DEK skyddas med nyckel krypterings nyckeln (KEK) från ditt nyckel valv. Krypterings-och dekrypterings processerna hanteras helt av Azure HDInsight.

För OS-diskar som är anslutna till de virtuella datorerna i klustret är endast ett krypterings lager tillgängligt (PMK). Vi rekommenderar att kunderna inte kopierar känsliga data till OS-diskar om en CMK-kryptering krävs för sina scenarier.

Om Key Vault-brandväggen är aktive rad i nyckel valvet där disk krypterings nyckeln lagras, måste den regionala HDInsight-providerns IP-adresser för den region där klustret ska distribueras läggas till i konfiguration av Key Vault-brandväggen. Detta är nödvändigt eftersom HDInsight inte är en betrodd Azure Key Vault-tjänst.

Du kan använda Azure Portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckel valvet. När en nyckel roterar börjar HDInsight-klustret med den nya nyckeln inom några minuter. Aktivera funktionen för nyckel skydd för [mjuk borttagning](../key-vault/general/soft-delete-overview.md) för att skydda dig mot utpressnings scenarier och oavsiktlig borttagning. Nyckel valv utan denna skydds funktion stöds inte.

### <a name="get-started-with-customer-managed-keys"></a>Kom igång med Kundhanterade nycklar

Vi går igenom följande steg för att skapa ett kundhanterat nyckel aktiverat HDInsight-kluster:

1. Skapa hanterade identiteter för Azure-resurser
1. Skapa Azure Key Vault
1. Skapa nyckel
1. Skapa åtkomst princip
1. Skapa HDInsight-kluster med kundhanterad nyckel aktive rad
1. Rotera krypterings nyckeln

Varje steg förklaras i något av följande avsnitt i detalj.

### <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

Skapa en användardefinierad hanterad identitet för att autentisera till Key Vault.

Se [skapa en användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för vissa steg. Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md). Se till att spara resurs-ID: t för den hanterade identiteten när du lägger till det i Key Vault åtkomst principen.

### <a name="create-azure-key-vault"></a>Skapa Azure Key Vault

Skapa ett nyckelvalv. Se [skapa Azure Key Vault](../key-vault/general/quick-create-portal.md) för vissa steg.

HDInsight har endast stöd för Azure Key Vault. Om du har ett eget nyckel valv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att nyckel valvet måste ha **mjuk borttagning** aktiverat. Mer information om hur du importerar befintliga nycklar finns på [nycklar, hemligheter och certifikat](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Skapa nyckel

1. Från det nya nyckel valvet navigerar du till **Inställningar**  >  **nycklar**  >  **+ generera/importera**.

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Generera en ny nyckel i Azure Key Vault":::

1. Ange ett namn och välj sedan **skapa**. Behåll standard **nyckel typen** för **RSA**.

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="genererar nyckel namn":::

1. När du kommer tillbaka till sidan **nycklar** väljer du den nyckel som du skapade.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="nyckel lista för nyckel valv":::

1. Välj version för att öppna sidan med **nyckel versionen** . När du använder din egen nyckel för kluster kryptering i HDInsight måste du ange nyckel-URI: n. Kopiera **nyckel identifieraren** och spara den någonstans tills du är redo att skapa klustret.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="Hämta nyckel identifierare":::

### <a name="create-access-policy"></a>Skapa åtkomst princip

1. Från det nya nyckel valvet navigerar du till **Inställningar**  >  **åtkomst principer**  >  **+ Lägg till åtkomst princip**.

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Skapa ny princip för Azure Key Vault åtkomst":::

1. På sidan **Lägg till åtkomst princip** anger du följande information:

    |Egenskap |Beskrivning|
    |---|---|
    |Nyckel behörigheter|Välj **Hämta**, **packa upp nyckel** och **Radbryt nyckel**.|
    |Hemliga behörigheter|Välj **Hämta**, **Ange** och **ta bort**.|
    |Välj huvud konto|Välj den användare som tilldelats den hanterade identitet som du skapade tidigare.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Ange Välj huvud konto för Azure Key Vault åtkomst princip":::

1. Välj **Lägg till**.

1. Välj **Spara**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Spara Azure Key Vault åtkomst princip":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Skapa kluster med kund hanterad nyckel disk kryptering

Nu är du redo att skapa ett nytt HDInsight-kluster. Kundhanterade nycklar kan bara tillämpas på nya kluster när klustret skapas. Det går inte att ta bort kryptering från kund hanterade nyckel kluster och Kundhanterade nycklar kan inte läggas till i befintliga kluster.

Från och med november 2020-utgåvan stöder HDInsight skapandet av kluster med hjälp av både versions-och versions lösa nyckel-URI: er. Om du skapar klustret med en versions lös nyckel-URI kommer HDInsight-klustret att försöka utföra automatisk rotation när nyckeln uppdateras i Azure Key Vault. Om du skapar klustret med en versions nyckel-URI måste du utföra en manuell nyckel rotation enligt beskrivningen i [rotationen av krypterings nyckeln](#rotating-the-encryption-key).

För kluster som skapats före november 2020-versionen måste du utföra nyckel rotation manuellt med den versions bara nyckel-URI: n.

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

När klustret skapas kan du antingen använda en versions nyckel eller en versions hanterings nyckel på följande sätt:

- **Versions hantering** – ange den fullständiga **nyckel identifieraren**, inklusive nyckel versionen, under skapandet av klustret. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Versions hantering** – ange endast **nyckel-ID** när klustret skapas. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Du måste också tilldela den hanterade identiteten till klustret.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Skapa nytt kluster":::

#### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du använder Azure CLI för att skapa ett nytt Apache Spark kluster med disk kryptering aktiverat. Mer information finns i [Azure CLI AZ HDInsight Create](/cli/azure/hdinsight#az-hdinsight-create). Parametern `encryption-key-version` är valfri.

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

I följande exempel visas hur du använder en Azure Resource Manager mall för att skapa ett nytt Apache Spark kluster med disk kryptering aktiverat. Mer information finns i [Vad är arm-mallar?](../azure-resource-manager/templates/overview.md). Egenskapen Resource Manager-mall `diskEncryptionKeyVersion` är valfri.

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

Innehållet i resurs hanterings mal len `azuredeploy.json` :

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

### <a name="rotating-the-encryption-key"></a>Rotera krypterings nyckeln

Du kan ändra de krypterings nycklar som används i det kluster som körs med hjälp av Azure Portal eller Azure CLI. För den här åtgärden måste klustret ha åtkomst till både den aktuella nyckeln och den avsedda nya nyckeln, annars går det inte att rotera nyckeln. För kluster som skapats efter versionen från november 2020 kan du välja om du vill att din nya nyckel ska ha en version eller inte. För kluster som skapats före versionen från november 2020 måste du använda en versions nyckel när du roterar krypterings nyckeln.

#### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill rotera nyckeln behöver du URI för bas nyckel valvet. När du har gjort det går du till avsnittet HDInsight-kluster egenskaper i portalen och klickar på **ändra nyckel** under **URL för disk krypterings nyckel**. Ange den nya nyckel-URL: en och skicka för att rotera nyckeln.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="rotera disk krypterings nyckel":::

#### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du roterar disk krypterings nyckeln för ett befintligt HDInsight-kluster. Mer information finns i [Azure CLI AZ HDInsight rotation-Disk-Encryption-Key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Vanliga frågor och svar om kund hanterad nyckel kryptering

**Hur kommer HDInsight-klustret åt mitt nyckel valv?**

HDInsight ansluter Azure Key Vault-instansen med hjälp av den hanterade identitet som du associerar med HDInsight-klustret. Den här hanterade identiteten kan skapas innan eller när klustret skapas. Du måste också bevilja åtkomst till hanterad identitet till nyckel valvet där nyckeln lagras.

**Är den här funktionen tillgänglig för alla kluster i HDInsight?**

Kundhanterad nyckel kryptering är tillgänglig för alla kluster typer förutom Spark 2,1 och 2,2.

**Kan jag använda flera nycklar för att kryptera olika diskar eller mappar?**

Nej, alla hanterade diskar och resurs diskar krypteras med samma nyckel.

**Vad händer om klustret förlorar åtkomst till nyckel valvet eller nyckeln?**

Om klustret förlorar åtkomst till nyckeln, visas varningar i Apache Ambari-portalen. I det här läget går det inte att utföra **ändrings nyckeln** . När nyckel åtkomsten har återställts försvinner Ambari-varningar och åtgärder som nyckel rotation kan utföras.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="avisering om nyckel åtkomst Ambari":::

**Hur kan jag återställa klustret om nycklarna tas bort?**

Eftersom enbart "mjuk borttagning"-aktiverade nycklar stöds, om nycklarna återställs i nyckel valvet, bör klustret återfå åtkomst till nycklarna. Om du vill återställa en Azure Key Vault nyckel, se [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) eller [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Kommer de nya noderna att stödja Kundhanterade nycklar sömlöst om ett kluster skalas upp?**

Ja. Klustret behöver åtkomst till nyckeln i nyckel valvet vid skalning. Samma nyckel används för att kryptera både hanterade diskar och resurs diskar i klustret.

**Finns kundens Kundhanterade nycklar på min plats?**

HDInsight Kundhanterade nycklar är tillgängliga i alla offentliga moln och i nationella moln.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Kryptering på värden med hjälp av plattforms hanterade nycklar

### <a name="enable-in-the-azure-portal"></a>Aktivera i Azure Portal

Kryptering på värden kan aktive ras när klustret skapas i Azure Portal.

> [!Note]
> När kryptering på värden är aktiverat kan du inte lägga till program i ditt HDInsight-kluster från Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Aktivera kryptering på värden.":::

Med det här alternativet aktive ras [kryptering på värden](../virtual-machines/disks-enable-host-based-encryption-portal.md) för virtuella HDInsight-datorer temporära data diskar som använder PMK. Kryptering på värden stöds bara [på vissa VM SKU: er i begränsade regioner](../virtual-machines/disks-enable-host-based-encryption-portal.md) och HDInsight stöder [följande konfiguration och SKU: er för noden](./hdinsight-supported-node-configuration.md).

Om du vill förstå rätt VM-storlek för ditt HDInsight-kluster ser du [välja rätt VM-storlek för ditt Azure HDInsight-kluster](hdinsight-selecting-vm-size.md). Standard-VM-SKU: n för Zookeeper-noden när kryptering på värden är aktive rad kommer att vara DS2V2.

### <a name="enable-using-powershell"></a>Aktivera med PowerShell

Följande kodfragment visar hur du kan skapa ett nytt Azure HDInsight-kluster som har kryptering på värd aktive rad med PowerShell. Den använder parametern `-EncryptionAtHost $true` för att aktivera funktionen.

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

Följande kodfragment visar hur du kan skapa ett nytt Azure HDInsight-kluster som har kryptering på värd aktiverat, med hjälp av Azure CLI. Den använder parametern `--encryption-at-host true` för att aktivera funktionen.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Key Vault finns i [Vad är Azure Key Vault](../key-vault/general/overview.md).
* [Översikt över företags säkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
