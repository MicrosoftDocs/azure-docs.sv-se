---
title: Återställning av Azure Service Fabric haveri beredskap
description: Azure Service Fabric erbjuder funktioner för att hantera katastrofer. Den här artikeln beskriver de typer av katastrofer som kan uppstå och hur du hanterar dem.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791163"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Haveri beredskap i Azure Service Fabric
En viktig del av att leverera hög tillgänglighet säkerställer att tjänsterna kan överleva alla olika typer av problem. Detta är särskilt viktigt för problem som är oplanerade och utanför din kontroll. 

I den här artikeln beskrivs några vanliga fel lägen som kan vara katastrofer om de inte är modellerade och hanterade på rätt sätt. Den diskuterar också åtgärder och åtgärder som ska vidtas om en katastrof sker ändå. Målet är att begränsa eller eliminera risken för drift avbrott eller data förlust när fel, planerat eller på annat sätt inträffar.

## <a name="avoiding-disaster"></a>Undvika katastrof
Det främsta målet med Azure Service Fabric är att hjälpa dig att modellera både din miljö och dina tjänster på ett sådant sätt att vanliga typer av fel inte är katastrofer. 

I allmänhet finns det två typer av haveri-/haveri scenarier:
- Maskin-och program varu fel
- Drift fel

### <a name="hardware-and-software-faults"></a>Maskin-och program varu fel
Maskin-och program varu fel är oförutsägbara. Det enklaste sättet att överleva fel är att köra fler kopior av tjänsten över maskin-eller program varu fel gränser. 

Om din tjänst till exempel bara körs på en dator, är det ett haveri för den tjänsten. Det enkla sättet att undvika den här katastrofen är att se till att tjänsten körs på flera datorer. Testning krävs också för att säkerställa att en dator inte stör den aktiva tjänsten. Kapacitets planering säkerställer att en ersättnings instans kan skapas någon annan stans och att minskningen av kapaciteten inte överbelastar de återstående tjänsterna. 

Samma mönster fungerar oavsett vad du försöker att undvika. Om du till exempel är orolig över ett SAN-nätverk kan du köra flera San-nätverk. Om du är orolig över förlusten av en server ställning kan du köra flera rack. Om du oroar dig för att förlora data Center bör tjänsten köras i flera Azure-regioner, över flera Azure-tillgänglighetszoner eller i dina egna data Center. 

När en tjänst sträcker sig över flera fysiska instanser (datorer, rack, data Center, regioner) är du fortfarande underkastade vissa typer av samtidiga haverier. Men enstaka och till och med flera misslyckanden av en viss typ (till exempel en enskild virtuell dator eller en nätverks länk) hanteras automatiskt och är inte längre en "olycka". 

Service Fabric tillhandahåller mekanismer för att expandera klustret och hantera att placera Felaktiga noder och tjänster tillbaka. Service Fabric kan också köra många instanser av dina tjänster för att förhindra att oplanerade haverier inaktive ras i verkliga haverier.

Det kan finnas orsaker till varför det inte är möjligt att köra en distribution som är tillräckligt stor för att spänna över felen. Det kan till exempel ta fler maskin varu resurser än vad du är villig att betala i förhållande till risken för problem. När du hanterar distribuerade program kan ytterligare kommunikations hopp eller tillstånds kostnader för replikering mellan geografiska avstånd orsaka en oacceptabel fördröjning. Den här raden ritas annorlunda för varje program. 

För program fel kan felet vara i den tjänst som du försöker skala. I det här fallet kan fler kopior inte förhindra katastrofen eftersom fel tillståndet korreleras över alla instanser.

### <a name="operational-faults"></a>Drift fel
Även om din tjänst sträcker sig över hela världen med många uppsägningar, kan den fortfarande uppleva katastrofal-händelser. Någon kan till exempel oavsiktligt konfigurera om DNS-namnet för tjänsten eller ta bort den direkt. 

