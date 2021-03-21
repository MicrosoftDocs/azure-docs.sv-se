---
title: Hantera labb konton i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto i en Azure-prenumeration.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434098"
---
# <a name="create-and-manage-lab-accounts"></a>Skapa och hantera labbkonton
I Azure Lab Services är ett labb konto en behållare för hanterade labb typer, till exempel labb. En administratör konfigurerar ett labb konto med Azure Lab Services och ger till gång till labb ägare som kan skapa labb i kontot. Den här artikeln beskriver hur du skapar ett labb konto, visar alla labb konton eller tar bort ett labb konto.

## <a name="create-a-lab-account"></a>Skapa ett labbkonto
Följande steg visar hur du använder Azure Portal till att skapa ett labbkonto med Azure Lab Services. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn till vänster väljer du **Alla tjänster**. Välj **labb konton** i avsnittet **DevOps** . Om du väljer stjärnan (`*`) bredvid **Labbkonton** läggs det till i avsnittet **FAVORITER** på den vänstra menyn. Från och med nästa gång väljer du **Labbkonton** under **FAVORITER**.

    ![Alla tjänster -> Labbkonton](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. På sidan **labb konton** väljer du **Lägg till** i verktygsfältet eller **Skapa labb konto** på sidan. 

    ![Välj Lägg till på sidan Labbkonton](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. På fliken **grundläggande** på sidan **skapa ett labb konto** gör du följande: 
    1. För **namnet på labbkontot** anger du ett namn. 
    2. Välj den **Azure-prenumeration** där du vill skapa labbkontot.
    3. Som **Resursgrupp** väljer du **Skapa ny** och anger ett namn på resursgruppen.
    4. Som **Plats** väljer du den plats/region där du vill att labbkontot ska skapas.
    5. I fältet **Tillåt labbets skapare att välja plats för labbet** anger du om du vill att labbskaparna ska kunna välja en plats för labbet. Det här alternativet är inaktiverat som standard. När det är inaktiverat kan inte labbskaparna ange någon plats för labbet som de skapar. Labbarna skapas på den geografiska plats som är närmast labbkontot. När det är aktiverat kan en labbskapare välja en plats vid den tid då labbet skapas. Mer information finns i [Tillåt labb skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md). 

        ![Skapa labb konto – > grunderna](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Välj **Nästa: Avancerat** längst ned på sidan för att navigera till fliken **Avancerat** och utför sedan följande steg: 
    1. Välj ett befintligt **delat avbildnings Galleri** eller skapa ett. Du kan spara mallen VM i det delade avbildnings galleriet så att den återanvänds av andra. Detaljerad information om delade avbildnings gallerier finns [i använda ett delat avbildnings galleri i Azure Lab Services](how-to-use-shared-image-gallery.md).
    2. Ange om du vill **stänga virtuella Windows-datorer automatiskt** när användarna kopplar från dem. Ange hur länge de virtuella datorerna ska vänta tills användaren ansluter igen innan den stängs av automatiskt. 
    3. För **peer Virtual Network** väljer du ett peer-virtuellt nätverk (VNet) för labb nätverket. Labb som skapats i det här kontot är anslutna till det valda virtuella nätverket och har åtkomst till resurserna i det valda virtuella nätverket. Mer information finns i [ansluta ditt labbs virtuella nätverk till ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md).    
    8. Ange ett **adress intervall** för virtuella datorer i labbet. Adress intervallet bör vara i CIDR-notation (Classless Inter-Domain routing) (exempel: 10.20.0.0/23). Virtuella datorer i labbet skapas i det här adress intervallet. Mer information finns i [Ange ett adress intervall för virtuella datorer i labbet](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > Egenskapen för **adress intervall** gäller endast om ett **virtuellt dator nätverk** är aktiverat för labbet.

        ![Skapa labb konto – > Advanced](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Välj **Nästa: Taggar** längst ned på sidan för att växla till fliken **taggar** . Lägg till de taggar som du vill koppla till labb kontot. Taggar är namn/värde-par som låter dig kategorisera resurser och Visa konsol IDE rad fakturering genom att tillämpa samma tagg på flera resurser och resurs grupper. Mer information finns i [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).

    ![Skärm bild som visar sidan "Skapa labb konto" med fliken Taggar markerat.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Välj **Granska + skapa** längst ned på den här sidan för att växla till fliken **Granska + skapa** . 
4. Granska sammanfattnings informationen på den här sidan och välj **skapa**. 

    ![Skapa labb konto – > Taggar](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Vänta tills distributionen är klar expanderar du **Nästa steg** och väljer **gå till resurs** som det visas i följande bild: 

    Du kan också välja **klock ikonen** i verktygsfältet (**meddelanden**), kontrol lera att distributionen har slutförts och sedan välja **gå till resurs**. 

    Du kan också välja **Uppdatera** på sidan **Labbbkonton** och välja labbkontot som du skapade. 

    ![Fönstret Skapa ett labbkonto](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Sidan **Labbkonto** öppnas:

    ![Sidan Labbkonto](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Visa labb konton
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** på menyn. 
3. Välj **labb konton** för **typen**. 
    Du kan också filtrera efter prenumeration, resurs grupp, platser och taggar. 

    ![Alla resurser-> Lab-konton](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Ta bort ett labb konto
Följ anvisningarna från föregående avsnitt som visar labb konton i en lista. Använd följande instruktioner för att ta bort ett labb konto: 

1. Välj det **labb konto** som du vill ta bort. 
2. Välj **ta bort** från verktygsfältet. 

    ![Labb konton – > knappen Ta bort](./media/how-to-manage-lab-accounts/delete-button.png)
1. Skriv **Ja** för bekräftelse.
1. Välj **Ta bort**. 

    ![Ta bort labb konto – bekräftelse](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> Du kan också använda PowerShell-modulen AZ. LabServices (för hands version) för att hantera labb konton. Mer information finns på [Start sidan för AZ. LabServices på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

## <a name="next-steps"></a>Nästa steg
Se andra artiklar i avsnittet **instruktions guider**  ->  **skapa och konfigurera labb konton (labb konto ägare)** i innehålls förteckningen (TOC). 