---
title: Kom igång med konfiguration av Azure Automation tillstånd
description: Den här artikeln beskriver hur du utför de vanligaste uppgifterna i Azure Automation tillstånds konfiguration.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051506"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Kom igång med konfiguration av Azure Automation tillstånd

Den här artikeln innehåller en steg-för-steg-guide om hur du utför de vanligaste uppgifterna med Azure Automation tillstånds konfiguration, till exempel skapa, importera och kompilera konfigurationer, aktivera datorer för att hantera och Visa rapporter. En översikt över tillstånds konfiguration finns i [Översikt över tillstånds konfiguration](automation-dsc-overview.md). För dokumentation om Desired State Configuration (DSC), se [Översikt över Desired State Configuration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Om du vill ha en exempel miljö som redan har kon figurer ATS utan att följa stegen som beskrivs i den här artikeln kan du använda [mallen Azure Automation hanterad nod](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Med den här mallen ställer du in en fullständig tillstånds konfiguration (DSC)-miljö, inklusive en virtuell Azure-dator som hanteras av State Configuration (DSC).

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra exemplen i den här artikeln:

- Ett Azure Automation-konto. Mer information om ett Automation-konto och dess krav finns i [Översikt över Automation-kontots autentisering](./automation-security-overview.md).
- En Azure Resource Manager virtuell dator (inte klassisk) som kör ett [operativ system som stöds](automation-dsc-overview.md#operating-system-requirements). Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>Skapa en DSC-konfiguration

Du kan skapa en enkel [DSC-konfiguration](/powershell/scripting/dsc/configurations/configurations) som garanterar antingen närvaron eller frånvaron av Windows-funktionen i **Web Server** (IIS), beroende på hur du tilldelar noder.

1. Starta [VSCode](https://code.visualstudio.com/docs) (eller valfri text redigerare).
1. Skriv följande text:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Spara filen som **TestConfig.ps1**.

Den här konfigurationen anropar en resurs i varje Node-block, [WindowsFeature-resursen](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Den här resursen garanterar närvaron eller frånvaron av **webb server** funktionen.

## <a name="import-a-configuration-into-azure-automation"></a>Importera en konfiguration till Azure Automation

Därefter importerar du konfigurationen till Automation-kontot.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto väljer du **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **Lägg till**.
1. I fönstret Importera konfiguration bläddrar du till `TestConfig.ps1` filen på din dator.

   ![Skärm bild av bladet * * import Configuration * *](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicka på **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Visa en konfiguration i Azure Automation

När du har importerat en konfiguration kan du Visa den i Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto väljer du  **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **TestConfig**. Det här är namnet på konfigurationen som du importerade i föregående procedur.
1. I konfigurations fönstret för TestConfig klickar du på **Visa konfigurations källa**.

   ![Skärm bild av bladet TestConfig konfiguration](./media/automation-dsc-getting-started/ViewConfigSource.png)

   En TestConfig konfigurations källa öppnas och visar PowerShell-koden för konfigurationen.

## <a name="compile-a-configuration-in-azure-automation"></a>Kompilera en konfiguration i Azure Automation

Innan du kan använda ett önskat tillstånd för en nod, måste en DSC-konfiguration som definierar det läget kompileras till ett eller flera nodkonfigurationer (MOF-dokument) och placeras på Automation DSC hämtnings Server. En mer detaljerad beskrivning av hur du kompilerar konfigurationer i tillstånds konfiguration (DSC) finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
Mer information om hur du kompilerar konfigurationer finns i [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **TestConfig**. Detta är namnet på den tidigare importerade konfigurationen.
1. I konfigurations fönstret för TestConfig klickar du på **kompilera** och sedan på **Ja**. Detta startar ett kompilerings jobb.

   ![Skärm bild av knappen kompilera i TestConfig konfigurations sida](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> När du kompilerar en konfiguration i Azure Automation distribueras automatiskt alla skapade MOF-filer för noden som skapats till hämtnings servern.

## <a name="view-a-compilation-job"></a>Visa ett Compilation-jobb

När du har startat en kompilering kan du Visa den på panelen för att **kompilera jobb** på **konfigurations** sidan. På panelen **Compilation Jobs** visas pågående, slutförda och misslyckade jobb. När du öppnar ett fönster för kompilering av kompilering visas information om det jobbet, inklusive eventuella fel eller varningar, indataparametrar som används i konfigurations-och kompilerings loggar.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** och sedan på **TestConfig**. Detta är namnet på den tidigare importerade konfigurationen.
1. Under **Compilation Jobs** väljer du det Compilation-jobb som du vill visa. Ett fönster för kompilering öppnas med namnet med datumet då Compilation-jobbet startades.

   ![Skärm bild av sidan för att kompilera jobb](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicka på valfri panel i fönstret kompilera jobb om du vill visa mer information om jobbet.

## <a name="view-node-configurations"></a>Visa nodkonfigurationer

Slutfört slut för ande av ett Compilation-jobb skapar en eller flera nya nodkonfigurationer. En Node-konfiguration är ett MOF-dokument som distribueras till pull-servern och är redo att hämtas och tillämpas av en eller flera noder. Du kan visa nodkonfigurationer i ditt Automation-konto på sidan för tillstånds konfiguration (DSC). En Node-konfiguration har ett namn med formuläret `ConfigurationName.NodeName` .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **kompilerade konfigurationer** .

   ![Skärm bild av fliken kompilerade konfigurationer](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Aktivera en Azure Resource Manager VM för hantering med tillstånds konfiguration

Du kan använda tillstånds konfiguration för att hantera virtuella Azure-datorer (både klassiska och Resource Manager), lokala virtuella datorer, Linux-datorer, virtuella AWS-datorer och lokala fysiska datorer. I den här artikeln får du lära dig hur du aktiverar endast Azure Resource Manager virtuella datorer. Information om hur du aktiverar andra typer av datorer finns i [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) väljer du fliken **noder** och klickar sedan på **+ Lägg till**.

   ![Skärm bild av sidan DSC-noder som markerar knappen Lägg till virtuell Azure-dator](./media/automation-dsc-getting-started/OnboardVM.png)

1. I fönstret Virtual Machines väljer du den virtuella datorn.
1. I informations fönstret för virtuell dator klickar du på **+ Anslut**.

   > [!IMPORTANT]
   > Den virtuella datorn måste vara en Azure Resource Manager virtuell dator som kör ett [operativ system som stöds](automation-dsc-overview.md#operating-system-requirements).

2. På sidan registrering väljer du namnet på den nod som ska användas för den virtuella datorn i fältet **konfigurations namn för nod** . Det är valfritt att ange ett namn i det här läget. Du kan ändra den tilldelade nodens konfiguration när du har aktiverat noden.

3. Markera **noden omstart om det behövs** och klicka sedan på **OK**.

   ![Skärm bild av registrerings bladet](./media/automation-dsc-getting-started/RegisterVM.png)

   Den Node-konfiguration som du har angett tillämpas på den virtuella datorn med intervall som anges av värdet för **konfigurations läges frekvens**. Den virtuella datorn söker efter uppdateringar av nodens konfiguration enligt intervall som anges av värdet **uppdaterings frekvens** . Mer information om hur dessa värden används finns i [Konfigurera den lokala Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure startar processen med att aktivera den virtuella datorn. När den är klar visas den virtuella datorn på fliken **noder** på sidan för tillstånds konfiguration (DSC) i Automation-kontot.

## <a name="view-the-list-of-managed-nodes"></a>Visa listan över hanterade noder

Du kan visa listan över alla datorer som har Aktiver ATS för hantering i ditt Automation-konto på fliken **noder** på sidan för tillstånds konfiguration (DSC).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan State Configuration (DSC) klickar du på fliken **noder** .

## <a name="view-reports-for-managed-nodes"></a>Visa rapporter för hanterade noder

Varje tids tillstånds konfiguration utför en konsekvens kontroll på en hanterad nod, skickar noden en status rapport tillbaka till hämtnings servern. Du kan visa rapporterna på sidan för noden.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan State Configuration (DSC) klickar du på fliken **noder** . Här kan du se en översikt över konfigurations status och information för varje nod.

   ![Skärm bild av Node-sida](./media/automation-dsc-getting-started/NodesTab.png)

1. På fliken **noder** klickar du på nodens post för att öppna rapporten. Klicka på rapporten som du vill visa mer information om rapporter.

   ![Skärm bild av rapport bladet](./media/automation-dsc-getting-started/NodeReport.png)

På bladet för en enskild rapport kan du se följande status information för motsvarande konsekvens kontroll:

- Rapportens status. Möjliga värden:
    * Kompatibel – noden är kompatibel med kontrollen.
   * Misslyckades – Det gick inte att utföra kontrollen i konfigurationen.
   * Inte kompatibel-noden är i `ApplyandMonitor` läget och datorn har inte önskad status.
- Start tiden för konsekvens kontrollen.
- Den totala körningen för konsekvens kontroll.
- Typ av konsekvens kontroll.
- Eventuella fel, inklusive felkoden och fel meddelandet.
- DSC-resurser som används i konfigurationen och status för varje resurs (oavsett om noden har önskat tillstånd för den resursen). Du kan klicka på varje resurs för att få mer detaljerad information om resursen.
- Nodens namn, IP-adress och konfigurations läge.

Du kan också klicka på **Visa rå rapport** om du vill se de faktiska data som noden skickar till servern.
Mer information om hur du använder dessa data finns i [använda en DSC-rapport Server](/powershell/scripting/dsc/pull-server/reportserver).

Det kan ta lite tid efter att en nod har Aktiver ATS innan den första rapporten är tillgänglig. Du kan behöva vänta upp till 30 minuter för den första rapporten när du har aktiverat en nod.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Tilldela om en nod till en annan nod-konfiguration

Du kan tilldela en nod till att använda en annan nods konfiguration än den som du ursprungligen tilldelade.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan State Configuration (DSC) klickar du på fliken **noder** .
1. På fliken **noder** klickar du på namnet på den nod som du vill omtilldela.
1. Klicka på **tilldela konfiguration av nod** på sidan för noden.

    ![Skärm bild av sidan information om nod som markerar knappen tilldela nod-konfiguration](./media/automation-dsc-getting-started/AssignNode.png)

1. På sidan tilldela konfiguration av nod väljer du den Node-konfiguration som du vill tilldela noden till och klickar sedan på **OK**.

    ![Skärm bild av sidan tilldela konfiguration av nod](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Avregistrera en nod

Om du inte längre vill att en nod ska hanteras av tillstånds konfigurationen kan du avregistrera den.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto klickar du på **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan State Configuration (DSC) klickar du på fliken **noder** .
1. På fliken **noder** klickar du på namnet på den nod som du vill avregistrera.
1. Klicka på **avregistrera** i fönstret för den noden.

    ![Skärm bild av sidan information om noden som markerar knappen avregistrera](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Översikt över Azure Automation tillstånds konfiguration](automation-dsc-overview.md).
- Om du vill aktivera funktionen för virtuella datorer i din miljö, se [aktivera Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).
- För att förstå PowerShell DSC, se [Översikt över önskad tillstånds konfiguration i Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).