Anta till exempel att du hade en tillstånds känslig Service Fabric-tjänst och att någon har tagit bort tjänsten av misstag. Om det inte finns någon annan minskning är den tjänsten och hela det tillstånd som den hade nu borta. Dessa typer av drift haverier ("Hoppsan") kräver olika åtgärder och återställnings steg än vanliga oplanerade haverier. 

Det bästa sättet att undvika dessa typer av drift fel är att:
- Begränsa drifts åtkomst till miljön.
- Strikt granskning av farliga åtgärder.
- Införa automatisering, förhindra manuella eller out-of-band-ändringar och validera vissa ändringar i miljön innan du gör dem.
- Se till att destruktiva åtgärder är "mjuka". Mjuka åtgärder börjar inte gälla omedelbart eller kan inte utföras inom ett tids fönster.

Service Fabric tillhandahåller mekanismer för att förhindra drift fel, till exempel att tillhandahålla [rollbaserad](service-fabric-cluster-security-roles.md) åtkomst kontroll för kluster åtgärder. De flesta av dessa drift fel kräver dock organisatoriska ansträngningar och andra system. Service Fabric tillhandahåller mekanismer för kvarvarande drifts fel, främst [säkerhets kopiering och återställning för tillstånds känsliga tjänster](service-fabric-backuprestoreservice-quickstart-azurecluster.md).

## <a name="managing-failures"></a>Hantera problem
Målet med Service Fabric är automatisk hantering av felen. Men för att hantera vissa typer av problem måste tjänsterna ha ytterligare kod. Andra typer av försök bör _inte_ automatiskt åtgärdas för säkerhets-och affärs kontinuitets skäl. 

### <a name="handling-single-failures"></a>Hantera enskilda problem
Enskilda datorer kan inte utföras av alla typer av orsaker. Ibland är det maskin varu orsaker, t. ex. strömförsörjning och nätverks maskin varu fel. Andra problem finns i program varan. Dessa omfattar även problem med operativ systemet och själva tjänsten. Service Fabric identifierar automatiskt de här typerna av fel, inklusive fall där datorn blir isolerad från andra datorer på grund av nätverks problem.

Oavsett vilken typ av tjänst som körs kan en enda instans utföras under drift stopp för den tjänsten om den enskilda kopian av koden Miss lyckas av någon anledning. 

Om du vill hantera ett enskilt haveri är det enklast att se till att dina tjänster körs på fler än en nod som standard. Kontrol lera att `InstanceCount` är större än 1 för tillstånds lösa tjänster. För tillstånds känsliga tjänster är den minsta rekommendationen att `TargetReplicaSetSize` och `MinReplicaSetSize` båda anges till 3. Om du kör fler kopior av Service koden ser du till att din tjänst kan hantera alla enskilda problem automatiskt. 

### <a name="handling-coordinated-failures"></a>Hantera koordinerade haverier
Koordinerade fel i ett kluster kan bero på antingen planerade eller oplanerade infrastruktur fel och ändringar, eller planerade program varu ändringar. Service Fabric modeller infrastruktur zoner som upplever koordinerade fel som *fel domäner*. Områden som kommer att uppleva koordinerade program varu ändringar modelleras som *uppgraderings domäner*. Mer information om fel domäner, uppgraderings domäner och kluster sto pol Ogin finns i [Beskriv ett Service Fabric kluster med hjälp av kluster resurs hanteraren](service-fabric-cluster-resource-manager-cluster-description.md).

Som standard anses Service Fabric fel-och uppgraderings domäner när du planerar var tjänsterna ska köras. Som standard försöker Service Fabric se till att dina tjänster körs över flera fel-och uppgraderings domäner så att tjänsterna är tillgängliga även om planerade eller oplanerade ändringar sker. 

Anta till exempel att det uppstår fel i en ström källa som gör att alla datorer på en rack Miss lyckas samtidigt. När flera kopior av tjänsten körs, blir förlusten av många datorer i fel domän fel i ett annat exempel på ett enstaka fel för en tjänst. Det är därför viktigt att hantera fel-och uppgraderings domäner för att säkerställa hög tillgänglighet för dina tjänster. 

