---
title: Vanliga frågor om dokumentation om Microsoft Security code Analysis
description: Lär dig mer om tillägget Microsoft Security code Analysis genom att granska vanliga frågor och svar.
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
ms.openlocfilehash: 3bff42b9b82104909c79b75c7b1de51c9ddb6e0d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801323"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

> [!Note]
> Från och med den 1 mars 2022 kommer tillägget Microsoft Security code Analysis (MSCA) att dras tillbaka. Befintliga MSCA-kunder behåller sin åtkomst till MSCA till och med den 1 mars 2022. Mer information om alternativa alternativ i Azure-DevOps finns i [OWASP för käll kods analys](https://owasp.org/www-community/Source_Code_Analysis_Tools) . För att kunderna ska kunna migrera till GitHub kan du kolla [GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Har du några frågor? Läs följande vanliga frågor och svar om du vill ha mer information.

## <a name="general-faq"></a>Allmänna vanliga frågor och svar

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Kan jag installera tillägget på min Visual Studio Team Foundation Server-instans i stället för på en Azure DevOps-instans?

Nej. Tillägget är inte tillgängligt för hämtning och installation av Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Måste jag köra Microsofts säkerhets kod analys med min version? 

Kanske. Det beror på typen av analys verktyg. Käll koden kan vara det enda som krävs, eller så kan du behöva bygga utdata.

Till exempel, CredScan (Credential scanner) analyserar filer i mappstrukturen för kod lagrings platsen. På grund av den här analysen kan du köra CredScan och publicera säkerhets analys loggar i en fristående version för att få resultat.

För andra verktyg som BinSkim som analyserar efter build-artefakter krävs versionen först.

### <a name="can-i-break-my-build-when-results-are-found"></a>Kan jag avbryta min version när det finns resultat?

Ja. Du kan införa en Bygg rast när ett verktyg rapporterar ett problem i logg filen. Lägg bara till bygg aktiviteten efter analys och markera kryss rutan för alla verktyg som du vill bryta skapandet för.

I användar gränssnittet för aktiviteten efter analys kan du välja att avbryta versionen när ett verktyg rapporterar antingen fel eller både fel och varningar.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Hur skiljer sig kommando rads argumenten i Azure DevOps från dessa argument i de fristående Skriv bords verktygen? 

För det mesta är Azure DevOps build-aktiviteterna direkta omslutningar runt kommando rads argumenten för säkerhets verktygen. Du kan skicka som argument till en Bygg aktivitet som du vanligt vis skickar till ett kommando rads verktyg.

Märkbara skillnader:

- Verktygen körs från källmappen i agenten $ (build. SourcesDirectory) eller från% BUILD_SOURCESDIRECTORY%. Ett exempel är C:\Agent \_ work\1\s.
- Sökvägar i argumenten kan vara relativa till roten i den käll katalog som anges ovan. Sökvägar kan också vara absoluta. Du får absoluta sökvägar antingen genom att använda Azure DevOps build-variabler eller genom att köra en lokal agent med kända distributions platser för lokala resurser.
- Verktyg tillhandahåller automatiskt en sökväg eller mapp till utdatafilen. Om du anger en utmatnings plats för en build-uppgift ersätts platsen med en sökväg till vår välkända plats för loggar på Build-agenten
- Vissa ytterligare kommando rads argument har ändrats för vissa verktyg. Ett exempel är tillägg eller borttagning av alternativ som garanterar att inget användar gränssnitt startas.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Kan jag köra en build-aktivitet som en skanner för autentiseringsuppgifter över flera databaser i en Azure DevOps-version?

Nej. Det finns inte stöd för att köra säkra utvecklings verktyg över flera databaser i en enda pipeline.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Den utdatafil som jag har angett skapas inte eller så går det inte att hitta den utdatafil som jag har angett

Bygg aktiviteter filtrerar vissa användarindata. För den här frågan är det särskilt att uppdatera platsen för den genererade utdatafilen till en gemensam plats på Build-agenten. Mer information om den här platsen finns i följande frågor.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Var sparas utdatafilerna som genereras av verktygen? 

Bygg aktiviteterna lägger automatiskt till sökvägar till den här välkända platsen på Build-agenten: $ (agent. BuildDirectory) \_ sdt\logs. Eftersom vi är standardiserade på den här platsen har alla team som skapar eller använder kod analys loggar till gång till utdata.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Kan jag köa en build för att köra dessa uppgifter på en värdbaserad build-agent? 

Ja. Alla aktiviteter och verktyg i tillägget kan köras på en värdbaserad build-agent.

>[!NOTE]
> Build-uppgiften för program mot skadlig kod kräver en build-agent med Windows Defender aktiverat. Den värdbaserade Visual Studio 2017 och senare innehåller en sådan agent. Bygg aktiviteten körs inte på den värdbaserade agenten för Visual Studio 2015.
>
> Även om signaturer inte kan uppdateras på dessa agenter bör signaturer alltid vara mindre än tre timmar gamla.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Kan jag köra dessa build-uppgifter som en del av en versions pipeline i stället för en build-pipeline?

I de flesta fall är ja.

Azure DevOps stöder dock inte körning av aktiviteter i versions pipeliner när aktiviteterna publicerar artefakter. Denna bristande support förhindrar att aktiviteten publicera säkerhets analys loggar kan köras i en versions pipeline. Aktiviteten kan i stället Miss lyckas med ett beskrivande fel meddelande.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Varifrån kan du hämta verktygen med Bygg aktiviteterna?

Med build-uppgifter kan du hämta verktygnas NuGet-paket från [Azure DevOps Package Management-flödet](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Bygg uppgifter kan också använda Node Package Manager, som måste förinstalleras på Build-agenten. Ett exempel på en sådan installation är kommandot **NPM install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Vilken funktion installerar tillägget på min Azure DevOps-organisation? 

Vid installationen blir de säkerhets build-uppgifter som tillhandahålls av tillägget tillgängliga för alla användare i din organisation. När du skapar eller redigerar en Azure-pipeline är dessa uppgifter tillgängliga från samlings listan för build-Task. Annars har du ingen påverkan på hur du installerar tillägget i din Azure DevOps-organisation. Installationen ändrar inte några konto inställningar, projekt inställningar eller pipeliner.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Vill du installera tillägget ändra mina befintliga Azure-pipeliner? 

Nej. Om du installerar tillägget blir säkerhets byggen tillgängliga för att lägga till dina pipeliner. Du måste fortfarande lägga till eller uppdatera Bygg definitioner, så att verktygen kan arbeta med din Bygg process.

## <a name="task-specific-faq"></a>Vanliga frågor och svar om aktiviteter

Frågor som är specifika för att bygga uppgifter visas i det här avsnittet.

### <a name="credential-scanner"></a>Skanner för autentiseringsuppgifter

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Vad är vanliga undervisnings scenarier och exempel?

Här följer information om två av de vanligaste undervisnings scenarierna.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>För att utelämna alla förekomster av en viss hemlighet inom den angivna sökvägen

Hash-nyckeln för hemligheten från CredScan-utdatafilen krävs på det sätt som visas i följande exempel.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> Hash-nyckeln genereras av en del av matchning svärdet eller fil innehållet. Eventuell käll kods revision kan ändra hash-nyckeln och inaktivera regeln för att utelämna.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>För att ignorera alla hemligheter i en angiven fil eller för att undertrycka själva hemligheter filen

Fil uttrycket kan vara ett fil namn. Det kan också vara basename delen av en fullständig fil Sök väg eller ett fil namn. Jokertecken stöds inte.

I följande exempel visas hur du döljer filen \<InputPath>\src\JS\lib\angular.js

Exempel på giltiga undertrycks regler:

- \<InputPath>\src\JS\lib\angular.js – förhindrar filen på den angivna sökvägen
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js-undertrycker en fil med samma namn

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "file": "\\files\\AdditonalSearcher.xml", 
        "_justification": "Additional CredScan searcher specific to my team"
    },
    {
        "file": "\\files\\unittest.pfx", 
        "_justification": "Legitimate UT certificate file with private key"
    }
  ]
}
```

>[!WARNING] 
> Alla framtida hemligheter som läggs till i filen ignoreras också automatiskt.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Vilka är rekommenderade rikt linjer för att hantera hemligheter?

Med följande resurser kan du på ett säkert sätt hantera hemligheter och komma åt känslig information i dina program:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Azure AD-Hanterad tjänstidentitet (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Hanterade identiteter i Azure App Service och Azure Functions](../../app-service/overview-managed-identity.md)
 - [AppAuthentication-bibliotek](/dotnet/api/overview/azure/service-to-service-authentication)


Mer information finns i blogg inlägget [Hantera hemligheter på ett säkert sätt i molnet](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Kan jag skriva egna anpassade sökre?

Genomsökning av autentiseringsuppgifter är beroende av en uppsättning innehålls sökre som definieras vanligt vis i buildsearchers.xml-filen. Filen innehåller en matris med serialiserade XML-objekt som representerar ett **ContentSearcher** -objekt. Programmet distribueras med en uppsättning vältestade sökverktyg. Men du kan även implementera egna anpassade sökverktyg.

En innehålls sökre definieras enligt följande:

- **Namn**: namnet på den beskrivande sökmotor som ska användas i utdatafilen för autentiseringsuppgifter. Vi rekommenderar att du använder namngivnings konventionen kamel notation för Sök efter namn.
- **RuleID**: det stabila täckande ID: t för Sök funktionen:
    - En standard sökre för en autentiseringsuppgift-skanner tilldelas ett **RuleID** -värde som CSCAN0010, CSCAN0020 eller CSCAN0030. Den sista siffran är reserverad för att slå samman eller dela upp Sök grupper via reguljära uttryck (regex).
    - **RuleID** -värdet för en anpassad sökning måste ha ett eget namn område. Exempel är CSCAN- \<Namespace\> 0010, CSCAN- \<Namespace\> 0020 och CSCAN- \<Namespace\> 0030.
    - Ett fullständigt kvalificerat Sök namn är kombinationen av ett **RuleID** -värde och ett Sök förnamn. Exempel är CSCAN0010. KeyStoreFiles och CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: regex för fil namns tillägg för att kontrol lera mot Sök funktionen.
- **ContentSearchPatterns**: en matris med strängar som innehåller regex-uttryck som ska matchas. Om inga Sök mönster har definierats returneras alla filer som matchar **ResourceMatchPattern** -värdet.
- **ContentSearchFilters**: en matris med strängar som innehåller regex-instruktioner för att filtrera sökalternativ som är Specific false-identifieringar.
- **MatchDetails**: ett beskrivande meddelande, mildrande instruktioner eller båda för att läggas till för varje matchning av Sök funktionen.
- **Rekommendation**: förslag – fält innehåll för en matchning med hjälp av för snabb rapport formatet.
- **Allvarlighets grad**: ett heltal som visar allvarlighets graden för ett problem. Den högsta allvarlighets graden har värdet 1.

  ![XML som visar installations programmet för autentiseringsuppgifter](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn-analysverktyg

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Vad är vanliga fel när du använder Roslyn för analys av uppgifter?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projektet återställdes med fel Microsoft.NETCore.App-version

Det fullständiga fel meddelandet:

"Fel: projektet återställdes med Microsoft.NETCore.App-version *x. x*, men med aktuella inställningar används version *y. y. y* i stället. Lös problemet genom att kontrol lera att samma inställningar används för återställning och för efterföljande åtgärder, till exempel build eller Publish. Det här problemet kan vanligt vis uppstå om egenskapen RuntimeIdentifier anges under build eller Publish men inte under återställningen. "

Eftersom Roslyn-uppgifter körs som en del av kompileringen måste käll trädet på Build-datorn vara i ett build-tillstånd.

Ett steg mellan dina huvud steg för bygge och Roslyn kan ha gjort att käll trädet har försatts i ett tillstånd som förhindrar att de skapas. Det här extra steget är förmodligen **dotnet.exe publicera**. Försök att duplicera det steg som gör en NuGet återställning precis innan steget Roslyn analys verktyg. Det här dubblerade steget kan sätta tillbaka käll trädet i ett buildable-tillstånd.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe kan inte skapa en analys instans

Det fullständiga fel meddelandet:

"" csc.exe avslutades med felkod 1--en instans av Analyzer *AAAA* kan inte skapas från C: \\ *bbbb*. dll: det gick inte att läsa in filen eller sammansättningen "Microsoft. CodeAnalysis, version =*X.* x, Culture = neutral, PublicKeyToken = 31bf3856ad364e35" eller något av dess beroenden. Det går inte att hitta den angivna filen i systemet. "

Se till att din kompilator stöder Roslyn-analyser. Om du kör kommandot **csc.exe/version** ska du rapportera version svärdet 2,6 eller senare.

Ibland kan en. CSPROJ-fil åsidosätta build-datorns Visual Studio-installation genom att referera till ett paket från Microsoft.Net. compilers. Om du inte tänker använda en angiven version av kompilatorn tar du bort referenser till Microsoft.Net. compilers. Annars kontrollerar du att versionen av det refererade paketet är 2,6 eller senare.

Försök att hämta fel logg Sök vägen, som anges i alternativet **csc.exe/Errorlog** . Alternativet och sökvägen visas i loggen för build-uppgiften Roslyn-analyser. De kan se ut ungefär som **/Errorlog: f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.CSPROJ.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C#-kompilator versionen är inte tillräckligt aktuell

Om du vill hämta de senaste versionerna av C#-kompilatorn går du till [Microsoft.net. compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Kör **csc.exe/version** i kommando tolken för att hämta den installerade versionen. Se till att referera till ett Microsoft.Net. compilers NuGet-paket som är version 2,6 eller senare.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild-och VSBuild-loggar hittas inte

Roslyn analys verktyg behöver fråga Azure DevOps efter MSBuild-loggen från MSBuild-build-uppgiften. Om Analyzer-aktiviteten körs omedelbart efter MSBuild-uppgiften, är loggen ännu inte tillgänglig. Placera andra aktiviteter mellan aktiviteten MSBuild och Roslyn. Exempel på andra uppgifter är BinSkim och antivirus program.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare hjälp är Microsoft Security code Analysis-supporten tillgänglig måndag till fredag från 9:00 till 5:00 PM Pacific, normal tid.

- Onboarding: Läs vår [onboarding-dokumentation](security-code-analysis-onboard.md)
  
- Support: skicka e-post till vårt team på [Microsoft Security code Analysis support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)