---
title: Anslutningslägen och krav
description: Förklarar anslutnings alternativ för Azure Arc-aktiverade data tjänster för från din miljö till Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d148509af45b93dce8dbd99b9afc674276b149b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493980"
---
# <a name="connectivity-modes-and-requirements"></a>Anslutningslägen och krav

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Anslutningslägen

Det finns flera alternativ för anslutnings graden från din Azure Arc-aktiverad data tjänst miljö till Azure. När dina krav varierar beroende på affärs policy, myndighets förordning eller om nätverks anslutningen till Azure är tillgänglig kan du välja mellan följande anslutnings lägen.

Azure Arc-aktiverade data tjänster ger dig möjlighet att ansluta till Azure i två olika *anslutnings lägen*: 

- Direkt ansluten 
- Indirekt ansluten

Anslutnings läget ger dig flexibiliteten att välja hur mycket data som skickas till Azure och hur användare interagerar med data styrenheten för bågen. Beroende på vilket anslutnings läge som valts kan vissa funktioner i Azure Arc-aktiverade data tjänster eventuellt vara otillgängliga.

Om Azure Arc-aktiverade data tjänster är direkt anslutna till Azure kan användarna använda [Azure Resource Manager API: er](/rest/api/resources/), Azure CLI och Azure Portal för att använda Azures Arc-datatjänster. Upplevelsen i direkt ansluten läge är ungefär som hur du använder andra Azure-tjänster med etablering/avetablering, skalning, konfigurering och så vidare i Azure Portal.  Om Azure Arc-aktiverade data tjänster är indirekt anslutna till Azure är Azure Portal en skrivskyddad vy. Du kan se inventeringen av SQL-hanterade instanser och postgres för storskaliga instanser som du har distribuerat och information om dem, men du kan inte vidta några åtgärder på dem i Azure Portal.  I läget för indirekt anslutning måste alla åtgärder vidtas lokalt med hjälp av Azure Data Studio, de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] inbyggda Kubernetes-verktygen, t. ex. kubectl.

Dessutom kan Azure Active Directory och Azure Role-Based Access Control bara användas i det direkt anslutna läget eftersom det finns ett beroende på en kontinuerlig och direkt anslutning till Azure för att tillhandahålla den här funktionen.

Vissa Azure-anslutna tjänster är bara tillgängliga när de kan nås direkt, till exempel Azure Defender Security Services, container Insights och Azure Backup till Blob Storage.

||**Indirekt ansluten**|**Direkt ansluten**|**Aldrig ansluten**|
|---|---|---|---|
|**Beskrivning**|I läget för indirekt anslutning finns de flesta hanterings tjänster lokalt i din miljö utan direkt anslutning till Azure.  En minimal mängd data måste skickas till Azure för inventerings-och fakturerings syfte _._ Den exporteras till en fil och överförs till Azure minst en gång per månad.  Ingen direkt eller kontinuerlig anslutning till Azure krävs.  Vissa funktioner och tjänster som kräver en anslutning till Azure är inte tillgängliga.|Direkt ansluten läge erbjuder alla tillgängliga tjänster när en direkt anslutning kan upprättas med Azure. Anslutningar initieras alltid _från_ din miljö till Azure och använder standard portar och protokoll som https/443.|Inga data kan skickas till eller från Azure på något sätt.|
|**Aktuell tillgänglighet**| Tillgängligt i förhandsversion.|Tillgängligt i förhandsversion.|Stöds inte för närvarande.|
|**Typiska användnings fall**|Lokala data Center som inte tillåter anslutning till eller från data området i data centret på grund av affärs-eller reglerings policyer eller av frågor om externa attacker eller data exfiltrering.  Typiska exempel: finansiella institutioner, sjukvårds vård, myndigheter. <br/><br/>Kant webbplats platser där Edge-webbplatsen vanligt vis inte har någon anslutning till Internet.  Typiska exempel: olje-/gas-eller militära fält program.  <br/><br/>Platser med Edge-webbplatser som tillfälligt har anslutning till långa perioder av avbrott.  Typiska exempel: Stadiums, kryssnings-fartyg. | Organisationer som använder offentliga moln.  Typiska exempel: Azure, AWS eller Google Cloud.<br/><br/>Edge-webbplats platser där Internet anslutning vanligt vis finns och tillåts.  Typiska exempel: butiker, tillverkning.<br/><br/>Företags data Center med fler tillåtande principer för anslutning till/från data området i data centret till Internet.  Typiska exempel: icke-reglerade företag, små/medel stora företag|Verkligt "Air-gapped"-miljöer där inga data under några omständigheter kan komma eller gå från data miljön. Typiska exempel: Top Secrets myndigheters anläggningar.|
|**Hur data skickas till Azure**|Det finns tre alternativ för hur fakturerings-och inventerings data kan skickas till Azure:<br><br> 1) data exporteras från data området av en automatiserad process som har anslutning till både det skyddade data området och Azure.<br><br>2) data exporteras från data området av en automatiserad process inom data området, kopieras automatiskt till en mindre säker region och en automatiserad process i den mindre säkra regionen överför data till Azure.<br><br>3) data exporteras manuellt av en användare inom den säkra regionen, manuellt från den säkra regionen och laddas manuellt till Azure. <br><br>De två första alternativen är en automatiserad kontinuerlig process som kan schemaläggas att köras ofta, så det finns en minimal fördröjning i överföringen av data till Azure som endast omfattas av den tillgängliga anslutningen till Azure.|Data skickas automatiskt och skickas kontinuerligt till Azure.|Data skickas aldrig till Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Funktions tillgänglighet efter anslutnings läge

