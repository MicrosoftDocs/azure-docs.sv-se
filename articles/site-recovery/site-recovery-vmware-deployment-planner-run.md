---
title: Kör distributions planeraren för VMware haveri beredskap med Azure Site Recovery
description: Den här artikeln beskriver hur du kör Distributionshanteraren för Azure Site Recovery för haveri beredskap i VMware till Azure.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004729"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>Kör distributions planeraren för VMware haveri beredskap
Den här artikeln utgör användarhandboken för Azure Site Recovery Deployment Planner för produktionsdistribution av VMware till Azure.


## <a name="modes-of-running-deployment-planner"></a>Lägen för att köra distributionshanteraren
Du kan köra kommandoradsverktyget (ASRDeploymentPlanner.exe) i någon av följande tre lägen:

1.  [Profilering](#profile-vmware-vms)
2.  [Rapportgenerering](#generate-report)
3.  [Beräkna dataflöde](#get-throughput)

Först kör du verktyget i profileringsläge för att samla in uppgifter om den virtuella datorns dataomsättning och IOPS. Kör sedan en rapportgenerering så att du ser kraven på nätverksbandbredd och lagring samt kostnaden för haveriberedskap.

## <a name="profile-vmware-vms"></a>Profilera virtuella VMware-datorer
I profileringsläge ansluter distributionskapacitetsplaneraren till vCenter-servern/vSphere ESXi-värden och samlar in prestandadata om den virtuella datorn.

* Profilering påverkar inte prestanda hos de virtuella produktionsdatorerna eftersom ingen direktanslutning upprättas till dem. Alla prestandadata samlas in från vCenter-servern/vSphere ESXi-värden.
* Verktyget skickar frågor till vCenter-servern/vSphere EXSi-värden var 15:e minut, så att servern ska påverkas minimalt av profileringen. Frågeintervallet äventyrar dock inte profileringens noggrannhet eftersom verktyget lagrar prestandaräknardata varje minut.

### <a name="create-a-list-of-vms-to-profile"></a>Skapa en lista över virtuella datorer att profilera
Du behöver först en lista över de virtuella datorer som ska profileras. Du kan hämta namnen på alla virtuella datorer på en vCenter-server/vSphere ESXi-värd med hjälp av VMware vSphere PowerCLI-kommandon i följande procedur. Alternativt kan du lista de egna namnen eller IP-adresserna till de virtuella datorer som du vill profilera manuellt i en fil.

1. Logga in till den virtuella dator som VMware vSphere PowerCLI är installerad på.
2. Öppna VMware vSphere PowerCLI-konsolen.
3. Se till att körningsprincipen är aktiverad för skriptet. Om den är inaktiverad startar du VMware vSphere PowerCLI-konsolen i administratörsläge och aktiverar den med följande kommando:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Du kan också behöva köra följande kommando om Connect-VIServer inte känns igen som namnet på cmdleten.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Kör de två kommandona i listan här för att hämta alla namnen på virtuella datorer på en vCenter-server/sShere ESXi-värd och spara den i en txt-fil.
Ersätt &lsaquo;servernamn&rsaquo;, &lsaquo;användarnamn&rsaquo;, &lsaquo;lösenord&rsaquo; och &lsaquo;utdatafil.txt&rsaquo; med egna värden.

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Öppna utdatafilen i Anteckningar och kopiera sedan namnen på alla virtuella datorer som du vill profilera till en annan fil (till exempel ProfileVMList.txt), med ett namn på en virtuell dator per rad. Den här filen används som indata för parametern *-VMListFile* i kommandoradsverktyget.

    ![Lista med namn på virtuella datorer i kapacitetsplaneraren
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Starta profilering
När du har skapat listan med virtuella datorer att profilera kan du köra verktyget i profileringsläge. Här är listan med obligatoriska och valfria parametrar när du ska köra verktyget i profileringsläge.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parameternamn | Beskrivning |
|---|---|
| -Operation | StartProfiling |
| -Server | Fullständigt domännamn eller IP-adress för den vCenter-server/vSphere ESXi-värd vars virtuella datorer ska profileras.|
| -User | Användarnamn för anslutning till vCenter-servern/vSphere ESXi-värden. Användaren måste minst ha läsbehörighet.|
| -VMListFile | En fil som innehåller en lista över virtuella datorer som ska profileras. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn/en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara detsamma som namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden.<br>Filen VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>virtuell_dator_A</li><li>10.150.29.110</li><li>virtuell_dator_B</li><ul> |
|-NoOfMinutesToProfile|Antal minuter som profileringen ska köras. Minimum är 30 minuter.|
|-NoOfHoursToProfile|Antal timmar som profileringen ska köras.|
| -NoOfDaysToProfile | Antal dagar som profileringen ska köras. Du bör köra profileringen i minst 7 dagar så att du fångar upp mönster för arbetsbelastningen i din miljö under den angivna perioden och kan generera en korrekt rekommendation. |
|-Virtualization|Ange visualiseringstyp (VMware eller Hyper-V).|
| -Directory | (Valfritt) UNC (Universal Naming Convention) eller lokal katalogsökväg för lagring av de profildata som genereras under profileringen. Om inget katalognamn anges används katalogen ”ProfiledData” under den aktuella sökvägen som standardkatalog. |
| -Password | (Valfritt) Lösenord för att ansluta till vCenter-servern/vSphere ESXi-värden. Om du inte anger något värde nu uppmanas du att ange det när kommandot körs.|
|-Port|(Valfritt) Portnummer för anslutning till vCenter/ESXi-värden. Standardporten är 443.|
|-Protocol| (Valfritt) Ange protokollet, antingen http eller https, för anslutning till vCenter. Standardprotokollet är https.|
| -StorageAccountName | (Valfritt) Namnet på det lagringskonto som används för beräkning av dataflödet som kan uppnås för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när dataflödet ska beräknas. Lagringskontot måste vara av typen generell användning v1 (GPv1). |
| -StorageAccountKey | (Valfritt) Den lagringskontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > <*[lagringskontots namn]*> > Inställningar > Åtkomstnycklar > Key1. |
| -Environment | (Valfritt) Det här är din målmiljö för Azure Storage-kontot. Detta kan vara ett av tre värden – AzureCloud, AzureUSGovernment eller AzureChinaCloud. Standardvärdet är AzureCloud. Använd parametern när din Azure-region är antingen Azure amerikanska myndigheter eller Azure Kina 21Vianet. |


Vi rekommenderar att du profilerar dina virtuella datorer under minst 7 dagar. Om omsättningsmönstret varierar under en månad rekommenderar vi att du profilerar under veckan när omsättningen är som störst. Det bästa är att profilera i 31 dagar för att få en bättre rekommendation. ASRDeploymentPlanner.exe körs under hela profileringsperioden. Profileringstiden anges i dagar i verktyget. För ett snabbtest av verktyget eller ett konceptbevis kan du profilera i några timmar eller minuter. Minsta tillåtna profileringstid är 30 minuter.

Under profileringen kan du välja att skicka namn och nyckel för ett lagringskonto om du vill se vilket dataflöde som Site Recovery kan uppnå för replikeringen från konfigurationsservern/processervern till Azure. Om du inte skickar namn och nyckel för ett lagringskonto under profileringen beräknar inte verktyget det dataflöde som kan uppnås.

Du kan köra flera instanser av verktyget för olika uppsättningar av virtuella datorer. Se till att det inte finns dubbletter av namn på virtuella datorer i profileringsuppsättningarna. Till exempel kanske du har profilerat tio virtuella datorer (VM1–VM10). Efter några dagar vill du profilera ytterligare fem virtuella datorer (VM11–VM15), och då kan du köra verktyget från en annan kommandotolk för den andra uppsättningen av virtuella datorer (VM11–VM15). Se till att den andra uppsättningen av virtuella datorer inte innehåller något av namnen på de virtuella datorerna från den första profileringsinstansen, eller använd en annan utdatakatalog för den andra körningen. Om två instanser av verktyget används för profilering av samma virtuella datorer och samma utdatakatalog används kommer den genererade rapporten att vara felaktig.

Som standard är verktyget konfigurerat för att profilera och generera rapporter upp till 1000 virtuella datorer. Du kan ändra gränsen genom att ändra nyckelvärdet i filen *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Om du vill skapa till exempel 1500 virtuella datorer med standardinställningarna ska du skapa två VMList.txt-filer. En med 1000 virtuella datorer och en med 500. Kör de två instanserna av Distributionshanteraren för Azure Site Recovery, en med VMList1.txt och annat med VMList2.txt. Du kan använda samma katalogsökväg för att lagra profilerade data för de båda virtuella VMList-datorerna.

Vi har märkt att åtgärden kan misslyckas med otillräckligt minne baserat på maskinvarukonfigurationen. Det gäller i synnerhet RAM-storleken på servern varifrån verktyget körs för att generera rapporten. Om du har bra maskinvara kan du ändra MaxVMsSupported till ett högre värde.  

Om du har flera vCenter-servrar måste du köra en instans av ASRDeploymentPlanner för varje vCenter-server när du ska profilera.

Den virtuella datorns konfiguration inhämtas en gång i början av profileringsåtgärden och lagras i en fil med namnet VMDetailList.xml. Den här informationen används när rapporten genereras. Ändringar i den virtuella datorkonfigurationen (till exempel ett ökat antal kärnor, diskar och nätverkskort) från början till slutet av profileringen registreras inte. Om konfigurationen för en profilerad virtuell dator ändrats under profileringen finns det en lösning i den offentliga förhandsutgåvan för att hämta den senaste virtuella datorinformationen när rapporten ska genereras:

* Säkerhetskopiera filen VMdetailList.xml och ta bort den från dess nuvarande plats.
* Skicka argumenten -User och -Password när rapporten ska genereras.

Profileringskommandot genererar flera filer i profileringskatalogen. Ta inte bort några filer, eftersom detta påverkar rapportgenereringen.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exempel 1: Profilera virtuella datorer i 30 dagar och beräkna dataflödet från lokala datorer till Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exempel 2: Profilera virtuella datorer i 15 dagar

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Exempel 3: Profilera virtuella datorer under 60 minuter för ett snabbtest av verktyget
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Exempel 4: Profilera virtuella datorer i två timmar för ett konceptbevis
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Om servern där verktyget körs startas om eller kraschar, eller om du avslutar verktyget med Ctrl + C, bevaras profileringsdata. Du riskerar dock att förlora de senaste 15 minuternas profileringsdata. I en sådan instans måste du köra om verktyget i profileringsläge när servern startas igen.
>* När du skickar namn och nyckel för ett lagringskonto mäter verktyget dataflödet vid profileringens sista steg. Om verktyget avslutas innan profileringen har slutförts normalt beräknas inte dataflödet. Du kan hitta dataflödet innan du genererar rapporten genom att köra åtgärden GetThroughput från kommandotolken. Annars innehåller inte den genererade rapporten dataflödesinformationen.


## <a name="generate-report"></a>Generera en rapport
Verktyget genererar en makroaktiverad Microsoft Excel-fil (XLSM) som rapportutdata med en sammanfattning av alla distributionsrekommendationer. Rapporten namnges `DeploymentPlannerReport_<unique numeric identifier>.xlsm` och placeras i den angivna katalogen.

>[!NOTE]
>För rapportgenerering krävs en Windows-dator eller Windows Server med Excel 2013 eller senare. Decimal tecknet på den här datorn ska konfigureras som "." för att skapa kostnads beräkningar. Om du har installations programmet "," som decimal symbol går du till "ändra datum, tid eller tal format" på kontroll panelen och går till "ytterligare inställningar" för att ändra decimal tecknet till ".".

När profileringen är färdig kan köra du verktyget i läget för rapportgenerering. Följande tabell innehåller en lista med obligatoriska och valfria verktygsparametrar som ska köras i läget för rapportgenerering.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parameternamn | Beskrivning |
|-|-|
| -Operation | GenerateReport |
| -Server |  Fullständigt domännamn eller IP-adress för vCenter-/vSphere-servern (använd samma namn eller IP-adress som du använde vid profileringen) där de profilerade virtuella datorer som rapporten ska gälla finns. Tänk på att om du har använt en vCenter-server vid profileringen kan du inte använda en vSphere-server till rapportgenerering och tvärtom.|
| -VMListFile | Den fil som innehåller listan över profilerade virtuella datorer som rapporten ska genereras för. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn eller en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara identiska med namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden, och vara desamma som vid profileringen.|
|-Virtualization|Ange visualiseringstyp (VMware eller Hyper-V).|
| -Directory | (Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om du inte anger något namn används katalogen ProfiledData. |
| -GoalToCompleteIR | (Valfritt) Antalet timmar som den inledande replikeringen av de profilerade virtuella datorerna måste slutföras på. I den genererade rapporten anges det hur många virtuella datorer som den inledande replikeringen kan slutföras på inom den angivna tiden. Standardvärdet är 72 timmar. |
| -User | (Valfritt) Användarnamn som ska användas för anslutning till vCenter-/vSphere-servern. Namnet används för att hämta den senaste konfigurationsinformationen för de virtuella datorerna, exempelvis antal diskar, antal kärnor och antal nätverkskort som ska användas i rapporten. Om du inte anger något namn används den konfigurationsinformation som samlades in i början av profileringen. |
| -Password | (Valfritt) Lösenord för att ansluta till vCenter-servern/vSphere ESXi-värden. Om inget lösenord anges som parameter uppmanas du att ange lösenordet senare när kommandot körs. |
|-Port|(Valfritt) Portnummer för anslutning till vCenter/ESXi-värden. Standardporten är 443.|
|-Protocol|(Valfritt) Ange protokollet, antingen http eller https, för anslutning till vCenter. Standardprotokollet är https.|
| -DesiredRPO | (Valfritt) Önskat mål för återställningspunkt (RPO) i minuter. Standardvärdet är 15 minuter.|
| -Bandwidth | Bandbredd i Mbit/s. Det här värdet används till att beräkna det RPO som kan uppnås för den angivna bandbredden. |
| -StartDate | (Valfritt) Startdatum och tidpunkt i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). *StartDate* måste anges tillsammans med *EndDate*. När StartDate anges genereras rapporten för de profileringsdata som samlats in mellan StartDate och EndDate. |
| -EndDate | (Valfritt) Slutdatum och tidpunkt i formatet MM-DD-ÅÅÅÅ:HH:MM (24-timmarsformat). *EndDate* måste anges tillsammans med *StartDate*. När du anger EndDate genereras rapporten för profileringsdata som samlats in mellan StartDate och EndDate. |
| -GrowthFactor | (Valfritt) Tillväxtfaktor, uttryckt i procent. Standardvärdet är 30 procent. |
| -UseManagedDisks | (Valfritt) UseManagedDisks - Ja/Nej. Standardvärdet är Ja. Antalet virtuella datorer som kan placeras i ett enda lagringskonto beräknas utifrån om redundans/redundanstest för virtuella datorer görs på en hanterad disk istället för en ohanterad disk. |
|-SubscriptionId |(Valfritt) GUID för prenumerationen. Observera att den här parametern krävs när du behöver generera rapporten kostnads uppskattning med det senaste priset baserat på din prenumeration, erbjudandet som är associerat med din prenumeration och för din specifika Azure-region i den **angivna valutan**.|
|-TargetRegion|(Valfritt) Azure-regionen som är mål för replikeringen. Eftersom Azure har olika kostnader för olika regioner ska du använda den här parametern till att generera en rapport för en specifik Azure-region.<br>Standard är usavästra2 eller den senast använda målregionen.<br>Läs mer i listan med [målregioner som stöds](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Valfritt) Erbjudandet som är associerat med den angivna prenumerationen. Standard är MS-AZR-0003P (Betala per användning).|
|-Currency|(Valfritt) Valutan i vilken kostnaden visas i den genererade rapporten. Standard är amerikanska dollar ($) eller den senast använda valutan.<br>Läs mer i listan med [valutor som stöds](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Som standard är verktyget konfigurerat för att profilera och generera rapporter upp till 1000 virtuella datorer. Du kan ändra gränsen genom att ändra nyckelvärdet i filen *ASRDeploymentPlanner.exe.config*.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exempel 1: Generera en rapport med standardvärden när profileringsdata ligger på den lokala enheten
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exempel 2: Generera en rapport när profileringsdata ligger på en fjärrserver
Användaren ska ha läs-/skrivbehörighet för fjärrkatalogen.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exempel 3: Generera en rapport med specifik bandbredd och specifikt mål för att slutföra IR inom angiven tid
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exempel 4: Generera en rapport med 5 procents tillväxtfaktor i stället för standardvärdet 30 procent
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exempel 5: Generera en rapport med en delmängd av profileringsdata
Anta exempelvis att du har profileringsdata för 30 dagar och bara vill generera rapporten för 20 dagar.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exempel 6: Generera en rapport för ett återställningspunktmål på 5 minuter
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Exempel 7: Generera en rapport för Azure-regionen Indien, södra med indiska rupier och specifikt erbjudande-ID

Observera att prenumerations-ID krävs för att generera kostnads rapport i en speciell valuta.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Percentilvärdet som används för beräkningen
**Vilket standardvärde för percentilen för resultatmåtten som samlas in under profileringen använder verktyget när en rapport genereras?**

Standardvärdet i verktyget är den 95:e percentilen för läs/skriv-IOPS och dataomsättningar som samlas in när alla de virtuella datorerna profileras. Det här måttet garanterar att den topp vid den 100:e percentilen som de virtuella datorerna kan nå eftersom de tillfälliga händelserna inte används för att fastställa mållagringskontot och kraven på källbandbredd. Till exempel kan en tillfällig händelse vara ett säkerhetskopieringsjobb som körs en gång om dagen, periodisk databasindexering eller en analysrapportgenereringsaktivitet eller andra liknande kortvariga händelser vid vissa tidpunkter.

Med värden från den 95:e percentilen får du en korrekt bild av den faktiska arbetsbelastningen, och då får du bästa möjliga prestanda när dessa arbetsbelastningar sedan körs på Azure. Vi tror inte att du kommer att behöva ändra det här antalet. Om du inte ändrar det här värdet (till 90:e percentilen exempelvis) kan du uppdatera konfigurationsfilen *ASRDeploymentPlanner.exe.config* i standardmappen, spara den och generera en ny rapport för befintliga profileringsdata.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Överväganden för tillväxtfaktorer
**Varför bör jag överväga för tillväxtfaktor när jag planerar distributioner?**

Det är viktigt att ge utrymme för ökande arbetsbelastning eftersom dataanvändningen tenderar att öka med tiden. När du väl har skyddat dina data kan du inte byta till ett annat lagringskonto för skydd om arbetsbelastningen skulle förändras utan att inaktivera och sedan återaktivera skyddet.

Anta exempelvis att din virtuella dator i dag passar på ett standardlagringskonto för replikering. Under de kommande tre månaderna kan flera ändringar förekomma:

* Antalet användare av program som körs på den virtuella datorn kommer att öka.
* Den resulterande ökade omsättning på den virtuella datorn kräver att den virtuella datorn ska gå över till Premium Storage så att Site Recovery-replikering kan hålla jämna steg.
* Därför måste du inaktivera och återaktivera skyddet för ett Premium Storage-konto.

Vi rekommenderar starkt att du planerar för tillväxt vid distributionsplanering och medan standardvärdet är 30 procent. Det är du som är expert på användningsmönster och tillväxtprognoser i dina program, och du kan ändra det här värdet när du genererar rapporter. Du kan i själva verket skapa flera rapporter med olika tillväxtfaktorer för samma profileringsdata och se vilka rekommendationer för mållagring och källbandbredd som fungerar bäst för dig.

Den genererade rapporten i Microsoft Excel innehåller följande information:

* [Lokal Sammanfattning](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Rekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM-<->lagrings placering](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Compatible VMs (Kompatibla virtuella datorer)](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (Inkompatibla virtuella datorer)](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Kostnads uppskattning](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Beräkna dataflöde

Om du vill få en uppskattning av vilket dataflöde som Site Recovery kan uppnå från de lokala datorerna till Azure under replikeringen ska du köra verktyget i GetThroughput-läget. Verktyget beräknar dataflödet från den server som verktyget körs på. Den här servern ska helst vara baserad på guiden för storleksändring av konfigurationsservern. Om du redan har distribuerat infrastrukturkomponenter för Site Recovery lokalt kör du verktyget på konfigurationsservern.

Öppna en kommandotolk och gå till mappen för planeringsverktyget för Site Recovery. Kör ASRDeploymentPlanner.exe med följande parametrar.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parameternamn | Beskrivning |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Ange visualiseringstyp (VMware eller Hyper-V).|
| -Directory | (Valfritt) UNC eller lokal katalogsökväg där profileringsdata (filer som genererats under profileringen) lagras. Dessa data krävs när rapporten ska genereras. Om namnet på en katalog inte anges används katalogen ProfiledData. |
| -StorageAccountName | Namnet på det lagringskonto som används för beräkning av den bandbredd som används för datareplikering lokalt till Azure. Verktyget överför testdata till det här lagringskontot när bandbredden ska beräknas. Lagringskontot måste vara antingen av typen generell användning v1 (GPv1).|
| -StorageAccountKey | Den lagringskontonyckel som används för åtkomst till lagringskontot. Gå till Azure Portal > Lagringskonton > <*[lagringskontots namn]*> Inställningar > Åtkomstnycklar > Key1 (eller en primär åtkomstnyckel för ett klassiskt lagringskonto). |
| -VMListFile | En fil som innehåller listan med virtuella datorer som ska profileras när den förbrukade bandbredden ska beräknas. Filsökvägen kan vara absolut eller relativ. Den här filen ska innehålla ett virtuellt datornamn/en IP-adress per rad. Namnen på de virtuella datorerna i filen ska vara samma som namnen på de virtuella datorerna på vCenter-servern/vSphere ESXi-värden.<br>Filen VMList.txt innehåller exempelvis följande virtuella datorer:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (Valfritt) Det här är din målmiljö för Azure Storage-kontot. Detta kan vara ett av tre värden – AzureCloud, AzureUSGovernment eller AzureChinaCloud. Standardvärdet är AzureCloud. Använd parametern när din Azure-region är antingen Azure amerikanska myndigheter eller Azure Kina 21Vianet. |

Verktyget skapar flera 64 MB stora filer med namnet asrvhdfile<#>.vhd (där # är antalet filer) i den angivna katalogen. Verktyget överför filerna till lagringskontot när dataflödet ska beräknas. När dataflödet har beräknats tar verktyget bort alla filer både från lagringskontot och från den lokala servern. Om verktyget avslutas av någon anledning medan det beräknar dataflödet tar det inte bort filerna från lagringsutrymmet eller från den lokala servern. Du måste ta bort dem manuellt.

Dataflödet beräknas för en viss tidpunkt och är största möjliga dataflöde som Site Recovery kan uppnå under replikeringen givet att alla andra faktorer är oförändrade. Om ett annat program till exempel börjar förbruka mer bandbredd i samma nätverk kommer det faktiska dataflödet att variera under replikeringen. Om du kör kommandot GetThroughput från en konfigureringsserver så kommer verktyget inte att ha någon information om skyddade virtuella datorer eller pågående replikering. Resultatet av det uppmätta dataflödet ser annorlunda ut om GetThroughput-åtgärden körs när de skyddade virtuella datorerna har hög dataomsättning. Du bör därför köra verktyget flera gånger under profileringen så att du får förståelse för vilka dataflödesnivåer som kan uppnås vid olika tidpunkter. I rapporten visas det senaste uppmätta dataflödet.

### <a name="example"></a>Exempel
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Kör verktyget på en server som har samma lagringsutrymme och processoregenskaper som konfigurationsservern.
>
> Ange den rekommenderade bandbredden för att uppfylla återställningspunktmålet 100 procent av tiden för replikering. Om verktyget inte rapporterar en ökning av uppnådda dataflöden trots att du har konfigurerat rätt mängd bandbredd kan du göra följande:
>
>  1. Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Site Recovery.
>
>  2. Kontrollera om valvet för Site Recovery ligger i den närmaste fysiska Microsoft Azure-region som stöds, så att svarstiden i nätverket minimeras.
>
>  3. Kontrollera de lokala lagringsegenskaperna för att avgöra om du kan förbättra maskinvaran (till exempel hårddisk till SSD).
>
>  4. Ändra inställningarna på processervern för Site Recovery och [öka mängden bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Nästa steg
* [Analysera den genererade rapporten](site-recovery-vmware-deployment-planner-analyze-report.md).
