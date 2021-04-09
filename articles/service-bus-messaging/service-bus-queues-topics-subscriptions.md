---
title: Azure Service Bus Messaging – köer, ämnen och prenumerationer
description: Den här artikeln innehåller en översikt över Azure Service Bus meddelande enheter (kö, ämnen och prenumerationer).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b8fb68509ad920fc6911290377f49b89ec610b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096327"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer
Azure Service Bus har stöd för en uppsättning molnbaserade, objektorienterade mellanprogram teknologier, inklusive Reliable Message Queuing och varaktiga meddelanden för publicering/prenumeration. Dessa Brokered Messaging-funktioner kan ses som fristående meddelande funktioner som stöder publicerings prenumeration, temporala kopplingar och belastnings Utjämnings scenarier med hjälp av Service Bus meddelande arbets belastning. Frikopplad kommunikation har många fördelar. Till exempel kan klienter och servrar ansluta efter behov och utföra sina åtgärder på ett asynkront sätt.

Meddelande enheterna som utgör kärnan till meddelande funktionerna i Service Bus är **köer**, **ämnen och prenumerationer** och regler/åtgärder.

## <a name="queues"></a>Köer
Köer ger en eller flera konkurrerande konsumenter **först in-, First-Out** (FIFO). Det innebär att mottagare normalt får och bearbetar meddelanden i den ordning som de har lagts till i kön. Och endast en meddelande mottagare får och bearbetar varje meddelande. En viktig fördel med att använda köer är att uppnå **temporal koppling av program komponenter**. Med andra ord behöver producenterna (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden på samma tid. Det beror på att meddelanden lagras varaktigt i kön. Dessutom behöver producenten inte vänta på att ett svar från konsumenten ska fortsätta att bearbeta och skicka meddelanden.

En relaterad förmån är **belastnings utjämning**, vilket gör det möjligt för producenter och konsumenter att skicka och ta emot meddelanden med olika priser. I många program varierar system belastningen över tid. Bearbetnings tiden som krävs för varje arbets enhet är dock normalt konstant. Genom att använda en kö som är inblandad är det bara att använda programmet för att kunna hantera genomsnittlig belastning i stället för hög belastning. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Den här funktionen sparar pengar direkt med avseende på den mängd infrastruktur som krävs för att hantera program belastningen. När belastningen ökar kan fler arbets processer läggas till för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom gör den här hämtnings Utjämnings belastningen den bästa användningen av arbets datorerna även om arbets datorerna med att bearbeta Power pull-meddelanden med sin egen maximala taxa. Det här mönstret kallas ofta för ett **konkurrerande konsument**-mönster.

Att använda köer på mellan meddelande producenter och konsumenter är en lös koppling mellan komponenterna. Eftersom producenter och konsumenter inte är medvetna om varandra kan en konsument uppgraderas utan att det påverkar producenten.

### <a name="create-queues"></a>Skapa köer
Du kan skapa köer med hjälp av mallarna [Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md)eller [Resource Manager](service-bus-resource-manager-namespace-queue.md). Skicka och ta emot meddelanden med hjälp av klienter skrivna i [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [python](service-bus-python-how-to-use-queues.md), [Java Script](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)och [ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Mottagnings lägen
Du kan ange två olika lägen för Service Bus ta emot meddelanden.

- **Ta emot och ta bort**. I det här läget, när Service Bus tar emot begäran från konsumenten, markerar det meddelandet som förbrukat och returnerar det till klient programmet. Det här läget är den enklaste modellen. Det fungerar bäst för scenarier där programmet kan tolerera att inte bearbeta ett meddelande om ett fel uppstår. För att förstå det här scenariot bör du överväga ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. När Service Bus markerar meddelandet som förbrukat börjar programmet förbruka meddelanden vid omstart. Det kommer att sakna det meddelande som förbrukades före kraschen.
- **Spetsigt lås**. I det här läget blir Receive-åtgärden två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. 
    1. Söker efter nästa meddelande som ska förbrukas, **låser** det för att hindra andra användare från att ta emot det och sedan returnera meddelandet till programmet. 
    1. När programmet har slutfört bearbetningen av meddelandet begär det att Service Bus tjänsten slutför det andra steget i mottagnings processen. Sedan **markerar tjänsten meddelandet som förbrukat**. 

        Om programmet inte kan bearbeta meddelandet av någon anledning kan det begära att Service Buss tjänsten ska **överge** meddelandet. Service Bus **låser upp** meddelandet och gör det tillgängligt att tas emot igen, antingen av samma konsument eller av en annan konkurrerande konsument. För det andra är det en **tids gräns** som är kopplad till låset. Om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut, kan Service Bus låsa upp meddelandet och göra det tillgängligt för att tas emot igen.

        Om programmet kraschar efter det att meddelandet har bearbetats, men innan det begär Service Buss tjänsten för att slutföra meddelandet, kan Service Bus leverera meddelandet till programmet när det startas om. Den här processen kallas ofta minst **en gång för** bearbetning. Det vill säga varje meddelande bearbetas minst en gång. Men i vissa situationer kan samma meddelande levereras igen. Om ditt scenario inte kan tolerera dubbel bearbetning, lägger du till ytterligare logik i programmet för att identifiera dubbletter. Mer information finns i [dubblettidentifiering](duplicate-detection.md). Den här funktionen kallas **exakt** för bearbetning.

        > [!NOTE]
        > Mer information om dessa två lägen finns i avsnittet om att [lösa mottagnings åtgärder](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer
En kö tillåter bearbetning av ett meddelande av en enskild konsument. Ämnen och prenumerationer är i motsats till köer och innehåller en en-till-många-form av kommunikation i ett mönster för **publicering och prenumeration** . Det är användbart för att skala till ett stort antal mottagare. Varje publicerat meddelande görs tillgängligt för varje prenumeration som registrerats i ämnet. Publisher skickar ett meddelande till ett ämne och en eller flera prenumeranter får en kopia av meddelandet, beroende på filter regler som angetts för dessa prenumerationer. Prenumerationerna kan använda ytterligare filter för att begränsa vilka meddelanden som ska tas emot. Utgivare skickar meddelanden till ett ämne på samma sätt som de skickar meddelanden till en kö. Men konsumenterna tar inte emot meddelanden direkt från ämnet. Konsumenterna tar i stället emot meddelanden från prenumerationer av ämnet. En ämnes prenumeration liknar en virtuell kö som tar emot kopior av meddelanden som skickas till ämnet. Konsumenter tar emot meddelanden från en prenumeration på samma sätt som de tar emot meddelanden från en kö.

Funktionen för att skicka meddelanden i en kö mappar direkt till ett ämne och dess funktioner för meddelande mottagning mappar till en prenumeration. Detta innebär bland annat att prenumerationer har stöd för samma mönster som beskrivs tidigare i det här avsnittet med avseende på köer: konkurrerande konsument, temporala, belastnings utjämning och belastnings utjämning.

### <a name="create-topics-and-subscriptions"></a>Skapa ämnen och prenumerationer
Att skapa ett ämne liknar att skapa en kö, enligt beskrivningen i föregående avsnitt. Du kan skapa ämnen och prenumerationer med hjälp av mallarna [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-tutorial-topics-subscriptions-cli.md)eller [Resource Manager](service-bus-resource-manager-namespace-topic.md). Skicka sedan meddelanden till ett ämne och ta emot meddelanden från prenumerationer med hjälp av klienter skrivna i [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [python](service-bus-python-how-to-use-topics-subscriptions.md), [Java Script](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)och [ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Regler och åtgärder
I många fall måste meddelanden med särskilda egenskaper bearbetas på olika sätt. Om du vill aktivera den här bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har önskade egenskaper och sedan utföra vissa ändringar av dessa egenskaper. Medan Service Bus prenumerationer visar alla meddelanden som skickas till ämnet, kan du bara kopiera en delmängd av dessa meddelanden till kön för virtuella prenumerationer. Filtreringen utförs med hjälp av prenumerations filter. Sådana ändringar kallas **filter åtgärder**. När en prenumeration skapas kan du ange ett filter uttryck som fungerar på meddelandets egenskaper. Egenskaperna kan vara både system egenskaper (till exempel **etikett**) och anpassade program egenskaper (till exempel **StoreName**.) SQL filter-uttrycket är valfritt i det här fallet. Utan ett SQL-filter-uttryck, utförs eventuella filter åtgärder som definierats för en prenumeration på alla meddelanden för den prenumerationen.

Ett fullständigt arbets exempel finns i TopicSubscriptionWithRuleOperationsSample- [exemplet](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) på GitHub.

Mer information om möjliga filter värden finns i dokumentationen för klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) och [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) .

## <a name="java-message-service-jms-20-entities"></a>JMS-enheter (Java Message Service) 2,0
Följande entiteter är tillgängliga via JMS (Java Message Service) 2,0 API.

  * Tillfälliga köer
  * Tillfälliga ämnen
  * Delade varaktiga prenumerationer
  * Delade varaktiga prenumerationer
  * Delade icke-varaktiga prenumerationer
  * Ej delade icke-varaktiga prenumerationer

Lär dig mer om [JMS 2,0-entiteter](java-message-service-20-entities.md) och om hur du [använder dem](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus meddelande hantering](service-bus-messaging-overview.md)
* [Snabbstart: Skicka och ta emot meddelanden med Azure-portalen och .NET](service-bus-quickstart-portal.md)
* [Självstudie: Uppdatera lagret med Azure-portalen och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-portal.md)


