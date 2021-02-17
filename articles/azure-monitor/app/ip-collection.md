---
title: Azure Application Insights IP-webbplatssamling | Microsoft Docs
description: Förstå hur IP-adresser och geolokalisering hanteras med Azure Application Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584072"
---
# <a name="geolocation-and-ip-address-handling"></a>Hantering av geolokalisering och IP-adresser

Den här artikeln förklarar hur geolokalisering och hantering av IP-adresser fungerar i Application Insights tillsammans med hur du ändrar standard beteendet.

## <a name="default-behavior"></a>Standardbeteende

Som standard samlas IP-adresser in tillfälligt, men lagras inte i Application Insights. Den grundläggande processen ser ut så här:

När telemetri skickas till Azure, används IP-adressen för att göra en geolokalisering på plats med [GeoLite2 från MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Resultatet av sökningen används för att fylla i fälten `client_City` , `client_StateOrProvince` och `client_CountryOrRegion` . Adressen ignoreras sedan och `0.0.0.0` skrivs till `client_IP` fältet.

* Webb läsar telemetri: vi samlar tillfälligt in avsändarens IP-adress. IP-adressen beräknas av inmatnings slut punkten.
* Server telemetri: modulen Application Insights telemetri samlar tillfälligt in klientens IP-adress. IP-adressen samlas inte in lokalt när `X-Forwarded-For` rubriken har angetts.

Det här beteendet är avsiktligt för att undvika onödig insamling av personliga data. När det är möjligt rekommenderar vi att du undviker insamling av person uppgifter. 

## <a name="overriding-default-behavior"></a>Åsidosätter standard beteende

Standardvärdet är att inte samla in IP-adresser. Vi erbjuder fortfarande flexibiliteten att åsidosätta det här beteendet. Vi rekommenderar dock att du verifierar att samlingen inte bryter mot krav på efterlevnad eller lokala föreskrifter. 

Mer information om personlig data hantering i Application Insights finns i [rikt linjerna för person uppgifter](../logs/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Lagra IP-Datadata

För att aktivera IP-insamling och lagring `DisableIpMasking` måste egenskapen för Application Insights-komponenten anges till `true` . Den här egenskapen kan anges via Azure Resource Manager mallar eller genom att anropa REST API. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portalen 

Om du bara behöver ändra beteendet för en enskild Application Insights resurs använder du Azure Portal. 

1. Gå till mallen Application Insights resurs > **Automation**-  >  **export** 

2. Välj **distribuera**

    ![Knapp med ordet "Deploy" markerat i rött](media/ip-collection/deploy.png)

3. Välj **Redigera mall**.

    ![Knapp med ordet "redigera" markerat i rött](media/ip-collection/edit-template.png)

4. Gör följande ändringar i JSON för resursen och välj sedan **Spara**:

    ![Skärm bilden lägger till ett kommatecken efter "IbizaAIExtension" och lägger till en ny rad nedan med "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Om det uppstår ett fel som säger att **_resurs gruppen finns på en plats som inte stöds av en eller flera resurser i mallen. Välj en annan resurs grupp._** Välj tillfälligt en annan resurs grupp i list rutan och välj sedan den ursprungliga resurs gruppen igen för att lösa problemet.

5. Välj **Jag accepterar**  >  **köp**. 

    ![Markerad ruta med ord "Jag accepterar de villkor som anges ovan" markerade i rött ovanför knappen med ordet "köp" markerat i rött.](media/ip-collection/purchase.png)

    I det här fallet köper du faktiskt inget nytt. Vi uppdaterar bara konfigurationen av den befintliga Application Insightss resursen.

6. När distributionen är klar kommer nya telemetridata att registreras.

    Om du väljer och redigerar mallen igen visas bara standard mal len utan den nyligen tillagda egenskapen. Om du inte ser IP-Datadata och vill bekräfta att `"DisableIpMasking": true` har angetts kör du följande PowerShell: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    En lista med egenskaper kommer att returneras som ett resultat. En av egenskaperna ska läsas `DisableIpMasking: true` . Om du kör PowerShell innan du distribuerar den nya egenskapen med Azure Resource Manager, finns inte egenskapen.

### <a name="rest-api"></a>Rest-API

[REST API](/rest/api/azure/) -nyttolasten för att göra samma ändringar är följande:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetri-initierare

Om du behöver ett mer flexibelt alternativ än `DisableIpMasking` kan du använda en [telemetri-initierare](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) för att kopiera hela eller delar IP-adressen till ett anpassat fält. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Om du inte kan komma åt `ISupportProperties` kontrollerar du och kontrollerar att du kör den senaste stabila versionen av Application Insights SDK. `ISupportProperties` är avsedda för höga kardinal värden, och `GlobalProperties` är mer lämpliga för låga kardinal-värden som region namn, miljö namn osv. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Aktivera telemetri-initierare för ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Aktivera telemetri-initierare för ASP.NET Core

Du kan skapa en telemetri-initierare på samma sätt för ASP.NET Core som ASP.NET, men om du vill aktivera initieraren använder du följande exempel som referens:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript för klientsidan](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript för klientsidan

Till skillnad från SDK: er för Server sidan beräknar inte Java Script SDK på klient sidan IP-adress. Som standard utförs beräkning av IP-adresser för telemetri på klient sidan i inmatnings slut punkten i Azure. 

Om du vill beräkna IP-adressen direkt på klient sidan måste du lägga till din egen anpassade logik och använda resultatet för att ange `ai.location.ip` taggen. När `ai.location.ip` har angetts utförs inte beräkning av IP-adress av inmatnings slut punkten och den angivna IP-adressen används för sökning på geolokalisering. I det här scenariot är IP-adressen fortfarande nollställd som standard. 

Om du vill hålla hela IP-adressen beräknad från din anpassade logik kan du använda en telemetri-initierare som kopierar de IP-Datadata som du angav i `ai.location.ip` till ett separat anpassat fält. Men återigen till skillnad från SDK: er på Server sidan, utan att behöva lita på bibliotek från tredje part eller din egen samlings logik, beräknar SDK för klient sidan inte adressen åt dig.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Om data på klient sidan passerar en proxy innan vidarebefordran till inmatnings slut punkten kan IP-adress beräkningen Visa IP-adressen för proxyservern och inte klienten. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visa resultatet av telemetri-initieraren

Om du skickar ny trafik till platsen och väntar några minuter. Du kan sedan köra en fråga för att bekräfta att samlingen fungerar:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nyligen insamlade IP-adresser visas i `customDimensions_client-ip` kolumnen. Standard `client-ip` kolumnen kommer fortfarande att ha alla fyra oktetter, antingen noll. 

Om du testar från localhost och värdet för `customDimensions_client-ip` är `::1` , förväntas det här värdet. `::1` representerar loopback-adressen i IPv6. Den motsvarar `127.0.0.1` i IPv4.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [insamling av personliga data](../logs/personal-data-mgmt.md) i Application Insights.

* Lär dig mer om hur [IP-webbplatssamling](https://apmtips.com/posts/2016-07-05-client-ip-address/) i Application Insights fungerar. (Den här artikeln är ett äldre externt blogg inlägg som skrivits av någon av våra tekniker. Detta fördaterar det aktuella standard beteendet där IP-adressen registreras som `0.0.0.0` , men den ökar djupet på Mechanics för det inbyggda `ClientIpHeaderTelemetryInitializer` .)
