---
title: Flytta virtuella datorer till en Azure-region med tillgänglighets zoner med hjälp av Azure Site Recovery
description: Lär dig hur du flyttar virtuella datorer till en tillgänglighets zon i en annan region med Site Recovery
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93393140"
---
# <a name="move-azure-vms-into-availability-zones"></a>Flytta virtuella Azure-datorer till tillgänglighetszoner

I den här artikeln beskrivs hur du flyttar virtuella Azure-datorer till en tillgänglighets zon i en annan region. [Läs den här artikeln](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)om du vill flytta till en annan zon i samma region.


Tillgänglighetszoner i Azure hjälper dig att skydda dina program och data från data Center fel. Varje tillgänglighetszon består av ett eller flera datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska separeringen av Tillgänglighetszoner inom en region hjälper till att skydda program och data från data Center problem. Med Tillgänglighetszoner erbjuder Azure ett service avtal (SLA) på 99,99% för drift tid för virtuella datorer (VM). Tillgänglighetszoner stöds i SELECT-regioner, som anges i [regioner som stöder Tillgänglighetszoner](../availability-zones/az-region.md).

I ett scenario där de virtuella datorerna distribueras som *en enda instans* till en viss region och du vill förbättra tillgängligheten genom att flytta dessa virtuella datorer till en tillgänglighets zon kan du göra det med hjälp av Azure Site Recovery. Den här åtgärden kan delas in ytterligare i:

- Flytta virtuella datorer med enstaka instans till Tillgänglighetszoner i en mål region
- Flytta virtuella datorer i en tillgänglighets uppsättning till Tillgänglighetszoner i en mål region

> [!IMPORTANT]
> För att flytta virtuella Azure-datorer till en tillgänglighets zon i en annan region rekommenderar vi nu att du använder [Azure Resource-arbetskraft](../resource-mover/move-region-availability-zone.md). Resurs förflyttning är i offentlig för hands version och ger:
> - En enda hubb för att flytta resurser mellan regioner.
> - Minskad flytt tid och komplexitet. Allt du behöver finns på en enda plats.
> - En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
> - Ett enkelt sätt att identifiera beroenden för resurser som du vill flytta. Detta hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i mål regionen efter flytten.
> - Automatisk rensning av resurser i käll regionen, om du vill ta bort dem efter flytten.
> - Prestandatester. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.



## <a name="check-prerequisites"></a>Kontrollera krav

