---
title: Azure Service Bus ämnes filter | Microsoft Docs
description: Den här artikeln förklarar hur prenumeranter kan definiera vilka meddelanden som ska tas emot från ett ämne genom att ange filter.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: f28b26ee112b47b9782823f6c79670dee9a3f082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651671"
---
# <a name="topic-filters-and-actions"></a>Ämnesfilter och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerationsregler. Varje regel består av ett **filter villkor** som väljer specifika meddelanden och som **kan** innehålla en **åtgärd** som kommenterar det valda meddelandet. 

Alla regler **utan åtgärder** kombineras med ett `OR` villkor och resulterar i ett **enda meddelande** i prenumerationen även om du har flera matchnings regler. 

Varje regel **med en åtgärd** genererar en kopia av meddelandet. Det här meddelandet har en egenskap `RuleName` som kallas där värdet är namnet på den matchande regeln. Åtgärden kan lägga till eller uppdatera egenskaper eller ta bort egenskaper från det ursprungliga meddelandet för att skapa ett meddelande i prenumerationen. 

Föreställ dig följande scenario:

- Prenumerationen har fem regler.
- Två regler innehåller åtgärder.
- Tre regler innehåller inte åtgärder.

Om du i det här exemplet skickar ett meddelande som matchar alla fem reglerna får du tre meddelanden i prenumerationen. Det är två meddelanden för två regler med åtgärder och ett meddelande för tre regler utan åtgärder. 

Varje nyligen skapad ämnes prenumeration har en första standard prenumerations regel. Om du inte uttryckligen anger ett filter villkor för regeln, är det filter som används det **sanna** filtret som gör att alla meddelanden kan väljas i prenumerationen. Standard regeln har ingen associerad antecknings åtgärd.

## <a name="filters"></a>Filter
Service Bus stöder tre filter villkor:

-   *SQL-filter* – en **SQLFILTER** innehåller ett SQL-liknande villkorligt uttryck som utvärderas i Service Broker mot de inkommande meddelanden som är användardefinierade egenskaper och system egenskaper. Alla system egenskaper måste föregås av `sys.` i villkors uttrycket. [SQL-språkets delmängd för filter villkor](service-bus-messaging-sql-filter.md) testar om det finns egenskaper ( `EXISTS` ), null-värden ( `IS NULL` ), logiska not/och/eller relationella operatorer, enkla numeriska aritmetiska och enkla text mönster matchning med `LIKE` .
-   *Booleska filter* – **TrueFilter** och **FalseFilter** kan antingen orsaka att alla inkommande meddelanden (**True**) eller inget av de inkommande meddelandena (**falskt**) väljs för prenumerationen. Dessa två filter härleds från SQL-filtret. 
-   *Korrelations filter* – en **CorrelationFilter** innehåller en uppsättning villkor som matchas mot ett eller flera av de inloggade meddelandets användar-och system egenskaper. En vanlig användning är att matcha mot egenskapen **correlationId** , men programmet kan också välja att matcha följande egenskaper:

    - **Innehålls**
     - **Etikett**
     - **Meddelande**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Till**
     - användardefinierade egenskaper. 
     
     En matchning finns när värdet för en egenskap som anländer är lika med värdet som anges i korrelations filtret. Jämförelsen är Skift läges känslig för sträng uttryck. När du anger flera matchnings egenskaper kombinerar filtret dem som ett logiskt och villkor, vilket innebär att filtret ska matcha, alla villkor måste matcha.

Alla filter utvärderar meddelande egenskaper. Filter kan inte utvärdera meddelande texten.

Komplexa filter regler kräver bearbetnings kapacitet. I synnerhet orsakar användningen av SQL filter-regler lägre övergripande meddelande data flöde vid prenumeration, ämne och namn områdes nivå. När det är möjligt bör program välja korrelations filter över SQL-liknande filter eftersom de är mycket effektivare i bearbetningen och har mindre påverkan på data flödet.

## <a name="actions"></a>Åtgärder

Med villkor för SQL-filter kan du definiera en åtgärd som kan kommentera meddelandet genom att lägga till, ta bort eller ersätta egenskaper och deras värden. Åtgärden [använder ett SQL-like-uttryck som har en](service-bus-messaging-sql-filter.md) felaktig Lean-syntax i SQL Update-uttrycket. Åtgärden utförs på meddelandet efter att den har matchats och innan meddelandet har marker ATS i prenumerationen. Ändringarna i meddelande egenskaperna är privata för det meddelande som kopieras till prenumerationen.

## <a name="usage-patterns"></a>Användningsmönster

Det enklaste användnings scenariot för ett ämne är att varje prenumeration får en kopia av varje meddelande som skickas till ett ämne, vilket möjliggör ett sändnings mönster.

Filter och åtgärder möjliggör två ytterligare grupper av mönster: partitionering och routning.

Partitionering använder filter för att distribuera meddelanden över flera befintliga ämnes prenumerationer på ett förutsägbart och ömsesidigt uteslutande sätt. Partitionerings mönstret används när ett system skalas ut för att hantera många olika kontexter i funktions identiska avdelningar som var och en innehåller en delmängd av de totala data. till exempel kund profil information. Med partitionering skickar en utgivare meddelandet till ett ämne utan att kräva någon kunskap om partitionerings modellen. Meddelandet flyttas sedan till rätt prenumeration från vilken det sedan kan hämtas av partitionens meddelande hanterare.

Routning använder filter för att distribuera meddelanden över ämnes prenumerationer på ett förutsägbart sätt, men inte nödvändigt vis exklusivt. I samband med funktionen för [automatisk vidarebefordring](service-bus-auto-forwarding.md) kan ämnes filter användas för att skapa komplexa vägvals diagram inom ett Service Bus namn område för meddelande distribution inom en Azure-region. Med Azure Functions eller Azure Logic Apps fungerar som en bro mellan Azure Service Bus namnrum kan du skapa komplexa globala topologier med direkt integrering i branschspecifika program.

## <a name="examples"></a>Exempel
Exempel finns i [Service Bus filter exempel](service-bus-filter-examples.md).



> [!NOTE]
> Eftersom Azure Portal nu stöder Service Bus Explorer-funktioner kan du skapa eller redigera prenumerations filter från portalen. 

## <a name="next-steps"></a>Nästa steg
Se följande exempel: 

- [.NET – grundläggande självstudier för att skicka och ta emot med filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-ämnes filter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-mall](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)