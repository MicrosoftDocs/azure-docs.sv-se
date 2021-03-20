---
title: Hantera certifikat i ett Azure Service Fabric-kluster
description: Beskriver hur du lägger till nya certifikat, förnyar certifikat och tar bort certifikat till eller från ett Service Fabric kluster.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6dd4440d76bed9d110c13baab9f4e67b3a5c64c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660924"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Lägga till eller ta bort certifikat för ett Service Fabric-kluster i Azure
Vi rekommenderar att du bekantar dig med hur Service Fabric använder X. 509-certifikat och känner till [kluster säkerhets scenarier](service-fabric-cluster-security.md). Du måste förstå vad ett kluster certifikat är och vad som används för, innan du fortsätter.

Azure Service Fabric SDK: s standard beteende för certifikat inläsning är att distribuera och använda det definierade certifikatet med förfallo datum längst fram i framtiden. oavsett primär eller sekundär konfigurations definition. Att återgå till det klassiska beteendet är en rekommenderad avancerad åtgärd och kräver att värdet för parametern "UseSecondaryIfNewer" anges till false i `Fabric.Code` konfigurationen.

Med Service Fabric kan du ange två kluster certifikat, en primär och en sekundär, när du konfigurerar certifikat säkerhet när klustret skapas, förutom klient certifikat. Se hur du [skapar ett Azure-kluster via portalen](service-fabric-cluster-creation-via-portal.md) eller [skapar ett Azure-kluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) för information om hur du konfigurerar dem i Create Time. Om du bara anger ett kluster certifikat vid skapande tillfället används det som primärt certifikat. När klustret har skapats kan du lägga till ett nytt certifikat som sekundärt.

> [!NOTE]
> För ett säkert kluster behöver du alltid minst ett giltigt kluster certifikat (inte återkallat och inte förfallet) (primär eller sekundär) distribuerat (om inte klustret slutar fungera). 90 dagar innan alla giltiga certifikat når förfallo datum, genererar systemet en varnings spårning och en varnings hälso händelse på noden. Dessa är för närvarande de enda aviseringarna Service Fabric skickas angående förfallo datum för certifikat.
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Lägga till ett sekundärt kluster certifikat med hjälp av portalen
Det går inte att lägga till ett sekundärt kluster certifikat via Azure Portal; Använd Azure PowerShell. Processen beskrivs senare i det här dokumentet.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Ta bort ett kluster certifikat med hjälp av portalen
För ett säkert kluster behöver du alltid minst ett giltigt certifikat (inte återkallat och inte förfallet). Det certifikat som distribueras med det sista datumet för förfallo datumet används och om du tar bort det kommer klustret att sluta fungera. Se till att endast ta bort certifikatet som har upphört att gälla, eller ett oanvänt certifikat som upphör att gälla närmast.

Om du vill ta bort ett oanvänt kluster säkerhets certifikat går du till avsnittet säkerhet och väljer alternativet ta bort från snabb menyn i det oanvända certifikatet.

Om avsikten är att ta bort det certifikat som är markerat som primärt, måste du distribuera ett sekundärt certifikat med ett utgångs datum längre fram i framtiden än det primära certifikatet, vilket aktiverar beteendet för automatisk förnyelse. ta bort det primära certifikatet efter att den automatiska förnyelsen har slutförts.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Lägg till ett sekundärt certifikat med Azure Resource Manager

De här stegen förutsätter att du är bekant med hur Resource Manager fungerar och har distribuerat minst ett Service Fabric kluster med hjälp av en Resource Manager-mall och att du har den mall som du använde för att konfigurera klustret är praktiskt. Det förutsätts också att du är van att använda JSON.

