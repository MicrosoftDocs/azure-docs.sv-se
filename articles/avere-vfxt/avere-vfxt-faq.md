---
title: Vanliga frågor och svar – AVERT vFXT för Azure
description: Använd dessa vanliga frågor och svar för att avgöra om AVERT vFXT för Azure passar dina behov. Lär dig hur du fungerar med AVERT vFXT for Azure tillsammans med andra Azure-komponenter.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: c6459e2daf17772b28ee53289754deb33b4823ae
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272699"
---
# <a name="avere-vfxt-for-azure-faq"></a>Vanliga frågor och svar om Avere vFXT för Azure

I den här artikeln får du svar på frågor som kan hjälpa dig att avgöra om AVERT vFXT för Azure passar dina behov. Den ger grundläggande information om AVERT vFXT och förklarar hur det fungerar med andra Azure-komponenter och med produkter från externa leverantörer.

## <a name="general"></a>Allmänt

### <a name="what-is-avere-vfxt-for-azure"></a>Vad är Avere vFXT för Azure?

Aver vFXT for Azure är ett fil system med hög prestanda som cachelagrar aktiva data i Azure Compute för effektiv bearbetning av kritiska arbets belastningar.

### <a name="is-avere-vfxt-a-storage-solution"></a>Är AVERT vFXT en lagrings lösning?

Nej. Aver vFXT for Azure är en fil systemets *cache* som bifogas till lagrings miljöer, till exempel din EMC-eller NetApp NAS eller en Azure Blob-behållare. Aver vFXT effektiviserar data begär Anden från klienter, och cachelagrar de data som används för att förbättra prestanda i hög skala och över tid. Aver vFXT lagrar inte data. Det finns ingen information om mängden data som lagras bakom den.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Är AVERT vFXT en nivå lösning?

Aver vFXT for Azure hanterar inte automatiskt data mellan frekventa och låg frekventa nivåer.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hur gör jag för att vet du om en miljö är rätt för aver-vFXT?

Det bästa sättet att tänka på om den här frågan är att fråga "är arbets belastningen som cacheable?" Det vill säga att arbets belastningen har ett högt Skriv-till-Skriv-förhållande? Ett exempel är 80/20 eller 70/30 läsningar/skrivningar.

Överväg att aver vFXT för Azure om du har en filbaserad analytisk pipeline som körs på ett stort antal virtuella Azure-datorer och uppfyller ett eller flera av följande villkor:

* Den allmänna prestandan är långsam eller inkonsekvent på grund av långa fil åtkomst tider (i millisekunder eller sekunder beroende på krav). Den här fördröjningen accepteras inte för kunden.

* Data som krävs för bearbetning befinner sig i slutet av en WAN-miljö och det är opraktiskt att flytta data permanent. Data kan finnas i en annan Azure-region eller i ett kund Data Center.

* Ett stort antal klienter begär data, till exempel i ett HPC-kluster (data behandling med höga prestanda). Det stora antalet samtidiga förfrågningar kan öka svars tiden.

* Kunden vill köra sin nuvarande pipeline "i befintligt skick" i Azure Virtual Machines och behöver en POSIX-baserad lösning för delad lagring (eller caching) för skalbarhet. Genom att använda AVERT vFXT för Azure behöver du inte bygga om arbets pipelinen för att göra interna anrop till Azure Blob Storage.

* Ditt HPC-program baseras på NFSv3-klienter. (I vissa fall kan den använda SMB 2,1-klienter, men prestanda är begränsad.)

Följande diagram kan hjälpa dig att besvara den här frågan. Närmare ditt arbets flöde är i det övre högra hörnet, desto mer sannolikt är det att den Avera vFXT för Azure caching-lösningen är rätt för din miljö.

