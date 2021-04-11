---
title: Dokumentations översikt över Microsoft Security code Analysis
description: Lär dig mer om Microsoft Security code Analysis-tillägget. Med det här tillägget kan du lägga till säkerhets kod analyser i Azure DevOps CI/ID pipelines.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801204"
---
# <a name="about-microsoft-security-code-analysis"></a>Om Microsofts säkerhets kod analys

> [!Note]
> Från och med den 1 mars 2022 kommer tillägget Microsoft Security code Analysis (MSCA) att dras tillbaka. Befintliga MSCA-kunder behåller sin åtkomst till MSCA till och med den 1 mars 2022. Mer information om alternativa alternativ i Azure-DevOps finns i [OWASP för käll kods analys](https://owasp.org/www-community/Source_Code_Analysis_Tools) . För att kunderna ska kunna migrera till GitHub kan du kolla [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Med tillägget Microsoft Security code Analysis kan teamen lägga till säkerhets kod analyser till sina pipeliner för Azure DevOps-kontinuerlig integrering och leverans (CI/CD). Den här analysen rekommenderas av [sdl-experter (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) på Microsoft.

Ett konsekvent UX fören klar säkerheten genom att dölja komplexiteten med att köra verktyg. Med NuGet-baserad leverans av verktygen behöver team inte längre hantera installationen eller uppdateringen av verktyg. Med både kommando rads-och Basic-gränssnitt för att bygga uppgifter kan alla användare ha så mycket kontroll över verktygen som de vill.

Team kan också använda kraftfulla postprocessing-funktioner som:

- Publicerings loggar för kvarhållning.
- Skapa åtgärds bara rapporter som fokuserar på utvecklare.
- Konfigurera Bygg raster vid Regressions test.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Varför ska jag använda Microsoft Security code Analysis?

### <a name="security-simplified"></a>Förenklad säkerhet

Att lägga till Microsofts säkerhets kod analys verktyg i din Azure DevOps-pipeline är lika enkelt som att lägga till nya aktiviteter. Anpassa uppgifterna eller använd deras standard beteende. Aktiviteterna körs som en del av din Azure DevOps-pipeline och genererar loggar som beskriver många typer av resultat.

### <a name="clean-builds"></a>Rensa versioner

När du har åtgärdat de första problemen som har rapporter ATS av verktygen kan du konfigurera tillägget att avbryta versioner vid nya problem. Det är enkelt att konfigurera kontinuerliga integrerings versioner på varje pull-begäran.

### <a name="set-it-and-forget-it"></a>Ange den och Glöm den

Som standard är build-aktiviteterna och verktygen aktuella. Om det finns en uppdaterad version av ett verktyg behöver du inte ladda ned och installera det. Tillägget tar hand om uppdateringen.

### <a name="under-the-hood"></a>Under huven

Tilläggets build-uppgifter döljer de komplexa funktionerna i:
  - Kör verktyg för statisk analys av säkerhet.
  - Bearbetning av resultaten från loggfiler för att skapa en sammanfattnings rapport eller bryta skapandet.

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft Security code Analysis Tool-uppsättning

Med tillägget Microsoft Security code Analysis kan du enkelt komma åt de senaste versionerna av viktiga analys verktyg. Tillägget innehåller både Microsoft-hanterade verktyg och verktyg med öppen källkod.

De här verktygen laddas ned automatiskt till den molnbaserade agenten efter att du har använt motsvarande build-uppgift för att konfigurera och köra pipelinen.

I det här avsnittet visas en uppsättning verktyg som för närvarande är tillgängliga i tillägget. Titta efter ytterligare verktyg. Skicka även förslag på de verktyg som vi vill lägga till.

### <a name="anti-malware-scanner"></a>Skanner mot skadlig kod

Den program vara som skapas av program vara mot skadlig kod ingår nu i tillägget Microsoft Security code analysis. Den här uppgiften måste köras på en build-agent där Windows Defender redan är installerat. Mer information finns på webbplatsen för [Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim är en förenklad körbar fil (PE) som validerar kompilator inställningar, länkar inställningar och andra säkerhetsrelaterade egenskaper för binära filer. Den här bygg aktiviteten innehåller ett kommando rads gränssnitt runt binskim.exe-konsolprogram. BinSkim är ett verktyg med öppen källkod. Mer information finns i [BinSkim på GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skanner för autentiseringsuppgifter

Lösen ord och andra hemligheter lagrade i käll koden är ett betydande problem. Autentisering av autentiseringsuppgifter är ett eget statiskt analys verktyg som hjälper dig att lösa det här problemet. Verktyget identifierar autentiseringsuppgifter, hemligheter, certifikat och annat känsligt innehåll i din käll kod och dina build-utdata.

### <a name="roslyn-analyzers"></a>Roslyn-analysverktyg

Roslyn-analyser är Microsofts Compilation-integrerade verktyg för statisk analys av hanterade C# och Visual Basic kod. Mer information finns i [Roslyn-baserade analyser](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

TSLint är ett utöknings Bart verktyg för statisk analys som kontrollerar TypeScript kod för läsbarhet, hanterbarhet och fel i funktioner. Det stöds i stor utsträckning av moderna redigerare och build-system. Du kan anpassa den med dina egna luddfria regler, konfigurationer och formaterade. TSLint är ett verktyg med öppen källkod. Mer information finns i [TSLint på GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analys och efter bearbetning av resultat

Microsoft Security code Analysis-tillägget innehåller också tre postprocessing-uppgifter. Dessa uppgifter hjälper dig att analysera resultaten som finns i säkerhets verktygets aktiviteter. När de läggs till i en pipeline följer dessa uppgifter vanligt vis alla andra verktygs uppgifter.

### <a name="publish-security-analysis-logs"></a>Publicera säkerhets analys loggar

Med build-aktiviteten publicera säkerhets analys loggar bevaras loggfilerna för de säkerhets verktyg som körs under bygget. Du kan läsa dessa loggar för undersökning och uppföljning.

Du kan publicera loggfilerna till Azure-artefakter som en. zip-fil. Du kan också kopiera dem till en tillgänglig fil resurs från din privata build-agent.

### <a name="security-report"></a>Säkerhets rapport

Säkerhets rapportens Bygg-aktivitet tolkar loggfilerna. De här filerna skapas av de säkerhets verktyg som körs under bygget. Bygg aktiviteten skapar sedan en enda sammanfattande rapport fil. Den här filen visar alla problem som hittats av analys verktygen.

Du kan konfigurera den här uppgiften för att rapportera resultat för vissa verktyg eller för alla verktyg. Du kan också välja vilken ärende nivå som ska rapporteras, som endast fel eller både fel och varningar.

### <a name="post-analysis-build-break"></a>Efter analys (Bygg rast)

Med bygge-aktiviteten efter analys kan du mata in en Bygg rast som gör att en version av en version inte kan köras. Du matar in en Bygg rast om ett eller flera analys verktyg rapporterar problem i koden.

Du kan konfigurera den här uppgiften för att avbryta skapandet av problem som upptäckts av specifika verktyg eller alla verktyg. Du kan också konfigurera det baserat på allvarlighets graden för problem som hittas, till exempel fel eller varningar.

>[!NOTE]
>Enligt design slutförs varje versions uppgift om uppgiften har slutförts. Detta är sant om ett verktyg hittar problem, så att versionen kan köras för att slutföras genom att tillåta att alla verktyg körs.

## <a name="next-steps"></a>Nästa steg

Instruktioner för hur du installerar och installerar Microsofts säkerhets kod analys finns i vår [onboarding-och installations guide](security-code-analysis-onboard.md).

Mer information om hur du konfigurerar build-aktiviteter finns i vår [konfigurations guide](security-code-analysis-customize.md) eller i [konfigurations guiden för yaml](yaml-configuration.md).

Om du har fler frågor om tillägget och de verktyg som finns kan du kolla in vår [FAQ-sida](security-code-analysis-faq.md).