När du kör Service Fabric i Azure hanteras fel domäner och uppgraderings domäner automatiskt. I andra miljöer kanske de inte är det. Om du skapar egna kluster lokalt ska du se till att mappa och planera din feldomän-layout korrekt.

Uppgraderings domäner är användbara för modellerings områden där program vara ska uppgraderas samtidigt. Därför definierar uppgraderings domäner ofta gränserna där program varan tas offline under planerade uppgraderingar. Uppgraderingar av både Service Fabric och dina tjänster följer samma modell. Mer information om löpande uppgraderingar, uppgraderings domäner och Service Fabric hälso modell som hjälper till att förhindra oönskade ändringar från att påverka klustret och tjänsten finns i:

 - [Programuppgradering](service-fabric-application-upgrade.md)
 - [Själv studie kurs om program uppgradering](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric hälso modell](service-fabric-health-introduction.md)

Du kan visualisera layouten för klustret med hjälp av kluster kartan som finns i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Noderna sprids över fel domäner i Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Att utforma fel, löpande uppgraderingar, köra många instanser av Service koden och tillståndet, placerings regler för att säkerställa att dina tjänster körs i fel-och uppgraderings domäner och inbyggd hälso övervakning är bara *några* av de funktioner som Service Fabric tillhandahåller vanliga drifts problem och fel som kan uppstå i haverier. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Hantera samtidiga maskinvaru-eller program varu problem
Vi har pratat om enskilda haverier. Som du kan se är de lätta att hantera för både tillstånds lösa och tillstånds känsliga tjänster genom att flera kopior av koden (och tillstånd) körs i fel-och uppgraderings domäner. 

Flera samtidiga slumpmässiga problem kan också inträffa. Detta är mer sannolikt att leda till avbrott eller en faktisk katastrof.


#### <a name="stateless-services"></a>Tillstånds lösa tjänster
Antalet instanser för en tillstånds lös tjänst indikerar det önskade antalet instanser som måste köras. När några (eller alla) instanser av instanserna inte fungerar, svarar Service Fabric att automatiskt skapa ersättnings instanser på andra noder. Service Fabric fortsätter att skapa ersättningar tills tjänsten är tillbaka till det önskade instans antalet.

Anta till exempel att den tillstånds lösa tjänsten har `InstanceCount` värdet-1. Det här värdet innebär att en instans ska köras på varje nod i klustret. Om några av dessa instanser Miss söker, identifierar Service Fabric att tjänsten inte har önskad status och försöker skapa instanser på noder där de saknas.

#### <a name="stateful-services"></a>Tillstånds känsliga tjänster
Det finns två typer av tillstånds känsliga tjänster:
- Tillstånds känslig med beständigt tillstånd.
- Tillstånds känslig med icke beständig status. (Tillstånd lagras i minnet.)

Återställning efter fel för en tillstånds känslig tjänst beror på typen av tillstånds känslig tjänst, hur många repliker tjänsten hade och hur många repliker som misslyckades.

I en tillstånds känslig tjänst replikeras inkommande data mellan repliker (den primära och alla aktiva sekundära). Om en majoritet av replikerna tar emot data betraktas data *som dedikerat* . (För fem repliker kommer tre att vara ett kvorum.) Det innebär att när som helst kommer det att finnas minst ett kvorum med repliker med den senaste informationen. Om repliker inte fungerar (till exempel två av fem) kan vi använda kvorumkonfigurationen för att beräkna om vi kan återställa. (Eftersom de återstående tre av fem replikerna fortfarande är upp garanterar vi att minst en replik kommer att ha fullständiga data.)

När ett kvorum med repliker inte fungerar, deklareras partitionen som status för *kvorum* . Anta att en partition har fem repliker, vilket innebär att minst tre garanterar fullständiga data. Om det uppstår ett kvorum (tre av fem) repliker kan Service Fabric inte avgöra om de återstående replikerna (två av fem) har tillräckligt med data för att återställa partitionen. I de fall där Service Fabric identifierar kvorum, är dess standard beteende att förhindra ytterligare skrivningar till partitionen, deklarerar kvorumet och väntar på att ett kvorum med repliker ska återställas.