|**Funktion**|**Indirekt ansluten**|**Direkt ansluten**|
|---|---|---|
|**Automatisk hög tillgänglighet**|Stöds|Stöds|
|**Själv tjänst etablering**|Stöds<br/>Skapandet kan göras via Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , eller Kubernetes inbyggda verktyg (Helm, kubectl, oc osv.) eller med Azure Arc Enabled Kubernetes GitOps-etablering.|Stöds<br/>Förutom alternativen för att skapa läge med indirekt anslutning kan du också skapa via Azure Portal, Azure Resource Manager API: er, Azure CLI-eller ARM-mallarna. **Väntande tillgänglighet för direkt anslutet läge**
|**Elastisk skalbarhet**|Stöds|Stöds<br/>**Väntande tillgänglighet för direkt anslutet läge**|
|**Billing**|Stöds<br/>Fakturerings data exporteras regelbundet och skickas till Azure.|Stöds<br/>Fakturerings data skickas automatiskt och skickas kontinuerligt till Azure och avspeglas i nära real tid. **Väntande tillgänglighet för direkt anslutet läge**|
|**Lagerhantering**|Stöds<br/>Inventerings data exporteras regelbundet och skickas till Azure.<br/><br/>Använd klient verktyg som Azure Data Studio, Azure Data CLI eller `kubectl` för att visa och hantera lagret lokalt.|Stöds<br/>Inventerings data skickas automatiskt och skickas kontinuerligt till Azure och avspeglas i nära real tid. På så sätt kan du hantera lagret direkt från Azure Portal. **Väntande tillgänglighet för direkt anslutet läge**|
|**Automatiska uppgraderingar och korrigeringar**|Stöds<br/>Datakontrollanten måste antingen ha direkt åtkomst till Microsoft Container Registry (MCR) eller att behållar avbildningarna måste hämtas från MCR och skickas till ett lokalt, privat behållar register som data styrenheten har åtkomst till.|Stöds<br/>**Väntande tillgänglighet för direkt anslutet läge**|
|**Automatisk säkerhets kopiering och återställning**|Stöds<br/>Automatisk lokal säkerhets kopiering och återställning.|Stöds<br/>Förutom automatisk säkerhets kopiering och återställning av lokala platser kan du _också skicka säkerhets_ kopior till Azure Backup för långsiktig kvarhållning. **Väntande tillgänglighet för direkt anslutet läge**|
|**Övervakning**|Stöds<br/>Lokal övervakning med Grafana-och Kibana-instrumentpaneler.|Stöds<br/>Förutom lokala övervaknings instrument paneler kan du _välja_ att skicka övervaknings data och loggar till Azure Monitor för vid storskalig övervakning av flera platser på ett och samma ställe. **Väntande tillgänglighet för direkt anslutet läge**|
|**Autentisering**|Använd lokalt användar namn/lösen ord för datakontrollant och instrument panels autentisering. Använd SQL-och postgres-inloggningar eller Active Directory för anslutning till databas instanser.  Använd K8s-autentiseringsproviders för autentisering till Kubernetes-API: et.|Förutom eller i stället för autentiseringsmetoderna för läget för indirekt anslutning kan du _välja_ att använda Azure Active Directory. **Väntande tillgänglighet för direkt anslutet läge**|
|**Rollbaserad åtkomstkontroll (RBAC)**|Använd Kubernetes RBAC på Kubernetes API. Använd SQL och postgres RBAC för databas instanser.|Du kan välja att integrera med Azure Active Directory och Azure RBAC. **Väntande tillgänglighet för direkt anslutet läge**|
|**Azure Defender**|Stöds inte|Planerat för framtida|

