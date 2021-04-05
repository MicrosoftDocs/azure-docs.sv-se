---
title: Flytta virtuella Azure-datorer till en annan Azure-region med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella Azure-datorer från en Azure-region till en annan.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394840"
---
# <a name="move-vms-to-another-azure-region"></a>Flytta virtuella datorer till en annan Azure-region

Det finns scenarier där du vill flytta dina befintliga virtuella Azure IaaS-datorer (VM) från en region till en annan. Du vill till exempel förbättra tillförlitligheten och tillgängligheten för dina befintliga virtuella datorer, för att förbättra hanterbarheten eller för att kunna flytta av styrnings orsaker. Mer information finns i [Översikt över Azure VM-flyttning](azure-to-azure-move-overview.md). 

Du kan använda [Azure Site Recovery](site-recovery-overview.md) tjänst för att flytta virtuella Azure-datorer till en sekundär region.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> 
> * Verifiera krav för flytten
> * Förbered de virtuella käll datorerna och mål regionen
> * Kopiera data och aktivera replikering
> * Testa konfigurationen och genomför flytten
> * Ta bort resurserna i käll regionen


> [!IMPORTANT]
> För att flytta virtuella Azure-datorer till en annan region rekommenderar vi nu att du använder [Azure Resource-arbetskraft](../resource-mover/tutorial-move-region-virtual-machines.md). Resurs förflyttning är i offentlig för hands version och ger:
> - En enda hubb för att flytta resurser mellan regioner.
> - Minskad flytt tid och komplexitet. Allt du behöver finns på en enda plats.
> - En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
> - Ett enkelt sätt att identifiera beroenden för resurser som du vill flytta. Detta hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i mål regionen efter flytten.
> - Automatisk rensning av resurser i käll regionen, om du vill ta bort dem efter flytten.
> - Prestandatester. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.