Avgöra om en katastrof har inträffat för en tillstånds känslig tjänst och sedan hantera den i följande tre steg:

1. Avgör om det finns något kvorum eller inte.
   
   Förlorade kvorum deklareras när en majoritet av replikerna av en tillstånds känslig tjänst är nere på samma gång.
2. Fastställer om kvorumet är permanent eller inte.
   
   Det mesta av tiden är att felen är tillfälliga. Processerna startas om, noderna startas om, virtuella datorer startas om och nätverks partitioner. Ibland är även felen permanenta. Om felen är permanenta eller inte beror på om den tillstånds känsliga tjänsten behåller sitt tillstånd eller om den behåller den endast i minnet: 
   
   - För tjänster utan beständig status uppstår ett problem med ett kvorum eller flera repliker _direkt_ i permanent kvorum förlust. När Service Fabric identifierar kvorum i en tillstånds känslig tjänst, fortsätter den omedelbart till steg 3 genom att deklarera (potential) data förlust. Att gå vidare till data förlust är meningsfullt eftersom Service Fabric vet att det inte finns någon tidpunkt i väntan på att replikerna ska komma tillbaka. Även om de återställs går data förlorade på grund av tjänstens beständiga natur.
   - För tillstånds känsliga tjänster kan ett fel i ett kvorum eller flera repliker orsaka att Service Fabric vänta tills replikerna kommer tillbaka och återställa kvorumet. Detta resulterar i avbrott i tjänsten för alla _skrivningar_ till den berörda partitionen (eller "replik uppsättningen") för tjänsten. Läsningar kan dock fortfarande vara möjliga med minskad konsekvens garanti. Standard tiden som Service Fabric väntar på att kvorumet ska återställas är *oändlig*, eftersom det är en (potentiell) data förlust händelse och medför andra risker. Det innebär att Service Fabric inte fortsätter till nästa steg om inte en administratör vidtar åtgärder för att deklarera data förlust.
3. Avgöra om data förloras och återställning från säkerhets kopior.

   Om du har deklarerat att kvorumet förloras (antingen automatiskt eller via administrativ åtgärd), Service Fabric och tjänsterna flyttas vidare för att avgöra om data faktiskt förlorades. I det här läget vet Service Fabric också att de andra replikerna inte kommer tillbaka. Det var beslutet som fattades när vi slutade vänta på att kvorumet skulle lösas. Den bästa åtgärden för tjänsten är vanligt vis att frysa och vänta på en speciell administrativ åtgärd.
   
   När Service Fabric anropar `OnDataLossAsync` metoden är det alltid på grund av _misstänkt_ data förlust. Service Fabric garanterar att anropet levereras till den _bästa_ återstående repliken. Detta är den replik som har förloppet. 
   
   Orsaken till att det alltid finns _misstänkt_ data förlust är att det är möjligt att den återstående repliken har samma tillstånd som den primära gjorde när kvorumet skulle försvinna. Men utan det läget för att jämföra det, finns det inget bra sätt för Service Fabric eller operatörer att känna till.     
   
   Vad gör en typisk implementering av `OnDataLossAsync` metoden?
   1. De implementerings loggar som `OnDataLossAsync` har utlösts och som sedan startas om nödvändiga administrativa aviseringar.
   1. Normalt pausar och väntar på ytterligare beslut och manuella åtgärder som ska vidtas. Detta beror på att även om säkerhets kopieringar är tillgängliga, kan de behöva förberedas. 
   
      Om till exempel två olika tjänster koordinerar information kan dessa säkerhets kopieringar behöva ändras för att se till att efter återställningen har den information som dessa två tjänster bryr sig om är konsekvent. 
   1. Det finns ofta någon annan telemetri eller ett annat avgaser från tjänsten. Dessa metadata kan finnas i andra tjänster eller i loggar. Den här informationen kan användas vid behov för att avgöra om det fanns några anrop som tagits emot och bearbetats på den primära som inte fanns i säkerhets kopian eller repliker ATS till den aktuella repliken. Dessa anrop kan behöva spelas upp eller läggas till i säkerhets kopian innan återställningen är möjlig.  
   1. Implementeringen jämför den återstående replikens tillstånd med den som finns i tillgängliga säkerhets kopior. Om du använder Service Fabric pålitliga samlingar finns det [verktyg och processer](service-fabric-reliable-services-backup-restore.md) som du kan använda för att göra det. Målet är att se om statusen i repliken är tillräcklig och se vad säkerhets kopieringen kan saknas.
   1. När jämförelsen är klar, och efter att återställningen har slutförts (vid behov), ska Service koden returnera **True** om några tillstånds ändringar gjordes. Om repliken har fastställt att det var den bästa tillgängliga kopian av statusen och inga ändringar har gjorts, returnerar koden **falskt**. 
   
      Värdet **True** anger att _andra_ återstående repliker nu kan vara inkonsekventa med det här. De kommer att släppas och återskapas från den här repliken. Värdet **false** anger att inga tillstånds ändringar gjordes, så de andra replikerna kan behålla det du har. 

