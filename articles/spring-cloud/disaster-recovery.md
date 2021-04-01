---
title: Azure våren Cloud geo-Disaster Recovery | Microsoft Docs
description: Lär dig hur du skyddar ditt våren Cloud-program från regionala avbrott
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092910"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Haveri beredskap för Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

I den här artikeln beskrivs några strategier som du kan använda för att skydda dina Azure våren Cloud-program från att drabbas av drift stopp.  En region eller ett Data Center kan drabbas av drift stopp som orsakas av regionala katastrofer, men noggrann planering kan minimera påverkan på dina kunder.

## <a name="plan-your-application-deployment"></a>Planera program distributionen

Azure våren Cloud-program körs i en angiven region.  Azure körs i ett antal geografier över hela världen. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera data Center.  Varje Azure-region är kopplad till en annan region inom samma geografi, tillsammans med ett regionalt par. Azure serialiserar plattforms uppdateringar (planerat underhåll) över regionala par, vilket säkerställer att endast en region i varje par uppdateras i taget. I händelse av ett avbrott som påverkar flera regioner prioriteras minst en region i varje par för återställning.

Att säkerställa hög tillgänglighet och skydd från katastrofer kräver att du distribuerar dina moln program till flera regioner.  Azure tillhandahåller en lista över [kopplade regioner](../best-practices-availability-paired-regions.md) så att du kan planera dina våren-moln distributioner till regionala par.  Vi rekommenderar att du beaktar tre viktiga faktorer när du utformar en mikrotjänsts arkitektur: regions tillgänglighet, Azure-kopplade regioner och tjänst tillgänglighet.

*  Region tillgänglighet: Välj ett geografiskt område nära användarna för att minimera nätverks fördröjningen och överförings tiden.
*  Azure-kopplade regioner: Välj kopplade regioner inom det valda geografiska utrymmet för att säkerställa koordinerade plattforms uppdateringar och prioriterad återställning vid behov.
*  Tjänst tillgänglighet: Bestäm om dina kopplade regioner ska köra frekvent/varm, varm/varm eller varm/kall.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Använd Azure Traffic Manager för att dirigera trafik

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) tillhandahåller DNS-baserad belastnings utjämning för trafiken och kan distribuera nätverks trafik över flera regioner.  Använd Azure Traffic Manager för att dirigera kunder till den närmaste Azure våren Cloud Service-instansen till dem.  För bästa prestanda och redundans ska du dirigera all program trafik via Azure Traffic Manager innan du skickar den till din Azure våren Cloud-tjänst.

Om du har Azure våren Cloud-program i flera regioner använder du Azure-Traffic Manager för att kontrol lera flödet av trafik till dina program i varje region.  Definiera en Azure Traffic Manager-slutpunkt för varje tjänst som använder tjänstens IP-adress. Kunder bör ansluta till ett Azure Traffic Manager DNS-namn som pekar på moln tjänsten Azure våren.  Azure Traffic Manager belastnings Utjämnings trafik mellan de definierade slut punkterna.  Om en katastrof träffar ett Data Center dirigerar Azure Traffic Manager trafiken från den regionen till dess par, vilket garanterar tjänste kontinuiteten.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Skapa Azure-Traffic Manager för Azure våren Cloud

1. Skapa ett Azure våren-moln i två olika regioner.
Du behöver två tjänst instanser av Azure våren Cloud som distribueras i två olika regioner (USA, östra och Västeuropa). Starta ett befintligt Azure våren Cloud-program med hjälp av Azure Portal för att skapa två tjänst instanser. Var och en fungerar som primär och misslyckad slut punkt för trafik. 

**Två tjänst instans uppgifter:**

| Tjänstens namn | Location | Program |
|--|--|--|
| service – exempel – a | East US | Gateway/auth-service/account-service |
| service – exempel-b | Europa, västra | Gateway/auth-service/account-service |

2. Konfigurera anpassad domän för tjänst följ det [anpassade domän dokumentet](spring-cloud-tutorial-custom-domain.md) för att konfigurera en anpassad domän för dessa två befintliga tjänst instanser. När den har kon figurer ATS kommer båda tjänst instanserna att bindas till den anpassade domänen: bcdr-test.contoso.com

3. Skapa en Traffic Manager och två slut punkter: [skapa en Traffic Manager profil med hjälp av Azure Portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Här är Traffic Manager-profilen:
* Traffic Manager DNS-namn: `http://asc-bcdr.trafficmanager.net`
* Slut punkts profiler: 

| Profil | Typ | Mål | Prioritet | Anpassade huvud inställningar |
|--|--|--|--|--|
| Slut punkt för en profil | Extern slut punkt | service-sample-a.asc-test.net | 1 | värd: bcdr-test.contoso.com |
| Slut punkt B-profil | Extern slut punkt | service-sample-b.asc-test.net | 2 | värd: bcdr-test.contoso.com |

4. Skapa en CNAME-post i DNS-zonen: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. Nu är miljön helt konfigurerad. Kunderna ska kunna komma åt appen via: bcdr-test.contoso.com

## <a name="next-steps"></a>Nästa steg

* [Snabb start: Distribuera ditt första Azure våren Cloud-program](spring-cloud-quickstart.md)
