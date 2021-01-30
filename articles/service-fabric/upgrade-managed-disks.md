---
title: Uppgradera klusternoder för att använda Azure Managed disks
description: Så här uppgraderar du ett befintligt Service Fabric-kluster för att använda Azure Managed disks med liten eller ingen stillestånds tid för klustret.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: c374c4536309a13abcf8c882b041a9c5357878e5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090662"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Uppgradera klusternoder för att använda Azure Managed disks

[Azure Managed disks](../virtual-machines/managed-disks-overview.md) är det rekommenderade disk lagrings erbjudandet för användning med Azure Virtual Machines för beständig data lagring. Du kan förbättra återhämtningen av dina Service Fabric arbets belastningar genom att uppgradera de virtuella datorernas skalnings uppsättningar som ligger bakom dina nodtyper för att använda hanterade diskar. Så här uppgraderar du ett befintligt Service Fabric-kluster för att använda Azure Managed disks med liten eller ingen stillestånds tid för klustret.

Den allmänna strategin för att uppgradera en Service Fabric klusternod till att använda hanterade diskar är att:

1. Distribuera en annan virtuell dators skalnings uppsättning för den nodtypen, men med [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) -objektet tillagt i `osDisk` avsnittet i distributions mal len för skalnings uppsättning för virtuell dator. Den nya skalnings uppsättningen ska bindas till samma belastningsutjämnare/IP-adress som den ursprungliga, så att dina kunder inte upplever avbrott i tjänsten under migreringen.

2. När båda de ursprungliga och uppgraderade skalnings uppsättningarna körs sida vid sida inaktiverar du de ursprungliga instans instanserna en i taget så att system tjänsterna (eller replikerna av tillstånds känsliga tjänster) migreras till den nya skalnings uppsättningen.

3. Kontrol lera att klustret och de nya noderna är felfria och ta sedan bort den ursprungliga skalnings uppsättningen och nodens tillstånd för de borttagna noderna.

Den här artikeln beskriver steg för steg hur du uppgraderar den primära nodtypen i ett exempel kluster för att använda hanterade diskar, samtidigt som du undviker eventuella avbrott i klustret (se OBS!). Det första stadiet i exempel-test klustret består av en nodtyp av [silver tålighet](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), med en enda skalnings uppsättning med fem noder.

> [!NOTE]
> Begränsningarna för en Basic SKU-belastningsutjämnare förhindrar att ytterligare en skalnings uppsättning läggs till. Vi rekommenderar att du använder standard-SKU-belastningsutjämnaren i stället. Mer information finns i [jämförelse av de två SKU: erna](../load-balancer/skus.md).

> [!CAUTION]
> Du får bara ett avbrott med den här proceduren om du har beroenden för kluster-DNS (till exempel vid åtkomst till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). [Bästa praxis för klient dels tjänster](/azure/architecture/microservices/design/gateway) är att ha någon typ av belastningsutjämnare framför dina nodtyper för att göra det möjligt att växla [mellan](/azure/architecture/guide/technology-choices/load-balancing-overview) noder utan avbrott.

