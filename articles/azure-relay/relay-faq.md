---
title: Azure Relay FAQ | Microsoft Docs
description: Den här artikeln innehåller svar på några vanliga frågor om Azure Relay tjänsten.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 83cbc4b02bb8b63878abd046ed2314728546e87a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250066"
---
# <a name="azure-relay-faqs"></a>Azure Relay vanliga frågor

I den här artikeln besvaras några vanliga frågor och svar om [Azure Relay](https://azure.microsoft.com/services/service-bus/). Allmän information om priser och support för Azure finns i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allmänna frågor
### <a name="what-is-azure-relay"></a>Vad är Azure Relay?
[Tjänsten Azure Relay](relay-what-is-it.md) underlättar dina hybrid program genom att hjälpa dig att på ett säkert sätt exponera tjänster som finns i ett företags nätverk till det offentliga molnet. Du kan exponera tjänsterna utan att öppna en brand Väggs anslutning och utan att kräva påträngande ändringar i företags nätverkets infrastruktur.

### <a name="what-is-a-relay-namespace"></a>Vad är ett relä namn område?
Ett [namn område](relay-create-namespace-portal.md) är en omfattnings behållare som du kan använda för att adressera relä resurser i ditt program. Du måste skapa ett namn område för att kunna använda reläet. Det här är ett av de första stegen för att komma igång.

### <a name="what-happened-to-service-bus-relay-service"></a>Vad hände med att Service Bus Relay tjänsten?
Den tidigare namngivna Service Bus Relays tjänsten kallas nu [Azure Relay](service-bus-relay-tutorial.md). Du kan fortsätta att använda den här tjänsten som vanligt. Den Hybridanslutningar funktionen är en uppdaterad version av en tjänst som har planterats från Azure BizTalk Services. WCF Relay och Hybridanslutningar fortsätter att stödjas.

## <a name="pricing"></a>Priser
I det här avsnittet besvaras några vanliga frågor om relä pris strukturen. Du kan också se [vanliga frågor och svar om Azure-support](https://azure.microsoft.com/support/faq/) för allmän pris information om Azure. Fullständig information om priser för Reläering finns [Service Bus pris information][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Hur debiteras du för Hybridanslutningar och WCF Relay?
Fullständig information om priser för Reläering finns i tabellen [hybridanslutningar-och WCF-reläer][Pricing overview] på sidan med pris information för Service Bus. Utöver de priser som anges på sidan debiteras du för tillhör ande data överföringar för utgående trafik utanför data centret där ditt program är etablerad.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Hur faktureras jag för Hybridanslutningar?
Här är tre exempel på fakturerings scenarier för Hybridanslutningar:

*   Scenario 1:
    *   Du har en enda lyssnare, till exempel en instans av Hybridanslutningar Manager som är installerad och körs kontinuerligt under hela månaden.
    *   Du skickar 3 GB data över anslutningen under månaden. 
    *   Den totala kostnaden är $5.
*   Scenario 2:
    *   Du har en enda lyssnare, till exempel en instans av Hybridanslutningar Manager som är installerad och körs kontinuerligt under hela månaden.
    *   Du skickar 10 GB data över anslutningen under månaden.
    *   Den totala kostnaden är $7,50. Det är $5 för anslutningen och de första 5 GB + $2,50 för ytterligare 5 GB data.
*   Scenario 3:
    *   Du har två instanser, A och B, av Hybridanslutningar Manager installerat och körs kontinuerligt under hela månaden.
    *   Du skickar 3 GB data över anslutning A under månaden.
    *   Du skickar 6 GB data över Connection B under månaden.
    *   Den totala kostnaden är $10,50. Det är $5 för anslutning A + $5 för anslutning B + $0,50 (för den sjätte gigabyte på anslutning B).


### <a name="how-are-hours-calculated-for-relay"></a>Hur beräknas timmar för relä?

WCF Relay är endast tillgängligt i namn områden på standard nivån. Priser och [anslutnings kvoter](../service-bus-messaging/service-bus-quotas.md) för reläer har annars inte ändrats. Det innebär att reläerna fortsätter att debiteras baserat på antalet meddelanden (inte åtgärder) och relä timmar. Mer information finns i tabellen ["hybridanslutningar-och WCF-reläer"](https://azure.microsoft.com/pricing/details/service-bus/) på sidan med pris information.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Vad händer om jag har fler än en lyssnare ansluten till ett speciellt relä?
I vissa fall kan ett enda relä ha flera anslutna lyssnare. Ett relä anses öppet när minst en relä lyssnare är ansluten till den. Att lägga till lyssnare till ett öppet relä resultat i ytterligare relä timmar. Antalet relä avsändare (klienter som anropar eller skickar meddelanden till reläer) som är anslutna till ett relä påverkar inte beräkningen av relä timmar.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Hur beräknas meddelande mätaren för WCF-reläer?
(**Detta gäller endast WCF-reläer. Meddelanden är inte en kostnad för Hybridanslutningar.**)

I allmänhet beräknas fakturerbara meddelanden för reläer med samma metod som används för sammanställda entiteter (köer, ämnen och prenumerationer), som beskrivs ovan. Det finns dock några viktiga skillnader.

Att skicka ett meddelande till en Azure Relay behandlas som "fullständig via" Skicka till relä lyssnaren som tar emot meddelandet. Den behandlas inte som en skicka-åtgärd till Azure Relay, följt av en leverans till relä lyssnaren. Ett anrop till tjänsten Request-Reply-service (på upp till 64 KB) mot en relä lyssnare resulterar i två fakturerbara meddelanden: ett fakturerbart meddelande för begäran och ett fakturerbart meddelande för svaret (förutsatt att svaret också är 64 KB eller mindre). Detta skiljer sig från att använda en kö för att tjänstassisterad mellan en klient och en tjänst. Om du använder en kö för att tjänstassisterad mellan en klient och en tjänst, kräver samma Request-Reply-mönster att en begäran skickas till kön, följt av en ur kö/leverans från kön till tjänsten. Detta följs av ett svar som skickas till en annan kö och en ur kö/leverans från den kön till klienten. Med samma storleks antaganden i hela (upp till 64 KB) resulterar det i det reparerade kön i 4 fakturerbara meddelanden. Du faktureras för dubbelt så många meddelanden att implementera samma mönster som du utför med hjälp av reläet. Det finns naturligtvis fördelar med att använda köer för att uppnå det här mönstret, till exempel hållbarhet och belastnings utjämning. Dessa förmåner kan motivera ytterligare kostnader.

Reläer som öppnas med hjälp av **netTCPRelay** WCF-bindning behandla meddelanden som inte är enskilda meddelanden, men som data strömmar i systemet. När du använder den här bindningen får bara avsändaren och lyssnaren insyn i ram för de enskilda meddelanden som skickas och tas emot. För reläer som använder **netTCPRelay** -bindningen behandlas alla data som en ström för att beräkna fakturerbara meddelanden. I det här fallet beräknar Service Bus den totala mängden data som skickas eller tas emot via varje enskilt relä på en 5 minuters basis. Sedan delar den upp den totala mängden data med 64 KB för att fastställa antalet fakturerbara meddelanden för det reläet under den tids perioden.

## <a name="quotas"></a>Kvoter
| Kvot namn | Omfång |  Kommentarer | Värde |
| --- | --- | --- | --- |
| Samtidiga lyssnare i ett relä |Entitet (hybrid anslutning eller WCF-relä) |Efterföljande begär Anden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |25 |
| Samtidiga relä anslutningar per alla relä slut punkter i ett tjänst namn område |Namnområde |- |5 000 |
| Relä slut punkter per tjänst namn område |Namnområde |- |10 000 |
| Meddelande storlek för [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) -och [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) -reläer |Namnområde |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |64 kB |
| Meddelande storlek för [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) -och [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) -reläer |Namnområde |Ingen gräns för meddelande storlek. |Obegränsat |

### <a name="does-relay-have-any-usage-quotas"></a>Har reläet några användnings kvoter?
För alla moln tjänster anger Microsoft som standard en sammanställd kvot för månatlig användning som beräknas för alla kund prenumerationer. Vi förstår att när dina behov kan överskrida dessa gränser. Du kan när som helst kontakta kund tjänst så att vi kan förstå dina behov och justera dessa gränser på lämpligt sätt. För Service Bus är de aggregerade användnings kvoterna följande:

* 5 000 000 000 meddelanden
* 2 000 000 relä timmar

Även om vi förbehåller oss rätten att inaktivera ett konto som överskrider den månatliga användnings kvoten ger vi e-postaviseringar, och vi gör flera försök att kontakta kunden innan du vidtar åtgärder. Kunder som överskrider dessa kvoter ansvarar fortfarande för överförbruknings kostnader.

### <a name="naming-restrictions"></a>Namngivnings begränsningar
Ett namn på en Relay-namnrymd måste vara mellan 6 och 50 tecken långt.

## <a name="subscription-and-namespace-management"></a>Hantering av prenumerationer och namn område
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur gör jag för att migrera ett namn område till en annan Azure-prenumeration?

Om du vill flytta ett namn område från en Azure-prenumeration till en annan prenumeration kan du antingen använda [Azure Portal](https://portal.azure.com) eller använda PowerShell-kommandon. Om du vill flytta ett namn område till en annan prenumeration måste namn området redan vara aktivt. Användaren som kör kommandona måste vara administratörs användare på både käll-och mål prenumerationer.

#### <a name="azure-portal"></a>Azure Portal

Om du vill använda Azure Portal för att migrera Azure Relay namnrum från en prenumeration till en annan prenumeration kan du läsa mer i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Använd följande kommandosekvens för att använda PowerShell för att flytta ett namn område från en Azure-prenumeration till en annan prenumeration. För att kunna utföra den här åtgärden måste namn området redan vara aktivt och användaren som kör PowerShell-kommandon måste vara en administratörs användare på både käll-och mål prenumerationer.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Felsökning
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Vilka är några av undantagen som genereras av Azure Relay API: er och föreslagna åtgärder som du kan vidta?
En beskrivning av vanliga undantag och föreslagna åtgärder som du kan utföra finns i [undantag för vidarebefordran][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk kan jag använda för att skapa en signatur?
Signaturer för delad åtkomst (SAS) är en autentiseringsmekanism baserad på SHA-256-säkra hash-värden eller URI: er. Information om hur du genererar egna signaturer i Node.js, PHP, python, Java, C och C# finns i [Service Bus autentisering med signaturer för delad åtkomst][Shared Access Signatures].

### <a name="is-it-possible-to-allow-only-some-relay-endpoints"></a>Är det möjligt att bara tillåta vissa relä slut punkter?
Ja. Relä klienten ansluter till Azure Relay tjänsten med hjälp av fullständigt kvalificerade domän namn. Kunder kan lägga till en post för `*.servicebus.windows.net` i brand väggar som har stöd för godkännande av DNS-listor.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
