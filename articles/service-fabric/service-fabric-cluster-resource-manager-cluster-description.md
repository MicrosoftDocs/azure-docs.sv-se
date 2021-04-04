---
title: Beskriv ett kluster med hjälp av kluster resurs hanteraren
description: Beskriv ett Service Fabric kluster genom att ange fel domäner, uppgraderings domäner, egenskaper för noden och nodens kapacitet för kluster resurs hanteraren.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92911774"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Beskriv ett Service Fabric kluster med hjälp av kluster resurs hanteraren

Kluster resurs hanterarens funktion i Azure Service Fabric ger flera mekanismer för att beskriva ett kluster:

* Feldomäner
* Uppgradera domäner
* Nodegenskaper
* Nods kapacitet

Under körningen använder kluster resurs hanteraren den här informationen för att säkerställa hög tillgänglighet för de tjänster som körs i klustret. Även om de här viktiga reglerna upprätthålls försöker den också optimera resursanvändningen i klustret.

## <a name="fault-domains"></a>Feldomäner

En feldomän är något av de koordinerade fel områdena. En enskild dator är en fel domän. Den kan Miss lyckas på egen hand av olika orsaker, från strömförsörjnings fel till fel på felaktig NIC-programvara.

Datorer som är anslutna till samma Ethernet-växel finns i samma feldomän. Det är datorer som delar en enda kraft källa eller på en enda plats.

Eftersom den är naturlig för maskin varu fel som överlappar varandra är fel domäner hierarkiska. De representeras som URI: er i Service Fabric.

Det är viktigt att fel domäner konfigureras korrekt eftersom Service Fabric använder den här informationen för att på ett säkert sätt placera tjänster. Service Fabric inte vill placera tjänster så att avbrott i en feldomän (som orsakas av fel i en komponent) gör att en tjänst går nedåt.

I Azure-miljön använder Service Fabric fel domän information som tillhandahålls av miljön för att konfigurera noderna i klustret på rätt sätt. För fristående instanser av Service Fabric definieras fel domäner vid den tidpunkt då klustret konfigureras.

> [!WARNING]
> Det är viktigt att fel domän informationen som tillhandahålls Service Fabric är korrekt. Anta till exempel att Service Fabric klustrets noder körs i 10 virtuella datorer, som körs på 5 fysiska värdar. I det här fallet, även om det finns 10 virtuella datorer, finns det bara fem olika (toppnivå) fel domäner. Att dela samma fysiska värd gör att virtuella datorer delar samma rot Fels domän, eftersom de virtuella datorerna upplever ett koordinerat fel om deras fysiska värden Miss lyckas.  
>
> Service Fabric förväntar sig att fel domänen för en nod inte ska ändras. Andra metoder för att säkerställa hög tillgänglighet för de virtuella datorerna, t. ex. [ha virtuella datorer](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10)), kan orsaka konflikter med Service Fabric. Dessa mekanismer använder transparent migrering av virtuella datorer från en värd till en annan. De omkonfigureras inte eller meddelar den aktiva koden i den virtuella datorn. Därför *stöds de inte* som miljöer för att köra Service Fabric kluster. 
>
> Service Fabric bör vara den enda teknik med hög tillgänglighet som används. Mekanismer som direktmigrering av virtuella datorer och San är inte nödvändiga. Om dessa mekanismer används tillsammans med Service Fabric _minskar_ de programmets tillgänglighet och tillförlitlighet. Anledningen är att de tillför ytterligare komplexitet, lägger till centraliserade källor med problem och använder strategier för tillförlitlighet och tillgänglighet som är i konflikt med dem i Service Fabric.
>
>

I följande bild färgkodar vi alla entiteter som bidrar till fel domäner och listar alla de olika fel domäner som är resultatet. I det här exemplet har vi data Center ("DC"), rack ("R") och blad ("B"). Om varje blad innehåller fler än en virtuell dator kan det finnas ett annat skikt i fel domänens hierarki.

![Noder som är ordnade via fel domäner][Image1]

Under körningen anser Service Fabric Cluster Resource Manager fel domäner i layouterna kluster och planeras. Tillstånds känsliga repliker eller tillstånds lösa instanser för en tjänst distribueras så att de befinner sig i olika fel domäner. Att distribuera tjänsten över fel domäner säkerställer att tjänstens tillgänglighet inte komprometteras när en fel domän Miss lyckas på någon nivå i hierarkin.

Kluster resurs hanteraren bryr sig inte om hur många skikt som finns i fel domänens hierarki. Det görs ett försök att se till att förlusten av en del av hierarkin inte påverkar tjänster som körs i den.

