---
title: Flytta virtuella Azure-datorer till tillgänglighets zoner i en annan region med Azure Resource-arbetskraften
description: Lär dig hur du flyttar virtuella Azure-datorer till tillgänglighets zoner med Azure Resource flyttal.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543008"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Flytta virtuella Azure-datorer till en tillgänglighets zon i en annan region

I den här artikeln lär du dig hur du flyttar virtuella Azure-datorer (och relaterade nätverks-och lagrings resurser) till en tillgänglighets zon i en annan Azure-region med hjälp av [Azure Resource](overview.md)överbelastning.

[Azures tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones) hjälper till att skydda din Azure-distribution från data Center fel. Varje tillgänglighetszon består av ett eller fler datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. För att garantera återhämtning finns det minst tre separata zoner i alla [aktiverade regioner](../availability-zones/az-region.md). Med resurs förflyttning kan du flytta:

- En enskild instans av en virtuell dator till en tillgänglighets zon/tillgänglighets uppsättning i mål regionen.
- En virtuell dator i en tillgänglighets zon/tillgänglighets uppsättning i mål regionen.
- En virtuell dator i en tillgänglighets zon för käll området till en tillgänglighets zon i mål regionen.


> [!IMPORTANT]
> Azure Resource-arbetskraft är för närvarande en offentlig för hands version.

[Läs den här artikeln](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)om du vill flytta virtuella datorer till en annan tillgänglighets zon i samma region.

## <a name="prerequisites"></a>Förutsättningar

- *Ägar* åtkomst till den prenumeration där resurser som du vill flytta finns.
    - Första gången du lägger till en resurs för en viss käll-och mål mappning i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen.
    - Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare eller administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