Det är mycket viktigt att tjänst författarna kan öva på potentiella data förlust och haverier innan tjänsterna distribueras i produktionen. För att skydda mot risken för data förlust är det viktigt att regelbundet [säkerhetskopiera statusen](service-fabric-reliable-services-backup-restore.md) för alla tillstånds känsliga tjänster till en Geo-redundant lagring. 

Du måste också se till att du har möjlighet att återställa statusen. Eftersom säkerhets kopieringar av många olika tjänster görs vid olika tidpunkter, måste du se till att dina tjänster har en konsekvent vy över varandra efter en återställning. 

Anta till exempel en situation där en tjänst genererar ett nummer och lagrar den och skickar den sedan till en annan tjänst som också lagrar den. Efter en återställning kan du upptäcka att den andra tjänsten har numret, men att det första inte gör det, eftersom säkerhets kopian inte innehåller den åtgärden.

Om du tar reda på att de återstående replikerna inte är tillräckliga för att kunna fortsätta i ett data förlust scenario och du inte kan återskapa tjänst tillstånd från telemetri eller avgas, bestämmer säkerhets kopierings frekvensen det bästa möjliga återställnings punkt målet. Service Fabric innehåller många verktyg för att testa olika typer av avbrott, inklusive permanent kvorum och data förlust som kräver återställning från en säkerhets kopia. De här scenarierna ingår som en del av test verktyg i Service Fabric som hanteras av fel analys tjänsten. Mer information om verktygen och mönstren finns i [Introduktion till fel analys tjänsten](service-fabric-testability-overview.md). 

> [!NOTE]
> System tjänster kan också drabbas av förlust av kvorum. Påverkan är unik för den aktuella tjänsten. Till exempel påverkar kvorumet i namngivnings tjänsten namn matchning, medan kvorum i Redundanshanterarens tjänsten blockerar skapande av nya tjänster och redundans. 
> 
> Service Fabric system tjänster följer samma mönster som dina tjänster för tillstånds hantering, men vi rekommenderar inte att du försöker flytta dem bort från kvorumet och till potentiell data förlust. I stället rekommenderar vi att du  [söker efter support](service-fabric-support.md) för att hitta en lösning som är riktad mot din situation. Det är vanligt vis bättre att bara vänta tills de båda replikerna har returnerats.
>

#### <a name="troubleshooting-quorum-loss"></a>Felsöka kvorum

