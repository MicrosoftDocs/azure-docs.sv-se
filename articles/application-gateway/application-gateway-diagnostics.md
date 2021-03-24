---
title: Backend-hälsa och diagnostiska loggar
titleSuffix: Azure Application Gateway
description: Lär dig hur du aktiverar och hanterar åtkomst loggar och prestanda loggar för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: 32d96ce79844cd89e06035036bfa68703a738ed1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950492"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Server dels hälsa och diagnostikloggar för Application Gateway

Du kan övervaka Azure Application Gateway-resurser på följande sätt:

* [Backend-hälsa](#back-end-health): Application Gateway ger möjlighet att övervaka servrarnas hälso tillstånd i backend-poolerna via Azure Portal och via PowerShell. Du kan också hitta hälso tillståndet för backend-poolerna via diagnostikloggar för prestanda.

* [Loggar](#diagnostic-logging): loggar gör det möjligt för prestanda, åtkomst och andra data att sparas eller förbrukas från en resurs i övervaknings syfte.

* [Mått](application-gateway-metrics.md): Application Gateway har flera mått som hjälper dig att kontrol lera att systemet fungerar som förväntat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Server dels hälsa

Application Gateway ger möjlighet att övervaka hälsan för enskilda medlemmar i backend-pooler via portalen, PowerShell och kommando rads gränssnittet (CLI). Du kan också hitta en samlad hälso översikt över backend-pooler med hjälp av diagnostikloggar för prestanda.

Backend-hälsorapporten visar utdata från den Application Gateway hälso avsökningen till backend-instanserna. När avsökningen lyckas och Server delen kan ta emot trafik, betraktas den som felfri. Annars betraktas den som ohälsosam.

> [!IMPORTANT]
> Om det finns en nätverks säkerhets grupp (NSG) i ett Application Gateway undernät öppnar du Port intervall 65503-65534 för v1-SKU: er och 65200-65535 för v2 SKU: er i Application Gateway-undernätet för inkommande trafik. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunder av dessa gateways, inte att kunna initiera några ändringar på dessa slut punkter.


### <a name="view-back-end-health-through-the-portal"></a>Visa Server dels hälsa via portalen

I portalen tillhandahålls Server dels hälsa automatiskt. I en befintlig Application Gateway väljer du **övervakning** av  >  **Server dels hälsa**.

Varje medlem i backend-poolen visas på den här sidan (oavsett om det är ett nätverkskort, en IP-adress eller ett fullständigt domän namn). Namn på backend-pool, port, backend-HTTP-inställningar och hälso status visas. Giltiga värden för hälso status är **felfria**, **felaktiga** och **okända**.

> [!NOTE]
> Om du ser en backend-hälsostatus som är **okänd** kontrollerar du att åtkomsten till Server delen inte blockeras av en NSG-regel, en användardefinierad väg (UDR) eller en anpassad DNS i det virtuella nätverket.

![Server dels hälsa][10]

### <a name="view-back-end-health-through-powershell"></a>Visa backend-hälsa via PowerShell

Följande PowerShell-kod visar hur du visar backend-hälsa med hjälp av `Get-AzApplicationGatewayBackendHealth` cmdleten:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Visa backend-hälsa via Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultat

Följande fragment visar ett exempel på svaret:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostikloggar

Du kan använda olika typer av loggar i Azure för att hantera och felsöka programgatewayer. Du kan komma åt vissa av de här loggarna via portalen. Alla loggar kan extraheras från Azure Blob Storage och visas i olika verktyg, till exempel [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), Excel och Power BI. Du kan lära dig mer om de olika typerna av loggar i följande lista:

* **Aktivitets logg**: du kan använda [Azure aktivitets loggar](../azure-resource-manager/management/view-activity-logs.md) (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickas till din Azure-prenumeration och deras status. Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.
* **Åtkomst logg**: du kan använda den här loggen för att Visa Application Gateway åtkomst mönster och analysera viktig information. Detta inkluderar anroparens IP, begärd URL, svars svars tid, retur kod och byte in och ut. En åtkomst logg samlas in var 60: e sekund. Den här loggen innehåller en post per instans av Application Gateway. Application Gateway-instansen identifieras av egenskapen instanceId.
* **Prestanda logg**: du kan använda den här loggen för att se hur Application Gateway instanser presterar. Den här loggen samlar in prestanda information för varje instans, inklusive totalt antal betjänade begär Anden, data flöde i byte, totalt antal begär Anden, misslyckade begär Anden, antal misslyckade och felfria backend-instanser. En prestanda logg samlas in var 60: e sekund. Prestanda loggen är endast tillgänglig för v1 SKU. Använd [mått](application-gateway-metrics.md) för prestanda data för v2-SKU: n.
* **Brand Väggs logg**: du kan använda den här loggen för att visa de begär Anden som loggas via antingen identifierings-eller skydds läget för en Programgateway som är konfigurerad med brand väggen för webbaserade program. Brand Väggs loggar samlas in var 60: e sekund. 

> [!NOTE]
> Loggar är endast tillgängliga för resurser som distribueras i Azure Resource Manager distributions modell. Du kan inte använda loggar för resurser i den klassiska distributions modellen. En bättre förståelse för de två modellerna finns i artikeln [förstå distribution av Resource Manager och klassisk distribution](../azure-resource-manager/management/deployment-models.md) .

Du har tre alternativ för att lagra dina loggar:

* **Storage-konto**: Storage-konton passar bäst när loggarna ska lagras en längre tid och granskas vid behov.
* **Event Hub**: Event Hub är ett bra alternativ för att integrera med andra verktyg för säkerhets informations-och händelse hantering (Siem) för att få aviseringar för dina resurser.
* **Azure Monitor loggar**: Azure Monitor loggar används bäst för allmän övervakning i real tid av ditt program eller tittar på trender.

### <a name="enable-logging-through-powershell"></a>Aktivera loggning via PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Du måste aktivera åtkomst-och prestanda loggning för att kunna börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet är av formatet:/Subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> . Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för lagrings konto](./media/application-gateway-diagnostics/diagnostics1.png)

2. Anteckna det resurs-ID för Application Gateway som loggning har Aktiver ATS för. Det här värdet är av formatet:/Subscriptions/ \<subscriptionId\> /ResourceGroups/ \<resource group name\> /providers/Microsoft.Network/applicationGateways/ \<application gateway name\> . Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Aktivitets loggar kräver inget separat lagrings konto. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

### <a name="enable-logging-through-the-azure-portal"></a>aktivera loggning via Azure Portal

1. Leta upp resursen och välj **diagnostikinställningar** i Azure Portal.

   För Application Gateway är tre loggar tillgängliga:

   * Åtkomst logg
   * Prestanda logg
   * Brand Väggs logg

2. Om du vill börja samla in data väljer du **Aktivera diagnostik**.

   ![Aktivera diagnostik][1]

3. På sidan **Diagnostikinställningar** kan du göra inställningar för diagnostikloggarna. I det här exemplet lagrar Log Analytics loggarna. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Starta konfigurationsprocessen][2]

5. Ange ett namn för inställningarna, bekräfta inställningarna och välj **Spara**.

### <a name="activity-log"></a>Aktivitetslogg

Azure genererar aktivitets loggen som standard. Loggarna bevaras för 90 dagar i Azure Event logs-butiken. Läs mer om dessa loggar genom att läsa artikeln [Visa händelser och aktivitets logg](../azure-resource-manager/management/view-activity-logs.md) .

### <a name="access-log"></a>Åtkomst logg

Åtkomst loggen skapas endast om du har aktiverat den på varje Application Gateway instans, enligt beskrivningen i föregående steg. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Varje åtkomst till Application Gateway loggas i JSON-format som visas nedan. 

#### <a name="for-application-gateway-standard-and-waf-sku-v1"></a>För Application Gateway standard-och WAF SKU (v1)

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som hanterade begäran.        |
|clientIP     | Ursprunglig IP för begäran.        |
|clientPort     | Ursprunglig port för begäran.       |
|httpMethod     | HTTP-metod som används av begäran.       |
|requestUri     | URI för den mottagna begäran.        |
|RequestQuery     | **Server-routad**: backend-instansen som skickade begäran.</br>**X-AzureApplicationGateway-log-ID**: KORRELATIONS-ID som används för begäran. Den kan användas för att felsöka trafik problem på backend-servrarna. </br>**Server status**: http-svarskod som Application Gateway emot från Server delen.       |
|UserAgent     | Användar agent från rubriken HTTP-begäran.        |
|httpStatus     | HTTP-statuskod som returnerades till klienten från Application Gateway.       |
|httpVersion     | HTTP-version för begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentBytes| Storlek på paket som skickas, i byte.|
|timeTaken| Tids längd (i millisekunder) som det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första byten i en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att fältet Time-Taken vanligt vis innehåller den tidpunkt då begäran och svars paket överförs över nätverket. |
|sslEnabled| Huruvida kommunikation med backend-pooler använder TLS/SSL. Giltiga värden är på och av.|
|värd| Det värdnamn som begäran har skickats till backend-servern. Om värd namnet för Server delen åsidosätts, kommer det här namnet att återspegla detta.|
|originalHost| Det värdnamn som begäran togs emot av Application Gateway från klienten.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
#### <a name="for-application-gateway-and-waf-v2-sku"></a>För Application Gateway-och WAF v2-SKU: er

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som hanterade begäran.        |
|clientIP     | Ursprunglig IP för begäran.        |
|httpMethod     | HTTP-metod som används av begäran.       |
|requestUri     | URI för den mottagna begäran.        |
|UserAgent     | Användar agent från rubriken HTTP-begäran.        |
|httpStatus     | HTTP-statuskod som returnerades till klienten från Application Gateway.       |
|httpVersion     | HTTP-version för begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentBytes| Storlek på paket som skickas, i byte.|
|timeTaken| Hur lång tid (i **sekunder**) det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första byten i en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att fältet Time-Taken vanligt vis innehåller den tidpunkt då begäran och svars paket överförs över nätverket. |
|sslEnabled| Om kommunikationen till backend-poolerna använder TLS. Giltiga värden är på och av.|
|sslCipher| Chiffrering som används för TLS-kommunikation (om TLS är aktiverat).|
|sslProtocol| SSL/TLS-protokoll som används (om TLS är aktiverat).|
|serverRouted| Backend-servern som Application Gateway dirigerar begäran till.|
|serverStatus| HTTP-statuskod för backend-servern.|
|serverResponseLatency| Svars tid för svaret från backend-servern.|
|värd| Adress som anges i värd rubriken för begäran. Om det här fältet har skrivits om med hjälp av omskrivning av huvuden, innehåller det uppdaterade värd namnet|
|originalRequestUriWithArgs| Det här fältet innehåller den ursprungliga URL: en för begäran |
|requestUri| Det här fältet innehåller URL: en efter Skriv åtgärden på Application Gateway |
|originalHost| Det här fältet innehåller det ursprungliga värd namnet för begäran
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Prestanda logg

Prestanda loggen skapas endast om du har aktiverat den på varje Application Gateway instans, enligt beskrivningen i föregående steg. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Prestanda logg data genereras i intervall på 1 minut. Den är endast tillgänglig för v1 SKU. Använd [mått](application-gateway-metrics.md) för prestanda data för v2-SKU: n. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     |  Application Gateway instans som prestanda data ska skapas för. För en Application Gateway med flera instanser finns det en rad per instans.        |
|healthyHostCount     | Antal felfria värdar i backend-poolen.        |
|unHealthyHostCount     | Antalet värdar som inte är felfria i backend-poolen.        |
|requestCount     | Antal begär Anden som hanteras.        |
|svarstid | Genomsnittlig svars tid (i millisekunder) för begär Anden från instansen till Server delen som hanterar begär Anden. |
|failedRequestCount| Antal misslyckade förfrågningar.|
|dataflöde| Genomsnittligt data flöde sedan den senaste loggen mätt i byte per sekund.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Svars tiden beräknas från den tidpunkt då den första byten i HTTP-begäran tas emot till den tidpunkt då den sista byten av HTTP-svaret skickas. Det är summan av Application Gateway bearbetnings tiden plus nätverks kostnaden till Server delen, plus den tid som server delen tar för att bearbeta begäran.

### <a name="firewall-log"></a>Brand Väggs logg

Brand Väggs loggen skapas endast om du har aktiverat den för varje Application Gateway, enligt beskrivningen i föregående steg. Den här loggen kräver också att brand väggen för webb program är konfigurerad på en Programgateway. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som brand Väggs data ska skapas för. För en Application Gateway med flera instanser finns det en rad per instans.         |
|clientIp     |   Ursprunglig IP för begäran.      |
|clientPort     |  Ursprunglig port för begäran.       |
|requestUri     | URL för den mottagna begäran.       |
|ruleSetType     | Regel uppsättnings typ. Det tillgängliga värdet är OWASP.        |
|ruleSetVersion     | Regel uppsättnings version används. Tillgängliga värden är 2.2.9 och 3,0.     |
|ruleId     | Regel-ID för Utlös ande händelse.        |
|meddelande     | Användarvänligt meddelande för händelsen som utlöses. Mer information finns i avsnittet information.        |
|åtgärd     |  Åtgärd som vidtas på begäran. Tillgängliga värden blockeras och tillåts (för anpassade regler), matchas (när en regel matchar en del av begäran) och identifieras och blockeras (båda båda för obligatoriska regler, beroende på om WAF är i identifierings-eller skydds läge).      |
|webbplats     | Platsen som loggen skapades för. För närvarande visas bara globala grupper eftersom reglerna är globala.|
|information     | Information om händelsen som utlöses.        |
|information. Message     | Beskrivning av regeln.        |
|information. data     | Vissa data som finns i en begäran som matchade regeln.         |
|information. fil     | Konfigurations fil som innehåller regeln.        |
|information. rad     | Rad nummer i konfigurations filen som utlöste händelsen.       |
|värdnamn   | Värdnamn eller IP-adress för Application Gateway.    |
|transactionId  | Unikt ID för en specifik transaktion som hjälper till att gruppera flera regel överträdelser som inträffade inom samma begäran.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100",
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure-CLI:t, Azure REST-API:t eller Azure Portal. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med hjälp av [appar för Power BI mallar](/power-bi/service-template-apps-overview)kan du analysera dina data.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visa och analysera loggar för åtkomst, prestanda och brand vägg

[Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md) kan samla in räknar-och händelse logg filen från ditt Blob Storage-konto. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysera åtkomst loggar via GoAccess

Vi har publicerat en Resource Manager-mall som installerar och kör den populära [GoAccess](https://goaccess.io/) log analyzer för Application Gateway åtkomst loggar. GoAccess tillhandahåller värdefull statistik för HTTP-trafik, till exempel unika besökare, begärda filer, värdar, operativ system, webbläsare, HTTP-statuskod med mera. Mer information finns i [Readme-filen i mappen Resource Manager-mall i GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknare och händelse loggar med hjälp av [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure aktivitets logg med Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) blogg inlägg.
* [Visa och analysera Azure aktivitets loggar i Power BI och fler](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogg inlägg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