- Prenumerationen måste ha tillräckligt med kvot för att skapa käll resurserna i mål regionen. Om den inte gör det kan du begära ytterligare begränsningar. [Läs mer](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Kontrol lera priser och avgifter som är kopplade till den mål region som du flyttar virtuella datorer till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.
    


## <a name="check-vm-requirements"></a>Kontrol lera krav för virtuella datorer

1. Kontrol lera att de virtuella datorer som du vill flytta stöds.

    - [Verifiera](support-matrix-move-region-azure-vm.md#windows-vm-support) stödda virtuella Windows-datorer.
    - [Kontrol lera](support-matrix-move-region-azure-vm.md#linux-vm-support) virtuella Linux-datorer och kernel-versioner som stöds.
    - Kontrol lera inställningar för [beräkning](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och [nätverk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) som stöds.
2. Kontrol lera att de virtuella datorer som du vill flytta är aktiverade.
3. Se till att de virtuella datorerna har de senaste betrodda rot certifikaten och en uppdaterad lista över återkallade certifikat (CRL). 
    - På virtuella Azure-datorer som kör Windows installerar du de senaste Windows-uppdateringarna.
    - På virtuella datorer som kör Linux följer du rikt linjerna för Linux-distributören för att säkerställa att datorn har de senaste certifikaten och CRL: en 
4. Tillåt utgående anslutning från virtuella datorer:
    - Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till dessa [URL: er](support-matrix-move-region-azure-vm.md#url-access)
    - Om du använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar skapar du dessa [service tag-regler](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Välj vilka resurser som ska flyttas

Välj de resurser som du vill flytta.

- Du kan välja vilken resurs typ som helst som stöds över resurs grupper i den käll region som du väljer.
- Du flyttar resurser till en mål region i käll regions prenumerationen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

1. Sök efter resurs förflyttning i Azure Portal. Under **tjänster** väljer du sedan **Azure Resource förflyttning**.

    ![Sök efter resurs förflyttning](./media/move-region-availability-zone/search.png)

2. I **Översikt** väljer du **Kom igång**.

    ![Knappen för att komma igång](./media/move-region-availability-zone/get-started.png)

3. I **Flytta resurser**  >  **källa + mål** väljer du käll prenumeration och region.
4. I **mål** väljer du den region som du vill flytta de virtuella datorerna till. Klicka på **Nästa**.

     ![Sida att fylla i käll-och mål prenumeration/-region](./media/move-region-availability-zone/source-target.png)

6. I **resurser att flytta klickar du** på **Välj resurser**.
7. I **Välj resurser** väljer du den virtuella datorn. Du kan bara lägga till resurser som stöds för flytt. Klicka sedan på **färdig**. I **resurser att flytta klickar du** på **Nästa**.

    ![Sidan för att välja de virtuella datorer som ska flyttas](./media/move-region-availability-zone/select-vm.png)
8. Kontrol lera käll-och mål inställningarna i **Granska + Lägg till**.

    ![Sida för att granska inställningar och fortsätta med flytten](./media/move-region-availability-zone/review.png)

9. Klicka på **Fortsätt** för att börja lägga till resurserna.
10. När processen har slutförts klickar du på Lägg **till resurser för flytta** i meddelande ikonen.

    ![Meddelande i meddelanden](./media/move-region-availability-zone/notification.png)

När du har klickat på meddelandet visas resurser på sidan **över regioner**

> [!NOTE]
> När du har klickat på meddelandet visas resurser på sidan **över flera regioner** i ett *förberedelse vänte* läge.
> - Om du vill ta bort en resurs från en flyttnings samling är metoden för att göra det beroende av var du befinner dig i flyttnings processen. [Läs mer](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Matcha beroenden

1. Om resurser visar ett *verifierings beroende* meddelande i kolumnen **problem** klickar du på knappen **Verifiera beroenden** . Verifierings processen.
2. Om det finns beroenden klickar du på **Lägg till beroenden**. 
3. I **Lägg till beroenden** väljer du de beroende resurserna > **lägga till beroenden**. Övervaka förloppet i aviseringarna.

    ![Knapp för att lägga till beroenden](./media/move-region-availability-zone/add-dependencies.png)

3. Lägg till ytterligare beroenden om det behövs och verifiera beroenden igen. 

    ![Sida för att lägga till ytterligare beroenden](./media/move-region-availability-zone/add-additional-dependencies.png)

4. På sidan **över regioner** kontrollerar du att resurserna nu är i ett *förberedelse* tillstånd utan problem.

    ![Sida som visar resurser i förberedelse av väntande tillstånd](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen 

Innan du kan förbereda och flytta virtuella datorer måste käll resurs gruppen finnas i mål regionen. 

### <a name="prepare-to-move-the-source-resource-group"></a>Förbereda flytt av källresursgruppen

Förbered enligt följande:

1. I **flera regioner** väljer du käll resurs gruppen > **Förbered**.
2. I **Förbered resurser** klickar du på **Förbered**.

    ![För att förbereda käll resurs gruppen](./media/move-region-availability-zone/prepare-resource-group.png)

    Under förberedelse processen genererar resurs förflyttningen Azure Resource Manager (ARM) mallar med hjälp av resurs grupps inställningarna. Resurser i resurs gruppen påverkas inte.

> [!NOTE]
>  När du har bearbetat resurs gruppen är det i läget *Starta flyttning väntar* . 

![Status som visar tillståndet initiera väntar](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen

Starta flyttningen enligt följande:

1. I **flera regioner** väljer du resurs grupp > **initiera flytt**
2. ra **Flytta resurser**, klicka på **initiera flytta**. Resurs gruppen flyttas till en *initierings status som börjar* gälla.
3. När flytten har påbörjats skapas mål resurs gruppen baserat på den genererade ARM-mallen. Käll resurs gruppen flyttas till ett *förväntat flyttnings* tillstånd.

![Status som visar commit-flyttning](./media/move-region-availability-zone/commit-move-pending.png)

För att genomföra och slutföra flytt processen:

1. I **flera regioner** väljer du resurs gruppen > **genomför flytt**
2. ra **Flytta resurser**, klicka på **genomför**.

> [!NOTE]
> När flytten har genomförts är käll resurs gruppen i ett *väntande tillstånd för att ta bort källan* .


## <a name="add-a-target-availability-zone"></a>Lägg till mål tillgänglighets zon

Innan vi flyttar resten av resurserna, ställer vi in en mål tillgänglighets zon för den virtuella datorn.

1. På sidan **över regioner** klickar du på länken i kolumnen **mål konfiguration** för den virtuella dator som du flyttar.

    ![Egenskaper för virtuell dator](./media/move-region-availability-zone/select-vm-settings.png)

3. I **konfigurations inställningar** anger du inställningen för den virtuella mål datorn. Du kan konfigurera en virtuell dator i mål regionen enligt följande:
    -  Skapa en ny motsvarande resurs i mål regionen. Förutom de inställningar du anger skapas mål resursen med samma inställningar som källan.
    - Välj en befintlig virtuell dator i mål regionen. 

4. I **zoner** väljer du den zon där du vill placera den virtuella datorn när den flyttas.

    Ändringar görs bara för den resurs som du redigerar. Du måste uppdatera alla beroende resurser separat.

5. I **SKU** anger du den [Azure-nivå](..//virtual-machines/sizes.md) som du vill tilldela den virtuella mål datorn.
6. I **tillgänglighets uppsättning** väljer du en tillgänglighets uppsättning om du vill att den virtuella mål datorn ska köras inom en tillgänglighets uppsättning i tillgänglighets zonen.
7. Välj **Spara ändringar**.

    ![Inställningar för virtuell dator](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Förbered resurser för att flytta

Nu när käll resurs gruppen flyttas kan du förbereda för att flytta de andra resurserna.

1. I **flera regioner** väljer du de resurser som du vill förbereda. 

    ![Sidan för att välja Förbered för andra resurser](./media/move-region-availability-zone/prepare-other.png)

2. Välj **Förbered**. 

> [!NOTE]
> - Under förberedelse processen installeras Azure Site Recovery Mobility-agenten på virtuella datorer för replikering.
> - VM-data replikeras regelbundet till mål regionen. Detta påverkar inte den virtuella käll datorn.
> - Resurs flyttning genererar ARM-mallar för de andra käll resurserna.
> - När du har bearbetat resurser är de i ett *initierings* tillstånd som väntar.

![Sida som visar resurser i status för att påbörja flytt](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Påbörja flytten

När resurserna har förberetts kan du nu initiera flytten. 

1. I **flera regioner** väljer du resurser med tillstånds *initieringen väntar*. Klicka sedan på **påbörja flyttning**
2. I **Flytta resurser** klickar du på **påbörja flyttning**.

    ![Sida för att initiera flytt av resurser](./media/move-region-availability-zone/initiate-move.png)

3. Spåra flyttnings förlopp i meddelande fältet.


> [!NOTE]
> - Virtuella repliker skapas i mål regionen för virtuella datorer. Den virtuella käll datorn stängs av och vissa drift stopp inträffar (vanligt vis minuter).
> - Resurs förflyttningen återskapar andra resurser med ARM-mallarna som för bereddes. Det finns vanligt vis ingen stillestånds tid.
> - När du har bearbetat resurser är de i ett tillstånd där *flytt väntar* .


![Sidan om du vill visa resurser i förväntat flytt läge](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Ta bort eller bekräfta?

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kan ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där *initieringen väntar*.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där *borttagnings källan väntar*, och du kan välja om du vill ta bort den.

## <a name="discard-the-move"></a>Ta bort flytten 

Du kan ta bort flytten på följande sätt:

1. I **flera regioner** väljer du resurser med status *commit flytta väntar* och klickar på **ta bort flyttning**.
2. I **ta bort flyttning** klickar du på **Ignorera**.
3. Spåra flyttnings förlopp i meddelande fältet.
 

> [!NOTE]
> För virtuella datorer, efter att ha tagit bort resurser, är de i ett *initierings* tillstånd som väntar.

## <a name="commit-the-move"></a>Genomför flyttningen

Spara flyttningen om du vill slutföra flyttnings processen. 

1. I **flera regioner** väljer du resurser med status *bekräftelse flytt väntar* och klickar på **genomför flyttning**.
2. I **genomför resurser** klickar du på **genomför**.

    ![Sida för att allokera resurser för att slutföra flytten](./media/move-region-availability-zone/commit-resources.png)

3. Spåra inchecknings förloppet i meddelande fältet.

> [!NOTE]
> - När flytten har genomförts upphör virtuella datorer att replikera. Den virtuella käll datorn påverkas inte av genomförandet.
> - Commit påverkar inte käll nätverks resurser.
> - När flytten har genomförts är resurserna i ett väntande tillstånd för att *ta bort källan* .

![Sidan visar resurser i * ta bort källan väntar på tillstånd](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Konfigurera inställningar efter flyttningen

Mobilitets tjänsten avinstalleras inte automatiskt från virtuella datorer. Avinstallera det manuellt eller lämna det om du planerar att flytta servern igen.


## <a name="delete-source-resources-after-commit"></a>Ta bort käll resurser efter incheckning

Efter flytten kan du välja att ta bort resurser i käll regionen.

1. I **flera regioner** klickar du på namnet på varje käll resurs som du vill ta bort.
2. På sidan Egenskaper för varje resurs väljer du **ta bort**.

## <a name="delete-additional-resources-created-for-move"></a>Ta bort ytterligare resurser som har skapats för flytt

Efter flyttningen kan du ta bort flyttnings samlingen manuellt och Site Recovery resurser som har skapats.

- Flyttnings samlingen är dold som standard. Du måste aktivera dolda resurser för att kunna se det.
- Cache-lagringen har ett lås som måste tas bort innan den kan tas bort.

Ta bort enligt följande: 

1. Leta upp resurserna i resurs gruppen ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Kontrol lera att alla virtuella datorer och andra käll resurser i käll regionen har flyttats eller tagits bort. Detta säkerställer att det inte finns några väntande resurser som använder dem.
2. Ta bort resurserna:

    - Namnet på flyttnings samlingen är ```movecollection-<sourceregion>-<target-region>``` .
    - Namnet på cachens lagrings konto är ```resmovecache<guid>```
    - Valv namnet är ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om](about-move-process.md) flytt processen.