Det är bäst om samma antal noder finns på varje nivå av djup i fel domänens hierarki. Om trädet för fel domäner är obalanserat i klustret är det svårare för kluster resurs hanteraren att ta reda på den bästa fördelningen av tjänster. Obalanserade fel domäner innebär att förlust av vissa domäner påverkar tillgängligheten för tjänster som är mer än andra domäner. Därför är kluster resurs hanteraren avbröts mellan två mål:

* Den vill använda datorerna i den "tungt" domänen genom att placera tjänster på dem. 
* Den vill placera tjänster i andra domäner så att en domän inte kan orsaka problem.

Vad ser de obalanserade domänerna ut? Följande diagram visar två olika klustrade layouter. I det första exemplet fördelas noderna jämnt över fel domänerna. I det andra exemplet har en feldomän många fler noder än de andra fel domänerna.

![Två olika klustrade layouter][Image2]

I Azure kan du välja vilken feldomän som innehåller en nod som hanteras åt dig. Men beroende på antalet noder som du etablerar kan du fortfarande använda fel domäner som har fler noder än i andra.

Anta till exempel att du har fem fel domäner i klustret men etablera sju noder för en nodtyp (**NodeType**). I det här fallet slutförs de två första fel domänerna med fler noder. Om du fortsätter att distribuera fler **NodeType** -instanser med bara ett par instanser blir problemet sämre. Därför rekommenderar vi att antalet noder i varje nodtyp är en multipel av antalet fel domäner.

## <a name="upgrade-domains"></a>Uppgradera domäner

Uppgraderings domäner är en annan funktion som hjälper Service Fabric kluster resurs hanteraren att förstå klustrets layout. Uppgraderings domäner definierar uppsättningar av noder som uppgraderas samtidigt. Uppgraderings domäner hjälper kluster resurs hanteraren att förstå och dirigera hanterings åtgärder som uppgraderingar.

Uppgraderings domäner är mycket som fel domäner, men med ett par viktiga skillnader. Först definierar områden med koordinerade maskin varu fel fel domäner. Uppgraderings domäner, å andra sidan, definieras av en princip. Du bestämmer hur många du vill, i stället för att låta miljön diktera talet. Du kan ha så många uppgraderings domäner som du gör-noder. En annan skillnad mellan fel domäner och uppgraderings domäner är att uppgraderings domäner inte är hierarkiska. I stället är de mer som en enkel tagg.

Följande diagram visar tre uppgraderings domäner stripe över tre fel domäner. Det visar också en möjlig placering för tre olika repliker av en tillstånds känslig tjänst, där var och en är i olika fel-och uppgraderings domäner. Den här placeringen gör det möjligt att förlora en feldomän i mitten av en tjänst uppgradering och fortfarande ha en kopia av koden och data.  

![Placering med fel-och uppgraderings domäner][Image3]

Det finns för-och nack delar med ett stort antal uppgraderings domäner. Flera uppgraderings domäner innebär att varje steg i uppgraderingen är mer detaljerad och påverkar ett mindre antal noder eller tjänster. Färre tjänster måste flyttas i taget, vilket introducerar mindre omsättning i systemet. Detta kan förbättra tillförlitligheten, eftersom mindre av tjänsten påverkas av eventuella problem som införs under uppgraderingen. Flera uppgraderings domäner innebär också att du behöver mindre tillgänglig buffert på andra noder för att hantera effekten av uppgraderingen.

Om du till exempel har fem uppgraderings domäner hanterar noderna i varje ungefär 20 procent av trafiken. Om du behöver ta bort uppgraderings domänen för en uppgradering måste belastningen vanligt vis gå någonstans. Eftersom du har fyra återstående uppgraderings domäner måste var och en ha plats för ca 25 procent av den totala trafiken. Flera uppgraderings domäner innebär att du behöver mindre buffert på noderna i klustret.

Överväg om du har 10 uppgraderings domäner i stället. I så fall hanterar varje uppgraderings domän bara 10 procent av den totala trafiken. När ett uppgraderings steg passerar genom klustret behöver varje domän ha plats för endast ca 11 procent av den totala trafiken. Flera uppgraderings domäner gör det vanligt vis möjligt att köra noderna med högre användning, eftersom du behöver mindre reserverad kapacitet. Samma sak gäller för fel domäner.  

