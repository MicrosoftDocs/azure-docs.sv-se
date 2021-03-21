---
title: Kom igång-guide för Azure IT-operatörer | Microsoft Docs
description: Kom igång-guide för Azure IT-operatörer
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 1eb0bf2c0e214c3e076895903176d15ee792c70c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093046"
---
# <a name="get-started-for-azure-it-operators"></a>Kom igång för Azure IT-operatörer

Den här guiden innehåller grundläggande begrepp som rör distribution och hantering av en Microsoft Azure infrastruktur. Om du inte har använt molnbaserad data behandling, eller själva Azure, hjälper den här guiden dig att snabbt komma igång med koncept, distribution och hanterings information. Många delar av den här guiden beskriver en åtgärd som att distribuera en virtuell dator och sedan tillhandahålla en länk för djupgående teknisk information.

## <a name="cloud-computing-overview"></a>Översikt över molntjänster

Molnbaserad data behandling är ett modernt alternativ till det traditionella lokala data centret. Leverantörer av offentliga moln ger och hanterar all infrastruktur för data behandling och underliggande hanterings program. Dessa leverantörer tillhandahåller en mängd olika moln tjänster. En moln tjänst i det här fallet kan vara en virtuell dator, en webb server eller en molnbaserad databas motor. Som en Cloud Provider-kund kan du låna ut dessa moln tjänster efter behov. När du gör det konverteras kapital kostnaden för maskin varu underhåll till en drifts kostnad. En moln tjänst ger också följande fördelar:

- Snabb distribution av stora beräknings miljöer

- Snabbt avallokering av system som inte längre behövs

- Enkel distribution av traditionellt komplexa system som belastnings utjämning

- Möjlighet att tillhandahålla flexibel beräknings kapacitet eller skalning vid behov

- Mer kostnads effektiva dator miljöer

- Åtkomst från var som helst med en webbaserad portal eller programmerings automatisering

- Molnbaserade tjänster som uppfyller de flesta beräknings-och program behov

Med lokal infrastruktur har du fullständig kontroll över den maskin vara och program vara som distribueras. Historiskt sett har detta lett till beslut om maskin varu anskaffning som fokuserar på att skala upp. Ett exempel är att köpa en server med fler kärnor som uppfyller behoven för högsta prestanda. Den här infrastrukturen kan tyvärr vara underutnyttjad utanför ett behovs fönster. Med Azure kan du distribuera endast den infrastruktur som du behöver och när som helst justera upp eller ned. Detta leder till en fokusera på att skala ut genom distributionen av ytterligare Compute-noder för att tillgodose ett prestanda behov. Att skala ut moln tjänster är mer kostnads effektivt än att skala upp genom kostsam maskin vara.

Microsoft har distribuerat många Azure-datacenter runtom i världen, med mer planerade. Dessutom ökar Microsoft suveräna moln i regioner som Kina och Tyskland. Endast de största globala företagen kan distribuera data Center på det här sättet, så att Azure gör det enkelt för företag av valfri storlek att distribuera sina tjänster nära sina kunder.

För små företag tillåter Azure att en låg kostnads start punkt, med möjlighet att skala snabbt som efter frågan på beräknings ökningar. Detta förhindrar en stor budget investering i infrastrukturen och ger flexibiliteten att skapa och bygga upp system vid behov. Användningen av molnbaserad data behandling passar bra med den snabba och snabba modellen för start tillväxt.

Mer information om tillgängliga Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Molnbaserad data behandlings modell

