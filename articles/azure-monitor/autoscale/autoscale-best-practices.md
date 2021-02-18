---
title: Bästa metoder för autoskalning
description: Autoskalning av mönster i Azure för Web Apps, skalnings uppsättningar för virtuella datorer och Cloud Services
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623950"
---
# <a name="best-practices-for-autoscale"></a>Bästa metoder för autoskalning
Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)-, [Cloud Services](https://azure.microsoft.com/services/cloud-services/)-, [App Service-Web Apps-](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](../../api-management/api-management-key-concepts.md).

## <a name="autoscale-concepts"></a>Skala koncept

* En resurs kan bara ha *en* inställning för autoskalning
* En inställning för autoskalning kan ha en eller flera profiler och varje profil kan ha en eller flera regler för autoskalning.
* En inställning för autoskalning skalar instanser vågrätt, vilket ligger *utanför* genom att öka instanserna och *i* genom att minska antalet instanser.
  En autoskalning-inställning har ett högsta, lägsta och standardvärde för instanser.
* Ett jobb för autoskalning läser alltid det associerade måttet som ska skalas genom att kontrol lera om det har överskridit det konfigurerade tröskelvärdet för skala ut eller skala in. Du kan visa en lista över mått som autoskalning kan skalas med vid [Azure Monitor autoskalning av vanliga mått](autoscale-common-metrics.md).
* Alla tröskelvärden beräknas på en instans nivå. Till exempel, "skala ut efter en instans när genomsnitts processor > 80% när antalet instanser är 2", innebär att skala ut när genomsnitts processor över alla instanser är större än 80%.
* Alla autoskalning-avbrott loggas i aktivitets loggen. Du kan sedan konfigurera en [aktivitets logg avisering](../alerts/activity-log-alerts.md) så att du kan meddela dig via e-post, SMS eller webhookar när det uppstår ett automatiskt skalnings haveri.
* På samma sätt publiceras alla lyckade skalnings åtgärder i aktivitets loggen. Du kan sedan konfigurera en aktivitets logg avisering så att du kan meddela dig via e-post, SMS eller Webhooks när det finns en slutförd autoskalning-åtgärd. Du kan också konfigurera e-post-eller webhook-meddelanden för att få meddelanden om lyckade skalnings åtgärder via fliken meddelanden i den automatiska skalnings inställningen.

## <a name="autoscale-best-practices"></a>Metod tips för autoskalning

Använd följande metod tips när du använder autoskalning.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Se till att de högsta och lägsta värdena är olika och har lämplig marginal mellan sig

Om du har en inställning som har minst 2, max = 2 och det aktuella instansantalet är 2, kan ingen skalningsåtgärd utföras. Ha en lämplig marginal mellan högsta och lägsta antal instanser, som ingår. Autoskalning skalar alltid mellan dessa gränser.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuell skalning återställs vid autoskalning med min och max

Om du uppdaterar instans antalet manuellt till ett värde över eller under det högsta värdet, skalar automatiskt skalnings motorn tillbaka till det minsta värdet (om nedan) eller det högsta (om ovan). Du kan till exempel ange intervallet mellan 3 och 6. Om du har en instans som körs skalas den automatiska skalnings motorn till tre instanser vid nästa körning. Om du anger skalningen till åtta instanser manuellt, skalas den igen till sex instanser nästa körning.  Manuell skalning är tillfälligt om du inte återställer reglerna för automatisk skalning.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Använd alltid en kombination av skalbarhet och skalnings uttryck som utför en ökning och minskning
Om du bara använder en del av kombinationen vidtar autoskalning bara en åtgärd i en enda riktning (skala ut eller in) tills den når det högsta eller lägsta antalet instanser som definierats i profilen. Detta är inte optimalt, helst vill du att din resurs ska skalas upp vid tider med hög användning för att säkerställa tillgängligheten. På samma sätt kan du när som helst få din resurs att skalas ned, så att du kan utnyttja besparingarna.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Välj lämplig statistik för ditt diagnostikmått
För diagnostiska mått kan du välja mellan *genomsnitt*, *lägsta*, *högsta* och *Total* som mått som ska skalas efter. Den vanligaste statistiken är *genomsnitt*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Välj tröskelvärden noga för alla måttyper
Vi rekommenderar att du noga väljer olika tröskelvärden för att skala ut och skala ut baserat på praktiska situationer.

Vi *rekommenderar inte* inställningarna för autoskalning som i exemplen nedan med samma eller liknande tröskelvärden för ut och i villkor:

* Öka antalet instanser med 1 när antalet trådar > = 600
* Minska antalet instanser med 1 när antalet trådar < = 600

Nu ska vi titta på ett exempel på vad som kan leda till ett problem som kan verka förvirrande. Tänk på följande sekvens.

1. Anta att det finns två instanser att börja med och sedan växer det genomsnittliga antalet trådar per instans till 625.
2. Skala ut skalar ut genom att lägga till en tredje instans.
3. Därefter antar vi att det genomsnittliga antalet trådar mellan instanser infaller till 575.
4. Innan du skalar ned försöker autoskalning att uppskatta vad det slutliga stadiet är om det skalas i. Till exempel, 575 x 3 (Aktuellt antal instanser) = 1 725/2 (det slutliga antalet instanser som skalats ned) = 862,5 trådar. Det innebär att den automatiska skalningen skulle behöva skalas ut igen även efter att den skalats i, om det genomsnittliga antalet trådar fortfarande är detsamma eller till och med bara är ett litet belopp. Men om den skalas upp igen upprepas hela processen, vilket leder till en oändlig loop.
5. För att undvika den här situationen (kallas "växlar") skalar inte autoskalning nedåt. Istället hoppar den över och utvärderar villkoret igen nästa gång tjänstens jobb körs. Växlar-tillstånd kan förväxla många människor eftersom autoskalning inte verkar fungera när det genomsnittliga antalet trådar var 575.

Uppskattning under en skalbarhet är avsedd att undvika "växlar"-situationer, där skalnings-och skalnings åtgärder ständigt går tillbaka och tillbaka. Tänk på detta när du väljer samma tröskelvärden för att skala ut och in.

Vi rekommenderar att du väljer en lämplig marginal mellan skalan och i tröskelvärden. Anta till exempel följande bättre regel kombination.

* Öka antalet instanser med 1 räknare när CPU% >= 80
* Minska antalet instanser med 1 räknare när CPU% <= 60

I det här fallet  

1. Anta att det finns två instanser att börja med.
2. Om den genomsnittliga CPU% över instanser går till 80 skalar autoskalning ut genom att lägga till en tredje instans.
3. Anta nu att CPU: n% faller på 60.
4. Skalnings regeln för autoskalning beräknar det slutliga läget om det var för att skala in. Till exempel, 60 x 3 (Aktuellt antal instanser) = 180/2 (slutligt antal instanser vid skalning) = 90. Det innebär att autoskalning inte skalas eftersom det skulle behöva skalas igen omedelbart. I stället hoppar det över skalning.
5. Nästa gång den automatiska skalningen utförs fortsätter processorn att falla till 50. Den beräknar igen – 50 x 3 instans = 150/2 instanser = 75, vilket ligger under tröskelvärdet på 80, så att den skalas upp till 2 instanser.

> [!NOTE]
> Om den automatiska skalnings motorn upptäcker att växlar kan uppstå till följd av skalning till mål antalet instanser, försöker den också skala till ett annat antal instanser mellan det aktuella antalet och antalet mål. Om växlar inte sker inom det här intervallet fortsätter skalnings åtgärden med det nya målet automatiskt.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Att tänka på vid skalning av tröskelvärden för särskilda mått
 För särskilda mått som lagring eller Service Bus Kölängd, är tröskelvärdet det genomsnittliga antalet meddelanden som är tillgängliga per Aktuellt antal instanser. Välj ett värde för det här måttets tröskelvärde noggrant.

Vi illustrerar det med ett exempel för att se till att du förstår beteendet bättre.

* Öka antalet instanser med 1 när antalet meddelanden i lagrings kön >= 50
* Minska antalet instanser med 1 när antalet meddelanden i lagrings kön <= 10

Tänk på följande:

1. Det finns två instanser av lagrings kön.
2. Meddelanden håller på att komma och när du granskar lagrings kön läser det totala antalet 50. Du kan utgå från att den automatiska skalningen bör starta en skalnings åtgärd. Observera dock att det fortfarande är 50/2 = 25 meddelanden per instans. Därför sker inte skalbarhet. För att den första utskalning ska ske bör det totala antalet meddelanden i lagrings kön vara 100.
3. Därefter antar vi att det totala antalet meddelanden når 100.
4. En tredje instans av en instans av lagrings kön har lagts till på grund av en skalbar åtgärd.  Nästa skalnings åtgärd sker inte förrän det totala antalet meddelanden i kön når 150 eftersom 150/3 = 50.
5. Nu blir antalet meddelanden i kön mindre. Med tre instanser sker den första skalnings åtgärden när det totala antalet meddelanden i alla köer är upp till 30 eftersom 30/3 = 10 meddelanden per instans, vilket är tröskelvärdet för skala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Att tänka på vid skalning när flera profiler har konfigurerats för autoskalning
I en inställning för autoskalning kan du välja en standard profil, som alltid används utan beroende på schema eller tid, eller så kan du välja en återkommande profil eller en profil för en fast period med ett datum-och tidsintervall.

När den automatiska skalnings tjänsten bearbetar dem, sker det alltid i följande ordning:

1. Fast datum profil
2. Återkommande profil
3. Standard profil ("Always")

Om ett profil villkor är uppfyllt, kontrollerar inte autoskalning nästa profil villkor under det. Autoskalning bearbetar bara en profil i taget. Det innebär att om du även vill inkludera ett bearbetnings villkor från en profil på lägre nivå måste du även inkludera dessa regler i den aktuella profilen.

Vi ska gå igenom med ett exempel:

Bilden nedan visar en inställning för autoskalning med en standard profil med minsta antal instanser = 2 och maximalt antal instanser = 10. I det här exemplet konfigureras regler för att skala ut när antalet meddelanden i kön är större än 10 och skalas när antalet meddelanden i kön är mindre än tre. Nu kan resursen skalas mellan två och tio instanser.

Det finns dessutom en återkommande profil uppsättning för måndag. Den är inställd för minsta antal instanser = 3 och maximalt antal instanser = 10. Det innebär att den första automatiska skalnings kontrollen för det här tillståndet sker på måndag, och om antalet instanser är två, skalas det till det nya minimivärdet på tre. Så länge som den automatiska skalningen fortsätter att hitta det här profil villkoret (måndag) bearbetar det bara CPU-baserade regler för skalbarhet/i som kon figurer ATS för den här profilen. För tillfället söker det inte efter kölängd. Men om du även vill att villkoret för Kölängd ska kontrol leras bör du inkludera dessa regler från standard profilen i din måndag profil.

När autoskalning växlar tillbaka till standard profilen kontrollerar den först om minimi-och Max villkoren är uppfyllda. Om antalet instanser är 12, skalas det till 10, vilket är det högsta tillåtna antalet för standard profilen.

![inställningar för autoskalning](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Att tänka på vid skalning när flera regler har konfigurerats i en profil

Det finns fall där du kan behöva ange flera regler i en profil. Följande regler för autoskalning används av autoskalning-motorn när flera regler är inställda.

Vid *utskalning* körs autoskalning om en regel uppfylls.
Vid *skalbarhet* kräver autoskalning att alla regler är uppfyllda.

För att illustrera, förutsätter vi att du har följande fyra regler för autoskalning:

* Om CPU < 30%, skala in med 1
* Om minne < 50%, skala in med 1
* Om CPU > 75%, skala ut med 1
* Om minne > 75%, skala ut med 1

Sedan inträffar följande:

* Om CPU är 76% och minnet är 50%, skalas vi ut.
* Om CPU är 50% och minnet är 76%, skalas vi ut.

Å andra sidan, om CPU är 25% och minnet är 51%, skalas **inte** . För att kunna skala in måste CPU: n vara 29% och minne 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Välj alltid ett säkert standardantal instanser
Antalet standard instanser är viktigt eftersom autoskalning skalar din tjänst till det antalet när mått inte är tillgängliga. Välj därför ett standard instans antal som är säkert för dina arbets belastningar.

### <a name="configure-autoscale-notifications"></a>Konfigurera aviseringar för autoskalning
Autoskalning skickas till aktivitets loggen om något av följande villkor inträffar:

* Autoskalning utfärdar en skalnings åtgärd.
* Tjänsten för autoskalning slutförde en skalnings åtgärd.
* Det går inte att utföra en skalnings åtgärd för AutoScale-tjänsten.
* Mått är inte tillgängliga för AutoScale-tjänsten för att fatta ett skalnings beslut.
* Mått är tillgängliga (återställning) igen för att fatta ett skalnings beslut.
* Autoskalning identifierar växlar och avbryter skalnings försöket. En logg typ visas `Flapping` i den här situationen. Om du ser detta bör du överväga om tröskelvärdena är för smala.
* Autoskalning identifierar växlar men kan fortfarande skalas. En logg typ visas `FlappingOccurred` i den här situationen. Om du ser detta har motorn för autoskalning försökt skala (t. ex. från 4 instanser till 2), men har fastställt att detta skulle orsaka växlar. I stället har den automatiska skalnings motorn skalats till ett annat antal instanser (t. ex. med 3 instanser i stället för 2), vilket inte längre orsakar växlar, så den har skalats till det här antalet instanser.

Du kan också använda en aktivitets logg avisering för att övervaka den automatiska skalnings motorns hälso tillstånd. Här följer exempel på hur du [skapar en aktivitets logg avisering för att övervaka alla åtgärder för autoskalning av motorn i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) eller för att [skapa en aktivitets logg avisering för att övervaka alla misslyckade skalnings åtgärder för autoskalning i/skala ut i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Förutom att använda aktivitets logg aviseringar kan du också konfigurera e-post eller webhook-meddelanden för att få meddelanden om lyckade skalnings åtgärder via fliken meddelanden i inställningen för autoskalning.

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitets logg avisering för att övervaka alla åtgärder för autoskalning av motorn i din prenumeration.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitets logg avisering för att övervaka alla misslyckade skalnings åtgärder för autoskalning i/skala ut i din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