Här följer [mallar och cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) för Azure Resource Manager som vi använder för att slutföra uppgraderings scenariot. Mal lin liga ändringar kommer att förklaras i [distribuera en uppgraderad skalnings uppsättning för den primära nodtypen](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  nedan.

## <a name="set-up-the-test-cluster"></a>Konfigurera test klustret

Vi konfigurerar det första Service Fabric test klustret. Börja med att [hämta](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) Azure Resource Manager exempel mallar som vi ska använda för att slutföra det här scenariot.

Logga sedan in på ditt Azure-konto.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Följande kommandon vägleder dig genom att skapa ett nytt självsignerat certifikat och distribuera test klustret. Om du redan har ett certifikat som du vill använda, kan du hoppa över att [använda ett befintligt certifikat för att distribuera klustret](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generera ett självsignerat certifikat och distribuera klustret

Först tilldelar du de variabler du behöver för Service Fabric kluster distribution. Justera värdena för `resourceGroupName` ,  `certSubjectName` , `parameterFilePath` och `templateFilePath` för ditt eget konto och din miljö:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Kontrol lera att `certOutputFolder` platsen finns på den lokala datorn innan du kör kommandot för att distribuera ett nytt Service Fabric-kluster.

Öppna sedan [*Initial-1NodeType-UnmanagedDisks.parameters.jspå*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) filen och justera värdena för `clusterName` och `dnsName` för att motsvara de dynamiska värden som du angav i PowerShell och spara ändringarna.

Distribuera sedan Service Fabric test kluster:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

När distributionen är klar letar du upp *. pfx* -filen ( `$certPfx` ) på den lokala datorn och importerar den till certifikat arkivet:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Åtgärden returnerar certifikatets tumavtryck, som du kommer att använda för att [ansluta till det nya klustret](#connect-to-the-new-cluster-and-check-health-status) och kontrol lera dess hälso status. (Hoppa över följande avsnitt, som är en alternativ metod för kluster distribution.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Använd ett befintligt certifikat för att distribuera klustret

Du kan också använda ett befintligt Azure Key Vault-certifikat för att distribuera test klustret. För att göra detta måste du [Hämta referenser till Key Vault](#obtain-your-key-vault-references) och tumavtryck för certifikatet.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Öppna filen [*Initial-1NodeType-UnmanagedDisks.parameters.js*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) och ändra värdena för `clusterName` och `dnsName` till något unikt.

Ange slutligen ett resurs grupp namn för klustret och ange `templateFilePath` `parameterFilePath` platser och platser för dina *initial-1NodeType-UnmanagedDisks-* filer:

> [!NOTE]
> Den angivna resurs gruppen måste redan finnas och finnas i samma region som din Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Kör slutligen följande kommando för att distribuera det första test klustret:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Anslut till det nya klustret och kontrol lera hälso status

Anslut till klustret och se till att alla fem noderna är felfria (ersätter `clusterName` `thumb` variablerna och för klustret):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Vi är nu redo att påbörja uppgraderings proceduren.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Distribuera en uppgraderad skalnings uppsättning för den primära nodtypen

För att kunna uppgradera, eller *vertikalt skala*, måste vi distribuera en kopia av den här nodtypen för skalnings uppsättningen för virtuella datorer, vilket annars är identiskt med den ursprungliga skalnings uppsättningen (inklusive referens till samma `nodeTypeRef` , `subnet` och `loadBalancerBackendAddressPools` ) förutom att den innehåller den önskade uppgraderingen/ändringarna och det egna separata under nätet och den inkommande NAT-adresspoolen. Eftersom vi uppgraderar en Primary Node-typ markeras den nya skalnings uppsättningen som primär ( `isPrimary: true` ), precis som den ursprungliga skalnings uppsättningen. (För uppgraderingar som inte är primära för en annan nod utesluter du bara detta.)

För enkelhetens skull har de nödvändiga ändringarna redan gjorts åt dig i filerna *Upgrade-1NodeType-2ScaleSets-ManagedDisks* [Template](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) och [Parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) .

I följande avsnitt förklaras mallens ändringar i detalj. Om du vill kan du hoppa över förklaringen och fortsätta till [Nästa steg i uppgraderings proceduren](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Uppdatera kluster mal len med den uppgraderade skalnings uppsättningen

Här är ändringar i den ursprungliga kluster distributions mal len för att lägga till en uppgraderad skalnings uppsättning för den primära nodtypen.

#### <a name="parameters"></a>Parametrar

Lägg till parametrar för instans namn, antal och storlek på den nya skalnings uppsättningen. Observera att `vmNodeType1Name` är unikt för den nya skalnings uppsättningen, medan värdena count och size är identiska med den ursprungliga skalnings uppsättningen.

**Mallfil**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Parameter fil**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variabler

I `variables` avsnittet distributionsmall lägger du till en post för den inkommande NAT-adresspoolen för den nya skalnings uppsättningen.

**Mallfil**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Resurser

I avsnittet *resurser* för distributions mal len lägger du till den nya skalnings uppsättningen för virtuella datorer. Tänk på följande:

* Den nya skalnings uppsättningen refererar till samma nodtyp som den ursprungliga:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Den nya skalnings uppsättningen refererar till Server delen och under nätet för belastnings Utjämnings servern (men använder en annan inkommande NAT-pool för belastnings utjämning):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Precis som den ursprungliga skalnings uppsättningen markeras den nya skalnings uppsättningen som den primära nodtypen. (Om du uppgraderar icke-primära nodtyper utelämnar du den här ändringen.)

    ```json
    "isPrimary": true,
    ```

* Till skillnad från den ursprungliga skalnings uppsättningen uppgraderas den nya skalnings uppsättningen för att använda hanterade diskar.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

När du har implementerat alla ändringar i mall-och frågeparametrar kan du fortsätta till nästa avsnitt för att hämta dina Key Vault referenser och distribuera uppdateringarna till klustret.

### <a name="obtain-your-key-vault-references"></a>Hämta dina Key Vault referenser

Om du vill distribuera den uppdaterade konfigurationen måste du först få flera referenser till ditt kluster certifikat som lagras i din Key Vault. Det enklaste sättet att hitta dessa värden är genom Azure Portal. Du behöver:

* **Key Vault-URL för ditt kluster certifikat.** Från din Key Vault i Azure Portal väljer du **certifikat**  >  *ditt önskade certifikat*  >  **hemlighets identifierare**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Tumavtryck för ditt kluster certifikat.** (Du har förmodligen redan det här om du har [anslutit till det första klustret](#connect-to-the-new-cluster-and-check-health-status) för att kontrol lera hälso statusen.) Från bladet samma certifikat (**certifikat**  >  som *det önskade certifikatet*) i Azure Portal kopierar du **X. 509 SHA-1-tumavtryck (i hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Resurs-ID för Key Vault.** Från Key Vault i Azure Portal väljer du **Egenskaper**  >  **resurs-ID**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuera den uppdaterade mallen

Justera `parameterFilePath` och `templateFilePath` kör sedan följande kommando:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

När distributionen är klar kontrollerar du kluster hälsan igen och ser till att alla tio noder (fem på originalet och fem på den nya skalnings uppsättningen) är felfria.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrera Seed-noder till den nya skalnings uppsättningen

Nu är det dags att börja inaktivera noderna i den ursprungliga skalnings uppsättningen. Eftersom dessa noder blir inaktiverade migreras system tjänsterna och dirigeringsrouters till de virtuella datorerna i den nya skalnings uppsättningen eftersom den också är markerad som den primära nodtypen.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Använd Service Fabric Explorer för att övervaka migreringen av startnoder till den nya skalnings uppsättningen och förloppet för noder i den ursprungliga skalnings uppsättningen från att *inaktivera* till *inaktive rad* status.

![Service Fabric Explorer visar status för inaktiverade noder](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Det kan ta lite tid att slutföra den inaktiverade åtgärden för alla noder i den ursprungliga skalnings uppsättningen. För att garantera data konsekvens kan endast en Seed-nod ändras i taget. För varje Seed-nods ändring krävs en kluster uppdatering. att ersätta en Seed-nod kräver därför två kluster uppgraderingar (en varje för nod tillägg och borttagning). Om du uppgraderar de fem startnoderna i det här exempel scenariot uppstår tio kluster uppgraderingar.

## <a name="remove-the-original-scale-set"></a>Ta bort den ursprungliga skalnings uppsättningen

Ta bort skalnings uppsättningen när inaktive rings åtgärden har slutförts.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

I Service Fabric Explorer kommer de borttagna noderna (och därmed *kluster hälso tillståndet*) nu att visas i *fel* tillstånd.

![Service Fabric Explorer visar inaktiverade noder i fel tillstånd](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Ta bort de föråldrade noderna från Service Fabric-klustret för att återställa hälso tillståndet för klustret till *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer med noder i fel tillstånd borttaget](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Nästa steg

I den här genom gången har du lärt dig hur du uppgraderar den virtuella datorns skalnings uppsättningar för ett Service Fabric-kluster för att använda hanterade diskar och undvika avbrott i tjänsten under processen. Mer information om närliggande ämnen finns i följande resurser.

Lär dig att:

* [Skala upp en primär nodtyp för Service Fabric-klustret](service-fabric-scale-up-primary-node-type.md)

* [Konvertera en mall för skalnings uppsättningar för att använda hanterade diskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Ta bort en Service Fabric nodtyp](service-fabric-how-to-remove-node-type.md)

Se även:

* [Exempel: uppgradera klusternoder för att använda Azure Managed disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)

* [Överväganden vid vertikal skalning](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)