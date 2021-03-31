---
title: Programkonsekventa säkerhets kopieringar av virtuella Linux-datorer
description: Skapa programkonsekventa säkerhets kopieringar av dina virtuella Linux-datorer till Azure. Den här artikeln förklarar hur du konfigurerar skript ramverket för att säkerhetskopiera virtuella Linux-datorer som distribueras i Azure. Den här artikeln innehåller också felsöknings information.
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 22053004026a2dd8976027359f11d50a5663b334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88999248"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Programkonsekventa säkerhetskopior av virtuella Linux-datorer på Azure

När du tar ögonblicks bilder av dina virtuella datorer innebär applikations konsekvens att dina program startar när VM-starten startas efter att ha återställts. Som du kan föreställa dig är programmets konsekvens mycket viktigt. För att se till att dina virtuella Linux-datorer är konsekventa kan du använda Linux pre-script och post-script för att ta programkonsekventa säkerhets kopieringar. För skript och efter skript stöder Azure Resource Manager distribuerade virtuella Linux-datorer. Skript för tillämpnings konsekvens stöder inte Service Manager-distribuerade virtuella datorer eller virtuella Windows-datorer.

## <a name="how-the-framework-works"></a>Så här fungerar ramverket

Ramverket innehåller ett alternativ för att köra anpassade för skript och efter skript när du tar ögonblicks bilder av virtuella datorer. För skript körs precis innan du tar ögonblicks bilden av den virtuella datorn och efter att du har skapat ögonblicks bilden av den virtuella datorn. För skript och post-skript ger flexibiliteten att styra ditt program och din miljö, medan du tar ögonblicks bilder av virtuella datorer.

För skript anropar interna program-API: er, som ingick i IOs och tömde minnes innehåll till disken. De här åtgärderna säkerställer att ögonblicks bilden är programkonsekvent. Efter skript används inbyggda API: er för att tina upp IOs, vilket gör att programmet kan återuppta normala åtgärder efter ögonblicks bilden av den virtuella datorn.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Steg för att konfigurera för skript och efter skript

1. Logga in som rot användaren på den virtuella Linux-dator som du vill säkerhetskopiera.

2. Från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig)hämtar du **VMSnapshotScriptPluginConfig.jspå** och kopierar den till mappen **/etc/Azure** för alla virtuella datorer som du vill säkerhetskopiera. Om mappen **/etc/Azure** inte finns skapar du den.

3. Kopiera skriptet för för skript och efter skript för ditt program på alla virtuella datorer som du planerar att säkerhetskopiera. Du kan kopiera skripten till valfri plats på den virtuella datorn. Se till att uppdatera den fullständiga sökvägen till skriptfilerna i **VMSnapshotScriptPluginConfig.jspå** filen.

4. Se till att du har följande behörigheter för de här filerna:

   - **VMSnapshotScriptPluginConfig.jspå**: behörighet "600". Till exempel måste bara "rot" användaren ha behörigheterna Läs och skriv för den här filen, och ingen användare ska ha behörigheten "kör".

   - **För skript fil**: behörighet "700".  Till exempel måste bara "rot" användaren ha behörigheterna "läsa", "skriva" och "kör" för den här filen.

   - **Efter skript** Behörighet "700". Till exempel måste bara "rot" användaren ha behörigheterna "läsa", "skriva" och "kör" för den här filen.

   > [!IMPORTANT]
   > Ramverket ger användarna mycket kraft. Skydda ramverket och se till att endast rot användaren har åtkomst till kritiska JSON-och skriptfiler.
   > Om kraven inte uppfylls, körs inte skriptet, vilket resulterar i en krasch och inkonsekvent säkerhets kopiering av fil systemet.
   >

5. Konfigurera **VMSnapshotScriptPluginConfig.jspå** som beskrivs här:
    - **pluginName**: lämna det här fältet som det är, eller så kanske dina skript inte fungerar som förväntat.

    - **preScriptLocation**: Ange den fullständiga sökvägen till för skriptet på den virtuella datorn som ska säkerhets kopie ras.

    - **postScriptLocation**: Ange den fullständiga sökvägen till post-skriptet på den virtuella datorn som ska säkerhets kopie ras.

    - **preScriptParams**: Ange de valfria parametrar som måste skickas till för skriptet. Alla parametrar ska vara i citat tecken. Avgränsa parametrarna med kommatecken om du använder flera parametrar.

    - **postScriptParams**: Ange de valfria parametrarna som måste skickas till post-skriptet. Alla parametrar ska vara i citat tecken. Avgränsa parametrarna med kommatecken om du använder flera parametrar.

    - **preScriptNoOfRetries**: Ange hur många gånger för skriptet ska provas igen om det uppstår något fel innan det avslutas. Noll innebär bara ett försök och inget nytt försök om det uppstår ett fel.

    - **postScriptNoOfRetries**: Ange hur många gånger det ska göras ett nytt försök att utföra ett fel innan du avslutar. Noll innebär bara ett försök och inget nytt försök om det uppstår ett fel.

    - **timeoutInSeconds**: ange enskilda tids gränser för för skriptet och efter skriptet (maximalt värde kan vara 1800).

    - **continueBackupOnFailure**: Ange det här värdet till **Sant** om du vill att Azure Backup ska återgå till en konsekvent eller kraschad fil system-konsekvent säkerhets kopiering om det inte går att använda skript eller efter skript. Om värdet är **false** Miss lyckas säkerhets kopieringen om det finns ett skript fel (förutom när du har en virtuell dator med en enda disk som går tillbaka till en kraschad säkerhets kopiering oberoende av den här inställningen). Om **continueBackupOnFailure** -värdet är inställt på False görs ett nytt försök att säkerhetskopiera, om säkerhets kopieringen Miss lyckas, baserat på en logik för återförsök i tjänsten (för det specificerade antalet försök).

    - **fsFreezeEnabled**: Ange om Linux-fsfreeze ska anropas när du tar ögonblicks bilden av den virtuella datorn för att säkerställa fil systemets konsekvens. Vi rekommenderar att den här inställningen är **True** , om inte programmet har ett beroende vid inaktive ring av fsfreeze.

    - **ScriptsExecutionPollTimeSeconds**: Ange tiden som tillägget måste försättas i vilo läge mellan varje pollning till skript körningen. Om värdet till exempel är 2, kontrollerar tillägget om körningen av skript har slutförts var 2: e sekund. Det lägsta och högsta värdet kan ta 1 respektive 5. Värdet ska vara ett heltal.

