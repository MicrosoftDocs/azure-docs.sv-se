---
title: Vanliga frågor och svar om Azure Cloud Services (utökad support)
description: Vanliga frågor och svar om Azure Cloud Services (utökad support)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 33bafac9247f007978fef568469d643f1a1098df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383594"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Vanliga frågor och svar om Azure Cloud Services (utökad support)
Den här artikeln beskriver vanliga frågor som rör Azure Cloud Services (utökad support).

## <a name="general"></a>Allmänt

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Vad är resurs namnet för Cloud Services (klassisk) & Cloud Services (utökad support)?
- Cloud Services (klassisk): `microsoft.classiccompute/domainnames`
- Cloud Services (utökad support): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Vilka platser är tillgängliga för att distribuera Cloud Services (utökad support)?
Cloud Services (utökad support) är tillgängligt i alla offentliga moln regioner.

### <a name="how-does-my-quota-change"></a>Hur ändras min kvot? 
Kunderna måste begära en kvot med samma processer som andra Azure Resource Manager produkter. Kvoten i Azure Resource Manager är regional och en separat kvot förfrågan krävs för varje region.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Varför visas inte någon produktions & mellanlagrings plats längre?
Cloud Services (utökad support) stöder inte det logiska konceptet för en värdbaserad tjänst, som inkluderar två platser (produktion & mellanlagring). Varje distribution är en oberoende distribution av moln tjänster (utökad support). Testa och mellanlagra en ny version av en moln tjänst, distribuera en moln tjänst (utökad support) och tagga den som VIP-utbytbar med en annan moln tjänst (utökad support)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Varför kan jag inte skapa en tom moln tjänst längre?
Begreppet värd tjänst namn finns inte längre, du kan inte skapa en tom moln tjänst (utökad support).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Stöder Cloud Services (utökad support) Resource Health kontroll (RHC)?
Nej, Cloud Services (utökad support) stöder inte Resource Health kontroll (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>Hur förändras roll instans måtten?
Det finns inga ändringar i roll instans måtten. 

### <a name="how-are-web--worker-roles-changing"></a>Hur ändras Web & Worker-roller?
Det finns inga ändringar i design, arkitektur eller komponenter för webb-och arbets roller. 

### <a name="how-are-role-instances-changing"></a>Hur ändras roll instanser?
Det finns inga ändringar i design, arkitektur eller komponenter för roll instanserna. 

### <a name="how-will-guest-os-updates-change"></a>Hur ändras ändringar i gäst operativ system?
 Det finns inga ändringar i utgivnings metoden. Cloud Services (klassisk) och Cloud Services (utökad support) får samma uppdateringar.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Stöder Cloud Services (utökad support) stopp-allokerade och stoppade tillstånd?

Cloud Services (utökad support) distribution har endast stöd för läget stoppat-allokerat som visas som "stoppad" i Azure Portal. Stoppat-avallokerat tillstånd stöds inte. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Stöder Cloud Services (utökad support) distributioner stöd för skalning i kluster, tillgänglighets zoner och regioner?
Cloud Services-distributioner (Extended support) kan inte skalas över flera kluster, tillgänglighets zoner och regioner. 

### <a name="how-can-i-get-the-deployment-id-for-my-cloud-service-extended-support"></a>Hur kan jag hämta distributions-ID: t för min moln tjänst (utökad support)
Distributions-ID aka privata ID kan nås med hjälp av [CloudServiceInstanceView](https://docs.microsoft.com/rest/api/compute/cloudservices/getinstanceview#cloudserviceinstanceview) -API: et. Den är också tillgänglig på Azure Portal på bladet roll och instanser i moln tjänsten (utökad support)

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Finns det några pris skillnader mellan Cloud Services (klassiska) och Cloud Services (utökad support)?
Cloud Services (utökad support) använder Azure Key Vault-och Basic (ARM) offentliga IP-adresser.Kunder som behöver certifikat måste använda Azure Key Vault för certifikat hantering ([Läs mer](https://azure.microsoft.com/pricing/details/key-vault/) om Azure Key Vault prissättning.)   Varje offentlig IP-adress för Cloud Services (utökad support) debiteras separat ([Läs mer](https://azure.microsoft.com/pricing/details/ip-addresses/) om priser för offentliga IP-adresser) 
## <a name="resources"></a>Resurser 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Vilka resurser som är länkade till en Cloud Services-distribution (utökad support) måste finnas i samma resurs grupp?
Belastnings utjämning, nätverks säkerhets grupper och routningstabeller måste vara aktiva i samma region och resurs grupp. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Vilka resurser som är länkade till en Cloud Services (utökad support) distribution måste finnas i samma region?
Key Vault, virtuella nätverk, offentliga IP-adresser, nätverks säkerhets grupper och väg tabeller måste vara aktiva i samma region.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Vilka resurser som är länkade till en Cloud Services (utökad support) distribution måste finnas i samma virtuella nätverk?
Offentliga IP-adresser, belastningsutjämnare, nätverks säkerhets grupper och väg tabeller måste vara aktiva i samma virtuella nätverk. 

## <a name="deployment-files"></a>Distributions filer 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Hur kan jag använda en mall för att distribuera eller hantera min distribution?
Mall-och parameter-filer kan skickas som en parameter med hjälp av REST, PowerShell och CLI. De kan också överföras med hjälp av Azure Portal.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Måste jag ha fyra filer nu? (mall, parameter, csdef, cscfg)
Mall-och parameter-filer används bara för distributions automatisering. Precis som Cloud Services (klassisk) kan du manuellt skapa beroende resurser först och sedan en Cloud Services (utökad stöd) distribution med PowerShell-, CLI-kommandon eller via portalen med befintliga csdef, cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Hur ändras min program kod på Cloud Services (utökad support)
Det krävs inga ändringar för program koden som paketerats i cspkg. Dina befintliga program kommer att fortsätta fungera som tidigare. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Är Cloud Services (utökad support) Tillåt CTP-paket format?
CTP-paketets format stöds inte i Cloud Services (utökad support). Det ger dock en förbättrad paket storleks gräns på 800 MB

## <a name="migration"></a>Migrering

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Kommer Cloud Services (utökad support) att minska felen på grund av allokeringsfel?
Nej, distributioner av moln tjänster (utökad support) är knutna till ett kluster som Cloud Services (klassisk). Det innebär att allokeringsfel fortsätter att finnas om klustret är fullt. 

### <a name="when-do-i-need-to-migrate"></a>När behöver jag migrera? 
Hur lång tid det tar och hur komplext migreringen är beror på en mängd variabler. Planering är det mest effektiva steget för att förstå omfattningen av arbete, Blocker och komplexitet vid migrering.

## <a name="networking"></a>Nätverk 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Varför kan jag inte skapa en distribution utan ett virtuellt nätverk?
Virtuella nätverk är en resurs som krävs för distribution på Azure Resource Manager. Cloud Services (utökad support) distribution måste finnas i ett virtuellt nätverk. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Varför ser jag nu så många nätverks resurser? 
I Azure Resource Manager visas komponenter i din Cloud Services-distribution (utökad support) som en resurs för bättre synlighet och bättre kontroll. Samma typ av resurser användes i Cloud Services (klassisk), men de var bara dolda. Ett exempel på en sådan resurs är den offentliga Load Balancer, som nu är en explicit "skrivskyddad" resurs som skapas automatiskt av plattformen

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Vilka begränsningar gäller för ett undernät med respektive för att Cloud Services (utökad support)?
Ett undernät som innehåller Cloud Services (utökad support) distributioner kan inte delas med distributioner från andra beräknings produkter som Virtual Machines, Virtual Machines skalnings uppsättningar, Service Fabric, osv.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Vilka metoder för IP-allokering stöds i Cloud Services (utökad support)?
Cloud Services (utökad support) har stöd för dynamiska & metoder för statisk IP-allokering. Statiska IP-adresser refereras till som reserverade IP-adresser i cscfg-filen.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Varför debiteras jag för IP-adresser?
Kunder faktureras för användning av IP-adresser på Cloud Services (utökad support) precis som användare faktureras för IP-adresser som är kopplade till virtuella datorer. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Kan jag använda ett DNS-namn med Cloud Services (utökad support)? 
Ja. Cloud Services (utökad support) kan också tilldelas ett DNS-namn. Med Azure Resource Manager är DNS-etiketten en valfri egenskap för den offentliga IP-adress som är tilldelad till moln tjänsten. Formatet på DNS-namnet för Azure Resource Manager baserade distributioner är `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>Kan jag uppdatera eller ändra den virtuella nätverks referensen för en befintlig moln tjänst (utökad support)? 
Nej. En virtuell nätverks referens är obligatorisk när en moln tjänst skapas. Det går inte att ändra den virtuella nätverks referensen för en befintlig moln tjänst. Det virtuella nätverkets adress utrymme kan ändras med VNet-API: er. 

## <a name="certificates--key-vault"></a>Certifikat & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Varför måste jag hantera mina certifikat på Cloud Services (utökad support)?
Cloud Services (utökad support) har antagit samma process som andra beräknings erbjudanden där certifikat finns i kundens hanterade nyckel valv. Detta gör det möjligt för kunderna att få fullständig kontroll över sina hemligheter & certifikat. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Kan jag använda ett Key Vault för alla mina distributioner i alla regioner?
Nej. Key Vault är en regional resurs och kunder behöver en Key Vault i varje region. En Key Vault kan dock användas för alla distributioner inom en specifik region.

## <a name="next-steps"></a>Nästa steg
Om du vill börja använda Cloud Services (utökad support), se [distribuera en moln tjänst (utökad support) med PowerShell](deploy-powershell.md)
