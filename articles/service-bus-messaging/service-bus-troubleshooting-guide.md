---
title: Fel söknings guide för Azure Service Bus | Microsoft Docs
description: Lär dig mer om fel söknings tips och rekommendationer för några problem som kan uppstå när du använder Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: b44587747a59acb3c0124c0a76b63de68d6d8ae7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031298"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Fel söknings guide för Azure Service Bus
Den här artikeln innehåller fel söknings tips och rekommendationer för några problem som kan uppstå när du använder Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problem med anslutning, certifikat eller tids gräns
Följande steg kan hjälpa dig med fel sökning av problem med anslutning/certifikat/tids gräns för alla tjänster under *. servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller virtuella nätverks-eller certifikat kedjan, som är vanliga när du använder Java SDK.

    Ett exempel på ett slutfört meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på fel meddelande:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrol lera om någon port är blockerad i brand väggen. Portarna som används är 443 (HTTPS), 5671 (AMQP) och 9354 (net Messaging/SBMP). Beroende på vilket bibliotek du använder används även andra portar. Här är exempel kommandot som kontrollerar om 5671-porten är blockerad. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget härifrån. [](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc` , `ping` och så vidare. 
- Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 
- Om du vill hitta rätt IP-adresser som ska läggas till i tillåten för dina anslutningar, se [vilka IP-adresser som jag behöver lägga till i tillåten](service-bus-faq.md#what-ip-addresses-do-i-need-to-add-to-allow-list). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå med tjänst uppgraderingar/omstarter

### <a name="symptoms"></a>Symtom
- Begär Anden kan begränsas tillfälligt.
- Det kan finnas en minskning av inkommande meddelanden/begär Anden.
- Logg filen kan innehålla fel meddelanden.
- Programmen kan vara frånkopplade från tjänsten under några sekunder.

### <a name="cause"></a>Orsak
Uppgraderingar och omstarter av backend-tjänsten kan orsaka problemen i dina program.

### <a name="resolution"></a>Lösning
Om program koden använder SDK är principen för återförsök redan inbyggd och aktiv. Programmet återansluter utan betydande påverkan på programmet/arbets flödet.

## <a name="unauthorized-access-send-claims-are-required"></a>Obehörig åtkomst: skicka anspråk krävs

### <a name="symptoms"></a>Symtom 
Du kanske ser det här felet när du försöker komma åt ett Service Bus ämne från Visual Studio på en lokal dator med hjälp av en användardefinierad hanterad identitet med Send-behörighet.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Orsak
Identiteten har inte behörighet att komma åt Service Bus avsnittet. 

### <a name="resolution"></a>Lösning
Lös problemet genom att installera biblioteket [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Mer information finns i [lokal utvecklings autentisering](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication). 

Information om hur du tilldelar behörigheter till roller finns i [autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Azure Service Bus resurser](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Service Bus undantag: det gick inte att skicka token

### <a name="symptoms"></a>Symtom
När du försöker skicka fler än 1000 meddelanden med samma Service Bus anslutning får du följande fel meddelande: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Orsak
Det finns en gräns för antalet tokens som används för att skicka och ta emot meddelanden med en enda anslutning till ett Service Bus-namnområde. Det är 1000. 

### <a name="resolution"></a>Lösning
Öppna en ny anslutning till Service Bus namn området om du vill skicka fler meddelanden.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Det går inte att lägga till en virtuell nätverks regel med PowerShell

### <a name="symptoms"></a>Symtom
Du har konfigurerat två undernät från ett enda virtuellt nätverk i en virtuell nätverks regel. När du försöker ta bort ett undernät med cmdleten [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) tar det inte bort under nätet från regeln för det virtuella nätverket. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Orsak
Det Azure Resource Manager-ID som du har angett för under nätet kan vara ogiltigt. Detta kan inträffa när det virtuella nätverket finns i en annan resurs grupp än den som har Service Bus-namnområdet. Om du inte uttryckligen anger resurs gruppen för det virtuella nätverket skapar CLI-kommandot Azure Resource Manager-ID med hjälp av resurs gruppen i namn området Service Bus. Det går därför inte att ta bort under nätet från nätverks regeln. 

### <a name="resolution"></a>Lösning
Ange det fullständiga Azure Resource Manager-ID: t för under nätet som innehåller namnet på den resurs grupp som har det virtuella nätverket. Exempel:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Resource Manager undantag](service-bus-resource-manager-exceptions.md). Den visar undantag som genereras när du interagerar med Azure Service Bus att använda Azure Resource Manager (via mallar eller direkta anrop).
- [Undantag för meddelanden](service-bus-messaging-exceptions.md). Den innehåller en lista över undantag som har genererats av .NET Framework för Azure Service Bus.