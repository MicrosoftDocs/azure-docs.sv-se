---
title: Övervaka Key Vault med Azure Monitor för Key Vault (förhands granskning) | Microsoft Docs
description: I den här artikeln beskrivs Azure Monitor för nyckel valv.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: c669946ab76fcaeaaa6fd681f521408643c5a63c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531267"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Övervaka Key Vault-tjänsten med Azure Monitor för Key Vault (för hands version)
Azure Monitor för Key Vault (för hands version) tillhandahåller omfattande övervakning av dina nyckel valv genom att leverera en enhetlig vy över dina Key Vault förfrågningar, prestanda, misslyckade och svars tider.
Den här artikeln hjälper dig att förstå hur du kan publicera och anpassa användningen av Azure Monitor för Key Vault (för hands version).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Introduktion till Azure Monitor för Key Vault (för hands version)

Innan du hoppar till erfarenheten bör du förstå hur den visar och visualiserar information.
-    **I skalnings perspektiv** visas en ögonblicks bild av prestanda baserat på begär Anden, uppdelning av misslyckade åtgärder och en översikt över driften och svars tiden.
-   **Detalj** analys av ett särskilt nyckel valv för att utföra detaljerad analys.
-    **Anpassningsbara** där du kan ändra vilka mått du vill se, ändra eller ange tröskelvärden som överensstämmer med dina gränser och spara din egen arbets bok. Diagram i arbets boken kan fästas på Azure-instrumentpaneler.

Azure Monitor för Key Vault kombinerar både loggar och mått för att tillhandahålla en global övervaknings lösning. Alla användare kan komma åt de mät databaserade övervaknings data, men inkludering av loggarbaserade visualiseringar kan kräva att användarna [aktiverar loggning av sina Azure Key Vault](../../key-vault/general/logging.md).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Konfigurera nyckel valv för övervakning

> [!NOTE]
> Aktivering av loggar är en betald tjänst som tillhandahåller ytterligare övervaknings funktioner.

1. På fliken åtgärder & svars tid kan du avgöra hur många och vilka nyckel valv som är aktiverade. Börja samla in genom att välja knappen **Aktivera** , som tar dig till en separat arbets bok som visar de nyckel valv som kräver att du aktiverar diagnostikloggar.

    ![Skärm bild av fliken åtgärder och svars tid med knappen blå aktivering som visas](./media/key-vaults-insights-overview/enable-logging.png)

2. Om du vill aktivera diagnostikloggar klickar du på länken **Aktivera** under kolumnen åtgärder och skapar en ny diagnostisk inställning som skickar loggar till en Log Analytics-arbetsyta. Vi rekommenderar att du skickar alla loggar till samma arbets yta.

3. När diagnostikinställningar har sparats kommer du att kunna visa alla loggbaserade diagram och visualiseringar under Key Vault insikter. Observera att det kan ta flera minuter för timmar att börja fylla i loggarna.

