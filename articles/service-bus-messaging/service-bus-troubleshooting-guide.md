---
title: Fel söknings guide för Azure Service Bus | Microsoft Docs
description: Lär dig mer om fel söknings tips och rekommendationer för några problem som kan uppstå när du använder Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 7de39e5a3a7b6cbb8e5fa504f073023853e18366
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179705"
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
- Om du vill hitta rätt IP-adresser som ska läggas till i listan över tillåtna anslutningar, se [vilka IP-adresser som jag behöver lägga till i listan över tillåtna](service-bus-faq.md#what-ip-addresses-do-i-need-to-add-to-allow-list). 


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

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Resource Manager undantag](service-bus-resource-manager-exceptions.md). Den visar undantag som genereras när du interagerar med Azure Service Bus att använda Azure Resource Manager (via mallar eller direkta anrop).
- [Undantag för meddelanden](service-bus-messaging-exceptions.md). Den innehåller en lista över undantag som har genererats av .NET Framework för Azure Service Bus.