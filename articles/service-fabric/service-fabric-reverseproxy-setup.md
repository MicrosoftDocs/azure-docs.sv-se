---
title: Konfigurera omvänd proxy för Azure Service Fabric
description: Lär dig hur du konfigurerar och konfigurerar tjänsten reverse proxy för ett Azure Service Fabric-program.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256336"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Konfigurera och konfigurera omvänd proxy i Azure Service Fabric
Omvänd proxy är en valfri Azure Service Fabric-tjänst som hjälper mikrotjänster som körs i ett Service Fabric kluster att identifiera och kommunicera med andra tjänster som har http-slutpunkter. Mer information finns i [omvänd proxy i Azure Service Fabric](service-fabric-reverseproxy.md). Den här artikeln visar hur du konfigurerar och konfigurerar omvänd proxy i klustret. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Aktivera omvänd proxy med Azure Portal

Azure Portal innehåller ett alternativ för att aktivera omvänd proxy när du skapar ett nytt Service Fabric-kluster. Du kan inte uppgradera ett befintligt kluster om du vill använda omvänd proxy via portalen. 

Om du vill konfigurera omvänd proxy när du [skapar ett kluster med hjälp av Azure Portal](./service-fabric-cluster-creation-via-portal.md), se till att göra följande:

1. I **steg 2: kluster konfiguration**, under **typ av nodtyp**, väljer du **Aktivera omvänd proxy**.

   ![Aktivera omvänd proxy på portalen](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Valfritt Om du vill konfigurera säker omvänd proxy måste du konfigurera ett TLS/SSL-certifikat. I **steg 3: säkerhet** går du till **Konfigurera kluster säkerhets inställningar**, under **konfigurations typ**, och väljer **anpassad**. Under **reversed proxy SSL-certifikat** väljer du sedan **inkludera ett SSL-certifikat för omvänd proxy** och anger information om certifikatet.

   ![Konfigurera säker omvänd proxy på portalen](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Om du väljer att inte konfigurera den omvända proxyn med ett certifikat när du skapar klustret kan du göra det senare via Resource Manager-mallen för klustrets resurs grupp. Läs mer i [Aktivera omvänd proxy via Azure Resource Manager mallar](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Aktivera omvänd proxy via Azure Resource Manager mallar

För kluster i Azure kan du använda Azure Resource Manager-mallen för att aktivera omvänd proxy i Service Fabric. Du kan aktivera omvänd proxy när du skapar klustret eller aktiverar det genom att uppdatera klustret vid ett senare tillfälle. 

För ett nytt kluster kan du [skapa en anpassad Resource Manager-mall](service-fabric-cluster-creation-via-arm.md) eller så kan du använda en exempel mall. 

Du hittar exempel på Resource Manager-mallar som kan hjälpa dig att konfigurera säker omvänd proxy för ett Azure-kluster i [Sample-mallarna för säker omvänd proxy](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) på GitHub. Se [Konfigurera https omvänd proxy i ett säkert kluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) i Readme-filen för instruktioner och mallar som ska användas för att konfigurera säker omvänd proxy med ett certifikat och för att hantera certifikat förnyelse.

För ett befintligt kluster kan du exportera Resource Manager-mallen för klustrets resurs grupp med hjälp av [Azure Portal](../azure-resource-manager/templates/export-template-portal.md), [POWERSHELL](../azure-resource-manager/management/manage-resources-powershell.md)eller [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md).

När du har en Resource Manager-mall kan du aktivera omvänd proxy med följande steg:

1. Definiera en port för omvänd proxy i [avsnittet parametrar](../azure-resource-manager/templates/template-syntax.md) i mallen.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Ange porten för varje NodeType-objekt i avsnittet [**Microsoft. ServiceFabric/Clusters**](/azure/templates/microsoft.servicefabric/clusters) ( [resurs typ](../azure-resource-manager/templates/template-syntax.md)).

    Porten identifieras av parameter namnet reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Om du vill konfigurera TLS/SSL-certifikat på porten för den omvända proxyn lägger du till certifikatet till egenskapen ***reverseProxyCertificate** _ i resurs typen _ *Microsoft. ServiceFabric/Clusters* *  [](../azure-resource-manager/templates/template-syntax.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Stöd för ett reverse proxy-certifikat som skiljer sig från kluster certifikatet
 Om det omvända proxy-certifikatet skiljer sig från det certifikat som skyddar klustret, ska det tidigare angivna certifikatet installeras på den virtuella datorn och läggas till i åtkomst kontrol listan (ACL) så att Service Fabric kan komma åt det. Detta kan göras i avsnittet [**Microsoft. Compute/virtualMachineScaleSets-**](/azure/templates/microsoft.compute/virtualmachinescalesets) [resurs typ](../azure-resource-manager/templates/template-syntax.md). För installation lägger du till certifikatet i osProfile. Tilläggs avsnittet i mallen kan uppdatera certifikatet i ACL: en.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> När du använder certifikat som skiljer sig från kluster certifikatet för att aktivera omvänd proxy i ett befintligt kluster, installerar du det omvända proxy-certifikatet och uppdaterar ACL: en på klustret innan du aktiverar den omvända proxyn. Slutför distributionen av [Azure Resource Manager mallar](service-fabric-cluster-creation-via-arm.md) genom att använda inställningarna ovan innan du påbörjar en distribution för att aktivera omvänd proxy i steg 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Aktivera omvänd proxy i fristående kluster

För fristående kluster aktiverar du omvänd proxy i ClusterConfig.jspå filen. Du kan aktivera omvänd proxy när klustret skapas eller genom att uppgradera konfigurationen för ett befintligt kluster. Mer information om inställningarna som är tillgängliga i ClusterConfig.jspå filer finns i [Inställningar för fristående kluster](./service-fabric-cluster-manifest.md).

Följande steg visar vilka inställningar som ska användas för att aktivera omvänd proxy och eventuellt för att skydda den omvända proxyn med ett X. 509-certifikat. 

1. Om du vill aktivera omvänd proxy ställer du in värdet **reverseProxyEndpointPort** för nodtypen under **Egenskaper** i kluster konfigurationen. Följande JSON visar inställning av slut punkts porten för omvänd proxy till 19081 för noder med typen "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. Valfritt För en säker omvänd proxy konfigurerar du ett certifikat i avsnittet **säkerhet** under **Egenskaper**. 
   - För en utvecklings-eller test miljö kan du använda inställningen **ReverseProxyCertificate** :

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - För en produktions miljö rekommenderas **ReverseProxyCertificateCommonNames** -inställningen:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Om du vill veta mer om hur du konfigurerar och hanterar certifikat för ett fristående kluster, samt mer information om hur du konfigurerar certifikat som används för att skydda omvänd proxy, se [X509-certifikatbaserad säkerhet](./service-fabric-windows-cluster-x509-security.md).

När du har ändrat din ClusterConfig.jspå fil för att aktivera omvänd proxy, följer du anvisningarna i [uppgradera kluster konfigurationen](service-fabric-cluster-config-upgrade-windows-server.md) för att skicka ändringarna till klustret.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Exponera omvänd proxy på en offentlig port via Azure Load Balancer

Om du vill åtgärda den omvända proxyn från en plats utanför ett Azure-kluster konfigurerar du Azure Load Balancer-regler och en Azure-hälsoavsökning för porten för omvänd proxy. De här stegen kan utföras med hjälp av Azure Portal eller Resource Manager-mallen när som helst efter att klustret har skapats. 

> [!WARNING]
> När du konfigurerar porten för omvänd proxy i Load Balancer adresseras alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt utanför klustret. Det innebär att mikrotjänster som är avsedda att vara interna kan identifieras av en bestämd skadlig användare. Detta potentiellt visar allvarliga sårbarheter som kan utnyttjas. exempel:
>
> * En obehörig användare kan starta en denial of service-attack genom att upprepade gånger anropa en intern tjänst som inte har en tillräckligt härdad attack yta.
> * En obehörig användare kan leverera felaktiga paket till en intern tjänst, vilket resulterar i oavsiktligt beteende.
> * En tjänst som är avsedd att vara intern kan returnera privat eller känslig information som inte är avsedd att exponeras för tjänster utanför klustret, vilket innebär att denna känsliga information exponeras för en obehörig användare. 
>
> Se till att du fullständigt förstår och minimerar potentiella säkerhets aspekterna för ditt kluster och appar som körs på den, innan du gör den omvända proxy-porten offentlig. 
>

Om du vill exponera omvänd proxy offentligt för ett fristående kluster, beror det på hur du gör det i systemet som är värd för klustret och ligger utanför den här artikelns omfång. Föregående varning om att exponera omvänd proxy offentligt, gäller dock fortfarande.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Exponera den omvända proxyn med Azure Portal 

1. Klicka på resurs gruppen för klustret i Azure Portal och klicka sedan på belastnings utjämning för klustret.
2. Om du vill lägga till en hälso avsökning för porten för omvänd proxy klickar du på **hälso avsökningar** under **Inställningar** i den vänstra rutan i fönstret belastningsutjämnare. Klicka sedan på **Lägg till** överst i fönstret hälso avsökningar och ange information om porten för omvänd proxy. Klicka sedan på **OK**. Som standard är den omvända proxy-porten 19081, om du inte har ändrat den när du skapade klustret.

   ![Konfigurera hälso avsökning för omvänd proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Om du vill lägga till en Load Balancer regel för att exponera den omvända **proxy-porten** klickar du på **belastnings Utjämnings regler** i den vänstra rutan i fönstret belastningsutjämnare. Klicka sedan på **Lägg till** överst i fönstret regler för belastnings utjämning och ange information om porten för omvänd proxy. Se till att du anger **Portvärdet** till den port som du vill att den omvända proxyn ska exponeras på, **Server dels port** svärdet för den port som du angav när du aktiverade omvänd proxy och **hälso avsökning** svärdet för den hälso avsökning som du konfigurerade i föregående steg. Ange andra fält som lämpliga och klicka på **OK**.

   ![Konfigurera belastnings Utjämnings regel för omvänd proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Exponera den omvända proxyn via Resource Manager-mallar

Följande JSON refererar till samma mall som används i [Aktivera omvänd proxy via Azure Resource Manager mallar](#enable-reverse-proxy-via-azure-resource-manager-templates). Läs avsnittet i dokumentet om du vill ha information om hur du skapar en Resource Manager-mall eller exporterar en mall för ett befintligt kluster.  Ändringarna görs i avsnittet [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadbalancers) [resurs typ](../azure-resource-manager/templates/template-syntax.md).

```json
{
    "apiVersion": "[variables('lbApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    ...
    ...
    "loadBalancingRules": [
        ...
        {
            "name": "LBSFReverseProxyRule",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('SFReverseProxyPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"
                },
                "frontendPort": "[parameters('SFReverseProxyPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                },
                "protocol": "tcp"
            }
        }
    ],
    "probes": [
        ...
        {
            "name": "SFReverseProxyProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port":     "[parameters('SFReverseProxyPort')]",
                "protocol": "tcp"
            }
        }  
    ]
}
```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Anpassa beteendet för omvänd proxy med hjälp av infrastruktur inställningar

Du kan anpassa beteendet för omvänd proxy via infrastruktur inställningarna i Resource Manager-mallen för kluster som finns i Azure eller i ClusterConfig.jspå fil för fristående kluster. Inställningar som styr beteendet för omvänd proxy finns i avsnittet [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) i avsnittet **fabricSettings** under avsnittet kluster **Egenskaper** . 

Du kan till exempel ange värdet för **DefaultHttpRequestTimeout** för att ange tids gränsen för begär anden till den omvända proxyn till 180 sekunder som i följande JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Mer information om hur du uppdaterar infrastruktur inställningar för Azure-kluster finns i [Anpassa kluster inställningar med Resource Manager-mallar](service-fabric-cluster-config-upgrade-azure.md). Information om fristående kluster finns i [Anpassa kluster inställningar för fristående kluster](service-fabric-cluster-config-upgrade-windows-server.md). 

Flera infrastruktur inställningar används för att upprätta säker kommunikation mellan omvänd proxy och tjänster. Detaljerad information om de här inställningarna finns i [ansluta till en säker tjänst med den omvända proxyn](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Nästa steg
* [Konfigurera vidarebefordran till säker HTTP-tjänst med den omvända proxyn](service-fabric-reverseproxy-configure-secure-communication.md)
* Konfigurations alternativ för omvänd proxy finns [i avsnittet ApplicationGateway/http i anpassa Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
