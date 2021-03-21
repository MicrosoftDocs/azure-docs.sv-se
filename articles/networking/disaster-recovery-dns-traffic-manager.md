---
title: Haveri beredskap med Azure DNS och Traffic Manager | Microsoft Docs
description: Översikt över katastrof återställnings lösningar med hjälp av Azure DNS och Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234162"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Haveriberedskap med hjälp av Azure DNS och Traffic Manager

Haveriberedskap handlar om att återställa från en betydande förlust av programfunktion. För att kunna välja en katastrof återställnings lösning måste företags-och teknik ägare först bestämma vilken nivå av funktioner som krävs vid en katastrof, till exempel otillgänglig, delvis tillgänglig via begränsade funktioner eller fördröjd tillgänglighet eller helt tillgängligt.
De flesta företags kunder väljer en arkitektur med flera regioner för återhämtning mot en redundansväxling av program eller infrastruktur nivå. Kunderna kan välja flera metoder i fråge formuläret för att uppnå redundans och hög tillgänglighet via redundant arkitektur. Här följer några av de populära metoderna:

- **Aktiv-passiv med kall vänte läge**: i den här lösningen för redundans är de virtuella datorerna och andra apparater som körs i standby-regionen inte aktiva förrän det finns behov av redundans. Produktions miljön replikeras dock i form av säkerhets kopior, VM-avbildningar eller Resource Manager-mallar till en annan region. Den här mekanismen för växling vid fel är kostnads effektiv men tar längre tid att utföra en fullständig redundansväxling.
 
    ![Aktiv/passiv med kall vänte läge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Bild-aktiv/passiv med återställning av haveri beredskap för kall vänte läge*

- **Aktiv/passiv med pilot ljus**: i den här lösningen för redundans konfigureras vänte läges miljön med en minimal konfiguration. Installations programmet har bara de tjänster som krävs för att stödja en minimal och kritisk uppsättning program. I sitt egna formulär kan det här scenariot endast köra minimala funktioner, men kan skala upp och skapa ytterligare tjänster för att dra nytta av produktions belastningen om en redundansväxling inträffar.
    
    ![Aktiv/passiv med pilot ljus](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Bild: aktiv/passiv med pilot låg haveri beredskaps konfiguration*

- **Aktiv/passiv med varmt vänte läge**: i den här lösningen för redundans är vänte läge förvärmt och är redo att ta bas inläsningen, automatisk skalning aktive ras och alla instanser är igång. Den här lösningen skalas inte för att ta fullständig produktions belastning men fungerar, och alla tjänster är igång. Den här lösningen är en förstärkt version av pilot metoden.
    
    ![Aktiv/passiv med varmt vänte läge](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Bild: aktiv/passiv med haveri beredskap för katastrof återställning*
    
Mer information om redundans och hög tillgänglighet finns i [haveri beredskap för Azure-program](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planera din katastrof återställnings arkitektur

Det finns två tekniska aspekter för att konfigurera din katastrof återställnings arkitektur:
-  Använda en distributions metod för att replikera instanser, data och konfigurationer mellan primära miljöer och vänte läge. Den här typen av haveri beredskap kan göras internt via Azure Site-Recovery via Microsoft Azure partner utrustning/tjänster som Veritas eller NetApp. 
- Utveckla en lösning för att minska nätverks-/webb trafik från den primära platsen till vänte läges platsen. Den här typen av katastrof återställning kan uppnås via Azure DNS, Azure Traffic Manager (DNS) eller globala belastningsutjämnare från tredje part.

Den här artikeln är begränsad till metoder via omdirigering av nätverks-och webb trafik. Instruktioner för hur du konfigurerar Azure Site Recovery finns i [Azure Site Recovery dokumentation](../site-recovery/index.yml).
DNS är en av de mest effektiva mekanismerna för att minska nätverks trafiken eftersom DNS ofta är globalt och externt för data centret och är isolerat från eventuella regional-eller tillgänglighets zons nivå problem (AZ). En kan använda en DNS-baserad redundans och i Azure kan två DNS-tjänster utföra samma i vissa mode-Azure DNS (auktoritativ DNS) och Azure Traffic Manager (DNS-baserad Smart trafik dirigering). 

Det är viktigt att förstå några av de koncept i DNS som används för att diskutera de lösningar som beskrivs i den här artikeln:
- **DNS A-post** – a-poster är pekare som pekar en domän mot en IPv4-adress. 
- **CNAME eller kanoniskt namn** – den här post typen används för att peka på en annan DNS-post. CNAME svarar inte med en IP-adress utan pekaren till posten som innehåller IP-adressen. 
- **Viktad routning** – en kan välja att koppla en vikt till tjänstens slut punkter och sedan distribuera trafiken utifrån tilldelade vikter. Denna routningsmetod är en av de fyra mekanismer för trafikroutning som är tillgängliga i Traffic Manager. Mer information finns i [metoden för viktad routning](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Prioriterad routning** – prioriterad routning baseras på hälso kontroller av slut punkter. Som standard skickar Azure Traffic Manager all trafik till slut punkten med högst prioritet och vid ett haveri eller katastrof dirigerar Traffic Manager trafiken till den sekundära slut punkten. Mer information finns i [metoden prioriterad routning](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Manuell redundans med Azure DNS
Den Azure DNS manuella redundansväxlingen för haveri beredskap använder standard-DNS-mekanismen för att redundansväxla till säkerhets kopierings platsen. Det manuella alternativet via Azure DNS fungerar bäst när det används tillsammans med kall vänte läge eller pilot metoden. 

![Manuell redundans med Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Bild – manuell redundans med Azure DNS*

De antaganden som görs för lösningen är:
- Både primära och sekundära slut punkter har statiska IP-adresser som inte ändras ofta. Anta att IP-adressen för den primära platsen är 100.168.124.44 och att IP-adressen för den sekundära platsen är 100.168.124.43.
- Det finns en Azure DNS zon för både den primära och den sekundära platsen. Anta att slut punkten är prod.contoso.com för den primära platsen och att säkerhets kopierings platsen är dr.contoso.com. En DNS-post för huvud programmet som kallas www- \. contoso.com finns också.   
- TTL-värdet är på eller under SLA-uppsättningen RTO i organisationen. Om ett företag till exempel anger RTO för ett katastrof svar som ska vara 60 minuter bör TTL vara mindre än 60 minuter, helst desto lägre desto bättre. 
  Du kan konfigurera Azure DNS för manuell redundans enligt följande:
- Skapa en DNS-zon
- Skapa DNS-zon poster
- Uppdatera CNAME-post

### <a name="step-1-create-a-dns"></a>Steg 1: skapa en DNS
Skapa en DNS-zon (till exempel www \. -contoso.com) enligt nedan:

![Skapa en DNS-zon i Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Bild – skapa en DNS-zon i Azure*

### <a name="step-2-create-dns-zone-records"></a>Steg 2: skapa DNS-zon poster

I den här zonen skapar du tre poster (till exempel www- \. contoso.com, Prod.contoso.com och Dr.Consoto.com) som visas nedan.

![Skapa DNS-zon poster](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Bild – skapa DNS-zon poster i Azure*

I det här scenariot har webbplatsens \. contoso.com ett TTL-värde på 30 minuter, vilket är bra under den angivna RTO och pekar på produktions platsens Prod.contoso.com. Den här konfigurationen är under normal affärs verksamhet. TTL-värdet för prod.contoso.com och dr.contoso.com har angetts till 300 sekunder eller 5 minuter. Du kan använda en tjänst för Azure-övervakning, till exempel Azure Monitor eller Azure App insikter, eller alla partner övervaknings lösningar som dynaTrace, du kan även använda hemutvecklande lösningar som kan övervaka eller identifiera problem med program eller virtuella infrastrukturer.

### <a name="step-3-update-the-cname-record"></a>Steg 3: uppdatera CNAME-posten

När ett problem har identifierats ändrar du värdet för posten så att det pekar på dr.contoso.com enligt nedan:
       
![Uppdatera CNAME-post](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Bild – uppdatera CNAME-posten i Azure*

Inom 30 minuter omdirigeras alla frågor till www-contoso.com till dr.contoso.com när de flesta matchare kommer att uppdatera den cachelagrade zonfilen \. .
Du kan också köra följande Azure CLI-kommando för att ändra CNAME-värdet:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Det här steget kan utföras manuellt eller via Automation. Det kan göras manuellt via-konsolen eller av Azure CLI. Azure SDK och API kan användas för att automatisera CNAME-uppdateringen så att ingen manuell åtgärd krävs. Automation kan skapas via Azure Functions eller i ett övervaknings program från tredje part eller till och med lokalt.

### <a name="how-manual-failover-works-using-azure-dns"></a>Hur manuell redundans fungerar med hjälp av Azure DNS
Eftersom DNS-servern ligger utanför redundansväxlingen eller katastrof zonen, är den isolerad mot eventuella drift stopp. Detta gör att användaren kan skapa ett enkelt scenario för växling vid fel som är kostnads effektivt och som kommer att fungera hela tiden förutsatt att operatören har nätverks anslutning under en katastrof och kan göra det. Om lösningen är skriptad måste du se till att servern eller tjänsten som kör skriptet bör vara isolerad mot problem som påverkar produktions miljön. Tänk också på att det låga TTL-värdet som angavs mot zonen, så att ingen konflikt löses över hela världen, så att slut punkten cachelagras för länge och kunder kan komma åt webbplatsen i RTO. För ett kall vänte läge och pilot ljus kan det krävas att en del förvärmning och annan administrativ aktivitet kan krävas – det bör också finnas tillräckligt med tid innan du gör vänd.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatisk redundans med Azure Traffic Manager
När du har komplexa arkitekturer och flera uppsättningar resurser som kan utföra samma funktion kan du konfigurera Azure-Traffic Manager (baserat på DNS) för att kontrol lera hälso tillståndet för dina resurser och dirigera trafiken från den icke-felfria resursen till den felfria resursen. I följande exempel har både den primära och den sekundära regionen en fullständig distribution. Den här distributionen omfattar moln tjänsterna och en synkroniserad databas. 

![Automatisk redundans med Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Bild-automatisk redundans med Azure Traffic Manager*

Men endast den primära regionen hanterar nätverks förfrågningar aktivt från användarna. Den sekundära regionen blir bara aktiv när den primära regionen upplever ett tjänst avbrott. I så fall skickas alla nya nätverks begär anden till den sekundära regionen. Eftersom säkerhets kopieringen av databasen är nära momentan, har båda belastnings utjämningarna IP-adresser som kan kontrol leras och instanserna alltid är igång, och den här topologin ger ett alternativ för att gå i för en låg RTO och redundans utan någon manuell åtgärd. Den sekundära redundansväxlingen måste vara redo att gå direkt efter fel i den primära regionen.
Det här scenariot är idealiskt för användning av Azure-Traffic Manager som har inbyggda avsökningar för olika typer av hälso kontroller, inklusive http/https och TCP. Azure Traffic Manager har också en regel motor som kan konfigureras för redundans när ett fel uppstår enligt beskrivningen nedan. Vi tar hänsyn till följande lösning med Traffic Manager:
- Kunden har regionen #1-slutpunkten känd som prod.contoso.com med en statisk IP-adress som 100.168.124.44 och en region #2-slutpunkt som kallas dr.contoso.com med en statisk IP-adress som 100.168.124.43. 
-   Var och en av de här miljöerna sker via en offentlig egenskap som en belastningsutjämnare. Belastningsutjämnaren kan konfigureras med en DNS-baserad slut punkt eller ett fullständigt kvalificerat domän namn (FQDN) som visas ovan.
-   Alla instanser i region 2 finns nära replikering i real tid med region 1. Dessutom är dator avbildningarna uppdaterade och alla program/konfigurations data korrigeras och är i linje med region 1.  
-   Autoskalning är förkonfigurerat i förväg. 

De steg som vidtas för att konfigurera redundansväxlingen med Azure Traffic Manager är följande:
1. Skapa en ny Azure Traffic Manager-profil
2. Skapa slut punkter inom Traffic Manager profilen
3. Konfigurera hälso kontroll och redundanskonfiguration

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Steg 1: skapa en ny Azure Traffic Manager-profil
Skapa en ny Azure Traffic Manager-profil med namnet contoso123 och välj routningsmetod som prioritet. Om du har en befintlig resurs grupp som du vill associera med kan du välja en befintlig resurs grupp, annars skapar du en ny resurs grupp.

![Skapa Traffic Manager profil](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Bild – skapa en Traffic Manager-profil*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Steg 2: skapa slut punkter i Traffic Manager profilen

I det här steget skapar du slut punkter som pekar på platserna för produktion och haveri beredskap. Här väljer du **typ** som en extern slut punkt, men om resursen finns i Azure kan du även välja **Azure-slutpunkt** . Om du väljer **Azure-slutpunkt** väljer du en **mål resurs** som antingen är en **App Service** eller en **offentlig IP-adress** som tilldelas av Azure. Prioriteten anges som **1** eftersom den är den primära tjänsten för region 1.
På samma sätt kan du skapa en slut punkt för haveri beredskap i Traffic Manager också.

![Skapa Disaster Recovery-slutpunkter](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Bild – skapa Disaster Recovery-slutpunkter*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Steg 3: Konfigurera hälso kontroll och redundanskonfiguration

I det här steget ställer du in DNS-TTL på 10 sekunder, som följer de flesta rekursiva matchare med Internet. Den här konfigurationen innebär att ingen DNS-matchare cachelagrar informationen i mer än 10 sekunder. För inställningarna för slut punkts övervakaren är sökvägen aktuell uppsättning vid/eller rot, men du kan anpassa slut punkts inställningarna för att utvärdera en sökväg, till exempel prod.contoso.com/index. Exemplet nedan visar **https** som avsöknings protokoll. Du kan dock även välja **http** eller **TCP** . Valet av protokoll beror på slut programmet. Avsöknings intervallet är inställt på 10 sekunder, vilket möjliggör snabb avsökning och ett nytt försök har angetts till 3. Det innebär att Traffic Manager redundansväxlas till den andra slut punkten om tre intervall i följd registrerar ett fel. Följande formel definierar den totala tiden för automatisk redundans: tid för redundans = TTL + omförsök * avsöknings intervall och i det här fallet är värdet 10 + 3 * 10 = 40 sekunder (max).
Om försöket är inställt på 1 och TTL är inställt på 10 sekunder är tiden för redundans 10 + 1 * 10 = 20 sekunder. Ange ett nytt värde som är större än **1** för att eliminera risken för redundans på grund av falska positiva identifieringar eller mindre nätverks-signaler. 


![Konfigurera hälso kontroll](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Bild – konfigurera hälso kontroll och redundanskonfiguration*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Hur automatisk redundans fungerar med hjälp av Traffic Manager

Under en katastrof kommer den primära slut punkten att avsökas och statusen ändras till **försämrad** och haveri beredskaps platsen är **online**. Som standard skickar Traffic Manager all trafik till den primära slutpunkten (högsta prioritet). Om den primära slut punkten verkar försämrad dirigerar Traffic Manager trafiken till den andra slut punkten så länge den är felfri. En har möjlighet att konfigurera fler slut punkter inom Traffic Manager som kan fungera som ytterligare slut punkter för redundans eller, som belastningsutjämnare delar belastningen mellan slut punkterna.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Läs mer om [Azure DNS](../dns/dns-overview.md).