![Diagram diagram som visar att Läs tung belastning med tusentals klienter passar bättre för aver vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>I vilken skala för klienter gör vFXT-lösningen för att lösa det mest begripliga?

Lösningen aver vFXT cache är byggd för att hantera hundratals, tusentals eller flera tusen data bearbetnings kärnor. Om du har ett fåtal datorer som kör lätt arbete är AVERT vFXT inte rätt lösning.

Vanliga Avera vFXT-kunder kör krävande arbets belastningar med början på cirka 1 000 CPU-kärnor. De här miljöerna kan vara så stora som 50 000 kärnor eller mer. Eftersom AVERT vFXT är skalbart kan du lägga till noder som stöder dessa arbets belastningar när de växer för att kräva mer data flöde eller mer IOPS.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hur mycket data kan ett vFXT-miljölagrat "AVERT"?

Aver vFXT för Azure är en cache. Data lagras inte särskilt. Den använder en kombination av RAM-och SSD för att lagra cachelagrade data. Data lagras permanent på ett Server dels lagrings system (till exempel ett NetApp NAS-system eller en BLOB-behållare). AVERT vFXT-systemet saknar information om mängden data som lagras bakom det. Aver vFXT cachelagrar bara den delmängd av de data som klienterna begär.  

### <a name="what-regions-are-supported"></a>Vilka regioner stöds?

Aver vFXT for Azure stöds i alla regioner utom för suveräna regioner (Kina, Tyskland). Se till att den region som du vill använda kan stödja den stora mängden beräknings kärnor och de VM-instanser som krävs för att skapa det AVERT vFXT klustret.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hur gör jag för att få hjälp med att använda AVERT vFXT?

En specialiserad grupp med support personal erbjuder hjälp med att Avera vFXT för Azure. Följ instruktionerna i [få hjälp med systemet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) för att öppna ett support ärende från Azure Portal.

### <a name="is-avere-vfxt-highly-available"></a>Är AVERT vFXT hög tillgängligt?

Ja, aver vFXT körs exklusivt som en HA-lösning.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Stöder AVERT vFXT för Azure även andra moln tjänster?

Ja, kunder kan använda mer än en moln leverantör med AVERT vFXT-klustret. Det stöder AWS S3 standard buckets, Google Cloud Services standard buckets och Azure Blob-behållare.

> [!NOTE]
> En program varu avgift gäller att använda AVERT vFXT med AWS eller Google Cloud Storage. Det finns ingen ytterligare program varu avgift för att använda Azure Blob Storage.

## <a name="technical-compute"></a>Teknisk: beräkning

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Kan du beskriva vad en aver vFXT-miljö "ser ut som"?

Aver vFXT är en klustrad apparat som består av flera virtuella Azure-datorer. Ett Python-bibliotek hanterar skapande, borttagning och ändring av kluster. Läs [Vad är AVERT vFXT för Azure?](avere-vfxt-overview.md) om du vill veta mer.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Vilken typ av virtuella Azure-datorer är det att AVERT vFXT körs på?  

Ett AVERT vFXT för Azure-kluster använder Microsoft Azure E32s_v3 virtuella datorer.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Skalar du vFXT-miljön för AVERT?

Ditt AVERT vFXT-kluster kan vara så litet som tre noder för virtuella datorer eller så stora som 24 noder. Kontakta teknisk support för Azure om du vill ha hjälp med att planera om du tror att du behöver ett kluster på fler än nio noder. Det större antalet noder kräver en större distributions arkitektur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Är den Avera vFXT-miljön "autoskalning"?

Nej. Du kan skala kluster storleken uppåt och nedåt, men om du lägger till eller tar bort klusternoder är ett manuellt steg.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan jag köra det AVERT vFXT-kluster som en skalnings uppsättning för virtuella datorer?

Aver vFXT stöder inte distribution av en skalnings uppsättning för virtuella datorer. Flera inbyggda funktioner för tillgänglighets stöd är endast utformade för atomiska virtuella datorer som ingår i ett kluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Kan jag köra det AVERT vFXT-kluster på virtuella datorer med låg prioritet?

Nej, systemet kräver en underliggande stabil uppsättning virtuella datorer.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Kan jag köra det AVERT vFXT-kluster i behållare?

Nej, aver vFXT måste distribueras som ett oberoende program.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Räknar vFXT virtuella datorer mot min beräknings kvot?

Ja. Kontrol lera att du har tillräcklig kvot i regionen för att stödja klustret.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan jag köra kluster datorerna AVERT vFXT i olika tillgänglighets zoner?

Nej. Modellen för hög tillgänglighet i AVERT vFXT har för närvarande inte stöd för enskilda aver vFXT-kluster medlemmar i olika tillgänglighets zoner.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan jag klona AVERT vFXT Virtual Machines?

Nej, du måste använda det Python-skript som stöds för att lägga till eller ta bort noder i det Avera vFXT-klustret. Mer information finns [i hantera AVERT vFXT-kluster](avere-vfxt-manage-cluster.md).

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Finns det en "VM"-version av program varan som kan köras i min egen lokala miljö?

Nej, systemet erbjuds som en klustrad apparat och testas på vissa typer av virtuella datorer. Den här begränsningen hjälper kunder att undvika att skapa ett system som inte har stöd för höga prestanda krav i ett typiskt AVERT vFXT-arbetsflöde.

## <a name="technical-disks"></a>Teknisk: diskar

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Vilka typer av diskar stöds för virtuella Azure-datorer?

Aver vFXT för Azure kan använda 1 – TB eller 4 – TB Premium SSD-konfigurationer. Premium SSD-konfigurationen kan distribueras som flera hanterade diskar.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Stöder klustret ohanterade diskar?

Nej, klustret kräver hanterade diskar.

### <a name="does-the-system-support-local-attached-ssds"></a>Stöder systemet lokala (anslutna) SSD?

Aver vFXT för Azure har för närvarande inte stöd för lokala SSD. Diskar som används för AVERT vFXT måste kunna stängas av och startas om, men lokalt anslutna SSD i den här konfigurationen kan bara avbrytas.

### <a name="does-the-system-support-ultra-ssds"></a>Stöder systemet Ultra SSD?

Nej, systemet stöder bara Premium SSD-konfigurationer.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan jag koppla bort mitt Premium-SSD och återansluta dem senare för att bevara cache-innehåll mellan användning?

Det finns inte stöd för att ta bort och återansluta SSD. Metadata eller fil innehåll på källan kan ha ändrats mellan användnings områden, vilket kan orsaka problem med data integriteten.

### <a name="does-the-system-encrypt-the-cache"></a>Krypterar systemet cacheminnet?

Data överförs över diskarna, men krypteras inte. Diskarna kan dock vara krypterade. Mer information finns i [skydda och använda principer på virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Teknisk: nätverk

### <a name="what-network-is-recommended"></a>Vilket nätverk rekommenderas?

Om du använder lokal lagring med aver vFXT bör du ha en 1 Gbit/s och bättre nätverks anslutning mellan lagrings utrymmet och klustret. Om du har en liten mängd data och vill kopiera data till molnet innan du kör jobb, kan VPN-anslutningen räcka.

> [!TIP]
> Den långsammare nätverks länken är, de långsammaste första "kall" läsningarna blir. Långsamma läsningar ökar svars tiden för arbets pipelinen.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan jag köra AVERT vFXT i ett annat virtuellt nätverk än mitt beräknings kluster?

Ja, du kan skapa ditt AVERT vFXT-system i ett annat virtuellt nätverk. Läs [planera ditt AVERT vFXT-system](avere-vfxt-deploy-plan.md) för mer information.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Kräver AVERT vFXT sitt eget undernät?

Ja. AVERT vFXT körs strikt som ett kluster med hög tillgänglighet (HA) och kräver flera IP-adresser för att fungera. Om klustret finns i ett eget undernät undviker du risken för IP-konflikter, vilket kan orsaka problem vid installation och normal drift. Klustrets undernät kan finnas i ett virtuellt nätverk som används av andra resurser, så länge inga IP-adresser överlappar varandra.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kan jag köra AVERT vFXT på InfiniBand?

Nej, AVERT vFXT använder endast Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hur gör jag för att åtkomst till min lokala NAS-miljö från AVERT vFXT?

Den Avera vFXT-miljön är som alla andra virtuella Azure-datorer i så att den kräver dirigerad åtkomst via en nätverksgateway eller VPN till kundens Data Center (och bak). Överväg att använda Azure ExpressRoute-anslutningen om den är tillgänglig i din miljö.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Vilka är bandbredds kraven för AVERT vFXT?

Det övergripande kravet på bandbredd beror på två faktorer:

* Mängden data som begärs från källan
* Klient systemets tolerans för svars tid under inledande data inläsning  

För latens känsliga miljöer bör du använda en fiber lösning med en minsta länk hastighet på 1 Gbit/s. Använd ExpressRoute om det är tillgängligt.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kan jag köra AVERT vFXT med offentliga IP-adresser?

Nej, aver vFXT är avsett att användas i en nätverks miljö som skyddas via bästa praxis.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kan jag begränsa Internet åtkomst från mitt klusters virtuella nätverk?

I allmänhet kan du konfigurera ytterligare säkerhet på det virtuella nätverket efter behov, men vissa begränsningar kan störa klustrets drift.

Om du till exempel begränsar utgående Internet åtkomst från det virtuella nätverket uppstår problem med klustret, såvida du inte även lägger till en regel som uttryckligen tillåter åtkomst till AzureCloud. Den här situationen beskrivs i [kompletterande dokumentation på GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Om du behöver hjälp med anpassad säkerhet kontaktar du supporten enligt beskrivningen i [få hjälp med systemet](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Teknisk: backend-lagring (Core-filer)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hur många Core-filmerare har en enda aver vFXT-miljö stöd för?

Ett AVERT vFXT-kluster har stöd för upp till 20 Core-Filskydd.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hur lagrar vFXT-miljön data?

AVERT vFXT är inte lagrings utrymme. Det är ett cacheminne som läser och skriver data från flera lagrings mål som kallas core-filer. Data som lagras på Premium SSD-diskar i AVERT vFXT är tillfälliga och skickas slutligen till backend-lagringen på Server sidan.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Vilka Core-stöder vFXT-support?

I allmänhet stöder AVERT vFXT för Azure följande system som core-filer:

* Dell EMC-Isilon (OneFS 7,1, 7,2, 8,0 och 8,1) 
* NetApp ONTAP (klustrat läge 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) och (7-läge 7. *, 8.0-8.3)

* Azure Blob-behållare (endast lokalt redundant lagring)
* AWS S3-buckets
* Google Cloud-buckets

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Varför stöder inte AVERT vFXT alla NFS-filer?

Även om alla NFS-plattformar uppfyller samma IETF-standarder, är det i praktiken att varje implementation har sin egen knep. Informationen påverkar hur AVERT vFXT interagerar med lagrings systemet. De system som stöds är de mest använda plattformarna i Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Stöder aver vFXT privat objekt lagring (t. ex. SwiftStack)?

Aver vFXT har inte stöd för lagring av privata objekt.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hur kan jag få en speciell lagrings produkt under support?

Support baseras på mängden efter frågan i fältet. Om det finns tillräckligt med intäktsbaserade förfrågningar för att stödja en NAS-lösning kommer vi att ta hänsyn till den. Gör förfrågningar via supporten för Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan jag använda Azure Blob Storage som ett kärnor?

Ja, aver vFXT för Azure kan använda en Block-Blob-behållare som en moln kärna.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Vilka är kraven på lagrings kontot för en BLOB-kärna?

Ditt lagrings konto måste vara ett GPv2-konto (General-Purpose v2) och konfigurerat enbart för lokalt redundant lagring. Geo-redundant lagring och zon-redundant lagring stöds inte.

Läs [Azure Blob Storage Cloud Core](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) -filer för mer information om lagrings konto kraven.

### <a name="can-i-use-archive-blob-storage"></a>Kan jag använda Arkiv Blob Storage?

Nej. Service avtals avtalet (SLA) för Arkiv lag ring är inte kompatibelt med det lokala katalog-och fil åtkomst behovet i det AVERT vFXT systemet.

### <a name="can-i-use-cool-blob-storage"></a>Kan jag använda cool Blob Storage?

Blob-lagring på låg nivå rekommenderas vanligt vis inte för ett AVERT vFXT för Azure core-filer. Låg frekvent nivå erbjuder lägre kostnader för lagring men högre drifts kostnader. (Se [Block Blob-prissättning](<https://azure.microsoft.com/pricing/details/storage/blobs/>) för mer information.) Om data kommer att kommas åt och ändras eller tas bort ofta, bör du överväga att använda frekvent nivå.

[Åtkomst nivåer](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) ger mer information om när det kan vara bra att använda lagring på låg frekvent nivå som vFXT kärnor.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hur gör jag för att kryptera BLOB-behållaren?

Du kan konfigurera BLOB-kryptering antingen i Azure (föredra) eller på den Avera vFXT Core-filnivån.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan jag använda min egen krypterings nyckel för en BLOB core-filer?

Som standard krypteras data via Microsoft-hanterade nycklar för Azure Blob-, tabell-och Queue-lagring, plus Azure Files. Du kan ta med din egen nyckel för kryptering av Blob Storage och Azure Files. Om du väljer att använda AVERT vFXT-kryptering måste du använda den AVERT-genererade nyckeln och lagra den lokalt.

## <a name="purchasing"></a>Inköp

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hur gör jag för att skaffa ett AVERT vFXT för Azure-licensiering?

Det är enkelt att få ett AVERT vFXT för Azure-licens via Azure Marketplace. Registrera dig för ett Azure-konto och följ sedan anvisningarna i [distribuera AVERT vFXT-klustret](avere-vfxt-deploy.md) för att skapa ett AVERT vFXT-kluster.

### <a name="how-much-does-avere-vfxt-cost"></a>Hur mycket kostar det att vFXT?

I Azure finns det ingen ytterligare licens avgift för att använda ett AVERT vFXT-kluster. Kunderna ansvarar för lagring och andra avgifter för Azure-förbrukning.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kan du använda vFXT virtuella datorer med låg prioritet?

Nej, AVERT vFXT-kluster kräver tjänsten Always On. Kluster kan stängas av när de inte behövs.

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med AVERT vFXT för Azure läser du de här artiklarna och lär dig hur du planerar och distribuerar ditt eget system:

* [Planera för ditt Avere vFXT-system](avere-vfxt-deploy-plan.md)
* [Distributionsöversikt](avere-vfxt-deploy-overview.md)
* [Förbered för att skapa ett AVERT vFXT-kluster](avere-vfxt-prereqs.md)
* [Distribuera Avere vFXT-klustret](avere-vfxt-deploy.md)

Om du vill veta mer om funktioner och användnings fall för AVERT vFXT går du till [AVERT vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
