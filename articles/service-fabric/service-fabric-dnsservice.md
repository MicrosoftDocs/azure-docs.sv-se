---
title: Azure Service Fabric DNS-tjänsten
description: Använd Service Fabricens DNS-tjänst för att identifiera mikrotjänster inifrån klustret.
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91268051"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS-tjänsten i Azure Service Fabric
DNS-tjänsten är en valfri system tjänst som du kan aktivera i klustret för att identifiera andra tjänster som använder DNS-protokollet. 

Många tjänster, särskilt behållar tjänster, kan adresseras via en redan befintlig URL. Det är önskvärt att kunna matcha tjänsterna med hjälp av standard-DNS-protokollet i stället för Service Fabric Naming Service-protokollet. Med DNS-tjänsten kan du mappa DNS-namn till ett tjänst namn och därmed matcha slut punktens IP-adresser. Sådana funktioner upprätthåller portabiliteten i behållar tjänster på olika plattformar och kan göra "lyft och Shift"-scenarier enklare genom att låta dig använda befintliga tjänst webb adresser i stället för att behöva skriva om kod för att utnyttja Naming Service. 

DNS-tjänsten mappar DNS-namn till tjänst namn, som i sin tur löses av Naming Service för att returnera tjänst slut punkten. DNS-namnet för tjänsten tillhandahålls vid tidpunkten för skapandet. Följande diagram visar hur DNS-tjänsten fungerar för tillstånds lösa tjänster.

![Diagram över hur DNS-namn mappas till tjänst namn av DNS-tjänsten för tillstånds lösa tjänster.](./media/service-fabric-dnsservice/stateless-dns.png)

Från och med Service Fabric version 6,3 har Service Fabric DNS-protokollet utökats till att omfatta ett schema för att adressera partitionerade tillstånds känsliga tjänster. Dessa tillägg gör det möjligt att lösa vissa partitions-IP-adresser med en kombination av tillstånds känsliga DNS-namn och partitionens namn. Alla tre partitionerings scheman stöds:

- Namngiven partitionering
- Intervall partitionering
- Singleton-partitionering

Följande diagram visar hur DNS-tjänsten fungerar för partitionerade tillstånds känsliga tjänster.

