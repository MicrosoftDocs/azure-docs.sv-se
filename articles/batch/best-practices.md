---
title: Bästa praxis
description: Lär dig metod tips och användbara tips för att utveckla dina Azure Batch-lösningar.
ms.date: 03/11/2020
ms.topic: conceptual
ms.openlocfilehash: d1040762c171af486c7f5d66daca44ec65602aff
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561846"
---
# <a name="azure-batch-best-practices"></a>Metod tips för Azure Batch

I den här artikeln beskrivs en samling med bästa praxis och användbara tips för att använda tjänsten Azure Batch effektivt, baserat på verkliga upplevelser med batch. De här tipsen kan hjälpa dig att förbättra prestandan och undvika design fall GRO par i dina Azure Batch-lösningar.

> [!TIP]
> Information om säkerhet i Azure Batch finns i [metod tips för batch-säkerhet och efterlevnad](security-best-practices.md).

## <a name="pools"></a>Pooler

[Pooler](nodes-and-pools.md#pools) är beräknings resurserna för att köra jobb i batch-tjänsten. I följande avsnitt finns rekommendationer för att arbeta med batch-pooler.

### <a name="pool-configuration-and-naming"></a>Konfiguration av pooler och namngivning

- **Poolens fördelnings läge** När du skapar ett batch-konto kan du välja mellan två pool tilldelnings lägen: **Batch-tjänst** eller **användar prenumeration**. I de flesta fall bör du använda standard läget för batch-tjänsten, där pooler allokeras bakom scenerna i batch-hanterade prenumerationer. I det alternativa användarprenumerationsläget skapas virtuella Batch-datorer och andra resurser direkt i din prenumeration när en pool skapas. Användar prenumerations konton används främst för att aktivera en viktig, men liten del av scenarier. Du kan läsa mer om användar prenumerations läge på [ytterligare konfiguration för användar prenumerations läge](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **' virtualMachineConfiguration ' eller ' cloudServiceConfiguration '.**
    Även om du kan skapa pooler med hjälp av en konfiguration måste nya pooler konfigureras med hjälp av "virtualMachineConfiguration" och inte "cloudServiceConfiguration". Alla aktuella och nya batch-funktioner kommer att stödjas av konfigurations pooler för virtuella datorer. Cloud Services konfigurations grupper stöder inte alla funktioner och inga nya funktioner planeras. Du kommer inte att kunna skapa nya cloudServiceConfiguration-pooler eller lägga till nya noder i befintliga pooler [efter den 29 februari 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Mer information finns i [migrera konfiguration av batch-pool från Cloud Services till virtuell dator](batch-pool-cloud-service-to-virtual-machine-configuration.md).

- **Överväg jobb-och uppgifts körnings tid när du bestämmer jobb till pool-mappning.**
    Om du har jobb som huvudsakligen är kortsiktiga och de förväntade totala antalet aktiviteter är små, så att den förväntade körnings tiden för jobbet inte är lång, allokera inte en ny pool för varje jobb. Tilldelnings tiden för noderna minskar jobbets körnings tid.

- **Pooler bör ha mer än en Compute-nod.**
    Enskilda noder är inte garanterat alltid tillgängliga. Även om det är ovanligt kan maskin varu fel, operativ system uppdateringar och en annan värd för andra problem orsaka att enskilda noder är offline. Om batch-arbetsbelastningen kräver deterministisk, garanterad status, bör du allokera pooler med flera noder.

- **Återanvänd inte resurs namn.**
    Batch-resurser (jobb, pooler osv.) kommer ofta att gå över tid. Du kan till exempel skapa en pool på måndag, ta bort den på tisdag och sedan skapa en annan pool på torsdag. Varje ny resurs som du skapar ska tilldelas ett unikt namn som du inte har använt tidigare. Detta kan göras med hjälp av ett GUID (antingen som hela resurs namnet eller som en del av den) eller bädda in den tid som resursen skapades i resurs namnet. Batch stöder [DisplayName](/dotnet/api/microsoft.azure.batch.jobspecification.displayname), som kan användas för att ge en resurs ett läsligt namn även om det verkliga resurs-ID: t är något som inte är det som är mänskligt. Med hjälp av unika namn blir det enklare för dig att särskilja vilka specifika resurser som något i loggar och mått. Det tar också bort tvetydighet om du skulle behöva ange ett support ärende för en resurs.

- **Kontinuitet under underhåll av pooler och haveri.**
    Det är bäst att låta dina jobb använda pooler dynamiskt. Om dina jobb använder samma pool för allt, finns det en risk att dina jobb inte körs om något går fel med poolen. Detta är särskilt viktigt för tids känsliga arbets belastningar. Du kan åtgärda detta genom att välja eller skapa en pool dynamiskt när du schemalägger varje jobb, eller ha ett sätt att åsidosätta poolnamn så att du kan kringgå en ohälsosam pool.

- **Verksamhets kontinuitet under underhåll och haveri för pooler** Det finns många möjliga orsaker som kan förhindra att en pool växer till önskad storlek som du vill ha, till exempel interna fel, kapacitets begränsningar osv. Därför bör du vara redo att återanvända jobb på en annan pool (eventuellt med en annan VM-storlek – batch stöder detta via [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)) om det behövs. Undvik att använda ett statiskt pool-ID med förväntat att det aldrig kommer att tas bort och aldrig ändras.

### <a name="pool-lifetime-and-billing"></a>Livstid för pool och fakturering

Poolens livs längd kan variera beroende på vilken metod du vill tilldela och vilka alternativ som tillämpas på konfigurationen av poolen. Pooler kan ha en godtycklig livs längd och ett varierande antal data bearbetnings noder i poolen vid varje tidpunkt. Det är ditt ansvar att hantera Compute-noderna i poolen, antingen uttryckligen eller via funktioner som tillhandahålls av tjänsten ([autoskalning](nodes-and-pools.md#automatic-scaling-policy) eller [autopool](nodes-and-pools.md#autopools)).

- **Se till att pooler är färska.**
    Ändra storlek på dina pooler till noll med några månader för att se till att du får de [senaste uppdateringarna för Node agent och fel korrigeringar](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md). Poolen tar inte emot uppdateringar för Node-agenten om den inte återskapas eller ändras till 0 datornoder. Innan du återskapar eller ändrar storlek på poolen rekommenderar vi att du hämtar eventuella noder för fel sökning enligt beskrivningen i avsnittet [Nodes](#nodes) .

- **Skapa ny pool** På en liknande anteckning rekommenderar vi inte att du tar bort och återskapar dina pooler på daglig basis. Skapa i stället en ny pool och uppdatera dina befintliga jobb så att de pekar på den nya poolen. När alla aktiviteter har flyttats till den nya poolen tar du bort den gamla poolen.

- **Pool effektivitet och fakturering** Batch debiterar sig inga extra avgifter, men du debiteras för de beräknings resurser som används. Du debiteras för varje Compute-nod i poolen, oavsett vilken status den har i. Detta inkluderar eventuella avgifter som krävs för att noden ska kunna köras, till exempel lagrings-och nätverks kostnader. Mer information om bästa praxis finns i [kostnads analys och budgetar för Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Allokeringsfel för pool

Allokeringsfel för pooler kan inträffa när som helst under första allokeringen eller efterföljande storleks ändringar. Detta kan bero på temporär kapacitets utbelastning i en region eller andra Azure-tjänster som batchen använder. Kärn kvoten är inte en garanti utan en gräns.

### <a name="unplanned-downtime"></a>Oplanerat stillestånd

Batch-pooler kan uppleva stillestånds händelser i Azure. Tänk på detta när du planerar och utvecklar ditt scenario eller ett arbets flöde för batch.

Om en nod Miss lyckas försöker batch automatiskt återställa de här Compute-noderna åt dig. Detta kan utlösa en omschemaläggningen av alla pågående aktiviteter på den nod som återställs. Se [utforma för nya försök](#design-for-retries-and-re-execution) för att lära dig mer om avbrutna uppgifter.

### <a name="custom-image-pools"></a>Anpassade bildpooler

När du skapar en Azure Batch-pool med den virtuella dator konfigurationen anger du en VM-avbildning som tillhandahåller operativ systemet för varje Compute-nod i poolen. Du kan skapa poolen med en Azure Marketplace-avbildning som stöds, eller så kan du [skapa en anpassad avbildning med en avbildning av ett delat avbildnings Galleri](batch-sig-images.md). Även om du kan använda en [hanterad avbildning](batch-custom-images.md) för att skapa en anpassad avbildning, rekommenderar vi att du skapar anpassade avbildningar med hjälp av den delade avbildnings galleriet närhelst det är möjligt. Med hjälp av den delade avbildnings galleriet kan du etablera pooler snabbare, skala större mängder virtuella datorer och förbättra tillförlitligheten vid etablering av virtuella datorer.

### <a name="third-party-images"></a>Avbildningar från tredje part

Pooler kan skapas med avbildningar från tredje part som publicerats på Azure Marketplace. Med batch-konton för användar prenumerations läge kan du se felet "tilldelningen misslyckades på grund av kontroll av marknads plats inköp" när du skapar en pool med vissa avbildningar från tredje part. För att lösa det här felet accepterar du villkoren som anges av avbildningens utgivare. Du kan göra det med hjälp av [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) eller [Azure CLI](/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Azure-region beroende

Vi rekommenderar att du inte är beroende av en enda Azure-region om du har en tids känslig eller produktions belastning. Även om det är sällsynt, finns det problem som kan påverka en hel region. Om din bearbetning till exempel behöver starta vid en angiven tidpunkt, kan du överväga att skala upp poolen i din primära region på ett *bra sätt innan du börjar med start tiden*. Om poolens skalning Miss lyckas kan du återgå till att skala upp en pool i en säkerhets kopierings region (eller regioner). Pooler över flera konton i olika regioner ger en klar och lättillgänglig säkerhets kopia om något går fel med en annan pool. Mer information finns i [utforma ditt program för hög tillgänglighet](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Jobb

Ett [jobb](jobs-and-tasks.md#jobs) är en behållare som är utformad för att innehålla hundratals, tusentals eller till och med miljon tals uppgifter. Följ dessa rikt linjer när du skapar jobb.

### <a name="fewer-jobs-more-tasks"></a>Färre jobb, fler aktiviteter

Att använda ett jobb för att köra en enskild uppgift är ineffektivt. Till exempel är det mer effektivt att använda ett enda jobb som innehåller 1000 uppgifter i stället för att skapa 100-jobb som innehåller 10 aktiviteter. Att köra 1000-jobb, var och en med en enda uppgift, är den minst effektiva, långsammast och mest dyra metoden att ta.

Därför bör du se till att du inte utformar en batch-lösning som kräver tusentals aktiva jobb samtidigt. Det finns ingen kvot för aktiviteter, så att du kör många aktiviteter under så få jobb som möjligt effektivt använder dina [jobb-och jobb schema kvoter](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Jobbets livs längd

Ett batch-jobb har en obegränsad livs längd tills det tas bort från systemet. Dess status anger om den kan acceptera fler aktiviteter för schemaläggning eller inte.

Ett jobb flyttas inte automatiskt till slutfört tillstånd om det inte uttryckligen avslutas. Detta kan aktive ras automatiskt via egenskapen [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) eller [maxWallClockTime](/rest/api/batchservice/job/add#jobconstraints).

Det finns ett [aktivt standard jobb och en kvot för jobb schema](batch-quota-limit.md#resource-quotas). Jobb och jobb scheman i slutfört tillstånd räknas inte över till den här kvoten.

## <a name="tasks"></a>Aktiviteter

[Aktiviteter](jobs-and-tasks.md#tasks) är enskilda enheter av arbete som utgör ett jobb. Aktiviteter skickas av användaren och schemaläggs av batch på för att beräkna noder. Det finns flera design aspekter att fatta när du skapar och kör uppgifter. I följande avsnitt beskrivs vanliga scenarier och hur du utformar dina aktiviteter för att hantera problem och utföra effektiva åtgärder.

### <a name="save-task-data"></a>Spara uppgifts data

Compute-noder är av sin natur. Det finns många funktioner i batch, till exempel [autopool](nodes-and-pools.md#autopools) och [autoskalning](nodes-and-pools.md#automatic-scaling-policy) som kan göra det enkelt för noder att försvinna. När noder lämnar en pool (på grund av en storleks ändring eller en pool borttagning) raderas även alla filer på noderna. Därför bör en aktivitet flytta utdatan från noden som den körs på och till en varaktig lagring innan den är klar. Om en aktivitet Miss lyckas bör den flytta de loggar som krävs för att diagnostisera misslyckandet till ett varaktigt lager.

Batch har integrerat stöd Azure Storage för att ladda upp data via [OutputFiles](batch-task-output-files.md), samt en rad olika delade fil system, eller så kan du utföra överföringen själv i dina uppgifter.

### <a name="manage-task-lifetime"></a>Hantera uppgifts livs längd

Ta bort aktiviteter när de inte längre behövs eller ange en [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) . Om en `retentionTime` har angetts rensar batch automatiskt disk utrymmet som används av aktiviteten när det `retentionTime` upphör att gälla.

Att ta bort uppgifter utför två saker. Det garanterar att du inte har en version av aktiviteterna i jobbet, vilket kan göra det svårare att fråga/hitta den uppgift som du är intresse rad av (eftersom du måste filtrera genom de slutförda uppgifterna). Den rensar också motsvarande uppgifts data på noden (anges `retentionTime` har inte redan nåtts). På så sätt kan du se till att dina noder inte fyller i aktivitets data och att det inte får slut på disk utrymme.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Skicka in ett stort antal uppgifter i samlingen

Aktiviteter kan skickas på en enskild basis eller i samlingar. Skicka uppgifter i [samlingar](/rest/api/batchservice/task/addcollection) på upp till 100 i taget när du utför Mass inlämning av uppgifter för att minska omkostnader och överförings tider.

### <a name="set-max-tasks-per-node-appropriately"></a>Ange Max aktiviteter per nod på lämpligt sätt

Batch stöder oversubscribing-aktiviteter på noder (som kör fler aktiviteter än en nod har kärnor). Det är upp till dig att se till att aktiviteterna anpassas till noderna i poolen. Du kan till exempel ha en försämrad upplevelse om du försöker schemalägga åtta uppgifter som varje använder 25% CPU-användning på en nod (i en pool med `taskSlotsPerNode = 8` ).

### <a name="design-for-retries-and-re-execution"></a>Design för nya försök och ny körning

Aktiviteter kan göras om automatiskt med batch. Det finns två typer av återförsök: User-styrd och Internal. Användar kontrollerade återförsök anges av aktivitetens [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount). När ett program som anges i uppgiften avslutas med en slutkod som inte är noll, görs ett nytt försök till värdet för `maxTaskRetryCount` .

Även om det är sällsynt, kan en aktivitet omprövas internt på grund av att det inte går att uppdatera beräknings noden, till exempel att det inte går att uppdatera det interna läget eller ett haveri på noden medan aktiviteten körs. Aktiviteten provas på samma Compute-nod, om möjligt, upp till en intern gräns innan den ger aktiviteten och en uppskjuten aktivitet som ska schemaläggas om av batch, eventuellt på en annan Compute-nod.

Det finns inga design skillnader när du kör aktiviteter på dedikerade eller låg prioritets noder. Om en aktivitet avbryts vid körning på en nod med låg prioritet eller avbryts på grund av ett haveri på en dedikerad nod, begränsas båda situationerna genom att aktiviteten ändras till att motstå felen.

### <a name="build-durable-tasks"></a>Bygg tåliga uppgifter

Aktiviteter bör utformas för att motstå fel och försöka hantera försök igen. Detta är särskilt viktigt för tids krävande uppgifter. Det gör du genom att se till att uppgifterna genererar samma resultat även om de körs mer än en gång. Ett sätt att åstadkomma detta är att göra dina uppgifter till "måls sökning". Ett annat sätt är att se till att uppgifterna är idempotenta (uppgifter kommer att ha samma resultat oavsett hur många gånger de körs).

Ett vanligt exempel är en uppgift för att kopiera filer till en Compute-nod. En enkel metod är en uppgift som kopierar alla angivna filer varje gång den körs, vilket är ineffektivt och inte är konstruerat för att motstå felen. Skapa i stället en aktivitet för att se till att filerna finns på Compute-noden. en uppgift som inte kopierar om filer som redan finns. På det här sättet hämtar uppgiften där den slutade om den avbröts.

### <a name="avoid-short-execution-time"></a>Undvik kort körnings tid

Aktiviteter som bara körs för en till två sekunder är inte idealiska. Du bör försöka göra en stor mängd arbete i en enskild uppgift (10 sekunders tid på upp till timmar eller dagar). Om varje aktivitet körs i en minut (eller mer) är tids gränsen för schemaläggningen som en bråkdel av den totala beräknings tiden liten.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Använd pool-scope för korta uppgifter på Windows-noder

När du schemalägger en aktivitet på batch-noder kan du välja om du vill köra den med aktivitets omfånget eller poolens omfång. Om aktiviteten bara ska köras under en kort tid, kan uppgifts omfattningen vara ineffektiv på grund av de resurser som krävs för att skapa det automatiska användar kontot för den aktiviteten. För ökad effektivitet bör du överväga att ställa in dessa uppgifter till poolens omfång. Mer information finns i [köra en aktivitet som en automatisk användare med pool-scope](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Noder

En [Compute-nod](nodes-and-pools.md#nodes) är en virtuell Azure-dator (VM) eller en virtuell moln tjänst som är dedikerad för bearbetning av en del av programmets arbets belastning. Följ dessa rikt linjer när du arbetar med noder.

### <a name="idempotent-start-tasks"></a>Idempotenta start uppgifter

Precis som med andra uppgifter ska nodens [Start aktivitet](jobs-and-tasks.md#start-task) vara idempotenta, eftersom den kommer att köras varje gång noden startas. En idempotenta-uppgift är bara en som ger ett konsekvent resultat vid körning flera gånger.

### <a name="isolated-nodes"></a>Isolerade noder

Överväg att använda isolerade VM-storlekar för arbets belastningar med efterlevnads-eller reglerings krav. De isolerade storlekar som stöds i konfigurations läget för den virtuella datorn är `Standard_E80ids_v4` ,,,, `Standard_M128ms` `Standard_F72s_v2` `Standard_G5` `Standard_GS5` och `Standard_E64i_v3` . Mer information om isolerade VM-storlekar finns i [isolering av virtuella datorer i Azure](../virtual-machines/isolation.md).

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Hantera tids krävande tjänster via gränssnittet för operativ system tjänster

Ibland måste du köra en annan Agent tillsammans med batch-agenten i noden. Du kanske till exempel vill samla in data från noden och rapportera dem. Vi rekommenderar att dessa agenter distribueras som OS-tjänster, till exempel en Windows-tjänst eller en Linux `systemd` -tjänst.

När de här tjänsterna körs får de inte ta fillås på några filer i batch-hanterade kataloger på noden, eftersom annars kan batch inte ta bort dessa kataloger på grund av fillås. Om du t. ex. installerar en Windows-tjänst i en start aktivitet, i stället för att starta tjänsten direkt från arbets katalogen starta aktivitet, kopierar du filerna någon annan stans (eller om filerna redan finns, hoppa över kopian). Installera sedan tjänsten från den platsen. När batch kör om din start uppgift, tar den bort start uppgiftens arbets katalog och skapar den igen. Detta fungerar eftersom tjänsten har fillås på den andra katalogen, inte arbets katalogen för start aktiviteten.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Undvik att skapa katalog Knut punkter i Windows

Katalog kopplingar, ibland kallade katalog hårda länkar, är svåra att hantera under aktivitets-och jobb rensning. Använd symlinks (mjuka länkar) i stället för hårda länkar.

### <a name="collect-the-batch-agent-logs"></a>Samla in batch agent-loggar

Om du upptäcker ett problem som involverar beteendet för en nod eller aktiviteter som körs på en nod, ska du samla in batch-agentens loggar innan du avallokerar noderna i fråga. Batch agent-loggarna kan samlas in med hjälp av API: t Ladda upp batch-tjänst loggar. Dessa loggar kan levereras som en del av ett support ärende till Microsoft och hjälper till med problem fel sökning och lösning.

### <a name="manage-os-upgrades"></a>Hantera OS-uppgraderingar

För batch-konton för användar prenumerations läge kan automatiserade OS-uppgraderingar avbryta aktivitets förloppet, särskilt om aktiviteterna körs långvarigt. Att [skapa idempotenta-uppgifter](#build-durable-tasks) kan hjälpa till att minska felen som orsakas av dessa avbrott. Vi rekommenderar också [att du schemalägger uppgradering av OS-avbildningar för de tillfällen då aktiviteter inte förväntas köras](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

För Windows-pooler `enableAutomaticUpdates` anges som `true` standard som standard. Att tillåta automatiska uppdateringar rekommenderas, men du kan ange det här värdet `false` om du vill kontrol lera att en OS-uppdatering inte sker utan förvarning.

## <a name="isolation-security"></a>Isolerings säkerhet

Om ditt scenario kräver att du isolerar jobb från varandra i isolerings syfte, gör du det genom att ha dem i separata pooler. En pool är säkerhets isolerings gränser i batch och som standard är två pooler inte synliga eller kan kommunicera med varandra. Undvik att använda separata batch-konton som ett sätt att isolera.

## <a name="moving-batch-accounts-across-regions"></a>Flytta batch-konton över regioner

Det finns scenarier där det kan vara bra att flytta ett befintligt batch-konto från en region till en annan. Till exempel kanske du vill flytta till en annan region som en del av Disaster Recovery-planeringen.

Azure Batch-konton kan inte flyttas direkt från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera den befintliga konfigurationen av ditt batch-konto. Du kan sedan mellanlagra resursen i en annan region genom att exportera batch-kontot till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.

När du har överfört mallen till den nya regionen måste du återskapa certifikat, jobb scheman och programpaket. Kom ihåg att ta bort det ursprungliga batch-kontot eller resurs gruppen för att genomföra ändringarna och slutföra flyttningen av batch-kontot.

Mer information om Resource Manager och mallar finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Anslutning

Läs följande rikt linjer för anslutning i dina batch-lösningar.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Nätverks säkerhets grupper (NSG: er) och användardefinierade vägar (UDR)

När du konfigurerar [batch-pooler i ett virtuellt nätverk](batch-virtual-network.md)bör du se till att du noga följer rikt linjerna för användningen av `BatchNodeManagement` Service Tag-numret, portarna, protokollen och riktningen för regeln. Användningen av service tag gen rekommenderas i stället för att använda de underliggande IP-adresserna för batch-tjänsten. Detta beror på att IP-adresserna kan ändras med tiden. Användning av IP-adresser för batch-tjänsten direkt kan orsaka instabilitet, avbrott eller avbrott för dina batch-pooler.

För användardefinierade vägar (UDR) ser du till att du har en process för att uppdatera batch-tjänstens IP-adresser med jämna mellanrum i routningstabellen, eftersom dessa adresser ändras med tiden. Information om hur du hämtar en lista över IP-adresser för batch-tjänsten finns i [tjänst Taggar lokalt](../virtual-network/service-tags-overview.md). IP-adresserna för batch-tjänsten kommer att associeras med `BatchNodeManagement` Service Tag-numret (eller den regionala variant som matchar ditt batch-kontoområdet).

### <a name="honoring-dns"></a>DNS-inlöst

Se till att dina system följer DNS-TTL (Time-to-Live) för batch-kontots tjänst-URL. Se dessutom till att batch-tjänstens klienter och andra anslutnings metoder till batch-tjänsten inte förlitar sig på IP-adresser (eller [skapa en pool med statiska offentliga IP-adresser](create-pool-public-ip.md) enligt beskrivningen nedan).

Om dina begär Anden tar emot HTTP-svar på 5xx nivå och det finns ett "anslutning: Stäng"-huvud i svaret, bör batch-tjänstens klient studera rekommendationen genom att stänga den befintliga anslutningen, matcha om DNS för batch-kontots tjänst-URL och försöka utföra följande förfrågningar på en ny anslutning.

### <a name="retry-requests-automatically"></a>Försök utföra begär Anden automatiskt

Se till att dina batch-betjäna klienter har lämpliga principer för återförsök på plats för att automatiskt försöka utföra begär anden igen, även under normal drift och inte enbart under några tids perioder för tjänste underhåll. Dessa principer för återförsök bör omfatta ett intervall på minst 5 minuter. Funktioner för automatisk återförsök tillhandahålls med olika batch-SDK: er, till exempel [.net RetryPolicyProvider-klassen](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Statiska offentliga IP-adresser

Normalt nås virtuella datorer i en batch-pool via offentliga IP-adresser som kan ändras under poolens livstid. Detta kan göra det svårt att interagera med en databas eller annan extern tjänst som begränsar åtkomsten till vissa IP-adresser. För att säkerställa att de offentliga IP-adresserna i poolen inte ändras, kan du skapa en pool med en uppsättning statiska offentliga IP-adresser som du styr. Mer information finns i [skapa en Azure Batch pool med angivna offentliga IP-adresser](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Testa anslutning med Cloud Services konfiguration

Du kan inte använda det normala "ping"-/ICMP-protokollet med moln tjänster eftersom ICMP-protokollet inte tillåts via Azure Load Balancer. Mer information finns i [anslutningar och nätverk för Azure Cloud Services](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>Underliggande beroenden för batch-noden

Tänk på följande beroenden och begränsningar när du utformar dina batch-lösningar.

### <a name="system-created-resources"></a>System-skapade resurser

Azure Batch skapar och hanterar en uppsättning användare och grupper på den virtuella datorn, som inte ska ändras. Det här är skillnaderna:

Windows:

- En användare med namnet **PoolNonAdmin**
- En användar grupp med namnet **WATaskCommon**

Linux:

- En användare med namnet **_azbatch**

### <a name="file-cleanup"></a>Fil rensning

Batch försöker aktivt rensa arbets katalogen som aktiviteter körs i, när deras kvarhållningsperiod upphör att gälla. Alla filer som skrivs utanför katalogen är [ditt ansvar att rensa upp](#manage-task-lifetime) för att undvika att fylla i disk utrymme.

Den automatiserade rensningen av arbets katalogen blockeras om du kör en tjänst i Windows från startTask arbets katalog, på grund av att mappen fortfarande används. Detta leder till försämrade prestanda. Åtgärda detta genom att ändra katalogen för tjänsten till en separat katalog som inte hanteras av batch.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om [standard Azure Batch kvoter, gränser och begränsningar samt hur du begär kvot ökningar](batch-quota-limit.md).
- Lär dig hur du kan [identifiera och undvika fel i en pool-och nods bakgrunds åtgärder ](batch-pool-node-error-checking.md).