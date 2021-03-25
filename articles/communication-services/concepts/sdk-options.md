---
title: 'Klient bibliotek och REST API: er för Azure Communication Services'
titleSuffix: An Azure Communication Services concept document
description: 'Läs mer om SDK: er och REST API: er för Azure Communication Services.'
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107898"
---
# <a name="client-libraries-and-rest-apis"></a>Klientbibliotek och REST-API:er

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure Communication Services-funktionerna organiseras konceptuellt i sex områden. Vissa områden har fullständigt SDK: er med öppen källkod. Den anropande SDK: n använder patentskyddade nätverks gränssnitt och är stängd-källa och chatt-biblioteket innehåller ett beroende för stängd källa. Exempel och ytterligare teknisk information för SDK: er publiceras i [Azure Communication Services GitHub-lagrings platsen](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Klientbibliotek

| Sammansättning               | Protokoll             |Öppen vs. stängd källa| Namnrymder                          | Funktioner                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Öppna            | Azure. ResourceManager.-kommunikation | Etablera och hantera resurser för kommunikations tjänster             |
| Common                 | REST | Öppna               | Azure. Communication. common          | Tillhandahåller bas typer för andra SDK: er |
| Identitet         | REST | Öppna               | Azure. Communication. identitet  | Hantera användare, åtkomsttoken |
| Telefonnummer         | REST | Öppna               | Azure. Communication. PhoneNumbers  | Hantera telefonnummer |
| Chatt                   | REST med tillverkarspecifik signalering | Öppna med signal paket för stängd källa    | Azure. Communication. Chat            | Lägg till text baserad chatt i real tid i dina program  |
| SMS                    | REST | Öppna              | Azure. Communication. SMS             | Skicka och ta emot SMS-meddelanden |
| Sänder                | Tillverkarspecifik transport | Stängd |Azure. Communication. Call         | Använd röst, video, skärm delning och andra funktioner för data kommunikation i real tid          |

Observera att SDK: erna för Azure Resource Manager, identitet och SMS fokuserar på tjänst integrering och i många fall kan säkerhets problem uppstå om du integrerar dessa funktioner i slutanvändarens program. Common-och chatt-SDK: erna är lämpliga för tjänst-och klient program. Anrops-SDK: n är utformad för klient program. En SDK fokuserar på tjänst scenarier är under utveckling.

### <a name="languages-and-publishing-locations"></a>Språk och publicerings platser

Publicerings platser för enskilda SDK-paket beskrivs nedan.

| Område           | JavaScript | .NET | Python | Java SE | iOS | Android | Övrigt                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Gå via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Common         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | Ej tillämpligt      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identitet | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefonnummer | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chatt           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Sänder        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referensdokumentation     | [dokumentation](https://azure.github.io/azure-sdk-for-js/communication.html)         | [dokumentation](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [dokumentation](http://azure.github.io/azure-sdk-for-java/communication.html)     | [dokumentation](/objectivec/communication-services/calling/)      | [dokumentation](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>REST API:er

API: er för kommunikations tjänster dokumenteras tillsammans med andra Azure REST-API: er i [docs.Microsoft.com](/rest/api/azure/). I den här dokumentationen får du lära dig hur du strukturerar dina HTTP-meddelanden och ger vägledning om hur du använder Postman. Den här dokumentationen erbjuds också i Swagger-format på [GitHub](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Ytterligare support information

### <a name="ios-and-android-support-details"></a>information om iOS och Android-support

- Kommunikations tjänster iOS SDK-mål iOS version 13 + och Xcode 11 +.
- Android Java SDK mål-Android API Level 21 + och Android Studio 4.0 +

### <a name="net-support-details"></a>.NET-support information

Med undantag för anrop av kommunikations tjänst paket riktar sig .NET standard 2,0 som stöder plattformarna som anges nedan.

Support via .NET Framework 4.6.1
- Windows 10, 8,1, 8 och 7
- Windows Server 2012 R2, 2012 och 2008 R2 SP1

Support via .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Högsta OS X-10.12 +
- Linux flera versioner/distributioner
- UWP 10.0.16299 (RS3) september 2017
- Unity 2018,1
- Mono 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="calling-sdk-timeouts"></a>Anropar SDK-tidsgräns

Följande tids gränser gäller för kommunikations tjänsterna som anropar SDK: er:

| Action           | Tidsgräns i sekunder |
| -------------- | ---------- |
| Deltagare för att ansluta/ta bort | 120 |
| Lägg till eller ta bort ny spärr från ett samtal (starta/stoppa video eller skärm delning) | 40 |
| Tids gräns för överföring av anrops åtgärd | 60 |
| 1:1 för anrops etablering | 85 |
| Timeout för etablering av grupp anrop | 85 |
| Tids gräns för PSTN-anrop | 115 |
| Befordra 1:1-anrop till en timeout för grupp anrop | 115 |


## <a name="api-stability-expectations"></a>Förväntningar för API-stabilitet

> [!IMPORTANT]
> Det här avsnittet innehåller vägledning om REST API: er och SDK: er som marker ATS som **stabila** API: er som marker ATS för hands version, för hands version eller beta kan ändras eller föråldras **utan föregående meddelande**.

I framtiden kan vi dra tillbaka versioner av SDK: er för kommunikations tjänster och vi kan införa ändringar i våra REST-API: er och publicerade SDK: er. Azure Communication Services följer *vanligt vis* två support principer för att ta ur bruks versioner:

- Du får ett meddelande minst tre år innan det krävs för att ändra kod på grund av ett kommunikations tjänst gränssnitts ändringar. Alla dokumenterade REST API: er och SDK-API: er är i allmänhet minst tre års varning innan gränssnitten tas ur bruk.
- Du kommer att meddelas minst ett år innan du behöver uppdatera SDK-sammansättningar till den senaste lägre versionen. Dessa obligatoriska uppdateringar bör inte kräva några kod ändringar eftersom de har samma huvud version. Detta gäller särskilt för anropande och chat-bibliotek som har real tids komponenter som ofta kräver säkerhets-och prestanda uppdateringar. Vi rekommenderar att du håller dina SDK: er för kommunikations tjänster uppdaterade.

### <a name="api-and-sdk-decommissioning-examples"></a>API-och SDK-inställnings exempel

**Du har integrerat v24-versionen av SMS-REST API i ditt program. Azure-kommunikation släpper v25.**

Du får 3 års varning innan dessa API: er slutar fungera och tvingas att uppdateras till v25. Den här uppdateringen kan kräva en kod ändring.

**Du har integrerat v 2.02-versionen av anrops-SDK: n i ditt program. Azure-kommunikation frigör v-2.05.**

Du kan behöva uppdatera till v 2.05-versionen av anrops-SDK inom 12 månader från lanseringen av v 2.05. Detta bör vara en enkel ersättning av artefakten utan att det krävs någon kod ändring eftersom v-2.05 finns i den version av v2 och inte har några större ändringar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande SDK-översikter:

- [Översikt över samtals-SDK](../concepts/voice-video-calling/calling-sdk-features.md)
- [Översikt över chatt SDK](../concepts/chat/sdk-features.md)
- [Översikt över SMS SDK](../concepts/telephony-sms/sdk-features.md)

Så här kommer du igång med Azure Communication Services:

- [Skapa Azure-kommunikations resurser](../quickstarts/create-communication-resource.md)
- Generera [användar åtkomst-token](../quickstarts/access-tokens.md)
