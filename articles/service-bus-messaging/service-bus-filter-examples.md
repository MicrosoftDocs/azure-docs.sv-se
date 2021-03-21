---
title: Ange prenumerations filter i Azure Service Bus | Microsoft Docs
description: Den här artikeln innehåller exempel på hur du definierar filter och åtgärder på Azure Service Bus ämnes prenumerationer.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654577"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Ange prenumerations filter (Azure Service Bus)
Den här artikeln innehåller några exempel på hur du ställer in filter på Service Bus ämnes prenumerationer. Konceptuell information om filter finns i [filter](topic-filters.md).

## <a name="filter-on-system-properties"></a>Filtrera efter system egenskaper
Om du vill referera till en system egenskap i ett filter använder du följande format: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>Filtrera efter meddelande egenskaper
Här följer några exempel på hur du använder meddelande egenskaper i ett filter. Du kan komma åt meddelande egenskaper med `user.property-name` eller bara `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>Filtrera på meddelande egenskaper med specialtecken
Om meddelandets egenskaps namn har specialtecken använder du dubbla citat tecken ( `"` ) för att omge egenskaps namnet. Om egenskaps namnet till exempel är `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` använder du följande syntax i filtret. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>Filtrera på meddelande egenskaper med numeriska värden
I följande exempel visas hur du kan använda egenskaper med numeriska värden i filter. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Parameterbaserade filter
Här följer några exempel på hur du använder parameterbaserade filter. I de här exemplen `DataTimeMp` är en meddelande egenskap av typen `DateTime` och `@dtParam` en parameter som skickas till filtret som ett `DateTime` objekt.

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>Använda i och inte i

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Ett C#-exempel finns i [avsnittet Filtrera exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filter-using-correlationid"></a>Korrelations filter med hjälp av CorrelationID

```csharp
new CorrelationFilter("Contoso");
```

Den filtrerar meddelanden med `CorrelationID` inställningen till `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Korrelations filter med system-och användar egenskaper

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Det motsvarar: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Nästa steg
Se följande exempel: 

- [.NET – grundläggande självstudier för att skicka och ta emot med filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-ämnes filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-mall](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)