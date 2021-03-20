---
title: Flytta virtuella Azure-datorer till en annan region med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93398070"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Flytta virtuella Azure-datorer till en annan Azure-region

Du kanske vill flytta virtuella IaaS-datorer (Azure Infrastructure as a Service) från en region till en annan för att förbättra tillförlitlighet, tillgänglighet, hantering och styrning. Den här självstudien visar hur du flyttar virtuella datorer till en annan region med hjälp av Azure Site Recovery. Du lär dig följande:

> [!div class="checklist"]
> * Verifiera kraven
> * Förbereda källregionens virtuella datorer
> * Förbereda målregionen
> * Kopiera data till målregionen
> * Testa konfigurationen
> * Utföra flytten
> * Ta bort resurserna från käll regionen


> [!IMPORTANT]
> Den här artikeln beskriver hur du flyttar virtuella Azure-datorer från en region till *en annan.* Om målet är att förbättra tillgängligheten för din infrastruktur genom att flytta virtuella datorer till tillgänglighets zoner kan du läsa mer i [flytta virtuella Azure-datorer till Tillgänglighetszoner](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att du har virtuella Azure-datorer i Azure-regionen som du vill flytta *från*.
- Kontrol lera att ditt val av [käll region – kombination av mål region stöds](./azure-to-azure-support-matrix.md#region-support)och välj sedan noga mål regionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera konto behörigheter. Om du precis har skapat ditt kostnads fria Azure-konto är *du* administratör för din prenumeration. Om du inte är administratör kan du arbeta med administratören för att få de behörigheter som du behöver:
  -  Om du vill aktivera replikering för en virtuell dator och kopiera data till målet genom att använda Site Recovery måste du ha behörighet att skapa en virtuell dator i dina Azure-resurser. Den inbyggda rollen Virtuell datordeltagare har dessa behörigheter. Med behörigheterna kan du:
        - Skapa en virtuell dator i den valda resursgruppen.
        - Skapa en virtuell dator i det valda virtuella nätverket.
        - Skriva till det valda lagringskontot.

  - Du måste också ha behörighet för att hantera Site Recovery åtgärder. Rollen Site Recovery Contributor har alla behörigheter som krävs för att hantera Site Recovery åtgärder i ett Azure Recovery Services-valv.

## <a name="prepare-the-source-vms"></a>Förbereda källregionens virtuella datorer

1. Kontrol lera att de virtuella Azure-datorerna som du planerar att flytta har de senaste rot certifikaten. Om de inte gör det kan du inte aktivera data kopiering till mål regionen på grund av säkerhets begränsningar.

    - För virtuella Windows-datorer installerar du de senaste Windows-uppdateringarna så att alla betrodda rot certifikat finns på datorn. I en frånkopplad miljö följer du standard Windows Update och certifikat uppdaterings processer för din organisation.
    - För virtuella Linux-datorer följer du anvisningarna från Linux-distributören för att få de senaste betrodda rot certifikaten och listan över återkallade certifikat.
2. Se till att du inte använder en autentiseringsprovider för att kontrol lera nätverks anslutningen för virtuella datorer som du planerar att flytta.
3. Om en virtuell dator som du vill flytta inte har åtkomst till Internet och använder en brand Väggs-proxy för att kontrol lera utgående åtkomst, kontrollerar du [kraven](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Dokumentera layouten för käll nätverket och alla resurser som du använder, inklusive (men inte begränsat till) belastnings utjämning, nätverks säkerhets grupper och offentliga IP-adresser för verifiering.

## <a name="prepare-the-target-region"></a>Förbereda målregionen

1. I din Azure-prenumeration kontrollerar du att du kan skapa virtuella datorer i mål regionen som används för haveri beredskap. Kontakta supporten om du vill aktivera den begärda kvoten om det behövs.

2. Kontrol lera att din prenumeration har tillräckligt med resurser för att stödja dina virtuella käll datorer. Om du använder Site Recovery för att kopiera data till målet, används samma storlek eller närmaste tillgängliga storlek för de virtuella mål datorerna.

3. Se till att du skapar en mål resurs för varje komponent som du har identifierat i layouten för käll nätverket. Detta säkerställer att de virtuella datorerna har alla funktioner och funktioner i mål regionen som de hade i käll regionen.

   Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk och ett lagrings konto när du aktiverar replikering för den virtuella käll datorn. Du kan också skapa dessa resurser i förväg och tilldela dem till den virtuella datorn som en del av steget aktivera-replikering. Men du måste manuellt skapa andra resurser i mål regionen. Se följande dokument för att skapa de vanligaste nätverks resurserna baserat på din virtuella käll dator konfiguration:

   - [Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md)
   - [Lastbalanserare](../load-balancer/index.yml)
   - [Offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)
    
   För alla andra nätverks komponenter, se [dokumentationen om Azure-nätverk](../index.yml?pivot=products&panel=network). 

4. Om du vill testa konfigurationen innan du utför flytten kan du [skapa ett nätverk för icke-produktion](../virtual-network/quick-create-portal.md) manuellt i mål regionen. Testning av installations programmet skapar minimal störning i produktions miljön och vi rekommenderar det.
    
## <a name="copy-data-to-the-target-region"></a>Kopiera data till målregionen
I följande steg används Azure Site Recovery för att kopiera data till mål regionen.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Skapa valvet i valfri region förutom källan

1. Logga in på [Azure Portal](https://portal.azure.com)  >  **Recovery Services**.
2. Välj **skapa ett resurs**  >  **hanterings verktyg**  >  **säkerhets kopiering och Site Recovery**.
3. I **namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
4. Skapa en resursgrupp med namnet **ContosoRG**.
5. Ange en Azure-region. Information om hur du kontrollerar regioner som stöds finns [Azure Site Recovery pris information](https://azure.microsoft.com/pricing/details/site-recovery/).
6. För Recovery Services valv väljer du **Översikt**  >  **ConsotoVMVault**  >  **+ Replikera**.
7. För **källa** väljer du **Azure**.
8. För **käll plats** väljer du den Azure-region där de virtuella datorerna körs för närvarande.
9. Välj Azure Resource Manager distributions modell. Välj sedan **käll prenumerationen** och **käll resurs gruppen**.
10. Spara inställningarna genom att klicka på **OK** .

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella Azure-datorer och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorer som är associerade med prenumerationen och resurs gruppen.

1. Välj den virtuella dator som du vill flytta och välj sedan **OK**.
2. För **Inställningar** väljer du **haveri beredskap**.
3. För **Konfigurera**  >  **mål region** för haveri beredskap väljer du den mål region som du replikerar till.
4. Välj att använda standard mål resurserna eller de som du skapade i förväg.
5. Välj **Aktivera replikering** för att starta jobbet.


 

## <a name="test-the-configuration"></a>Testa konfigurationen


1. Gå till valvet. I **Inställningar**  >  **replikerade objekt** väljer du den virtuella dator som du vill flytta till mål regionen. Välj sedan **testa redundans**.
2. I **testa redundans** väljer du en återställnings punkt som ska användas för redundansväxlingen:

   - **Senaste bearbetade**: Redundansväxlar den virtuella datorn till den senaste återställningspunkten som bearbetades av Site Recovery-tjänsten. Tidsstämpeln visas. Ingen tid för bearbetning av data, så det här alternativet ger ett litet återställnings tids mål (RTO).
   - **Senaste program – konsekvent**: växlar över alla virtuella datorer till den senaste programkonsekventa återställnings punkten. Tidsstämpeln visas.
   - **Anpassad**: Välj annan återställningspunkt.

3. Välj målets virtuella Azure-nätverk, dit du vill flytta de virtuella Azure-datorerna för att testa konfigurationen.

   > [!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för redundanstest, inte i produktions nätverket i mål regionen.

4. Börja testa flyttningen genom att välja **OK**. Om du vill spåra förloppet väljer du den virtuella datorn för att öppna dess **Egenskaper.** Eller Välj jobbet **testa redundans** i valvet. Välj sedan **Inställningar**  >  **jobb**  >  **Site Recovery jobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn körs med rätt storlek och ansluten till lämpligt nätverk.
6. Om du vill ta bort den virtuella datorn som du skapade för testning väljer du **rensning** av redundanstest på det replikerade objektet. Anteckna **och** spara eventuella observationer som rör testet.

## <a name="perform-the-move-and-confirm"></a>Utföra flytt och bekräfta

1. Gå till valvet i **Inställningar**  >  **replikerade objekt**, Välj den virtuella datorn och välj sedan **redundans**.
1. Välj **senaste** för **redundans**. 
2. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella käll datorn innan redundansväxlingen utlöses. Men redundansväxlingen fortsätter även om avstängningen Miss lyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
3. När jobbet är klart kontrollerar du att den virtuella datorn visas i Azure-regionen som förväntat.
4. I **replikerade objekt** högerklickar du på den virtuella datorn och väljer **genomför**. Detta avslutar flyttningen. Vänta tills commit-jobbet har slutförts.

## <a name="discard-the-resources-from-the-source-region"></a>Ta bort resurserna från käll regionen

- Gå till den virtuella datorn och välj **inaktivera replikering**. Detta stoppar processen med att kopiera data för den virtuella datorn.

  > [!IMPORTANT]
  > Slutför det här steget för att undvika att debiteras för Site Recovery replikering efter flytten.

Om du inte planerar att återanvända någon av käll resurserna följer du dessa steg:

1. Ta bort alla relevanta nätverks resurser i käll regionen som du angav i steg 4 i [förbereda de virtuella käll datorerna](#prepare-the-source-vms).
2. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du flyttar virtuella Azure-datorer till en annan Azure-region. Nu kan du konfigurera haveri beredskap för de virtuella datorerna.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