## <a name="connectivity-requirements"></a>Anslutnings krav

**För vissa funktioner krävs en anslutning till Azure.**

**All kommunikation med Azure initieras alltid från din miljö.** Detta gäller även för åtgärder som initieras av en användare i Azure Portal.  I så fall är det en uppgift som är i kö i Azure.  En agent i din miljö initierar kommunikationen med Azure för att se vilka uppgifter som finns i kön, kör aktiviteterna och rapporterar tillbaka status/slut för ande till Azure.

|**Typ av data**|**Riktning**|**Obligatorisk/valfri**|**Ytterligare kostnader**|**Läge krävs**|**Kommentarer**|
|---|---|---|---|---|---|
|**Containeravbildningar**|Microsoft Container Registry – >-kund|Obligatorisk|Inga|Indirekt eller direkt|Behållar avbildningar är metoden för att distribuera program varan.  I en miljö som kan ansluta till Microsoft Container Registry (MCR) via Internet kan behållar avbildningarna hämtas direkt från MCR.  Om distributions miljön inte har direkt anslutning kan du hämta avbildningarna från MCR och skicka dem till ett privat behållar register i distributions miljön.  När du skapar kan du konfigurera skapande processen att hämta från den privata behållar registret i stället för MCR. Detta gäller även för automatiserade uppdateringar.|
|**Resurs inventering**|Kund miljö – > Azure|Obligatorisk|Inga|Indirekt eller direkt|En inventering av data kontroller, databas instanser (PostgreSQL och SQL) lagras i Azure för fakturerings syfte och används också för att skapa en inventering av alla datakontrollanter och databas instanser på ett ställe som är särskilt användbart om du har mer än en miljö med Azure Arc-datatjänster.  När instanser har etablerats, avetablerats, skalas ut/in, skalas upp/ned, så uppdateras inventeringen i Azure.|
|**Information om fakturering av telemetri**|Kund miljö – > Azure|Obligatorisk|Inga|Indirekt eller direkt|Användningen av databas instanser måste skickas till Azure i fakturerings syfte.  Det kostar ingen kostnad för Azure Arc-aktiverade data tjänster under för hands versions perioden.|
|**Övervaka data och loggar**|Kund miljö – > Azure|Valfritt|Kanske beroende på data volym (se [Azure Monitor priser](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Indirekt eller direkt|Du kanske vill skicka det lokalt insamlade övervaknings data och loggar till Azure Monitor för att samla in data i flera miljöer på en och samma plats och även använda Azure Monitor tjänster som aviseringar med hjälp av data i Azure Machine Learning osv.|
|**Azure Role-baserade Access Control (Azure RBAC)**|Kund miljö – > Azure-> kund miljö|Valfritt|Nej|Endast direkt|Om du vill använda Azure RBAC måste du alltid upprätta en anslutning med Azure.  Om du inte vill använda Azure RBAC kan lokala Kubernetes RBAC användas.  **Väntande tillgänglighet för direkt anslutet läge**|
|**Azure Active Directory (AD)**|Kund miljö – > Azure-> kund miljö|Valfritt|Kanske, men du kanske redan betalar för Azure AD|Endast direkt|Om du vill använda Azure AD för autentisering måste du alltid upprätta en anslutning med Azure. Om du inte vill använda Azure AD för autentisering kan du välja oss Active Directory Federation Services (AD FS) (ADFS) över Active Directory. **Väntande tillgänglighet för direkt anslutet läge**|
|**Säkerhetskopiering och återställning**|Kund miljö – > kund miljö|Obligatorisk|Inga|Direkt eller indirekt|Tjänsten säkerhets kopiering och återställning kan konfigureras så att den pekar på lokala lagrings klasser. |
|**Azure Backup-långsiktig kvarhållning**| Kund miljö – > Azure | Valfritt| Ja för Azure Storage | Endast direkt |Du kanske vill skicka säkerhets kopior som tas lokalt till Azure Backup för långsiktig kvarhållning av säkerhets kopior och ta tillbaka dem till den lokala miljön för återställning. **Väntande tillgänglighet för direkt anslutet läge**|
|**Azure Defender säkerhets tjänster**|Kund miljö – > Azure-> kund miljö|Valfritt|Ja|Endast direkt|**Väntande tillgänglighet för direkt anslutet läge**|
|**Etablering och konfigurations ändringar från Azure Portal**|Kund miljö – > Azure-> kund miljö|Valfritt|Nej|Endast direkt|Etablerings-och konfigurations ändringar kan göras lokalt med Azure Data Studio eller [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  I direkt anslutnings läge kommer du också att kunna tillhandahålla och göra konfigurations ändringar från Azure Portal. **Väntande tillgänglighet för direkt anslutet läge**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Information om Internet adresser, portar, kryptering och stöd för proxyserver

I förhands gransknings fasen stöds för närvarande endast det indirekt anslutna läget. I det här läget finns det bara tre anslutningar som krävs för att tillhandahålla tjänster på Internet. Dessa anslutningar är:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [Azure Resource Manager-API:er](#azure-resource-manager-apis)
- [API: er för Azure Monitor](#azure-monitor-apis)

Alla HTTPS-anslutningar till Azure och Microsoft Container Registry krypteras med SSL/TLS med hjälp av officiellt signerade och verifierbara certifikat.

I följande avsnitt finns information om de här anslutningarna. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Microsoft Container Registry är värd för de avbildningar av Azure Arc-aktiverade data Services-behållare.  Du kan hämta avbildningarna från MCR och skicka dem till ett privat behållar register och konfigurera distributions processen för datakontrollanten för att hämta behållar avbildningarna från det privata behållar registret.

#### <a name="connection-source"></a>Anslutningens källa

Kubernetes-kubelet på varje Kubernetes-noder hämtar behållar avbildningarna.

#### <a name="connection-target"></a>Anslutningens mål

`mcr.microsoft.com`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Kan använda proxy

Ja

#### <a name="authentication"></a>Autentisering

Inget

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager-API:er
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] och Azure CLI ansluter till Azure Resource Manager-API: er för att skicka och hämta data till och från Azure för vissa funktioner.

#### <a name="connection-source"></a>Anslutningens källa

En dator som kör Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] eller Azure CLI som ansluter till Azure.

#### <a name="connection-target"></a>Anslutningens mål

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Kan använda proxy

Ja

#### <a name="authentication"></a>Autentisering 

Azure Active Directory

### <a name="azure-monitor-apis"></a>API: er för Azure Monitor

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] och Azure CLI ansluter till Azure Resource Manager-API: er för att skicka och hämta data till och från Azure för vissa funktioner.

#### <a name="connection-source"></a>Anslutningens källa

En dator [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] som kör eller Azure CLI som laddar upp övervaknings mått eller loggar till Azure Monitor.

#### <a name="connection-target"></a>Anslutningens mål

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protokoll

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Kan använda proxy

Ja

#### <a name="authentication"></a>Autentisering 

Azure Active Directory

> [!NOTE]
> För närvarande är alla webbläsares HTTPS/443-anslutningar till Grafana-och Kibana-instrumentpanelerna och från [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] till datakontrollantens API SSL-krypterade med självsignerade certifikat.  En funktion kommer att vara tillgänglig i framtiden som gör att du kan tillhandahålla dina egna certifikat för kryptering av dessa SSL-anslutningar.

Anslutning från Azure Data Studio och [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] till Kubernetes-API-servern använder Kubernetes-autentisering och kryptering som du har upprättat.  Varje användare som använder Azure Data Studio och [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] måste ha en autentiserad anslutning till Kubernetes-API: et för att utföra många av de åtgärder som rör Azure Arc-aktiverade data tjänster.

