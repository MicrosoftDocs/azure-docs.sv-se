---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742700"
---
## <a name="examples"></a>Exempel

### <a name="filter-on-system-properties"></a>Filtrera efter system egenskaper
Om du vill referera till en system egenskap i ett filter använder du följande format: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Filtrera efter meddelande egenskaper
Här följer några exempel på hur du använder meddelande egenskaper i ett filter. Du kan komma åt meddelande egenskaper med `user.property-name` eller bara `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Filtrera på meddelande egenskaper med specialtecken
Om meddelandets egenskaps namn har specialtecken använder du dubbla citat tecken ( `"` ) för att omge egenskaps namnet. Om egenskaps namnet till exempel är `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` använder du följande syntax i filtret. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Filtrera på meddelande egenskaper med numeriska värden
I följande exempel visas hur du kan använda egenskaper med numeriska värden i filter. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Parameterbaserade filter
Här följer några exempel på hur du använder parameterbaserade filter. I de här exemplen `DataTimeMp` är en meddelande egenskap av typen `DateTime` och `@dtParam` en parameter som skickas till filtret som ett `DateTime` objekt.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Använda i och inte i

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ett C#-exempel finns i [avsnittet Filtrera exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


### <a name="set-rule-action-for-a-sql-filter"></a>Ange regel åtgärd för ett SQL-filter

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Korrelations filter med hjälp av CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Den filtrerar meddelanden med `CorrelationID` inställningen till `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Korrelations filter med system-och användar egenskaper

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Det motsvarar: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

