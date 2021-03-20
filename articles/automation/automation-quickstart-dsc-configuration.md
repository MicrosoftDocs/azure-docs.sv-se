---
title: Azure snabb start – konfigurera en virtuell dator med önskad tillstånds konfiguration | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med att konfigurera en virtuell dator med önskad tillstånds konfiguration.
services: automation
ms.subservice: dsc
keywords: dsc, configuration, automation
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e7fec2bee61844ac294e5463bd5bc88ec3fb5e98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86186086"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>Konfigurera en virtuell dator med önskad tillstånds konfiguration

Genom att aktivera Azure Automation tillstånds konfiguration kan du hantera och övervaka konfigurationerna för dina Windows-och Linux-servrar med önskad tillstånds konfiguration (DSC). Konfigurationer som går från en önskad konfiguration kan identifieras eller automatiskt korrigeras. Den här snabb starten beskriver hur du aktiverar en virtuell Linux-dator och distribuerar en LAMP-stack med Azure Automation tillstånds konfiguration.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](./manage-runas-account.md).
* En Azure Resource Manager virtuell dator (inte klassisk) som kör Red Hat Enterprise Linux, CentOS eller Oracle Linux. Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Linux-dator i Azure Portal](../virtual-machines/linux/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure på https://portal.azure.com .

## <a name="enable-a-virtual-machine"></a>Aktivera en virtuell dator

Det finns många olika metoder för att aktivera en dator för tillstånds konfigurations funktion. Den här snabb starten visar hur du aktiverar funktionen för en virtuell dator med ett Automation-konto. Du kan lära dig mer om olika metoder för att aktivera dina datorer för tillstånds konfiguration genom [att läsa aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](./automation-dsc-onboarding.md).

1. I det vänstra fönstret i Azure Portal väljer du **Automation-konton**. Om den inte visas i den vänstra rutan klickar du på **alla tjänster** och söker i den resulterande vyn.
1. Välj ett Automation-konto i listan.
1. I det vänstra fönstret i Automation-kontot väljer du **Tillståndskonfiguration (DSC)**.
2. Klicka på **Lägg till** att öppna sidan för att välja virtuell dator.
3. Hitta den virtuella dator som du vill aktivera DSC för. Du kan använda sökfältet och filteralternativ för att hitta en viss virtuell dator.
4. Klicka på den virtuella datorn och klicka sedan på **Anslut**
5. Välj lämpliga DSC-inställningar för den virtuella datorn. Om du redan har för berett en konfiguration kan du ange den som `Node Configuration Name` . Du kan ställa in [konfigurationsläget](/powershell/scripting/dsc/managing-nodes/metaConfig) på att styra datorns konfigurationsbeteende.
6. Klicka på **OK**. När DSC-tillägget distribueras till den virtuella datorn visas statusen som `Connecting` .

![Aktivera en virtuell Azure-dator för DSC](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Importera moduler

Moduler innehåller DSC-resurser och många hittar du i [PowerShell-galleriet](https://www.powershellgallery.com). Alla resurser som används i dina konfigurationer måste importeras till Automation-kontot innan de kompileras. För de här självstudierna krävs modulen med namnet **nx**.

1. I det vänstra fönstret i Automation-kontot väljer du **moduler-Galleri** under **delade resurser**.
1. Sök efter den modul som ska importeras genom att skriva en del av namnet: `nx` .
1. Klicka på den modul som ska importeras.
1. Klicka på **Importera**.

![Importera en DSC-modul](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>Importera konfigurationen

I den här snabbstarten används en DSC-konfiguration som konfigurerar Apache HTTP Server, MySQL och PHP på datorn. Se [DSC-konfigurationer](/powershell/scripting/dsc/configurations/configurations).

Skriv följande i en text redigerare och spara den lokalt som **AMPServer.ps1**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

Importera konfigurationen:

1. I det vänstra fönstret i Automation-kontot väljer du **Tillståndskonfiguration (DSC)** och klickar sedan på fliken **Konfigurationer**.
2. Klicka på **+ Lägg till**.
3. Välj den konfigurations fil som du sparade i föregående steg.
4. Klicka på **OK**.

## <a name="compile-a-configuration"></a>Kompilera en konfiguration

Du måste kompilera en DSC-konfiguration till en Node-konfiguration (MOF-dokument) innan den kan tilldelas till en nod. Kompilering verifierar konfigurationen och tillåter indata för parametervärden. Mer information om hur du kompilerar en konfiguration finns [i kompilera konfigurationer i tillstånds konfiguration](automation-dsc-compile.md).

1. I det vänstra fönstret i Automation-kontot väljer du **tillstånds konfiguration (DSC)** och klickar sedan på fliken **konfigurationer** .
1. Välj konfigurationen `LAMPServer` .
1. Välj **kompilera** i meny alternativen och klicka sedan på **Ja**.
1. I vyn konfiguration visas ett nytt kompileringsfel i kö. När jobbet har slutförts kan du gå vidare till nästa steg. Om det finns några problem kan du klicka på compile-jobbet för mer information.

## <a name="assign-a-node-configuration"></a>Tilldela en nodkonfiguration

Du kan tilldela en kompilerad Node-konfiguration till en DSC-nod. Tilldelningen tillämpar konfigurationen på datorn och övervakas eller autokorrigeras för eventuella inriktningar från den konfigurationen.

1. I det vänstra fönstret i Automation-kontot väljer du **tillstånds konfiguration (DSC)** och klickar sedan på fliken **noder** .
1. Välj den nod som du vill tilldela en konfiguration till.
1. Klicka på **Tilldela nodkonfiguration**
1. Välj noden konfiguration `LAMPServer.localhost` och klicka på **OK**. Tillstånds konfigurationen tilldelar nu den kompilerade konfigurationen till noden och nodens status ändras till `Pending` . Vid nästa periodiska kontroll hämtar noden konfigurationen, tillämpar den och rapporterar status. Det kan ta upp till 30 minuter för noden att hämta konfigurationen, beroende på inställningarna för noden. 
1. Om du vill tvinga fram en omedelbar kontroll kan du köra följande kommando lokalt på den virtuella Linux-datorn: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Tilldela en nodkonfiguration](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Visa nod-status

Du kan visa status för alla hanterade noder för tillstånds konfiguration i ditt Automation-konto. Informationen visas genom att välja **tillstånds konfiguration (DSC)** och klicka på fliken **noder** . Du kan filtrera visningen efter status, konfiguration av nod eller namns ökning.

![DSC-nodstatus](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten aktiverade du en virtuell Linux-dator för tillstånds konfiguration, skapade en konfiguration för en LAMP-stack och distribuerade konfigurationen till den virtuella datorn. Om du vill veta hur du kan använda Azure Automation tillstånds konfiguration för att aktivera kontinuerlig distribution fortsätter du till artikeln:

> [!div class="nextstepaction"]
> [Konfigurera kontinuerlig distribution med Chocolatey](./automation-dsc-cd-chocolatey.md)
