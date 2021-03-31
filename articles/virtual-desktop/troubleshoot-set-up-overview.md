---
title: Översikt över fel sökning av Windows Virtual Desktop – Azure
description: En översikt för fel sökning av problem när du konfigurerar en Windows Virtual Desktop-miljö.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4d4bdc4fa15f634b36f12a650b70b9ffd89b40e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539108"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Fel sökning – översikt, feedback och support för Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md).

Den här artikeln innehåller en översikt över de problem som du kan stöta på när du konfigurerar en Windows Virtual Desktop-miljö och ger möjlighet att lösa problemen.

## <a name="report-issues"></a>Rapportera problem

Om du vill rapportera problem eller föreslå funktioner för virtuella Windows-datorer med Azure Resource Manager-integrering kan du gå till [Windows Virtual Desktop-Tech-community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop). Du kan använda Tech-communityn för att diskutera bästa praxis eller föreslå och rösta på nya funktioner.

När du gör ett inlägg som ber om hjälp eller föreslår en ny funktion ska du se till att du beskriver ditt ämne i så mycket information som möjligt. Detaljerad information kan hjälpa andra användare att besvara din fråga eller förstå den funktion som du föreslår en röst för.

## <a name="escalation-tracks"></a>Eskalerade spår

Innan du gör något annat måste du kontrol lera [sidan Azure-status](https://status.azure.com/status) och [Azure Service Health](https://azure.microsoft.com/features/service-health/) för att kontrol lera att Azure-tjänsten körs på rätt sätt.

Använd följande tabell för att identifiera och lösa problem som kan uppstå när du konfigurerar en miljö med hjälp av fjärr skrivbords klienten. När din miljö har kon figurer ATS kan du använda vår nya [diagnostik-tjänst](diagnostics-role-service.md) för att identifiera problem med vanliga scenarier.

| **Problem**                                                            | **Föreslagen lösning**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Session Host pool Azure-Virtual Network (VNET) och Express Route-inställningar               | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/)och välj lämplig tjänst (under kategorin nätverk). |
| Skapa virtuell dator för Session Host pool (VM) när Azure Resource Manager mallar som medföljer Windows Virtual Desktop används inte | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/)och välj sedan **Windows Virtual Desktop** för tjänsten. <br> <br> Om du har problem med Azure Resource Manager mallar som medföljer Windows Virtual Desktop kan du läsa avsnittet Azure Resource Manager mall fel i [skapa en Fakturapool](troubleshoot-set-up-issues.md). |
| Hantera Windows virtuell skrivbord värd miljö från Azure Portal    | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> För hanterings problem när du använder Fjärrskrivbordstjänster/Windows Virtual Desktop PowerShell, se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) eller [öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), Välj **Windows Virtual Desktop** för tjänsten, Välj **konfiguration och hantering** för problem typen och välj sedan **problem med att konfigurera miljön med PowerShell** för problem under typen. |
| Hantera konfiguration av virtuella Windows-skrivbordet knutna till värdar och program grupper (app-grupper)      | Se [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)eller [öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), Välj **Windows Virtual Desktop** för tjänsten och välj sedan lämplig problem typ.|
| Distribuera och hantera FSLogix profil behållare | Se [fel söknings guide för FSLogix-produkter](/fslogix/fslogix-trouble-shooting-ht/) och om det inte löser [problemet öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten, väljer **FSLogix** för problem typen och väljer sedan motsvarande typ av problem. |
| Fjärr skrivbords klienter fungerar inte på Start                                                 | Se [Felsöka fjärr skrivbords klienten](troubleshoot-client.md) och om det inte löser problemet  [öppnar du en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten och väljer sedan **fjärr skrivbords klienter** för problem typen.  <br> <br> Om det är ett nätverks problem måste användarna kontakta sin nätverks administratör. |
| Ansluten men ingen feed                                                                 | Felsök med hjälp av [användaren ansluter men ingenting visas (ingen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) i [Windows Virtual Desktop Service-anslutningar](troubleshoot-service-connection.md). <br> <br> Om dina användare har tilldelats en app-grupp öppnar du  [en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/), väljer **Windows Virtual Desktop** för tjänsten och väljer sedan **fjärr skrivbords klienter** för problem typen. |
| Problem med identifiering av feed på grund av nätverket                                            | Användarna måste kontakta sin nätverks administratör. |
| Ansluta klienter                                                                    | Se [Windows Virtual Desktop Service-anslutningar](troubleshoot-service-connection.md) och om det inte löser problemet kan du läsa [konfiguration av virtuell dator i Session Host](troubleshoot-vm-configuration.md). |
| Svars tider för fjärrprogram eller skriv bord                                      | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |
| Licensierings meddelanden eller fel                                                          | Om problemen är knutna till en specifik applikation eller produkt kontaktar du det team som ansvarar för produkten. |
| Problem med autentiseringsmetoder eller-verktyg från tredje part | Kontrol lera att din tredjeparts leverantör stöder scenarier för virtuella Windows-datorer och se om det finns några kända problem. |
| Problem med att använda Log Analytics för virtuella Windows-datorer | [Öppna en support förfrågan för Azure](https://azure.microsoft.com/support/create-ticket/)om du har problem med det diagnostiska schemat.<br><br>För frågor, visualisering eller andra problem i Log Analytics väljer du lämplig problem typ under Log Analytics. |
| Problem med M365-appar | Kontakta M365 administrations Center med något av [Hjälp alternativen för M365 administrations Center](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker problem när du skapar en adresspool i en Windows Virtual Desktop-miljö finns i [skapa pooler för värdar](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem som rör agenten för virtuella Windows-datorer eller sessioner finns i [Felsöka vanliga problem med Windows Virtual Desktop agent](troubleshoot-agent.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md)
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa fel under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
