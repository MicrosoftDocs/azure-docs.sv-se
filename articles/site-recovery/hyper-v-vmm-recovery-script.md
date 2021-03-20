---
title: Lägg till ett skript i en återställnings plan i Azure Site Recovery
description: Lär dig hur du lägger till ett VMM-skript i en återställnings plan för haveri beredskap för virtuella Hyper-V-datorer i VMM-moln.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89425882"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Lägga till ett VMM-skript i en återställnings plan

Den här artikeln beskriver hur du skapar ett System Center Virtual Machine Manager-skript (VMM) och lägger till det i en återställnings plan i [Azure Site Recovery](site-recovery-overview.md).

Publicera eventuella kommentarer eller frågor längst ned i den här artikeln eller på [sidan Microsoft Q&en fråga för Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda PowerShell-skript i dina återställnings planer. För att kunna nås från återställnings planen måste du skriva skriptet och placera skriptet i VMM-biblioteket. Tänk på följande när du skriver skriptet:

* Se till att skript använder try-catch-block, så att undantag hanteras på ett smidigt sätt.
    - Om ett undantag inträffar i skriptet, slutar skriptet att köras och uppgiften visas som misslyckad.
    - Om ett fel inträffar körs inte resten av skriptet.
    - Om ett fel inträffar när du kör en oplanerad redundansväxling fortsätter återställnings planen.
    - Om ett fel inträffar när du kör en planerad redundansväxling stoppas återställnings planen. Korrigera skriptet, kontrol lera att det fungerar som förväntat och kör sedan återställnings planen igen.
        - `Write-Host`Kommandot fungerar inte i ett återställnings plan skript. Om du använder `Write-Host` kommandot i ett skript, Miss lyckas skriptet. Skapa utdata genom att skapa ett proxy-skript som i sin tur kör huvud skriptet. Använd kommandot för att se till att alla utdata är skickas **\>\>** .
        - Skriptet nådde tids gränsen om det inte returneras inom 600 sekunder.
        - Om något skrivs till STDERR klassificeras skriptet som misslyckat. Den här informationen visas i skript körnings informationen.

* Skript i en återställnings plan körs i kontexten för VMM-tjänstkontot. Se till att det här kontot har Läs behörighet för den fjär resurs som skriptet finns på. Testa skriptet så att det körs med samma nivå av användar rättigheter som VMM-tjänstkontot.
* VMM-cmdlets levereras i en Windows PowerShell-modul. Modulen installeras när du installerar VMM-konsolen. Om du vill läsa in modulen i skriptet använder du följande kommando i skriptet: 

    `Import-Module -Name virtualmachinemanager`

    Mer information finns i [Kom igång med Windows PowerShell och VMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12)).
* Se till att du har minst en biblioteks server i VMM-distributionen. Som standard finns sökvägen till biblioteks resursen för en VMM-server lokalt på VMM-servern. Mappnamnet är MSCVMMLibrary.

  Om din biblioteks resurs Sök väg är fjärran sluten (eller om den är lokal men inte delas med MSCVMMLibrary) konfigurerar du resursen på följande sätt med hjälp av \\ libserver2. contoso. com\share\ som exempel:
  
  1. Öppna Registereditorn och gå sedan till **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Ändra värdet för **ScriptLibraryPath** till **\\ \libserver2.contoso.com\share \\**. Ange fullständigt fullständigt domän namn. Ange behörigheter till resursens plats. Detta är rot-noden för resursen. Om du vill kontrol lera rotnoden i VMM går du till rotnoden i biblioteket. Sökvägen som öppnas är roten till sökvägen. Det här är den sökväg som du måste använda i variabeln.

  1. Testa skriptet med hjälp av ett användar konto som har samma nivå av användar rättigheter som VMM-tjänstkontot. Genom att använda dessa användar rättigheter verifieras att de fristående, testade skripten körs på samma sätt som de körs i återställnings planer. På VMM-servern anger du körnings principen som ska kringgås enligt följande:

     a. Öppna **Windows PowerShell-konsolen 64-bitars** som administratör.
     
     b. Ange **set-ExecutionPolicy bypass**. Mer information finns i [använda Set-ExecutionPolicy-cmdlet](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

     > [!IMPORTANT]
     > Ange **set-ExecutionPolicy kringgå** endast i 64-bitars PowerShell-konsolen. Om du ställer in den för 32-bitars PowerShell-konsolen körs inte skripten.

## <a name="add-the-script-to-the-vmm-library"></a>Lägg till skriptet i VMM-biblioteket

Om du har en VMM-datakälla kan du skapa ett skript på VMM-servern. Ta sedan med skriptet i din återställnings plan.

1. Skapa en ny mapp i biblioteks resursen. Till exempel \<VMM server name> \MSSCVMMLibrary\RPScripts. Placera mappen på käll-och mål VMM-servrarna.
1. Skapa skriptet. Namnge till exempel skriptet RPScript. Kontrol lera att skriptet fungerar som förväntat.
1. Placera skriptet i \<VMM server name> mappen \MSSCVMMLibrary på käll-och mål VMM-servrarna.

## <a name="add-the-script-to-a-recovery-plan"></a>Lägg till skriptet i en återställnings plan

När du har lagt till virtuella datorer eller replikeringsgrupper i en återställnings plan och skapat planen kan du lägga till skriptet i gruppen.

1. Öppna återställnings planen.
1. I listan **steg** väljer du ett objekt. Välj sedan antingen **skript** eller **manuell åtgärd**.
1. Ange om du vill lägga till skriptet eller åtgärden före eller efter det valda objektet. Klicka på knapparna **Flytta upp** och **Flytta ned** om du vill flytta upp eller ned skript positionen.
1. Om du lägger till ett VMM-skript väljer du **redundans till VMM-skript**. Ange den relativa sökvägen till resursen i **skript Sök väg**. Ange till exempel **\RPScripts\RPScript.PS1**.
1. Om du lägger till en Azure Automation Runbook anger du det Automation-konto som Runbook finns i. Välj sedan det Azure Runbook-skript som du vill använda.
1. Kontrol lera att skriptet fungerar som förväntat genom att göra ett redundanstest för återställnings planen.


## <a name="next-steps"></a>Nästa steg
* Läs mer om att [köra redundans](site-recovery-failover.md).

