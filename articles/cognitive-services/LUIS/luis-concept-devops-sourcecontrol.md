---
title: Käll kontroll och utvecklings grenar – LUIS
description: Underhålla din Language Understanding-app (LUIS) under käll kontroll. Så här tillämpar du uppdateringar på en LUIS-app när du arbetar i en utvecklings gren.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932527"
---
# <a name="devops-practices-for-luis"></a>DevOps-metoder för LUIS

Program varu tekniker som utvecklar en Language Understanding-app (LUIS) kan tillämpa DevOps-metoder kring [käll kontroll](luis-concept-devops-sourcecontrol.md), [automatiserade versioner](luis-concept-devops-automation.md), [testning](luis-concept-devops-testing.md)och [versions hantering](luis-concept-devops-automation.md#release-management) genom att följa dessa rikt linjer.

## <a name="source-control-and-branch-strategies-for-luis"></a>Källkontroll och förgreningsstrategier för LUIS

En av de viktigaste faktorer som DevOps är beroende av är [käll kontroll](/azure/devops/user-guide/source-control). Ett käll kontroll system gör det möjligt för utvecklare att samar beta med kod och spåra ändringar. Med hjälp av grenar kan utvecklare växla mellan olika versioner av kodbasen och arbeta oberoende av andra medlemmar i teamet. När utvecklare höjer en [pull-begäran](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) för att föreslå uppdateringar från en gren till en annan, eller när ändringar slås samman, kan det vara utlösaren för [automatiserade versioner](luis-concept-devops-automation.md) för att bygga och kontinuerligt testa kod.

Genom att använda koncepten och vägledningen som beskrivs i det här dokumentet kan du utveckla en LUIS-app när du spårar ändringar i ett käll kontroll system och följer dessa metod tips för program varu teknik:

- **Källkontroll**
  - Käll koden för din LUIS-app är i ett format som kan läsas av människa.
  - Modellen kan skapas från källan på ett repeterbart sätt.
  - Käll koden kan hanteras av en käll kods lagrings plats.
  - Autentiseringsuppgifter och hemligheter som till exempel redigerings-och prenumerations nycklar lagras aldrig i käll koden.

- **Förgrening och sammanfogning**
  - Utvecklare kan arbeta från oberoende grenar.
  - Utvecklare kan arbeta i flera grenar samtidigt.
  - Det är möjligt att integrera ändringar i en LUIS-app från en förgrening till en annan genom att basera eller slå samman.
  - Utvecklare kan slå samman en PR till den överordnade grenen.

- **Versionshantering**
  - Varje komponent i ett stort program bör vara versions oberoende, så att utvecklare kan identifiera ändringar eller uppdateringar genom att titta på versions numret.

- **Kod granskningar**
  - Ändringarna i PR visas som läslig källkod som kan granskas innan du accepterar PR.

## <a name="source-control"></a>Källkontroll

Om du vill underhålla [appens schema definition](./app-schema-definition.md) för en Luis-app i ett käll kods hanterings system använder du [LUDown format ( `.lu` )](/azure/bot-service/file-format/bot-builder-lu-file-format)  åter givning av appen. `.lu` formatet är det bästa `.json` formatet eftersom det är läsligt, vilket gör det enklare att göra och granska ändringar i pull.

### <a name="save-a-luis-app-using-the-ludown-format"></a>Spara en LUIS-app med LUDown-formatet

Så här sparar du en LUIS-app i `.lu` formatet och placerar den under käll kontroll:

- ANTINGEN: [Exportera appens version](./luis-how-to-manage-versions.md#other-actions) `.lu` från Luis- [portalen](https://www.luis.ai/) och Lägg till den i din käll kontroll lagrings plats

- ELLER: Använd en text redigerare för att skapa en `.lu` fil för en Luis-app och lägga till den i käll kontrollens lagrings plats

> [!TIP]
> Om du arbetar med JSON-exporten av en LUIS-app kan du [konvertera den till LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert).  Använd `--sort` alternativet för att se till att avsikter och yttranden sorteras alfabetiskt.  
> Observera att **. LU** -export kapaciteten som är inbyggd i Luis-portalen sorterar redan utdata.

### <a name="build-the-luis-app-from-source"></a>Bygg LUIS-appen från källan

För att en LUIS-app ska kunna *bygga från källan* innebär att du kan [skapa en ny version av Luis-appen genom att importera `.lu` källan](./luis-how-to-manage-versions.md#import-version) , för att [träna versionen](./luis-how-to-train.md) och [publicera den](./luis-how-to-publish-app.md). Du kan göra detta i LUIS-portalen eller på kommando raden:

- Använd LUIS-portalen för att importera appens [ `.lu` version](./luis-how-to-manage-versions.md#import-version) från käll kontroll och [träna](./luis-how-to-train.md) och [publicera](./luis-how-to-publish-app.md) appen.

- Använd [kommando rads gränssnittet för bot Framework för Luis](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) på kommando raden eller i ett CI/CD-arbetsflöde för [](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) att importera `.lu` appens version från käll kontroll till ett Luis program och [träna](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) och [publicera](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) appen.

### <a name="files-to-maintain-under-source-control"></a>Filer som ska underhållas under käll kontroll

Följande typer av filer för ditt LUIS-program bör underhållas under käll kontroll:

- `.lu` fil för LUIS-programmet

- [Enhets test definitions filer](luis-concept-devops-testing.md#writing-tests) (yttranden och förväntade resultat)

- [Batch-testfiler](./luis-how-to-batch-test.md#batch-test-file) (yttranden och förväntade resultat) som används för prestanda testning

### <a name="credentials-and-keys-are-not-checked-in"></a>Autentiseringsuppgifter och nycklar är inte incheckade

Ta inte med prenumerations nycklar eller liknande konfidentiella värden i filer som du checkar in på din lagrings platsen där de kan vara synliga för obehöriga personer. De nycklar och andra värden som du bör förhindra från incheckning är:

- LUIS-redigerings-och förutsägelse nycklar
- LUIS-redigering och förutsägelse slut punkter
- Prenumerations nycklar för Azure
- Åtkomsttoken, till exempel token för ett Azure [-tjänstens huvud namn](/cli/azure/ad/sp) som används för Automation-autentisering

#### <a name="strategies-for-securely-managing-secrets"></a>Strategier för säker hantering av hemligheter

Strategier för säker hantering av hemligheter är:

- Om du använder git-versions kontroll kan du lagra körnings hemligheter i en lokal fil och förhindra incheckning av filen genom att lägga till ett mönster som matchar fil namnet i en [. gitignore](https://git-scm.com/docs/gitignore) -fil
- I ett Automation-arbetsflöde kan du lagra hemligheter säkert i parameter konfigurationen som erbjuds av den Automation-tekniken. Om du till exempel använder GitHub- [åtgärder](https://github.com/features/actions)kan du lagra hemligheter på ett säkert sätt i [GitHub hemligheter](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Förgrening och sammanfogning

System för distribuerad versions kontroll som git ger flexibilitet i hur grupp medlemmar publicerar, delar, granskar och itererar om kod ändringar via utvecklings grenar som delas med andra. Anta en [strategi för git-branchningslogik](/azure/devops/repos/git/git-branching-guidance) som passar ditt team.

Vilken förgrenings strategi du antar är att grupp medlemmarna kan arbeta med lösningen i en *funktions gren* oberoende av det arbete som pågår i andra grenar.

För att stödja oberoende arbete i grenar med ett LUIS-projekt:

- **Huvud grenen har en egen LUIS-app.** Den här appen representerar det aktuella läget för din lösning för ditt projekt och dess aktuella aktiva version bör alltid mappas till `.lu` källan som finns i huvud grenen. Alla uppdateringar av `.lu` källan för den här appen bör granskas och testas så att den här appen kan distribueras för att bygga miljöer som produktion när som helst. När uppdateringar till `.lu` är sammanfogade i huvud delen av en funktions gren, bör du skapa en ny version i Luis-appen och lägga [till versions numret](#versioning).

- **Varje funktions gren måste använda sin egen instans av en Luis-app**. Utvecklare arbetar med den här appen i en funktions gren utan risk för att påverka utvecklare som arbetar i andra grenar. Den här "dev Branch"-appen är en arbets kopia som ska tas bort när funktions grenen tas bort.

![Git-funktions gren](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>Utvecklare kan arbeta från oberoende grenar

Utvecklare kan arbeta med uppdateringar i en LUIS-app oberoende av andra grenar genom att:

1. Skapa en funktions gren från huvud grenen (beroende på din gren strategi, vanligt vis main eller utveckla).

1. [Skapa en ny Luis-app i Luis-portalen](./luis-how-to-start-new-app.md) ("*dev Branch app*") så att den endast stöder arbetet i funktions grenen.

   * Om det `.lu` redan finns en källa för lösningen i din gren, eftersom den sparades efter det att jobbet gjort en annan gren tidigare i projektet, skapar du din Luis-app för dev Branch genom att importera `.lu` filen.

   * Om du börjar arbeta med ett nytt projekt har du ännu inte `.lu` källan till din huvudsakliga Luis-app i lagrings platsen. Du skapar `.lu` filen genom att exportera din dev Branch-app från portalen när du har slutfört ditt funktions gren arbete och skicka in det som en del av din PR.

1. Arbeta med den aktiva versionen av din dev Branch-app för att implementera nödvändiga ändringar. Vi rekommenderar att du endast arbetar i en enda version av din dev Branch-app för alla funktions gren arbeten. Om du skapar fler än en version i din dev Branch-app måste du vara noga med att spåra vilken version som innehåller de ändringar du vill checka in när du ökar din PR.

1. Testa uppdateringarna – se [test for Luis DevOps](luis-concept-devops-testing.md) för information om hur du testar din dev Branch-app.

1. Exportera den aktiva versionen av din dev Branch-app som `.lu` från [listan versioner](./luis-how-to-manage-versions.md).

1. Checka in dina uppdateringar och Bjud in peer-granskning av dina uppdateringar. Om du använder GitHub ska du öka en pull- [begäran](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests).

1. När ändringarna har godkänts, sammanfoga uppdateringarna till huvud grenen. Nu ska du skapa en ny [version](./luis-how-to-manage-versions.md) av huvudLUIS-appen med hjälp *av den uppdaterade* `.lu` i huvud gruppen. Se [versions hantering](#versioning) för att tänka på hur du anger versions namnet.

1. När funktions grenen tas bort är det en bra idé att ta bort LUIS-appen för dev Branch som du skapade för funktions gren arbetet.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>Utvecklare kan arbeta i flera grenar samtidigt

Om du följer mönstret som beskrivs ovan i [utvecklare kan arbeta från oberoende grenar](#developers-can-work-from-independent-branches), så använder du ett unikt Luis-program i varje funktions gren. En enda utvecklare kan arbeta i flera grenar samtidigt, så länge de växlar till rätt dev Branch LUIS-app för den gren som de för närvarande arbetar med.

Vi rekommenderar att du använder samma namn för både funktions grenen och för den dev Branch LUIS-app som du skapar för funktions gren arbetet, för att göra det mindre troligt att du av misstag arbetar med fel app.

Som vi nämnt ovan rekommenderar vi att du för enkelhetens skull arbetar i en enda version i varje dev Branch-app. Om du använder flera versioner bör du vara noga med att aktivera rätt version när du växlar mellan dev Branch-appar.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Flera utvecklare kan arbeta på samma gren samtidigt

Du kan ha stöd för flera utvecklare som arbetar med samma funktions gren på samma tid:

- Utvecklare checkar ut samma funktions gren och push-och pull-ändringar som skickats av sig själva och andra utvecklare medan de fungerar, som vanligt.

- Om du följer mönstret som beskrivs ovan i [utvecklare kan arbeta från oberoende grenar](#developers-can-work-from-independent-branches), kommer den här grenen att använda ett unikt Luis-program för att stödja utvecklingen. LUIS-appen för dev Branch kommer att skapas av den första medlemmen i utvecklings teamet som börjar arbeta i funktions grenen.

- [Lägg till team medlemmar som deltagare](./luis-how-to-collaborate.md) i dev Branch Luis-appen.

- När funktions grenen fungerar, exportera den aktiva versionen av dev Branch LUIS-appen som `.lu` från [listan versioner](./luis-how-to-manage-versions.md), spara den uppdaterade `.lu` filen i lagrings platsen och checka in och ta en titt på ändringarna.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Införliva ändringar från en gren till en annan med hjälp av ombase eller sammanfoga

Vissa andra utvecklare på ditt team som arbetar i en annan gren kan ha gjort uppdateringar av `.lu` källan och sammanfoga dem till huvud grenen efter att du har skapat din funktions gren. Du kanske vill införliva sina ändringar i din fungerande version innan du fortsätter att göra egna ändringar i din funktions gren. Du kan göra detta genom [att basera eller slå ihop till main](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) på samma sätt som vilken annan kod till gång som helst. Eftersom LUIS-appen i LUDown-format är läslig, stöder den koppling med standard verktyg för sammanslagning.

Följ de här tipsen om du baserar om LUIS-appen i en funktions gren:

- Innan du baserar eller sammanfogar bör du kontrol lera att din lokala kopia av `.lu` källan för appen har alla dina senaste ändringar som du har tillämpat med Luis-portalen, genom att exportera appen på nytt från portalen först. På så sätt kan du se till att alla ändringar som du har gjort i portalen och inte har exporter ATS än inte går förlorade.

- Under sammanslagningen använder du standard verktyg för att lösa eventuella sammanslagnings konflikter.

- Glöm inte efter ombasering eller sammanslagning är klart för att importera appen tillbaka till portalen igen, så att du arbetar med den uppdaterade appen när du fortsätter att tillämpa dina egna ändringar.

### <a name="merge-prs"></a>Sammanfoga pull

När din PR har godkänts kan du slå samman dina ändringar till huvud grenen. Inga särskilda överväganden gäller för LUDown-källan för en LUIS-app: den är läslig och stöder koppling med standard verktyg för sammanslagning. Eventuella sammanfognings konflikter kan lösas på samma sätt som med andra källfiler.

När din PR har slagits samman rekommenderar vi att du rensar:

- Ta bort grenen i din lagrings platsen

- Ta bort LUIS-appen "dev Branch" som du skapade för funktions gren arbetet.

På samma sätt som med program kod till gångar bör du skriva enhets test för att medfölja LUIS app-uppdateringar. Du bör använda arbets flöden för kontinuerlig integrering för att testa:

- Uppdateringar i en PR innan PR sammanfogas
- Huvud grenen LUIS-appen efter att ett PR har godkänts och ändringarna har slagits samman i huvud.

Mer information om testning av LUIS-DevOps finns i [testa för DevOps för Luis](luis-concept-devops-testing.md). Mer information om hur du implementerar arbets flöden finns i [Automation-arbetsflöden för Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Kodgranskningar

En LUIS-app i LUDown-format är läslig, som har stöd för kommunikation av ändringar i en PR som är lämplig för granskning. Enhets test filer skrivs också i LUDown-format och kan också enkelt granskas i en PR.

## <a name="versioning"></a>Versionshantering

Ett program består av flera komponenter som kan omfatta sådant som en robot som körs i [Azure bot service](/azure/bot-service/bot-service-overview-introduction), [QNA Maker](https://www.qnamaker.ai/), [röst tjänsten för Azure](../speech-service/overview.md)med mera. För att uppnå målet med löst kopplade program, använder du [versions kontroll](/azure/devops/learn/git/what-is-version-control) så att varje komponent i ett program har versions hantering oberoende, så att utvecklare kan identifiera ändringar eller uppdateringar genom att titta på versions numret. Det är enklare att version av LUIS-appen oberoende av andra komponenter om du underhåller den i sin egen lagrings platsen.

LUIS-appen för huvud grenen bör ha ett versions schema som tillämpas. När du sammanfogar uppdateringar till `.lu` en Luis-app i huvud gruppen, kommer du att importera den uppdaterade källan till en ny version i Luis-appen för huvud grenen.

Vi rekommenderar att du använder ett numeriskt versions schema för huvud versionen av LUIS-appen, till exempel:

`major.minor[.build[.revision]]`

Varje uppdatering ökar versions numret vid den sista siffran.

Den större/lägre versionen kan användas för att ange omfånget för ändringarna i LUIS app-funktionen:

* Huvud version: en betydande förändring, till exempel stöd för en ny [avsikts](./luis-concept-intent.md) -eller [entitet](./luis-concept-entity-types.md)
* Lägre version: en baklänges-kompatibel mindre förändring, till exempel efter en betydande ny utbildning
* Build: ingen funktions ändring, bara en annan version.

När du har fastställt versions numret för den senaste versionen av din huvudsakliga LUIS-app måste du bygga och testa den nya program versionen och publicera den till en slut punkt där den kan användas i olika build-miljöer, till exempel kvalitets säkring eller produktion. Vi rekommenderar starkt att du automatiserar alla dessa steg i ett arbets flöde för kontinuerlig integration (CI).

Se:
- [Automation-arbetsflöden](luis-concept-devops-automation.md) för information om hur du implementerar ett CI-arbetsflöde för att testa och släppa en Luis-app.
- [Versions hantering](luis-concept-devops-automation.md#release-management) för information om hur du distribuerar Luis-appen.

### <a name="versioning-the-feature-branch-luis-app"></a>Version av LUIS-appen "funktions gren"

När du arbetar med en "dev Branch" LUIS-app som du har skapat för att stödja arbete i en funktions gren, kommer du att exportera appen när ditt arbete har slutförts och du kommer att inkludera den uppdaterade `'lu` i din PR. Grenen i din lagrings platsen och LUIS-appen "dev Branch" bör tas bort när PR har slagits samman i huvud. Eftersom den här appen endast existerar för att stödja arbetet i funktions grenen finns det inget särskilt versions schema som du behöver tillämpa i den här appen.

När dina ändringar i din PR slås samman i huvud, det vill säga när versions hantering ska tillämpas, så att alla uppdateringar av huvud versioner sker oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [testning av Luis-DevOps](luis-concept-devops-testing.md)
* Lär dig hur du [implementerar DevOps för Luis med GitHub](luis-how-to-devops-with-github.md)
