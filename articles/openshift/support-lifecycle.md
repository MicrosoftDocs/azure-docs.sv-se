---
title: Support livs cykel för Azure Red Hat OpenShift
description: Förstå support livs cykeln och vilka versioner som stöds för Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: fca01c77a1ff47cbeee167eb408ed9f29a1307bd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634336"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Supportlivscykel för Azure Red Hat OpenShift 4

Red Hat släpper lägre versioner av Red Hat OpenShift container Platform (OCP) ungefär var tredje månad. Dessa versioner innehåller nya funktioner och förbättringar. Uppdaterings versioner är oftare (vanligt vis veckovis) och är endast avsedda för viktiga fel korrigeringar i en lägre version. Dessa korrigerings versioner kan innehålla korrigeringar av säkerhets sårbarheter eller större buggar.

Den nya versionen av Azure Red Hat är byggd från vissa versioner av OCP. Den här artikeln beskriver de versioner av OCP som stöds för Azure Red Hat OpenShift och information om uppgraderingar, föråldrade och support principer.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift-versioner

Red Hat OpenShift container Platform använder semantisk versions hantering. Med semantisk versions hantering används olika nivåer av versions nummer för att ange olika nivåer av versions hantering. I följande tabell visas olika delar av ett semantiskt versions nummer, i det här fallet med versions numret 4.4.11.

|Huvud version (x)|Lägre version (y)|Uppdatering (z)|
|-|-|-|
|4|4|11|

Varje tal i versionen indikerar allmän kompatibilitet med den tidigare versionen:

* **Huvud version**: inga större versioner av versionen planeras just nu. Huvud versioner ändras när inkompatibla API-ändringar eller bakåtkompatibilitet kan vara brutna.
* **Lägre version**: lanseras ungefär var tredje månad. Lägre versions uppgraderingar kan omfatta funktioner tillägg, förbättringar, utfasningar, borttagningar, fel korrigeringar, säkerhets förbättringar och andra förbättringar.
* **Korrigeringar**: publiceras vanligt vis varje vecka eller efter behov. Uppgraderingar av uppdaterings versioner kan omfatta fel korrigeringar, säkerhets förbättringar och andra förbättringar.

Kunderna bör sträva efter att köra den senaste del versionen av den huvud version de kör. Om ditt produktions kluster till exempel är på 4,4 och 4,5 är den senaste tillgängliga lägre versionen för 4-serien, bör du uppgradera till 4,5 så snart som möjligt.

### <a name="upgrade-channels"></a>Uppgradera kanaler

Uppgraderings kanaler är knutna till en lägre version av Red Hat OpenShift container Platform (OCP). Till exempel kommer OCP 4,4-uppgraderings kanaler aldrig att innehålla en uppgradering till en 4,5-version. Uppgradera kanal kontrollen endast versions val och påverkar inte versionen av klustret.

Azure Red Hat OpenShift 4 stöder endast stabila kanaler. Till exempel: stabil-4,4.

Du kan använda en stabil-4,5-kanal för att uppgradera från en tidigare del version av Azure Red Hat OpenShift. Kluster som uppgraderas med hjälp av snabb, för hands version och kandidat kanaler stöds inte.

Om du ändrar till en kanal som inte innehåller din aktuella version kan en avisering visas och inga uppdateringar kan rekommenderas, men du kan ändra tillbaka till den ursprungliga kanalen när som helst.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift container Platform-version support policy

Azure Red Hat OpenShift stöder två allmänt tillgängliga (GA) lägre versioner av Red Hat OpenShift container Platform:
* Den senaste sekundär versionen av GA som lanseras i Azure Red Hat OpenShift (som vi refererar till som N)
* En tidigare del version (N-1)

Om den är tillgänglig i en stabil uppgraderings kanal, kan senare del versioner (N + 1, N + 2) som är tillgängliga i överordnade OCP också stödjas.

Kritiska uppdaterings uppdateringar tillämpas på kluster automatiskt av Azure Red Hat OpenShift site tillförlitlighet ingenjörer (SRE). Kunder som vill installera korrigerings uppdateringar i förväg är kostnads fria.

Om till exempel Azure Red Hat OpenShift presenterar 4,5. z idag, tillhandahålls support för följande versioner:

|Ny del version|Versions lista som stöds|
|-|-|
|4,5. z|4.5. z, 4.4. z|

". z" är representativ för korrigerings versioner. Om det är tillgängligt i en stabil uppgraderings kanal kan kunder även uppgradera till 4.6. z.

När en ny del version introduceras, är den äldsta lägre versionen föråldrad och tas bort. Anta till exempel att den aktuella versions listan som stöds är 4.5. z och 4.4. z. När Azure Red Hat OpenShift släpper 4.6. z tas versionen 4.4. z bort och kommer inte att fungera inom 30 dagar.