6. Skript ramverket har nu kon figurer ATS. Om säkerhets kopieringen av den virtuella datorn redan har kon figurer ATS anropar nästa säkerhets kopiering skripten och utlöser programkonsekvent säkerhets kopiering. Om säkerhets kopian av den virtuella datorn inte har kon figurer ATS konfigurerar du den med hjälp av [säkerhetskopiera virtuella Azure-datorer till Recovery Services valv.](./backup-azure-vms-first-look-arm.md)

## <a name="troubleshooting"></a>Felsökning

Se till att du lägger till lämplig loggning när du skriver ditt för skript och efter skript och granska skript loggarna för att åtgärda eventuella skript problem. Om du fortfarande har problem med att köra skript, se följande tabell för mer information.

| Fel | Felmeddelande | Rekommenderad åtgärd |
| ------------------------ | -------------- | ------------------ |
| ScriptExecutionFailed |För skriptet returnerade ett fel, så säkerhets kopieringen kanske inte är programkonsekvent.| Åtgärda problemet genom att titta på fel loggarna för skriptet.|  
|Efter ScriptExecutionFailed |Efter skriptet returnerade ett fel som kan påverka program status. |Granska fel loggarna för skriptet för att åtgärda problemet och kontrol lera program statusen. |
| ScriptNotFound |För skriptet hittades inte på den plats som anges i **VMSnapshotScriptPluginConfig.js** i konfigurations filen. |Kontrol lera att för skript finns på den sökväg som anges i konfigurations filen för att säkerställa programkonsekvent säkerhets kopiering.|
| Efter ScriptNotFound |Det gick inte att hitta efter skript på den plats som anges i **VMSnapshotScriptPluginConfig.js** i konfigurations filen. |Se till att efter skript finns på den sökväg som anges i konfigurations filen för att säkerställa programkonsekvent säkerhets kopiering.|
| IncorrectPluginhostFile |**Pluginvärdfilen** -filen som medföljer VmSnapshotLinux-tillägget är skadad, så det går inte att köra för skript och post-script och säkerhets kopieringen kommer inte att vara konsekvent.| Avinstallera **VmSnapshotLinux** -tillägget och ominstalleras automatiskt med nästa säkerhets kopiering för att åtgärda problemet. |
| IncorrectJSONConfigFile | Filen **VMSnapshotScriptPluginConfig.js** är felaktig, så det går inte att köra för skript och post-script och säkerhets kopieringen kommer inte att vara konsekvent. | Ladda ned kopian från [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) och konfigurera den igen. |
| InsufficientPermissionforPre-Script | För att skript ska kunna köras måste rot användaren vara ägare till filen och filen ska ha "700"-behörigheter (det vill säga endast "ägare" måste ha behörigheterna "läsa", "skriva" och "köra"). | Se till att "rot" användaren är ägare till skript filen och att endast "ägare" har behörigheterna "läsa", "skriva" och "kör". |
| InsufficientPermissionforPost-Script | För att skript ska kunna köras bör rot användaren vara ägare till filen och filen ska ha "700"-behörigheter (det vill säga endast "ägare" måste ha behörigheterna "läsa", "skriva" och "köra"). | Se till att "rot" användaren är ägare till skript filen och att endast "ägare" har behörigheterna "läsa", "skriva" och "kör". |
| Före-ScriptTimeout | Tids gränsen nåddes för körning av programkonsekvent säkerhets kopiering för skript. | Kontrol lera skriptet och öka tids gränsen i **VMSnapshotScriptPluginConfig.jspå** filen som finns på **/etc/Azure**. |
| Post-ScriptTimeout | Tids gränsen nåddes för körningen av programkonsekvent säkerhets kopierings post-skriptet. | Kontrol lera skriptet och öka tids gränsen i **VMSnapshotScriptPluginConfig.jspå** filen som finns på **/etc/Azure**. |

## <a name="next-steps"></a>Nästa steg

[Konfigurera VM-säkerhetskopiering till ett Recovery Services-valv](./backup-azure-vms-first-look-arm.md)
