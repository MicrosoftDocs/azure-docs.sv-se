---
title: Azure Service Bus vanliga frågor och svar (FAQ) | Microsoft Docs
description: Den här artikeln innehåller svar på några vanliga frågor och svar om Azure Service Bus.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693417"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Service Bus

I den här artikeln beskrivs några vanliga frågor om Microsoft Azure Service Bus. Du kan också besöka [vanliga frågor och svar om Azure-support](https://azure.microsoft.com/support/faq/) för allmän pris information och supportinformation för Azure.


## <a name="general-questions-about-azure-service-bus"></a>Allmänna frågor om Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) är en moln plattform för asynkrona meddelanden som gör att du kan skicka data mellan frikopplade system. Microsoft erbjuder den här funktionen som en tjänst, vilket innebär att du inte behöver vara värd för din egen maskin vara för att använda den.

### <a name="what-is-a-service-bus-namespace"></a>Vad är ett Service Bus namn område?
En [namnrymd](service-bus-create-namespace-portal.md) tillhandahåller en omfattnings behållare för att adressera Service Bus resurser i ditt program. Du måste skapa ett namn område för att kunna använda Service Bus och är ett av de första stegen i komma igång.

### <a name="what-is-an-azure-service-bus-queue"></a>Vad är en Azure Service Bus kö?
En [Service Bus kö](service-bus-queues-topics-subscriptions.md) är en entitet i vilken meddelanden lagras. Köer är användbara när du har flera program eller flera delar av ett distribuerat program som måste kommunicera med varandra. Kön liknar ett distributions Center i så att flera produkter (meddelanden) tas emot och sedan skickas från den platsen.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Vad är Azure Service Bus ämnen och prenumerationer?
Ett ämne kan visualiseras som en kö och när du använder flera prenumerationer blir det en rikare meddelande modell. i stort sett ett ett-till-många-kommunikations verktyg. Den här publicerings-/prenumerations modellen (eller *pub/sub*) aktiverar ett program som skickar ett meddelande till ett ämne med flera prenumerationer för att få meddelandet mottaget av flera program.

### <a name="what-is-a-partitioned-entity"></a>Vad är en partitionerad entitet?
En konventionell kö eller ett ämne hanteras av en enskild meddelande tjänst och lagras i ett meddelande arkiv. Stöds endast på nivån Basic och standard-meddelande nivåer, en [partitionerad kö eller ett ämne](service-bus-partitioning.md) hanteras av flera meddelande hanterare och lagras i flera meddelande arkiv. Den här funktionen innebär att det totala data flödet för en partitionerad kö eller ett ämne inte längre begränsas av prestandan för en enskild meddelande utjämning eller meddelande arkiv. Ett tillfälligt avbrott i ett meddelande arkiv återger inte heller en partitionerad kö eller ett ämne som inte är tillgängligt.

Ordningen är inte säkerställd när du använder partitionerade entiteter. I händelse av att en partition inte är tillgänglig kan du fortfarande skicka och ta emot meddelanden från andra partitioner.

 Partitionerade entiteter stöds inte längre i [Premium-SKU: n](service-bus-premium-messaging.md). 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Var lagrar Azure Service Bus data?
Azure Service Bus standard nivån använder Azure SQL Database för sitt Server dels lagrings lager. För alla regioner utom Brasilien och Sydostasien finns säkerhets kopian av databasen i en annan region (vanligt vis i den Azure-kopplade regionen). För södra Brasilien och Sydostasien regioner lagras databas säkerhets kopior i samma region för att tillgodose placering-krav för de här regionerna.

Azure Service Bus Premium-nivån lagrar metadata och data i regioner som du väljer. När geo-haveri beredskap har kon figurer ATS för ett Azure Service Bus Premium-namnområde kopieras metadata till den sekundära regionen som du väljer.


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Vilka portar måste jag öppna i brand väggen? 
Du kan använda följande protokoll med Azure Service Bus för att skicka och ta emot meddelanden:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- Hypertext Transfer Protocol 1,1 med TLS (HTTPS)

I följande tabell visas de utgående TCP-portarna som du måste öppna för att kunna använda dessa protokoll för att kommunicera med Azure Service Bus:

| Protokoll | Port | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP med TLS. Se [AMQP-protokoll guide](service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Den här porten används för HTTP/REST API och för AMQP-ingångar |

HTTPS-porten krävs vanligt vis för utgående kommunikation även när AMQP används över Port 5671, eftersom flera hanterings åtgärder som utförs av klient-SDK: er och förvärv av tokens från Azure Active Directory (när de används) körs över HTTPS. 

De officiella Azure-SDK: erna använder vanligt vis AMQP-protokollet för att skicka och ta emot meddelanden från Service Bus. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Det äldre WindowsAzure. Service Bus-paketet för .NET Framework har ett alternativ för att använda det äldre "Service Bus Messaging Protocol" (SBMP), även kallat "netmessaging". Protokollet använder TCP-portarna 9350-9354. Standard läget för det här paketet är att automatiskt identifiera om dessa portar är tillgängliga för kommunikation och kommer att växla till WebSockets med TLS via port 443 om så inte är fallet. Du kan åsidosätta den här inställningen och framtvinga det här läget genom att ställa in `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) på [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) inställningen, som tillämpas globalt för programmet.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Vilka IP-adresser måste jag lägga till i listan över tillåtna?
Följ dessa steg om du vill hitta rätt IP-adresser som ska läggas till i listan över tillåtna anslutningar:

1. Kör följande kommando från en kommando tolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anteckna IP-adressen som returnerades i `Non-authoritative answer` . 

Om du använder **zon redundans** för ditt namn område måste du utföra några ytterligare steg: 

1. Först kör du nslookup i namn området.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Anteckna namnet i avsnittet **icke-auktoritativt svar** , vilket är i något av följande format: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Kör nslookup för var och en med suffix S1, S2 och S3 för att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighets zoner. 

    > [!NOTE]
    > Den IP-adress som returnerades av `nslookup` kommandot är inte en statisk IP-adress. Det förblir dock konstant tills den underliggande distributionen tas bort eller flyttas till ett annat kluster.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Var kan jag hitta IP-adressen för klienten som skickar/tar emot meddelanden till/från ett namn område? 
Vi loggar inte IP-adresserna för klienter som skickar eller tar emot meddelanden till/från namn området. Återskapa nycklar så att alla befintliga klienter inte kan autentisera och granska [Azures inställningar för rollbaserad åtkomst kontroll (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)för att säkerställa att endast tillåtna användare eller program har åtkomst till namn området. 

Om du använder ett **Premium** -namnområde använder du [IP-filtrering](service-bus-ip-filtering.md), [slut punkter för virtuella nätverks tjänster](service-bus-service-endpoints.md)och [privata slut punkter](private-link-service.md) för att begränsa åtkomsten till namn området. 

## <a name="best-practices"></a>Bästa praxis
### <a name="what-are-some-azure-service-bus-best-practices"></a>Vad är några Azure Service Bus bästa praxis?
Se [metod tips för prestanda förbättringar med Service Bus][Best practices for performance improvements using Service Bus] – i den här artikeln beskrivs hur du optimerar prestanda vid utbyte av meddelanden.

### <a name="what-should-i-know-before-creating-entities"></a>Vad ska jag veta innan jag skapar entiteter?
Följande egenskaper för en kö och ämnen kan inte ändras. Ta hänsyn till den här begränsningen när du etablerar dina entiteter, eftersom dessa egenskaper inte kan ändras utan att en ny ersättnings entitet skapas.

* Partitionering
* Sessioner
* Dubblettidentifiering
* Express entitet

## <a name="pricing"></a>Priser
I det här avsnittet besvaras några vanliga frågor om Service Bus prissättnings struktur.

I artikeln [Service Bus priser och fakturering](https://azure.microsoft.com/pricing/details/service-bus/) förklaras fakturerings mätare i Service Bus. Mer information om Service Bus pris alternativ finns i [Service Bus pris information](https://azure.microsoft.com/pricing/details/service-bus/).

Du kan också besöka [vanliga frågor och svar om Azure-support](https://azure.microsoft.com/support/faq/) för allmän pris information om Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Hur debiteras du för Service Bus?
Fullständig information om Service Bus priser finns i [Service Bus pris information][Pricing overview]. Utöver de priser som anges debiteras du för tillhör ande data överföringar för utgående trafik utanför data centret där ditt program är etablerad.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Vilken användning av Service Bus är beroende av data överföring? Vad är det inte?
All data överföring inom en angiven Azure-region tillhandahålls utan kostnad, samt all inkommande data överföring. Data överföring utanför en region omfattas av utgående kostnader som finns [här](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Debiteras Service Bus för lagring?
Nej. Service Bus debiteras inte för lagring. Det finns dock en kvot som begränsar den maximala mängden data som kan sparas per kö/ämne. Se nästa vanliga frågor och svar.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Jag har ett Service Bus standard namn område. Varför visas avgifter under resurs gruppen "$system"?
Azure Service Bus nyligen uppgraderade fakturerings komponenterna. På grund av den här ändringen kan du se rad objekt för resursen "/Subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" under resurs gruppen "$system" om du har ett Service Bus standard namn område.

Dessa avgifter representerar bas avgiften per Azure-prenumeration som har etablerad en Service Bus standard-namnrymd. 

Det är viktigt att Observera att dessa avgifter inte är nya, det vill säga de fanns i den tidigare fakturerings modellen. Den enda ändringen är att de nu är listade under $system. Det är färdigt på grund av begränsningar i det nya fakturerings systemet som grupperar prenumerations kostnader, som inte är knutna till en speciell resurs, under "$system" resurs-ID.

## <a name="quotas"></a>Kvoter

En lista över Service Bus gränser och kvoter finns i [Översikt över Service Bus kvoter][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hur hanterar du meddelanden med storlek > 1 MB?
Service Bus meddelande tjänster (köer och ämnen/prenumerationer) gör att programmet kan skicka meddelanden med upp till 256 KB (standard nivå) eller 1 MB (Premium-nivå). Om du hanterar meddelanden som är större än 1 MB använder du det kontroll mönster för anspråk som beskrivs i [det här blogg inlägget](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Felsökning
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Varför kan jag inte skapa ett namn område efter att ha tagit bort det från en annan prenumeration? 
När du tar bort ett namn område från en prenumeration väntar du i fyra timmar innan du skapar det igen med samma namn i en annan prenumeration. Annars kan du få följande fel meddelande: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Vad är några av undantagen som genereras av Azure Service Bus API: er och deras föreslagna åtgärder?
En lista över möjliga Service Bus undantag finns i [Översikt över undantag][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk som stöder generering av en signatur?
Signaturer för delad åtkomst är en autentiseringsmekanism som baseras på SHA-256-säkra hash-värden eller URI: er. Information om hur du genererar egna signaturer i Node.js, PHP, Java, python och C# finns i artikeln [signaturer för delad åtkomst][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Hantering av prenumerationer och namn område
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur gör jag för att migrera ett namn område till en annan Azure-prenumeration?

Du kan flytta ett namn område från en Azure-prenumeration till en annan med hjälp av antingen [Azure Portal](https://portal.azure.com) -eller PowerShell-kommandon. För att kunna utföra åtgärden måste namn området redan vara aktivt. Användaren som kör kommandona måste vara administratör både på käll-och mål prenumerationerna.

#### <a name="portal"></a>Portal

Om du vill använda Azure Portal för att migrera Service Bus namnrum till en annan prenumeration följer du anvisningarna [här](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Följande sekvens med PowerShell-kommandon flyttar ett namn område från en Azure-prenumeration till en annan. För att kunna utföra den här åtgärden måste namn området redan vara aktivt, och användaren som kör PowerShell-kommandon måste vara administratör för både käll-och mål prenumerationer.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>Är det möjligt att inaktivera TLS 1,0 eller 1,1 på Service Bus namn områden?
Nej. Det går inte att inaktivera TLS 1,0 eller 1,1 på Service Bus-namnområden. Använd TLS 1,2 eller senare i klient program som ansluter till Service Bus. Mer information finns i [FRAMTVINGA TLS 1,2-användning med Azure Service Bus-Microsoft Tech community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912).

## <a name="next-steps"></a>Nästa steg
Mer information om Service Bus finns i följande artiklar:

* [Vi presenterar Azure Service Bus Premium (blogg inlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Vi presenterar Azure Service Bus Premium (channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus](service-bus-messaging-overview.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
