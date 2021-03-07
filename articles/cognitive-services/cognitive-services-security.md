---
title: Azure Cognitive Services-säkerhet
titleSuffix: Azure Cognitive Services
description: Lär dig mer om de olika säkerhets aspekterna för Cognitive Services användning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 66de81387fe27bd2a81ebcfeab7d86aac7031ff1
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425302"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services-säkerhet

Säkerhet bör betraktas som högsta prioritet när du utvecklar alla program. Med utgången av aktiverade appar för artificiell intelligens är säkerheten ännu viktigare. I den här artikeln beskrivs olika aspekter av Azure Cognitive Services Security, till exempel användningen av Transport Layer Security, autentisering, säker konfigurering av känsliga data och Customer Lockbox för åtkomst till kund data.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Alla Cognitive Services slut punkter som exponeras via HTTP genomdriver TLS 1,2. Med ett framtvingat säkerhets protokoll bör användare som försöker anropa en Cognitive Services slut punkt följa dessa rikt linjer:

* Klient operativ systemet (OS) måste ha stöd för TLS 1,2
* Det språk (och den plattform) som används för att göra HTTP-anropet måste ange TLS 1,2 som en del av begäran
  * Beroende på språk och plattform, är det möjligt att ange TLS antingen implicit eller explicit

För .NET-användare bör du överväga <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security bästa praxis </a>.

## <a name="authentication"></a>Autentisering

När du diskuterar autentisering finns det flera vanliga felbegrepp. Autentisering och auktorisering är ofta förvirrande för varandra. Identitet är också en viktig komponent i säkerheten. En identitet är en samling information om ett <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">huvud konto </a>. Identitets leverantörer (IdP) tillhandahåller identiteter för Authentication Services. Autentisering handlar om att verifiera en användares identitet. Auktorisering är specifikationen av åtkomst rättigheter och behörigheter för resurser för en specifik identitet. Flera av de Cognitive Services erbjudandena, inklusive Azure rollbaserad åtkomst kontroll (Azure RBAC). Azure RBAC kan användas för att förenkla vissa av ceremonin som arbetar med att hantera huvud konton manuellt. Mer information finns i [Azure-rollbaserad åtkomst kontroll för Azure-resurser](../role-based-access-control/overview.md).

Mer information om autentisering med prenumerations nycklar, åtkomsttoken och Azure Active Directory (AAD) finns i <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">autentisera begär anden till Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Miljövariabler och program konfiguration

Miljövariabler är namn/värde-par som lagras i en speciell miljö. Ett säkrare alternativ till att använda hårdkodad-värden för känsliga data är att använda miljövariabler. Hårdkodad-värden är osäkra och bör undvikas.

> [!CAUTION]
> Använd **inte** hårdkodad-värden för känsliga data, så det är en stor säkerhets risk.

> [!NOTE]
> Även om miljövariabler lagras i oformaterad text, är de isolerade i en miljö. Om en miljö komprometteras, så är det också variablerna i miljön.

### <a name="set-environment-variable"></a>Ange miljö variabel

Om du vill ange miljövariabler använder du ett av följande kommandon – där `ENVIRONMENT_VARIABLE_KEY` är den namngivna nyckeln och `value` är det värde som lagras i miljö variabeln.

# <a name="command-line"></a>[Kommando rad](#tab/command-line)

Skapa och tilldela en beständig miljö variabel, med hänsyn till värdet.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Läs miljö variabeln i en ny instans av **kommando tolken**.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Skapa och tilldela en beständig miljö variabel, med hänsyn till värdet.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Läs miljö variabeln i en ny instans av **Windows PowerShell**.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Skapa och tilldela en beständig miljö variabel, med hänsyn till värdet.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

Läs miljö variabeln i en ny instans av **bash**.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> När du har angett en miljö variabel startar du om din Integrated Development Environment (IDE) för att se till att de nyligen tillagda miljövariablerna är tillgängliga.

### <a name="get-environment-variable"></a>Hämta miljö variabel

Om du vill hämta en miljö variabel måste den läsas i minnet. Beroende på vilket språk du använder bör du tänka på följande kodfragment. Dessa kodfragment visar hur du hämtar miljövariabeln `ENVIRONMENT_VARIABLE_KEY` och tilldelar den till en variabel med namnet `value` .

# <a name="c"></a>[C#](#tab/csharp)

Mer information finns i <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` </a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Mer information finns i <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` </a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Mer information finns i <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` </a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Mer information finns i <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Mer information finns i <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Mer information finns i <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Customer Lockbox

[Customer lockbox för Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) ger ett gränssnitt för kunder som kan granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Den används i fall där en Microsoft-tekniker behöver åtkomst till kundinformation under en supportförfrågan. Information om hur Customer Lockbox begär Anden initieras, spåras och lagras för senare granskningar och granskningar finns i [Customer lockbox](../security/fundamentals/customer-lockbox-overview.md). 

Customer Lockbox är tillgängligt för den här kognitiva tjänsten:

* Översättare

För följande tjänster kommer Microsoft-tekniker inte att komma åt någon kund information på E0-nivån: 

* Language Understanding
* Ansikte
* Content Moderator
* Personanpassning

> [!IMPORTANT]
> För **formulär igenkänning** kommer Microsoft-tekniker inte att komma åt någon kund information i resurser som skapats efter den 10 juli 2020.

För att begära möjlighet att använda E0 SKU, fyller du i och skickar det här [formuläret för begäran](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda E0 SKU med LUIS måste du skapa en ny resurs från Azure Portal och välja E0 som pris nivå. Användarna kommer inte att kunna uppgradera från F0 till den nya E0 SKU: n.

Tal tjänsten har för närvarande inte stöd för Customer Lockbox. Kund information kan dock lagras med hjälp av din egen lagring (BYOS), så att du kan uppnå liknande data kontroller för Customer Lockbox. Tänk på att tal tjänst data är kvar och bearbetas i den region där tal resursen skapades. Detta gäller för alla data i vila och data under överföring. När du använder anpassnings funktioner som Custom Speech och anpassad röst överförs all kund information, lagras och bearbetas i samma region där din BYOS (om den används) och en röst tjänst resurs finns.

> [!IMPORTANT]
> Microsoft använder **inte** kund information för att förbättra sina tal modeller. Om slut punkts loggningen är inaktive rad och inga anpassningar används, lagras även inga kund uppgifter. 

## <a name="next-steps"></a>Nästa steg

* Utforska de olika [Cognitive Services](./what-are-cognitive-services.md)
* Läs mer om [Cognitive Services virtuella nätverk](cognitive-services-virtual-networks.md)