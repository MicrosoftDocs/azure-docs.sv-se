---
title: Prestandadiagnostik för Azure Virtual Machines | Microsoft Docs
description: Introducerar Azure Performance Diagnostics för Windows.
services: virtual-machines-windows
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 70d1a52ff3326fcd3a628cc7f0f9d91edc9274e7
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655398"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Prestandadiagnostik för virtuella Azure-datorer

Verktyget prestanda diagnostik hjälper dig att felsöka prestanda problem som kan påverka en virtuell Windows-eller Linux-dator (VM). De fel söknings scenarier som stöds är snabba kontroller av kända problem och bästa praxis, och komplexa problem som innefattar långsamma prestanda för virtuella datorer eller hög användning av CPU, disk utrymme eller minne.

Du kan köra prestandadiagnostik direkt från Azure Portal, där du också kan granska insikter och en rapport om olika loggar, omfattande konfigurations data och diagnostikdata. Vi rekommenderar att du kör prestandadiagnostik och granskar insikter och diagnostikdata innan du kontaktar Microsoft Support.

> [!NOTE]
> För Windows stöds för närvarande prestanda diagnostik på virtuella datorer som har .NET SDK version 4,5 eller en senare version installerad. Stegen för att köra prestandadiagnostik på klassiska virtuella datorer finns i [Azure Performance Diagnostics VM-tillägget](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Operativsystem som stöds

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8,1
* Windows 8

### <a name="linux"></a>Linux

- Följande distributioner stöds för närvarande:

    | Distribution               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux Server        | 6,10 [ `*` ], 7,3, 7,5, 7,6, 7,7, 7,8 |
    | CentOS                     | 6,5 [ `*` ], 7,6, 7,7, 7,8                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ], 8,1, 8,2                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Se [kända problem](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Installera och köra prestandadiagnostik på den virtuella datorn

Prestandadiagnostik installerar ett VM-tillägg som kör ett diagnostiskt verktyg med namnet PerfInsights. PerfInsights är tillgängligt för både [Windows](./how-to-use-perfinsights.md) och [Linux](./how-to-use-perfinsights-linux.md). Följ dessa steg om du vill installera och köra prestandadiagnostik:

1. I den vänstra kolumnen med kommandon väljer du **virtuella datorer**.
1. I listan med namn på virtuella datorer väljer du den virtuella dator som du vill köra diagnostik på.
1. I den högra kolumnen med kommandon väljer du **prestanda diagnos**.

    ![Skärm bild av Azure Portal med knappen Installera prestandadiagnostik markerad](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > I den här skärm bilden är bladet med namn på virtuella datorer dolda.
1. Välj ett lagrings konto (valfritt)

    Om du vill använda ett enda lagrings konto för att lagra prestanda diagnostiska resultat för flera virtuella datorer kan du välja ett lagrings konto genom att klicka på knappen **Inställningar** i verktygsfältet. Klicka på **OK** -knappen när du har valt lagrings kontot.

    Om du inte anger ett lagrings konto skapas ett nytt lagrings konto som standard.

    ![Skärm bild av bladet prestanda diagnostik med verktygsfälts knappen Inställningar markerad](media/performance-diagnostics/settings-button.png)

    ![Skärm bild av val av lagrings konto från bladet inställningar för prestanda diagnostik](media/performance-diagnostics/select-storage-account.png)

1. Välj knappen **Installera prestanda Diagnostics** .
1. Markera kryss rutan **Kör diagnostik** om du vill köra en diagnostik när installationen är klar. Om du väljer det här alternativet kommer du att kunna välja scenariot för prestanda analys och relaterade alternativ.

    ![Skärm bild av installations knappen för prestandadiagnostik](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Välj ett analys scenario som ska köras

Följande analys scenarier är tillgängliga från Azure Portal. Välj en analys, beroende på det prestanda problem som du har. Välj varaktighet och spårnings alternativ vid behov för analysen.

* **Snabb prestanda analys**  
    Söker efter kända problem, analyserar bästa praxis och samlar in diagnostikdata. Det tar flera minuter att köra den här analysen. Läs mer om [Windows](./how-to-use-perfinsights.md) eller [Linux](./how-to-use-perfinsights-linux.md)

* **Prestanda analys**  
    Innehåller alla kontroller i snabb prestanda analys och övervakar hög resursförbrukning. Använd den här versionen för att felsöka allmänna prestanda problem, till exempel hög CPU, minne och disk användning. Den här analysen tar 30 sekunder till 15 minuter, beroende på vald varaktighet. Läs mer om [Windows](./how-to-use-perfinsights.md) eller [Linux](./how-to-use-perfinsights-linux.md)

* **Avancerad prestanda analys**`*`  
    Innehåller alla kontroller i prestanda analysen och samlar in en eller flera av de spår som anges i följande avsnitt. Använd det här scenariot för att felsöka komplexa problem som kräver ytterligare spår. Om du kör det här scenariot under längre perioder ökar den totala storleken på diagnostiska utdata, beroende på storleken på den virtuella datorn och de spårnings alternativ som valts. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på vald varaktighet. [Läs mer](./how-to-use-perfinsights.md)

* **Azure Files analys**`*`  
    Innehåller alla kontroller i prestanda analysen och fångar in en nätverks spårning och SMB-räknare. Använd det här scenariot för att felsöka prestanda i Azure Files. Den här analysen tar 30 sekunder till 15 minuter att köra, beroende på vald varaktighet. [Läs mer](./how-to-use-perfinsights.md)

>[!Note]
>[ `*` ] Dessa analys scenarier stöds bara i Windows.

![Skärm bild av fönstret Kör diagnostik i bladet prestanda diagnostik](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Ange symptom (valfritt)

Välj eventuella förvalda symtom i listan eller Lägg till nya symptom. Detta hjälper oss att förbättra analysen i framtiden.

### <a name="provide-support-request-number-if-available-optional"></a>Ange numret för support förfrågan, om det är tillgängligt (valfritt)

Om du arbetar med en Microsoft support-tekniker på ett befintligt support ärende, ange support ärende numret.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Granska sekretess policyn och juridiska villkor och markera kryss rutan för att bekräfta (krävs)

Om du vill köra diagnostiken måste du godkänna de juridiska villkoren och godkänna sekretess policyn.

### <a name="select-ok-to-run-the-diagnostics"></a>Välj OK för att köra diagnostiken

Ett meddelande visas som att prestandadiagnostik börjar installeras. När installationen är klar visas ett meddelande som anger att installationen har lyckats. Den valda analysen körs sedan för angiven varaktighet. Detta kan vara en tid att återskapa prestanda problemet så att diagnostikdata kan fångas vid rätt tidpunkt.

När analysen är klar överförs följande objekt till Azure-tabeller och en binary large object-behållare (BLOB) i det angivna lagrings kontot:

* Alla insikter och relaterad information om körningen
* En komprimerad utdatafil (zip) (med namnet **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) i Windows och en bolls fil (med namnet **PerformanceDiagnostics_yyyy mm-dd_hh-mm-SS-FFF. tar. gz** ) på Linux som innehåller loggfiler
* En HTML-rapport

Efter överföringen visas en ny diagnostisk rapport i Azure Portal.

![Skärm bild av en lista med diagnostikrapport i bladet prestanda diagnostik](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Ändra inställningar för prestanda diagnostik

Använd verktygs knappen **Inställningar** för att ändra lagrings kontot där du kan lagra diagnostiska insikter och utdata. Du kan använda samma lagrings konto för flera virtuella datorer som använder prestandadiagnostik. När du ändrar lagrings kontot tas inte gamla rapporter och insikter bort. De kommer dock inte längre att visas i listan med diagnostiska rapporter.

## <a name="review-insights-and-performance-diagnostics-report"></a>Granska insikter och prestanda diagnostikrapport

Varje diagnostisk körning innehåller en lista över insikter och rekommendationer, berörda resurser, loggfiler och annan avancerad diagnostikinformation som samlas in, samt en rapport för offlinevisning. En fullständig lista över alla insamlade diagnostikdata finns i **vilken typ av information som samlas in av PerfInsights?** på [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) eller [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Välj en prestanda diagnos rapport

Du kan använda listan med diagnostiska rapporter för att hitta alla diagnostiska rapporter som kördes. Listan innehåller information om analysen som användes, insikter som hittades och deras effekt nivåer. Markera en rad om du vill visa mer information.

![Skärm bild av att välja en diagnostikrapport från bladet prestanda diagnostik](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Granska en prestanda diagnostisk rapport

Varje prestanda diagnos rapport kan innehålla flera insikter och indikerar en effekt nivå av hög, medel eller låg. Alla insikter innehåller också rekommendationer för att minimera problemet. Insikter grupperas för enkel filtrering.

Effekt nivåer representerar risken för prestanda problem, baserat på faktorer som felaktig konfiguration, kända problem eller problem som rapporteras av andra användare. Du kanske inte har någon eller flera av de angivna problemen ännu. Du kan till exempel ha SQL-loggfiler och databasfiler på samma data disk. Det här villkoret har hög potential för Flask halsar och andra prestanda problem om databas användningen är hög, men du kanske inte märker något problem om användningen är låg.

![Skärm bild av bladet prestanda diagnostikrapport – översikt](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Granska prestanda diagnostiska insikter och rekommendationer

Du kan välja en insikt för att visa mer information om de berörda resurserna, föreslagna åtgärder och referens länkar.

![Skärm bild av information om prestanda-diagnostik Insight](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Hämta och granska rapporten med fullständig prestanda diagnostik

Du kan använda knappen **Hämta rapport** för att ladda ned en HTML-rapport som innehåller ytterligare utförlig diagnostikinformation, till exempel lagrings-och nätverks konfiguration, prestanda räknare, spårning, lista över processer och loggar. Innehållet är beroende av den valda analysen. För avancerad fel sökning kan rapporten innehålla ytterligare information och interaktiva diagram som är relaterade till hög CPU-användning, hög disk användning och processer som använder mycket minne. Mer information om rapporten prestanda diagnostik finns i [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) eller [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Hantera prestanda diagnostiska rapporter

Du kan ta bort en eller flera prestanda diagnostiska rapporter med knappen **ta bort rapport** .

## <a name="how-to-uninstall-performance-diagnostics"></a>Så här avinstallerar du prestandadiagnostik

Du kan avinstallera prestandadiagnostik från en virtuell dator. Den här åtgärden tar bort VM-tillägget men påverkar inte några diagnostikdata som finns i lagrings kontot.

![Skärm bild av bladet prestanda diagnostik med Avinstallera knapp markerat](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Var finns diagnostikdata från den virtuella datorn?

Alla insikter och rapporter för prestanda diagnostik lagras i ditt eget lagrings konto. Insikter lagras i Azure-tabeller. Den komprimerade filen rapporter lagras i en binary large object-behållare (BLOB) med namnet azdiagextnresults.

Du kan visa informationen om lagrings kontot med hjälp av knappen Inställningar i verktygsfältet.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Hur gör jag för att dela dessa data med Microsofts kund support

Det finns flera sätt att dela den diagnostiska rapporten med Microsoft.

**Alternativ 1:** Dela den senaste rapporten automatiskt  
När du öppnar ett support ärende med Microsoft är det viktigt att du delar rapporten prestanda diagnostik. Om du har valt att dela den här informationen med Microsoft samtidigt som du kör diagnostiken (genom att markera kryss rutan **Jag accepterar att information om att dela diagnostikinformation med Microsoft**) kommer Microsoft att kunna komma åt rapporten från ditt lagrings konto med en SAS-länk till zip-filen för utdata i upp till 30 dagar från körnings datumet. Endast den senaste rapporten är tillgänglig för support teknikern.

**Alternativ 2:** Generera en signatur för delad åtkomst för den komprimerade filen för den diagnostiska rapporten  
Du kan dela en länk till den komprimerade filen rapporter med hjälp av signaturer för delad åtkomst. Det gör du genom att följa dessa steg:

1. I Azure Portal bläddrar du till det lagrings konto där diagnostikdata lagras.
1. Välj **blobbar** under **BLOB service** avsnittet.
1. Välj behållaren **azdiagextnresults** .
1. Välj den komprimerade fil för prestanda diagnostik som du vill dela.
1. På fliken **skapa SAS** väljer du villkoren för delning.
1. Klicka på **generera BLOB SAS-token och URL**.
1. Kopiera **URL: en för blobb-SAS** och dela den med support teknikern.

**Alternativ 3:** Ladda ned rapporten från lagrings kontot

Du kan också leta reda på den komprimerade filen med prestandadiagnostik i steg 1 – 4 i alternativ 2. Välj att ladda ned filen och dela den via e-post eller be support teknikern om instruktioner för att ladda upp filen.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Hur gör jag för att samla in diagnostikdata vid rätt tidpunkt

Varje prestanda Diagnostics-körning har två steg:

1. Installera eller uppdatera VM-tillägget för prestanda diagnostik.
1. Kör diagnostiken för angiven varaktighet.

För närvarande finns det inget enkelt sätt att veta exakt när installationen av VM-tillägget är slutförd. I allmänhet tar det cirka 45 sekunder till 1 minut att installera VM-tillägget. När du har installerat VM-tillägget kan du köra dina återskapnings-steg för att låta prestandadiagnostik samla in rätt data för fel sökning.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande inte kan ta reda på orsaken till problemet och behöver mer hjälp kan du öppna ett support ärende med Microsofts kund support när du har granskat prestanda diagnostiska insikter och rapporter.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/)och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