> [!NOTE]
> Om du letar efter en exempel-mall och parametrar som du kan använda för att följa eller som en start punkt, laddar du ned den från den här [git-lagrings platsen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Redigera din Resource Manager-mall

För att under lätta för följande, kan exempel 5-VM-1-NodeTypes-Secure_Step2.JSpå innehåller alla ändringar som vi ska göra. exemplet finns tillgängligt på [git-lagrings platsen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Se till att följa alla steg**

1. Öppna den Resource Manager-mall som du använde för att distribuera klustret. (Om du har hämtat exemplet från föregående lagrings platsen använder du 5-VM-1-NodeTypes-Secure_Step1.JSpå för att distribuera ett säkert kluster och sedan öppna mallen).

2. Lägg till **två nya parametrar** "secCertificateThumbprint" och "secCertificateUrlValue" av typen "String" i avsnittet parameter i mallen. Du kan kopiera följande kodfragment och lägga till det i mallen. Beroende på din malls källa kanske du redan har definierat dessa, om du vill gå vidare till nästa steg. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Gör ändringar i resursen **Microsoft. ServiceFabric/Clusters** -leta upp resurs definitionen "Microsoft. ServiceFabric/Clusters" i mallen. Under egenskaperna för den definitionen hittar du "Certificate"-JSON-tagg, som bör se ut ungefär som följande JSON-kodfragment:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Lägg till en ny tagg "thumbprintSecondary" och ge den värdet "[parameters (' secCertificateThumbprint ')]".  

    Nu bör resurs definitionen se ut så här (beroende på din malls källa, men det kanske inte är exakt likadant som kodfragmentet nedan). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Om du vill **rulla över certifikatet** anger du det nya certifikatet som primärt och flyttar den aktuella primära som sekundär. Detta leder till att det aktuella primära certifikatet överförs till det nya certifikatet i ett distributions steg.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Gör ändringar i **alla** resurs definitioner för **Microsoft. Compute/VirtualMachineScaleSets** – leta upp resurs definitionen Microsoft. Compute/virtualMachineScaleSets. Bläddra till "utgivare": "Microsoft. Azure. ServiceFabric" under "virtualMachineProfile".

    I inställningarna för Service Fabric Publisher bör du se något som liknar detta.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Lägg till de nya certifikat posterna
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Egenskaperna bör nu se ut så här
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Om du vill **rulla över certifikatet** anger du det nya certifikatet som primärt och flyttar den aktuella primära som sekundär. Detta resulterar i förnyelsen av det aktuella certifikatet till det nya certifikatet i ett distributions steg.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Egenskaperna bör nu se ut så här    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Gör ändringar i **alla** resurs definitioner för **Microsoft. Compute/VirtualMachineScaleSets** – leta upp resurs definitionen Microsoft. Compute/virtualMachineScaleSets. Rulla till "vaultCertificates":, under "OSProfile". Det bör se ut ungefär så här.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Lägg till secCertificateUrlValue i den. Använd följande kodfragment:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Nu bör den resulterande JSON se ut ungefär så här.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Kontrol lera att du har upprepat steg 4 och 5 för alla resurs definitioner Nodetypes/Microsoft. Compute/virtualMachineScaleSets i mallen. Om du saknar någon av dem kommer certifikatet inte att installeras på den virtuella datorns skalnings uppsättning och du får oförutsägbara resultat i klustret, inklusive klustret som går ned (om du har ett giltigt certifikat som klustret kan använda för säkerhet. Dubbel kontroll, innan du fortsätter.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Redigera mallfilen så att de visar de nya parametrarna som du lade till ovan
Om du använder exemplet från [git-lagrings platsen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) för att följa med, kan du börja göra ändringar i EXEMPLET 5-VM-1-NodeTypes-Secure.parameters_Step2.JSpå 

Redigera din parameter fil för Resource Manager-mallen, Lägg till de två nya parametrarna för secCertificateThumbprint och secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

- Nu är du redo att distribuera din mall till Azure. Öppna en Azure PS version 1 + kommando tolk.
- Logga in på ditt Azure-konto och välj den aktuella Azure-prenumerationen. Det här är ett viktigt steg för folk som har åtkomst till fler än en Azure-prenumeration.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Testa mallen innan du distribuerar den. Använd samma resurs grupp som ditt kluster för närvarande är distribuerat till.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Distribuera mallen till resurs gruppen. Använd samma resurs grupp som ditt kluster för närvarande är distribuerat till. Kör kommandot New-AzResourceGroupDeployment. Du behöver inte ange läget eftersom standardvärdet är ett **stegvist** värde.

> [!NOTE]
> Om du anger att läget ska slutföras, kan du oavsiktligt ta bort resurser som inte finns i mallen. Använd den inte i det här scenariot.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Här är ett ifyllt exempel på samma PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

När distributionen är klar ansluter du till klustret med det nya certifikatet och utför några frågor. Om du kan göra det. Sedan kan du ta bort det gamla certifikatet. 

Om du använder ett självsignerat certifikat ska du inte glömma att importera dem till det lokala certifikat arkivet i TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
För snabb referens här är kommandot för att ansluta till ett säkert kluster 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
För snabb referens här är kommandot för att hämta kluster hälsa

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Distribuera klient certifikat till klustret.

Du kan använda samma steg som beskrivs i föregående steg 5 om du vill att certifikaten ska distribueras från ett nyckel valv till noderna. Du behöver bara definiera och använda olika parametrar.


## <a name="adding-or-removing-client-certificates"></a>Lägga till eller ta bort klient certifikat

Förutom kluster certifikat kan du lägga till klient certifikat för att utföra hanterings åtgärder på ett Service Fabric kluster.

Du kan lägga till två typer av klient certifikat – admin eller skrivskyddad. Dessa kan sedan användas för att styra åtkomsten till de administrativa åtgärderna och frågans åtgärder i klustret. Som standard läggs kluster certifikaten till i listan över tillåtna administratörs certifikat.

Du kan ange valfritt antal klient certifikat. Varje tillägg/borttagning-resultat i en konfigurations uppdatering av Service Fabric klustret.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Lägga till klient certifikat – admin eller Read-Only via portalen

1. Gå till avsnittet säkerhet och välj knappen "+ autentisering" överst i avsnittet säkerhet.
2. I avsnittet Lägg till autentisering väljer du "autentiseringstyp"-' skrivskyddad klient ' eller ' admin-klient '
3. Välj en autentiseringsmetod. Detta visar Service Fabric om det ska söka efter certifikatet med hjälp av ämnes namnet eller tumavtrycket. I allmänhet är det inte en bra säkerhets rutin att använda autentiseringsmetoden för ämnes namn. 

![Lägg till klient certifikat][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Borttagning av klient certifikat – admin eller Read-Only med portalen

Om du vill ta bort ett sekundärt certifikat från att användas för kluster säkerhet navigerar du till avsnittet säkerhet och väljer alternativet ta bort på snabb menyn för det aktuella certifikatet.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Lägga till program certifikat till en skalnings uppsättning för virtuella datorer

Information om hur du distribuerar ett certifikat som du använder för dina program till klustret finns i [det här exemplet på PowerShell-skript](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Nästa steg
Läs de här artiklarna om du vill ha mer information om kluster hantering:

* [Service Fabric kluster uppgraderings process och förväntningar från dig](service-fabric-cluster-upgrade.md)
* [Konfigurera rollbaserad åtkomst för klienter](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