Nack delen med många uppgraderings domäner är att uppgraderingar tenderar att ta längre tid. Service Fabric väntar en kort stund efter att en uppgraderings domän har slutförts och utför kontroller innan de börjar uppgradera nästa. Dessa fördröjningar gör det möjligt att identifiera problem som införts vid uppgraderingen innan uppgraderingen fortsätter. Kompromissen är acceptabel eftersom den förhindrar att felaktiga ändringar påverkar för mycket av tjänsten i taget.

Förekomsten av för få uppgraderings domäner har många negativa sido effekter. Varje uppgraderings domän är nere och uppgraderas, men en stor del av den övergripande kapaciteten är inte tillgänglig. Om du till exempel bara har tre uppgraderings domäner tar du bort en tredjedel av den övergripande tjänst-eller kluster kapaciteten i taget. Eftersom du behöver tillräcklig kapacitet i resten av klustret för att hantera arbets belastningen är det inte önskvärt eftersom du har en mycket stor del av tjänsten. Att underhålla bufferten innebär att de noderna är mindre inlästa än vid normal drift än annars. Detta ökar kostnaden för att köra tjänsten.

Det finns ingen verklig gräns för det totala antalet fel eller uppgraderings domäner i en miljö, eller begränsningar för hur de överlappar varandra. Men det finns vanliga mönster:

* Fel domäner och uppgraderings domäner mappade 1:1
* En uppgraderings domän per nod (fysisk eller virtuell OS-instans)
* En "Striped"-eller "Matrix"-modell där fel domäner och uppgraderings domäner utgör en matris med datorer som vanligt vis kör diagonalerna

![Layouter för fel-och uppgraderings domäner][Image4]

Det finns inget bästa svar för vilken layout som ska väljas. Var och en har för-och nack delar. Till exempel är 1FD: 1UD-modellen enkel att konfigurera. Modellen för en uppgraderings domän per Node-modell är mest likt vad användarna används till. Vid uppgraderingar uppdateras varje nod oberoende av varandra. Detta liknar hur små uppsättningar datorer uppgraderades manuellt tidigare.

Den vanligaste modellen är FD/UD-matrisen där fel domäner och uppgraderings domäner utgör en tabell och noder som placeras med början längs diagonalen. Det här är den modell som används som standard i Service Fabric kluster i Azure. För kluster med många noder, kommer allt att se ut som ett tätare mat ris mönster.

> [!NOTE]
> Service Fabric kluster som finns i Azure har inte stöd för att ändra standard strategin. Endast fristående kluster erbjuder anpassning.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Fel-och uppgraderings domän begränsningar och resulterande beteende
### <a name="default-approach"></a>Standard metod

Som standard håller kluster Resource Manager tjänster balanserade över fel-och uppgraderings domäner. Detta är modellerat som en [begränsning](service-fabric-cluster-resource-manager-management-integration.md). Begränsningen för fel-och uppgraderings domäner tillstånd: "för en specifik tjänstmall bör det aldrig finnas en skillnad som är större än en i antalet tjänst objekt (tillstånds lösa tjänst instanser eller tillstånds känsliga tjänst repliker) mellan två domäner på samma nivå i hierarkin."

Anta att den här begränsningen ger garantin "maximal differens". Begränsningen för fel-och uppgraderings domäner förhindrar vissa flyttningar eller arrangemang som strider mot regeln.

Anta till exempel att vi har ett kluster med sex noder, konfigurerat med fem fel domäner och fem uppgraderings domäner.

|  | FD0 | ADV | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Anta nu att vi skapar en tjänst med en **TargetReplicaSetSize** (eller, för en tillstånds lös tjänst, **InstanceCount**) värde på fem. Replikerna land på N1-N5. I själva verket används N6 aldrig oavsett hur många tjänster som du skapar. Men varför? Nu ska vi titta på skillnaden mellan den aktuella layouten och vad som skulle hända om N6 har valts.

Här är den layout vi fick och det totala antalet repliker per fel-och uppgraderings domän:

|  | FD0 | ADV | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Den här layouten är bal anse rad för noder per fel domän och uppgraderings domän. Det är också balanserade med antalet repliker per fel-och uppgraderings domän. Varje domän har samma antal noder och samma antal repliker.

Nu ska vi titta på vad som skulle hända om vi skulle använda N6 i stället för N2. Hur skulle replikerna distribueras sedan?

|  | FD0 | ADV | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Den här layouten strider mot vår definition av garantin "högsta differens" för fel domän begränsningen. FD0 har två repliker, medan FD1 har noll. Skillnaden mellan FD0 och FD1 är totalt två, vilket är större än den största skillnaden hos en. Eftersom begränsningen kränks tillåter inte kluster resurs hanteraren detta avtal.