Repliker kan tillfälligt stängas av på grund av ett tillfälligt haveri. Vänta en stund medan Service Fabric försöker att ta med dem. Om repliker har varit nere i mer än en förväntad varaktighet, följer du dessa fel söknings åtgärder:
- Repliker kan krascha. Kontrol lera hälso rapporter på replik nivå och dina program loggar. Samla in krasch dum par och vidta nödvändiga åtgärder för att återställa.
- Replik processen kanske inte svarar. Kontrol lera program loggarna för att verifiera detta. Samla in process dum par och stoppa sedan den process som inte svarar. Service Fabric skapar en ersättnings process och försöker återställa repliken.
- Noder som är värdar för replikerna kanske inte är igång. Starta om den underliggande virtuella datorn för att flytta noderna.

Ibland kanske det inte går att återställa repliker. Till exempel har enheterna misslyckats eller så svarar inte datorerna fysiskt. I dessa fall måste Service Fabric uppmanas att inte vänta på återställning av repliker.

Använd *inte* dessa metoder om potentiell data förlust är oacceptabelt att ta tjänsten online. I så fall bör alla ansträngningar göras för att återvinna fysiska datorer.

Följande åtgärder kan leda till data förlust. Kontrol lera innan du följer dem.
   
> [!NOTE]
> Det är _aldrig_ säkert att använda dessa metoder än på ett riktat sätt mot specifika partitioner. 
>

