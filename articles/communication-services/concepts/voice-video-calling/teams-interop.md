---
title: Team som möter samverkan
titleSuffix: An Azure Communication Services concept document
description: Delta i team möten
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 29eafcae9442215e23e80b946fc35314e23100d3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937240"
---
# <a name="teams-interoperability"></a>Samverkan mellan team

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Azure Communication Services kan användas för att bygga anpassade Mötes upplevelser som interagerar med Microsoft Teams. Användare av kommunikations tjänst lösningen (er) kan interagera med team deltagare via röst-, video-och skärm delning.

Med den här samverkan kan du skapa anpassade Azure-program som ansluter användare till Team-möten. Användare av anpassade program behöver inte ha Azure Active Directory identiteter eller team licenser för att uppleva den här funktionen. Detta är perfekt för att föra medarbetare (som kanske är bekanta med grupper) och externa användare (med hjälp av en anpassad program upplevelse) i en sömlös Mötes upplevelse. På så sätt kan du bygga upplevelser som liknar följande:

1. Anställda använder team för att schemalägga ett möte
2. Ditt anpassade kommunikations tjänst program använder Microsoft Graph-API: er för att få åtkomst till Mötes information
3. Mötes information delas med externa användare via ditt anpassade program
4. Externa användare använder ditt anpassade program för att ansluta till Team-mötet (via kommunikations tjänsterna som anropar klient biblioteket)

Hög nivå arkitekturen för det här användnings fallet ser ut så här: 

![Arkitektur för lag/interop](..//media/call-flows/teams-interop.png)

Även om vissa Teams Mötes funktioner, till exempel upphöjt, interaktivt läge, och grupp-rummen bara är tillgängliga för team användare, kommer ditt anpassade program ha åtkomst till programmets kärn funktioner för ljud-, video-och skärm delning.

När en kommunikations tjänst användare ansluter till Teams mötet, visas det visnings namn som angavs via det anropande klient biblioteket för team användare. Kommunikation Services-användaren behandlas annars som en anonym användare i team. Ditt anpassade program bör överväga användarautentisering och andra säkerhets åtgärder för att skydda team möten. Var mindful av säkerheten för att göra det möjligt för anonyma användare att ansluta till möten och använda [teamets säkerhets guide](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) för att konfigurera funktioner som är tillgängliga för anonyma användare.

Kommunikations tjänst användare kan ansluta till schemalagda team möten så länge anonyma kopplingar är aktiverade i [Mötes inställningarna](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Team i offentliga moln (GCC)
Samverkan med Azure Communication Services tillåts inte för team distributioner med hjälp av [Microsoft 365 myndigheter (GCC)](/MicrosoftTeams/plan-for-government-gcc) för tillfället. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anslut din samtalsapp till ett Teams-möte](../../quickstarts/voice-video-calling/get-started-teams-interop.md)