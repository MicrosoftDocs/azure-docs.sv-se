---
title: Profilering av en moln tjänst (klassisk) lokalt i Compute-emulatorn | Microsoft Docs
description: Undersök prestanda problem i Cloud Services med Visual Studio profiler
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2f924d84967c1a1928a47b59fd3a8c28da091130
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743567"
---
# <a name="testing-the-performance-of-a-cloud-service-classic-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testa prestanda för en moln tjänst (klassisk) lokalt i Azure Compute-emulatorn med hjälp av Visual Studio profiler

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Det finns en mängd olika verktyg och tekniker för att testa prestanda för moln tjänster.
När du publicerar en moln tjänst till Azure kan du låta Visual Studio samla in profilerings data och analysera det lokalt, enligt beskrivningen i [profilering av en Azure Application][1].
Du kan också använda diagnostik för att spåra olika prestanda räknare, enligt beskrivningen i [använda prestanda räknare i Azure][2].
Du kanske också vill profilera ditt program lokalt i Compute-emulatorn innan du distribuerar det till molnet.

Den här artikeln beskriver CPU-samplings metoden för profilering, som kan göras lokalt i emulatorn. CPU-sampling är en metod för profilering som inte är väldigt påträngande. Profileraren tar en ögonblicks bild av anrops stacken vid ett angivet samplings intervall. Data samlas in under en viss tids period och visas i en rapport. Den här metoden för profilering är en indikation på var i ett beräknings intensivt program som är mest av processor arbetet som utförs.  Det ger dig möjlighet att fokusera på "frekvent sökväg" där ditt program kostar det senaste tillfället.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurera Visual Studio för profilering
Först finns det några alternativ för Visual Studio-konfiguration som kan vara till hjälp vid profilering. För att kunna göra profilerings rapporterna behöver du symboler (. PDB-filer) för ditt program och även symboler för system bibliotek. Du måste se till att du refererar till de tillgängliga symbol servrarna. Det gör du genom att gå till **verktyg** -menyn i Visual Studio, välja **alternativ**, välja **fel sökning** och sedan **symboler**. Se till att Microsoft Symbol Server visas under **symbol fil platser (. pdb)**.  Du kan också referera till https://referencesource.microsoft.com/symbols , som kan ha ytterligare symboler.

![Symbol alternativ][4]

Om du vill kan du förenkla rapporterna som profileraren genererar genom att ange Just My Code. Med Just My Code aktiverat är funktions anrops stackar förenklade så att anrop helt internt till bibliotek och .NET Framework är dolda från rapporterna. Välj **alternativ** på **verktyg** -menyn. Expandera sedan noden **prestanda verktyg** och välj **Allmänt**. Markera kryss rutan för **att aktivera just My Code för profiler-rapporter**.

![Just My Code alternativ][17]

Du kan använda dessa instruktioner med ett befintligt projekt eller med ett nytt projekt.  Om du skapar ett nytt projekt för att testa de metoder som beskrivs nedan, väljer du ett **Azure Cloud Service** -projekt i C# och väljer en **webbroll** och en **arbets roll**.

![Projekt roller för Azure Cloud service][5]

