---
title: Så här flyttar du Azure Backup Recovery Services-valv
description: Anvisningar om hur du flyttar ett Recovery Services-valv mellan Prenumerationer och resursgrupper i Azure.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 49d6782af5a9c946eaf92147dab22e4605195d89
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514775"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Flytta ett Recovery Services-valv mellan Azure-prenumerationer och resursgrupper

Den här artikeln beskriver hur du flyttar ett Recovery Services-valv som konfigurerats för Azure Backup mellan Azure-prenumerationer eller till en annan resursgrupp i samma prenumeration. Du kan använda Azure Portal PowerShell för att flytta ett Recovery Services-valv.

## <a name="supported-regions"></a>Regioner som stöds

Alla offentliga regioner och suveräna regioner stöds, förutom Frankrike, centrala, Frankrike, södra, Tyskland, nordöstra, Tyskland, centrala, Kina, norra, Kina, norra 2, Kina, östra och Kina, östra 2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Förutsättningar för att flytta Recovery Services-valv

- När valvet flyttas mellan resursgrupper låses både käll- och målresursgrupperna, vilket förhindrar skriv- och borttagningsåtgärder. Mer information finns i den här [artikeln](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Endast administratörsprenumeration har behörighet att flytta ett valv.
- För att flytta valv mellan prenumerationer måste målprenumerationen finnas i samma klientorganisation som källprenumerationen och dess tillstånd måste vara aktiverat. Om du vill flytta ett valv till en annan Azure AD-katalog kan du läsa Överföra prenumeration [till en annan katalog](../role-based-access-control/transfer-subscription.md) och Vanliga frågor och svar om Recovery [Service-valv.](/backup-azure-backup-faq.yml#recovery-services-vault)
- Du måste ha behörighet att utföra skrivåtgärder på målresursgruppen.
- När du flyttar valvet ändras bara resursgruppen. Recovery Services-valvet finns på samma plats och kan inte ändras.
- Du kan bara flytta ett Recovery Services-valv per region i taget.
- Om en virtuell dator inte flyttas med Recovery Services-valvet mellan prenumerationer eller till en ny resursgrupp förblir de aktuella VM-återställningspunkterna intakta i valvet tills de upphör att gälla.
- Oavsett om den virtuella datorn flyttas med valvet eller inte kan du alltid återställa den virtuella datorn från den kvarhållna säkerhetskopieringshistoriken i valvet.
- För Azure Disk Encryption måste nyckelvalvet och de virtuella datorerna finnas i samma Azure-region och -prenumeration.
- Information om hur du flyttar en virtuell dator med hanterade diskar finns i den här [artikeln.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- Alternativen för att flytta resurser som distribueras via den klassiska modellen varierar beroende på om du flyttar resurserna i en prenumeration eller till en ny prenumeration. Mer information finns i den här [artikeln](../azure-resource-manager/management/move-resource-group-and-subscription.md).
- Säkerhetskopieringsprinciper som definierats för valvet bevaras när valvet flyttas mellan prenumerationer eller till en ny resursgrupp.
- Du kan bara flytta ett valv som innehåller någon av följande typer av säkerhetskopieringsobjekt. Alla säkerhetskopieringsobjekt av typer som inte anges nedan måste stoppas och data tas bort permanent innan du flyttar valvet.
  - Azure Virtual Machines
  - Microsoft Azure Recovery Services-agent (MARS)
  - Microsoft Azure Backup Server (MABS)
  - Data Protection Manager (DPM)
- Om du flyttar ett valv som innehåller säkerhetskopierade data för virtuella datorer mellan prenumerationer måste du flytta dina virtuella datorer till samma prenumeration och använda samma resursgruppnamn för den virtuella måldatorn (som i den gamla prenumerationen) för att fortsätta säkerhetskopiorna.

> [!NOTE]
> Det går inte att flytta Recovery Services-valv Azure Backup mellan Azure-regioner.<br><br>
> Om du har konfigurerat virtuella datorer (Azure IaaS, Hyper-V, VMware) eller fysiska datorer för haveriberedskap **med hjälp av Azure Site Recovery** blockeras flyttåtgärden. Om du vill flytta valv för Azure Site Recovery kan du läsa den [här artikeln om](../site-recovery/move-vaults-across-regions.md) du vill veta mer om att flytta valv manuellt.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Använda Azure Portal för att flytta Recovery Services-valvet till en annan resursgrupp

Flytta ett Recovery Services-valv och dess associerade resurser till en annan resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över **Recovery Services-valv** och välj det valv som du vill flytta. När valvets instrumentpanel öppnas visas den enligt följande bild.

   ![Öppna Recovery Services-valv](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Om du inte ser informationen **om Essentials** för valvet väljer du listrrutan. Nu bör du se information om Essentials för valvet.

   ![Fliken Information om information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. I valvöversiktsmenyn **väljer du** ändra bredvid **resursgruppen** för att öppna **fönstret Flytta** resurser.

   ![Ändra resursgrupp](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. I fönstret **Flytta resurser** rekommenderar vi att du flyttar de valfria relaterade resurserna för det valda valvet genom att markera kryssrutan enligt följande bild.

   ![Flytta prenumeration](./media/backup-azure-move-recovery-services/move-resource.png)

5. Om du vill lägga till målresursgruppen **väljer** du en befintlig resursgrupp i listrutan Resursgrupp eller väljer **alternativet Skapa en ny** grupp.

   ![Skapa resurs](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. När du har lagt till resursgruppen bekräftar du att verktyg och skript som är associerade med flyttade resurser inte fungerar förrän jag uppdaterar dem för att använda nya **resurs-ID:er** och sedan väljer **OK** för att slutföra flytten av valvet.

   ![Bekräftelsemeddelande](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Använda Azure Portal för att flytta Recovery Services-valvet till en annan prenumeration

Du kan flytta ett Recovery Services-valv och dess associerade resurser till en annan prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna listan över Recovery Services-valv och välj det valv som du vill flytta. När valvets instrumentpanel öppnas visas den enligt följande bild.

    ![Öppna Recovery Services-valv](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Om du inte ser informationen **om Essentials** för ditt valv väljer du listr listrutan. Nu bör du se information om Essentials för ditt valv.

    ![Informationsfliken Information](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. I valvöversiktsmenyn väljer **du Ändra** bredvid **Prenumeration** för att öppna **fönstret Flytta** resurser.

   ![Ändra prenumeration](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Välj de resurser som ska flyttas. Här rekommenderar vi att du använder **alternativet Välj alla** för att välja alla valfria resurser i listan.

   ![flytta resurs](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Välj **målprenumerationen** i listrutan Prenumeration, där du vill att valvet ska flyttas.
6. Om du vill lägga till målresursgruppen **väljer** du en befintlig resursgrupp i listrutan Resursgrupp eller väljer **alternativet Skapa en ny** grupp.

   ![Lägg till prenumeration](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Välj Jag förstår att verktyg och skript som är associerade med flyttade resurser inte fungerar förrän jag uppdaterar dem till att använda nya **resurs-ID:er** för att bekräfta och välj **sedan OK.**

> [!NOTE]
> Säkerhetskopiering mellan prenumerationer (RS-valv och skyddade virtuella datorer finns i olika prenumerationer) stöds inte. Dessutom kan inte alternativet för lagringsredundans från lokal redundant lagring (LRS) till global redundant lagring (GRS) och vice versa ändras under flyttåtgärden för valvet.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Använda PowerShell för att flytta Recovery Services-valv

Om du vill flytta ett Recovery Services-valv till en annan resursgrupp använder du `Move-AzureRMResource` cmdleten . `Move-AzureRMResource` kräver resursnamnet och typen av resurs. Du kan hämta båda från `Get-AzureRmRecoveryServicesVault` cmdleten .

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Om du vill flytta resurserna till en annan prenumeration inkluderar du `-DestinationSubscriptionId` parametern .

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

När du har kör cmdletarna ovan uppmanas du att bekräfta att du vill flytta de angivna resurserna. Skriv **Y för** att bekräfta. Efter en lyckad validering flyttas resursen.

## <a name="use-cli-to-move-recovery-services-vault"></a>Använda CLI för att flytta Recovery Services-valv

Om du vill flytta ett Recovery Services-valv till en annan resursgrupp använder du följande cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Om du vill flytta till en ny prenumeration anger du `--destination-subscription-id` parametern .

## <a name="post-migration"></a>Efter migreringen

1. Ange/verifiera åtkomstkontrollerna för resursgrupperna.  
2. Funktionen Rapportering och övervakning av säkerhetskopiering måste konfigureras igen för valvet när flytten är klar. Den tidigare konfigurationen går förlorad under flyttåtgärden.

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Flytta en virtuell Azure-dator till ett annat Recovery Service-valv. 

Om du vill flytta en virtuell Azure-dator som har Azure Backup aktiverat har du två alternativ. De beror på dina affärskrav:

- [Du behöver inte bevara tidigare säkerhetskopierade data](#dont-need-to-preserve-previous-backed-up-data)
- [Tidigare säkerhetskopierade data måste bevaras](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Du behöver inte bevara tidigare säkerhetskopierade data

För att skydda arbetsbelastningar i ett nytt valv måste det aktuella skyddet och data tas bort i det gamla valvet och säkerhetskopieringen konfigureras igen.

>[!WARNING]
>Följande åtgärd är destruktiv och kan inte ångras. Alla säkerhetskopierade data och säkerhetskopieringsobjekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

**Stoppa och ta bort aktuellt skydd på det gamla valvet:**

1. Inaktivera mjuk borttagning i valvegenskaperna. Följ [dessa steg om](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) du vill inaktivera mjuk borttagning.

2. Stoppa skyddet och ta bort säkerhetskopior från det aktuella valvet. På menyn Valvinstrumentpanel väljer du **Säkerhetskopieringsobjekt.** Objekt som anges här som måste flyttas till det nya valvet måste tas bort tillsammans med sina säkerhetskopierade data. Se hur du [tar bort skyddade objekt i molnet](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) och tar bort skyddade objekt [lokalt.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

3. Om du planerar att flytta AFS (Azure-filresurser), SQL-servrar eller SAP HANA-servrar måste du också avregistrera dem. I menyn på instrumentpanelen för valvet väljer du **Infrastruktur för säkerhetskopiering.** Se hur du [avregistrerar SQL-servern,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance) [avregistrerar](manage-afs-backup.md#unregister-a-storage-account)ett lagringskonto som är associerat med Azure-filresurser och [avregistrerar en SAP HANA instans](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. När de har tagits bort från det gamla valvet fortsätter du att konfigurera säkerhetskopieringarna för din arbetsbelastning i det nya valvet.

### <a name="must-preserve-previous-backed-up-data"></a>Tidigare säkerhetskopierade data måste bevaras

Om du behöver behålla aktuella skyddade data i det gamla valvet och fortsätta skyddet i ett nytt valv finns det begränsade alternativ för vissa arbetsbelastningar:

- För MARS kan du stoppa [skyddet med kvarhållna data](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) och registrera agenten i det nya valvet.

  - Azure Backup fortsätter att behålla alla befintliga återställningspunkter för det gamla valvet.
  - Du måste betala för att behålla återställningspunkterna i det gamla valvet.
  - Du kommer endast att kunna återställa säkerhetskopierade data för återställningspunkter som inte har visats i det gamla valvet.
  - En ny inledande replik av data måste skapas på det nya valvet.

- För en virtuell Azure-dator kan du stoppa skyddet med kvarhållna [data](backup-azure-manage-vms.md#stop-protecting-a-vm) för den virtuella datorn i det gamla valvet, flytta den virtuella datorn till en annan resursgrupp och sedan skydda den virtuella datorn i det nya valvet. Se [vägledning och begränsningar för att](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) flytta en virtuell dator till en annan resursgrupp.

  En virtuell dator kan endast skyddas i ett valv i taget. Den virtuella datorn i den nya resursgruppen kan dock skyddas på det nya valvet eftersom den betraktas som en annan virtuell dator.

  - Azure Backup tjänsten behåller de återställningspunkter som har säkerhetskopierats på det gamla valvet.
  - Du måste betala för att behålla återställningspunkterna i det gamla valvet (se Azure Backup [för](azure-backup-pricing.md) mer information).
  - Du kommer att kunna återställa den virtuella datorn, om det behövs, från det gamla valvet.
  - Den första säkerhetskopieringen av det nya valvet för den virtuella datorn i den nya resursen är en inledande replik.

## <a name="next-steps"></a>Nästa steg

Du kan flytta många olika typer av resurser mellan resursgrupper och prenumerationer.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).