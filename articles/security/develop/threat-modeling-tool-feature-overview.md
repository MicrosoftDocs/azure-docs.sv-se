---
title: Översikt över Microsoft Threat Modeling Tool funktioner – Azure
description: Lär dig mer om alla funktioner som är tillgängliga i Threat Modeling Tool, till exempel analysvyn och rapporter.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980676"
---
# <a name="threat-modeling-tool-feature-overview"></a>Översikt över Threat Modeling Tool funktioner

Threat Modeling Tool kan hjälpa dig med dina behov av hot modellering. En grundläggande introduktion till verktyget finns i [Kom igång med Threat Modeling Tool](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Threat Modeling Tool uppdateras ofta, så se till att den här guiden ofta visar de senaste funktionerna och förbättringarna.

Om du vill öppna en tom sida väljer du **skapa en modell**.

![Tom sida](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Om du vill se vilka funktioner som är tillgängliga i verktyget använder du hot modellen som skapats av vårt team i exemplet [Kom igång](threat-modeling-tool-getting-started.md) .

![Basic Threat Model](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigering

Innan vi diskuterar de inbyggda funktionerna kan vi granska huvud komponenterna som finns i verktyget.

### <a name="menu-items"></a>Meny alternativ

Upplevelsen liknar andra Microsoft-produkter. Nu ska vi granska meny objekt på den översta nivån.

![Meny alternativ](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Etikett                               | Information      |
| --------------------------------------- | ------------ |
| **Fil** | <ul><li>Öppna, Spara och Stäng filer</li><li>Logga in och logga ut från OneDrive-konton.</li><li>Dela länkar (Visa och redigera).</li><li>Visa fil information.</li><li>Använd en ny mall för befintliga modeller.</li></ul> |
| **Redigera** | Ångra och gör om åtgärder, samt kopiera, klistra in och ta bort. |
| **Visa** | <ul><li>Växla mellan vyerna **analys** och **design** .</li><li>Öppna stängda fönster (till exempel stenciler, element egenskaper och meddelanden).</li><li>Återställ layout till standardinställningar.</li></ul> |
| **Venndiagram** | Lägg till och ta bort diagram och gå igenom flikar i diagram. |
| **Rapporter** | Skapa HTML-rapporter för att dela med andra. |
| **Hjälp** | Hitta guider som hjälper dig att använda verktyget. |

Symbolerna är genvägar för menyerna på översta nivån:

| Symbol                               | Information      |
| --------------------------------------- | ------------ |
| **Öppna** | Öppnar en ny fil. |
| **Spara** | Sparar den aktuella filen. |
| **Design** | Öppnar vyn **design** där du kan skapa modeller. |
| **Analysera** | Visar genererade hot och deras egenskaper. |
| **Lägg till diagram** | Lägger till ett nytt diagram (liknar nya flikar i Excel). |
| **Ta bort diagram** | Tar bort det aktuella diagrammet. |
| **Kopiera/klipp ut/klistra in** | Kopierar, klipper och klistrar in element. |
| **Ångra/gör om** | Ångrar och gör om åtgärder. |
| **Zooma in/Zooma ut** | Zoomar in och ut ur diagrammet för att få en bättre vy. |
| **Feedback** | Öppnar MSDN-forumet. |

### <a name="canvas"></a>Arbetsyta

Arbets ytan är det utrymme där du drar och släpper element. Dra och släpp är det snabbaste och mest effektiva sättet att bygga modeller. Du kan också högerklicka och välja objekt på menyn för att lägga till generiska versioner av element, som du ser:

#### <a name="drop-the-stencil-on-the-canvas"></a>Ta bort stencilen på arbets ytan

![Arbets ytans släpp](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Välj stencilen

![Element egenskaper](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stenciler

Utifrån den mall du väljer kan du hitta alla stenciler som är tillgängliga för användning. Om du inte hittar rätt element, använder du en annan mall. Du kan också ändra en mall så att den passar dina behov. I allmänhet hittar du en kombination av kategorier som dessa:

| Namn på stencil                               | Information      |
| --------------------------------------- | ------------ |
| **Process** | Program, plugin-program för webbläsare, trådar, virtuella datorer |
| **Extern interaktör** | Autentiseringsproviders, webbläsare, användare, webb program |
| **Datalager** | Cache, lagring, konfigurationsfiler, databaser, register |
| **Data flöde** | Binärt, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, Named pipe, RPC/DCOM, SMB, UDP |
| **Förtroende linje/gräns gräns** | Företags nätverk, Internet, dator, Sandbox, användare/kernelläge |

### <a name="notesmessages"></a>Anteckningar/meddelanden

| Komponent                               | Information      |
| --------------------------------------- | ------------ |
| **Meddelanden** | Intern verktygs logik som varnar användare när ett fel uppstår, till exempel att inga data flödar mellan element. |
| **Kommentarer** | Manuella anteckningar läggs till i filen av teknik team i hela design-och gransknings processen. |

### <a name="element-properties"></a>Element egenskaper

Element egenskaperna varierar beroende på vilka element du väljer. Förutom förtroende gränserna innehåller alla andra element tre allmänna val:

| Element egenskap                               | Information      |
| --------------------------------------- | ------------ |
| **Namn** | Användbart för att namnge dina processer, butiker, interskådespelare och flöden så att de är lätta att känna igen. |
| **Utanför definitions området** | Om det här alternativet väljs tas elementet bort från den hot-generations mat ris (rekommenderas inte). |
| **Orsak för out of scope** | Motiverings fält så att användare vet varför utanför omfattning har valts. |

Egenskaperna ändras under varje element kategori. Välj varje element för att granska de tillgängliga alternativen. Du kan också öppna mallen om du vill veta mer. Vi går igenom funktionerna.

## <a name="welcome-screen"></a>Välkomstskärmen

När du öppnar appen visas **välkomst** skärmen.

### <a name="open-a-model"></a>Öppna en modell

Hovra över **öppna en modell** för att visa två alternativ: **öppna från den här datorn** och **öppna från OneDrive**. Det första alternativet öppnar **filen öppna** på skärmen. Det andra alternativet tar dig igenom inloggnings processen för OneDrive. När autentiseringen är klar kan du välja mappar och filer.

![Öppen modell](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Öppna från dator eller OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, förslag och problem

När du väljer **feedback, förslag och problem** går du till MSDN-forumet för sdl-verktyg. Du kan läsa vad andra personer säger om verktyget, inklusive lösningar och nya idéer.

![Skärm bild som visar en knapp med feedback, förslag och problem för texten.](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>vyn Design

När du öppnar eller skapar en ny modell öppnas **design** läge.

### <a name="add-elements"></a>Lägg till element

Du kan lägga till element i rutnätet på två sätt:

- **Dra och släpp**: dra det önskade elementet till rutnätet. Använd sedan element egenskaperna för att ange ytterligare information.
- **Högerklicka**: Högerklicka någonstans i rutnätet och välj objekt på den nedrullningsbara menyn. En allmän representation av det element du väljer visas på skärmen.

### <a name="connect-elements"></a>Ansluta element

Du kan ansluta element på två sätt:

- **Dra och släpp**: dra önskat data flöde till rutnätet och Anslut båda ändarna till lämpliga element.
- **Klicka på + Shift**: Klicka på det första elementet (skicka data), tryck på och håll ned SKIFT-tangenten och välj sedan det andra elementet (tar emot data). Högerklicka och välj **Anslut**. Om du använder ett dubbelriktat data flöde är ordningen inte lika viktig.

### <a name="properties"></a>Egenskaper

 Om du vill se de egenskaper som kan ändras i stencilerna väljer du stencilen och informationen fylls i. I följande exempel visas före och efter att en **databas** stencil dras till diagrammet:

#### <a name="before"></a>Före

![Före](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Efter

![Efter](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Meddelanden

Om du skapar en hot modell och glömmer att ansluta data flöden till element får du ett meddelande. Du kan ignorera meddelandet, eller så kan du följa anvisningarna för att åtgärda problemet. 

![Skärm bild som visar en hot modell koppling som inte är ansluten till element, med ett meddelande om att problemet uppstår.](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Kommentarer

Om du vill lägga till anteckningar i diagrammet växlar du från fliken **meddelanden** till fliken **anteckningar** .

## <a name="analysis-view"></a>Analysvy

När du har skapat diagrammet väljer du **analys** symbolen (förstorings glaset) i verktygsfältet genvägar för att växla till vyn **Analysvy** .

![Analysvy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Genererat hot-val

När du väljer ett hot kan du använda tre distinkta funktioner:

| Funktion                               | Information      |
| --------------------------------------- | ------------ |
| **Läs indikator** | <p>Hotet markeras som läst, vilket hjälper dig att hålla koll på de objekt som du har granskat.</p><p>![Läs-/oläst indikator](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interaktions fokus** | <p>Interaktion i diagrammet som tillhör ett hot är markerat.</p><p>![Interaktions fokus](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Hotegenskaper** | <p>Ytterligare information om hotet visas i fönstret **hot egenskaper** .</p><p>![Hotegenskaper](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioritets ändring

Du kan ändra prioritets nivån för varje genererat hot. Olika färger gör det enkelt att identifiera hot med hög, medelhög och låg prioritet.

![Prioritets ändring](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Redigerbara fält för hot egenskaper

Som du ser i föregående bild kan du ändra den information som genereras av verktyget. Du kan också lägga till information i vissa fält, till exempel motivering. Dessa fält genereras av mallen. Om du behöver mer information för varje hot kan du göra ändringar.

![Hotegenskaper](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporter

När du har ändrat prioritet och uppdaterat statusen för varje genererat hot kan du spara filen och/eller skriva ut en rapport. Gå till **rapporten**  >  **skapa fullständig rapport**. Namnge rapporten och se något som liknar följande bild:

![Skärm bild som visar ett exempel på en Threat Model-rapport, inklusive en sammanfattning, diagram och annan information.](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Nästa steg

- Skicka frågor, kommentarer och problem till tmtextsupport@microsoft.com . **[Hämta](https://aka.ms/threatmodelingtool)** Threat Modeling Tool för att komma igång.
- Om du vill bidra till en mall för communityn går du till vår [GitHub](https://github.com/Microsoft/threat-modeling-templates) -sida.