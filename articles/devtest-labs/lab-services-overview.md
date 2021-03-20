---
title: Azure Lab Services jämfört med Azure DevTest Labs
description: Jämför Azure DevTest Labs och Azure Lab Services.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: b1cd476faf6c457033ffeace03cd2e37b51e8578
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480090"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Jämför Azure DevTest Labs och Azure Lab Services
Det finns två tjänster på Azure som gör att du kan konfigurera labb miljöer i molnet. 

- **Azure DevTest Labs** – med den här tjänsten kan du snabbt konfigurera en miljö för ditt team (till exempel: utvecklings miljö eller test miljö i molnet). En labbägare skapar ett labb, etablerar Windows- eller Linux-datorer, installerar nödvändig programvara och verktyg och gör dem tillgängliga för labbanvändare. Labb användare ansluter till virtuella datorer (VM) i labbet och använder dem för sitt dagliga arbete, kortsiktiga projekt. När användarna börjar använda resurser i labbet, kan en labbadministratör analysera kostnaden och användningen för flera labb och ange övergripande principer för att optimera din organisations eller ditt teams kostnader.
- **Azure Lab Services** – med den här tjänsten kan du skapa hanterade labb typer. För närvarande är klass rums labben den enda typen av hanterade labb som stöds av Azure Lab Services. Tjänsten hanterar all infrastrukturhantering för en hanterad labbtyp, från att skapa virtuella datorer till att hantera fel och skala infrastrukturen. När en IT-administratör har skapat ett labb konto i Azure Lab Services kan en lärare snabbt konfigurera ett labb för sin klass, ange antal och typ av virtuella datorer som behöver övningar i klassen och lägga till användare i klassen. När en användare har registrerat sig för klassen, kan användaren komma åt den virtuella datorn för att göra övningar för klassen.  

## <a name="key-capabilities"></a>De viktigaste funktionerna

Dessa tjänster (Azure DevTest Labs och Azure Lab Services) har stöd för följande viktiga funktioner/funktioner:

- **Snabb och flexibel konfiguration av ett labb**. Med Azure Lab Services kan labbägare snabbt konfigurera ett labb efter behov. Tjänsten erbjuder möjligheten till full hantering av allt Azure-infrastrukturarbete för hanterade labbtyper, eller så kan labbägarna själva hantera och anpassa infrastrukturen inom ramarna för labbägarens prenumeration. Tjänsten tillhandahåller inbyggd skalning och motståndskraftig infrastruktur för labb som tjänsten hanterar åt dig.
- **Förenklad upplevelse för labbanvändare**. I en hanterad labbtyp, till exempel ett klassrumslabb, kan labbanvändarna registrera sig för ett labb med en registreringskod och få åtkomst till labbet när som helst för att använda labbresurserna. I ett labb som skapats i DevTest Labs, kan labbägaren ge behörighet till labbanvändarna för att skapa och få åtkomst till virtuella datorer, hantera och återanvända datadiskar och konfigurera återanvändbara hemligheter.  
- **Kostnadsoptimering och analys**. En labbägare kan ställa in labbscheman för automatisk start och avstängning av virtuella datorer. Labbägaren kan ställa in ett schema för att specificera de tider när labbets virtuella datorer ska vara tillgängliga för användarna, ställa in användningsprinciper per användare eller per labb för att optimera kostnaden och analysera användnings- och aktivitetstrender för ett labb. För hanterade labbtyper, till exempel klassrumslabb, finns för närvarande en mindre deluppsättning av alternativ för kostnadsoptimering och -analys tillgängliga.
- **Inbäddad säkerhet**. En labbägare kan konfigurera ett privat virtuellt nätverk och ett undernät för ett labb och aktivera en delad offentlig IP-adress. Labbanvändarna får säker åtkomst till resurserna via det virtuella nätverk som konfigurerats med ExpressRoute eller VPN för plats-till-plats (för närvarande tillgängligt endast i DevTest Labs).
- **Integration i dina arbetsflöden och verktyg**. Med Azure Lab Services kan du integrera labb i din organisations webbplats och hanteringssystem. Du kan automatiskt etablera miljöer inifrån dina verktyg för kontinuerlig integration/kontinuerlig distribution (för närvarande tillgängligt endast i DevTest Labs).

## <a name="scenarios"></a>Scenarier

Här följer några av de scenarier som Azure DevTest Labs och Azure Lab Services support:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Konfigurera ett ändringsbart labb i molnet för ditt klassrum  

- Skapa ett hanterat klassrumslabb. Du berättar bara för tjänsten exakt vad du behöver, så skapar och hanterar den labbets infrastruktur åt dig så att du kan fokusera på att undervisa din klass istället för att lägga tid på labbets tekniska detaljer.
- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för skolarbete.  
- Flytta din skolas fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet.
- Ta bort labbet med en enda klickning när du är klar.

### <a name="use-devtest-labs-for-development-environments"></a>Använda DevTest Labs för utvecklingsmiljöer

Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för utvecklingsdatorer för utvecklare. I det här scenariot har DevTest Labs följande fördelar:

- Ger utvecklare möjligheten att snabbt etablera sina utvecklingsdatorer på begäran.
- Etablerar Windows- och Linus-miljöer med återanvändbara mallar och artefakter.
- Utvecklare kan enkelt anpassa sina utvecklingsdatorer när så behövs.
- Administratörer kan kontrollera kostnaderna genom att säkerställa att utvecklare inte kan få fler virtuella datorer än de behöver för utvecklingen och att de stängs av när inte används.

