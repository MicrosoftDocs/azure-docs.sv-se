---
title: Konfigurera ett labb för ArcMap\ArcGIS Desktop med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för klasser med ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740112"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Konfigurera ett labb för ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) är en typ av geografiskt informations system (GIS).  ArcGIS används för att make\analyze Maps och arbeta med geografiska data som tillhandahålls av ESRI ( [Environment Systems Research Institute](https://www.esri.com/en-us/home) ).  Även om ArcGIS Desktop innehåller flera program, visar den här artikeln hur du konfigurerar labb för att använda ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) används för att skapa, redigera och analysera 2D-kartor.

## <a name="lab-configuration"></a>Labb konfiguration

För att börja konfigurera ett labb för att använda ArcMap behöver du ett Azure-prenumerations-och labb konto.  Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services.  Mer information om hur du skapar ett nytt labb konto finns i [Konfigurera ett labb konto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna för ditt labb konto enligt beskrivningen i följande tabell.  Mer information om hur du aktiverar Azure Marketplace-avbildningar finns i [Ange de Azure Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
|Marketplace-avbildning| Aktivera Windows 10 Pro-eller Windows 10 Pro N-avbildningen för användning i ditt labb konto.|

### <a name="licensing-server"></a>Licensierings Server

En typ av licensiering som ArcGIS Desktop erbjuder är [samtidig användnings licenser](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Detta kräver att du installerar ArcGIS License Manager på licens servern.  Licens hanteraren håller reda på antalet kopior av program vara som kan köras samtidigt.  Mer information om hur du konfigurerar licens hanteraren på servern finns i [License Manager-guiden](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Licens servern finns vanligt vis antingen i det lokala nätverket eller på en virtuell Azure-dator i ett virtuellt Azure-nätverk.  När licens servern har kon figurer ATS måste du koppla [det virtuella nätverket](./how-to-connect-peer-virtual-network.md) till ditt [labb konto](./tutorial-setup-lab-account.md).  Du måste använda nätverks-peering innan du skapar labbet så att dina virtuella labb datorer kan komma åt licens servern och vice versa.

Mer information finns i [Konfigurera en licens server som en delad resurs](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Labb inställningar

Storleken på den virtuella datorn (VM) som vi rekommenderar att du använder för ArcGIS Desktop beror på vilka program, tillägg och de olika versioner som eleverna kommer att använda.  Storleken på den virtuella datorn beror också på de arbets belastningar som eleverna förväntas utföra.  Se [ArcGIS Desktop system-krav](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) för att identifiera storleken på den virtuella datorn.  När du har identifierat den potentiella storleken på den virtuella datorn rekommenderar vi att du testar dina studenters arbets belastningar för att säkerställa tillräcklig prestanda.

I den här artikeln rekommenderar vi att du [ **använder den** virtuella dator storleken](administrator-guide.md#vm-sizing) för [10.7.1 av ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), förutsatt att inga andra ArcGIS Desktop-tillägg används.  Beroende på vilka behov som finns i din klass kan du dock behöva en **stor** eller till och med en **Small\Medium GPU (visualisering) VM-** storlek.  Till exempel har [tillägget spatial analytiker](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) som ingår i ArcGIS Desktop stöd för en GPU för bättre prestanda, men kräver inte någon GPU.

| Labb inställning | Värde och beskrivning |
| ------------ | ------------------ |
|Storlek på virtuell dator| **Medel**.  Passar bäst för Relations databaser, minnes intern cachelagring och analys.|  

### <a name="template-machine"></a>Mall dator

Stegen i det här avsnittet visar hur du konfigurerar mallen VM:

1.  Starta mallen VM och Anslut till datorn med RDP.

2.  Hämta och installera ArcGIS Desktop-komponenter med hjälp av anvisningarna från av ESRI.  De här stegen omfattar att tilldela License Manager för samtidig användnings licensiering: 
    - [Introduktion till installation och konfiguration av ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Konfigurera extern lagring för säkerhets kopiering för studenter.  Studenter kan spara filer direkt till den tilldelade virtuella datorn eftersom alla ändringar som de gör sparas mellan sessioner.  Vi rekommenderar dock att eleverna säkerhetskopierar sitt arbete till lagring som är externt från den virtuella datorn av några möjliga orsaker:
    - För att göra det möjligt för studenter att komma åt sitt arbete när klassen och labbet slutar.  
    - Om studenten får sin virtuella dator till ett felaktigt tillstånd och deras avbildning måste [återställas](how-to-set-virtual-machine-passwords.md#reset-vms).

    Med ArcGIS bör varje student säkerhetskopiera följande filer i slutet av varje arbets-session:

    - MXD-fil som lagrar layoutinformation för ett projekt.
    - Fildatabaser, som lagrar alla data som produceras av ArcGIS.
    - Andra data som studenten kan använda som rastrerade filer, formfiler, GeoTIFF osv.

    Vi rekommenderar att du använder OneDrive för lagring av säkerhets kopior.  Följ anvisningarna i artikeln [Installera och konfigurera OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive)för att konfigurera OneDrive på den virtuella mallen. 

4.  [Publicera](how-to-create-manage-template.md#publish-the-template-vm) sedan mallen VM för att skapa eleverna.

### <a name="auto-shutdown-and-disconnect-settings"></a>Inställningar för automatisk avstängning och från koppling

Med inställningarna för [Automatisk avstängning och från koppling](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) kan du se till att en ELEVS virtuella dator stängs av när den inte används.  De här inställningarna ska ställas in enligt de typer av arbets belastningar som dina studenter kommer att utföra så att deras virtuella datorer inte stängs av i mitten av sitt arbete.  Om du till exempel kopplar från **användare när de virtuella datorerna är inaktiva** kopplas studenten från RDP-sessionen när inga möss-eller tangent bords indata har identifierats under en viss tid.  Den här inställningen måste tillåta tillräckligt med tid för arbets belastningar där studenten inte aktivt använder musen eller tangent bordet, t. ex. för att köra långa frågor eller vänta på åter givning.

För ArcGIS rekommenderar vi följande värden för de här inställningarna:
- Koppla från användare när virtuella datorer är inaktiva
    - 30 minuter efter inaktivt tillstånd har identifierats
- Stäng av virtuella datorer när användare kopplar från
    - 15 minuter efter att användaren kopplats från

## <a name="cost"></a>Cost

Vi ska se en möjlig kostnads uppskattning för den här klassen. Den här beräkningen omfattar inte kostnaden för att köra licens servern. Vi använder en klass av 25 studenter. Det finns 20 timmar med den schemalagda klass tiden. Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider. Storleken på den virtuella datorn som vi valde var **medel**, vilket är 42 lab-enheter.

25 studenter \* (20 schemalagda timmar + 10 kvot timmar) \* 42 lab-enheter * 0,01 USD per timme = 315,00 USD

>[!IMPORTANT]
> Kostnads uppskattning är till exempel endast syfte.  Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)