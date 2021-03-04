---
title: Visa program beroenden med VM Insights
description: Map är en funktion i VM Insights. Den identifierar automatiskt program komponenter i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Den här artikeln innehåller information om hur du använder kart funktionen i olika scenarier.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 267ec0ed01fb85e9e29b0b8c9294d20d610db8fb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046575"
---
# <a name="use-the-map-feature-of-vm-insights-to-understand-application-components"></a>Använd kart funktionen för VM-insikter för att förstå program komponenter
I VM Insights kan du Visa identifierade program komponenter på virtuella Windows-och Linux-datorer (VM: ar) som körs i Azure eller i din miljö. Du kan titta på de virtuella datorerna på två sätt. Visa en karta direkt från en virtuell dator eller Visa en karta från Azure Monitor för att se komponenterna mellan grupper av virtuella datorer. Den här artikeln hjälper dig att förstå de här två visnings metoderna och hur du använder kart funktionen. 

Information om hur du konfigurerar VM Insights finns i [Aktivera VM-insikter](./vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introduktion till kart upplevelsen
Innan du simhopp i kart upplevelsen bör du förstå hur den visar och visualiserar information. Oavsett om du väljer kart funktionen direkt från en virtuell dator eller från Azure Monitor, ger kart funktionen en konsekvent upplevelse. Den enda skillnaden är att från Azure Monitor visar en karta alla medlemmar i ett program med flera nivåer eller kluster.

Funktionen Map visualiserar de virtuella datorernas beroenden genom att identifiera processer som körs och som har: 

- Aktiva nätverks anslutningar mellan servrar.
- Latens för inkommande och utgående anslutning.
- Portar i alla TCP-anslutna arkitekturer under ett angivet tidsintervall.  
 
Expandera en virtuell dator för att Visa process information och bara de processer som kommunicerar med den virtuella datorn. Klient gruppen visar antalet klient dels klienter som ansluter till den virtuella datorn. Server Port grupper visar antalet backend-servrar som den virtuella datorn ansluter till. Expandera en server-port-grupp om du vill se en detaljerad lista över servrar som ansluter över den porten.  

När du väljer den virtuella datorn visas egenskaperna för den virtuella datorn i rutan **Egenskaper** till höger. Egenskaperna omfattar system information som rapporteras av operativ systemet, egenskaperna för den virtuella Azure-datorn och ett ring diagram som sammanfattar de identifierade anslutningarna. 

![Fönstret Egenskaper](./media/vminsights-maps/properties-pane-01.png)

På höger sida av fönstret väljer du **logg händelser** för att visa en lista med data som den virtuella datorn har skickat till Azure Monitor. Dessa data är tillgängliga för frågor.  Välj valfri posttyp för att öppna sidan **loggar** där du ser resultatet för den post typen. Du kan också se en förkonfigurerad fråga som är filtrerad mot den virtuella datorn.  

![Fönstret logg händelser](./media/vminsights-maps/properties-pane-logs-01.png)

Stäng sidan **loggar** och gå tillbaka till fönstret **Egenskaper** . Där väljer du **aviseringar** för att visa aviseringar om hälso villkor för virtuella datorer. Kart funktionen integreras med Azure-aviseringar för att visa aviseringar för den valda servern i det valda tidsintervallet. Servern visar en ikon för aktuella aviseringar så visas aviseringarna i fönstret **dator aviseringar** . 

![Fönstret aviseringar](./media/vminsights-maps/properties-pane-alerts-01.png)

Om du vill att kart funktionen ska visa relevanta aviseringar skapar du en varnings regel som gäller för en speciell dator:

- Inkludera en sats för att gruppera aviseringar efter dator (till exempel **efter dator intervall 1 minut**).
- Basera aviseringen på ett mått.

Mer information om Azure-aviseringar och hur du skapar aviserings regler finns [i enhetliga aviseringar i Azure Monitor](../alerts/alerts-overview.md).

I det övre högra hörnet beskriver **förklarings** alternativet symboler och roller på kartan. Titta närmare på kartan och flytta den runt genom att använda zoomnings kontrollerna i det nedre högra hörnet. Du kan ställa in zoomnings nivån och passa kartan på sidans storlek.  

## <a name="connection-metrics"></a>Anslutnings mått
Fönstret **anslutningar** visar standard mått för den valda anslutningen från den virtuella datorn via TCP-porten. Måtten omfattar svars tid, begär Anden per minut, trafik data flöde och länkar.  

![Diagram över nätverks anslutning i fönstret anslutningar](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Misslyckade anslutningar
Kartan visar misslyckade anslutningar för processer och datorer. En streckad röd linje indikerar att ett klient system inte kan komma åt en process eller port. För system som använder beroende agenten rapporterar agenten om misslyckade anslutnings försök. Kart funktionen övervakar en process genom att kontrol lera TCP-socketar som inte upprättar en anslutning. Det här felet kan bero på en brand vägg, en felaktig konfiguration på klienten eller servern eller en fjärran sluten tjänst som inte är tillgänglig.

![En misslyckad anslutning på kartan](./media/vminsights-maps/map-group-failed-connection-01.png)

Att förstå misslyckade anslutningar kan hjälpa dig att felsöka, verifiera migrering, analysera säkerhet och förstå den övergripande arkitekturen för tjänsten. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta på ett problem. Anslutningar kan till exempel Miss Miss inträffat, till exempel när en växlings miljö plötsligt blir otillgänglig eller när två program nivåer inte kan kommunicera med varandra efter en molnbaserad migrering.

### <a name="client-groups"></a>Klient grupper
På kartan representerar klient grupper klient datorer som ansluter till den mappade datorn. En enda klient grupp representerar klienterna för en enskild process eller dator.

![En klient grupp på kartan](./media/vminsights-maps/map-group-client-groups-01.png)

Välj gruppen om du vill se de övervakade klienterna och IP-adresserna för systemen i en klient grupp. Gruppens innehåll visas nedan.  

![En klient grupps lista med IP-adresser på kartan](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Om gruppen innehåller övervakade och oövervakade klienter kan du filtrera klienterna genom att välja lämpligt avsnitt i gruppens ring diagram.

### <a name="server-port-groups"></a>Server-Port grupper
Server Port grupper representerar portar på servrar som har inkommande anslutningar från den mappade datorn. Gruppen innehåller Server porten och antalet servrar som har anslutningar till den porten. Välj gruppen om du vill se enskilda servrar och anslutningar. 

![En server port grupp på kartan](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Om gruppen innehåller övervakade och oövervakade servrar kan du filtrera servrarna genom att välja lämpligt avsnitt i gruppens ring diagram.

## <a name="view-a-map-from-a-vm"></a>Visa en karta från en virtuell dator 

Få åtkomst till VM Insights direkt från en virtuell dator:

1. I Azure Portal väljer du **Virtual Machines**. 
2. Välj en virtuell dator i listan. I avsnittet **övervakning** väljer du **insikter**.  
3. Välj fliken **karta** .

Kartan visualiserar de virtuella datorernas beroenden genom att identifiera kör process grupper och processer som har aktiva nätverks anslutningar under ett visst tidsintervall.  

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden som såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Om du vill köra frågan använder du **TimeRange** -väljaren i det övre vänstra hörnet. Du kan köra en fråga, till exempel under en incident eller för att se status innan en ändring.  

![Skärm bild av fliken karta i avsnittet övervaknings insikter i Azure Portal visar ett diagram över beroenden mellan virtuella datorer.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visa en karta från en skalnings uppsättning för virtuella datorer

Få åtkomst till VM Insights direkt från en skalnings uppsättning för virtuella datorer:

1. I Azure Portal väljer du **skalnings uppsättningar för virtuella datorer**.
2. Välj en virtuell dator i listan. I avsnittet **övervakning** väljer du **insikter**.  
3. Välj fliken **karta** .

Kartan visualiserar alla instanser i skalnings uppsättningen som en gruppnod tillsammans med gruppens beroenden. Den expanderade noden listar instanserna i skalnings uppsättningen. Du kan bläddra igenom instanserna 10 i taget. 

Om du vill läsa in en karta för en speciell instans väljer du först den instansen på kartan. Välj sedan knappen med **tre punkter** (...) till höger och välj **Läs in Server karta**. I kartan som visas ser du process grupper och processer som har aktiva nätverks anslutningar under ett angivet tidsintervall. 

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden som såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Använd **TimeRange** -väljaren för att köra frågan. Du kan köra en fråga, till exempel under en incident eller för att se status innan en ändring.

![Skärm bild av fliken karta i avsnittet övervaknings insikter i Azure Portal visar ett diagram över beroenden mellan virtuella datorers skalnings uppsättningar.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Du kan också komma åt en karta för en angiven instans från vyn **instanser** för skalnings uppsättningen för den virtuella datorn. I avsnittet **Inställningar** , gå till **instans**  >  **insikter**.

## <a name="view-a-map-from-azure-monitor"></a>Visa en karta från Azure Monitor

I Azure Monitor tillhandahåller Map-funktionen en global vy över dina virtuella datorer och deras beroenden. För att få åtkomst till Map-funktionen i Azure Monitor:

1. I Azure Portal väljer du **övervaka**. 
2. I avsnittet **insikter** väljer du **Virtual Machines**.
3. Välj fliken **karta** .

   ![Översikt över Azure Monitor översikt över flera virtuella datorer](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Välj en arbets yta genom att använda **arbets ytans** väljare överst på sidan. Om du har fler än en arbets yta för Log Analytics väljer du den arbets yta som är aktive rad med lösningen och som har virtuella datorer som rapporterar till den. 

**Grupp** väljaren returnerar prenumerationer, resurs grupper, [dator grupper](../logs/computer-groups.md)och virtuella datorers skalnings uppsättningar för datorer som är relaterade till den valda arbets ytan. Ditt val gäller endast för kart funktionen och kan inte överföras till prestanda eller hälsa.

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden som såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Använd **TimeRange** -väljaren för att köra frågan. Du kan köra en fråga, till exempel under en incident eller för att se status innan en ändring.  

## <a name="next-steps"></a>Nästa steg

Om du vill identifiera Flask halsar, kontrol lera prestanda och förstå den övergripande användningen av dina virtuella datorer, se [Visa prestanda status för VM-insikter](vminsights-performance.md).
