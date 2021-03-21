---
title: Hantera Ändringsspårning och inventering i Azure Automation
description: Den här artikeln beskriver hur du använder Ändringsspårning och inventering för att spåra program-och Microsoft-tjänsteändringar i din miljö.
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572641"
---
# <a name="manage-change-tracking-and-inventory"></a>Hantera ändringsspårning och inventering

När du lägger till en ny fil eller register nyckel som ska spåras aktiverar Azure Automation för [ändringsspårning och inventering](overview.md). Den här artikeln beskriver hur du konfigurerar spårning, granskar spårnings resultat och hanterar aviseringar när ändringar upptäcks.

Innan du använder procedurerna i den här artikeln bör du kontrol lera att du har aktiverat Ändringsspårning och inventering på dina virtuella datorer med någon av följande metoder:

* [Aktivera Ändringsspårning och inventering från ett Automation-konto](enable-from-automation-account.md)
* [Aktivera Ändringsspårning och inventering genom att bläddra i Azure Portal](enable-from-portal.md)
* [Aktivera Ändringsspårning och inventering från en runbook](enable-from-runbook.md)
* [Aktivera Ändringsspårning och inventering från en virtuell Azure-dator](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Begränsa omfånget för distributionen

Ändringsspårning och inventering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot ändringar. Mer information finns i [begränsa ändringsspårning och distributions omfång för lager](manage-scope-configurations.md).

## <a name="track-files"></a>Spåra filer

Du kan använda Ändringsspårning och inventering för att spåra ändringar i filer och mappar/kataloger. Det här avsnittet beskriver hur du konfigurerar fil spårning i Windows och på Linux.

### <a name="configure-file-tracking-on-windows"></a>Konfigurera fil spårning i Windows

Använd följande steg för att konfigurera fil spårning på Windows-datorer:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Automation-konton**.

3. I listan över Automation-konton väljer du det konto som du valde när du aktiverade Ändringsspårning och inventering.

4. I ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**.

5. Välj **Redigera inställningar** (kugg hjuls symbolen).

6. På sidan konfiguration av arbets yta väljer du **Windows-filer** och klickar sedan på **+ Lägg** till för att lägga till en ny fil som ska spåras.

7. I fönstret Lägg till Windows-fil för Ändringsspårning anger du informationen för filen eller mappen som ska spåras och klickar på **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om inställningen tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på filen som ska spåras.        |
    |Group     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **c:\Temp \\ \* . txt**. Du kan också använda miljövariabler, till exempel `%winDir%\System32\\\*.*` .       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är File och Folder.        |    
    |Rekursion     | Sant om rekursion används vid sökning efter objektet som ska spåras och falskt annars.        |    
    |Ladda upp fil innehåll | Sant om du vill överföra fil innehåll på spårade ändringar och annars FALSE.|

    Om du planerar att konfigurera övervakning av filer och mappar med jokertecken bör du tänka på följande:

    - Jokertecken krävs för att spåra flera filer.
    - Jokertecken kan endast användas i det sista segmentet i en sökväg, till exempel *C:\folder\file* eller */etc/*. conf *
    - Om en miljö variabel innehåller en sökväg som inte är giltig kommer verifieringen att lyckas, men sökvägen Miss lyckas När inventeringen körs.
    - När du anger sökvägen bör du undvika allmänna sökvägar, till exempel *c:*. * *, vilket leder till att för många mappar passerar.

8. Se till att du anger true för **Ladda upp fil innehåll**. Med den här inställningen aktive ras spårning av fil innehåll för den angivna fil Sök vägen.

### <a name="configure-file-tracking-on-linux"></a>Konfigurera fil spårning i Linux

Använd följande steg för att konfigurera fil spårning på Linux-datorer:

1. Välj **Redigera inställningar** (kugg hjuls symbolen).

2. På sidan konfiguration av arbets yta väljer du **Linux-filer** och väljer sedan **+ Lägg** till för att lägga till en ny fil som ska spåras.

3. På sidan **Lägg till Linux-fil för ändringsspårning** anger du informationen för filen eller katalogen som ska spåras och väljer sedan **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om inställningen tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på filen som ska spåras.        |
    |Group     | Ett grupp namn för logisk gruppering av filer.        |
    |Ange sökväg     | Sökvägen för att söka efter filen, till exempel **/etc/*. conf**.       |
    |Sökvägstyp     | Typ av sökväg. Möjliga värden är fil och katalog.        |
    |Rekursion     | Sant om rekursion används vid sökning efter objektet som ska spåras och falskt annars.        |
    |Använda Sudo     | Sant om du vill använda sudo vid sökning efter objektet och annars FALSE.         |
    |Länkar     | Inställning som avgör hur symboliska länkar ska hanteras när du bläddrar i kataloger. Möjliga värden:<br> IGNORE-ignorerar symboliska länkar och inkluderar inte de filer/kataloger som refereras.<br>Följ – följer de symboliska länkarna under rekursion och inkluderar även de filer/kataloger som refereras till.<br>Hantera – följer de symboliska länkarna och tillåter ändring av returnerat innehåll.<br>**Obs:** Alternativet hantera rekommenderas inte eftersom det inte stöder hämtning av fil innehåll.    |
    |Ladda upp fil innehåll | Sant om du vill överföra fil innehåll på spårade ändringar och annars FALSE. |

4. Se till att du anger **True** för **Ladda upp fil innehåll**. Med den här inställningen aktive ras spårning av fil innehåll för den angivna fil Sök vägen.

   ![Lägg till Linux-fil](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Spåra fil innehåll

Med fil innehålls spårning kan du visa innehållet i en fil före och efter en spårad ändring. Funktionen sparar fil innehållet till ett [lagrings konto](../../storage/common/storage-account-overview.md) när varje ändring sker. Här följer några regler som du följer när du spårar fil innehåll:

* Ett standard lagrings konto som använder distributions modellen för Resource Manager krävs för att lagra fil innehåll.
* Som standard godkänner lagringskonton anslutningar från klienter i alla nätverk. Om du har skyddat ditt lagrings konto så att det bara tillåter viss trafik, måste du ändra konfigurations reglerna så att ditt Automation-konto kan ansluta till det. Se [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md).
* Använd inte lagrings konton för Premium-och klassiska distributions modeller. Se [om Azure Storage-konton](../../storage/common/storage-account-create.md).
* Du kan bara ansluta lagrings kontot till ett Automation-konto.
* Ändringsspårning och inventering måste vara aktiverat i ditt Automation-konto.

### <a name="enable-tracking-for-file-content-changes"></a>Aktivera spårning av ändringar i fil innehåll

Använd följande steg för att aktivera spårning av ändringar i fil innehåll:

1. Välj **Redigera inställningar** (kugg hjuls symbolen).

2. Välj **fil innehåll** och välj sedan **länk**. Det här alternativet öppnar sidan **Lägg till innehålls plats för ändringsspårning** .

   ![Lägg till innehålls plats](./media/manage-change-tracking/enable.png)

3. Välj den prenumeration och det lagrings konto som ska användas för att lagra filens innehåll.

5. Om du vill aktivera spårning av fil innehåll för alla befintliga spårade filer väljer du **på** för att **Ladda upp fil innehåll för alla inställningar**. Du kan ändra den här inställningen för varje fil Sök väg senare.

   ![Ange lagrings konto](./media/manage-change-tracking/storage-account.png)

6. Ändringsspårning och inventering visar URI: er för lagrings konto och signatur för delad åtkomst (SAS) när ändrings spårning av fil innehåll aktive ras. Signaturerna upphör att gälla efter 365 dagar och du kan återskapa dem genom att välja **Återskapa**.

   ![Lista konto nycklar](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårad fil

När Ändringsspårning och inventeringen upptäcker en ändring av en spårad fil kan du Visa filens innehåll i fönstret ändra information.  

![Lista ändringar](./media/manage-change-tracking/change-list.png)

1. På sidan **ändrings spårning** från ditt Automation-konto väljer du en fil i listan över ändringar och väljer sedan **Visa fil innehålls ändringar** för att se innehållet i filen. I fönstret ändrings information visas fil informationen standard före och efter för varje egenskap.

   ![Ändra information](./media/manage-change-tracking/change-details.png)

2. Du visar fil innehållet i en sida-vid-sida-vy. Du kan välja **infogade** för att visa en infogad vy över ändringarna.

## <a name="track-registry-keys"></a>Spåra register nycklar

Använd följande steg för att konfigurera register nyckel spårning på Windows-datorer:

1. På sidan **ändrings spårning** från ditt Automation-konto väljer du **Redigera inställningar** (kugg hjuls symbolen).

2. På sidan konfiguration av arbets yta väljer du **Windows-registret**.

3. Välj **+ Lägg** till för att lägga till en ny register nyckel som ska spåras.

4. På sidan **Lägg till Windows-register för ändringsspårning** anger du informationen för nyckeln som ska spåras och väljer sedan **Spara**. I följande tabell definieras de egenskaper som du kan använda för informationen. När du anger en register Sök väg måste den vara nyckeln och inte ett värde.

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Enabled     | Sant om en inställning tillämpas och annars FALSE.        |
    |Objektnamn     | Eget namn på register nyckeln som ska spåras.        |
    |Group     | Grupp namn för register nycklar för logisk gruppering.        |
    |Windows-registernyckel   | Nyckel namn med sökväg, till exempel `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Sök efter loggar för ändrings poster

Du kan utföra olika sökningar mot Azure Monitor loggar för ändrings poster. När sidan ändrings spårning är öppen klickar du på **Log Analytics** för att öppna sidan loggar. Följande tabell innehåller exempel på loggs ökningar för ändrings poster.

|Fråga  |Beskrivning  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Visar de senaste inventerings posterna för Microsoft-tjänster som har ställts in på auto men som har rapporter ATS som stoppade. Resultaten är begränsade till den senaste posten för det angivna program namnet och datorn.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Visar ändrings poster för borttagen program vara.|

## <a name="next-steps"></a>Nästa steg

* Information om scope-konfigurationer finns i [begränsa ändringsspårning och distributions omfång för lager](manage-scope-configurations.md).
* Om du behöver söka i loggar som lagras i Azure Monitor loggar, se [loggs ökningar i Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md).
* Om du är färdig med distributioner kan du läsa [ta bort ändringsspårning och inventering](remove-feature.md).
* Information om hur du tar bort virtuella datorer från Ändringsspårning och inventering finns i [ta bort virtuella datorer från ändringsspårning och inventering](remove-vms-from-change-tracking.md).
* Information om hur du felsöker funktions fel finns i [felsöka ändringsspårning-och inventerings problem](../troubleshoot/change-tracking.md).
