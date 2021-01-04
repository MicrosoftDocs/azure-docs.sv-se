---
title: Övervakning av fil integritet i Azure Security Center | Microsoft Docs
description: Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center att använda den här genom gången.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: ab173f47ed09dbe77c12cad844d8a1f3654f4a9b
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734767"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Övervakning av fil integritet i Azure Security Center
Lär dig hur du konfigurerar FIM (File Integrity Monitoring) i Azure Security Center att använda den här genom gången.


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|Kräver [Azure Defender för servrar](defender-for-servers-introduction.md).<br>FIM överför data till Log Analytics-arbetsytan. Data avgifterna gäller baserat på mängden data som du överför. Mer information finns i [Log Analytics prissättning](https://azure.microsoft.com/pricing/details/log-analytics/) .|
|Nödvändiga roller och behörigheter:|**Arbets ytans ägare** kan aktivera/inaktivera FIM (mer information finns i [Azure roles for Log Analytics](/services-hub/health/azure-roles#azure-roles)).<br>**Läsaren** kan visa resultat.|
|Moln|![Ja ](./media/icons/yes-icon.png) kommersiella moln<br>![Ja ](./media/icons/yes-icon.png) National/suverän (US gov, Kina gov, övrigt gov)<br>Stöds bara i regioner där Azure Automation lösning för ändrings spårning är tillgänglig.<br>Se [regioner som stöds för länkad Log Analytics-arbetsyta](../automation/how-to/region-mappings.md).<br>[Läs mer om ändrings spårning](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-security-center"></a>Vad är FIM i Security Center?
File Integrity Monitoring (FIM), även kallat ändrings övervakning, undersöker operativsystemfiler, Windows-register, program vara, Linux-systemfiler med mera, för ändringar som kan tyda på ett angrepp. 

Security Center rekommenderar att entiteter övervakas med FIM, och du kan även definiera egna FIM-principer eller entiteter som ska övervakas. FIM aviserar dig om misstänkt aktivitet, till exempel:

- Skapa eller ta bort fil-och register nyckel
- Fil ändringar (ändringar i fil storlek, åtkomst kontrol listor och hash-värde för innehållet)
- Register ändringar (ändringar i storlek, åtkomst kontrol listor, typ och innehåll)

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Granska listan över föreslagna entiteter som ska övervakas med FIM
> * Definiera egna, anpassade FIM-regler
> * Granska ändringar i dina övervakade entiteter
> * Använd jokertecken för att förenkla spårning över kataloger


## <a name="how-does-fim-work"></a>Hur fungerar FIM?

Genom att jämföra det aktuella läget för dessa objekt med statusen under den föregående sökningen, varnar FIM dig om misstänkta ändringar har gjorts.

FIM använder Azure Ändringsspårning-lösningen för att spåra och identifiera ändringar i din miljö. När fil integritets övervakning har Aktiver ATS har du en **ändringsspårning** resurs av typen **lösning**. Information om frekvens information om data insamling finns i [ändringsspårning information om data insamling](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Om du tar bort **ändringsspårning** resursen inaktiverar du även funktionen för övervakning av fil integritet i Security Center.

## <a name="which-files-should-i-monitor"></a>Vilka filer ska jag övervaka?
När du väljer vilka filer som ska övervakas bör du överväga vilka filer som är kritiska för ditt system och dina program. Övervaka filer som du inte förväntar dig att ändra utan att planera. Om du väljer filer som ofta ändras av program eller operativ system (t. ex. loggfiler och textfiler), kommer den att skapa mycket brus, vilket gör det svårt att identifiera ett angrepp.

Security Center innehåller följande lista över rekommenderade objekt som ska övervakas baserat på kända angrepps mönster.

|Linux-filer|Windows-filer|Windows-registernycklar (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot.|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell-mappar|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell-mappar|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot.|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell-mappar|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell-mappar|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Aktivera övervakning av fil integritet 

FIM är endast tillgängligt från Security Center sidor i Azure Portal. Det finns för närvarande inga REST API för att arbeta med FIM.

1. Välj **fil integritets övervakning** från **Azure Defender** -instrument panelens **avancerade skydds** områden.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="Startar FIM" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    Sidan konfiguration av **fil integritets övervakning** öppnas.

    Följande information anges för varje arbets yta:

    - Totalt antal ändringar som inträffat under den senaste veckan (du kan se ett bindestreck "-" om FIM inte är aktiverat på arbets ytan)
    - Totalt antal datorer och virtuella datorer som rapporterar till arbets ytan
    - Geografisk plats för arbets ytan
    - Azure-prenumeration som arbets ytan är under

1. Använd den här sidan om du vill:

    - Komma åt och Visa status och inställningar för varje arbets yta

    - ![Uppgradera plan-ikonen ][4] Uppgradera arbets ytan för att använda Azure Defender. Den här ikonen anger att arbets ytan eller prenumerationen inte skyddas av Azure Defender. Din prenumeration måste skyddas av Azure Defender för att du ska kunna använda FIM-funktionerna. [Läs mer](security-center-pricing.md).

    - ![Aktivera ikon][3] Aktivera FIM på alla datorer under arbets ytan och konfigurera FIM-alternativen. Den här ikonen anger att FIM inte är aktiverat för arbets ytan.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="Aktivera FIM för en speciell arbets yta":::


    > [!TIP]
    > Om det inte finns någon aktiverings-eller uppgraderings knapp, och utrymmet är tomt, innebär det att FIM redan är aktiverat på arbets ytan.


1. Välj **Aktivera**. Informationen om arbets ytan, inklusive antalet Windows-och Linux-datorer under arbets ytan visas.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="Sidan information om FIM-arbetsyta":::

   De rekommenderade inställningarna för Windows och Linux visas också.  Expandera **Windows-filer**, **register**-och **Linux-filer** för att se en fullständig lista över rekommenderade objekt.

1. Avmarkera kryss rutorna för alla rekommenderade entiteter som du inte vill ska övervakas av FIM.

1. Välj **Använd övervakning av fil integritet** för att aktivera FIM.

> [!NOTE]
> Du kan ändra inställningarna när som helst. Mer information finns i [Redigera övervakade entiteter](#edit-monitored-entities) nedan.



## <a name="audit-monitored-workspaces"></a>Granska övervakade arbets ytor 

Instrument panelen för **fil integritets övervakning** visas för arbets ytor där FIM är aktiverat. FIM-instrumentpanelen öppnas när du har aktiverat FIM på en arbets yta eller när du väljer en arbets yta i fönstret för **övervakning av fil integritet** som redan har FIM aktiverat.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM-instrumentpanelen och dess olika informations paneler":::

FIM-instrumentpanelen för en arbets yta visar följande information:

- Totalt antal datorer som är anslutna till arbets ytan
- Totalt antal ändringar som inträffat under den valda tids perioden
- En analys av ändrings typ (filer, registret)
- En analys av ändrings kategorin (ändrad, tillagd, borttagen)

Välj **filter** överst i instrument panelen för att ändra den tids period som ändringarna visas för.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="Tids period filter för FIM-instrumentpanelen":::

Fliken **servrar** visar de datorer som rapporterar till den här arbets ytan. För varje dator visas följande på instrument panelen:

- Totalt antal ändringar som inträffat under den valda tids perioden
- En analys av totalt antal ändringar som fil ändringar eller register ändringar

**Loggs ökningen** öppnas när du anger ett dator namn i Sök fältet eller väljer en dator som visas på fliken datorer. loggs ökning visar alla ändringar som har gjorts under den valda tids perioden för datorn. Du kan expandera en ändring för mer information.

![Loggsökning][8]

Fliken **ändringar** (visas nedan) listar alla ändringar för arbets ytan under den valda tids perioden. För varje entitet som har ändrats visar instrument panelen:

- Dator som ändringen utfördes på
- Typ av ändring (register eller fil)
- Ändrings kategori (ändrad, tillagd, borttagen)
- Datum och tid för ändring

![Ändringar för arbets ytan][9]

**Ändrings information** öppnas när du anger en ändring i Sök fältet eller väljer en entitet som visas på fliken **ändringar** .

![Ändra information][10]

## <a name="edit-monitored-entities"></a>Redigera övervakade entiteter

1. Gå tillbaka till **instrument panelen för fil integritets övervakning** och välj **Inställningar**.

   ![Inställningar][11]

   **Arbets ytans konfiguration** öppnas och visar tre flikar: **Windows-registret**, **Windows-filer** och **Linux-filer**. Varje flik visar de entiteter som du kan redigera i den kategorin. Security Center identifierar om FIM är aktiverat (sant) eller inte aktiverat (falskt) för varje entitet i listan.  Genom att redigera entiteten kan du aktivera eller inaktivera FIM.

   ![Konfiguration av arbetsyta][12]

2. Välj ett identitets skydd. I det här exemplet valde vi ett objekt under Windows-registret. **Redigera för ändringsspårning** öppnas.

   ![Redigera eller spåra ändringar][13]

Under **Redigera för ändringsspårning** kan du:

- Aktivera (true) eller inaktivera (falskt) fil integritets övervakning
- Ange eller ändra enhetens namn
- Ange eller ändra värdet eller sökvägen
- Ta bort entiteten, ignorera ändringen eller spara ändringen

## <a name="add-a-new-entity-to-monitor"></a>Lägg till en ny entitet som ska övervakas
1. Gå tillbaka till **instrument panelen för fil integritets övervakning** och välj **Inställningar** högst upp. **Arbets ytans konfiguration** öppnas.
2. Under **arbets ytans konfiguration** väljer du fliken för den typ av enhet som du vill lägga till: Windows-registret, Windows-filer eller Linux-filer. I det här exemplet valde vi **Linux-filer**.

   ![Lägg till ett nytt objekt som ska övervakas][14]

3. Välj **Lägg till**. **Lägg till för ändringsspårning** öppnas.

   ![Ange begärd information][15]

4. Ange den begärda informationen på sidan **Lägg till** och välj **Spara**.

## <a name="disable-monitored-entities"></a>Inaktivera övervakade entiteter
1. Gå tillbaka till instrument panelen för **fil integritets övervakning** .
2. Välj en arbets yta där FIM är aktiverat. En arbets yta är aktive rad för FIM om knappen Aktivera eller uppgraderings plan saknas.

   ![Välj en arbets yta där FIM är aktiverat][16]

3. Under övervakning av fil integritet väljer du **Inställningar**.

   ![Välj inställningar][17]

4. Under **arbets ytans konfiguration** väljer du en grupp där **aktive rad** är inställt på sant.

   ![Konfiguration av arbets yta][18]

5. Under **Redigera för ändringsspårning** Window set **aktiverat** på falskt.

   ![Ange som aktive rad till falskt][19]

6. Välj **Spara**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Övervakning av mappar och Sök vägar med jokertecken

Använd jokertecken för att förenkla spårningen mellan kataloger. Följande regler gäller när du konfigurerar övervakning av mappar med jokertecken:
-   Jokertecken krävs för att spåra flera filer.
-   Jokertecken kan endast användas i det sista segmentet i en sökväg, till exempel C:\folder\file eller/etc/*. conf
-   Om en miljö variabel innehåller en sökväg som inte är giltig kommer verifieringen att lyckas, men sökvägen Miss lyckas När inventeringen körs.
-   När du anger sökvägen bör du undvika allmänna sökvägar, till exempel c: \* . *, vilket leder till att för många mappar passerar.

## <a name="disable-fim"></a>Inaktivera FIM
Du kan inaktivera FIM. FIM använder Azure Ändringsspårning-lösningen för att spåra och identifiera ändringar i din miljö. Genom att inaktivera FIM tar du bort Ändringsspårning-lösningen från den valda arbets ytan.

1. Om du vill inaktivera FIM går du tillbaka till instrument panelen för **fil integritets övervakning** .
2. Välj en arbetsyta.
3. Under **övervakning av fil integritet** väljer du **inaktivera**.

   ![Inaktivera FIM][20]

4. Inaktivera genom att välja **ta bort** .

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig att använda FIM (File Integrity Monitoring) i Security Center. Mer information om Security Center finns på följande sidor:

* [Ange säkerhets principer](tutorial-security-policy.md) --lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
* [Hantera säkerhets rekommendationer](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Azures säkerhetsblogg](/archive/blogs/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png