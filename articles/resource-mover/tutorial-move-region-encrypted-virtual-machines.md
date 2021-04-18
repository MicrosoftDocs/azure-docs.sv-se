---
title: Flytta krypterade virtuella Azure-datorer mellan regioner med hjälp av Azure Resource Mover
description: Lär dig hur du flyttar krypterade virtuella Azure-datorer till en annan region med hjälp av Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600700"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Självstudie: Flytta krypterade virtuella Azure-datorer mellan regioner

Den här artikeln beskriver hur du flyttar krypterade virtuella Azure-datorer till en annan Azure-region med hjälp av [Azure Resource Mover](overview.md). 

Krypterade virtuella datorer kan beskrivas som antingen:

- Virtuella datorer som har diskar med Azure Disk Encryption aktiverat. Mer information finns i Skapa [och kryptera en virtuell Windows-dator med hjälp av Azure Portal](../virtual-machines/windows/disk-encryption-portal-quickstart.md).
- Virtuella datorer som använder kund hanterade nycklar (CMK: er) för kryptering i vila eller kryptering på serversidan. Mer information finns i Använda Azure Portal för att aktivera kryptering på [serversidan med kund hanterade nycklar för hanterade diskar.](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrollera kraven. 
> * För virtuella datorer med Azure Disk Encryption aktiverat kopierar du nycklar och hemligheter från nyckelvalvet för källregionen till nyckelvalvet för målregionen.
> * Förbered för att flytta virtuella datorer och för att välja resurser i den källregion som du vill flytta dem från.
> * Lös resursberoenden.
> * För virtuella datorer med Azure Disk Encryption aktiverat tilldelar du målnyckelvalvet manuellt. För virtuella datorer som använder kryptering på serversidan med kund hanterade nycklar tilldelar du manuellt en diskkrypteringsuppsättning i målregionen.
> * Flytta nyckelvalvet eller diskkrypteringsuppsättningen.
> * Förbered och flytta källresursgruppen. 
> * Förbered och flytta de andra resurserna.
> * Bestäm om flytten ska tas bort eller inte. 
> * Du kan också ta bort resurser i källregionen efter flytten.

> [!NOTE]
> Den här självstudien visar den snabbaste vägen för att testa ett scenario. Den använder bara standardalternativen. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar

Krav |Information
--- | ---
**Prenumerationsbehörigheter** | Kontrollera att du har *ägaråtkomst* till prenumerationen som innehåller de resurser som du vill flytta.<br/><br/> *Varför behöver jag ägaråtkomst?* Första gången du lägger till en resurs för ett specifikt käll- och målpar i en [Azure-prenumeration](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)skapar Resource Mover en systemtilldelning av en hanterad identitet , som tidigare kallades hanterad tjänstidentitet (MSI). Den här identiteten är betrodd av prenumerationen. Innan du kan skapa identiteten och tilldela  den nödvändiga rollerna *(* Deltagare och Administratör för  användaråtkomst i källprenumerationen) måste det konto som du använder för att lägga till resurser ha ägarbehörighet i prenumerationen. Mer information finns i [Administratörsroller för klassiska prenumerationer, Azure-roller och Azure AD-roller.](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)
**Stöd för virtuella datorer** | Kontrollera att de virtuella datorer som du vill flytta stöds genom att göra följande:<li>[Kontrollera vilka](support-matrix-move-region-azure-vm.md#windows-vm-support) virtuella Windows-datorer som stöds.<li>[Kontrollera virtuella](support-matrix-move-region-azure-vm.md#linux-vm-support) Linux-datorer och kernelversioner som stöds.<li>Kontrollera inställningar [för beräkning,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och nätverk [som](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) stöds.
**Krav för nyckelvalv (Azure Disk Encryption)** | Om du har Azure Disk Encryption aktiverat för virtuella datorer behöver du ett nyckelvalv i både käll- och målregionerna. Mer information finns i Skapa [ett nyckelvalv.](../key-vault/general/quick-create-portal.md)<br/><br/> För nyckelvalven i käll- och målregionerna behöver du följande behörigheter:<li>Nyckelbehörigheter: Nyckelhanteringsåtgärder (hämta, lista) och kryptografiska åtgärder (dekryptera och kryptera)<li>Hemliga behörigheter: Åtgärder för hemlighetshantering (hämta, lista och ange)<li>Certifikat (lista och hämta)
**Diskkrypteringsuppsättning (kryptering på serversidan med CMK)** | Om du använder virtuella datorer med kryptering på serversidan som använder en CMK behöver du en diskkrypteringsuppsättning i både käll- och målregionerna. Mer information finns i Skapa [en diskkrypteringsuppsättning.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> Flytt mellan regioner stöds inte om du använder maskinvarusäkerhetsmodulen (HSM-nycklar) för kund hanterade nycklar.
**Kvot för målregion** | Prenumerationen behöver en tillräcklig kvot för att skapa de resurser som du flyttar i målregionen. Om den inte har någon kvot begär [du ytterligare gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md)
**Målregionsavgifter** | Kontrollera de priser och avgifter som är associerade med målregionen som du flyttar de virtuella datorerna till. Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Verifiera behörigheter i nyckelvalvet

Om du flyttar virtuella datorer som har Azure Disk Encryption aktiverat måste du köra ett skript som anges i avsnittet Kopiera nycklarna till [målnyckelvalvet.](#copy-the-keys-to-the-destination-key-vault) De användare som kör skriptet bör ha rätt behörighet för att göra det. Information om vilka behörigheter som behövs finns i följande tabell. Du hittar alternativen för att ändra behörigheterna genom att gå till nyckelvalvet i Azure Portal. Under **Inställningar** väljer du **Åtkomstprinciper.**

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Skärmbild av länken Åtkomstprinciper i fönstret Inställningar för nyckelvalvet." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Om användarbehörigheterna inte finns väljer du Lägg **till åtkomstprincip** och anger sedan behörigheterna. Om användarkontot redan har en princip, under **Användare,** anger du behörigheterna enligt anvisningarna i följande tabell.

Virtuella Azure-datorer som använder Azure Disk Encryption kan ha följande varianter, och du måste ange behörigheterna enligt deras relevanta komponenter. De virtuella datorerna kan ha:
- Ett standardalternativ där disken endast krypteras med hemligheter.
- Säkerhet som använder en [nyckelkrypteringsnyckel (KEK) har lagts till.](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-key-vault"></a>Nyckelvalv för källregion

För användare som kör skriptet anger du behörigheter för följande komponenter: 

Komponent | Privilegier som krävs
--- | ---
Hemligheter |  *Hämta* <br></br> Välj **Hemliga behörigheter**  >  **Hemlighetshanteringsåtgärder** och välj sedan **Hämta.** 
Nycklar <br></br> Om du använder en KEK behöver du dessa behörigheter utöver behörigheterna för hemligheter. | *Hämta* och *dekryptera* <br></br> Välj **Nyckelbehörigheter**  >  **Nyckelhanteringsåtgärder** och välj sedan **Hämta.** I **Kryptografiska åtgärder väljer** du **Dekryptera**.

### <a name="destination-region-key-vault"></a>Nyckelvalv för målregion

I **Åtkomstprinciper** kontrollerar du att **Azure Disk Encryption för volymkryptering** är aktiverat. 

För användare som kör skriptet anger du behörigheter för följande komponenter: 

Komponent | Privilegier som krävs
--- | ---
Hemligheter |  *Ange* <br></br> Välj **Hemliga behörigheter**  >  **Hemlighetshanteringsåtgärder** och välj sedan **Ange.** 
Nycklar <br></br> Om du använder en KEK behöver du dessa behörigheter utöver behörigheterna för hemligheter. | *Hämta,* *skapa* och *kryptera* <br></br> Välj **Nyckelbehörigheter**  >  **Nyckelhanteringsåtgärder** och välj sedan **Hämta** och **skapa.** I **Kryptografiska åtgärder** väljer du **Kryptera**.

<br>

Förutom de föregående behörigheterna i målnyckelvalvet måste du lägga till behörigheter för den hanterade [systemidentitet](./common-questions.md#how-is-managed-identity-used-in-resource-mover) som Resource Mover använder för att få åtkomst till Azure-resurserna åt dig. 

1. Under **Inställningar väljer** du Lägg till **åtkomstprinciper.** 
1. I **Välj huvudnamn** söker du efter MSI. MSI-namnet är ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Lägg till följande behörigheter för MSI:

    Komponent | Privilegier som krävs
    --- | ---
    Hemligheter|  *Hämta* och *lista* <br></br> Välj **Hemliga behörigheter**  >  **Hemlighetshanteringsåtgärder** och välj sedan **Hämta** och **lista.** 
    Nycklar <br></br> Om du använder en KEK behöver du dessa behörigheter utöver behörigheterna för hemligheter. | *Hämta* och *lista* <br></br> Välj **Nyckelbehörigheter**  >  **Nyckelhanteringsåtgärder** och välj sedan **Hämta** och **lista.**

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Kopiera nycklarna till målnyckelvalvet

Kopiera krypteringshemligheterna och nycklarna från källnyckelvalvet till målnyckelvalvet med hjälp av [ett skript](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) som vi tillhandahåller.

- Kör skriptet i PowerShell. Vi rekommenderar att du använder den senaste PowerShell-versionen.
- Mer specifikt kräver skriptet dessa moduler:
    - Az.Compute
    - Az.KeyVault (version 3.0.0)
    - Az.Accounts (version 2.2.3)

Kör skriptet genom att göra följande:

1. Öppna skriptet [i](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) GitHub.
1. Kopiera innehållet i skriptet till en lokal fil och ge det namnet *Copy-keys.ps1*.
1. Kör skriptet.
1. Logga in på Azure-portalen.
1. I listrutan i fönstret  Användarindata väljer du källprenumeration, resursgrupp och virtuell källdatorn. Välj sedan målplats och målvalv för disk- och nyckelkryptering.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Skärmbild av fönstret Användarindata för att ange skriptvärdena." :::

1. Välj knappen **Välj**. 
   
   När skriptet har körts klart får du ett meddelande om att CopyKeys har slutförts.

## <a name="prepare-vms"></a>Förbereda virtuella datorer

1. När du har kontrollerat att de virtuella datorerna uppfyller kraven [kontrollerar](#prerequisites)du att de virtuella datorer som du vill flytta är påslagna. Alla virtuella datordiskar som du vill ska vara tillgängliga i målregionen måste kopplas och initieras i den virtuella datorn.
1. Så här ser du till att de virtuella datorerna har de senaste betrodda rotcertifikaten och en uppdaterad lista över återkallade certifikat:
    - Installera de senaste Windows-uppdateringarna på virtuella Windows-datorer.
    - På virtuella Linux-datorer följer du distributörsvägledningen så att datorerna har de senaste certifikaten och listan över återkallade certifikat. 
1. Om du vill tillåta utgående anslutningar från de virtuella datorerna gör du något av följande:
    - Om du använder en URL-baserad brandväggsproxy för att styra utgående anslutningar tillåter [du åtkomst till URL:erna](support-matrix-move-region-azure-vm.md#url-access).
    - Om du använder regler för nätverkssäkerhetsgrupp (NSG) för att styra utgående anslutningar skapar du dessa [tjänsttaggregler.](support-matrix-move-region-azure-vm.md#nsg-rules)

## <a name="select-the-resources-to-move"></a>Välj de resurser som ska flyttas

- Du kan välja valfri resurstyp som stöds i någon av resursgrupperna i den källregion som du väljer.  
- Du kan flytta resurser till en målregion som finns i samma prenumeration som källregionen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

Gör följande för att välja resurser:

1. I Azure Portal du efter **resource mover**. Under Tjänster **väljer** du **sedan Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Skärmbild av sökresultat för Azure Resource Mover i Azure Portal." :::

1. I fönstret Azure Resource Mover **översikt** väljer du **Flytta mellan regioner.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Skärmbild av knappen Flytta mellan regioner för att lägga till resurser för att flytta till en annan region." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. I fönstret **Flytta resurser** väljer du fliken **Källa +** mål. I listrutan väljer du sedan källprenumerationen och regionen.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Sida för att välja käll- och målregion.." :::

1. Under **Mål** väljer du den region där du vill flytta de virtuella datorerna och väljer sedan **Nästa.**

1. Välj fliken **Resurser att flytta** och välj sedan Välj **resurser.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Skärmbild av fönstret Flytta resurser och knappen Välj resurser.]." :::

1. I fönstret **Välj resurser** väljer du de virtuella datorer som du vill flytta. Som vi nämnde i [avsnittet Välj de resurser som ska](#select-the-resources-to-move) flyttas kan du bara lägga till resurser som stöds för en flytt.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Skärmbild av fönstret Välj resurser där du kan välja virtuella datorer att flytta." :::

    > [!NOTE]
    >  I den här självstudien väljer du en virtuell dator som använder kryptering på serversidan (rayne-vm) med en kund hanterad nyckel och en virtuell dator med diskkryptering aktiverat (rayne-vm-ade).

1. Välj **Klar**.
1. Välj fliken **Resurser att flytta** och välj sedan **Nästa.**
1. Välj fliken **Granska** och kontrollera sedan käll- och målinställningarna. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Skärmbild av fönstret för att granska käll- och målinställningar." :::

1. Välj **Fortsätt** för att börja lägga till resurserna.
1. Välj meddelandeikonen för att följa förloppet. När processen är klar går du till fönstret **Meddelanden och väljer** Lade till resurser för att **flytta**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Skärmbild av fönstret Meddelanden för att bekräfta att resurserna har lagts till." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. När du har valt meddelandet granskar du resurserna på **sidan Över** regioner.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Skärmbild av tillagda resurser med statusen Förbered väntande." :::

> [!NOTE]
> - De resurser som du lägger till placeras i *väntande förberedelsetillstånd.*
> - Resursgruppen för de virtuella datorerna läggs till automatiskt.
> - Om du  ändrar posterna i målkonfigurationen så att de använder en resurs som redan finns i målregionen är resurstillståndet inställt på Commit *pending*(Genomför väntar) eftersom du inte behöver initiera en flytt för den.
> - Om du vill ta bort en resurs som har lagts till beror metoden du använder på var du befinner dig i flyttprocessen. Mer information finns i Hantera [flyttsamlingar och resursgrupper.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Lösa beroenden

1. Om några resurser visar *meddelandet Verifiera beroenden* i **kolumnen Problem** väljer du knappen **Verifiera** beroenden.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Skärmbild som visar knappen Verifiera beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Verifieringsprocessen påbörjas.
1. Om beroenden hittas väljer du **Lägg till beroenden**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Skärmbild av knappen Lägg till beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. I fönstret **Lägg till beroenden** lämnar du **standardalternativet Visa alla beroenden.**

    - **Visa alla beroenden** itererar genom alla direkta och indirekta beroenden för en resurs. För en virtuell dator visas till exempel nätverkskortet, det virtuella nätverket, nätverkssäkerhetsgrupper (NSG:er) och så vidare.
    - **Visa beroenden på första nivån visar** bara direkta beroenden. För en virtuell dator visas till exempel nätverkskortet men inte det virtuella nätverket.
 
1. Välj de beroende resurser som du vill lägga till och välj sedan **Lägg till beroenden.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Skärmbild av listan över beroenden och knappen Lägg till beroenden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Verifiera beroendena igen. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Skärmbild av fönstret för att omvalidera beroendena." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Tilldela målresurser

Du måste tilldela målresurser som är associerade med kryptering manuellt.

- Om du flyttar en virtuell dator som har Azure Disk Encryption aktiverat visas nyckelvalvet i målregionen som ett beroende.
- Om du flyttar en virtuell dator med kryptering på serversidan som använder CMK:er visas diskkrypteringsuppsättningen i målregionen som ett beroende. 
- Eftersom den här självstudien visar hur du flyttar en virtuell dator som har Azure Disk Encryption aktiverat och som använder en CMK, visas både målnyckelvalvet och diskkrypteringsuppsättningen som beroenden.

Så här tilldelar du målresurserna manuellt:

1. I posten för diskkrypteringsuppsättningen väljer **du Resurs som inte har tilldelats** i kolumnen **Målkonfiguration.**
1. I **Konfigurationsinställningar** väljer du måldiskkrypteringsuppsättningen och sedan **Spara ändringar.**
1. Du kan spara och verifiera beroenden för den resurs som du ändrar, eller så kan du bara spara ändringarna och sedan verifiera allt du ändrar på samma gång.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Skärmbild av fönstret &quot;Målkonfiguration&quot; för att spara ändringar i målregionen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    När du har lagt till målresursen ändras statusen för diskkrypteringsuppsättningen till *Genomför flytt väntar på*.

1. I nyckelvalvsposten väljer du **Resurs som inte har tilldelats** i kolumnen **Målkonfiguration.** Under **Konfigurationsinställningar** väljer du nyckelvalvet för målet och sparar sedan ändringarna. 

I det här skedet ändras status för diskkrypteringsuppsättningen och nyckelvalvet till *Commit move pending*(Genomför flytt väntar).

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Skärmbild av fönstret för att förbereda andra resurser." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Gör följande för att genomföra och slutföra flyttprocessen för krypteringsresurser:

1. I **Över regioner** väljer du resursen (diskkrypteringsuppsättningen eller nyckelvalvet) och väljer sedan Genomför **flytt.**
1. I **Flytta resurser** väljer du **Genomför.**

> [!NOTE]
> När du har utfört flytten ändras resursstatusen till Ta *bort källa som väntar* på .


## <a name="move-the-source-resource-group"></a>Flytta källresursgruppen 

Innan du kan förbereda och flytta virtuella datorer måste vm-resursgruppen finnas i målregionen. 

### <a name="prepare-to-move-the-source-resource-group"></a>Förbereda flytt av källresursgruppen

Under förberedelseprocessen genererar Resource Mover Azure Resource Manager (ARM)-mallar från resursgruppsinställningarna. Resurserna i resursgruppen påverkas inte.

Förbered flytten av källresursgruppen genom att göra följande:

1. I **Över regioner** väljer du källresursgruppen och sedan **Förbered**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Skärmbild av knappen Förbered i fönstret Förbered resurser." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. I **Förbered resurser** väljer du **Förbered**.

> [!NOTE]
> När du har förberett flytten ändras resursgruppens status till *Initiate move pending (Initiera flytt väntar).* 

 
### <a name="move-the-source-resource-group"></a>Flytta källresursgruppen

Börja flytta källresursgruppen genom att göra följande:

1. I fönstret **Över regioner** väljer du resursgruppen och sedan Initiera **flytt.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Skärmbild av knappen &quot;Starta flytt&quot; i fönstret &quot;Över regioner&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. I fönstret **Flytta resurser** väljer du **Initiera flytt.** Resursgruppens status ändras till Initiate move in progress ( *Initiera flytt pågår).*   
1. När du har initierat flytten skapas målresursgruppen baserat på den genererade ARM-mallen. Källresursgruppens status ändras till *Commit move pending*(Genomför flytt väntar).

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Skärmbild av fönstret Flytta resurser som visar att resursgruppens status har ändrats till &quot;Commit move pending&quot; (Genomför flytt väntar)." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Gör följande för att genomföra flytten och slutföra processen:

1. I fönstret **Över regioner** väljer du resursgruppen och sedan Genomför **flytt.**
1. I fönstret **Flytta resurser** väljer du **Genomför.**

> [!NOTE]
> När du har utfört flytten ändras källresursgruppens status till *Ta bort källa som väntar på*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Skärmbild av källresursgruppen som visar att statusen har ändrats till &quot;Ta bort källa väntar&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Förbereda resurser för flytt

Nu när krypteringsresurserna och källresursgruppen har flyttats kan du förbereda för att flytta andra resurser vars aktuella status *är Förbered väntar.*


1. I fönstret **Över regioner** verifierar du flytten igen och löser eventuella problem.
1. Om du vill redigera målinställningarna innan du påbörjar flytten väljer du länken i kolumnen **Målkonfiguration** för resursen och redigerar sedan inställningarna. Om du redigerar inställningarna för den virtuella måldatorn bör storleken på den virtuella måldatorn inte vara mindre än storleken på den virtuella källdatorn.
1. För resurser med *statusen Förbered väntar* som du vill flytta väljer du **Förbered**.
1. I fönstret **Förbered resurser** väljer du **Förbered**.

    - Under förberedelserna installeras Azure Site Recovery mobilitetsagenten på de virtuella datorerna för att replikera dem.
    - VM-data replikeras regelbundet till målregionen. Detta påverkar inte den virtuella källdatorn.
    - Resursflyttning genererar ARM-mallar för de andra källresurserna.

> [!NOTE]
> När du har förberett resurserna ändras deras status till Initiate move pending ( *Initiera flytt väntar).*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Skärmbild av fönstret Förbered resurser som visar resurserna i statusen &quot;Initiate move pending&quot; (Initiera flytt väntar)." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Initiera flytten

Nu när du har förberett resurserna kan du starta flytten. 

1. I fönstret **Över regioner** väljer du de resurser vars status är Initiate move pending (Initiera flytt *väntar)* och väljer sedan **Initiate move (Initiera flytt).**
1. I fönstret **Flytta resurser** väljer du **Initiera flytt.**
1. Spåra förloppet för flytten i meddelandefältet.

    - För virtuella datorer skapas virtuella repliker i målregionen. Den virtuella källdatorn stängs av och viss stilleståndstid inträffar (vanligtvis minuter).
    - Resource Mover skapar om andra resurser med hjälp av de förberedda ARM-mallarna. Det finns vanligtvis ingen stilleståndstid.
    - När du har flyttat resurserna ändras deras status till *Genomför flytt väntar på*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Skärmbild av en lista över resurser med statusen &quot;Commit move pending&quot; (Genomför flytt väntar)." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Tar du bort eller genomför?

Efter den första flytten kan du bestämma om du vill genomföra flytten eller ta bort den. 

- **Ignorera:** Du kan ta bort en flytt om du testar den och inte vill flytta källresursen. Om flytten tas bort returneras resursen till *statusen Initiate move pending (Initiera flytt väntar).*
- **Genomför:** Genomför slutför flytten till målregionen. När du har utfört en källresurs ändras dess status till *Ta* bort källa som väntar och du kan bestämma om du vill ta bort den.


## <a name="discard-the-move"></a>Ta bort flytten 

Om du vill ta bort flytten gör du följande:

1. I fönstret **Över regioner** väljer du resurser vars status är Commit move pending (Genomför *flytt väntar)* och väljer sedan **Discard move (Ta bort flytt).**
1. I fönstret **Ignorera flytt** väljer du **Ignorera.**
1. Spåra förloppet för flytten i meddelandefältet.


> [!NOTE]
> När du har tagit bort resurserna ändras VM-statusen till *Initiate move pending (Initiera flytt väntar).*

## <a name="commit-the-move"></a>Genomför flytten

För att slutföra flyttprocessen genomför du flytten genom att göra följande: 

1. I fönstret **Över regioner** väljer du resurser vars status är Commit move pending (Genomför flytt *väntar)* och väljer sedan **Commit move (Genomför flytt).**
1. I fönstret **Commit resources (Genomför** resurser) väljer du **Commit (Genomför).**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Skärmbild av en lista över resurser för att genomföra flytten." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Spåra förloppet för genomförande i meddelandefältet.

> [!NOTE]
> - När du har utfört flytten slutar de virtuella datorerna att replikera. Den virtuella källdatorn påverkas inte av genomföret.
> - Genomförandeprocessen påverkar inte källans nätverksresurser.
> - När du har utfört flytten ändras resursstatusen till Ta *bort källa som väntar* på .



## <a name="configure-settings-after-the-move"></a>Konfigurera inställningar efter flytten

- Mobilitetstjänsten avinstalleras inte automatiskt från virtuella datorer. Avinstallera det manuellt eller lämna det om du planerar att flytta servern igen.
- Ändra regler för rollbaserad åtkomstkontroll (RBAC) i Azure efter flytten.

## <a name="delete-source-resources-after-commit"></a>Ta bort källresurser efter genomförd

Efter flytten kan du ta bort resurser i källregionen om du vill. 

1. I fönstret **Över regioner** markerar du varje källresurs som du vill ta bort och väljer sedan Ta **bort källa.**
1. I **Ta bort källa** granskar du vad du tänker ta bort och i Bekräfta **borttagning** skriver du **ja.** Åtgärden går inte att ångra, så kontrollera noggrant!
1. När du har skrivit **ja** väljer du **Ta bort källa.**

> [!NOTE]
>  I portalen För resursflyttning kan du inte ta bort resursgrupper, nyckelvalv eller SQL Server instanser. Du måste ta bort var och en från egenskapssidan för varje resurs.


## <a name="delete-resources-that-you-created-for-the-move"></a>Ta bort resurser som du skapade för flytten

Efter flytten kan du manuellt ta bort flyttsamlingen och Site Recovery som du skapade under den här processen.

- Flyttsamlingen är dold som standard. Om du vill se den måste du aktivera dolda resurser.
- Cachelagringen har ett lås som måste tas bort innan den kan tas bort.

Så här tar du bort dina resurser: 
1. Leta upp resurserna i resursgruppen ```RegionMoveRG-<sourceregion>-<target-region>``` .
1. Kontrollera att alla virtuella datorer och andra källresurser i källregionen har flyttats eller tagits bort. Det här steget säkerställer att det inte finns några väntande resurser som använder dem.
1. Ta bort resurserna:

    - Flytta samlingsnamn: ```movecollection-<sourceregion>-<target-region>```
    - Namn på cachelagringskonto: ```resmovecache<guid>```
    - Valvnamn: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Flyttade krypterade virtuella Azure-datorer och deras beroende resurser till en annan Azure-region.


Som nästa steg kan du prova att flytta Azure SQL databaser och elastiska pooler till en annan region.

> [!div class="nextstepaction"]
> [Flytta Azure SQL resurser](./tutorial-move-region-sql.md)
