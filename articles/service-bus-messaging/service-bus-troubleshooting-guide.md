---
title: Felsökningsguide för Azure Service Bus | Microsoft Docs
description: Lär dig mer om felsökningstips och rekommendationer för några problem som kan uppstå när du använder Azure Service Bus.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786575"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Felsökningsguide för Azure Service Bus
Den här artikeln innehåller felsökningstips och rekommendationer för några problem som du kan se när du använder Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Anslutningsproblem, certifikat eller timeout-problem
Följande steg kan hjälpa dig med felsökning av anslutning/certifikat/timeout-problem för alla tjänster under *.servicebus.windows.net. 

- Bläddra till [eller wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Det hjälper dig att kontrollera om du har problem med IP-filtrering eller virtuellt nätverk eller certifikatkedja, vilket är vanligt när du använder Java SDK.

    Ett exempel på ett lyckat meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på felmeddelande:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrollera om någon port är blockerad i brandväggen. Portarna som används är 443 (HTTPS), 5671 (AMQP) och 9354 (Net Messaging/SBMP). Beroende på vilket bibliotek du använder används även andra portar. Här är exempelkommandot som kontrollerar om 5671-porten är blockerad. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det uppstår tillfälliga anslutningsproblem kör du följande kommando för att kontrollera om det finns några bortkopplade paket. Det här kommandot försöker upprätta 25 olika TCP-anslutningar var 1:e sekund med tjänsten. Sedan kan du kontrollera hur många av dem som lyckades/misslyckades och även se TCP-anslutningens svarstid. Du kan ladda ned `psping` verktyget [härifrån.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc` `ping` , och så vidare. 
- Hämta en nätverksspårning om de föregående stegen inte hjälper till och analysera den med hjälp av verktyg som [Wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 
- Information om hur du hittar rätt IP-adresser att lägga till i allowlist för dina anslutningar finns i Vilka IP-adresser behöver jag [lägga till i allowlist](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-). 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå vid tjänstuppgraderingar/omstarter

### <a name="symptoms"></a>Symtom
- Begäranden kan tillfälligt begränsas.
- Det kan finnas en minskning i inkommande meddelanden/begäranden.
- Loggfilen kan innehålla felmeddelanden.
- Programmen kan vara frånkopplade från tjänsten i några sekunder.

### <a name="cause"></a>Orsak
Uppgraderingar och omstarter av backend-tjänsten kan orsaka dessa problem i dina program.

### <a name="resolution"></a>Lösning
Om programkoden använder SDK är återförsöksprincipen redan inbyggd och aktiv. Programmet återansluter utan betydande inverkan på programmet/arbetsflödet.

## <a name="unauthorized-access-send-claims-are-required"></a>Obehörig åtkomst: Skicka anspråk krävs

### <a name="symptoms"></a>Symtom 
Du kan se det här felet när du försöker komma åt ett Service Bus-ämne från Visual Studio på en lokal dator med hjälp av en användar tilldelad hanterad identitet med behörighet att skicka.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Orsak
Identiteten har inte behörighet att komma åt Service Bus ämnet. 

### <a name="resolution"></a>Lösning
Lös problemet genom att installera [biblioteket Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Mer information finns i Autentisering [för lokal utveckling.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Information om hur du tilldelar behörigheter till roller finns i [Authenticate a managed identity with Azure Active Directory to access Azure Service Bus resources](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Service Bus: Placera token misslyckades

### <a name="symptoms"></a>Symtom
När du försöker skicka fler än 1 000 meddelanden med samma Service Bus anslutning får du följande felmeddelande: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Orsak
Det finns en gräns för hur många token som används för att skicka och ta emot meddelanden med en enda anslutning till en Service Bus namnområde. Det är 1 000. 

### <a name="resolution"></a>Lösning
Öppna en ny anslutning till Service Bus för att skicka fler meddelanden.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>Det går inte att lägga till en regel för virtuellt nätverk med PowerShell

### <a name="symptoms"></a>Symtom
Du har konfigurerat två undernät från ett enda virtuellt nätverk i en regel för virtuellt nätverk. När du försöker ta bort ett undernät med cmdleten [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) tas inte undernätet bort från regeln för virtuellt nätverk. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Orsak
Det Azure Resource Manager ID som du angav för undernätet kan vara ogiltigt. Detta kan inträffa när det virtuella nätverket finns i en annan resursgrupp än den som har Service Bus namnområdet. Om du inte uttryckligen anger resursgruppen för det virtuella nätverket konstruerar CLI-kommandot Azure Resource Manager-ID:t med hjälp av resursgruppen för Service Bus-namnområdet. Därför går det inte att ta bort undernätet från nätverksregeln. 

### <a name="resolution"></a>Lösning
Ange det fullständiga Azure Resource Manager-ID:t för undernätet som innehåller namnet på den resursgrupp som har det virtuella nätverket. Exempel:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Azure Resource Manager undantag](service-bus-resource-manager-exceptions.md). Den visar undantag som genereras när du interagerar Azure Service Bus med Azure Resource Manager (via mallar eller direkta anrop).
- [Meddelandeundantag](service-bus-messaging-exceptions.md). Den innehåller en lista över undantag som genereras av .NET Framework för Azure Service Bus.