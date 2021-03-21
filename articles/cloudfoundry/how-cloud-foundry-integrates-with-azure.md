---
title: Hur Cloud Foundry integreras med Azure | Microsoft Docs
description: Beskriver hur Cloud Foundry kan använda Azure-tjänster för att förbättra företags upplevelsen
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563327"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrera Cloud Foundry med Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) är en PaaS-plattform som körs ovanpå Cloud providers IaaS-plattform. Den erbjuder konsekvent program distributions upplevelse i moln leverantörer. Den kan också integreras med olika Azure-tjänster med företags klass HA, skalbarhet och kostnads besparingar.
Det finns [6 del system av Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), som kan skalas flexibelt online, inklusive: routning, autentisering, program livs cykel hantering, Service Management, meddelanden och övervakning. För varje under system kan du konfigurera Cloud Foundry att använda kontakt med Azure-tjänsten. 

![Cloud Foundry på Azure integration Architecture](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. hög tillgänglighet och skalbarhet
### <a name="managed-disk"></a>Hanterad disk
Bosh använder Azure CPI (Cloud Provider Interface) för disk skapande och borttagning av rutiner. Som standard används ohanterade diskar. Kunden måste manuellt skapa lagrings konton och sedan konfigurera kontona i CF MANIFEST-filer. Detta beror på begränsningen av antalet diskar per lagrings konto.
Nu är [hanterad disk](https://azure.microsoft.com/services/managed-disks/) tillgänglig, och erbjuder hanterad säker och tillförlitlig disk lagring för virtuella datorer. Kunden behöver inte längre hantera lagrings kontot för skalning och HA. Azure ordnar diskarna automatiskt. Oavsett om det är en ny eller en befintlig distribution, kommer Azure CPI att hantera skapandet eller migreringen av den hanterade disken under en CF-distribution. Den stöds med PCF 1,11. Du kan också utforska [vägledningen Cloud Foundry Managed disk](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) med öppen källkod för referens. 
### <a name="availability-zone-"></a>Tillgänglighets zon *
Som en molnbaserad program plattform är Cloud Foundry utformad med [fyra nivåer av hög tillgänglighet](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Medan de första tre nivåerna av program varu fel kan hanteras av själva CF-systemet, tillhandahålls plattforms fel tolerans av moln leverantörer. Nyckeln CF-komponenter bör skyddas med en moln leverantörs plattform HA-lösning. Detta inkluderar GoRouters, Diego hjärna, CF-databaser och service paneler. Som standard används [Azures tillgänglighets uppsättning](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) för fel tolerans mellan kluster i ett Data Center.
Den nya [Azure Availability-zonen](../availability-zones/az-overview.md) är nu fri att lanseras nu, vilket ger fel tolerans till nästa nivå, med redundans med låg latens mellan data Center.
Tillgänglighets zonen i Azure uppnår HA en uppsättning virtuella datorer i 2 + data Center, och varje uppsättning virtuella datorer är redundanta till andra mängder. Om en zon inte är igång är de andra uppsättningarna fortfarande aktiva, isolerade från haveriet.
> [!NOTE] 
> Tillgänglighets zonen i Azure erbjuds inte för alla regioner ännu, kontrol lera det senaste [meddelandet för listan över regioner som stöds](../availability-zones/az-overview.md). För Cloud Foundry med öppen källkod kontrollerar du [Azures tillgänglighets zon efter vägledning för öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. nätverks dirigering
Som standard används Azure Basic Load Balancer för inkommande CF API/Apps-begär Anden, som vidarebefordrar dem till Gorouters. CF-komponenter som Diego hjärna, MySQL, ERT kan också använda belastningsutjämnaren för att balansera trafiken för HA. Azure tillhandahåller också en uppsättning helt hanterade lösningar för belastnings utjämning. Om du letar efter TLS/SSL-avslutning ("SSL-avlastning") eller per HTTP/HTTPS-begäran, bör du överväga Application Gateway. För belastnings utjämning med hög tillgänglighet och skalbarhet på nivå 4, Överväg standard Load Balancer.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](../application-gateway/overview.md) erbjuder olika funktioner för belastnings utjämning i Layer 7, inklusive SSL-avlastning, slut punkt till slut punkt för TLS, brand vägg för webb program, cookie-baserad session tillhörighet och mer. Du kan [konfigurera Application Gateway i Cloud Foundry med öppen källkod](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). För PCF kontrollerar du viktig information för POC-test i  [PCF 2,1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) .

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer är en Layer 4-belastningsutjämnare. Den används för att distribuera trafiken mellan instanser av tjänster i en belastningsutjämnad uppsättning. Standard versionen innehåller [avancerade funktioner](../load-balancer/load-balancer-overview.md) ovanpå Basic-versionen. Till exempel 1. Max gränsen för backend-poolen höjs från 100 till 1000 virtuella datorer.  2. Slut punkterna stöder nu flera tillgänglighets uppsättningar i stället för en enda tillgänglighets uppsättning.  3. Ytterligare funktioner som t. ex. HA-portar, bättre övervaknings data och så vidare. Om du flyttar till tillgänglighets zonen i Azure måste du använda standard belastnings utjämning. För en ny distribution rekommenderar vi att du börjar med Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. autentisering 
[Cloud Foundry användar konto och autentisering](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) är den centrala identitets hanterings tjänsten för CF och dess olika komponenter. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Som standard används UAA för Cloud Foundry autentisering. Som ett avancerat alternativ stöder UAA också Azure AD som ett externt användar arkiv. Azure AD-användare kan komma åt Cloud Foundry med sin LDAP-identitet, utan ett Cloud Foundry konto. Följ de här stegen för att [Konfigurera Azure AD för UAA i PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. data lagring för Cloud Foundry runtime system
Cloud Foundry erbjuder bra utöknings barhet för att använda Azure blobstore eller Azure MySQL/PostgreSQL-tjänster för program körning av system lagring.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure-Blobstore för Cloud Foundry Cloud Controller Blobstore
Blobstore för moln styrenhet är ett viktigt data lager för buildpacks, droplet-paket och resurspooler. Som standard används NFS-server för Cloud Controller-blobstore. Använd Azure Blob Storage som extern lagring för att undvika en enskild felpunkt. Titta närmare på [Cloud Foundry dokumentation](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) för bakgrunden och [alternativ i pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL som Cloud Foundry elastisk kör tids databas *
CF elastisk körning kräver två huvud system databaser:
#### <a name="ccdb"></a>CCDB 
Moln styrenhets databasen.  Cloud Controller tillhandahåller REST API-slutpunkter som klienter kan använda för att komma åt systemet. CCDB lagrar tabeller för organisationer, utrymmen, tjänster, användar roller och mer för moln styrenhet.
#### <a name="uaadb"></a>UAADB 
Databasen för användar konto och autentisering. Den lagrar relaterade data för användarautentisering, till exempel krypterade användar namn och lösen ord.

Som standard kan en lokal system databas (MySQL) användas. Använd Azure Managed MySQL eller PostgreSQL Services för HA och för att skala. Här är en instruktion om att [Aktivera Azure MySQL/postgresql för CCDB, UAADB och andra system databaser med öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. öppna Service Broker
Azure Service Broker erbjuder konsekvent gränssnitt för att hantera programmets åtkomst till Azure-tjänster. Med den nya [öppna Service Broker för Azure Project](https://github.com/Azure/open-service-broker-azure) får du ett enkelt och enkelt sätt att leverera tjänster till program över Cloud Foundry, OpenShift och Kubernetes. Mer information om distributions anvisningar för PCF finns i [Azure Open Service Broker-panelen för PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) .

## <a name="6-metrics-and-logging"></a>6. mått och loggning
Azure Log Analytics munstycke är en Cloud Foundry komponent som vidarebefordrar mått från [Cloud Foundry loggregator-Firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) till [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/). Med munstycket kan du samla in, Visa och analysera systemets hälso tillstånds-och prestanda mått i flera distributioner.
Klicka [här](./cloudfoundry-oms-nozzle.md) om du vill lära dig hur du distribuerar Azure Log Analytics munstycke till både öppen källkod och pivot-Cloud Foundry miljö och sedan åtkomst till data från Azure Monitor loggar konsolen. 
> [!NOTE]
> Från PCF 2,0 vidarebefordras BOSH hälso mått för virtuella datorer till Loggregator-Firehose som standard och integreras i Azure Monitor loggar konsol.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. kostnads besparing
### <a name="cost-saving-for-devtest-environments"></a>Kostnads besparingar för utvecklings-och test miljöer
#### <a name="b-series-"></a>B-serien: *
Även om F och D VM-serien rekommenderades ofta för pivotal Cloud Foundry produktions miljö, ger den nya "Burstable" [B-serien](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) nya alternativ. De virtuella datorerna i B-serien är idealiska för arbets belastningar som inte behöver PROCESSORns fulla prestanda kontinuerligt, t. ex. webb servrar, små databaser och utvecklings-och test miljöer. Dessa arbets belastningar har vanligt vis höga prestanda krav. Det är $0.012/timme (B1) jämfört med $0,05/timme (F1). mer information finns i den fullständiga listan över [VM-storlekar](../virtual-machines/sizes-general.md) och [priser](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) . 
#### <a name="managed-standard-disk"></a>Hanterad standard disk: 
Premium diskar rekommenderades för tillförlitliga prestanda i produktionen.  Med [hanterad disk](https://azure.microsoft.com/services/managed-disks/)kan standard lagring också leverera liknande tillförlitlighet, med olika prestanda. För arbets belastningar som inte är prestanda känsliga, som utveckling/testning eller icke-kritisk miljö, erbjuder hanterade standard diskar ett alternativt alternativ med lägre kostnad.  
### <a name="cost-saving-in-general"></a>Kostnads besparingar i allmänhet 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Betydande kostnader för VM-besparingar med Azure-reservationer: 
Idag debiteras alla virtuella datorer med "på begäran"-prissättning, även om miljöerna vanligt vis hålls på obestämd tid. Nu kan du reservera VM-kapaciteten på 1-eller tre års period och få rabatt på 45-65%. Rabatter tillämpas i fakturerings systemet utan ändringar i din miljö. Mer information finns i [så här fungerar Azure-reservationer](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Hanterad Premium-disk med mindre storlekar: 
Managed disks stöder mindre disk storlekar, till exempel P4 (32 GB) och P6 (64 GB) för både Premium-och standard diskar. Om du har små arbets belastningar kan du spara kostnad när du migrerar från standard Premium diskar till hanterade Premium-diskar.
#### <a name="use-azure-first-party-services"></a>Använd Azure First parts-tjänster: 
Genom att dra nytta av Azures första parts tjänst sänker du den långsiktiga administrations kostnaden, förutom de HA och pålitlighet som anges ovan. 

Pivotal har lanserat en [liten ert](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) för PCF-kunder, komponenterna samplacerade i de fyra virtuella datorerna, med upp till 2500 program instanser. Utvärderings versionen är nu tillgänglig via [Azures marknads plats](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Nästa steg
Azures integrerings funktioner är först tillgängliga med [öppen källkod Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), innan de är tillgängliga i pivot-Cloud Foundry. Funktioner som marker ATS med * är fortfarande inte tillgängliga via PCF. Cloud Foundry integration med Azure Stack omfattas inte av det här dokumentet.
Om du har PCF stöd för de funktioner som har marker ATS med * eller Cloud Foundry integration med Azure Stack kan du kontakta din pivot-och Microsoft-konto ansvarig för senaste status.