Till exempel kan du lägga till kod i projektet som tar mycket tid och demonstrerar vissa problem. Lägg till exempel till följande kod i ett projekt för arbets roll:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Anropa den här koden från RunAsync-metoden i arbets rollens RoleEntryPoint-härledda klass. (Ignorera varningen om metoden körs synkront.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Skapa och kör din moln tjänst lokalt utan fel sökning (Ctrl + F5), med lösnings konfigurationen inställd på att **lanseras**. Detta säkerställer att alla filer och mappar skapas för att köra programmet lokalt och säkerställer att alla emulatorer startas. Kontrol lera att arbets rollen körs genom att starta gränssnittet för beräknings-emulatorn från aktivitets fältet.

## <a name="2-attach-to-a-process"></a>2: koppla till en process
I stället för att profilera programmet genom att starta det från Visual Studio 2010 IDE måste du koppla profileraren till en process som körs. 

Om du vill koppla profileraren till en process väljer du **profiler** på menyn **analysera** och sedan **koppla/koppla från**.

![Alternativet Bifoga profil][6]

Hitta WaWorkerHost.exe processen för en arbets roll.

![WaWorkerHost process][7]

Om projektmappen finns på en nätverks enhet ber profileraren att ange en annan plats för att spara profilerings rapporterna.

 Du kan också ansluta till en webbroll genom att koppla till WaIISHost.exe.
Om det finns flera arbets Rolls processer i ditt program måste du använda processID för att skilja dem åt. Du kan skicka frågor till processID genom programmering genom att öppna processobjektet. Om du till exempel lägger till den här koden i metoden Run i den RoleEntryPoint-härledda klassen i en roll, kan du titta på loggen i Compute-emulatorns gränssnitt för att veta vilken process du ska ansluta till.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Om du vill visa loggen startar du gränssnittet för beräknings-emulatorn.

![Starta användar gränssnittet för beräknings-emulatorn][8]

Öppna fönstret logg konsol för arbets roll i användar gränssnittet för beräknings-emulatorn genom att klicka på konsol fönstrets namn List. Du kan se process-ID i loggen.

![Visa process-ID][9]

En som du har bifogat, utför stegen i programmets användar gränssnitt (om det behövs) för att återskapa scenariot.

När du vill stoppa profileringen väljer du länken **stoppa profilering** .

![Stoppa profilerings alternativ][10]

## <a name="3-view-performance-reports"></a>3: Visa prestanda rapporter
Prestanda rapporten för ditt program visas.

I det här läget slutar profileraren att köra, sparar data i en. VSP-fil och visar en rapport som visar en analys av dessa data.

![Profiler-rapport][11]

Om du ser String. wstrcpy i den aktiva sökvägen, klickar du på Just My Code för att ändra vyn så att endast användar kod visas.  Om du ser sträng. concat, försök att trycka på knappen Visa alla kod.

Du bör se sammanfognings metoden och strängen. concat tar upp en stor del av körnings tiden.

![Analys av rapport][12]

Om du har lagt till sträng sammanfognings koden i den här artikeln bör du se en varning i uppgiftslista för detta. Du kan också se en varning om att det finns en orimlig mängd skräp insamling, vilket beror på antalet strängar som skapas och tas bort.

![Prestanda varningar][14]

## <a name="4-make-changes-and-compare-performance"></a>4: gör ändringar och jämför prestanda
Du kan också jämföra prestandan före och efter en kod ändring.  Stoppa processen som körs och redigera koden för att ersätta sammanfognings åtgärden för strängen med StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Kör en annan prestanda och jämför sedan prestandan. Om körningarna finns i samma session i prestanda Utforskaren kan du bara välja båda rapporterna, öppna snabb menyn och välja **Jämför prestanda rapporter**. Om du vill jämföra med en körning i en annan prestanda-session öppnar du menyn **analysera** och väljer **Jämför prestanda rapporter**. Ange båda filerna i dialog rutan som visas.

![Alternativet jämför prestanda rapporter][15]

Rapporterna fokuserar på skillnaderna mellan de två körningarna.

![Jämförelse rapport][16]

Grattis! Du har börjat med profileraren.

## <a name="troubleshooting"></a>Felsökning
* Se till att du profilerar en versions version och startar utan fel sökning.
* Om alternativet Anslut/koppla från inte är aktiverat på profiler-menyn kör du guiden prestanda.
* Använd Compute emulator-gränssnittet för att visa status för programmet. 
* Om du har problem med att starta program i emulatorn eller kopplar profileraren stänger du av beräknings-emulatorn och startar om den. Om det inte löser problemet kan du försöka starta om. Det här problemet kan uppstå om du använder Compute-emulatorn för att pausa och ta bort pågående distributioner.
* Om du har använt någon av profilerings kommandona från kommando raden, särskilt de globala inställningarna, kontrollerar du att VSPerfClrEnv/globaloff har anropats och att VsPerfMon.exe har avslut ATS.
* Om du ser meddelandet "PRF0025: inga data har samlats in", kontrollerar du att den process som du har kopplat till har processor aktivitet. Program som inte gör något beräknings arbete kanske inte genererar några samplings data.  Det är också möjligt att processen avslutades innan någon sampling utfördes. Kontrol lera att körnings metoden för en roll som du profileringr inte avslutas.

## <a name="next-steps"></a>Nästa steg
Instrumentering av Azure-binärfiler i emulatorn stöds inte i Visual Studio profiler, men om du vill testa minnesallokering kan du välja det alternativet vid profilering. Du kan också välja samtidiga profilering, som hjälper dig att avgöra om trådar tar tid att konkurrera för lås, eller nivå interaktions profilering, som hjälper dig att spåra prestanda problem när du interagerar mellan olika nivåer av ett program, oftast mellan data nivån och en arbets roll.  Du kan visa databas frågorna som din app genererar och använder profilerings data för att förbättra din användning av databasen. Information om nivå interaktions profilering finns i genom gång av blogg inlägg [: använda nivån interaktions profiler i Visual Studio Team System 2010][3].

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png