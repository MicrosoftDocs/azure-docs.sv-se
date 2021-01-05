---
title: Arbeta med stora datamängder
description: Lär dig hur du hämtar, formaterar, visar och hoppar över poster i stora data mängder när du arbetar med Azures resurs diagram.
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 59f69738bf9fe25cb739539b7a1f93e4499d781a
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826024"
---
# <a name="working-with-large-azure-resource-data-sets"></a>Arbeta med stora Azure-resurs data uppsättningar

Azure Resource Graph är utformat för att arbeta med och få information om resurser i din Azure-miljö. Resurs diagram gör det enkelt att komma åt dessa data, även när du frågar efter tusentals poster. Resurs diagram har flera alternativ för att arbeta med dessa stora data mängder.

Vägledning om hur du arbetar med frågor med hög frekvens finns i [rikt linjer för begränsade begär Anden](./guidance-for-throttled-requests.md).

## <a name="data-set-result-size"></a>Resultat storlek för data uppsättning

Som standard begränsar resurs diagram alla frågor till att bara returnera **100** poster. Den här kontrollen skyddar både användaren och tjänsten från oavsiktliga frågor som leder till stora data mängder. Den här händelsen inträffar oftast när en kund experimenterar med frågor för att hitta och filtrera resurser på det sätt som passar deras specifika behov. Den här kontrollen skiljer sig från att använda [Top](/azure/kusto/query/topoperator) -eller [limit](/azure/kusto/query/limitoperator) -språkDatautforskarens språk operatörer för att begränsa resultaten.

> [!NOTE]
> När du använder **första gången** rekommenderar vi att du sorterar resultaten efter minst en kolumn med `asc` eller `desc` . Utan sortering är de resultat som returneras slumpmässiga och inte repeterbara.

Standard gränsen kan åsidosättas genom alla metoder för att interagera med resurs diagram. I följande exempel visas hur du ändrar storleks gränsen för data uppsättningen till _200_:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

I [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)är kontrollen **$Top** och ingår i **QueryRequestOptions**.

Den kontroll som är _mest restriktiv_ är att vinna. Om din fråga till exempel använder **Top** -eller **Limit** -operatorer och skulle resultera i fler poster än den **första**, skulle de maximala poster som returneras vara lika med **först**. På samma sätt, om **Top** eller **Limit** är mindre än den **första**, skulle den returnerade post mängden vara det mindre värdet som kon figurer ATS av **Top** eller **Limit**.

För **närvarande har** det högsta tillåtna värdet _5000_, som det uppnår genom [växlings resultat](#paging-results) _1000_ poster i taget.

> [!IMPORTANT]
> När det **första** är konfigurerat att vara större än _1000_ poster måste frågan **projicera** fältet **ID** för att sid brytningen ska fungera. Om det saknas i frågan, kommer svaret inte att [växlas](#paging-results) och resultaten är begränsade till _1000_ poster.

## <a name="skipping-records"></a>Hoppar över poster

Nästa alternativ för att arbeta med stora data mängder är **Skip** -kontrollen. Den här kontrollen tillåter att din fråga hoppar över eller hoppar över det definierade antalet poster innan resultatet returneras. **Skip** är användbart för frågor som sorterar resultat på ett meningsfullt sätt där avsikten är att hämta poster någonstans mitt i resultat uppsättningen. Om de resultat som behövs finns i slutet av den returnerade data uppsättningen, är det mer effektivt att använda en annan sorterings konfiguration och hämta resultaten från data uppsättningens överkant i stället.

> [!NOTE]
> När du använder **Skip** rekommenderar vi att du sorterar resultaten efter minst en kolumn med `asc` eller `desc` . Utan sortering är de resultat som returneras slumpmässiga och inte repeterbara. Om `limit` eller `take` används i frågan ignoreras **hoppa över** .

I följande exempel visas hur du hoppar över de första _10_ posterna som en fråga resulterar i, i stället för att starta den returnerade resultat uppsättningen med den elfte posten:

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

I [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)är kontrollen **$Skip** och ingår i **QueryRequestOptions**.

## <a name="paging-results"></a>Växla resultat

När det är nödvändigt att dela upp en resultat uppsättning i mindre mängder poster för bearbetning eller eftersom en resultat uppsättning skulle överskrida det högsta tillåtna värdet för _1000_ returnerade poster, använder du sid indelning. [REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources) 
 **QueryResponse** innehåller värden för att indikera en resultat uppsättning har delats upp: **resultTruncated** och **$skipToken**. **resultTruncated** är ett booleskt värde som informerar konsumenten om det finns ytterligare poster som inte returneras i svaret. Det här villkoret kan också identifieras när **Count** -egenskapen är mindre än egenskapen **totalRecords** . **totalRecords** definierar hur många poster som matchar frågan.

 **resultTruncated** är **Sant** när växlingen är inaktive rad eller inte möjlig eftersom ingen `id` kolumn eller om det finns mindre resurser som är tillgängliga än en fråga begär. När **resultTruncated** är **True** anges inte egenskapen **$skipToken** .

I följande exempel visas hur du **hoppar över** de första 3000 posterna och returnerar de **första** 1000 posterna när posterna hoppades över med Azure CLI och Azure PowerShell:

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> Frågan måste **projicera** fältet **ID** för att sid brytning ska fungera. Om det saknas i frågan, innehåller svaret inte **$skipToken**.

Ett exempel finns i [Nästa sida fråga](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#next-page-query) i REST API dokumenten.

## <a name="formatting-results"></a>Formatering av resultat

Resultatet av en resurs diagram fråga finns i två format, _tabell_ -och _ObjectArray_. Formatet konfigureras med parametern **resultFormat** som en del av alternativen för begäran. _Tabell_ formatet är standardvärdet för **resultFormat**.

Resultatet från Azure CLI finns som standard i JSON. Resultat i Azure PowerShell är som standard en **PSCustomObject** , men de kan snabbt konverteras till JSON med hjälp av `ConvertTo-Json` cmdleten. I andra SDK: er kan frågeresultaten konfigureras för att skriva ut _ObjectArray_ -formatet.

### <a name="format---table"></a>Format – tabell

Standardformat, _tabell_, returnerar resultat i ett JSON-format som är utformat för att markera kolumn design och rad värden för de egenskaper som returneras av frågan. Det här formatet liknar data som definierats i en strukturerad tabell eller ett kalkyl blad med kolumner som identifieras först och sedan varje rad som representerar data som är justerade för dessa kolumner.

Här är ett exempel på ett frågeresultat med _tabellformatering_ :

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>Format – ObjectArray

_ObjectArray_ -formatet returnerar också resultat i JSON-format. Den här designen motsvarar dock den nyckel/värde-par-relation som är gemensam i JSON där kolumnen och raddata matchas i mat ris grupper.

Här är ett exempel på ett frågeresultat med _ObjectArray_ formatering:

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

Här följer några exempel på hur du ställer in **resultFormat** för att använda _ObjectArray_ -formatet:

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig mer om hur du [utforskar resurser](explore-resources.md).