Azure använder en molnbaserad data behandlings modell som baseras på tjänste kategorier som tillhandahålls kunder. De tre kategorierna av tjänster inkluderar IaaS (Infrastructure as a Service), plattform som en tjänst (PaaS) och program vara som en tjänst (SaaS). Leverantörer delar vissa eller alla ansvars områden för komponenter i data bearbetnings stacken i var och en av dessa kategorier. Låt oss ta en titt på var och en av kategorierna för molnbaserad data behandling.
![Jämförelse av molnbaserad data behandlings stack](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: infrastruktur som en tjänst

En IaaS moln leverantör kör och hanterar alla fysiska beräknings resurser och nödvändig program vara för att aktivera virtualisering av datorer. En kund av den här tjänsten distribuerar virtuella datorer i dessa värdbaserade Data Center. Även om de virtuella datorerna finns i ett externt Data Center, har IaaS-klienten kontroll över konfiguration och hantering av operativ systemet som lämnar den underliggande infrastrukturen till moln leverantören.

Azure innehåller flera IaaS-lösningar, inklusive virtuella datorer, skalnings uppsättningar för virtuella datorer och den relaterade nätverks infrastrukturen. Virtuella datorer är ett populärt alternativ för att först migrera tjänster till Azure eftersom det möjliggör en flyttnings modell för "lyft och Shift". Du kan konfigurera en virtuell dator som den infrastruktur som för närvarande kör dina tjänster i ditt data Center och sedan migrera program varan till den nya virtuella datorn. Du kan behöva göra konfigurations uppdateringar, till exempel URL: er till andra tjänster eller lagrings enheter, men du kan migrera många program på det här sättet.

Skalnings uppsättningar för virtuella datorer skapas ovanpå Azure Virtual Machines och ger ett enkelt sätt att distribuera kluster med identiska virtuella datorer. Skalnings uppsättningar för virtuella datorer stöder även automatisk skalning så att nya virtuella datorer kan distribueras automatiskt när det behövs. Detta gör att den virtuella datorns skalning anger en idealisk plattform som kan vara värd för kluster med högre nivå beräknings kluster, till exempel Azure Service Fabric och Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: plattform som en tjänst

Med PaaS kan du distribuera ditt program till en miljö som moln tjänst leverantören tillhandahåller. Leverantören utför all infrastruktur hantering så att du kan fokusera på program utveckling och data hantering.

Azure tillhandahåller flera PaaS-beräknings erbjudanden, inklusive Web Apps funktionen i Azure App Service och Azure Cloud Services (webb-och arbets roller). I båda fallen har utvecklare flera olika sätt att distribuera sina program utan att känna till allt om den nötter och de bultar som har stöd för det. Utvecklare behöver inte skapa virtuella datorer (VM), använda Remote Desktop Protocol (RDP) för att logga in på var och en, eller installera programmet. De träffar bara en knapp (eller nära den) och de verktyg som tillhandahålls av Microsoft etablerar de virtuella datorerna och sedan distribuerar och installerar programmet på dem.

#### <a name="saas-software-as-a-service"></a>SaaS: program vara som en tjänst

SaaS är program vara som hanteras centralt och hanteras centralt. Det är vanligt vis baserat på en arkitektur för flera innehavare – en enda version av programmet används för alla kunder. Den kan skalas ut till flera instanser för att säkerställa bästa prestanda på alla platser. SaaS-programvara licensieras vanligt vis via en månatlig eller årlig prenumeration. SaaS program varu leverantörer är ansvariga för alla komponenter i program varu stacken så att allt du hanterar är de tjänster som tillhandahålls.

Microsoft 365 är ett användbart exempel på ett SaaS-erbjudande. Prenumeranter betalar en månatlig eller årlig prenumerations avgift, och de får Microsoft Exchange, Microsoft OneDrive och resten av Microsoft Office Suite som en tjänst. Prenumeranter får alltid den senaste versionen och Exchange-servern hanteras åt dig. Jämfört med att installera och uppgradera Office varje år är detta billigare och kräver mindre ansträngning.

## <a name="azure-services"></a>Azure-tjänster

Azure erbjuder många tjänster i sin plattform för molnbaserad data behandling. Dessa tjänster omfattar följande.

### <a name="compute-services"></a>Beräkningstjänster

Tjänster för att vara värd för och köra program arbets belastning:

- Azure Virtual Machines – både Linux och Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps och Function-appar)

- Azure Batch (för storskaliga parallell-och batch-jobb)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Data tjänster

Tjänster för att lagra och hantera data:

- Azure Storage (består av Azure Blob-, Queue-, Table-och File-tjänsterna)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Program tjänster

Tjänster för skapande och operativ program:

- Azure Active Directory (Azure AD)

- Azure Service Bus för att ansluta distribuerade system

- Azure HDInsight för bearbetning av Big data

- Azure Logic Apps för integrerings-och Dirigerings arbets flöden

- Azure Media Services

### <a name="network-services"></a>Nätverks tjänster

Tjänster för nätverk både i Azure och mellan Azure och lokala data Center:

- Azure Virtual Network

- Azure ExpressRoute

- Azure-tillhandahållen DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Detaljerad dokumentation om Azure-tjänster finns i [dokumentationen för Azure-tjänsten](/azure).

## <a name="azure-key-concepts"></a>Azure Key-koncept

### <a name="datacenters-and-regions"></a>Data Center och regioner

Azure är en global moln plattform som är allmänt tillgänglig i många regioner runtom i världen. När du etablerar en tjänst, ett program eller en virtuell dator i Azure uppmanas du att välja en region. Den valda regionen representerar ett angivet Data Center där ditt program körs. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

En av fördelarna med att använda Azure är att du kan distribuera dina program till olika data center runtom i världen. Den region du väljer kan påverka programmets prestanda. Det är bäst att välja en region som är närmare för de flesta kunder, för att minska svars tiden i nätverks förfrågningar. Du kan också välja en region för att uppfylla de juridiska kraven för att distribuera din app i vissa länder/regioner.

### <a name="azure-portal"></a>Azure Portal