Om vi har valt N2 och N6 (i stället för N1 och N2) fick vi:

|  | FD0 | ADV | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Den här layouten är bal anse rad i fel domäner. Men nu bryter det mot uppgraderings domän begränsningen eftersom UD0 har noll repliker och UD1 har två. Den här layouten är också ogiltig och kommer inte att plockas av kluster resurs hanteraren.

Den här metoden för distribution av tillstånds känsliga repliker eller tillstånds lösa instanser ger bästa möjliga fel tolerans. Om en domän stängs av går det minimala antalet repliker/instanser förlorade.

Å andra sidan kan den här metoden vara för strikt och inte tillåta att klustret använder alla resurser. Vissa noder kan inte användas för vissa klusterkonfigurationer. Detta kan orsaka att Service Fabric inte placerar dina tjänster, vilket resulterar i varnings meddelanden. I föregående exempel går det inte att använda några av klusternoderna (N6 i exemplet). Även om du har lagt till noder i klustret (N7-N10) placeras repliker/instanser endast på N1 – N5 på grund av begränsningar i fel-och uppgraderings domäner.

|  | FD0 | ADV | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Alternativ metod

Kluster resurs hanteraren har stöd för en annan version av begränsningen för fel-och uppgraderings domäner. Den tillåter placering samtidigt som du garanterar en minimi nivå av säkerhet. Den alternativa begränsningen kan anges enligt följande: "för en specifik tjänstmall bör replikeringen mellan domäner se till att partitionen inte drabbas av en förlust av kvorum." Anta att den här begränsningen ger garanti för "säker het".

> [!NOTE]
> För en tillstånds känslig tjänst definierar vi *kvorum* i en situation när majoriteten av partitionens repliker är på samma gång. Om **TargetReplicaSetSize** till exempel är fem, representerar en uppsättning av tre repliker kvorum. Om **TargetReplicaSetSize** är sex krävs det också fyra repliker för kvorum. I båda fallen kan högst två repliker köras samtidigt om partitionen vill fortsätta fungera normalt.
>
> För en tillstånds lös tjänst finns det inga saker som att *förlora kvorum*. Tillstånds lösa tjänster fortsätter att fungera normalt även om en majoritet av instanserna går ned på samma gång. Vi fokuserar på tillstånds känsliga tjänster i resten av den här artikeln.
>

Nu ska vi gå tillbaka till föregående exempel. Med "kvorum Safe"-versionen av begränsningen är alla tre layouter giltiga. Även om FD0 misslyckades i den andra layouten eller om UD1 misslyckades i den tredje layouten skulle partitionen fortfarande ha kvorum. (Majoriteten av replikerna skulle fortfarande vara upp.) Med den här versionen av begränsningen kan N6 nästan alltid användas.

"Säker" säker "-metod ger större flexibilitet än metoden" maximal skillnad ". Orsaken är att det är lättare att hitta replik distributioner som är giltiga i nästan alla kluster sto pol Ogin. Den här metoden kan dock inte garantera de bästa fel tolerans egenskaperna eftersom vissa fel är sämre än andra.

I värsta fall kan en majoritet av replikerna gå förlorad om det uppstår ett problem med en domän och en ytterligare replik. I stället för att tre försök krävs för att förlora kvorum med fem repliker eller instanser, kan du nu förlora en majoritet med bara två problem.

### <a name="adaptive-approach"></a>Anpassningsbar metod

Eftersom båda metoderna har starka sidor och svagheter har vi infört en anpassningsbar metod som kombinerar dessa två strategier.

> [!NOTE]
> Detta är standard beteendet från och med Service Fabric version 6,2.
>
> Den anpassningsbara metoden använder sig av logiken "Max skillnad" som standard och växlar till "säker" säker "logik endast när det behövs. Kluster resurs hanteraren tar automatiskt upp vilka strategier som är nödvändiga genom att titta på hur klustret och tjänsterna har kon figurer ATS.
>
> Kluster resurs hanteraren ska använda "kvorum"-logiken för en tjänst båda dessa villkor är uppfyllda:
>
> * **TargetReplicaSetSize** för tjänsten är jämnt delbar med antalet fel domäner och antalet uppgraderings domäner.
> * Antalet noder är mindre än eller lika med antalet fel domäner multiplicerat med antalet uppgraderings domäner.
>
> Tänk på att kluster resurs hanteraren kommer att använda den här metoden för både tillstånds lösa och tillstånds känsliga tjänster, även om kvorumet inte är relevant för tillstånds lösa tjänster.

