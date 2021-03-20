---
title: Kör distributions planeraren för Hyper-V i Azure Site Recovery
description: Den här artikeln beskriver hur du kör Distributionshanteraren för Azure Site Recovery för haveri beredskap för Hyper-V till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082606"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Köra Distributionshanteraren för Azure Site Recovery för Hyper-V-haveriberedskap till Azure

Du kan köra kommando rads verktyget Site Recovery Deployment Planner (ASRDeploymentPlanner.exe) i något av dessa fyra lägen: 
-   Hämta listan med virtuella datorer (VM)
-   [Profil](#profile-hyper-v-vms)
-   Generera en rapport
-   [Beräkna dataflöde](#get-throughput)

Kör först verktyget för att hämta listan över virtuella datorer från en enda eller flera Hyper-V-värdar. Sedan kör du verktyget i profileringsläge för att samla in uppgifter om den virtuella datorns dataomsättning och IOPS. Kör sedan verktyget för att generera rapporten för att bedöma kraven på nätverksbandbredd och lagring.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Hämta VM-listan för att profilera virtuella Hyper-V-datorer
Du behöver först en lista över de virtuella datorer som ska profileras. Använd GetVMList-läget för distributionsplanerarverktyget för att generera listan över de virtuella datorer som finns på flera Hyper-V-värdar med ett enda kommando. När du har genererat hela listan kan du ta bort virtuella datorer som du inte vill profilera från utdatafilen. Använd sedan utdatafilen för alla andra åtgärder: profilera, generera rapporter och hämta dataflöde.

Du kan generera listan över virtuella datorer genom att peka verktyget mot ett Hyper-V-kluster eller en fristående Hyper-V-värd eller en kombination av båda.

### <a name="command-line-parameters"></a>Kommandoradsparametrar
Följande tabell innehåller en lista med obligatoriska och valfria verktygsparametrar som ska köras i GetVMList-läget. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Parameternamn | Beskrivning |
|---|---|
| -Operation | GetVMList |
| -User | Användarnamnet för att ansluta till Hyper-V-värden eller Hyper-V-klustret. Användaren måste ha administratörsbehörighet.|
| -ServerListFile | Filen med listan över servrar som innehåller de virtuella datorer som ska profileras. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla något av följande på varje rad:<ul><li>Hyper-V-värdnamn eller IP-adress</li><li>Hyper-V-klusternamn eller IP-adress</li></ul><br>**Exempel:** Filen ServerList.txt innehåller följande servrar:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Valfritt) UNC (Universal Naming Convention) eller lokal katalogsökväg för lagring av de data som genereras under åtgärden. Om inget namn är angivet används katalogen ProfiledData under den aktuella sökvägen som standardkatalog.|
|-OutputFile| Valfritt Filen med listan över virtuella datorer som hämtats från Hyper-V-servrarna har sparats. Om inget namn inte nämns lagras informationen i VMList.txt.  Använd filen för att börja profileringen när du har tagit bort virtuella datorer som inte behöver profileras.|
|-Password|(Valfritt) Lösenordet för att ansluta till Hyper-V-värden. Om du inte anger lösenordet som en parameter uppmanas du att ange det när du kör kommandot.|

### <a name="getvmlist-discovery"></a>GetVMList-identifiering

- **Hyper-V-kluster**: När Hyper-V-klusternamnet är angivet i serverns listfil hittar verktyget alla Hyper-V-noder för klustret och hämtar de virtuella datorer som finns på varje Hyper-V-värd.
**Hyper-V-värd**: När Hyper-V-värdnamnet är angivet kontrollerar verktyget först om det tillhör något kluster. Om ja, hämtar verktyget noder som tillhör klustret. Det hämtar sedan de virtuella datorerna från varje Hyper-V-värd. 

Du kan också välja att lista de egna namnen eller IP-adresserna till de virtuella datorer som du vill profilera manuellt i en fil.

Öppna utdatafilen i Anteckningar och kopiera sedan namnen på alla virtuella datorer som du vill profilera till en annan fil (till exempel ProfileVMList.txt). Använd ett namn på virtuell dator per rad. Filen används som indata till verktygets parameter -VMListFile för alla andra åtgärder: profilera, generera rapporter och hämta dataflöde.

### <a name="examples"></a>Exempel

#### <a name="store-the-list-of-vms-in-a-file"></a>Lagra listan med virtuella datorer i en fil
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Lagra listan med virtuella datorer på standardplatsen (-Directory path)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Profilera virtuella Hyper-V-datorer
I profileringsläge ansluter distributionskapacitetsplaneraren till varje Hyper-V-värd för att samla in prestandadata om de virtuella datorerna. 

Profilering påverkar inte prestanda hos de virtuella produktionsdatorerna eftersom ingen direktanslutning upprättas till dem. Alla prestandadata samlas in från Hyper-V-värden.

Verktyget frågar Hyper-V-värden var 15: e sekund för att säkerställa att profilering blir korrekt. Medelvärdet för varje minuts prestandaräknardata lagras.

Verktyget hanterar smidigt VM-migrering från en nod till en annan i klustret och lagringsmigreringen inom en värd.

### <a name="getting-the-vm-list-to-profile"></a>Hämta VM-listan att profilera
Referera till åtgärden GetVMList för att skapa en lista över virtuella datorer att profilera.

När du har skapat listan med virtuella datorer att profilera kan du köra verktyget i profileringsläge. 

### <a name="command-line-parameters"></a>Kommandoradsparametrar
Följande tabell visar en lista med obligatoriska och valfria verktygsparametrar som ska köras i profileringsläget. Verktyget är vanligt för scenarier om att flytta från VMware till Azure och att flytta från Hyper-V till Azure. De här parametrarna används för Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parameternamn | Beskrivning |
|---|---|
| -Operation | StartProfiling |
| -User | Användarnamnet för att ansluta till Hyper-V-värden eller Hyper-V-klustret. Användaren måste ha administratörsbehörighet.|
| -VMListFile | Filen som innehåller listan med virtuella datorer som ska profileras. Filsökvägen kan vara absolut eller relativ. För Hyper-V är den här filen utdatafilen för åtgärden GetVMList. Om du förbereder manuellt ska filen innehålla ett servernamn eller en IP-adress följt av VM-namnet (angränsat med ett \ per rad). Det VM-namn som angetts i filen ska vara samma som VM-namnet på Hyper-V-värden.<br><br>**Exempel:** VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Antal minuter som profileringen ska köras. Minimum är 30 minuter.|
|-NoOfHoursToProfile|Antal timmar som profileringen ska köras.|
|-NoOfDaysToProfile |Antal dagar som profileringen ska köras. Vi rekommenderar att du kör profileringen i mer än 7 dagar. När den körs så länge fångar du upp mönster för arbetsbelastningen i din miljö och kan generera korrekta rekommendationer.|
|-Virtualization|Visualiseringstypen (VMware eller Hyper-V).|
|-Directory|(Valfritt) UNC eller lokal sökväg för lagring av de profildata som genereras under profileringen. Om inget namn anges används katalogen ProfiledData under den aktuella sökvägen som standardkatalog.|
|-Password|(Valfritt) Lösenordet för att ansluta till Hyper-V-värden. Om du inte anger lösenordet som en parameter uppmanas du att ange det när du kör kommandot.|
|-StorageAccountName|(Valfritt) Namnet på det lagringskonto som används för beräkning av dataflödet som kan uppnås för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när dataflödet ska beräknas. Lagringskontot måste vara av typen generell användning v1 (GPv1).|
|-StorageAccountKey|(Valfritt) Den nyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > **lagrings konton**  >  *lagrings kontots namn*  >  **Inställningar**  >  **åtkomst nycklar**  >  **KEY1** (eller den primära åtkomst nyckeln för ett klassiskt lagrings konto).|
|-Environment|(Valfritt) Målmiljön för Azure Storage-kontot. Det kan vara ett av tre värden: AzureCloud, AzureUSGovernment eller AzureChinaCloud. Standardvärdet är AzureCloud. Använd parametern när din mål region är antingen Azure amerikanska myndigheter eller Azure Kina 21Vianet.|

Vi rekommenderar att du profilerar dina virtuella datorer under minst 7 dagar. Om omsättningsmönstret varierar under en månad rekommenderar vi att du profilerar under veckan när omsättningen är som störst. Det bästa är att profilera i 31 dagar för att få en bättre rekommendation. 

ASRDeploymentPlanner.exe körs under hela profileringsperioden. Profileringstiden anges i dagar i verktyget. För ett snabbtest av verktyget eller ett konceptbevis kan du profilera i några timmar eller minuter. Minsta tillåtna profileringstid är 30 minuter. 

Under profileringen kan du välja att skicka namn och nyckel för ett lagringskonto om du vill se vilket dataflöde som Azure Site Recovery kan uppnå för replikeringen från Hyper-V-servern till Azure. Om du inte skickar namn och nyckel för ett lagringskonto under profileringen beräknar inte verktyget det dataflöde som kan uppnås.

Du kan köra flera instanser av verktyget för olika uppsättningar av virtuella datorer. Se till att det inte finns dubbletter av namn på virtuella datorer i profileringsuppsättningarna. Vi kan till exempel säga att du har profilerat 10 virtuella datorer (VM1 till VM10). Efter några dagar vill du profilera ytterligare 5 virtuella datorer (VM11 till VM15). Du kan köra verktyget från en annan kommandoradskonsol för den andra uppsättningen virtuella datorer (VM11 till VM15). 

Se till att den andra uppsättningen virtuella datorer inte innehåller något av datornamnen från den första profileringsinstansen, eller använd en annan utdatakatalog för den andra körningen. Om två instanser av verktyget används för profilering av samma virtuella datorer och samma utdatakatalog används kommer den genererade rapporten att vara felaktig. 

Som standard är verktyget konfigurerat att profilera och generera rapporter för upp till 1 000 virtuella datorer. Du kan ändra gränsen genom att ändra nyckelvärdet MaxVMsSupported i filen ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Om du vill skapa till exempel 1 500 virtuella datorer med standardinställningarna ska du skapa två VMList.txt-filer. En med 1 000 virtuella datorer och en annan med 500 virtuella datorer. Kör de två instanserna av distributionsplaneraren för Azure Site Recovery: en med VMList1.txt och en med VMList2.txt. Du kan använda samma katalogsökväg för att lagra profilerade data för de båda virtuella VMList-datorerna. 

Åtgärden kan misslyckas med otillräckligt minne beroende på maskinvarukonfigurationen (i synnerhet RAM-storleken) på servern varifrån verktyget körs för att generera rapporten. Om du har bra maskinvara kan du ändra MaxVMsSupported till ett högre värde.  

Den virtuella datorns konfiguration inhämtas en gång i början av profileringsåtgärden och lagras i en fil med namnet VMDetailList.xml. Den här informationen används när rapporten genereras. Ändringar i den virtuella datorkonfigurationen (till exempel ett ökat antal kärnor, diskar och nätverkskort) från början till slutet av profileringen registreras inte. Om konfigurationen för en profilerad virtuell dator ändrats under profileringen finns det en lösning för att hämta den senaste virtuella datorinformationen när rapporten ska genereras:

* Säkerhetskopiera filen VMdetailList.xml och ta bort den från dess nuvarande plats.
* Skicka argumenten -User och -Password när rapporten ska genereras.

Profileringskommandot genererar flera filer i profileringskatalogen. Ta inte bort några filer, eftersom detta påverkar rapportgenereringen.

### <a name="examples"></a>Exempel

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Profilera virtuella datorer i 30 dagar och beräkna dataflödet från lokala datorer till Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Profilera virtuella datorer i 15 dagar
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Profilera virtuella datorer i 60 minuter för ett snabbtest av verktyget
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Profilera virtuella datorer i 2 timmar för ett konceptbevis
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Överväganden vid profilering

Om servern där verktyget körs startas om eller kraschar, eller om du avslutar verktyget med Ctrl + C, bevaras profileringsdata. Du riskerar dock att förlora de senaste 15 minuternas profileringsdata. I ett sådant fall måste du köra om verktyget i profileringsläge när servern startas igen.

När du skickar namn och nyckel för ett lagringskonto mäter verktyget dataflödet vid profileringens sista steg. Om verktyget avslutas innan profileringen har slutförts normalt beräknas inte dataflödet. Du kan hitta dataflödet innan du genererar rapporten genom att köra åtgärden GetThroughput från kommandotolken. Annars innehåller inte den genererade rapporten dataflödesinformationen.

Azure Site Recovery stöder inte virtuella datorer som har iSCSI-och direkt lagrings diskar. Verktyget kan inte identifiera och profilera iSCSI-och direkt lagrings diskar som är anslutna till virtuella datorer.

## <a name="generate-a-report"></a>Generera en rapport
Verktyget genererar en makroaktiverad Microsoft Excel-fil (XLSM) som rapportutdata. Där sammanfattas alla distributionsrekommendationer. Rapporten heter DeploymentPlannerReport_ *unik numerisk identifierare*. xlsm och placeras i den angivna katalogen.

När profileringen är färdig kan köra du verktyget i läget för rapportgenerering. 

### <a name="command-line-parameters"></a>Kommandoradsparametrar
Följande tabell innehåller en lista med obligatoriska och valfria verktygsparametrar som ska köras i läget för rapportgenerering. Verktyget är vanligt för att flytta från VMware till Azure och att flytta från Hyper-V till Azure. Följande parametrar kan användas för Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Parameternamn | Beskrivning |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Den fil som innehåller listan över profilerade virtuella datorer som rapporten ska genereras för. Filsökvägen kan vara absolut eller relativ. För Hyper-V är den här filen utdatafilen för åtgärden GetVMList. Om du förbereder manuellt ska filen innehålla ett servernamn eller en IP-adress följt av VM-namnet (angränsat med ett \ per rad). Det VM-namn som angetts i filen ska vara samma som VM-namnet på Hyper-V-värden.<br><br>**Exempel:** VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|Visualiseringstypen (VMware eller Hyper-V).|
|-Directory|(Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om inget namn anges används katalogen ProfiledData under den aktuella sökvägen som standardkatalog.|
| -User | (Valfritt) Användarnamnet för att ansluta till Hyper-V-värden eller Hyper-V-klustret. Användaren måste ha administratörsbehörighet. Användare och lösenord används till att hämta den senaste konfigurationsinformationen för de virtuella datorerna (som antal diskar, antal kärnor och antal nätverkskort) som ska användas i rapporten. Om det här värdet inte anges så används den konfigurationsinformation som samlades in under profileringen.|
|-Password|(Valfritt) Lösenordet för att ansluta till Hyper-V-värden. Om du inte anger lösenordet som en parameter uppmanas du att ange det när du kör kommandot.|
| -DesiredRPO | (Valfritt) Önskat mål för återställningspunkt (RPO) i minuter. Standardvärdet är 15 minuter.|
| -Bandwidth | (Valfritt) Bandbredd i megabit per sekund. Använd den här parametern för att beräkna vilken RPO som kan uppnås för den angivna bandbredden. |
| -StartDate | (Valfritt) Startdatum och starttid i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). StartDate måste anges tillsammans med EndDate. När StartDate anges genereras rapporten för de profileringsdata som samlats in mellan StartDate och EndDate. |
| -EndDate | (Valfritt) Slutdatum och sluttid i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). EndDate måste anges tillsammans med StartDate. När du anger EndDate genereras rapporten för profileringsdata som samlats in mellan StartDate och EndDate. |
| -GrowthFactor | (Valfritt) Tillväxtfaktor, uttryckt i procent. Standardvärdet är 30 procent. |
| -UseManagedDisks | (Valfritt) UseManagedDisks: Ja/Nej. Standardinställningen är Ja. Antalet virtuella datorer som kan placeras i ett enda lagringskonto beräknas utifrån om redundans/redundanstest för virtuella datorer görs på en hanterad disk istället för en ohanterad disk. |
|-SubscriptionId |(Valfritt) GUID för prenumerationen. Använd den här parametern för att generera rapporten Kostnadsuppskattning med det senaste priset baserat på din prenumeration, erbjudandet som är associerat med prenumerationen och Azure-målregionen i den angivna valutan.|
|-TargetRegion|(Valfritt) Azure-regionen som är mål för replikeringen. Eftersom Azure har olika kostnader för olika regioner ska du använda den här parametern för att generera en rapport för en specifik Azure-region. Standard är Västra USA 2 eller den senast använda målregionen. Läs mer i listan med [målregioner som stöds](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Valfritt) Erbjudandet som är associerat med prenumerationen. Standard är MS-AZR-0003P (Betala per användning).|
|-Currency|(Valfritt) Valutan i vilken kostnaden visas i den genererade rapporten. Standard är amerikanska dollar ($) eller den senast använda valutan. Läs mer i listan med [valutor som stöds](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Som standard är verktyget konfigurerat att profilera och generera rapporter för upp till 1 000 virtuella datorer. Du kan ändra gränsen genom att ändra nyckelvärdet MaxVMsSupported i filen ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Exempel
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Generera en rapport med standardvärden när profileringsdata ligger på den lokala enheten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Generera en rapport när profileringsdata ligger på en fjärrserver
Användaren ska ha läs-/skrivbehörighet för fjärrkatalogen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Generera en rapport med en specifik bandbredd som du kommer att etablera för replikeringen
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Generera en rapport med 5 procents tillväxtfaktor i stället för standardvärdet 30 procent 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Generera en rapport med en delmängd av profileringsdata
Anta exempelvis att du har profileringsdata för 30 dagar och bara vill generera rapporten för 20 dagar.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Generera en rapport för ett återställningspunktmål på 5 minuter
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Generera en rapport för Azure-regionen Indien, södra med indiska rupier och ett specifikt erbjudande-ID
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Percentilvärdet som används för beräkningen
När verktyget genererar en rapport används som standard percentilvärdet 95 för läsa/skriva IOPS, skriva IOPS och dataomsättning. Dessa värden samlas in under profilering av de virtuella datorerna. Det här måttet garanterar att den topp vid den 100:e percentilen som de virtuella datorerna kan nå på grund av tillfälliga händelser inte används för att fastställa mållagringskontot och kraven på källbandbredd. Till exempel kan en tillfällig händelse vara ett säkerhetskopieringsjobb som körs en gång om dagen, periodisk databasindexering eller en analysrapportgenerering eller andra kortvariga händelser vid vissa tidpunkter.

Med ett percentilvärde på 95 får du en korrekt bild av den faktiska arbetsbelastningen, och då får du bästa möjliga prestanda när dessa arbetsbelastningar sedan körs på Azure. Vi tror inte att du kommer att behöva ändra det här talet. Om du inte ändrar det här värdet (till en percentil på 90 exempelvis) kan du uppdatera konfigurationsfilen ASRDeploymentPlanner.exe.config i standardmappen, spara den och generera en ny rapport för befintliga profileringsdata.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Överväganden för tillväxtfaktor
Det är viktigt att ge utrymme för ökande arbetsbelastning eftersom dataanvändningen tenderar att öka med tiden. När du väl har skyddat dina data kan du inte byta till ett annat lagringskonto för skydd om arbetsbelastningen skulle förändras utan att inaktivera och sedan återaktivera skyddet.

Anta exempelvis att din virtuella dator i dag passar på ett standardlagringskonto för replikering. Under de kommande tre månaderna kan dessa ändringar förekomma:

1. Antalet användare av program som körs på den virtuella datorn kommer att öka.
2. Den ökade dataomsättning på den virtuella datorn kräver att den virtuella datorn ska gå över till Premium Storage så att Azure Site Recovery-replikering kan hålla jämna steg.
3. Du måste inaktivera och återaktivera skyddet för ett Premium Storage-konto.

Vi rekommenderar starkt att du planerar för tillväxt vid distributionsplanering. Standardvärdet är 30 procent, men det är du som är experten på dina programanvändningsmönster och tillväxtprognoser. Du kan ändra det här talet när du genererar en rapport. Dessutom kan du skapa flera rapporter med olika tillväxtfaktorer med samma profilerade data. Du kan sedan fastställa vilka rekommendationer om mållagring och källbandbredd som fungerar bäst för dig. 

Den genererade rapporten i Microsoft Excel innehåller följande information:

* [Lokal sammanfattning](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Rekommendationer](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-Storage Placement](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) (VM-lagringsplacering)
* [Compatible VMs (Kompatibla virtuella datorer)](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (Inkompatibla virtuella datorer)](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [On-premises storage requirement](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement) (Krav för lokal lagring)
* [IR-batchbearbetning](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Cost estimation](hyper-v-deployment-planner-cost-estimation.md) (Kostnadsberäkning)

![Rapport för kapacitetsplaneraren](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Beräkna dataflöde
Om du vill få en uppskattning av vilket dataflöde som Azure Site Recovery kan uppnå från de lokala datorerna under replikeringen ska du köra verktyget i dataflödesläget. Verktyget beräknar dataflödet från den server som verktyget körs på. Vi rekommenderar att den här servern är den Hyper-V-server vars virtuella datorer ska skyddas. 

### <a name="command-line-parameters"></a>Kommandoradsparametrar 
Öppna en kommandotolk och gå till mappen för distributionsplaneringsverktyget för Azure Site Recovery. Kör ASRDeploymentPlanner.exe med följande parametrar.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Parameternamn | Beskrivning |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|Visualiseringstypen (VMware eller Hyper-V).|
|-Directory|(Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om inget namn anges används katalogen ProfiledData under den aktuella sökvägen som standardkatalog.|
| -StorageAccountName | Namnet på det lagringskonto som används för beräkning av den bandbredd som används för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när bandbredden ska beräknas. Lagringskontot måste vara av typen generell användning v1 (GPv1).|
| -StorageAccountKey | Den lagringskontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > **lagrings konton**  >  *lagrings kontots namn*  >  **Inställningar**  >  **åtkomst nycklar**  >  **KEY1**.|
| -VMListFile | En fil som innehåller listan med virtuella datorer som ska profileras när den förbrukade bandbredden ska beräknas. Filsökvägen kan vara absolut eller relativ. För Hyper-V är den här filen utdatafilen för åtgärden GetVMList. Om du förbereder manuellt ska filen innehålla ett servernamn eller en IP-adress följt av VM-namnet (angränsat med ett \ per rad). Det VM-namn som angetts i filen ska vara samma som VM-namnet på Hyper-V-värden.<br><br>**Exempel:** VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(Valfritt) Målmiljön för Azure Storage-kontot. Det kan vara ett av tre värden: AzureCloud, AzureUSGovernment eller AzureChinaCloud. Standardvärdet är AzureCloud. Använd parametern när din Azure-region är antingen Azure amerikanska myndigheter eller Azure Kina 21Vianet.|

### <a name="example"></a>Exempel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Överväganden för dataflöde

Verktyget skapar flera 64 MB stora filer med namnet asrvhdfile *tal*.vhd (där *tal* är antalet filer) i den angivna katalogen. Verktyget överför filerna till lagringskontot när dataflödet ska beräknas. När dataflödet har beräknats tar verktyget bort alla filer både från lagringskontot och från den lokala servern. Om verktyget avslutas av någon anledning medan det beräknar dataflödet tar det inte bort filerna från lagringskontot eller från den lokala servern. Du måste ta bort dem manuellt.

Dataflödet mäts vid en angiven tidpunkt. Det är det maximala dataflöde som Azure Site Recovery kan uppnå vid replikering, om alla andra faktorer är desamma. Om ett annat program till exempel börjar förbruka mer bandbredd i samma nätverk kommer det faktiska dataflödet att variera under replikeringen. Resultatet av det uppmätta dataflödet ser annorlunda ut om GetThroughput-åtgärden körs när de skyddade virtuella datorerna har hög dataomsättning. 

Du bör därför köra verktyget vid olika tidpunkter under profileringen så att du får förståelse för vilka dataflödesnivåer som kan uppnås vid olika tidpunkter. I rapporten visas det senaste uppmätta dataflödet.

> [!NOTE]
> Kör verktyget på en server som har samma lagringsutrymme och processoregenskaper som en Hyper-V-server.

Ange den rekommenderade bandbredden för att uppfylla återställningspunktmålet 100 procent av tiden för replikering. Om verktyget inte rapporterar en ökning av uppnådda dataflöden trots att du har konfigurerat rätt mängd bandbredd kan du göra följande:

1. Kontrollera om något problem med tjänstkvaliteten (QoS) för nätverket begränsar dataflödet för Azure Site Recovery.
2. Kontrollera om valvet för Azure Site Recovery ligger i den närmaste fysiska Microsoft Azure-region som stöds, så att svarstiden i nätverket minimeras.
3. Kontrollera de lokala lagringsegenskaperna för att avgöra om du kan förbättra maskinvaran (till exempel hårddisk till SSD).

    
## <a name="next-steps"></a>Nästa steg
* [Analysera den genererade rapporten](hyper-v-deployment-planner-analyze-report.md)