4. Om du vill ha mer hjälp om hur du aktiverar diagnostikloggar för Key Vaults tjänsten läser du [hela guiden](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Visa från Azure Monitor

Från Azure Monitor kan du Visa information om förfrågningar, svars tid och fel från flera nyckel valv i prenumerationen och hjälpa till att identifiera prestanda problem och begränsnings scenarier.

Utför följande steg för att visa användning och åtgärder för nyckel valv i alla dina prenumerationer:

1. Logga in på [Azure Portal](https://portal.azure.com/)

2. Välj **övervaka** i rutan till vänster i Azure Portal och välj **nyckel valv (för hands version)** under avsnittet insikter.

![Skärm bild av översikts upplevelse med flera grafer](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Översikt över arbets bok

I arbets boken översikt för den valda prenumerationen visar tabellen interaktiva Key Vault-mått för nyckel valv grupperade i prenumerationen. Du kan filtrera resultat baserat på de alternativ du väljer i följande List rutor:

* Prenumerationer – endast prenumerationer som har nyckel valv visas.

* Nyckel valv – som standard är upp till fem nyckel valv markerade i förväg. Om du väljer alla eller flera nyckel valv i omfattnings väljaren returneras upp till 200 nyckel valv. Om du till exempel har sammanlagt 573 nyckel valv över tre prenumerationer som du har valt visas bara 200-valv.

* Tidsintervall – visar som standard de senaste 24 timmarna med information baserat på motsvarande val som gjorts.

Panelen räknare, under List rutan, slår upp det totala antalet nyckel valv i de valda prenumerationerna och visar hur många som är markerade. Det finns villkorsstyrda färgkodade termiska kartor för de kolumner i arbets boken som rapporterar förfrågningar, haverier och svars tids mått. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena.

## <a name="failures-workbook"></a>Arbets boks problem

Välj **felen** överst på sidan och fliken Felaktiga visas. Det visar API-träffar, frekvens över tid, tillsammans med mängden vissa svars koder.

![Skärm bild av arbets boken haveris](./media/key-vaults-insights-overview/failures.png)

Det finns en villkorsstyrd färg kodning eller termiska kartor för kolumner i arbets boken som rapporterar API: er träffar mått med ett blått värde. Den djupaste färgen har det högsta värdet och en ljusare färg baseras på de lägsta värdena.

Arbets boken visar lyckade (2xx status koder), autentiseringsfel (401/403 status koder), begränsning (429 status koder) och andra fel (4xx status koder).

För att bättre förstå vad var och en av status koderna representerar rekommenderar vi att läsa igenom dokumentationen om [Azure Key Vault status-och svars koder](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="operations--latency-workbook"></a>Arbets bok för åtgärder & svars tid

Välj **åtgärder & svars tid** överst på sidan och fliken **åtgärder & svars tid** öppnas. På den här fliken kan du publicera nyckel valv för övervakning. Mer detaljerade anvisningar finns i avsnittet [Konfigurera nyckel valv för övervakning](#configuring-your-key-vaults-for-monitoring) .

Du kan se hur många av dina nyckel valv som är aktiverade för loggning. Om minst ett valv har kon figurer ATS korrekt, kommer du att kunna se tabeller som visar drift-och status koder för varje nyckel valv. Du kan klicka i informations avsnittet för en rad om du vill ha mer information om den enskilda åtgärden.

![Skärm bild av diagram över drift och latens](./media/key-vaults-insights-overview/logs.png)

Om du inte ser några data för det här avsnittet kan du referera till det övre avsnittet om hur du aktiverar loggar för Azure Key Vault eller genom att titta i avsnittet fel sökning nedan.

## <a name="view-from-a-key-vault-resource"></a>Visa från en Key Vault resurs

Få åtkomst till Azure Monitor för Key Vault direkt från ett nyckel valv:

1. I Azure Portal väljer du nyckel valv.

2. Välj ett nyckel valv i listan. I avsnittet övervakning väljer du insikter (för hands version).

Dessa vyer är också tillgängliga genom att välja resurs namnet för ett nyckel valv från arbets boken Azure Monitors nivå.

![Skärm bild av vy från en Key Vault-resurs](./media/key-vaults-insights-overview/key-vault-resource-view.png)

I arbets boken **Översikt** för nyckel valvet visas flera prestanda mått som hjälper dig att snabbt utvärdera:

- Interaktiva prestanda diagram som visar de viktigaste detaljerna som rör Key Vault-transaktioner, svars tid och tillgänglighet.

- Mått och status paneler för att markera tjänstens tillgänglighet, totalt antal transaktioner till Key Vault-resursen och övergripande svars tid.

Om du väljer någon av de andra flikarna för att **Miss lyckas** eller **åtgärder** öppnas respektive arbets bok.

![Skärm bild av vyn med problem](./media/key-vaults-insights-overview/resource-failures.png)

Arbets boken fel sammangraderar resultaten av alla Key Vault-begäranden inom den valda tids ramen och ger kategorisering på lyckade (2xx), autentiseringsfel (401/403), begränsning (429) och andra fel.

![Skärm bild av vyn operationer](./media/key-vaults-insights-overview/operations.png)

Med arbets boken för åtgärder kan användarna få detaljerad information om alla transaktioner, som kan filtreras efter resultat status med hjälp av toppnivå panelerna.

![Skärm bild av vyn operationer](./media/key-vaults-insights-overview/info.png)

Användarna kan också begränsa vyer baserat på vissa transaktions typer i den övre tabellen, vilket dynamiskt uppdaterar den nedre tabellen, där användarna kan Visa fullständig åtgärds information i ett popup-fönster.

>[!NOTE]
> Observera att diagnostikinställningar måste vara aktiverat för att användarna ska kunna visa den här arbets boken. Mer information om hur du aktiverar diagnostisk inställning finns i mer information om [Azure Key Vault loggning](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Fäst och exportera

Du kan fästa något av mått avsnitten på en Azure-instrumentpanel genom att välja kartnål-ikonen längst upp till höger i avsnittet.

Översikten över flera prenumerationer och nyckel valv – stöd för att exportera resultaten i Excel-format genom att välja ikonen Ladda ned till vänster om ikonen för kartnålen.

![Skärm bild av PIN-ikonen vald](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Anpassa Azure Monitor för Key Vault

I det här avsnittet beskrivs vanliga scenarier för att redigera arbets boken för att anpassa stöd för dina data analys behov:
*  Ange omfång för arbets boken att alltid välja en viss prenumeration eller nyckel valv
* Ändra mått i rutnätet
* Ändra tröskelvärdet för begär Anden
* Ändra färg åter givningen

Du kan börja anpassningar genom att aktivera redigerings läget genom att välja knappen **Anpassa** i det översta verktygsfältet.

![Skärm bild av knappen anpassa](./media/key-vaults-insights-overview/customize.png)

Anpassningar sparas i en anpassad arbets bok för att förhindra att standard konfigurationen skrivs över i vår publicerade arbets bok. Arbets böcker sparas i en resurs grupp, antingen i avsnittet Mina rapporter som är privat för dig eller i avsnittet delade rapporter som är tillgängliga för alla som har åtkomst till resurs gruppen. När du har sparat den anpassade arbets boken måste du gå till arbets boks galleriet för att starta den.

![Skärm bild av arbets bokens Galleri](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Ange en prenumeration eller ett nyckel valv

Du kan konfigurera översikten över flera prenumerationer och nyckel valv, så att arbets böcker kan omfattas av en viss prenumeration eller nyckel valv på varje körning, genom att utföra följande steg:

1. Välj **övervakare** från portalen och välj sedan **nyckel valv (förhands granskning)** i det vänstra fönstret.
2. I **översikts** arbets boken väljer du **Redigera**i kommando fältet.
3. Välj i list rutan **prenumerationer** en eller flera prenumerationer som du vill att Yo ska använda som standard. Kom ihåg att arbets boken har stöd för att välja upp till totalt 10 prenumerationer.
4. Välj från List rutan **nyckel valv** ett eller flera konton som du vill att den ska använda som standard. Kom ihåg att arbets boken har stöd för att välja upp till totalt 200 lagrings konton.
5. Välj **Spara som** från kommando fältet för att spara en kopia av arbets boken med dina anpassningar och klicka sedan på **klar redigering** för att återgå till läsläge.

## <a name="troubleshooting"></a>Felsökning

Allmän fel söknings vägledning finns i artikeln om dedikerad informations [fel sökning](troubleshoot-workbooks.md)för arbets böcker.

I det här avsnittet får du hjälp med diagnos och fel sökning av några vanliga problem som kan uppstå när du använder Azure Monitor för Key Vault (för hands version). Använd listan nedan för att hitta den information som är relevant för det aktuella problemet.

### <a name="resolving-performance-issues-or-failures"></a>Lösa prestanda problem eller fel

Information om hur du felsöker viktiga valv relaterade problem som du identifierar med Azure Monitor för Key Vault (för hands version) finns i [Azure Key Vault-dokumentationen](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Varför kan jag bara se 200-nyckel valv

Det finns en gräns på 200 nyckel valv som kan väljas och visas. Oavsett antalet valda prenumerationer har antalet valda nyckel valv en gräns på 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Varför visas inte alla mina prenumerationer i prenumerations väljaren

Vi visar bara prenumerationer som innehåller nyckel valv, som väljs från det valda prenumerations filtret, som väljs i "katalog + prenumeration" i Azure Portals huvudet.

![Skärm bild av prenumerations filter](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Jag får ett fel meddelande om att "frågan överskrider det högsta antalet arbets ytor/regioner som tillåts", vad du gör nu

För närvarande finns det en gräns på 25 regioner och 200 arbets ytor för att visa dina data. du måste minska antalet prenumerationer och/eller resurs grupper.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Jag vill göra ändringar eller lägga till ytterligare visualiseringar för att Key Vault insikter, hur gör jag för att

Om du vill göra ändringar väljer du "redigerings läge" för att ändra arbets boken. sedan kan du spara arbetet som en ny arbets bok som är knuten till en angiven prenumeration och resurs grupp.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Vad är tids kornen när vi fäster någon del av arbets böckerna

Vi använder "Auto" Time-kornig het, vilket innebär att den är beroende av vilket tidsintervall som väljs.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Vad är tidsintervallet när någon del av arbets boken är fäst

Tidsintervallet beror på instrument panelens inställningar.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Varför visas inte några data för mina Key Vault under avsnittet åtgärder & svars tid

Om du vill visa dina loggarbaserade data måste du aktivera loggar för varje nyckel valv som du vill övervaka. Detta kan göras under diagnostikinställningar för varje nyckel valv. Du måste skicka dina data till en angiven Log Analytics-arbetsyta.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Jag har redan aktiverat loggar för min Key Vault, varför kan jag fortfarande inte se mina data under åtgärder & svars tid

Diagnostikloggar fungerar för närvarande inte retroaktivt, så data kommer bara att börja visas när det har skett åtgärder i nyckel valven. Därför kan det ta lite tid, från timmar till en dag, beroende på hur aktivt nyckel valvet är.

Om du har ett stort antal nyckel valv och-prenumerationer valda kan du dessutom inte Visa dina data på grund av frågans begränsningar. Du kan behöva minska antalet valda prenumerationer eller nyckel valv för att kunna visa dina data. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Vad händer om jag vill se andra data eller göra egna visualiseringar? Hur kan jag göra ändringar i Key Vault Insights

Du kan redigera den befintliga arbets boken genom att använda redigerings läget och sedan spara ditt arbete som en ny arbets bok som kommer att ha alla dina nya ändringar.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../platform/workbooks-overview.md).
