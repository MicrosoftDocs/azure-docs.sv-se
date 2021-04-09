---
title: Företagets policy och efterlevnad i Azure DevTest Labs
description: Den här artikeln innehåller vägledning om hur du styr företagets policy och efterlevnad för Azure DevTest Labs infrastruktur.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592355"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Styrning av Azure DevTest Labs infrastruktur – företagets policy och efterlevnad
Den här artikeln innehåller vägledning om hur du styr företagets policy och efterlevnad för Azure DevTest Labs infrastruktur. 

## <a name="public-vs-private-artifact-repository"></a>Offentligt eller privat artefakt lager

### <a name="question"></a>Fråga
När ska en organisation använda ett offentligt artefakt lager eller en privat artefakt lagrings plats i DevTest Labs?

### <a name="answer"></a>Svar
Den [offentliga artefakt lagrings platsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) innehåller en första uppsättning program varu paket som används oftast. Den hjälper till med snabb distribution utan att behöva investera tid för att återskapa vanliga utvecklingsverktyg och tillägg. Du kan välja att distribuera egna privata lagrings platser. Du kan använda en offentlig och en privat databas parallellt. Du kan också välja att inaktivera den offentliga lagrings platsen. Villkoren för att distribuera ett privat lager bör drivas av följande frågor och överväganden:

- Har organisationen ett krav på att ha en licensierad program vara som en del av deras DevTest Labs-erbjudande? Om svaret är ja, ska du skapa ett privat lager.
- Utvecklar organisationen anpassad program vara som tillhandahåller en speciell åtgärd, vilket krävs som en del av den övergripande etablerings processen? Om svaret är ja, ska en privat lagrings plats distribueras.
- Om organisationens styrnings princip kräver isolering, och externa databaser inte är under direkt konfigurations hantering av organisationen, bör ett privat artefakt lager distribueras. Som en del av den här processen kan en ursprunglig kopia av det offentliga lagrings platsen kopieras och integreras med den privata lagrings platsen. Sedan kan den offentliga lagrings platsen inaktive ras så att ingen i organisationen kan komma åt den längre. Den här metoden tvingar alla användare i organisationen att bara ha en enda lagrings plats som godkänts av organisationen och minimerar konfigurations avvikelser.

### <a name="single-repository-or-multiple-repositories"></a>Enkel lagrings plats eller flera databaser 

### <a name="question"></a>Fråga
Bör en organisations plan för en enda lagrings plats eller tillåta flera databaser?

### <a name="answer"></a>Svar
Som en del av din organisations övergripande styrnings-och konfigurations hanterings strategi rekommenderar vi att du använder en central lagrings plats. När du använder flera databaser kan de bli silor av ohanterad program vara under tiden. Med en central lagrings plats kan flera team använda artefakter från den här lagrings platsen för sina projekt. Den tillämpar standardisering, säkerhet, enkel hantering och eliminerar dubbleringen av insatser. Som en del av centralisering är följande åtgärder rekommenderade metoder för långsiktig hantering och hållbarhet:

- Associera Azure-databaser med samma Azure Active Directory klient som Azure-prenumerationen använder för autentisering och auktorisering.
- Skapa en grupp med namnet **alla DevTest Labs-utvecklare** i Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefakt utveckling bör placeras i den här gruppen.
- Samma Azure Active Directorys grupp kan användas för att ge åtkomst till Azure databaser-lagringsplatsen och till labbet.
- I Azure databaser bör branchning eller förgreningar användas för att separera ett utvecklings lager från den primära produktions lagrings platsen. Innehåll läggs bara till i huvud grenen med en pull-begäran efter en korrekt kod granskning. När kod granskaren godkänner ändringen, en lead-utvecklare, som ansvarar för underhåll av huvud grenen, sammanfogar den uppdaterade koden. 

## <a name="corporate-security-policies"></a>Företags säkerhets principer

### <a name="question"></a>Fråga
Hur kan en organisation se till att företagets säkerhets principer är på plats?

### <a name="answer"></a>Svar
En organisation kan uppnå det genom att utföra följande åtgärder:

1. Utveckla och publicera en omfattande säkerhets princip. Principen förbrukar reglerna för acceptabel användning som är associerade med användning av program vara, moln till gångar. Den definierar också vad som tydligt strider mot principen. 
2. Utveckla en anpassad avbildning, anpassade artefakter och en distributions process som tillåter dirigering i säkerhets sfären som har definierats med Active Directory. Den här metoden tillämpar företags gränser och anger en gemensam uppsättning miljö kontroller. Dessa kontroller mot miljön som en utvecklare kan ta hänsyn till när de utvecklar och följer en säker utvecklings livs cykel som en del av den övergripande processen. Målet är också att tillhandahålla en miljö som inte är mer begränsad än vad som kan förhindra utveckling, men en rimlig uppsättning kontroller. Grup principerna på organisationsenheten (OU) som innehåller virtuella labb datorer kan vara en delmängd av de totala grup principer som finns i produktionen. De kan också vara ytterligare en uppsättning för att korrekt minimera eventuella identifierade risker.

## <a name="data-integrity"></a>Dataintegritet

### <a name="question"></a>Fråga
Hur kan en organisation säkerställa data integriteten för att säkerställa att fjärrutvecklare inte kan ta bort kod eller införa skadlig kod eller program vara som inte godkänts?

### <a name="answer"></a>Svar
Det finns flera lager med kontroll för att minimera hotet från externa konsulter, entreprenörer eller anställda som är fjärran på att samar beta i DevTest Labs. 

Som tidigare nämnts måste det första steget ha ett acceptable use policy som har skapats och definierats som tydligt beskriver konsekvenserna när någon strider mot principen. 

Det första lagret med kontroller för fjärråtkomst är att tillämpa en fjärråtkomstprincip via en VPN-anslutning som inte är direkt ansluten till labbet. 

Det andra lagret med kontroller är att använda en uppsättning grup princip objekt som förhindrar kopiering och inklistring via fjärr skrivbord. En nätverks princip kan implementeras för att inte tillåta utgående tjänster från miljön, till exempel FTP-och RDP-tjänster från miljön. Användardefinierad routning kan tvinga all Azure-nätverkstrafik tillbaka till lokalt, men routningen kunde inte ta hänsyn till alla URL: er som kan möjliggöra överföring av data om du inte styrs via en proxy för att genomsöka innehåll och sessioner. Offentliga IP-adresser kan begränsas inom det virtuella nätverk som stöder DevTest Labs för att inte tillåta bryggning av en extern nätverks resurs.

Slutligen måste samma typ av begränsningar tillämpas i hela organisationen, vilket även skulle behöva ta hänsyn till alla möjliga metoder för flyttbara media eller externa URL: er som kan ta emot en post av innehållet. Kontakta din säkerhets personal för att granska och implementera en säkerhets princip. Fler rekommendationer finns i [Microsoft cyberhot Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Nästa steg
Se [migrering och integrering av program](devtest-lab-guidance-governance-application-migration-integration.md).