Vi går tillbaka till föregående exempel och förutsätter att ett kluster nu har åtta noder. Klustret är fortfarande konfigurerat med fem fel domäner och fem uppgraderings domäner och **TargetReplicaSetSize** -värdet för en tjänst som är värd för det klustret förblir fem.

|  | FD0 | ADV | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Eftersom alla nödvändiga villkor är uppfyllda använder kluster resurs hanteraren logiken "kvorum" för att distribuera tjänsten. Detta aktiverar användningen av N6-N8. En möjlig tjänste distribution i det här fallet kan se ut så här:

|  | FD0 | ADV | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Om tjänstens **TargetReplicaSetSize** -värde minskas till fyra (till exempel), kommer kluster resurs hanteraren att notera ändringen. Den återupptas med hjälp av logiken "Max skillnad" eftersom **TargetReplicaSetSize** inte kan delas av antalet fel domäner och uppgraderings domäner längre. Därför sker vissa replikeringar för att distribuera de återstående fyra replikerna på noderna N1-N5. På så sätt bryter inte versionen av den högsta differensen för fel domänen och uppgraderings domän logiken.

I föregående layout, om värdet **TargetReplicaSetSize** är fem och N1 tas bort från klustret, blir antalet uppgraderings domäner lika med fyra. Kluster resurs hanteraren börjar med att använda "största differens"-logik eftersom antalet uppgraderings domäner inte ens delar tjänstens **TargetReplicaSetSize** -värde längre. Det innebär att repliken R1, när den skapades igen, har för att landa på N4 så att begränsningen för fel-och uppgraderings domänen inte överskrids.

|  | FD0 | ADV | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |Saknas |Saknas |Saknas |Saknas |Saknas |Saknas |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurera fel-och uppgraderings domäner

I Azure-värdbaserade Service Fabric distributioner definieras fel domäner och uppgraderings domäner automatiskt. Service Fabric hämtar och använder miljö informationen från Azure.

Om du skapar ett eget kluster (eller vill köra en viss topologi i utvecklingen) kan du själv ange feldomänen och uppgradera domän informationen. I det här exemplet definierar vi ett lokalt utvecklings kluster på nio noder som omfattar tre Data Center (vart och ett med tre rack). Det här klustret har också tre uppgraderings domäner stripe i de tre data centren. Här är ett exempel på konfigurationen i ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

I det här exemplet används ClusterConfig.jsför för fristående distributioner:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> När du definierar kluster via Azure Resource Manager tilldelar Azure fel domäner och uppgraderings domäner. Definitionen av nodtypen och skalnings uppsättningar för virtuella datorer i din Azure Resource Manager-mall innehåller inte heller information om fel domän eller uppgraderings domän.
>

## <a name="node-properties-and-placement-constraints"></a>Egenskaper för Node och placerings begränsningar

Ibland (i själva verket) vill du förmodligen se till att vissa arbets belastningar bara körs på vissa typer av noder i klustret. Vissa arbets belastningar kan till exempel kräva GPU: er eller SSD, och andra kanske inte.

Ett bra exempel på att rikta maskin vara till vissa arbets belastningar är nästan alla n-nivåers arkitektur. Vissa datorer fungerar som klient delen eller API-betjäna sidan av programmet och exponeras för klienter eller Internet. Olika datorer, ofta med olika maskin varu resurser, hanterar arbetet för beräknings-eller lagrings skikten. Dessa är vanligt vis _inte_ direkt exponerade för klienter eller Internet.

Service Fabric förväntar sig i vissa fall att vissa arbets belastningar kan behöva köras på specifika maskinvarukonfigurationer. Exempel:

* Ett befintligt n-Nivåprogram har "lyfts upp och flyttats" till en Service Fabric-miljö.
* En arbets belastning måste köras på speciell maskin vara för prestanda-, skalnings-eller säkerhets isolerings skäl.
* En arbets belastning bör isoleras från andra arbets belastningar för princip-eller Resursanvändning.

För att stödja dessa typer av konfigurationer, innehåller Service Fabric taggar som du kan tillämpa på noder. Dessa taggar kallas för *Node-egenskaper*. *Placerings begränsningar* är de instruktioner som är kopplade till enskilda tjänster som du väljer för en eller flera Node-egenskaper. Placerings begränsningar definierar var tjänsterna ska köras. Uppsättningen med begränsningar är utöknings bar. Alla nyckel/värde-par kan fungera.

![Olika arbets belastningar för en kluster layout][Image5]

