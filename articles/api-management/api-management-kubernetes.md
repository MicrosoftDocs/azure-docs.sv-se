---
title: Använda Azure API Management med mikrotjänster som distribueras i Azure Kubernetes-tjänsten | Microsoft Docs
description: I den här artikeln beskrivs alternativen för att distribuera API Management med AKS
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 36dfc8c906c52c6822e583db3a08c891306f7e78
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047940"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Använda Azure API Management med mikrotjänster som distribueras i Azure Kubernetes-tjänsten

Mikrotjänster är perfekta för att skapa API: er. Med [Azure Kubernetes service](https://azure.microsoft.com/services/kubernetes-service/) (AKS) kan du snabbt distribuera och använda en [mikrotjänster-baserad arkitektur](/azure/architecture/guide/architecture-styles/microservices) i molnet. Du kan sedan använda [Azure API Management](https://aka.ms/apimrocks) (API Management) för att publicera mikrotjänster som API: er för intern och extern konsumtion. I den här artikeln beskrivs alternativen för att distribuera API Management med AKS. Det förutsätter grundläggande kunskaper om Kubernetes, API Management och Azure-nätverk. 

## <a name="background"></a>Bakgrund

När du publicerar mikrotjänster som API: er för förbrukning kan det vara svårt att hantera kommunikationen mellan mikrotjänster och de klienter som använder dem. Det finns en mängd olika frågor om överstyckning, till exempel autentisering, auktorisering, begränsning, cachelagring, omvandling och övervakning. Dessa problem är giltiga oavsett om mikrotjänster exponeras för interna eller externa klienter. 

[API Gateway](/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) -mönstret behandlar dessa frågor. En API-gateway fungerar som en front dörr för mikrotjänster, frigör klienter från mikrotjänster, lägger till ett extra säkerhets lager och minskar komplexiteten hos mikrotjänster genom att ta bort belastningen på att hantera överskotts problem. 

[Azure API Management](https://aka.ms/apimrocks) är en nyckel färdig lösning för att lösa dina behov av API-Gateway. Du kan snabbt skapa en konsekvent och modern Gateway för dina mikrotjänster och publicera dem som API: er. Som en API Management-lösning med fullständig livs cykel ger den också ytterligare funktioner som till exempel en självbetjänings utvecklares Portal för API-identifiering, API-livscykel hantering och API-analys.

När de används tillsammans tillhandahåller AKS och API Management en plattform för att distribuera, publicera, skydda, övervaka och hantera dina mikrotjänster-baserade API: er. I den här artikeln går vi igenom några alternativ för att distribuera AKS tillsammans med API Management. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes-tjänster och API: er

I ett Kubernetes-kluster distribueras behållare i [poddar](https://kubernetes.io/docs/concepts/workloads/pods/pod/), som är tillfälliga och har en livs cykel. När en arbetsnodens nod bryts går poddar som körs på noden förlorade. Därför kan IP-adressen för en POD ändras när som helst. Det går inte att använda den för att kommunicera med pod. 

Kubernetes introducerade begreppet [tjänster](https://kubernetes.io/docs/concepts/services-networking/service/)för att lösa det här problemet. En Kubernetes-tjänst är ett abstraktions lager som definierar en logisk grupp poddar och möjliggör extern trafik exponering, belastnings utjämning och tjänst identifiering för dessa poddar. 

När vi är redo att publicera våra mikrotjänster som API: er via API Management måste vi tänka på hur man mappar våra tjänster i Kubernetes till API: er i API Management. Det finns inga uppsättnings regler. Det beror på hur du har utformat och partitionerat dina affärs funktioner eller domäner i mikrotjänster i början. Om till exempel poddar bakom en tjänst ansvarar för alla åtgärder på en specifik resurs (t. ex. kund), kan tjänsten mappas till ett API. Om åtgärder på en resurs är partitionerade i flera mikrotjänster (t. ex. GetOrder, PlaceOrder) kan flera tjänster aggregeras logiskt i ett enda API i API Management (se figur 1). 

Mappningarna kan också utvecklas. Eftersom API Management skapar en fasad framför mikrotjänster, tillåter vi att du återanvänder och rätt storlek på våra mikrotjänster med tiden. 

![Mappa tjänster till API: er](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>Distribuera API Management framför AKS

Det finns några alternativ för att distribuera API Management framför ett AKS-kluster. 

Även om ett AKS-kluster alltid distribueras i ett virtuellt nätverk (VNet) behöver inte en API Management-instans distribueras i ett VNet. När API Management inte finns i klustrets VNet, måste AKS-klustret publicera offentliga slut punkter för API Management för att ansluta till. I så fall måste du skydda anslutningen mellan API Management och AKS. Med andra ord måste vi se till att klustret bara kan nås exklusivt via API Management. Vi går igenom alternativen. 

### <a name="option-1-expose-services-publicly"></a>Alternativ 1: exponera tjänster offentligt

Tjänster i ett AKS-kluster kan exponeras offentligt med [tjänst typer](../aks/concepts-network.md) av NodePort, Loadbalancer eller ExternalName. I det här fallet är tjänsterna tillgängliga direkt från det offentliga Internet. När du har distribuerat API Management framför klustret måste du se till att all inkommande trafik går igenom API Management genom att använda autentisering i mikrotjänster. API Management kan till exempel inkludera en åtkomsttoken i varje begäran som görs till klustret. Varje mikrotjänst ansvarar för att verifiera token innan begäran bearbetas. 


Detta kan vara det enklaste alternativet att distribuera API Management framför AKS, särskilt om du redan har autentiserings logik som implementerats i mikrotjänsterna. 

![Publicera tjänster direkt](./media/api-management-aks/direct.png)

Proffs
* Enkel konfiguration på API Managements sidan eftersom den inte behöver matas in i klustrets VNet
* Ingen ändring på AKS-sidan om tjänsterna redan är allmänt tillgängliga och autentiserings logik redan finns i mikrotjänster

Nack delar
* Potentiell säkerhets risk på grund av den offentliga visningen av tjänst slut punkter
* Ingen start punkt för inkommande kluster trafik
* Komplicerar mikrotjänster med duplicerad autentiserings logik

### <a name="option-2-install-an-ingress-controller"></a>Alternativ 2: installera en ingångs kontroll enhet

Även om alternativ 1 kan vara enklare, har det blivit mycket bättre nack delar som nämnts ovan. Om en API Management instans inte finns i klustrets VNet är ömsesidig TLS-autentisering (mTLS) ett robust sätt att säkerställa att trafiken är säker och betrodd i båda riktningarna mellan en API Management instans och ett AKS-kluster. 

Ömsesidig TLS-autentisering [stöds internt](./api-management-howto-mutual-certificates.md) av API Management och kan aktive ras i Kubernetes genom att en ingångs [kontroll installeras](../aks/ingress-own-tls.md) (figur 3). Det innebär att autentiseringen utförs i ingångs styrenheten, vilket fören klar mikrotjänsterna. Dessutom kan du lägga till IP-adresserna för API Management i listan över tillåtna av ingångs adresser för att se till att endast API Management har åtkomst till klustret.  

 
![Publicera via en ingångs kontroll](./media/api-management-aks/ingress-controller.png)


Proffs
* Enkel konfiguration på API Managements sidan eftersom den inte behöver matas in i klustrets VNet-och mTLS stöds internt
* Centraliserar skyddet för inkommande kluster trafik i ingångs kontroll lagret
* Minskar säkerhets risken genom att minimera offentligt synliga kluster slut punkter

Nack delar
* Ökar komplexiteten i kluster konfigurationen på grund av extra arbete att installera, konfigurera och underhålla ingångs styrenheten och hantera certifikat som används för mTLS
* Säkerhets risk på grund av offentlig synlighet för ingångs styrenhetens slut punkt (er)


När du publicerar API: er via API Management är det enkelt och vanligt att skydda åtkomsten till dessa API: er med hjälp av prenumerations nycklar. Utvecklare som behöver använda de publicerade API: erna måste innehålla en giltig prenumerations nyckel i HTTP-begäranden när de ringer till dessa API: er. Annars avvisas anropen direkt av API Management Gateway. De vidarebefordras inte till backend-tjänsterna.

En prenumeration krävs för att få en prenumerations nyckel för åtkomst till API: er. En prenumeration är i stort sett en namngiven behållare för ett par prenumerations nycklar. Utvecklare som behöver använda de publicerade API: erna kan få prenumerationer. Och de behöver inte godkännas av API-utgivare. API-utgivare kan också skapa prenumerationer direkt för API-konsumenter.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>Alternativ 3: Distribuera APIM i klustrets VNet

I vissa fall kan kunder med reglerande villkor eller strikta säkerhets krav hitta alternativ 1 och 2 icke-lönsamma lösningar på grund av allmänt utsatta slut punkter. I andra kan AKS-klustret och de program som förbrukar mikrotjänster finnas i samma VNet, och därför finns det ingen anledning att exponera klustret som all API-trafik kommer att finnas kvar i det virtuella nätverket. I dessa scenarier kan du distribuera API Management till klustrets VNet. [API Management Developer-och Premium-nivåerna](https://aka.ms/apimpricing) stöder VNet-distribution. 

Det finns två lägen för att [distribuera API Management till ett VNet](./api-management-using-with-vnet.md) – externt och internt. 

Om API-konsumenter inte finns i klustrets VNet, ska det externa läget (figur 4) användas. I det här läget injiceras API Management Gateway i klustrets VNet, men kan nås från offentliga Internet via en extern belastningsutjämnare. Det hjälper dig att dölja klustret helt samtidigt som du fortfarande tillåter att externa klienter använder mikrotjänsterna. Dessutom kan du använda funktioner i Azure-nätverk som nätverks säkerhets grupper (NSG) för att begränsa nätverks trafiken.

![Externt VNet-läge](./media/api-management-aks/vnet-external.png)

Om alla API-konsumenter finns i klustrets VNet, kan det interna läget (figur 5) användas. I det här läget injiceras API Management Gateway i klustrets VNET och kan bara nås från det här virtuella nätverket via en intern belastningsutjämnare. Det går inte att komma åt API Management Gateway eller AKS-klustret från det offentliga Internet. 

![Internt VNet-läge](./media/api-management-aks/vnet-internal.png)

 I båda fallen är AKS-klustret inte offentligt synligt. Jämfört med alternativ 2 kanske inte ingångs kontrollen är nödvändig. Beroende på ditt scenario och din konfiguration kan autentisering fortfarande krävas mellan API Management och dina mikrotjänster. Om ett tjänst nät exempelvis antas kräver det alltid ömsesidig TLS-autentisering. 

Proffs
* Det säkraste alternativet eftersom AKS-klustret inte har någon offentlig slut punkt
* Fören klar kluster konfigurationen eftersom den inte har någon offentlig slut punkt
* Möjlighet att dölja både API Management-och AKS i VNet med det interna läget
* Möjlighet att kontrol lera nätverks trafik med hjälp av funktioner i Azure-nätverk, till exempel nätverks säkerhets grupper (NSG)

Nack delar
* Ökar komplexiteten vid distribution och konfiguration av API Management för att fungera i VNet

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [nätverks koncept för program i AKS](../aks/concepts-network.md)
* Lär dig mer om [hur du använder API Management med virtuella nätverk](./api-management-using-with-vnet.md)