> [!NOTE]
> Observera att om kunderna kör en Red Hat OpenShift-version som inte stöds kan de uppmanas att uppgradera när de begär support för klustret. Kluster som kör ej stödda Red Hat OpenShift-utgåvor omfattas inte av SLA för Azure Red Hat OpenShift.

## <a name="release-and-deprecation-process"></a>Version och utfasnings process

Du kan referera till kommande versions versioner och föråldrade versioner av en versions kalender i Azure Red Hat OpenShift.

För nya lägre versioner av Red Hat OpenShift container Platform:
* SRE-teamet för Azure Red Hat OpenShift publicerar ett för hands meddelande med det planerade datumet för en ny versions version och respektive gamla versions utfasning i [Azure Red Hat OpenShift-versionen av viktig information](https://github.com/Azure/OpenShift/releases) minst 30 dagar innan borttagning.
* SRE-teamet för Azure Red Hat OpenShift publicerar ett meddelande om tjänstens hälso tillstånd som är tillgängligt för alla kunder med Azure Red Hat OpenShift och Portal åtkomst och skickar ett e-postmeddelande till prenumerations administratörerna med den planerade versionen av borttagnings datum.
* Kunder har 30 dagar från borttagnings versionen för att uppgradera till en lägre versions version som stöds för att fortsätta få support.

För nya korrigerings versioner av Red Hat OpenShift container Platform:
* På grund av den brådskande typen av korrigerings versioner kan dessa introduceras i tjänsten av Azure Red Hat OpenShift SRE-teamet när de blir tillgängliga.
* I allmänhet utför inte det SRE-teamet för Azure Red Hat OpenShift en bred kommunikation för installationen av nya korrigerings versioner. Teamet övervakar dock och validerar alltid tillgängliga CVE-korrigeringsfiler för att stödja dem inom rimlig tid. Om kund åtgärd krävs meddelar teamet kunderna om uppgraderingen.

## <a name="supported-versions-policy-exceptions"></a>Princip undantag för versioner som stöds

SRE-teamet för Azure Red Hat OpenShift reserverar rätten att lägga till eller ta bort nya/befintliga versioner, eller fördröjning av kommande versioner av lägre versioner som har identifierats för att ha en eller flera kritiska produktions konsekvenser för fel eller säkerhets problem utan föregående meddelande.

Vissa korrigerings versioner kan hoppas över, eller så kan distributionen påskyndas beroende på fel-eller säkerhets problemets allvarlighets grad.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-och CLI-versioner

När du distribuerar ett Azure Red Hat OpenShift-kluster i portalen eller med Azure CLI, är klustret standard den senaste (N) lägre versionen och den senaste kritiska korrigeringen. Om till exempel Azure Red Hat OpenShift stöder 4,5. z och 4.4. z, är standard versionen för nya installationer 4,5. z. Kunder som vill använda den senaste lägre versionen av OCP (N + 1, N + 2) kan uppgradera klustret när som helst till eventuell version som är tillgänglig i de stabila uppgraderings kanalerna.

## <a name="azure-red-hat-openshift-release-calendar"></a>Versions kalender för Azure Red Hat OpenShift

Se följande guide för den [tidigare versionen av Red Hat OpenShift container Platform (uppladdning)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP-version|Överordnad version|Azure Red Hat OpenShift allmän tillgänglighet|Uttjänta|
|-|-|-|-|
|4.3|Januari 2020|April 2020| Augusti 2020|
|4.4|Maj 2020|Juli 2020|4,6 GA|
|4,5|Juli 2020| November 2020|4,7 GA
|4,6|Oktober 2020| Februari 2021|4,8 GA|

## <a name="faq"></a>Vanliga frågor

**Vad händer när en användare uppgraderar ett OpenShift-kluster med en lägre version som inte stöds?**

Om du använder N-2-versionen eller äldre, innebär det att du är utanför supporten och kommer att bli ombedd att uppgradera. När uppgraderingen från version N-2 till N-1 lyckas, är du tillbaka i våra support principer. Exempel:
* Om den äldsta Azure Red Hat OpenShift-versionen som stöds är 4.4. z och du är på 4,3. z eller äldre är du utanför supporten.
* När uppgraderingen från 4.3. ö till 4.4. z eller senare lyckas är du tillbaka i våra support principer.

Det finns inte stöd för att återställa klustret till en tidigare version eller en återställning. Det finns bara stöd för att uppgradera till en nyare version.

**Vad betyder "utanför supporten"?**

"Utanför supporten" innebär att den version som du kör ligger utanför listan över versioner som stöds och du kan uppmanas att uppgradera klustret till en version som stöds när du begär support, såvida du inte är inom 30 dagar efter versionens utfasnings period. Dessutom gör inte Azure Red Hat OpenShift några körnings-eller SLA-garantier för kluster utanför listan över versioner som stöds i slutet av den 30 dagariga Grace-perioden.