- Kontrol lera om mål regionen har [stöd för Tillgänglighetszoner](../availability-zones/az-region.md). Kontrol lera att valet av [kombination av käll region/mål region stöds](./azure-to-azure-support-matrix.md#region-support). Fatta ett välgrundat beslut i mål regionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Kontrol lera konto behörigheter. Om du precis har skapat ditt kostnads fria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerations administratör arbetar du med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och slutligen kopiera data till målet med hjälp av Azure Site Recovery, måste du ha:

    1. Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen *virtuell dator deltagare* har följande behörigheter, bland annat:
        - Behörighet att skapa en virtuell dator i den valda resursgruppen
        - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
        - Behörighet att skriva till det valda lagringskontot

    2. Behörighet att hantera Azure Site Recovery uppgifter. Rollen *Site Recovery Contributor* har alla behörigheter som krävs för att hantera Site Recovery åtgärder i ett Recovery Services valv.

## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

1. De virtuella datorerna bör använda hanterade diskar om du vill flytta dem till en tillgänglighets zon med hjälp av Site Recovery. Du kan konvertera befintliga virtuella Windows-datorer som använder ohanterade diskar för att använda hanterade diskar. Följ stegen på [konvertera en virtuell Windows-dator från ohanterade diskar till hanterade diskar](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md). Se till att tillgänglighets uppsättningen är konfigurerad som *hanterad*.
2. Kontrollera att alla de senaste rotcertifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rot certifikaten inte finns kan inte data kopian till mål regionen aktive ras på grund av säkerhets begränsningar.

3. I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö följer du standard processerna för Windows Update och certifikat uppdatering för din organisation.

4. För virtuella Linux-datorer följer du rikt linjerna från Linux-distributören för att få de senaste betrodda rot certifikaten och listan över återkallade certifikat på den virtuella datorn.
5. Se till att du inte använder en autentiseringsprovider för att kontrol lera nätverks anslutningen för virtuella datorer som du vill flytta.

6. Kontrol lera de [utgående anslutnings kraven för virtuella datorer](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

7. Identifiera käll nätverkets layout och de resurser som du för närvarande använder för verifiering, inklusive belastningsutjämnare, NSG: er och offentlig IP-adress.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. Kontrol lera att din Azure-prenumeration låter dig skapa virtuella datorer i mål regionen som används för haveri beredskap. Om det behövs kan du kontakta supporten för att aktivera den begärda kvoten.

2. Se till att din prenumeration har tillräckligt med resurser för att hantera virtuella datorer med de storlekar som de virtuella källdatorerna har. Om du använder Site Recovery för att kopiera data till målet, används samma storlek eller närmsta möjliga storlek för den virtuella mål datorn.

3. Skapa en mål resurs för varje komponent som identifieras i käll nätverkets layout. Den här åtgärden säkerställer att när du har klippt till mål regionen har dina virtuella datorer alla funktioner och funktioner som du hade i källan.

    > [!NOTE]
    > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk och ett lagrings konto när du aktiverar replikering för den virtuella käll datorn. Du kan också skapa dessa resurser i förväg och tilldela den virtuella datorn som en del av steget aktivera replikering. Men för andra resurser, som nämnts senare, måste du skapa dem manuellt i mål regionen.

     Följande dokument beskriver hur du skapar de vanligaste nätverks resurserna som är relevanta för dig, baserat på konfigurationen av den virtuella käll datorn.

    - [Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md)
    - [Lastbalanserare](../load-balancer/index.yml)
    - [Offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)
    
   Information om andra nätverks komponenter finns i [dokumentationen](../index.yml?pivot=products&panel=network)till nätverket.

    > [!IMPORTANT]
    > Se till att du använder en zon-redundant belastningsutjämnare i målet. Du kan läsa mer på [standard Load Balancer och Tillgänglighetszoner](../load-balancer/load-balancer-standard-availability-zones.md).

4. [Skapa ett nätverk för icke-produktion](../virtual-network/quick-create-portal.md) manuellt i mål regionen om du vill testa konfigurationen innan du klipper över till mål regionen. Vi rekommenderar den här metoden eftersom den orsakar minimal störning i produktions miljön.

## <a name="enable-replication"></a>Aktivera replikering
Följande steg vägleder dig när du använder Azure Site Recovery för att aktivera replikering av data till mål regionen, innan du flyttar dem till Tillgänglighetszoner.

> [!NOTE]
> De här stegen är för en enskild virtuell dator. Du kan utöka samma till flera virtuella datorer. Gå till Recovery Services-valvet, Välj **+ Replikera** och välj relevanta virtuella datorer tillsammans.

1. I Azure Portal väljer du **virtuella datorer** och väljer den virtuella dator som du vill flytta till Tillgänglighetszoner.
2. I **Åtgärder** väljer du **Haveriberedskap**.
3. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion du ska replikera till. Se till att den här regionen [stöder](../availability-zones/az-region.md) Tillgänglighetszoner.
4. Välj **Nästa: avancerade inställningar**.
5. Välj lämpliga värden för mål prenumerationen, resurs gruppen för den virtuella mål datorn och det virtuella nätverket.
6. I avsnittet **tillgänglighet** väljer du den tillgänglighets zon som du vill flytta den virtuella datorn till. 
   > [!NOTE]
   > Om du inte ser alternativet för tillgänglighets uppsättning eller tillgänglighets-zonen kontrollerar du att [kraven](#prepare-the-source-vms) är uppfyllda och att [förberedelsen](#prepare-the-source-vms) av de virtuella käll datorerna är slutförd.
  

7. Välj **Aktivera replikering**. Den här åtgärden startar ett jobb för att aktivera replikering för den virtuella datorn.

## <a name="check-settings"></a>Kontrol lera inställningar

När replikeringen har slutförts kan du kontrollera replikeringsstatus, ändra inställningar för replikering och testa distributionen.

1. I den virtuella datormenyn väljer du **Haveriberedskap**.
2. Du kan kontrol lera replikeringsstatus, återställnings punkter som har skapats och källa och mål regioner på kartan.


## <a name="test-the-configuration"></a>Testa konfigurationen

1. I menyn virtuell dator väljer du  **haveri beredskap**.
2. Välj ikonen **testa redundans** .
3. I **testa redundans** väljer du en återställnings punkt som ska användas för redundansväxlingen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data så den ger ett lågt mål för återställningstid (RTO).
   - **Senaste app-konsekventa**: Det här alternativet redundansväxlar alla virtuella datorer till den senaste app-konsekventa återställningspunkten. Tidsstämpeln visas.
   - **Anpassad**: Välj annan återställningspunkt.

3. Välj målets virtuella Azure-testnätverk som du vill flytta de virtuella Azure-datorerna till för att testa konfigurationen. 

    > [!IMPORTANT]
    > Vi rekommenderar att du använder ett separat Azure VM-nätverk för test felen och inte produktions nätverket i mål regionen dit du vill flytta dina virtuella datorer.

4. Börja testa flyttningen genom att välja **OK**. Om du vill spåra förloppet väljer du den virtuella datorn för att öppna dess egenskaper. Alternativt kan du välja jobbet **testa redundans** i valv namnet > **Inställningar**  >  **jobb**  >  **Site Recovery jobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn som skapats som en del i testningen av flyttningen väljer du **rensning** av redundanstest på det replikerade objektet. I **Kommentarer** skriver du ned och sparar eventuella observationer från testet.

## <a name="move-to-the-target-region-and-confirm"></a>Flytta till mål regionen och bekräfta

1.  I menyn virtuell dator väljer du  **haveri beredskap**.
2. Välj ikonen för **redundans** .
3. I **Redundans** väljer du **Senaste**. 
4. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan. 
5. När jobbet är klart kontrollerar du att den virtuella datorn visas i Azure-regionen som förväntat.
6. I **Replikerade objekt** högerklickar du på den virtuella datorn > **Genomför**. Detta avslutar flytten till målregionen. Vänta tills inchecknings jobbet är klart.

## <a name="discard-the-resource-in-the-source-region"></a>Ta bort resursen från källregionen

Gå till den virtuella datorn. Välj **inaktivera replikering**. Den här åtgärden avbryter processen med att kopiera data för den virtuella datorn.  

> [!IMPORTANT]
> Gör föregående steg för att undvika att debiteras för Site Recovery replikering efter flytten. Källans replikeringsinställningar rensas automatiskt. Observera att Site Recovery-tillägget som installeras som en del av replikeringen inte tas bort och måste tas bort manuellt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du ökat tillgängligheten för en virtuell Azure-dator genom att flytta till en tillgänglighets uppsättning eller tillgänglighets zon. Nu kan du ange haveri beredskap för den flyttade virtuella datorn.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