![Diagram över hur DNS-namn mappas till tjänst namn av DNS-tjänsten för partitionerade tillstånds lösa tjänster.](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamiska portar stöds inte av DNS-tjänsten. Använd [tjänsten reverse proxy](./service-fabric-reverseproxy.md)för att lösa tjänster som exponeras på dynamiska portar.

## <a name="enabling-the-dns-service"></a>Aktivera DNS-tjänsten
> [!NOTE]
> Det finns ännu inte stöd för DNS-tjänsten för Service Fabric-tjänster i Linux.

När du skapar ett kluster med hjälp av portalen aktive ras DNS-tjänsten som standard i kryss rutan **Inkludera DNS-tjänst** på **kluster konfigurations** menyn:

![Aktivera DNS-tjänsten via portalen](./media/service-fabric-dnsservice/enable-dns-service.png)

Om du inte använder portalen för att skapa klustret eller om du uppdaterar ett befintligt kluster måste du aktivera DNS-tjänsten i en mall:

- Om du vill distribuera ett nytt kluster kan du antingen använda [exempel-mallarna](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) eller skapa en egen Resource Manager-mall. 
- Om du vill uppdatera ett befintligt kluster kan du gå till klustrets resurs grupp på portalen och klicka på **Automation-skript** för att arbeta med en mall som motsvarar klustrets aktuella tillstånd och andra resurser i gruppen. Mer information finns i [Exportera mallen från resurs gruppen](../azure-resource-manager/templates/export-template-portal.md).

När du har en mall kan du aktivera DNS-tjänsten med följande steg:

1. Kontrol lera att `apiversion` är inställt på `2017-07-01-preview` eller senare för `Microsoft.ServiceFabric/clusters` resursen och uppdatera den, om den inte är det, som visas i följande exempel:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu DNS-tjänsten på något av följande sätt:

   - Om du vill aktivera DNS-tjänsten med standardinställningar lägger du till den i `addonFeatures` avsnittet i `properties` avsnittet, som du ser i följande exempel:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Om du vill aktivera tjänsten med andra inställningar än standardinställningarna lägger du till ett `DnsService` avsnitt i `fabricSettings` avsnittet i `properties` avsnittet. I det här fallet behöver du inte lägga till DNS service i `addonFeatures` . Mer information om de egenskaper som kan anges för DNS-tjänsten finns i inställningar för [DNS-tjänsten](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. När du har uppdaterat kluster mal len med ändringarna tillämpar du dem och låter uppgraderingen slutföras. När uppgraderingen är klar börjar DNS-systemtjänsten att köras i klustret. Tjänst namnet är `fabric:/System/DnsService` , och du hittar det under avsnittet **system** service i Service Fabric Explorer. 

> [!NOTE]
> När du uppgraderar DNS från inaktive rad till aktive rad kanske Service Fabric Explorer inte visar det nya läget. Lös problemet genom att starta om noderna genom att ändra UpgradePolicy i din Azure Resource Manager-mall. Mer information finns i [referensen för Service Fabric mal len](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) .

> [!NOTE]
> Att aktivera DNS-tjänsten när du utvecklar på en lokal dator åsidosätter vissa DNS-inställningar. Om du får problem med att ansluta till Internet kontrollerar du dina DNS-inställningar.

## <a name="setting-the-dns-name-for-your-service"></a>Ange DNS-namnet för din tjänst
Du kan ange ett DNS-namn för dina tjänster antingen i deklarativ form för standard tjänster i ApplicationManifest.xml-filen eller via PowerShell-kommandon.

DNS-namnet för din tjänst kan matchas i hela klustret, så det är viktigt att säkerställa att DNS-namnet är unikt i klustret. 

Vi rekommenderar starkt att du använder ett namngivnings schema `<ServiceDnsName>.<AppInstanceName>` , till exempel `service1.application1` . Om ett program distribueras med hjälp av Docker Compos, tilldelas tjänster automatiskt DNS-namn med det här namngivnings schemat.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Ange DNS-namnet för en standard tjänst i ApplicationManifest.xml
Öppna projektet i Visual Studio eller din favorit redigerare och öppna ApplicationManifest.xml-filen. Gå till avsnittet standard tjänster och Lägg till attributet för varje tjänst `ServiceDnsName` . I följande exempel visas hur du anger tjänstens DNS-namn till `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
När programmet har distribuerats visar tjänst instansen i Service Fabric Explorer DNS-namnet för den här instansen, som visas i följande figur: 

![tjänst slut punkter](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

I följande exempel anges DNS-namnet för en tillstånds känslig tjänst till `statefulsvc.app` . Tjänsten använder ett namngivet partitionerings schema. Observera att partitionsnamnet är gemener. Detta är ett krav för partitioner som kommer att vara riktade till DNS-frågor. Mer information finns i [göra DNS-frågor på en tillstånds känslig tjänst partition](#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Ange DNS-namnet för en tjänst med hjälp av PowerShell
Du kan ange DNS-namnet för en tjänst när du skapar den med `New-ServiceFabricService` PowerShell-kommandot. I följande exempel skapas en ny tillstånds lös tjänst med DNS-namnet `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Förhandsgranskningsvyn Göra DNS-frågor på en tillstånds känslig tjänst partition
Från och med Service Fabric version 6,3 stöder DNS-tjänsten Service Fabric frågor för diskpartitioner.

För partitioner som ska användas i DNS-frågor gäller följande namngivnings begränsningar:

   - Partitionsnamn ska vara DNS-kompatibla.
   - Partition namn med flera etiketter (som innehåller punkt, ".", i namnet) ska inte användas.
   - Partitionsnamn ska vara gemener.

DNS-frågor som riktar sig mot en partition formateras enligt följande:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Plats:

- Den första delen av *partitioned-service-DNSName* är den första delen av TJÄNSTens DNS-namn.
- *PartitionPrefix* är ett värde som kan anges i avsnittet DNS service i kluster manifestet eller via klustrets Resource Manager-mall. Standardvärdet är "--". Mer information finns i  [Inställningar för DNS-tjänsten](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-partition-Name* är namnet på partitionen. 
- *PartitionSuffix* är ett värde som kan anges i avsnittet DNS service i kluster manifestet eller via klustrets Resource Manager-mall. Standardvärdet är en tom sträng. Mer information finns i  [Inställningar för DNS-tjänsten](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Återstående partitionerade service-DNSName* är den återstående delen av tjänstens DNS-namn.

I följande exempel visas DNS-frågor om partitionerade tjänster som körs på ett kluster med standardinställningar för `PartitionPrefix` och `PartitionSuffix` : 

- Använd om du vill matcha partition "0" för en tjänst med DNS-namn `backendrangedschemesvc.application` som använder ett intervall med partitionerat partitionerings schema `backendrangedschemesvc-0.application` .
- Använd för att matcha partition "First" av en tjänst med DNS-namn `backendnamedschemesvc.application` som använder ett namngivet partitionerings schema `backendnamedschemesvc-first.application` .

DNS-tjänsten returnerar IP-adressen för den primära repliken i partitionen. Om ingen partition anges returnerar tjänsten IP-adressen för den primära repliken av en slumpvis vald partition.

## <a name="using-dns-in-your-services"></a>Använda DNS i dina tjänster
Om du distribuerar fler än en tjänst kan du hitta slut punkterna för andra tjänster för att kommunicera med med hjälp av ett DNS-namn. DNS-tjänsten fungerar för tillstånds lösa tjänster och, i Service Fabric version 6,3 och senare, för tillstånds känsliga tjänster. För tillstånds känsliga tjänster som körs på versioner av Service Fabric före 6,3 kan du använda den inbyggda [tjänsten omvänd proxy](./service-fabric-reverseproxy.md) för HTTP-anrop för att anropa en viss tjänstmall. 

Dynamiska portar stöds inte av DNS-tjänsten. Du kan använda tjänsten reverse proxy för att lösa tjänster som använder dynamiska portar.

Följande kod visar hur du anropar en tillstånds lös tjänst via DNS. Det är bara ett reguljärt http-anrop där du anger DNS-namn, port och valfri sökväg som en del av URL: en.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

Följande kod visar ett anrop till en speciell partition för en tillstånds känslig tjänst. I det här fallet innehåller DNS-namnet partitionens namn (partition1). Anropet förutsätter ett kluster med standardvärden för `PartitionPrefix` och `PartitionSuffix` .

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Kända problem
* För Service Fabric version 6,3 och högre, finns det ett problem med DNS-sökningar för tjänst namn som innehåller ett bindestreck i DNS-namnet. Om du vill ha mer information om det här problemet kan du följa nedanstående [GitHub-problem](https://github.com/Azure/service-fabric-issues/issues/1197). En korrigering för detta kommer i nästa 6,3-uppdatering. 

* Det finns ännu inte stöd för DNS-tjänsten för Service Fabric-tjänster i Linux. DNS-tjänsten stöds för behållare i Linux. Manuell matchning med hjälp av Fabric client/ServicePartitionResolver är det tillgängliga alternativet.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om tjänst kommunikation i klustret med  [Anslut och kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md)