Mer information finns i [Använda DevTest Labs för utveckling](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Använda DevTest Labs för testmiljöer

Du kan använda Azure DevTest Labs för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för datorer för testare. I det här scenariot har DevTest Labs följande fördelar:

- Testarna kan testa den senaste versionen av sitt program genom att snabbt etablera Windows- och Linux-miljöer med återanvändningsbara mallar och artefakter.
- Testarna kan skala upp sin belastningstestning genom att etablera flera testagenter.
- Administratörerna kan kontrollera kostnaderna genom att säkerställa att testarna inte kan få fler virtuella datorer än vad de behöver för testningen och att de stängs av när inte används.

Mer information finns i [Använda DevTest Labs för testning](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Typer av labb
Du kan skapa två typer av labb: **hanterade labbtyper** med Azure Lab Services och **labb** med Azure Lab Services. Om du bara vill ange precis vad du behöver i ett labb och låta tjänsten konfigurera och hantera den infrastruktur som krävs för labbet väljer du något av de **hanterade labbtyperna**. För närvarande är **klassrumslabb** den enda hanterade labbtypen som du kan skapa med Azure Lab Services. Om du vill hantera din egen infrastruktur skapar du ett labb med hjälp av **Azure DevTest Labs**.

Följande avsnitt innehåller mer information om dessa labb. 

## <a name="managed-lab-types"></a>Hanterade labbtyper
Med Azure Lab Services kan du skapa laboratorier vars infrastruktur hanteras av Azure. I den här artikeln kallas de för hanterade labbtyper. Hanterade labbtyper erbjuder olika typer av labb som passar dina specifika behov. Den enda hanterade labbtypen som stöds för närvarande är **klassrumslabb**. 

Med hanterade labbtyper kommer du igång direkt med minimal konfigurering. Tjänsten hanterar all infrastrukturhantering för labbet, från att skapa virtuella datorer till att hantera fel och skala infrastrukturen. För att kunna skapa en hanterad labbtyp, till exempel ett klassrumslabb, måste du först skapa ett labbkonto för din organisation. Labbkontot fungerar som det centrala kontot där alla labb i organisationen hanteras. 

När du skapar och använder Azure-resurser i dessa hanterade labbtyper skapar och hanterar tjänsten resurser i interna Microsoft-prenumerationer. De skapas inte i din egen Azure-prenumeration. Tjänsten håller reda på användningen av dessa resurser i interna Microsoft-prenumerationer. Denna användning faktureras tillbaka till din Azure-prenumeration som innehåller labbkontot.   

Här följer några av **användningsfallen för hanterade labbtyper**: 

- Förse eleverna med ett labb med virtuella datorer som är konfigurerat med exakt det som behövs för en lektion. Ge varje elev ett begränsat antal timmar för användning av de virtuella datorerna för läxor eller personliga projekt.
- Konfigurera en pool med högpresterande virtuella datorer för att utföra beräkningsintensiv eller grafikintensiv research. Kör de virtuella datorerna efter behov och rensa datorerna när du är klar. 
- Flytta din skolas fysiska datorlabb till molnet. Skala automatiskt antalet virtuella datorer endast efter den maximala användning och de kostnadströsklar som du anger för labbet.  
- Etablera snabbt ett labb med virtuella datorer som värd för en hackathon. Ta bort labbet med en enda klickning när du är klar. 


## <a name="devtest-labs"></a>DevTest Labs
Du kan ha scenarier där du vill hantera hela infrastrukturen och konfigurationen själv, inom ramarna för din egen prenumeration. Om du vill göra det kan du skapa ett labb med Azure DevTest Labs i Azure-portalen. För de här labben behöver du inte skapa ett labbkonto. De här labben visas inte på labbkontot (som finns till för de hanterade labbtyperna).  

Här följer några **användnings fall för att använda DevTest Labs**: 

- Etablera snabbt ett labb med virtuella datorer som värd för en hackathon eller en praktisk session på en konferens. Ta bort labbet med en enda klickning när du är klar. 
- Skapa en pool med virtuella datorer med ditt program och låt ditt team enkelt använda en enda virtuell dator för samtidig testning av ej släppt programvara.  
- Förse utvecklare med virtuella datorer som konfigurerats med alla de verktyg som de behöver. Schemalägg automatisk start och avstängning för att minimera kostnaderna. 
- Skapa vid upprepade tillfällen ett labb med testdatorer som en del av distributionen. Testa dina senaste delar och rensa testdatorerna när du är klar. 
- Konfigurera flera olika olikt konfigurerade virtuella datorer och flera testagenter för skalning och prestandatester. 
- Erbjud utbildningssessioner till dina kunder med ett labb som konfigurerats med den senaste versionen av din produkt. Ge varje kund ett begränsat antal timmar för att använda labbet. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Hanterade labb typer jämfört med DevTest Labs
I följande tabell jämförs två typer av labb som stöds av Azure Lab Services: 

| Funktioner | Hanterade labbtyper | DevTest Labs |
| -------- | ----------------- | ---------- |
| Hantering av Azure-infrastrukturen i labbet |  Hanteras automatiskt av tjänsten | Du hanterar på egen hand  |
| Inbyggd motståndskraft mot infrastruktursproblem | Hanteras automatiskt av tjänsten | Du hanterar på egen hand  |
| Prenumerationshantering | Tjänsten hanterar tilldelning av resurser i Microsoft-prenumerationer som stöder tjänsten. Skalning hanteras automatiskt av tjänsten | Du hanterar det på egen hand i din egen Azure-prenumeration. Ingen automatisk skalning av prenumerationer. |
| Azure Resource Manager-distribution i labbet | Inte tillgängligt | Tillgänglig |

## <a name="next-steps"></a>Nästa steg

Se följande artiklar: 

- [Om Classroom Labs](../lab-services/classroom-labs-overview.md)
- [Om DevTest Labs](devtest-lab-overview.md)
