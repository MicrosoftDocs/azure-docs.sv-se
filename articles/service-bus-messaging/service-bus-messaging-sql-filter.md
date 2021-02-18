---
title: Azure Service Bus SQL filter-syntax för prenumerations regel | Microsoft Docs
description: Den här artikeln innehåller information om grammatik i SQL-filter. Ett SQL-filter stöder en delmängd av SQL-92-standarden.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 022f6cb1d698a10dc216db8d41c172691f7535ab
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652949"
---
# <a name="subscription-rule-sql-filter-syntax"></a>SQL filter-syntax för prenumerations regel

Ett *SQL-filter* är en av de tillgängliga filter typerna för Service Bus ämnes prenumerationer. Det är ett text uttryck som är Lean för en delmängd av SQL-92-standarden. Filter uttryck används med `sqlExpression` elementet i egenskapen "sqlFilter" för en Service Bus `Rule` i en [Azure Resource Manager mall](service-bus-resource-manager-namespace-topic-with-rule.md)eller Azure CLI- `az servicebus topic subscription rule create` kommandots [`--filter-sql-expression`](/cli/azure/servicebus/topic/subscription/rule#az_servicebus_topic_subscription_rule_create) argument och flera SDK-funktioner som tillåter hantering av prenumerations regler.

Service Bus Premium stöder även [syntaxen för SQL-JMS för SQL-meddelanden](https://docs.oracle.com/javaee/7/api/javax/jms/Message.html) via JMS 2,0-API: et.

  
``` 
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
```  
  
```  
<expression> ::=  
      <constant>   
      | <function>  
      | <property>  
      | <expression> { + | - | * | / | % } <expression>  
      | { + | - } <expression>  
      | ( <expression> )  
  
```  
  
```  
<property> :=   
       [<scope> .] <property_name>  
  
```  
  
## <a name="arguments"></a>Argument  
  
-   `<scope>` är en valfri sträng som anger omfånget för `<property_name>` . Giltiga värden är `sys` eller `user` . `sys`Värdet anger systemets omfattning där `<property_name>` är ett offentligt egenskaps namn för [klassen BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` anger användar omfång där `<property_name>` är en nyckel för [BrokeredMessage-klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ord listan. `user` omfånget är standard omfånget om det `<scope>` inte har angetts.  
  
## <a name="remarks"></a>Kommentarer

Ett försök att komma åt en icke-befintlig system egenskap är ett fel, medan ett försök att komma åt en icke-befintlig användar egenskap inte är ett fel. I stället utvärderas en icke-befintlig användar egenskap internt som ett okänt värde. Ett okänt värde behandlas särskilt under utvärderingen av operatorn.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argument  

 `<regular_identifier>` är en sträng som representeras av följande reguljära uttryck:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Den här grammatiken avser en sträng som börjar med en bokstav och som följs av en eller flera under streck/bokstav/siffra.  
  
`[:IsLetter:]` innebär valfritt Unicode-tecken som kategoriseras som en Unicode-bokstav. `System.Char.IsLetter(c)` Returnerar `true` IF `c` är en Unicode-bokstav.  
  
`[:IsDigit:]` innebär valfritt Unicode-tecken som kategoriseras som en decimal siffra. `System.Char.IsDigit(c)` Returnerar `true` IF `c` är en Unicode-siffra.  
  
En `<regular_identifier>` får inte vara ett reserverat nyckelord.  
  
`<delimited_identifier>` är en sträng som omges av vänster/höger hak paren tes ([]). En höger hak paren tes visas som två höger hak paren tes. Följande är exempel på `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` är en sträng som omges av dubbla citat tecken. Ett dubbelt citat tecken i identifierare representeras som två dubbla citat tecken. Det rekommenderas inte att använda citerade identifierare eftersom det enkelt kan förväxlas med en strängkonstant. Använd en avgränsad identifierare om möjligt. Här är ett exempel på `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>ofta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
`<pattern>` måste vara ett uttryck som utvärderas som en sträng. Den används som ett mönster för operatorn LIKE.      Den kan innehålla följande jokertecken:  
  
-   `%`: Valfri sträng med noll eller flera tecken.  
  
-   `_`: Alla enskilda bokstäver.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer  

`<escape_char>` måste vara ett uttryck som utvärderas som en sträng med längden 1. Den används som ett escape-tecken för operatorn LIKE.  
  
 Till exempel `property LIKE 'ABC\%' ESCAPE '\'` matchningar `ABC%` i stället för en sträng som börjar med `ABC` .  
  
## <a name="constant"></a>konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>` är en sträng med tal som inte står inom citat tecken och som inte innehåller några decimaler. Värdena lagras som `System.Int64` internt och följer samma intervall.  
  
     Här är exempel på långa konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` är en sträng med tal som inte omges av citat tecken och som innehåller ett decimal tecken. Värdena lagras som `System.Double` internt och följer samma omfång/precision.  
  
     I en framtida version kan det här numret lagras i en annan datatyp för att stödja exakta semantiska tal, så du bör inte förlita dig på det faktum att den underliggande data typen är `System.Double` för `<decimal_constant>` .  
  
     Följande är exempel på Decimal konstanter:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` är ett tal skrivet i matematisk notation. Värdena lagras som `System.Double` internt och följer samma omfång/precision. Följande är exempel på ungefärligt antal konstanter:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Kommentarer  

Booleska konstanter representeras av nyckelorden **True** eller **false**. Värdena lagras som `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Kommentarer  

Sträng konstanter omges av enkla citat tecken och innehåller alla giltiga Unicode-tecken. Ett enkelt citat tecken som är inbäddat i en strängkonstant representeras som två enkla citat tecken.  
  
## <a name="function"></a>-funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Kommentarer
  
`newid()`Funktionen returnerar en `System.Guid` som genereras av `System.Guid.NewGuid()` metoden.  
  
`property(name)`Funktionen returnerar värdet för den egenskap som refereras till av `name` . `name`Värdet kan vara ett giltigt uttryck som returnerar ett sträng värde.  
  
## <a name="considerations"></a>Överväganden
  
Tänk på följande [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) -semantik:  
  
-   Egenskaps namn är inte Skift läges känsliga.  
  
-   Operatorer följer C# implicit konvertering när det är möjligt.  
  
-   System egenskaper är offentliga egenskaper som visas i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -instanser.  
  
    Tänk på följande `IS [NOT] NULL` semantik:  
  
    -   `property IS NULL` utvärderas som `true` om antingen egenskapen inte finns eller om egenskapens värde är `null` .  
  
### <a name="property-evaluation-semantics"></a>Semantik för egenskaps utvärdering  
  
- Ett försök att utvärdera en icke-befintlig system egenskap genererar ett [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) -undantag.  
  
- En egenskap som inte finns utvärderas internt som **okänd**.  
  
  Okänd utvärdering i aritmetiska operatorer:  
  
- För binära operatorer, om antingen den vänstra eller högra sidan av operander utvärderas som **okänd**, är resultatet **Okänt**.  
  
- För unära operatorer är resultatet **Okänt** om en operand utvärderas som **okänd**.  
  
  Okänd utvärdering i binära jämförelse operatorer:  
  
- Om antingen den vänstra eller högra sidan av operander utvärderas som **okänd** är resultatet **Okänt**.  
  
  Okänd utvärdering i `[NOT] LIKE` :  
  
- Om en operand utvärderas som **okänd** är resultatet **Okänt**.  
  
  Okänd utvärdering i `[NOT] IN` :  
  
- Om den vänstra operanden utvärderas som **okänd** är resultatet **Okänt**.  
  
  Okänd utvärdering i **och** -operator:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Okänd utvärdering i **eller** -operator:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operator bindnings-semantik
  
-   Jämförelse operatorer som,,,, `>` `>=` `<` `<=` `!=` och `=` följer samma semantik som operatorn C# i data typs kampanjer och implicita konverteringar.  
  
-   Aritmetiska operatorer som,,, `+` `-` `*` `/` och `%` följer samma semantik som operatorn C# i data typs kampanjer och implicita konverteringar.

## <a name="examples"></a>Exempel
Exempel finns i [Service Bus filter exempel](service-bus-filter-examples.md).

## <a name="next-steps"></a>Nästa steg

- [SQLFilter-klass (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter-klass (.NET standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SqlFilter-klass (Java)](/java/api/com.microsoft.azure.servicebus.rules.SqlFilter)
- [SqlRuleFilter (Java Script)](/javascript/api/@azure/service-bus/sqlrulefilter)
- [`az servicebus topic subscription rule`](/cli/azure/servicebus/topic/subscription/rule)
- [New-AzServiceBusRule](/powershell/module/az.servicebus/new-azservicebusrule)