Azure Portal är ett webbaserat program som kan användas för att skapa, hantera och ta bort Azure-resurser och-tjänster. Azure Portal finns på [Portal.Azure.com](https://portal.azure.com). Den innehåller en anpassningsbar instrument panel och verktyg för att hantera Azure-resurser. Den innehåller även information om fakturering och prenumerationer. Mer information finns i [Microsoft Azure-portalen översikt](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) och [Hantera Azure-resurser via portalen](../../azure-resource-manager/management/manage-resources-portal.md).

### <a name="resources"></a>Resurser

Azure-resurser är enskilda beräknings-, nätverks-, data-eller app-värd tjänster som har distribuerats till en Azure-prenumeration. Några vanliga resurser är virtuella datorer, lagrings konton eller SQL-databaser. Azure-tjänster består ofta av flera relaterade Azure-resurser. En virtuell Azure-dator kan till exempel innehålla en virtuell dator, ett lagrings konto, ett nätverkskort och en offentlig IP-adress. Dessa resurser kan skapas, hanteras och tas bort individuellt eller som en grupp. Azure-resurser beskrivs mer detaljerat längre fram i den här hand boken.

### <a name="resource-groups"></a>Resursgrupper

En Azure-resurs grupp är en behållare som innehåller relaterade resurser för en Azure-lösning. Resurs gruppen kan innehålla alla resurser för lösningen eller endast de resurser som du vill hantera som en grupp. Azures resurs grupper beskrivs mer detaljerat längre fram i den här hand boken.

### <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Azure Resource Manager mall är en JavaScript Object Notation-fil (JSON) som definierar en eller flera resurser som ska distribueras till en resurs grupp. Den definierar även beroenden mellan distribuerade resurser. Resource Manager-mallar beskrivs mer detaljerat längre fram i den här hand boken.

### <a name="automation"></a>Automation

Förutom att skapa, hantera och ta bort resurser med hjälp av Azure Portal kan du automatisera dessa aktiviteter med hjälp av PowerShell eller kommando rads gränssnittet för Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell är en uppsättning moduler som tillhandahåller cmdlets för att hantera Azure. Du kan använda cmdletarna för att skapa, hantera och ta bort Azure-tjänster. Cmdletarna kan hjälpa dig att få konsekventa, repeterbara och praktiska distributioner. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Azure-kommandoradsgränssnittet

Azures kommando rads gränssnitt är ett verktyg som du kan använda för att skapa, hantera och ta bort Azure-resurser från kommando raden. Azure CLI är tillgängligt för Linux, Mac OS X och Windows. Mer information och teknisk information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST API:er

Azure bygger på en uppsättning REST API: er som stöder Azure Portal gränssnittet. De flesta av dessa REST API: er stöds också för att låta dig program mässigt etablera och hantera dina Azure-resurser och appar från valfri enhet med Internet. Mer information finns i [Azure rest SDK-referensen](/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Administratörer har åtkomst till Azure PowerShell och Azure CLI via en webb läsar tillgänglig upplevelse som kallas Azure Cloud Shell. Det här interaktiva gränssnittet är ett flexibelt verktyg för Linux-och Windows-administratörer att använda sitt kommando rads gränssnitt, antingen bash eller PowerShell. Azure Cloud Shell kan komma åt via portalen, som ett fristående webb gränssnitt på [Shell.Azure.com](https://shell.azure.com), eller från ett antal andra åtkomst punkter. Mer information finns i [Översikt över Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="azure-subscriptions"></a>Azure-prenumerationer

En prenumeration är en logisk gruppering av Azure-tjänster som är länkad till ett Azure-konto. Ett enda Azure-konto kan innehålla flera prenumerationer. Faktureringen för Azure-tjänster görs per prenumeration. Azure-prenumerationer har en konto administratör som har fullständig kontroll över prenumerationen och en tjänst administratör som har kontroll över alla tjänster i prenumerationen. Information om klassiska prenumerations administratörer finns i [lägga till eller ändra Azure-prenumerations administratörer](../../cost-management-billing/manage/add-change-subscription-administrator.md). Förutom administratörer kan enskilda konton beviljas detaljerad kontroll över Azure-resurser med hjälp av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Välj och aktivera en Azure-prenumeration

Innan du kan arbeta med Azure-tjänster behöver du en prenumeration. Det finns flera prenumerations typer.

**Kostnads fria konton**: länken för att registrera sig för ett kostnads fritt konto finns på [Azure-webbplatsen](https://azure.microsoft.com/). Detta ger dig en kredit på över 30 dagar för att testa en kombination av resurser i Azure. Om du överskrider ditt kredit belopp pausas ditt konto. I slutet av utvärderings versionen inaktive ras tjänsterna och fungerar inte längre. Du kan när som helst uppgradera till en prenumeration där du betalar per användning.

**MSDN**-prenumerationer: om du har en MSDN-prenumeration får du ett visst belopp i Azure-kredit varje månad. Om du till exempel har en prenumeration på Microsoft Visual Studio Enterprise med MSDN får du \$ 150 per månad i Azure-krediten.

Om du överskrider kredit beloppet inaktive ras tjänsten tills nästa månad börjar. Du kan inaktivera utgifts gränsen och lägga till ett kredit kort som ska användas för ytterligare kostnader. Några av dessa kostnader är rabatterade för MSDN-konton. Till exempel betalar du Linux-priset för virtuella datorer som kör Windows Server och det finns ingen extra kostnad för Microsoft-servrar som Microsoft SQL Server. På så sätt kan MSDN-konton vara perfekta för utveckling och test scenarier.

**BizSpark-konton**: Microsoft BizSpark-programmet har många fördelar med att starta. En av dessa förmåner är till gång till alla Microsoft-program för utvecklings-och test miljöer för upp till fem MSDN-konton. Du får $150 i Azure-kredit för vart och ett av dessa fem MSDN-konton och du betalar lägre priser för flera av Azure-tjänsterna, till exempel Virtual Machines.

**Betala per** användning: med den här prenumerationen betalar du för det du använder genom att koppla ett kredit kort eller betalkort till kontot. Om du är en organisation kan du också godkänna för fakturering.

**Enterprise-avtal**: med ett Enterprise-avtal kan du använda ett visst antal tjänster i Azure under nästa år, och du betalar beloppet i förväg. Det åtagande du gör används under året. Om du överskrider åtagande beloppet kan du betala överförbrukningen i förskott. Beroende på åtagandets belopp får du en rabatt på tjänsterna i Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Ge administrativ åtkomst till en Azure-prenumeration

Azure RBAC har flera inbyggda roller som du kan använda för att tilldela behörigheter. Om du vill göra en användare till en administratör för en Azure-prenumeration tilldelar du den till [ägar](../../role-based-access-control/built-in-roles.md#owner) rollen i prenumerations omfånget. Rollen ägare ger fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att ge åtkomst till andra.

Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Visa fakturerings information i Azure Portal

En viktig komponent i att använda Azure är möjligheten att Visa fakturerings information. Azure Portal ger detaljerad information om fakturerings information för Azure.

Mer information finns i [så här hämtar du din fakturerings faktura för Azure och dagliga användnings data](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Få fakturerings information från fakturerings-API: er

Förutom att Visa faktureringen i portalen kan du komma åt fakturerings informationen genom att använda ett skript eller ett program via Azures debitering REST-API: er:

- Du kan använda Azures användnings-API för att hämta dina användnings data. Du kan finjustera fakturerings användnings informationen genom att tagga relaterade Azure-resurser. Du kan till exempel tagga var och en av resurserna i en resurs grupp med ett avdelnings namn eller projekt namn och sedan spåra kostnaderna specifikt för den en taggen.

- Du kan använda [översikten över Azures förbruknings-API](../../cost-management-billing/manage/consumption-api-overview.md) för att visa en lista över alla tillgängliga resurser, tillsammans med metadata. Mer information om priser finns i [Översikt över Azures åter försäljnings priser](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognos kostnad med pris kalkylatorn

Priserna för varje tjänst i Azure är olika. Många Azure-tjänster har nivåerna Basic, standard och Premium. Vanligt vis har varje nivå flera pris-och prestanda nivåer. Genom att använda [pris kalkylatorn online](https://azure.microsoft.com/pricing/calculator)kan du skapa pris beräkningar. Kalkylatorn innehåller flexibilitet för att beräkna kostnaden för en enskild resurs eller en grupp med resurser.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager är en mekanism för distribution, hantering och organisation för Azure-resurser. Med hjälp av Resource Manager kan du samla in många enskilda resurser i en resurs grupp.

Resource Manager innehåller även distributions funktioner som möjliggör anpassningsbar distribution och konfiguration av relaterade resurser. Med hjälp av Resource Manager kan du till exempel distribuera ett program som består av flera virtuella datorer, en belastningsutjämnare och en databas i Azure SQL Database som en enda enhet. Du utvecklar dessa distributioner med hjälp av en Resource Manager-mall.

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera lösningen flera gånger under utvecklings livs cykeln och vara säker på att dina resurser distribueras i ett konsekvent tillstånd.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

- Du kan använda åtkomst kontroll för alla tjänster i resurs gruppen eftersom Azure RBAC är inbyggt integrerat i hanterings plattformen.

- Du kan använda taggar på resurser för att logiskt organisera alla resurser i din prenumeration.

- Du kan klargöra organisationens fakturering genom att Visa kostnader för en grupp resurser som delar samma tagg.

### <a name="tips-for-creating-resource-groups"></a>Tips för att skapa resurs grupper

När du fattar beslut om dina resurs grupper bör du tänka på följande tips:

- Alla resurser i en resurs grupp måste ha samma livs cykel.

- Du kan endast tilldela en resurs till en grupp i taget.

- Du kan när som helst lägga till eller ta bort en resurs från en resurs grupp. Varje resurs måste tillhöra en resurs grupp. Så om du tar bort en resurs från en grupp måste du lägga till den i en annan.

- Du kan när som helst flytta de flesta typer av resurser till en annan resurs grupp.

- Resurserna i en resurs grupp kan finnas i olika regioner.

- Du kan använda en resurs grupp för att kontrol lera åtkomsten till resurserna i den.

### <a name="building-resource-manager-templates"></a>Skapa Resource Manager-mallar

I Resource Manager-mallar definieras de resurser och resurspooler som ska distribueras till en enda resurs grupp. Du kan använda Resource Manager-mallar för att dirigera komplexa distributioner utan att behöva överskrida skript eller manuell konfiguration. När du har utvecklat en mall kan du distribuera den flera gånger – varje gång med ett identiskt resultat.

En Resource Manager-mall består av fyra delar:

- **Parametrar**: dessa är indata till distributionen. Parameter värden kan tillhandahållas av en mänsklig eller automatiserad process. En exempel parameter kan vara ett administratörs användar namn och lösen ord för en virtuell Windows-dator. Parameter värden används i hela distributionen när de anges.

- **Variabler**: dessa används för att lagra värden som används i hela distributionen. Till skillnad från parametrar anges inte ett variabel värde vid distributions tiden. I stället är den hårdkodad eller dynamiskt genererad.

- **Resurser**: det här avsnittet i mallen definierar vilka resurser som ska distribueras, till exempel virtuella datorer, lagrings konton och virtuella nätverk.

- **Utdata**: när en distribution har avslut ATS kan Resource Manager returnera data, till exempel dynamiskt genererade anslutnings strängar.

Följande mekanismer är tillgängliga för Automation-distribution:

- **Functions**: du kan använda flera funktioner i Resource Manager-mallar. Detta omfattar åtgärder som att konvertera en sträng till gemener, distribuera flera instanser av en definierad resurs och dynamiskt returnera mål resurs gruppen. Resource Manager-funktioner hjälper till att bygga dynamiska distributioner.

- **Resurs beroenden**: när du distribuerar flera resurser, kommer vissa resurser vara beroende av andra. För att under lätta distributionen kan du använda en beroende deklaration så att beroende resurser distribueras före andra.

- **Mall länkning**: inifrån en Resource Manager-mall kan du länka till en annan mall. Detta möjliggör distributions sammansättning i en uppsättning riktade, specifika mallar.

Du kan bygga Resource Manager-mallar i valfri text redigerare. Azure SDK för Visual Studio innehåller emellertid verktyg som hjälper dig. Med hjälp av Visual Studio kan du lägga till resurser till mallen via en guide och sedan distribuera och felsöka mallen direkt från Visual Studio. Mer information finns i [redigera Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).

Slutligen kan du konvertera befintliga resurs grupper till en återanvändbar mall från Azure Portal. Detta kan vara användbart om du vill skapa en distributions bara mall för en befintlig resurs grupp eller om du bara vill granska den underliggande JSON. Om du vill exportera en resurs grupp väljer du knappen **Automation-skript** från resurs gruppens inställningar.

## <a name="security-of-azure-resources-azure-rbac"></a>Säkerhet för Azure-resurser (Azure RBAC)

Du kan bevilja drift åtkomst till användar konton vid en angiven omfattning: prenumeration, resurs grupp eller enskild resurs. Det innebär att du kan distribuera en uppsättning resurser till en resurs grupp, till exempel en virtuell dator och alla relaterade resurser, samt bevilja behörighet till en speciell användare eller grupp. Den här metoden begränsar åtkomsten till de resurser som tillhör mål resurs gruppen. Du kan också bevilja åtkomst till en enskild resurs, till exempel en virtuell dator eller ett virtuellt nätverk.

Om du vill bevilja åtkomst tilldelar du en roll till användaren eller användar gruppen. Det finns många fördefinierade roller. Du kan också definiera egna anpassade roller.

Här följer några exempel på [inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md):

- **Ägare**: en användare med den här rollen kan hantera allt, inklusive åtkomst.

- **Läsare**: en användare med den här rollen kan läsa resurser av alla typer (utom hemligheter), men kan inte göra ändringar.

- **Virtuell dator deltagare**: en användare med den här rollen kan hantera virtuella datorer, men kan inte hantera det virtuella nätverk som de är anslutna till eller det lagrings konto där VHD-filen finns.

- **SQL DB-deltagare**: en användare med den här rollen kan hantera SQL-databaser men inte deras säkerhetsrelaterade principer.

- **SQL Security Manager**: en användare med den här rollen kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser.

- **Lagrings konto deltagare**: en användare med den här rollen kan hantera lagrings konton, men kan inte hantera åtkomst till lagrings kontona.

Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines är en av de centrala IaaS-tjänsterna i Azure. Azure Virtual Machines stöder distribution av virtuella Windows-eller Linux-datorer i ett Microsoft Azure Data Center. Med Azure Virtual Machines har du total kontroll över VM-konfigurationen och ansvarar för all installation, konfiguration och underhåll av program vara.

När du distribuerar en virtuell Azure-dator kan du välja en avbildning från Azure Marketplace, eller så kan du ange en egen generaliserad avbildning. Den här avbildningen används för att tillämpa operativ systemet och den inledande konfigurationen. Under distributionen hanterar Resource Manager vissa konfigurations inställningar, till exempel att tilldela dator namn, administratörs behörighet och nätverks konfiguration. Du kan använda tillägg för virtuella Azure-datorer för att ytterligare automatisera konfigurationer, till exempel program varu installation, antivirus konfiguration och övervaknings lösningar.

Du kan skapa virtuella datorer i många olika storlekar. Storleken på den virtuella datorn avgör resursallokeringen, till exempel bearbetning, minne och lagrings kapacitet. I vissa fall är specifika funktioner som RDMA-aktiverade nätverkskort och SSD-diskar bara tillgängliga med vissa VM-storlekar. En fullständig lista över VM-storlekar och-funktioner finns i "storlekar för virtuella datorer i Azure" för [Windows](../../virtual-machines/sizes.md) och [Linux](../../virtual-machines/sizes.md).

### <a name="use-cases"></a>Användningsfall

Eftersom Azure Virtual Machines ger fullständig kontroll över konfigurationen, är de idealiska för ett brett utbud av Server arbets belastningar som inte passar in i en PaaS modell. Server arbets belastningar som t. ex. databas servrar (SQL Server, Oracle eller MongoDB), Windows Server Active Directory, Microsoft SharePoint och många fler blir möjliga att köra på Microsoft Azure-plattformen. Om du vill kan du flytta sådana arbets belastningar från ett lokalt Data Center till en eller flera Azure-regioner, utan en stor del omkonfiguration.

### <a name="deployment-of-virtual-machines"></a>Distribution av virtuella datorer

Du kan distribuera virtuella Azure-datorer med hjälp av Azure Portal med hjälp av Automation med modulen Azure PowerShell eller genom att använda Automation med plattforms oberoende CLI.

#### <a name="portal"></a>Portal

Att distribuera en virtuell dator med hjälp av Azure Portal kräver bara en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan välja många olika operativ system avbildningar med varierande konfigurationer. Alla krav på lagring och nätverk konfigureras under distributionen. Mer information finns i "skapa en virtuell dator i Azure Portal" för [Windows](../../virtual-machines/windows/quick-create-portal.md) och [Linux](../../virtual-machines/linux/quick-create-portal.md).

Förutom att distribuera en virtuell dator från Azure Portal kan du distribuera en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser som definieras i mallen. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Genom att distribuera en virtuell Azure-dator med hjälp av PowerShell kan du slutföra distributions automatisering av alla relaterade resurser för virtuella datorer, inklusive lagring och nätverk. Mer information finns i [skapa en virtuell Windows-dator med Resource Manager och PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Förutom att distribuera Azure Compute-resurser individuellt kan du använda Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen tillhandahåller Azures kommando rads gränssnitt distributions automatisering och kan användas på Windows-, OS X-eller Linux-system. När du använder kommandot snabb registrering för Azure CLI **virtuell dator** distribueras alla relaterade resurser för virtuella datorer (inklusive lagring och nätverk) och den virtuella datorn. Mer information finns i [skapa en virtuell Linux-dator i Azure med hjälp av CLI](../../virtual-machines/linux/quick-create-cli.md).

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Åtkomst och säkerhet för virtuella datorer

Åtkomst till en virtuell dator från Internet kräver att det associerade nätverks gränssnittet eller belastningsutjämnaren, om det är tillämpligt, ska konfigureras med en offentlig IP-adress. Den offentliga IP-adressen innehåller ett DNS-namn som kommer att matcha den virtuella datorn eller belastningsutjämnaren. Mer information finns i [IP-adresser i Azure](../../virtual-network/public-ip-addresses.md).

Du kan hantera åtkomsten till den virtuella datorn via den offentliga IP-adressen med hjälp av en nätverks säkerhets grupps resurs (NSG). En NSG fungerar som en brand vägg och tillåter eller nekar trafik över nätverks gränssnittet eller under nätet på en uppsättning definierade portar. Om du till exempel vill skapa en fjärrskrivbordssession med en virtuell Azure-dator måste du konfigurera NSG för att tillåta inkommande trafik på port 3389. Mer information finns i [öppna portar till en virtuell dator i Azure med hjälp av Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md).

Precis som med hantering av alla dator system bör du tillhandahålla säkerhet för en virtuell Azure-dator på operativ systemet med hjälp av säkerhets referenser och brand väggar för program vara.

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en Microsoft-hanterad tjänst som tillhandahåller hållbar, skalbar och redundant lagring. Du kan lägga till ett Azure Storage-konto som en resurs till en resurs grupp med hjälp av valfri resurs distributions metod. Azure innehåller fyra lagrings typer: Blob Storage, File Storage, Table Storage och Queue Storage. När du distribuerar ett lagrings konto är två konto typer tillgängliga, generell användnings-och blob-lagring. Ett allmänt lagrings konto ger dig åtkomst till alla fyra lagrings typer. Blob Storage-konton liknar allmänna-syfte-konton, men innehåller specialiserade blobbar som innehåller frekventa och kall åtkomst nivåer. Mer information om Blob Storage finns i [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md).

Azure Storage-konton kan konfigureras med olika nivåer av redundans:

- **Lokalt Redundant lagring** ger hög tillgänglighet genom att se till att tre kopior av alla data görs synkront innan en skrivning bedöms lyckas. Dessa kopior lagras på en enda plats i en enda region. Replikerna finns i olika fel domäner och uppgraderings domäner. Det innebär att data är tillgängliga även om en lagrings nod som håller dina data Miss lyckas eller är offline för att uppdateras.

- **Geo-redundant lagring** gör tre synkrona kopior av data i den primära regionen för hög tillgänglighet och gör sedan asynkront tre repliker i en länkad region för haveri beredskap.

- Geo **-redundant lagring med Läs behörighet** är Geo-redundant lagring och möjligheten att läsa data i den sekundära regionen. Den här funktionen gör det lämpligt för partiell haveri beredskap. Om det är problem med den primära regionen kan du ändra ditt program så att det har skrivskyddad åtkomst till den kopplade regionen.

### <a name="use-cases"></a>Användningsfall

Varje lagrings typ har ett annat användnings fall.

#### <a name="blob-storage"></a>Blob Storage

Ordet *BLOB* är en akronym för *Binary Large Object*. Blobbar är ostrukturerade filer som de som du lagrar på din dator. Blob Storage kan lagra alla slags textdata eller binära data, till exempel ett dokument, en mediefil eller ett installationsprogram. Blob Storage kallas även för objektlagring. Azure Blob Storage innehåller också Azure Virtual Machines data diskar.

Azure Storage stöder tre typer av blobar:

- **Block-blobbar** används för att lagra vanliga filer upp till 195 GB i storlek (4 MB × 50 000 block). I första hand används blockblobar för lagring av filer som läses från början till slut, som mediefiler eller bildfiler för webbplatser. De är namngivna block blobbar eftersom filer som är större än 64 MB måste laddas upp som små block. Dessa block sammanställs sedan (eller skickas) till den slutliga blobben.

- **Page blobbar** används för att hålla slumpmässiga åtkomst till filer på upp till 1 TB. Page blobbar används främst som lagring för de virtuella hård diskar som tillhandahåller varaktiga diskar för Azure Virtual Machines, IaaS Compute service i Azure. De kallas för sidblobar eftersom de erbjuder slumpmässig läs-/skrivåtkomst till sidor på 512 bytes.

- **Tillägg av blobbar** består av block som block blobbar, men de är optimerade för att lägga till åtgärder. Dessa används ofta för att logga information från en eller flera källor till samma blob. Du kan till exempel skriva all spårnings loggning till samma tilläggs-BLOB för ett program som körs på flera virtuella datorer. En enda tilläggsblob kan ha upp till 195 GB.

Mer information finns i [Kom igång med Azure Blob Storage med hjälp av .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

#### <a name="file-storage"></a>File Storage

Azure File Storage är en tjänst som erbjuder fil resurser i molnet med hjälp av SMB-standardprotokollet (Server Message Block). Tjänsten stöder både SMB 2,1 och SMB 3,0. Med Azure File Storage kan du migrera program som förlitar sig på fil resurser till Azure snabbt och utan kostsamma omskrivningar. Program som körs på virtuella Azure-datorer, i moln tjänster eller från lokala klienter kan montera en fil resurs i molnet. Detta liknar hur ett Skriv bords program monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en fil lagrings resurs är en vanlig SMB-filresurs kan program som körs i Azure komma åt data i resursen via fil systemets I/O-API: er. Utvecklare kan därför använda sin befintliga kod och dina befintliga kunskaper för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdletar för att skapa, montera och hantera fil lagrings resurser som en del av administrationen av Azure-program.

Mer information finns i [komma igång med Azure File Storage i Windows](../../storage/files/storage-how-to-use-files-windows.md) eller [så här använder du Azure File Storage med Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

Azure Table Storage är en tjänst som lagrar strukturerade NoSQL-data i molnet. Table Storage är ett Arkiv för nyckel/attribut med en schema lös design. Eftersom Table Storage är schema-mindre, är det enkelt att anpassa dina data när dina program behöver utvecklas. Åtkomsten till data är snabb och kostnadseffektiv för alla typer av program. Kostnaden för Table Storage är normalt sett betydligt lägre än för motsvarande volymer med traditionell SQL.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. Du kan lagra valfritt antal entiteter i en tabell. Ett lagrings konto kan innehålla valfritt antal tabeller, upp till lagrings kontots kapacitets gräns.

Mer information finns i [Kom igång med Azure Table Storage](../../cosmos-db/tutorial-develop-table-dotnet.md).

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. Vid utformning av program för skalning är program komponenterna ofta fristående så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Mer information finns i [Kom igång med Azure Queue Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Distribuera ett lagrings konto

Det finns flera alternativ för att distribuera ett lagrings konto.

#### <a name="portal"></a>Portal

Distribution av ett lagrings konto med hjälp av Azure Portal kräver bara en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt lagrings konto till en ny eller befintlig resurs grupp. När du har skapat lagrings kontot kan du skapa en BLOB-behållare eller fil resurs med hjälp av portalen. Du kan skapa lagrings enheter för tabeller och köer program mässigt. Mer information finns i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

Förutom att distribuera ett lagrings konto från Azure Portal kan du distribuera en Azure Resource Manager-mall från portalen. Detta distribuerar och konfigurerar alla resurser som definieras i mallen, inklusive lagrings konton. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Genom att distribuera ett Azure Storage-konto med hjälp av PowerShell kan du slutföra distributions automatisering av lagrings kontot. Mer information finns i [använda Azure PowerShell med Azure Storage](/powershell/module/az.storage/).

Förutom att distribuera Azure-resurser individuellt kan du använda Azure PowerShell-modulen för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Kommandoradsgränssnittet (CLI)

Precis som med PowerShell-modulen tillhandahåller Azures kommando rads gränssnitt distributions automatisering och kan användas på Windows-, OS X-eller Linux-system. Du kan skapa ett lagrings konto med hjälp av kommandot **skapa** ett lagrings konto i Azure CLI. Mer information finns i [använda Azure CLI med Azure Storage.](../../storage/blobs/storage-quickstart-blobs-cli.md)

På samma sätt kan du använda Azure CLI för att distribuera en Azure Resource Manager-mall. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Åtkomst och säkerhet för Azure Storage

Azure Storage kan nås på olika sätt, inklusive trots att Azure Portal, vid skapande och drift av virtuella datorer och från lagrings klient bibliotek.

#### <a name="virtual-machine-disks"></a>Virtuella dator diskar

När du distribuerar en virtuell dator måste du också skapa ett lagrings konto för att lagra operativ system disken för den virtuella datorn och eventuella ytterligare data diskar. Du kan välja ett befintligt lagrings konto eller skapa ett nytt. Eftersom den maximala storleken för en BLOB är 1 024 GB har en enda virtuell dator disk en maximal storlek på 1 023 GB. Om du vill konfigurera en större data disk kan du presentera flera data diskar för den virtuella datorn och pool dem tillsammans som en enda logisk disk. Mer information finns i Hantera Azure-diskar för [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) och [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Lagrings verktyg

Azure Storage-konton kan nås via många olika lagrings Utforskare, till exempel Visual Studio Cloud Explorer. Med dessa verktyg kan du bläddra igenom lagrings konton och data. Mer information och en lista över tillgängliga lagrings Utforskare finns i [Azure Storage klient verktyg](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Lagrings-API

Lagrings resurser kan nås av alla språk som kan göra HTTP/HTTPS-begäranden. Dessutom erbjuder Azure Storage programmeringsbibliotek för flera populära språk. Dessa bibliotek fören klar arbetet med Azure Storage genom att hantera information som synkront och asynkront anrop, batchbearbetning av åtgärder, undantags hantering och automatiska återförsök. Mer information finns i [Azure Storage tjänst REST API referens](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Lagrings åtkomst nycklar

Varje lagrings konto har två autentiseringsinställningar, en primär och en sekundär. Kan användas för lagrings åtkomst åtgärder. Dessa lagrings nycklar används för att skydda ett lagrings konto och krävs för program mässig åtkomst till data. Det finns två nycklar för att tillåta tillfällig förnyelse av nycklar för att förbättra säkerheten. Det är viktigt att hålla nycklarna säkra eftersom deras innehav, tillsammans med konto namnet, ger obegränsad åtkomst till alla data i lagrings kontot.

#### <a name="shared-access-signatures"></a>Signaturer för delad åtkomst

Om du behöver tillåta att användare har kontrollerad åtkomst till dina lagrings resurser kan du skapa en signatur för delad åtkomst. En signatur för delad åtkomst är en token som kan läggas till i en URL som möjliggör delegerad åtkomst till en lagrings resurs. Alla som har token kan komma åt resursen som den pekar på med de behörigheter som anges, under den tids period som den är giltig. Mer information finns i [använda signaturer för delad åtkomst](../../storage/common/storage-sas-overview.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuella nätverk är nödvändiga för att stödja kommunikation mellan virtuella datorer. Du kan definiera undernät, anpassad IP-adress, DNS-inställningar, säkerhets filtrering och belastnings utjämning. Azure har stöd för olika användnings fall: endast molnbaserade nätverk eller hybrid virtuella nätverk.

### <a name="cloud-only-virtual-networks"></a>Endast molnbaserade virtuella nätverk

Ett virtuellt Azure-nätverk är som standard endast tillgängligt för resurser som är lagrade i Azure. Resurser som är anslutna till samma virtuella nätverk kan kommunicera med varandra. Du kan koppla nätverks gränssnitt och belastningsutjämnare för virtuella datorer med en offentlig IP-adress för att göra den virtuella datorn tillgänglig via Internet. Du kan skydda åtkomsten till de offentligt exponerade resurserna genom att använda en nätverks säkerhets grupp.

![Azure Virtual Network för ett webb program med två nivåer](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Virtuella hybrid nätverk

Du kan ansluta ett lokalt nätverk till ett virtuellt Azure-nätverk med hjälp av ExpressRoute eller VPN-anslutning från plats till plats. I den här konfigurationen är det virtuella Azure-nätverket i grunden ett molnbaserad tillägg till ditt lokala nätverk.

Eftersom det virtuella Azure-nätverket är anslutet till ditt lokala nätverk måste lokala virtuella nätverk använda en unik del av adress utrymmet som din organisation använder. På samma sätt som olika företags platser tilldelas ett särskilt IP-undernät blir Azure en annan plats när du utökar nätverket.
Det finns flera alternativ för att distribuera ett virtuellt nätverk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Kommandoradsgränssnittet (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-mallar

> **När du ska använda**: varje gång du arbetar med virtuella datorer i Azure kommer du att arbeta med virtuella nätverk. Detta möjliggör segmentering av dina virtuella datorer i offentliga och privata undernät som liknar lokala data Center.
>
> **Kom igång**: att distribuera ett virtuellt Azure-nätverk med hjälp av Azure Portal kräver bara en aktiv Azure-prenumeration och åtkomst till en webbläsare. Du kan distribuera ett nytt virtuellt nätverk till en ny eller befintlig resurs grupp. När du skapar en ny virtuell dator från portalen kan du välja ett befintligt virtuellt nätverk eller skapa ett nytt. Kom igång och [skapa ett virtuellt nätverk med hjälp av Azure Portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Åtkomst och säkerhet för virtuella nätverk

Du kan skydda virtuella Azure-nätverk med hjälp av en nätverks säkerhets grupp. NSG: er innehåller en lista över regler för åtkomst kontrol lista (ACL) som tillåter eller nekar nätverks trafik till dina VM-instanser i ett virtuellt nätverk. Du kan associera NSG: er med antingen undernät eller enskilda VM-instanser inom det under nätet. När du associerar en NSG med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det under nätet. Dessutom kan du ytterligare begränsa trafik till en enskild virtuell dator genom att associera en NSG direkt med den virtuella datorn. Mer information finns i [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa en virtuell Windows-dator](../../virtual-machines/windows/quick-create-portal.md)
- [Skapa en virtuell Linux-dator](../../virtual-machines/linux/quick-create-portal.md)