> [!NOTE]
> Den här självstudien visar hur du flyttar virtuella Azure-datorer från en region till en annan. Om du behöver förbättra tillgängligheten genom att flytta virtuella datorer i en tillgänglighets uppsättning till zon fästa virtuella datorer i en annan region, se avsnittet [flytta virtuella Azure-datorer till Tillgänglighetszoner själv studie kursen](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Förutsättningar

- Se till att de virtuella Azure-datorerna finns i den Azure-region som du vill flytta.
- Kontrol lera att valet av [käll region – kombination av mål region stöds](./azure-to-azure-support-matrix.md#region-support)och fatta ett informerat beslut om mål regionen.
- Vara säker på att du förstår [arkitekturen och komponenterna för scenariot](azure-to-azure-architecture.md).
- Granska [begräsningar i stöd samt krav](azure-to-azure-support-matrix.md).
- Verifiera konto behörigheter. Om du har skapat ditt kostnads fria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerations administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och huvudsakligen kopiera data med hjälp av Azure Site Recovery måste du ha:

    - Behörighet att skapa en virtuell dator i Azure-resurser. Den inbyggda rollen virtuell dator deltagare har följande behörigheter, bland annat:
    - Behörighet att skapa en virtuell dator i den valda resursgruppen
    - Behörighet att skapa en virtuell dator i det valda virtuella nätverket
    - Behörighet att skriva till det valda lagringskontot
    
    - Behörigheter för att hantera Azure Site Recovery åtgärder. Rollen Site Recovery Contributor har alla behörigheter som krävs för att hantera Site Recovery åtgärder i ett Recovery Services valv.

- Se till att alla de senaste rot certifikaten finns på de virtuella Azure-datorer som du vill flytta. Om de senaste rot certifikaten inte finns på den virtuella datorn kan säkerhets begränsningarna förhindra att data kopieras till mål regionen.

- I virtuella datorer med Windows installerar du alla de senaste uppdateringarna så att alla betrodda rotcertifikat finns på datorn. I en frånkopplad miljö använder du organisationens vanliga processer för Windows Update och certifikatuppdatering.
    
- För virtuella Linux-datorer följer du rikt linjerna från Linux-distributören för att få de senaste betrodda rot certifikaten och listan över återkallade certifikat på den virtuella datorn.
- Se till att du inte använder en autentiseringsprovider för att kontrol lera nätverks anslutningen för virtuella datorer som du vill flytta.

- Om den virtuella datorn som du försöker flytta inte har åtkomst till Internet, eller om den använder en brand Väggs-proxy för att kontrol lera utgående åtkomst, [kontrollerar du kraven](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Detta omfattar men är inte begränsat till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser.

- Kontrol lera att din Azure-prenumeration gör att du kan skapa virtuella datorer i mål regionen som används för haveri beredskap. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att stödja virtuella datorer med storlekar som matchar dina virtuella käll datorer. Om du använder Site Recovery för att kopiera data till målet, Site Recovery välja samma storlek eller närmsta möjliga storlek för den virtuella mål datorn.

- Se till att du skapar en mål resurs för varje komponent som identifieras i käll nätverkets layout. Det här steget är viktigt för att se till att dina virtuella datorer har alla funktioner och funktioner i mål regionen som du hade i käll regionen.

     > [!NOTE] 
     > Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för den virtuella käll datorn. Du kan också skapa ett nätverk i förväg och tilldela det till den virtuella datorn i användar flödet för att aktivera replikering. Som vi nämnt senare måste du manuellt skapa andra resurser i mål regionen.

    Information om hur du skapar de vanligaste nätverks resurserna som är relevanta för dig baserat på konfigurationen av den virtuella käll datorn finns i följande dokumentation:
    - [Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md)
    - [Lastbalanserare](../load-balancer/index.yml)
    -  [Offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md)
    - Information om andra nätverks komponenter finns i [dokumentationen till nätverket](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Förbereda
Följande steg visar hur du förbereder den virtuella datorn för flytt med Azure Site Recovery som en lösning. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Skapa valvet i vilken region som helst, förutom käll regionen

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. I Sök skriver du Recovery Services > klickar på Recovery Services valv
1. I menyn Recovery Services valv klickar du på + Lägg till.
1. I **Namn** anger du det egna namnet **ContosoVMVault**. Om du har mer än en prenumeration väljer du den lämpligaste.
1. Skapa resurs gruppen **conto sorg**.
1. Ange en Azure-region. Om du vill kontrol lera regioner som stöds, se geografisk tillgänglighet i [Azure Site Recovery pris information](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Välj **ContosoVMVault**   >  **replikerade objekt**  >  **+ Replikera** i Recovery Services valv.
1. I list rutan väljer du **Azure Virtual Machines**.
1. I **Källplats** väljer du den Azure källregion där de virtuella datorerna körs just nu.
1. Välj Resource Manager-distributionsmodellen. Välj sedan **källprenumerationen** och **källresursgruppen**.
1. Spara inställningarna genom att klicka på **OK** .

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivera replikering för virtuella Azure-datorer och börja kopiera data

Site Recovery hämtar en lista över de virtuella datorer som är associerade med prenumerationen och resurs gruppen.

1. I nästa steg väljer du den virtuella dator som du vill flytta och väljer sedan **OK**.
1. I **Inställningar** väljer du **haveri beredskap**.
1. I **Konfigurera haveriberedskap** > **Målregion** väljer du den målregion du ska replikera till.
1. I den här självstudiekursen accepterar du de andra standardinställningarna.
1. Välj **Aktivera replikering**. Det här steget startar ett jobb för att aktivera replikering för den virtuella datorn.



## <a name="move"></a>Flytta

Följande steg visar hur du utför flytten till mål regionen.

1. Gå till valvet. I **Inställningar**  >  **replikerade objekt** väljer du den virtuella datorn och väljer sedan **redundans**.
2. I **Redundans** väljer du **Senaste**.
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
4. När jobbet är klart kontrollerar du att den virtuella datorn visas i Azure-regionen som förväntat.


## <a name="discard"></a>Ignorera 

Om du har markerat den flyttade virtuella datorn och behöver göra ändringen till platsen för redundans eller vill gå tillbaka till en tidigare plats, högerklickar du på den virtuella datorn > **ändra återställnings punkt** i de **replikerade objekten**. Det här steget ger dig möjlighet att ange en annan återställnings punkt och redundansväxla till den. 


## <a name="commit"></a>Checka in 

När du har checkat in den flyttade virtuella datorn och är redo att genomföra ändringen i de **replikerade objekten**, högerklickar du på den virtuella datorn > **genomför**. I det här steget slutförs flyttnings processen till mål regionen. Vänta tills commit-jobbet har slutförts.

## <a name="clean-up"></a>Rensa

Följande steg vägleder dig genom hur du rensar käll området och relaterade resurser som användes för flytten.

För alla resurser som användes för flytten:

- Gå till den virtuella datorn. Välj **inaktivera replikering**. Det här steget stoppar processen från att kopiera data för den virtuella datorn.

   > [!IMPORTANT]
   > Det är viktigt att du utför det här steget för att undvika att debiteras för Azure Site Recovery replikering.

Om du inte har några planer på att återanvända någon av käll resurserna kan du utföra dessa ytterligare steg:

1. Ta bort alla relevanta nätverks resurser i käll regionen som du identifierade i [krav](#prerequisites).
1. Ta bort motsvarande lagringskonto från källregionen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en virtuell Azure-dator till en annan Azure-region. Nu kan du konfigurera haveri beredskap för den virtuella dator som du flyttade.

> [!div class="nextstepaction"]
> [Konfigurera haveriberedskap efter migrering](azure-to-azure-quickstart.md)
