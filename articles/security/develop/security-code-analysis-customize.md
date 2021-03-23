---
title: Anpassa Microsoft Security code Analysis-uppgifter
titleSuffix: Azure
description: Den här artikeln beskriver hur du anpassar aktiviteterna i tillägget Microsoft Security code Analysis
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
ms.openlocfilehash: ad395e1b782edb28845bb7db0607d2bab5b5697c
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802020"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurera och anpassa Bygg aktiviteterna

> [!Note]
> Från och med den 1 mars 2022 kommer tillägget Microsoft Security code Analysis (MSCA) att dras tillbaka. Befintliga MSCA-kunder behåller sin åtkomst till MSCA till och med den 1 mars 2022. Mer information om alternativa alternativ i Azure-DevOps finns i [OWASP för käll kods analys](https://owasp.org/www-community/Source_Code_Analysis_Tools) . För att kunderna ska kunna migrera till GitHub kan du kolla [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

I den här artikeln beskrivs de konfigurations alternativ som finns tillgängliga i de olika bygg aktiviteterna. Artikeln börjar med uppgifterna för analys verktyg för säkerhets kod. Den avslutas med uppgifterna efter bearbetningen.

## <a name="anti-malware-scanner-task"></a>Aktivitet för skanner för program mot skadlig kod

>[!NOTE]
> Build-uppgiften för program mot skadlig kod kräver en build-agent med Windows Defender aktiverat. Den värdbaserade Visual Studio 2017 och senare innehåller en sådan agent. Bygg aktiviteten körs inte på den värdbaserade agenten för Visual Studio 2015.
>
> Även om signaturer inte kan uppdateras på dessa agenter bör signaturer alltid vara mindre än tre timmar gamla.

Information om aktivitets konfigurationen visas i följande skärm bild och text.

![Konfigurera aktiviteten för att bygga program vara mot skadlig kod](./media/security-tools/5-add-anti-malware-task600.png)

I list rutan **typ** i skärm bilden är **Basic** valt. Välj **anpassad** om du vill ange kommando rads argument som anpassar sökningen.

Windows Defender använder Windows Update-klienten för att ladda ned och installera signaturer. Om det inte går att uppdatera signaturen på din build-agent kommer **HRESULT** -felkoden troligen från Windows Update.

Mer information om Windows Update fel och deras lösningar finns i [Windows Update felkoder per komponent](/windows/deployment/update/windows-update-error-reference) och TechNet [-artikeln Windows Update Agent-felkoder](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du [alternativen för yaml för program mot skadlig kod](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim-uppgift

> [!NOTE]
> Innan du kan köra BinSkim-uppgiften måste din version uppfylla något av följande villkor:
>  - Din version genererar binära artefakter från hanterad kod.
>  - Du har binära artefakter som du vill analysera med BinSkim.

Information om aktivitets konfigurationen visas i följande skärm bild och lista.

![Konfigurera BinSkim build-uppgiften](./media/security-tools/7-binskim-task-details.png)

- Ange att build-konfigurationen ska felsöka så att. pdb debug-filer skapas. BinSkim använder dessa filer för att mappa problem i binärfiler för utdata tillbaka till käll koden.
- Undvik att söka och skapa en egen kommando rad:
     - I listan **typ** väljer du **Basic**.
     - I **funktions** listan väljer du **analysera**.
- I **mål** anger du en eller flera specificerare för en fil, en katalog eller ett filter mönster. Dessa specificerare matchar en eller flera binärfiler som ska analyseras:
    - Flera angivna mål måste avgränsas med ett semikolon (;).
    - En specificerare kan vara en enskild fil eller innehålla jokertecken.
    - Directory-specifikationer måste alltid avslutas med \\ *.
    - Exempel:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Om du väljer **kommando rad** i listan **typ** måste du köra binskim.exe:
     - Kontrol lera att de första argumenten binskim.exe är verbet som **analyseras** följt av en eller flera Sök vägs specifikationer. Varje sökväg kan antingen vara en fullständig sökväg eller en sökväg i förhållande till käll katalogen.
     - Flera mål Sök vägar måste avgränsas med blank steg.
     - Du kan utelämna alternativet **/o** eller **/output** . Värdet för utdata läggs till för dig eller ersätts.
     - Standard kommando rads konfigurationerna visas på följande sätt.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> Efterföljande \\ * är viktigt om du anger kataloger för målet.

Mer information om kommando rads argument för BinSkim, regler per ID eller avslutnings koder finns i [användar handboken för BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du våra [BINSKIM yaml-alternativ](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Uppgift för genomsökning av autentiseringsuppgifter

Information om aktivitets konfigurationen visas i följande skärm bild och lista.

![Konfigurera uppgiften för att läsa in autentiseringsuppgifter](./media/security-tools/3-taskdetails.png)

Tillgängliga alternativ inkluderar:
  - **Visnings namn**: namnet på Azure DevOps-aktiviteten. Standardvärdet är skanner för att köra autentiseringsuppgifter
  - **Verktygets huvud version**: tillgängliga värden är **CredScan v2**, **CredScan v1**. Vi rekommenderar att kunderna använder **CredScan v2** -versionen.
  - **Utdataformat**: tillgängliga värden är **TSV**, **CSV**, **SARIF** och för **snabbt**.
  - **Verktygs version**: Vi rekommenderar att du väljer **senaste**.
  - **Genomsök mapp**: den databasmapp som ska genomsökas.
  - **Filtypen sökvägar**: alternativen för att hitta Sök efter-filen som används för genomsökning.
  - **Undertrycks fil**: en [JSON](https://json.org/) -fil kan förhindra problem i utgående loggen. Mer information om under trycknings scenarier finns i avsnittet Vanliga frågor och svar i den här artikeln.
  - **Utförlig utdata**: själv för klar Ande.
  - **Batchstorlek**: antalet samtidiga trådar som används för att köra skannern för autentiseringsuppgifter. Standardvärdet är 20. Möjliga värden är mellan 1 och 2 147 483 647.
  - **Matchnings-timeout**: hur lång tid i sekunder det tar att försöka lägga till en Sök funktion innan kontrollen avbryts.
  - **Fil genomsökningens Läs buffertstorlek**: storleken i byte på den buffert som används när innehållet läses. Standardvärdet är 524 288.  
  - **Maximal läsning av fil läsnings byte**: det maximala antalet byte som ska läsas från en fil under innehålls analysen. Standardvärdet är 104 857 600.
  - **Kontroll alternativ**  >  **Kör den här uppgiften**: anger när aktiviteten ska köras. Välj **anpassade villkor** för att ange mer komplexa villkor.
  - **Version**: Bygg uppgifts versionen i Azure DevOps. Det här alternativet används inte ofta.

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du [alternativen för yaml för autentiseringsuppgifter](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Roslyn för analys uppgifter

> [!NOTE]
> Innan du kan köra Roslyn-uppgiften måste din version uppfylla följande villkor:
>
> - Din build-definition innehåller den inbyggda MSBuild-eller VSBuild build-uppgiften för att kompilera C# eller Visual Basic kod. Aktiviteten analyserare använder sig av indata och utdata för den inbyggda uppgiften för att köra MSBuild-kompilering med Roslyn-analyserare aktiverade.
> - Build-agenten som kör den här bygg aktiviteten har Visual Studio 2017 version 15,5 eller senare installerad, så att den använder kompilator version 2,6 eller senare.

Information om aktivitets konfigurationen visas i följande lista och anmärkning.

Tillgängliga alternativ inkluderar:

- **Ruleset**: värden är **sdl krävs**, **sdl rekommenderas** eller din egen anpassade regel uppsättning.
- **Versions analys**: Vi rekommenderar att du väljer **senaste**.
- **Kompilator varningar ignorerar fil**: en textfil med en lista över varnings-ID: n som ignoreras.
- **Kontroll alternativ**  >  **Kör den här uppgiften**: anger när aktiviteten ska köras. Välj **anpassade villkor** för att ange mer komplexa villkor.

> [!NOTE]
>
> - Roslyn-analyser är integrerade med kompilatorn och kan endast köras som en del av csc.exe kompilering. Den här uppgiften kräver därför kommandot compile som kördes tidigare i versionen för att kunna spelas upp eller köras igen. Den här omuppspelningen eller körningen görs genom att skicka en fråga till Azure-DevOps (tidigare Visual Studio Team Services) för att skapa uppgifts loggar för MSBuild.
>
>   Det finns inget annat sätt för uppgiften att på ett tillförlitligt sätt Hämta kommando raden för MSBuild-kompilering från build-definitionen. Vi ansåg att du lägger till en text ruta för fri hands text så att användarna kan ange sina kommando rader. Men det skulle vara svårt att hålla dessa kommando rader aktuella och synkroniserade med huvud versionen.
>
>   Anpassade versioner kräver uppspelning av hela uppsättningen kommandon, inte bara compile-kommandon. I dessa fall är det inte enkelt eller tillförlitligt att aktivera Roslyn-analyser.
>
> - Roslyn-analyser är integrerade med kompileraren. Roslyn-analyserare kräver kompilering för att anropas.
>
>   Den nya bygg uppgiften implementeras genom att kompilera om C#-projekt som redan har skapats. Den nya aktiviteten använder bara MSBuild-och VSBuild-åtgärderna i samma build-eller build-definition som den ursprungliga aktiviteten. I det här fallet använder den nya uppgiften dock dem med Roslyn-analyserare aktiverade.
>
>   Om den nya aktiviteten körs på samma agent som den ursprungliga aktiviteten, skriver den nya aktivitetens utdata över den ursprungliga aktivitetens utdata i mappen *s* sources. Även om bygg resultatet är detsamma, rekommenderar vi att du kör MSBuild, kopierar utdata till den fristående katalogen för artefakter och kör sedan Roslyn-analyserare.

Mer resurser för Roslyn-uppgiften finns i [Roslyn-baserade analyser](/dotnet/standard/analyzers/api-analyzer) för Microsoft docs.

Du hittar det Analyzer-paket som har installerats och använts av den här bygg aktiviteten på NuGet-sidan [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Information om YAML-konfigurationen för den här uppgiften finns i våra [Roslyn-yaml alternativ](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint-uppgift

Mer information om TSLint finns i [TSLint GitHub-lagrings platsen](https://github.com/palantir/tslint).

>[!NOTE] 
>Som du kanske är medveten om, kan [TSLint GitHub lagrings platsen](https://github.com/palantir/tslint) start sida säga att TSLint kommer att vara inaktuellt någon gång i 2019. Microsoft undersöker [ESLint](https://github.com/eslint/eslint) som en alternativ uppgift.

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du våra [TSLINT yaml-alternativ](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Aktiviteten publicera säkerhets analys loggar

Information om aktivitets konfigurationen visas i följande skärm bild och lista.

![Konfigurera aktiviteten publicera säkerhets analys loggar](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artefakt namn**: valfri sträng identifierare.
- **Artefakt typ**: beroende på ditt val kan du publicera loggar till din Azure DevOps Server eller till en delad fil som är tillgänglig för build-agenten.
- **Verktyg**: du kan välja att bevara loggar för vissa verktyg, eller så kan du välja **alla verktyg** för att bevara alla loggar.

Information om YAML-konfigurationen för den här aktiviteten finns i [yaml alternativ för publicering av säkerhets loggar](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Säkerhets rapports aktivitet

Information om konfigurationen av säkerhets rapporten visas i följande skärm bild och lista.

![Konfigurera säkerhets rapportens Bygg aktivitet](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Rapporter**: Välj någon av **pipelinen-konsolen**, **TSV-filen** och **HTML-filformat** . En rapport fil skapas för varje valt format.
- **Verktyg**: Välj de verktyg i build-definitionen som du vill ha en sammanfattning av identifierade problem för. För varje valt verktyg kan det finnas ett alternativ för att välja om du bara ska se fel eller Visa både fel och varningar i sammanfattnings rapporten.
- **Avancerade alternativ**: om det inte finns några loggar för något av de valda verktygen kan du välja att logga en varning eller ett fel. Om du loggar ett fel, Miss lyckas uppgiften.
- **Mappen grundläggande loggar**: du kan anpassa mappen grundläggande loggar där loggar ska hittas. Men det här alternativet används vanligt vis inte.

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du [säkerhets rapportens yaml alternativ](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Uppgift efter analys

Information om aktivitets konfigurationen visas i följande skärm bild och lista.

![Konfigurera Bygg aktiviteten efter analys](./media/security-tools/a-post-analysis600.png)

- **Verktyg**: Välj de verktyg i din build-definition som du vill använda för att villkorligt injicera en versions rast. För varje valt verktyg kan det finnas ett alternativ för att välja om du bara vill bryta vid fel eller både fel och varningar.
- **Rapport**: du kan välja att skriva resultaten som orsakar Bygg rastet. Resultaten skrivs till Azure DevOps-konsol fönstret och logg filen.
- **Avancerade alternativ**: om det inte finns några loggar för något av de valda verktygen kan du välja att logga en varning eller ett fel. Om du loggar ett fel, Miss lyckas uppgiften.

Om du vill ha mer information om YAML-konfigurationen för den här aktiviteten kontrollerar du alternativen för att [publicera yaml](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Nästa steg

Information om YAML-baserad konfiguration finns i vår [konfigurations guide för yaml](yaml-configuration.md).

Om du har fler frågor om tillägget för säkerhets kod analys och de verktyg som erbjuds, kan du kolla in [vår vanliga frågor och svar](security-code-analysis-faq.md).