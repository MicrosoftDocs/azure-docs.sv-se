---
title: Team som möter samverkan
titleSuffix: An Azure Communication Services concept document
description: Delta i team möten
author: chpalm
manager: chpalm
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b7d992961fc165ce643bd0d6aba4d8a9bda1d38e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495597"
---
# <a name="teams-interoperability"></a>Samverkan mellan team

> [!IMPORTANT]
> Fyll i [det här formuläret](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)om du vill aktivera/inaktivera [team innehavarens interoperabilitet](../concepts/teams-interop.md).

Azure Communication Services kan användas för att bygga anpassade Mötes upplevelser som interagerar med Microsoft Teams. Användare av kommunikations tjänst lösningen (er) kan interagera med team deltagare via röst-, video-, Chat-och skärm delning.

Med team samverkan kan du skapa anpassade program som ansluter användare till Team-möten. Användare av anpassade program behöver inte ha Azure Active Directory identiteter eller team licenser för att uppleva den här funktionen. Detta är perfekt för att föra medarbetare (som kanske är bekanta med grupper) och externa användare (med hjälp av en anpassad program upplevelse) i en sömlös Mötes upplevelse. Exempel:

1. Anställda använder team för att schemalägga ett möte 
1. Mötes information delas med externa användare via ditt anpassade program.
   * **Använda Graph API** Ditt program för anpassade kommunikations tjänster använder Microsoft Graph-API: er för att komma åt Mötes information som ska delas. 
   * **Använda andra alternativ** Till exempel kan din Mötes länk kopieras från din kalender i Microsoft Teams.
1. Externa användare använder ditt anpassade program för att ansluta till Team-mötet (via kommunikations tjänsterna som anropar och chattar klient bibliotek)

Hög nivå arkitekturen för det här användnings fallet ser ut så här: 

![Arkitektur för lag/interop](./media/call-flows/teams-interop.png)

Även om vissa Teams Mötes funktioner, till exempel upphöjt, interaktivt läge, och grupp-rummen bara är tillgängliga för team användare, kommer ditt anpassade program ha åtkomst till programmets kärn funktioner för ljud, video, chatt och skärm delning. Mötes chatten kommer att vara tillgänglig för din anpassade program användare när de befinner sig i samtalet. De kommer inte att kunna skicka eller ta emot meddelanden före anslutning eller efter att ha lämnat samtalet. 

När en kommunikations tjänst användare ansluter till Teams mötet, visas det visnings namn som angavs via det anropande klient biblioteket för team användare. Kommunikation Services-användaren behandlas annars som en anonym användare i team.  Ditt anpassade program bör överväga användarautentisering och andra säkerhets åtgärder för att skydda team möten. Var mindful av säkerheten för att göra det möjligt för anonyma användare att ansluta till möten och använda [teamets säkerhets guide](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings) för att konfigurera funktioner som är tillgängliga för anonyma användare.

Service Teams lag för kommunikations tjänster är för närvarande i privat för hands version. När det är allmänt tillgängligt behandlas kommunikations tjänst användare som "externa åtkomst användare". Lär dig mer om extern åtkomst i [samtal, chatta och samar beta med personer utanför organisationen i Microsoft Teams](/microsoftteams/communicate-with-users-from-other-organizations).

Kommunikations tjänst användare kan ansluta till schemalagda team möten så länge anonyma kopplingar är aktiverade i [Mötes inställningarna](/microsoftteams/meeting-settings-in-teams).

## <a name="teams-in-government-clouds-gcc"></a>Team i offentliga moln (GCC)
Samverkan med Azure Communication Services är inte kompatibelt med team distributioner med hjälp av [Microsoft 365 offentliga moln (GCC)](/MicrosoftTeams/plan-for-government-gcc) för tillfället. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anslut din samtalsapp till ett Teams-möte](../quickstarts/voice-video-calling/get-started-teams-interop.md)