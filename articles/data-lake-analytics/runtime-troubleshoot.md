---
title: Så här felsöker du Azure Data Lake Analytics U-SQL runtime-fel
description: Lär dig hur du felsöker U-SQL runtime-fel.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.openlocfilehash: 1236b83b410057e55015391772e37bd461a448d0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030621"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Lär dig hur du felsöker U-SQL runtime-fel på grund av körnings ändringar

Azure Data Lake U-SQL-körningsmiljön, inklusive kompilatorn, optimeringen och jobb hanteraren, är det som bearbetar din U-SQL-kod.

## <a name="choosing-your-u-sql-runtime-version"></a>Välja U-SQL runtime-version

När du skickar U-SQL-jobb från Visual Studio, ADL SDK eller Azure Data Lake Analytics-portalen, kommer jobbet att använda den aktuella standard körningen. Nya versioner av U-SQL-körningen släpps regelbundet och inkluderar både mindre uppdateringar och säkerhets korrigeringar.

Du kan också välja en anpassad körnings version. antingen på grund av att du vill testa en ny uppdatering måste du stanna kvar på en äldre version av en körnings miljö eller ha fått en snabb korrigering för ett rapporterat problem där du inte kan vänta på den vanliga nya uppdateringen.

> [!CAUTION]
> Om du väljer en annan körnings miljö än standardvärdet har du möjlighet att bryta U-SQL-jobben. Använd endast de här andra versionerna för testning.

I sällsynta fall kan Microsoft Support fästa en annan version av en körning som standard för ditt konto. Kontrol lera att du återställer PIN-koden så snart som möjligt. Om du förblir fäst i den versionen går den ut vid ett senare tillfälle.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Övervaka dina jobb U-SQL runtime-version

Du kan se historiken för vilken runtime-version dina tidigare jobb har använt i ditt kontos jobb historik via Visual Studios jobb webbläsare eller Azure Portal jobb historik.

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Välj **Visa alla jobb**. En lista över alla aktiva och nyligen avslutade jobb i kontot visas.
3. Du kan också klicka på **filter** för att hitta jobben efter **tidsintervall**, **jobb namn** och **författar** värden.
4. Du kan se körnings miljön som används i slutförda jobb.

![Visa körnings versionen av ett tidigare jobb](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

De tillgängliga körnings versionerna ändras med tiden. Standard körningen kallas alltid "default" och vi behåller minst den tidigare körningen tillgänglig under en tid samt gör särskilda körningar tillgängliga av olika orsaker. Explicit namngivna körningar följer vanligt vis följande format (kursiv stil används för variabel delar och [] anger valfria delar):

release_YYYYMMDD_adl_buildno [_modifier]

Release_20190318_adl_3394512_2 innebär till exempel den andra versionen av version 3394512 av körnings versionen av mars 18 2019 och release_20190318_adl_3394512_private innebär en privat version av samma version. Obs! datumet är relaterat till när den senaste incheckningen har gjorts för den versionen och inte nödvändigt vis det officiella lanserings datumet.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Felsöka U-SQL runtime-versions problem

Det finns två möjliga problem med körnings versioner som du kan stöta på:

1. Ett skript eller en viss användar kod ändrar beteendet från en version till nästa. Sådana överlappande ändringar förmedlas normalt i förväg med publiceringen av viktig information. Om du stöter på en sådan förändring kan du kontakta Microsoft Support för att rapportera det här problemet (om det inte har dokumenterats ännu) och skicka in dina jobb mot den äldre körnings versionen.

2. Du har använt en icke-standardkörning, antingen explicit eller implicit när den har fästs på ditt konto, och att körningen har tagits bort efter en stund. Om du stöter på saknade körningar uppgraderar du skripten så att de körs med den aktuella standard körningen. Om du behöver ytterligare tid kontaktar du Microsoft Support

## <a name="known-issues"></a>Kända problem

1. Om du refererar till Newtonsoft.Jspå fil version 12.0.3 eller senare i ett USQL-skript uppstår följande kompileringsfel:

    *"Vi beklagar! jobb som körs i ditt Data Lake Analytics-konto körs förmodligen långsammare eller kan inte slutföras. Ett oväntat problem hindrar oss från att automatiskt återställa den här funktionen till ditt Azure Data Lake Analytics-konto. Azure Data Lake tekniker har kontakt ATS för att undersöka. "*  

    Där anrops stacken ska innehålla:  
    `System.IndexOutOfRangeException: Index was outside the bounds of the array.`  
    `at Roslyn.Compilers.MetadataReader.PEFile.CustomAttributeTableReader.get_Item(UInt32 rowId)`  
    `...`

    **Lösning**: Använd Newtonsoft.Jspå fil v-12.0.2 eller lägre.
2. Kunder kan se temporära filer och mappar i butiken. De skapas som en del av den normala jobb körningen, men tas vanligt vis bort innan kunderna ser dem. Under vissa omständigheter, som är ovanliga och slumpmässiga, kan de förbli synliga under en viss tids period. De är slutligen borttagna och räknas aldrig som en del av användar lagringen eller genererar eventuella former av avgifter. Beroende på kundens jobb logik kan det orsaka problem. Om jobbet till exempel räknar upp alla filer i mappen och sedan jämför fil listor, kan det hända att det inte går att köra på grund av att de oväntade temporära filerna är tillgängliga. På samma sätt kan det hända att om ett underordnat jobb räknar upp alla filer från en specifik mapp för ytterligare bearbetning, kan det också räkna upp de temporära filerna.  

    **Lösning**: en korrigering identifieras i körnings miljön där temporära filer lagras i Temp-mappen på konto nivå än den aktuella mappen utdata. De temporära filerna skrivs i den nya Temp-mappen och tas bort när jobb körningen avbryts.  
    Eftersom den här korrigeringen hanterar kunddata är det mycket viktigt att du har den här snabb korrigeringen verifierad i MSFT innan den släpps. Den här korrigerings filen förväntas vara tillgänglig som beta runtime i mitten av år 2021 och som standard körning under den andra halvan av år 2021. 


## <a name="see-also"></a>Se även

- [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Hantera Azure Data Lake Analytics med Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Övervaka jobb i Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
