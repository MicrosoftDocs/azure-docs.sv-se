---
title: Konfigurera katastrof återställning för en IIS-webbapp med hjälp av Azure Site Recovery
description: Lär dig hur du replikerar virtuella datorer i IIS-webbservergruppen med hjälp av Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 56ac58e47bffc73c7079af043ad567a77e8f3323
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735513"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Konfigurera haveriberedskap för en IIS-baserad webbapp med flera nivåer

Program vara är motorn för affärs produktivitet i en organisation. Olika webb program kan hantera olika syfte i en organisation. Vissa program, t. ex. program som används för löne bearbetning, finansiella program och kund riktade webbplatser, kan vara kritiska för en organisation. För att förhindra att produktiviteten går förlorad är det viktigt för organisationen att köra dessa program kontinuerligt. Mer viktigt, som gör att dessa program ständigt är tillgängliga kan hjälpa till att förhindra skada på varumärket eller avbildningen av organisationen.

Kritiska webb program konfigureras vanligt vis som program på flera nivåer: webb, databas och program finns på olika nivåer. Förutom att spridas mellan olika nivåer kan programmen också använda flera servrar på varje nivå för att belastningsutjämna trafiken. Dessutom kan mappningar mellan olika nivåer och webb servern baseras på statiska IP-adresser. Vid redundans måste vissa av dessa mappningar uppdateras, särskilt om flera webbplatser är konfigurerade på webb servern. Om webb program använder TLS måste du uppdatera certifikat bindningar.

Traditionella återställnings metoder som inte baseras på replikering innebär säkerhets kopiering av olika konfigurationsfiler, register inställningar, bindningar, anpassade komponenter (COM eller .NET), innehåll och certifikat. Filerna återställs via en uppsättning manuella steg. Traditionella återställnings metoder för att säkerhetskopiera och manuellt återställa filer är besvärlig, fel känsliga och inte skalbara. Du kan till exempel lätt glömma att säkerhetskopiera certifikat. Efter redundansväxlingen är du kvar utan val utan att köpa nya certifikat för servern.

En felfri lösning för haveri beredskap stöder modellerings återställnings planer för komplexa program arkitekturer. Du bör också kunna lägga till anpassade steg i återställnings planen för att hantera program mappningar mellan nivåer. Om det finns en katastrof ger program mappningar en enkel klicknings-, en säker, säker lösning som hjälper till att leda till en lägre RTO.

Den här artikeln beskriver hur du skyddar ett webb program som baseras på Internet Information Services (IIS) med hjälp av [Azure Site Recovery](site-recovery-overview.md). Artikeln beskriver metod tips för att replikera ett IIS-baserat webb program med tre nivåer till Azure, hur du gör en haveri beredskap och hur du växlar över programmet till Azure.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att du vet hur du gör följande uppgifter:

* [Replikera en virtuell dator till Azure](vmware-azure-tutorial.md)
* [Utforma ett återställnings nätverk](./concepts-on-premises-to-azure-networking.md)
* [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
* [Gör en redundansväxling till Azure](site-recovery-failover.md)
* [Replikera en domänkontrollant](site-recovery-active-directory.md)
* [Replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Distributions mönster
Ett IIS-baserat webb program följer vanligt vis ett av följande distributions mönster:

**Distributions mönster 1**

En IIS-baserad webb Server grupp med konsol för programbegäran (ARR), en IIS-server och SQL Server.

![Diagram över en IIS-baserad webb grupp som har tre nivåer](./media/site-recovery-iis/deployment-pattern1.png)

**Distributions mönster 2**

En IIS-baserad webb Server grupp med ARR, en IIS-server, en program Server och SQL Server.

![Diagram över en IIS-baserad webb grupp som har fyra nivåer](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery-stöd

I exemplen i den här artikeln använder vi virtuella VMware-datorer med IIS 7,5 på Windows Server 2012 R2 Enterprise. Eftersom Site Recovery replikering inte är programspecifikt förväntas rekommendationerna i den här artikeln gälla i de scenarier som anges i följande tabell och för olika versioner av IIS.

### <a name="source-and-target"></a>Källa och mål

Scenario | Till en sekundär plats | Till Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysisk server | Inga | Ja
Azure|Ej tillämpligt|Ja

## <a name="replicate-virtual-machines"></a>Replikera virtuella datorer

Om du vill starta replikeringen av alla virtuella IIS-webbservergrupper till Azure, följer du anvisningarna i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

Om du använder en statisk IP-adress kan du ange den IP-adress som du vill att den virtuella datorn ska ta. Ange IP-adressen genom att gå till mål-IP för **beräknings-och nätverks inställningar**  >  .

![Skärm bild som visar hur du ställer in mål-IP i fönstret Site Recovery beräkning och nätverk](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan
En återställnings plan stöder sekvenseringen av olika nivåer i ett program med flera nivåer under en redundansväxling. Sekvenseringen bidrar till att upprätthålla programmets konsekvens. När du skapar en återställnings plan för ett webb program med flera nivåer slutför du stegen som beskrivs i [skapa en återställnings plan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Lägg till virtuella datorer i grupper för redundans
Ett typiskt IIS-webbprogram med flera nivåer består av följande komponenter:
* En databas nivå som har virtuella SQL-datorer.
* Webb nivån, som består av en IIS-server och en program nivå. 

Lägg till virtuella datorer i olika grupper baserat på nivån:

1. Skapa en återställnings plan. Lägg till de virtuella datorerna på databas nivån under grupp 1. Detta säkerställer att virtuella datorer i databas nivån stängs av senast och hämtas först.
1. Lägg till de virtuella datorerna på program nivån i grupp 2. Detta säkerställer att virtuella datorer på program nivå hämtas efter att databas nivån har upprättats.
1. Lägg till de virtuella datorerna på webb nivån i grupp 3. Detta säkerställer att virtuella datorer på virtuella datorer hämtas när program nivån har upprättats.
1. Lägg till belastnings utjämning för virtuella datorer i grupp 4. Detta säkerställer att de virtuella datorerna för belastnings utjämning skapas när webb nivån har upprättats.

Mer information finns i [Anpassa återställnings planen](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Lägg till ett skript i återställnings planen
För att IIS-webbgruppen ska fungera korrekt kan du behöva utföra några åtgärder på de virtuella Azure-datorerna efter redundansväxlingen eller under ett redundanstest. Du kan automatisera vissa åtgärder efter redundans. Du kan till exempel uppdatera DNS-posten, ändra en webbplats bindning eller ändra en anslutnings sträng genom att lägga till motsvarande skript i återställnings planen. [Lägg till ett VMM-skript i en återställnings plan](./hyper-v-vmm-recovery-script.md) beskriver hur du ställer in automatiserade uppgifter med hjälp av ett skript.

#### <a name="dns-update"></a>DNS-uppdatering
Om DNS har kon figurer ATS för dynamisk DNS-uppdatering, uppdaterar virtuella datorer vanligt vis DNS med den nya IP-adressen när de startar. Om du vill lägga till ett explicit steg för att uppdatera DNS med de nya IP-adresserna för de virtuella datorerna lägger du till ett [skript för att uppdatera IP-adresser i DNS](https://aka.ms/asr-dns-update) som en åtgärd efter redundansväxlingen i återställnings Plans grupper.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Anslutnings sträng i ett programs web.config
Anslutnings strängen anger den databas som webbplatsen kommunicerar med. Om anslutnings strängen innehåller namnet på den virtuella databas datorn behövs inga ytterligare steg efter redundansväxlingen. Programmet kan kommunicera automatiskt med-databasen. Om IP-adressen för den virtuella databas datorn bevaras, behöver den inte heller uppdatera anslutnings strängen. 

Om anslutnings strängen refererar till den virtuella databas datorn genom att använda en IP-adress måste den uppdateras efter redundansväxlingen. Följande anslutnings sträng pekar till exempel på databasen med IP-127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

Om du vill uppdatera anslutnings strängen på webb nivån lägger du till ett [IIS-anslutnings uppdaterings skript](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) efter grupp 3 i återställnings planen.

#### <a name="site-bindings-for-the-application"></a>Webbplats bindningar för programmet
Varje plats består av bindnings information. Bindnings informationen omfattar typen av bindning, IP-adressen som IIS-servern lyssnar på efter begär Anden för platsen, Port numret och värd namnen för platsen. Under redundansväxlingen kan du behöva uppdatera dessa bindningar om det finns en ändring i IP-adressen som är kopplad till dem.

> [!NOTE]
>
> Om du anger plats bindningen till **Alla otilldelade** behöver du inte uppdatera den här bindningen efter redundans. Om IP-adressen som är kopplad till en plats inte har ändrats efter redundansväxlingen, behöver du inte uppdatera plats bindningen. (Kvarhållning av IP-adressen beror på nätverks arkitekturen och undernät som har tilldelats till primära platser och återställnings platser. Det kanske inte är möjligt att uppdatera dem i din organisation.)

![Skärm bild som visar inställningen TLS/SSL-bindning](./media/site-recovery-iis/sslbinding.png)

Om du har associerat IP-adressen med en plats uppdaterar du alla webbplats bindningar med den nya IP-adressen. Om du vill ändra plats bindningarna lägger du till ett [uppdaterings skript för IIS-webbnivån](/samples/browse/?redirectedfrom=TechNet-Gallery) efter grupp 3 i återställnings planen.

#### <a name="update-the-load-balancer-ip-address"></a>Uppdatera IP-adressen för belastningsutjämnaren
Om du har en virtuell ARR-dator för att uppdatera IP-adressen lägger du till ett [IIS arr-skript för växling vid fel](/samples/browse/?redirectedfrom=TechNet-Gallery) efter grupp 4.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>TLS/SSL-certifikat bindning för en HTTPS-anslutning
En webbplats kan ha ett tillhör ande TLS/SSL-certifikat som säkerställer en säker kommunikation mellan webb servern och användarens webbläsare. Om webbplatsen har en HTTPS-anslutning och även har en associerad HTTPS-plats bindning till IIS-serverns IP-adress med en TLS/SSL-certifikat bindning, måste du lägga till en ny plats bindning för certifikatet med IP-adressen för den virtuella IIS-datorn efter redundans.

TLS/SSL-certifikatet kan utfärdas mot dessa komponenter:

* Det fullständigt kvalificerade domän namnet för webbplatsen.
* Namnet på servern.
* Ett certifikat med jokertecken för domän namnet.  
* En IP-adress. Om TLS/SSL-certifikatet utfärdas mot IIS-serverns IP-adress måste ett annat TLS/SSL-certifikat utfärdas mot IP-adressen för IIS-servern på Azure-webbplatsen. En ytterligare TLS-bindning för det här certifikatet måste skapas. Därför rekommenderar vi inte att du använder ett TLS/SSL-certifikat som utfärdats mot IP-adressen. Det här alternativet används inte längre och kommer snart att bli inaktuellt i enlighet med nya certifikat utfärdare/webb läsar forum ändringar.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Uppdatera beroendet mellan webb nivån och program nivån
Om du har ett programspecifikt beroende baserat på IP-adressen för de virtuella datorerna måste du uppdatera beroendet efter redundansväxlingen.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

1. I Azure Portal väljer du Recovery Services valvet.
2. Välj den återställnings plan som du skapade för IIS-webbservergruppen.
3. Välj **Testa redundans**.
4. Starta processen för redundanstest genom att välja återställnings punkten och det virtuella Azure-nätverket.
5. När den sekundära miljön är upp kan du utföra verifieringar.
6. När verifieringen är klar kan du rensa redundanstestningen genom att välja **valideringar har slutförts**.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1. I Azure Portal väljer du Recovery Services valvet.
1. Välj den återställnings plan som du skapade för IIS-webbservergruppen.
1. Välj **Redundans**.
1. Starta redundansväxlingen genom att välja återställnings punkten.

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om att [Replikera andra program](site-recovery-workload.md) med hjälp av Site Recovery.