- Använd `Repair-ServiceFabricPartition -PartitionId` eller- `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API: et. Med det här API: et kan du ange partitionens ID för att ta bort förlorade kvorum och till potentiell data förlust.
- Om ditt kluster stöter på frekventa fel som gör att tjänster kan hamna i ett kvorum med kvorum och risken för _data förlust är acceptabel_, kan tjänsten automatiskt återställas genom att ange ett lämpligt [QuorumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) -värde. Service Fabric väntar på det angivna `QuorumLossWaitDuration` värdet (Standardvärdet är oändligt) innan återställningen utförs. Vi rekommenderar *inte* den här metoden eftersom den kan orsaka oväntade data förluster.

## <a name="availability-of-the-service-fabric-cluster"></a>Service Fabric klustrets tillgänglighet
I allmänhet är Service Fabric Cluster en mycket distribuerad miljö utan några enskilda fel punkter. Ett fel på en nod kan inte orsaka tillgänglighets-eller Tillförlitlighets problem för klustret, främst på grund av att Service Fabric system tjänster följer samma rikt linjer som tidigare. Det innebär att de alltid körs med tre eller fler repliker som standard, och system tjänster som är tillstånds lösa körs på alla noder. 

Underliggande Service Fabric nätverks-och identifierings lager är fullständigt distribuerade. De flesta system tjänster kan återskapas från metadata i klustret, eller veta hur de synkroniseras om från andra platser. Tillgängligheten för klustret kan bli komprometterad om system Services får problem med kvorum, som de som beskrivs ovan. I dessa fall kanske du inte kan utföra vissa åtgärder i klustret (som att starta en uppgradering eller distribuera nya tjänster), men själva klustret är fortfarande igång. 

Tjänster i ett kluster som körs fortsätter att köras under dessa villkor, om de inte kräver skrivningar till system tjänsterna för att fortsätta att fungera. Om Redundanshanteraren till exempel har förlorat kvorum, kommer alla tjänster att fortsätta att köras. Men inga tjänster som inte kan startas om automatiskt, eftersom detta kräver inblandning av Redundanshanteraren. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Problem i ett Data Center eller en Azure-region
I sällsynta fall kan ett fysiskt Data Center bli tillfälligt otillgängligt från strömavbrott eller nätverks anslutning. I dessa fall kommer Service Fabric kluster och-tjänster i data centret eller Azure-regionen inte vara tillgängliga. _Dina data kommer dock att bevaras_. 

För kluster som körs i Azure kan du Visa uppdateringar om avbrott på [sidan Azure-status][azure-status-dashboard]. Det är mycket osannolikt att ett fysiskt Data Center är delvis eller fullständigt förstört, alla Service Fabric kluster som finns där, eller tjänsterna i dem, kan gå förlorade. Denna förlust omfattar alla tillstånd som inte säkerhets kopie ras utanför data centret eller regionen.

Det finns två olika strategier för att efterlevande varaktigt eller varaktigt strömavbrott i ett enda data Center eller en region: 

- Kör separata Service Fabric kluster i flera sådana regioner och Använd en mekanism för redundans och återställning efter fel mellan dessa miljöer. Den här sorteringen av aktiva/aktiva eller aktiva/passiva modeller med flera kluster kräver ytterligare hanterings-och åtgärds kod. Den här modellen kräver också samordning av säkerhets kopiering från tjänsterna i ett Data Center eller en region så att de är tillgängliga i andra data Center eller regioner när det inte går. 
- Kör ett enda Service Fabric-kluster som sträcker sig över flera data Center eller regioner. Den lägsta konfigurationen som stöds för den här strategin är tre Data Center eller regioner. Det rekommenderade antalet regioner eller data Center är fem. 
  
  Den här modellen kräver en mer komplex kluster sto pol Ogin. Fördelen är dock att ett avbrott i ett Data Center eller en region konverteras från en katastrof till ett normalt haveri. Dessa fel kan hanteras av de mekanismer som fungerar för kluster inom en enda region. Fel domäner, uppgraderings domäner och Service Fabric placerings regler säkerställer att arbets belastningarna distribueras så att de tolererar vanliga fel. 
  
  Mer information om principer som kan hjälpa dig att använda tjänster i den här typen av kluster finns i [placerings principer för Service Fabric Services](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Slumpmässiga fel som leder till kluster fel
Service Fabric har begreppet *startnoder*. Detta är noder som upprätthåller tillgängligheten för det underliggande klustret. 

Med hjälp av dirigeringsrouters kan du se till att klustret förblir igång genom att upprätta lån med andra noder och betjäna som tiebreakers under vissa typer av fel. Om slumpmässiga fel tar bort en majoritet av startnoderna i klustret och inte tas tillbaka snabbt, stängs klustret av automatiskt. Klustret Miss lyckas sedan. 

I Azure hanterar Service Fabric Resource Provider Service Fabric-klusterkonfigurationer. Som standard distribuerar Resource Provider dirigeringsroutrar mellan fel-och uppgraderings domäner för den *primära nodtypen*. Om den primära nodtypen har marker ATS som silver eller guld tålighet, kommer klustret att försöka befordra en annan icke-Seed-nod från den primära nodtypen för den primära nodtypen, när du tar bort en Seed-nod (genom skalning i den primära nodtypen eller genom att ta bort den manuellt). Det här försöket Miss lyckas om du har mindre tillgänglig kapacitet än ditt klusters Tillförlitlighets nivå kräver för den primära nodtypen.

I båda fristående Service Fabric kluster och Azure är den primära nodtypen den som kör frön. När du definierar en typ av primär nod, kommer Service Fabric automatiskt att dra nytta av antalet noder som tillhandahålls genom att skapa upp till nio startnoder och sju repliker av varje system tjänst. Om en uppsättning slumpmässiga felen tar ut en majoritet av dessa repliker samtidigt, kommer system tjänsterna att ange kvorum. Om en majoritet av startnoderna förloras stängs klustret strax efter.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du simulerar olika fel med hjälp av [test ramverket](service-fabric-testability-overview.md).
- Läs andra haveri beredskap och resurser med hög tillgänglighet. Microsoft har publicerat en stor mängd vägledning om dessa ämnen. Även om vissa av dessa resurser refererar till en viss teknik för användning i andra produkter, innehåller de många allmänna metod tips som du kan använda i Service Fabric-kontexten:
  - [Tillgänglighetschecklista](/azure/architecture/checklist/resiliency-per-service)
  - [Utföra en katastrof återställnings granskning](../azure-sql/database/disaster-recovery-drills.md)
  - [Haveriberedskap och hög tillgänglighet för Azure-program][dr-ha-guide]
- Läs mer om [Service Fabric support alternativ](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
