---
title: Felsöka övervakare av Windows Virtual Desktop – Azure
description: Fel sökning av problem med Azure Monitor för virtuella Windows-datorer.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: dda58868432248fe93a9fbc83d1e538dfc9b61ba
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709454"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop"></a>Felsöka Azure Monitor för virtuella Windows-datorer

Den här artikeln visar kända problem och lösningar för vanliga problem i Azure Monitor för virtuella Windows-datorer.

## <a name="issues-with-configuration-and-setup"></a>Problem med konfiguration och installation

Om konfigurations arbets boken inte fungerar korrekt för att automatisera installationen kan du använda dessa resurser för att konfigurera din miljö manuellt:

- Om du vill aktivera diagnostik manuellt eller komma åt Log Analytics-arbetsytan läser du [Skicka diagnostik för virtuella Windows-datorer till Log Analytics](diagnostics-log-analytics.md).
- Om du vill installera Log Analytics-tillägget på en sessions värd manuellt, se [Log Analytics tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md).
- Om du vill konfigurera en ny Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Information om hur du lägger till, tar bort eller redigerar prestanda räknare finns i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md).
- Information om hur du konfigurerar Windows-händelseloggar för en Log Analytics arbets yta finns i [samla in Windows händelse logg data källor med Log Analytics-agenten](../azure-monitor/agents/data-sources-windows-events.md).

## <a name="my-data-isnt-displaying-properly"></a>Mina data visas inte korrekt

Om dina data inte visas korrekt kontrollerar du följande vanliga lösningar:

- Kontrol lera först att du har konfigurerat rätt konfigurations arbets bok enligt beskrivningen i [använda Azure Monitor för Windows Virtual Desktop för att övervaka distributionen](azure-monitor.md). Om du saknar räknare eller händelser visas inte de data som är kopplade till dem i Azure Portal.
- Kontrol lera åtkomst behörigheterna & kontakta resurs ägarna för att begära behörigheter som saknas. alla som övervakar virtuella Windows-skrivbord kräver följande behörigheter:
    - Läs åtkomst till de Azure-prenumerationer som innehåller dina Windows-resurser för virtuella skriv bord
    - Läs åtkomst till prenumerationens resurs grupper som innehåller dina virtuella Windows-värdar för fjärrskrivbordssession 
    - Läs åtkomst till de Log Analytics arbets ytor som du använder
- Du kan behöva öppna utgående portar i serverns brand vägg för att tillåta Azure Monitor och Log Analytics att skicka data till portalen. Information om hur du gör detta finns i följande artiklar:
      - [Azure Monitor utgående portar](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics brand Väggs krav](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements). 
- Ser du inte data från senaste aktivitet? Du kanske vill vänta i 15 minuter och uppdatera flödet. Azure Monitor har en svars tid på 15 minuter för att fylla logg data. Läs mer i logg data Inhämtnings [tid i Azure Monitor](../azure-monitor/logs/data-ingestion-time.md).

Om du inte saknar någon information men dina data fortfarande inte visas korrekt, kan det finnas ett problem i frågan eller data källorna. Granska [kända problem och begränsningar](#known-issues-and-limitations). 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Jag vill anpassa Azure Monitor för virtuellt Windows-skrivbord

Azure Monitor för virtuella Windows-datorer använder Azure Monitor-arbetsböcker. Med arbets böcker kan du spara en kopia av mallen för Windows Virtual Desktop-arbetsböcker och göra egna anpassningar.

Anpassade mallar för arbets böcker kommer inte automatiskt att införa uppdateringar från gruppen produkter. Mer information finns i [Felsöka arbets boksbaserade insikter](../azure-monitor/insights/troubleshoot-workbooks.md) och [Översikt över arbets böcker](../azure-monitor/visualize/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Jag kan inte tolka data

Lär dig mer om data termer på [Azure Monitor för Virtual Desktop-ordlista](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>De data jag behöver är inte tillgängliga

Om du vill övervaka fler prestanda räknare eller Windows-händelseloggar kan du göra det möjligt för dem att skicka diagnostikinformation till din Log Analytics arbets yta och övervaka dem i **Host Diagnostics: värd webbläsare**. 

- Information om hur du lägger till prestanda räknare finns i [Konfigurera prestanda räknare](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- Information om hur du lägger till Windows-händelser finns i [Konfigurera händelse loggar i Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

Kan du inte hitta någon data punkt för att diagnostisera ett problem? Skicka oss feedback!

- Information om hur du lämnar feedback finns i [fel söknings översikt, feedback och support för Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

Följande är problem och begränsningar vi är medvetna om och arbetar med att åtgärda:

- Du kan bara övervaka en adresspool i taget. 
- Om du vill spara favorit inställningarna måste du spara en anpassad mall i arbets boken. Anpassade mallar kommer inte automatiskt att införa uppdateringar från produkt gruppen.
- Konfigurations arbets boken kommer ibland att visa fel när dina val lästes in. Uppdatera frågan, ange ditt val igen om det behövs och felet bör lösa sig självt. 
- Vissa fel meddelanden är inte formulerade på ett användarvänligt sätt och alla fel meddelanden beskrivs inte i dokumentationen.
- Prestanda räknaren Totalt antal sessioner kan överskrida sessioner med ett litet nummer och det totala antalet sessioner kan verka som om gränsen är max.
- Antalet tillgängliga sessioner återspeglar inte skalnings principer för den här poolen.   
- Ser du motstridiga eller oväntade anslutnings tider? I sällsynta fall kan en anslutnings slut för ande händelse gå förlorad och kan påverka vissa visuella objekt och mått.
- Tid för anslutning omfattar den tid det tar för användarna att ange sina autentiseringsuppgifter. Detta motsvarar upplevelsen, men i vissa fall kan du Visa falskt-toppar. 
    

## <a name="next-steps"></a>Nästa steg

Om du är osäker på hur du tolkar data eller vill lära dig mer om vanliga villkor kan du läsa [Azure Monitor för ord listan för virtuella Windows-datorer](azure-monitor-glossary.md). Om du vill lära dig hur du konfigurerar och använder Azure Monitor för virtuella Windows-datorer, se [använd Azure Monitor för Windows Virtual Desktop för att övervaka distributionen](azure-monitor.md).
