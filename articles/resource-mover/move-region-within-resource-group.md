---
title: Flytta resurser till en annan region med Azure Resource-arbetskraften
description: Lär dig hur du flyttar resurser inom en resurs grupp till en annan region med Azure Resource-arbetskraften.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797289"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Flytta resurser mellan regioner (från resurs grupp)

I den här artikeln får du lära dig hur du flyttar resurser i en särskild resurs grupp till en annan Azure-region. I resurs gruppen väljer du de resurser som du vill flytta. Sedan flyttar du dem med hjälp av [Azure Resource-arbetskraft](overview.md).

> [!IMPORTANT]
> Azure Resource-arbetskraft är för närvarande en offentlig för hands version.


## <a name="prerequisites"></a>Förutsättningar

- Du behöver *ägar* åtkomst till den prenumeration där resurser som du vill flytta finns.
    - Första gången du lägger till en resurs för en viss käll-och mål mappning i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen.
    - Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare eller administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
- Prenumerationen måste ha tillräckligt med kvot för att skapa käll resurserna i mål regionen. Om den inte gör det kan du begära ytterligare begränsningar. [Läs mer](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Kontrol lera priser och avgifter som är kopplade till den mål region som du flyttar virtuella datorer till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.
- Kontrol lera att de resurser som du vill flytta stöds av resurs förflyttningen:
    - Virtuella Azure-datorer och tillhör ande diskar
    - Nätverkskort
    - Tillgänglighetsuppsättningar
    - Virtuella Azure-nätverk
    - Offentliga IP-adresser
    - Nätverkssäkerhetsgrupper (NSG:er)
    - Interna och offentliga belastningsutjämnare
    - Azure SQL-databaser och elastiska pooler


## <a name="check-vm-requirements"></a>Kontrol lera krav för virtuella datorer

1. Kontrol lera att de virtuella datorer som du vill flytta stöds.

    - [Verifiera](support-matrix-move-region-azure-vm.md#windows-vm-support) stödda virtuella Windows-datorer.
    - [Kontrol lera](support-matrix-move-region-azure-vm.md#linux-vm-support) virtuella Linux-datorer och kernel-versioner som stöds.
    - Kontrol lera inställningar för [beräkning](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och [nätverk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) som stöds.
2. Se till att de virtuella datorerna har de senaste betrodda rot certifikaten och en uppdaterad lista över återkallade certifikat (CRL). 
    - På virtuella Azure-datorer som kör Windows installerar du de senaste Windows-uppdateringarna.
    - På virtuella datorer som kör Linux följer du rikt linjerna för Linux-distributören för att säkerställa att datorn har de senaste certifikaten och CRL: en 
3. Tillåt utgående anslutning från virtuella datorer:
    - Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till dessa [URL: er](support-matrix-move-region-azure-vm.md#url-access)
    - Om du använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar skapar du dessa [service tag-regler](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Välj vilka resurser som ska flyttas

Välj de resurser som du vill flytta. Du flyttar resurser till en mål region i käll regions prenumerationen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

> [!NOTE]
>  Välj inte de associerade diskarna eller så fungerar inte åtgärden. Tillhör ande diskar ingår automatiskt i en flyttning av virtuella datorer.

1. Öppna relevant resurs grupp i Azure Portal.
2. På sidan resurs grupp väljer du de resurser som du vill flytta.
3. Välj **Flytta**  >  **Flytta till en annan region**.

    ![Val för att flytta resurser till en annan region](./media/move-region-within-resource-group/select-move-region.png)
    
4. I **källa + mål** väljer du den mål region dit du vill flytta resurserna. Välj sedan **Nästa**.


    ![Sidan källa och mål för att välja mål region](./media/move-region-within-resource-group/source-target.png)


7. I **resurser som ska flyttas väljer du** **Nästa**.  
8. I **Välj resurser** väljer du den resurs som du vill flytta. Du kan bara lägga till resurser som stöds för flytt. Välj sedan **Done** (Klar).
9. I **Flytta resurser** väljer du **Nästa**. 
10. I **Granska + Lägg till**, kontrol lera käll-och mål information.
11. Bekräfta att du förstår att metadata om de resurser som flyttas kommer att lagras i en resurs grupp som skapats för detta ändamål, och att du tillåter resurs förflyttning för att skapa en Systemhanterad identitet för att få åtkomst till prenumerations resurserna.
1. Välj **Fortsätt** om du vill börja lägga till resurserna.

    ![Sammanfattnings sida för att kontrol lera informationen och fortsätta med flytten](./media/move-region-within-resource-group/summary.png)    

11. Åtgärden Lägg till resurs startar. När åtgärden har slutförts visar meddelandena att resurserna har lagts till och distributionen lyckades.
13. I meddelandena väljer du **Lägg till resurser för flytta**.

    ![Meddelande som visas i meddelanden](./media/move-region-within-resource-group/notification.png)    


14. När du har valt meddelandet läggs de resurser du har valt till i en flytta-samling i Azure Resource förflyttnings hubben.  Med resurs förflyttningen kan du kontrol lera beroenden och sedan börja flytta resurser till mål regionen.

## <a name="resolve-dependencies"></a>Matcha beroenden

De resurser som du flyttar visas på sidan **över regioner** i ett *förberedelse vänte* läge. Starta verifieringen enligt följande:

1. Om resurser visar ett *verifierings beroende* meddelande i kolumnen **ärenden** väljer du knappen **Verifiera beroenden** . Verifierings processen börjar.

    ![Knapp för att verifiera beroenden](./media/move-region-within-resource-group/validate-dependencies.png)

2. Om det finns beroenden väljer du **Lägg till beroenden**. 
3. I **Lägg till beroenden** väljer du de beroende resurserna > **lägga till beroenden**. Övervaka förloppet i aviseringarna.

    ![Knapp för att lägga till beroenden](./media/move-region-within-resource-group/add-dependencies.png)

3. Lägg till ytterligare beroenden om det behövs och verifiera beroenden efter behov. Välj **Uppdatera** för att säkerställa att resurser visar ett aktuellt tillstånd.

4. På sidan **över regioner** kontrollerar du att resurserna nu är i ett *förberedelse* tillstånd utan problem.

    ![Sida för att Visa förberedelse av väntande tillstånd för alla resurser](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen 

Innan du kan förbereda och flytta resurser måste käll resurs gruppen finnas i mål regionen. 

### <a name="prepare-to-move-the-source-resource-group"></a>Förbereda flytt av källresursgruppen

Förbered enligt följande:

1. I **flera regioner** väljer du käll resurs gruppen > **Förbered**.
2. I **Förbered resurser** väljer du **Förbered**.
1. 
    ![För att förbereda käll resurs gruppen](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Under förberedelse processen genererar resurs förflyttningen Azure Resource Manager (ARM) mallar med hjälp av resurs grupps inställningarna. Resurser i resurs gruppen påverkas inte.
    
> [!NOTE]
>  När du har bearbetat resurs gruppen är det i läget *Starta flyttning väntar* . Uppdatera för att visa det senaste läget.

![Status som visar tillståndet initiera väntar](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen

Starta flyttningen enligt följande:

1. I **flera regioner** väljer du resurs grupp > **initiera flytt**
2. ra **Flytta resurser**, Välj **initiera flytt**. Resurs gruppen flyttas till en *initierings status som börjar* gälla.
3. När flytten har påbörjats skapas mål resurs gruppen baserat på den genererade ARM-mallen. Käll resurs gruppen flyttas till ett *förväntat flyttnings* tillstånd.

![Status som visar commit-flyttning](./media/move-region-availability-zone/commit-move-pending.png)

För att genomföra och slutföra flytt processen:

1. I **flera regioner** väljer du resurs gruppen > **genomför flytt**
2. ra **Flytta resurser**, Välj **genomför**.

> [!NOTE]
> När flytten har genomförts är käll resurs gruppen i ett *väntande tillstånd för att ta bort källan* .

## <a name="modify-target-settings"></a>Ändra målinställningar

Om du inte vill flytta en käll resurs kan du göra något av följande:

- Skapa en resurs i mål regionen med samma namn och inställningar som resursen i käll regionen.
- Skapa en ny motsvarande resurs i mål regionen. Förutom de inställningar du anger skapas mål resursen med samma inställningar som källan.
- Använd en befintlig resurs i mål regionen.

Ändra en inställning enligt följande:

1. Om du vill ändra en inställning väljer du posten i kolumnen **mål konfiguration** för resursen.
2. På sidan **mål konfiguration** anger du de mål inställningar som du vill använda.
    Ändringar görs bara för den resurs som du redigerar. Du måste uppdatera alla beroende resurser separat.   
    
De exakta inställningarna som du ändrar beror på resurs typen. [Läs mer](modify-target-settings.md) om att redigera mål inställningar.

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

1. I **flera regioner** väljer du resurser med tillstånds *initieringen väntar*. Välj sedan **initiera flytt**.
2. I **Flytta resurser** väljer du **initiera flytta**.

    ![Välj för knappen initiera flyttning](./media/move-region-within-resource-group/initiate-move.png)

3. Spåra flyttnings förlopp i meddelande fältet.


> [!NOTE]
> - Virtuella repliker skapas i mål regionen för virtuella datorer. Den virtuella käll datorn stängs av och vissa drift stopp inträffar (vanligt vis minuter).<br/>
> - Resurs förflyttningen återskapar andra resurser med ARM-mallarna som för bereddes. Det finns vanligt vis ingen stillestånds tid.<br/> 
> - För belastningsutjämnare kopieras inte NAT-regler. Skapa dem i mål regionen när du har bekräftat flytten.
> - För offentliga IP-adresser kopieras inte DNS-namn etiketten. Skapa etiketten igen när du har bekräftat flyttningen.
> - När du har bearbetat resurser är de i ett tillstånd där *flytt väntar* .


## <a name="discard-or-commit"></a>Ta bort eller bekräfta?

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kan ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där *initieringen väntar*.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där *borttagnings källan väntar*, och du kan välja om du vill ta bort den.


## <a name="discard-the-move"></a>Ta bort flytten 

Du kan ta bort flytten på följande sätt:

1. I **flera regioner** väljer du resurser med status *bekräftelse flytt väntar* och väljer **ta bort flyttning**.
2. I **ta bort flyttning** väljer du **Ignorera**.
3. Spåra flyttnings förlopp i meddelande fältet.
4. När meddelandena visar att flyttningen lyckades väljer du **Uppdatera**. 

> [!NOTE]
> För virtuella datorer, efter att ha tagit bort resurser, är de i ett *initierings* tillstånd som väntar.

## <a name="commit-the-move"></a>Genomför flyttningen

Spara flyttningen om du vill slutföra flyttnings processen. 


1. I **flera regioner** väljer du resurser med status *bekräftelse flytt väntar* och väljer **genomför flyttning**.
2. I **genomför resurser** väljer du **genomför**.

    ![Sida för att allokera resurser för att slutföra flytten](./media/move-region-within-resource-group/commit-resources.png)

3. Spåra inchecknings förloppet i meddelande fältet.

> [!NOTE]
> - När flytten har genomförts upphör virtuella datorer att replikera. Den virtuella käll datorn påverkas inte av genomförandet.
> - Commit påverkar inte käll nätverks resurser.
> - När flytten har genomförts är resurserna i ett väntande tillstånd för att *ta bort källan* .

## <a name="configure-settings-after-the-move"></a>Konfigurera inställningar efter flyttningen

1. Eftersom DNS-namn etiketter inte kopieras över för offentliga IP-adresser, när flytten är klar, navigerar du till mål resurserna och uppdaterar etiketten. 
2. För interna belastningsutjämnare, eftersom NAT-regler inte kopieras över, navigerar du till resurserna som har skapats i mål regionen och uppdaterar NAT-reglerna.
3. Mobilitets tjänsten avinstalleras inte automatiskt från virtuella datorer. Avinstallera det manuellt eller lämna det om du planerar att flytta servern igen.
## <a name="delete-source-resources-after-commit"></a>Ta bort käll resurser efter incheckning

Efter flytten kan du välja att ta bort resurser i käll regionen. 

1. I **flera regioner** väljer du namnet på varje käll resurs som du vill ta bort.
2. På sidan Egenskaper för varje resurs väljer du **ta bort**.

## <a name="delete-additional-resources-created-for-move"></a>Ta bort ytterligare resurser som har skapats för flytt

Efter flyttningen kan du ta bort flyttnings samlingen manuellt och Site Recovery resurser som har skapats.

- Flyttnings samlingen är dold som standard. Du måste aktivera dolda resurser för att kunna se det.
- Cache-lagringen har ett lås som måste tas bort innan den kan tas bort.

Ta bort enligt följande: 

1. Leta upp resurserna i resurs gruppen ```RegionMoveRG-<sourceregion>-<target-region>``` i käll regionen.
2. Kontrol lera att alla virtuella datorer och andra käll resurser i flyttnings samlingen har flyttats/tagits bort. Detta säkerställer att det inte finns några väntande resurser som använder dem.
2. Ta bort resurserna:

    - Namnet på flyttnings samlingen är ```movecollection-<sourceregion>-<target-region>``` .
    - Namnet på cachens lagrings konto är ```resmovecache<guid>```
    - Valv namnet är ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Nästa steg


[Lär dig mer om](about-move-process.md) flytt processen.