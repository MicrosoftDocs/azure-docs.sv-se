---
title: Flytta krypterade virtuella Azure-datorer mellan regioner med Azure Resource-arbetskraft
description: Lär dig hur du flyttar krypterade virtuella Azure-datorer till en annan region med Azure Resource-arbetskraften
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361017"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Självstudie: flytta krypterade virtuella Azure-datorer över regioner

I den här artikeln lär du dig hur du flyttar krypterade virtuella Azure-datorer till en annan Azure-region med hjälp av [Azure Resource](overview.md)överfart. Det här betyder vad vi menar för kryptering:

- Virtuella datorer som har diskar med Azure Disk Encryption aktiverade. [Läs mer](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Eller virtuella datorer som använder Kundhanterade nycklar (CMKs) för kryptering vid vila (kryptering på Server sidan). [Läs mer](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera krav. 
> * För virtuella datorer med Azure Disk Encryption aktiverat kopierar du nycklar och hemligheter från nyckel valvet för käll regionen till nyckel valvet för mål området.
> * Förbered virtuella datorer för att flytta dem och välj resurser i käll regionen som du vill flytta.
> * Lös resurs beroenden.
> * För virtuella datorer med Azure Disk Encryption aktiverat tilldelar du mål nyckel valvet manuellt. För virtuella datorer som använder kryptering på Server sidan med Kundhanterade nycklar tilldelar du manuellt en disk krypterings uppsättning i mål regionen.
> * Flytta nyckel valvet och/eller disk krypterings uppsättningen.
> * Förbered och flytta käll resurs gruppen. 
> * Förbered och flytta de andra resurserna.
> * Bestäm om du vill ta bort eller bekräfta flytten. 
> * Du kan också ta bort resurser i käll regionen efter flytten.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar

**Krav** |**Information**
--- | ---
**Prenumerations behörigheter** | Kontrol lera att du har *ägar* åtkomst till prenumerationen som innehåller de resurser som du vill flytta.<br/><br/> **Varför behöver jag ägar åtkomst?** Första gången du lägger till en resurs för ett visst käll-och mål par i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen. Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare och administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
**Stöd för virtuella datorer** | Kontrol lera att de virtuella datorer som du vill flytta stöds.<br/><br/> - [Verifiera](support-matrix-move-region-azure-vm.md#windows-vm-support) stödda virtuella Windows-datorer.<br/><br/> - [Kontrol lera](support-matrix-move-region-azure-vm.md#linux-vm-support) virtuella Linux-datorer och kernel-versioner som stöds.<br/><br/> – Kontrol lera inställningarna för [beräkning](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och [nätverk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) som stöds.
**Krav för nyckel valv (Azure Disk Encryption)** | Om du har aktiverat Azure Disk Encryption för virtuella datorer måste du förutom nyckel valvet i käll regionen ha ett nyckel valv i mål regionen. [Skapa ett nyckel valv](../key-vault/general/quick-create-portal.md).<br/><br/> För nyckel valven i käll-och mål regionen behöver du följande behörigheter:<br/><br/> -Nyckel behörigheter: nyckel hanterings åtgärder (get, List); Kryptografiska åtgärder (dekryptera och kryptera).<br/><br/> – Hemliga behörigheter: hemliga hanterings åtgärder (get, list och set)<br/><br/> -Certificate (list och get).
**Disk krypterings uppsättning (kryptering på Server sidan med CMK)** | Om du använder virtuella datorer med kryptering på Server sidan med en CMK, förutom disk krypterings uppsättningen i käll regionen, behöver du en disk krypterings uppsättning i mål regionen. [Skapa en disk krypterings uppsättning](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Det finns inte stöd för att flytta mellan regioner om du använder HSM-nycklar för Kundhanterade nycklar.
**Mål regions kvot** | Prenumerationen måste ha tillräckligt med kvot för att skapa de resurser som du flyttar i mål regionen. Om den inte har någon kvot [begär du ytterligare begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Avgifter för mål region** | Kontrol lera priser och avgifter som är kopplade till den mål region som du flyttar virtuella datorer till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Verifiera användar behörigheter för nyckel valv för virtuella datorer som använder Azure Disk Encryption (ADE)

Om du flyttar virtuella datorer som har Azure Disk Encryption aktiverat måste du köra ett skript enligt [nedan](#copy-the-keys-to-the-destination-key-vault) för vilket användaren som kör skriptet ska ha rätt behörighet. Se tabellen nedan om du vill veta mer om vilka behörigheter som krävs. Du kan hitta alternativen för att ändra behörigheterna genom att gå till nyckel valvet i Azure Portal, under **Inställningar**, Välj **åtkomst principer**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Knapp för att öppna åtkomst principer för nyckel valv." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Om det inte finns några användar behörigheter väljer du **Lägg till åtkomst princip** och anger behörigheterna. Om användar kontot redan har en princip, under **användare**, anger du behörigheterna enligt tabellen nedan.

Virtuella Azure-datorer med hjälp av ADE kan ha följande variationer och behörigheterna måste anges i enlighet med detta för relevanta komponenter.
- Standard alternativet där disken krypteras med endast hemligheter
- Ökad säkerhet med [nyckel krypterings nyckel](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Käll regions nyckel valv

Nedanstående behörigheter måste anges för användaren som kör skriptet 

**Komponent** | **Behörighet krävs**
--- | ---
Hemligheter|  Hämta behörighet <br> </br> I hemliga >   **hanterings åtgärder** för hemliga behörigheter väljer du **Hämta** 
Nycklar <br> </br> Om du använder nyckel krypterings nyckel (KEK) behöver du den här behörigheten förutom hemligheter| Hämta och dekryptera behörighet <br> </br> I **nyckel**  >  **hanterings åtgärder** för nyckel behörigheter väljer du **Hämta**. I **kryptografiska åtgärder** väljer du **dekryptera**.

### <a name="destination-region-keyvault"></a>Mål regions nyckel valv

I **åtkomst principer** kontrollerar du att **Azure Disk Encryption för volym kryptering** har Aktiver ATS. 

Nedanstående behörigheter måste anges för användaren som kör skriptet 

**Komponent** | **Behörighet krävs**
--- | ---
Hemligheter|  Ange behörighet <br> </br> I hemliga >   **hanterings åtgärder** för hemliga behörigheter väljer du **Ange** 
Nycklar <br> </br> Om du använder nyckel krypterings nyckel (KEK) behöver du den här behörigheten förutom hemligheter| Hämta, skapa och kryptera behörighet <br> </br> I **nyckel**  >  **hanterings åtgärder** för nyckel behörigheter väljer du **Hämta** och **skapa** . I **kryptografiska operationer** väljer du **kryptera**.

Förutom ovanstående behörigheter måste du i mål nyckel valvet lägga till behörigheter för den [hanterade system identitet](./common-questions.md#how-is-managed-identity-used-in-resource-mover) som resurs förflyttningen använder för att få åtkomst till Azure-resurser för din räkning. 

1. Under **Inställningar** väljer du **Lägg till åtkomst principer**. 
2. I **Välj huvud konto** söker du efter MSI. MSI-namnet är ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. Lägg till nedanstående behörigheter för MSI

**Komponent** | **Behörighet krävs**
--- | ---
Hemligheter|  Hämta och lista behörighet <br> </br> I hemliga **behörigheter för** >   **hemliga hanterings åtgärder** väljer du **Hämta** och **lista** 
Nycklar <br> </br> Om du använder nyckel krypterings nyckel (KEK) behöver du den här behörigheten förutom hemligheter| Hämta, lista behörighet <br> </br> I **nyckel**  >  **hanterings åtgärder** för nyckel behörigheter väljer du **Hämta** och **lista**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Kopiera nycklarna till mål nyckel valvet

Du måste kopiera krypterings hemligheter och-nycklar från käll nyckel valvet till mål nyckel valvet med ett skript som vi tillhandahåller.

- Du kör skriptet i PowerShell. Vi rekommenderar att du kör den senaste versionen av PowerShell.
- Mer specifikt kräver skriptet följande moduler:
    - Az.Compute
    - AZ. 3.0.0-valv (version
    - AZ. accounts (version 2.2.3)

Kör så här:

1. Navigera till [skriptet](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) i GitHub.
2. Kopiera innehållet i skriptet till en lokal fil och namnge det *Copy-keys.ps1*.
3. Kör skriptet.
4. Logga in på Azure.
5. I popup-fönstret **användarindata** väljer du käll prenumerationen, resurs gruppen och den virtuella käll datorn. Välj sedan mål platsen och mål valvet för disk-och nyckel kryptering.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Popup-fönster för att ange skript värden." :::


6. När skriptet har slutförts visar skärm resultatet att CopyKeys lyckades.

## <a name="prepare-vms"></a>Förbereda virtuella datorer

1. När du har [kontrollerat att de virtuella datorerna uppfyller kraven](#prerequisites)kontrollerar du att de virtuella datorerna som du vill flytta är aktiverade. Alla virtuella dator diskar som du vill ska vara tillgängliga i mål regionen måste vara kopplade till och initieras på den virtuella datorn.
3. Kontrol lera att de virtuella datorerna har de senaste betrodda rot certifikaten och en uppdaterad lista över återkallade certifikat (CRL). Gör så här:
    - På virtuella Windows-datorer installerar du de senaste Windows-uppdateringarna.
    - På virtuella Linux-datorer följer du vägledningen för distributör så att datorerna har de senaste certifikaten och CRL: en. 
4. Tillåt utgående anslutning från virtuella datorer enligt följande:
    - Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till dessa [URL: er](support-matrix-move-region-azure-vm.md#url-access)
    - Om du använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar skapar du dessa [service tag-regler](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Välj vilka resurser som ska flyttas


- Du kan välja vilken resurs typ som helst som stöds i alla resurs grupper i käll regionen som du väljer.  
- Du flyttar resurser till en mål region som är i samma prenumeration som käll regionen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

Välj resurser enligt följande:

1. Sök efter *resurs förflyttning* i Azure Portal. Under **tjänster** väljer du sedan **Azure Resource förflyttning**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Sök Resultat för resurs förflyttning i Azure Portal." :::

2. I **Översikt** klickar du på **Flytta mellan regioner**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Om du vill lägga till resurser som ska flyttas till en annan region." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. I **Flytta resurser**  >  **källa + mål** väljer du käll prenumeration och region.
4. I **mål** väljer du den region som du vill flytta de virtuella datorerna till. Klicka på **Nästa**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Sidan för att välja käll-och mål region.." :::

5. I **resurser att flytta klickar du** på **Välj resurser**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Om du vill välja resurs att flytta.]." :::

6. I **Välj resurser** väljer du de virtuella datorerna. Du kan bara lägga till resurser som [stöds för flytt](#prepare-vms). Klicka sedan på **färdig**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="För att välja de virtuella datorer som ska flyttas." :::

    > [!NOTE]
    >  I den här självstudien väljer vi en virtuell dator som använder kryptering på Server sidan (Rayne-VM) med en kundhanterad nyckel och en virtuell dator med disk kryptering aktive rad (Rayne-VM-ade).

7.  I **resurser att flytta klickar du** på **Nästa**.
8. Kontrol lera käll-och mål inställningarna i **granskning**. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="För att granska inställningarna och fortsätta med flytten." :::

9. Klicka på **Fortsätt** för att börja lägga till resurserna.
10. Välj ikonen meddelanden för att spåra förloppet. När Lägg till-processen har slutförts väljer du **tillagda resurser för flytta** i meddelandena.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Ett meddelande om att bekräfta att resurserna har lagts till har lagts till." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. När du har klickat på meddelandet granskar du resurserna på sidan **över flera regioner** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Sidor som visar tillagda resurser med förberedelse av väntande." :::

> [!NOTE]
> - De resurser som du lägger till är *förväntat i förberedelse* tillstånd.
> - Resurs gruppen för de virtuella datorerna läggs till automatiskt.
> - Om du ändrar **mål konfigurations** posterna så att de använder en resurs som redan finns i mål regionen är resursens tillstånd att *Bekräfta väntar*, eftersom du inte behöver initiera en flytt.
> - Om du vill ta bort en resurs som har lagts till beror metoden för att göra det beroende på var du befinner dig i flyttnings processen. [Läs mer](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Matcha beroenden

1. Om några resurser visar ett *verifierings beroende* meddelande i kolumnen **ärenden** väljer du knappen **Verifiera beroenden** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="NButton för att kontrol lera beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Verifierings processen börjar.
2. Om det finns beroenden klickar du på **Lägg till beroenden**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Knapp för att lägga till beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. I **Lägg till beroenden** lämnar du standard alternativet **Visa alla beroenden** .

    - **Visa alla beroenden** upprepas genom alla direkta och indirekta beroenden för en resurs. För en virtuell dator visar till exempel NÄTVERKSKORTet, det virtuella nätverket, nätverks säkerhets grupper (NSG: er) osv.
    - Visa endast direkta beroenden på **första nivå-beroenden** . För en virtuell dator visar till exempel NÄTVERKSKORTet, men inte det virtuella nätverket.
 
4. Välj de beroende resurser som du vill lägga till > **lägga till beroenden**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Välj beroenden från listan över beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Verifiera beroenden igen. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="För att verifiera igen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Tilldela mål resurser

Mål resurser som är kopplade till krypteringen kräver manuell tilldelning.

- Om du flyttar en virtuell dator som har Azure Disk Encryption (ADE) så visas nyckel valvet i mål regionen som ett beroende.
- Om du flyttar en virtuell dator som har kryptering på Server sidan som använder anpassade hanterade nycklar (CMKs), visas disk krypteringen som angetts i mål regionen som ett beroende. 
- Eftersom den här självstudien flyttar en virtuell dator med ADE aktiverat, och en virtuell dator som använder en CMK, visas både mål nyckel valvet och disk krypterings uppsättningen som beroenden.

Tilldela manuellt enligt följande:

1. I post för disk krypterings uppsättning väljer du **resurs som inte är tilldelad** i kolumnen **mål konfiguration** .
2. I **konfigurations inställningar** väljer du mål disk krypterings uppsättningen. Välj sedan **Spara ändringar**.
3. Du kan välja att spara och verifiera beroenden för den resurs som du ändrar, eller så kan du bara spara ändringarna och verifiera allt du ändrar i en enda go.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Sidan för att välja disk krypterings uppsättning i mål regionen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    När du har lagt till mål resursen, blir statusen för disk krypterings uppsättningen för att *Bekräfta förväntat flyttning*.
3. I nyckel valvs posten väljer du **resurs som inte är tilldelad** i kolumnen **mål konfiguration** . **Konfigurations inställningar** väljer du mål nyckel valvet. Spara ändringarna. 

I det här skedet är både disk krypterings uppsättningen och Key Vault-statusen inställd på att *Bekräfta flyttningen väntar*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="För att välja Förbered för andra resurser." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

För att genomföra och slutföra flyttnings processen för krypterings resurser.

1. I **flera regioner** väljer du resursen (disk krypterings uppsättning eller nyckel valv) > **genomför flytt**.
2. ra **Flytta resurser**, klicka på **genomför**.

> [!NOTE]
> När flytten har genomförts är resursen i ett *väntande tillstånd för att ta bort källan* .


## <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen 

Innan du kan förbereda och flytta virtuella datorer måste resurs gruppen för den virtuella datorn finnas i mål regionen. 

### <a name="prepare-to-move-the-source-resource-group"></a>Förbereda flytt av källresursgruppen

Under förberedelse processen genererar resurs förflyttningen Azure Resource Manager (ARM) mallar med hjälp av resurs grupps inställningarna. Resurser i resurs gruppen påverkas inte.

Förbered enligt följande:

1. I **flera regioner** väljer du käll resurs gruppen > **Förbered**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Förbered resurs grupp." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. I **Förbered resurser** klickar du på **Förbered**.

> [!NOTE]
> När du har bearbetat resurs gruppen är det i läget *Starta flyttning väntar* . 

 
### <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen

Starta flyttningen enligt följande:

1. I **flera regioner** väljer du resurs grupp > **initiera flytt**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="För att initiera flytten." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. ra **Flytta resurser**, klicka på **initiera flytta**. Resurs gruppen flyttas till en *initierings status som börjar* gälla.   
3. När flytten har påbörjats skapas mål resurs gruppen baserat på den genererade ARM-mallen. Käll resurs gruppen flyttas till ett *förväntat flyttnings* tillstånd.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Granska läget för att flytta vänte läge." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

För att genomföra och slutföra flytt processen:

1. I **flera regioner** väljer du resurs gruppen > **genomför flytt**.
2. ra **Flytta resurser**, klicka på **genomför**.

> [!NOTE]
> När flytten har genomförts är käll resurs gruppen i ett *väntande tillstånd för att ta bort källan* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Granska statusen ta bort förväntat flytt." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Förbered resurser för att flytta

Nu när krypterings resurserna och käll resurs gruppen flyttas kan du förbereda för att flytta andra resurser som är i *förberedelse* tillstånd.


1. I **flera regioner**, validera igen och Lös eventuella problem.
2. Om du vill redigera mål inställningarna innan du påbörjar flyttningen väljer du länken i kolumnen **mål konfiguration** för resursen och redigerar inställningarna. Om du redigerar inställningarna för den virtuella mål datorn bör storleken på den virtuella mål datorn inte vara mindre än storleken på den virtuella käll datorn.
3. Välj **Förbered** för resurser i det *förberedelse väntande* tillstånd som du vill flytta.
3. I **Förbered resurser** väljer du **Förbered**

    - Under förberedelse processen installeras Azure Site Recovery Mobility-agenten på virtuella datorer för att replikera dem.
    - VM-data replikeras regelbundet till mål regionen. Detta påverkar inte den virtuella käll datorn.
    - Resurs flyttning genererar ARM-mallar för de andra käll resurserna.

När du har bearbetat resurser är de i ett *initierings* tillstånd som väntar.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Sida som visar resurser i status för att påbörja flytt." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Påbörja flytten

När resurserna har förberetts kan du nu initiera flytten. 

1. I **flera regioner** väljer du resurser med tillstånds *initieringen väntar*. Klicka sedan på **Starta flyttning**.
2. I **Flytta resurser** klickar du på **påbörja flyttning**.
3. Spåra flyttnings förlopp i meddelande fältet.

    - Virtuella repliker skapas i mål regionen för virtuella datorer. Den virtuella käll datorn stängs av och vissa drift stopp inträffar (vanligt vis minuter).
    - Resurs förflyttningen återskapar andra resurser med ARM-mallarna som för bereddes. Det finns vanligt vis ingen stillestånds tid.
    - När du har flyttat resurserna är de i ett tillstånd där *flytt väntar* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Sida som visar resurser i förväntat flytt läge." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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
> När du har tagit bort resurser är de virtuella datorerna i ett *initierings* tillstånd som väntar.

## <a name="commit-the-move"></a>Genomför flyttningen

Spara flyttningen om du vill slutföra flyttnings processen. 

1. I **flera regioner** väljer du resurser med status *bekräftelse flytt väntar* och klickar på **genomför flyttning**.
2. I **genomför resurser** klickar du på **genomför**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="För att allokera resurser för att slutföra flytten." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Spåra inchecknings förloppet i meddelande fältet.

> [!NOTE]
> - När flytten har genomförts upphör virtuella datorer att replikera. Den virtuella käll datorn påverkas inte av genomförandet.
> - Commit påverkar inte käll nätverks resurser.
> - När flytten har genomförts är resurserna i ett väntande tillstånd för att *ta bort källan* .



## <a name="configure-settings-after-the-move"></a>Konfigurera inställningar efter flyttningen

- Mobilitets tjänsten avinstalleras inte automatiskt från virtuella datorer. Avinstallera det manuellt eller lämna det om du planerar att flytta servern igen.
- Ändra Azure-rollbaserad åtkomst kontroll (Azure RBAC) regler efter flytten.

## <a name="delete-source-resources-after-commit"></a>Ta bort käll resurser efter incheckning

Efter flytten kan du välja att ta bort resurser i käll regionen. 

1. I **flera regioner** väljer du varje käll resurs som du vill ta bort. Välj sedan **ta bort källa**.
2. I **ta bort källa** granskar du vad du vill ta bort och i **Bekräfta borttagning** skriver du **Ja**. Åtgärden kan inte ångras. kontrol lera noggrant!
3. När du har skrivit **Ja** väljer du **ta bort källa**.

> [!NOTE]
>  I resursens flytt Portal kan du inte ta bort resurs grupper, nyckel valv eller SQL Server-servrar. Du måste ta bort dessa individuellt från sidan Egenskaper för varje resurs.


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

I den här kursen får du:

> [!div class="checklist"]
> * Flyttade krypterade virtuella Azure-datorer och deras beroende resurser till en annan Azure-region.


Nu kan du prova att flytta Azure SQL-databaser och elastiska pooler till en annan region.

> [!div class="nextstepaction"]
> [Flytta Azure SQL-resurser](./tutorial-move-region-sql.md)