### <a name="built-in-node-properties"></a>Inbyggda Node-egenskaper

Service Fabric definierar vissa standardnode-egenskaper som kan användas automatiskt så att du inte behöver definiera dem. Standard egenskaperna som definieras på varje nod är **NodeType** och **nodnamn**.

Du kan till exempel skriva en placerings begränsning som `"(NodeType == NodeType03)"` . **NodeType** är en egenskap som används ofta. Det är användbart eftersom det motsvarar 1:1 med en typ av dator. Varje typ av dator motsvarar en typ av arbets belastning i ett traditionellt program på n-nivå.

![Placerings begränsningar och egenskaper för nod][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Placerings begränsningar och syntax för Node-egenskapen

Värdet som anges i egenskapen Node kan vara en sträng, boolesk eller signerat. Instruktionen på tjänsten kallas för en placerings *begränsning* eftersom den är begränsad där tjänsten kan köras i klustret. Begränsningen kan vara vilken boolesk instruktion som helst som fungerar på nodens egenskaper i klustret. Giltiga väljare i dessa booleska uttryck är:

* Villkorliga kontroller för att skapa särskilda uttryck:

  | Uttryck | Syntax |
  | --- |:---:|
  | "lika med" | "==" |
  | "inte lika med" | "!=" |
  | "större än" | ">" |
  | "större än eller lika med" | ">=" |
  | "mindre än" | "<" |
  | "mindre än eller lika med" | "<=" |

* Booleska instruktioner för gruppering och logiska åtgärder:

  | Uttryck | Syntax |
  | --- |:---:|
  | särskilt | "&&" |
  | eller | "&#124;&#124;" |
  | Ogiltigt | "!" |
  | "Gruppera som en enda instruktion" | "()" |

Här följer några exempel på Basic constraint-uttryck:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Endast noder där den övergripande placerings begränsnings instruktionen utvärderas till "true" kan ha tjänsten placerad. Noder som inte har en definierad egenskap matchar inte någon placerings begränsning som innehåller egenskapen.

Anta att följande Node-egenskaper har definierats för en nodtyp i ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

I följande exempel visas Node-egenskaper som definierats via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster.

> [!NOTE]
> I din Azure Resource Manager-mall är nodtypen vanligt vis parameterstyrda. Det skulle se ut `"[parameters('vmNodeType1Name')]"` snarare än NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Du kan skapa service placerings *begränsningar* för en tjänst på följande sätt:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Om alla noder i NodeType01 är giltiga kan du också välja nodtypen med begränsningen `"(NodeType == NodeType01)"` .

En tjänsts placerings begränsningar kan uppdateras dynamiskt under körning. Om du behöver kan du flytta en tjänst runt i klustret, lägga till och ta bort krav och så vidare. Service Fabric säkerställer att tjänsten förblir igång och tillgänglig även när dessa typer av ändringar görs.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Placerings begränsningar anges för varje namngiven tjänst instans. Uppdateringarna ersätter alltid (skriver över) det som tidigare angavs.

Kluster definitionen definierar egenskaperna för en nod. Att ändra en nods egenskaper kräver en uppgradering till kluster konfigurationen. Att uppgradera en nods egenskaper kräver att varje nod har startat om för att rapportera de nya egenskaperna. Service Fabric hanterar dessa löpande uppgraderingar.

## <a name="describing-and-managing-cluster-resources"></a>Beskriva och hantera kluster resurser

En av de viktigaste jobben i alla Orchestrator är att hjälpa till med att hantera resursförbrukning i klustret. Att hantera kluster resurser kan innebära ett par olika saker.

Först ser du till att datorer inte är överbelastade. Det innebär att se till att datorer inte kör fler tjänster än de kan hantera.

För det andra är det en balans och optimering som är avgörande för att köra tjänster effektivt. Kostnads effektiva eller prestanda känsliga tjänst erbjudanden kan inte tillåta att vissa noder är aktiva medan andra är kall. Aktiva noder leder till resurs konkurrens och dåliga prestanda. Kalla noder representerar slöseri med resurser och ökade kostnader.

Service Fabric representerar resurser som *mått*. Mått är alla logiska eller fysiska resurser som du vill beskriva till Service Fabric. Exempel på mått är "WorkQueueDepth" eller "MemoryInMb". Information om de fysiska resurser som Service Fabric kan styra på noder finns i [resurs styrning](service-fabric-resource-governance.md). Information om standard måtten som används av kluster resurs hanteraren och hur du konfigurerar anpassade mått finns i [den här artikeln](service-fabric-cluster-resource-manager-metrics.md).

Mått skiljer sig från placerings begränsningar och Node-egenskaper. Node-egenskaperna är statiska beskrivningar av noderna. Mått beskriver resurser som noderna har och som tjänsterna använder när de körs på en nod. En Node-egenskap kan vara **HasSSD** och kan ställas in på True eller false. Mängden utrymme som är tillgängligt för den SSD och hur mycket som konsumeras av tjänster är ett mått som "DriveSpaceInMb".

Precis som för placerings begränsningar och Node-egenskaper kan Service Fabric Cluster Resource Manager inte förstå vad namnen på måtten betyder. Mått namn är bara strängar. Det är en bra idé att deklarera enheter som en del av de mått namn som du skapar när de kan vara tvetydiga.

## <a name="capacity"></a>Kapacitet

Om du har inaktiverat all resurs *utjämning* ser Service Fabric kluster resurs hanteraren fortfarande till att ingen nod går över sin kapacitet. Det går att hantera kapacitets överskridningar om inte klustret är fullt eller om arbets belastningen är större än en nod. Kapaciteten är en annan *begränsning* som kluster resurs hanteraren använder för att förstå hur mycket av en resurs en nod har. Återstående kapacitet spåras också för klustret som helhet.

Både kapaciteten och förbrukningen på service nivån uttrycks som mått. Måttet kan till exempel vara "ClientConnections" och en nod kan ha en kapacitet för "ClientConnections" på 32 768. Andra noder kan ha andra gränser. En tjänst som körs på noden kan säga att den för närvarande konsumerar 32 256 av måttet "ClientConnections".

Under körningen spårar kluster resurs hanteraren återstående kapacitet i klustret och på noder. För att spåra kapaciteten, drar kluster resurs hanteraren varje tjänsts användning från en nods kapacitet där tjänsten körs. Med den här informationen kan kluster resurs hanteraren ta reda på var du ska placera eller flytta repliker, så att noderna inte översätts över kapacitet.

![Klusternoder och kapacitet][Image7]

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Du kan se kapaciteter som definierats i kluster manifestet. Här är ett exempel på ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Här är ett exempel på kapaciteter som definieras via ClusterConfig.jspå för fristående distributioner eller Template.jspå för Azure-värdbaserade kluster:

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Belastningen på en tjänst ändras ofta dynamiskt. Anta att en repliks belastning på "ClientConnections" har ändrats från 1 024 till 2 048. Noden som den kördes på hade sedan en kapacitet på 512 kvar för det måttet. Nu är repliken eller instansens placering ogiltig, eftersom det inte finns tillräckligt med utrymme på noden. Kluster resurs hanteraren måste återställa noden nedan. Det minskar belastningen på den nod som är över-kapacitet genom att flytta en eller flera av replikerna eller instanserna från noden till andra noder.

Kluster resurs hanteraren försöker minimera kostnaderna för att flytta repliker. Du kan lära dig mer om [rörelse kostnad](service-fabric-cluster-resource-manager-movement-cost.md) och att [ombalansera strategier och regler](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kluster kapacitet

Hur låter Service Fabric kluster resurs hanteraren det övergripande klustret vara för fullt? Med dynamisk belastning finns det ingen mängd det kan göra. Tjänster kan ha sin belastnings topp oberoende av åtgärder som kluster resurs hanteraren tar. Därför kan ditt kluster med mycket utrymme idag bli underskrivet om det finns en topp i morgon.

Med kontrollerna i Cluster Resource Manager kan du förhindra problem. Det första du kan göra är att inte skapa nya arbets belastningar som gör att klustret blir fullt.

Anta att du skapar en tillstånds lös tjänst och att den har en viss belastning som är kopplad till den. Tjänsten värnar om måttet "DiskSpaceInMb". Tjänsten använder fem enheter av "DiskSpaceInMb" för varje instans av tjänsten. Du vill skapa tre instanser av tjänsten. Det innebär att du behöver 15 enheter om "DiskSpaceInMb" ska finnas i klustret för att du ska kunna skapa dessa tjänst instanser.

Kluster resurs hanteraren beräknar kontinuerligt kapaciteten och användningen av varje mått så att det kan fastställa den återstående kapaciteten i klustret. Om det inte finns tillräckligt med utrymme avvisar kluster resurs hanteraren anropet för att skapa en tjänst.

Eftersom kravet bara är att 15 enheter ska vara tillgängliga kan du allokera det här utrymmet på många olika sätt. Det kan till exempel finnas en återstående kapacitets enhet på 15 olika noder eller tre återstående kapacitets enheter på fem olika noder. Om kluster resurs hanteraren kan arrangera om saker så att det finns fem enheter tillgängliga på tre noder, placerar tjänsten tjänsten. Att ordna om klustret är vanligt vis möjligt om klustret är nästan fullt eller om de befintliga tjänsterna inte kan konsol IDE ras av någon anledning.

## <a name="node-buffer-and-overbooking-capacity"></a>Node buffer och överboknings kapacitet

Om en nods kapacitet för ett mått anges kommer kluster resurs hanteraren aldrig att placera eller flytta repliker till en nod om den totala belastningen skulle gå över den angivna nodens kapacitet. Detta kan ibland förhindra placering av nya repliker eller ersätta misslyckade repliker om klustret är nära full kapacitet och en replik med stor belastning måste placeras, ersättas eller flyttas.

För att tillhandahålla mer flexibilitet kan du ange antingen Node buffer eller för att boka kapaciteten. När en Node buffer eller disbooking-kapacitet har angetts för ett mått, försöker kluster resurs hanteraren att placera eller flytta repliker på ett sådant sätt att bufferten eller den sammanställda kapaciteten fortfarande används, men tillåter att bufferten eller den sammanställda kapaciteten används vid behov för åtgärder som ökar tjänstens tillgänglighet, till exempel:

* Ny replik placering eller ersätta misslyckade repliker
* Placering under uppgraderingar
* Korrigering av mjuka och hårda begränsnings överträdelser
* Defragmentering

Buffertens buffert är en reserverad del av kapaciteten under angiven nod kapacitet och överordnad kapacitet är en del av extra kapacitet utöver den angivna nodens kapacitet. I båda fallen kommer kluster resurs hanteraren att försöka hålla den här kapaciteten kostnads fri.

Om en nod till exempel har en angiven kapacitet för mått *CpuUtilization* på 100 och procent andelen buffert för det måttet har värdet 20%, kommer total och icke-buffrad kapacitet att vara 100 respektive 80 respektive kluster resurs hanteraren placerar inte mer än 80 inläsnings enheter på noden under normala förhållanden.

![Total kapacitet är lika med Node-kapacitet (Node buffer + unbuffed)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

Node buffer ska användas när du vill reservera en del av Node-kapaciteten som endast ska användas för åtgärder som ökar tjänstens tillgänglighet.

Å andra sidan, om procent andelen av noderna används och är inställd på 20% blir summan och de buffrade kapaciteten 120 respektive 100.

![Total kapacitet är lika med överboknings kapacitet plus Node-kapacitet (överbokning + unbuffed)](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

Överboknings kapacitet ska användas när du vill tillåta att kluster resurs hanteraren placerar repliker på en nod även om deras totala resursanvändning skulle överskrida kapaciteten. Detta kan användas för att tillhandahålla ytterligare tillgänglighet för tjänster med kostnad för prestanda. Om överbokning används måste användar program logiken kunna fungera med färre fysiska resurser än vad som krävs.

Om du har angett Node buffer eller om du har angett fler funktioner, kommer kluster resurs hanteraren inte att flytta eller placera repliker om den totala belastningen på målnoden skulle gå över total kapacitet (Node-kapacitet i händelse av Node-buffert och nod kapacitet + överboknings kapacitet vid överbokning).

Överboknings kapacitet kan också anges som oändligt. I det här fallet kommer kluster resurs hanteraren att försöka behålla den totala belastningen på noden under den angivna noden, men kan placera en mycket större belastning på noden som kan leda till allvarlig prestanda försämring.

Det går inte att använda både Node buffer och den aktuella kapaciteten för en mått på samma tidpunkt.

Här är ett exempel på hur du anger Node buffer eller för att boka kapaciteter i *ClusterManifest.xml*:

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Här är ett exempel på hur du anger Node buffer eller för att boka kapaciteter via *ClusterConfig.jspå* för fristående distributioner eller *Template.jspå* för Azure-värdbaserade kluster:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om arkitekturen och informations flödet i kluster resurs hanteraren finns i [Översikt över kluster Resource Manager-arkitektur](service-fabric-cluster-resource-manager-architecture.md).
* Att definiera defragmentering-mått är ett sätt att konsolidera belastningen på noderna i stället för att sprida ut det. Information om hur du konfigurerar defragmentering finns i [defragmentering av mått och belastning i Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Börja från början och [få en introduktion till Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Information om hur kluster resurs hanteraren hanterar och balanserar belastning i klustret finns i [utjämna Service Fabric klustret](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
