---
title: Vanliga frågor och svar om Windows 10 Enterprise multi-session – Azure
description: Vanliga frågor och bästa metoder för att använda Windows 10 Enterprise multi-session för virtuella Windows-datorer.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aafac1133b2bee54624d5f0b00061c5332fcd90b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631888"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Vanliga frågor och svar om flera sessioner för Windows 10 Enterprise

I den här artikeln besvaras vanliga frågor och rekommendationer för Windows 10 Enterprise multi-session.

## <a name="what-is-windows-10-enterprise-multi-session"></a>Vad är Windows 10 Enterprise multi-session?

Windows 10 Enterprise multi-session, tidigare känt som Windows 10 Enterprise för virtuella skriv bord (EVD), är en ny värd för fjärrskrivbordssession som tillåter flera samtidiga interaktiva sessioner. Tidigare kunde endast Windows Server göra detta. Den här funktionen ger användarna en välbekant Windows 10-upplevelse medan den kan dra nytta av kostnads fördelarna med multi-session och använda befintliga Windows-licensiering per användare i stället för klient åtkomst licenser för fjärr skrivbords tjänster. Mer information om licenser och priser finns i [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hur många användare kan samtidigt ha en interaktiv session i Windows 10 Enterprise multi-session?

Hur många interaktiva sessioner som kan vara aktiva samtidigt är beroende av systemets maskin varu resurser (vCPU, minne, disk och vGPU), hur dina användare använder sina appar när de är inloggade i en session och hur lång tid ditt Systems arbets belastning är. Vi rekommenderar att du validerar systemets prestanda för att förstå hur många användare du kan ha på Windows 10 Enterprise multi-session. Mer information finns i [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Varför rapporterar min app Windows 10 Enterprise multi-session som ett serveroperativ system?

Windows 10 Enterprise multi-session är en virtuell utgåva av Windows 10 Enterprise. En av skillnaderna är att operativ systemet (OS) rapporterar [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) med värdet 3, samma värde som Windows Server. Den här egenskapen ser till att operativ systemet är kompatibelt med befintliga hanterings verktyg för RDSH, RDSH-program för flera sessioner och de flesta prestanda optimeringar på låg nivå för RDSH-miljöer. Vissa program installations program kan blockera installation på Windows 10 multi-session beroende på om de identifierar att ProductType är inställt på Client. Om din app inte installeras kan du kontakta program leverantören för att få en uppdaterad version.

## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Kan jag köra Windows 10 Enterprise multi-session lokalt?

Windows 10 Enterprise multi-session kan inte köras i lokala produktions miljöer eftersom den är optimerad för Windows Virtual Desktop-tjänsten för Azure. Det är gentemot licens avtalet att köra Windows 10 Enterprise multi-session utanför Azure i produktions syfte. Windows 10 Enterprise multi-session aktive ras inte mot lokala nyckel hanterings tjänster (KMS).

## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hur gör jag för att anpassa Windows 10 Enterprise-avbildningen för flera sessioner för min organisation?

Du kan starta en virtuell dator (VM) i Azure med Windows 10 Windows 10 Enterprise multi-session och anpassa den genom att installera LOB-program, sysprep/generalize och sedan skapa en avbildning med hjälp av Azure Portal.

Kom igång genom att skapa en virtuell dator i Azure med Windows 10 Enterprise multi-session. I stället för att starta den virtuella datorn i Azure kan du ladda ned den virtuella hård disken direkt. Därefter kan du använda den virtuella hård disk som du laddade ned för att skapa en ny virtuell dator i generation 1 på en Windows 10-dator med Hyper-V aktiverat.

Anpassa avbildningen efter dina behov genom att installera LOB-program och Sysprep-avbildningen. När du är klar med anpassningen laddar du upp avbildningen till Azure med den virtuella hård disken i. Sedan hämtar du det virtuella Windows-skrivbordet från Azure Marketplace och använder det för att distribuera en ny adresspool med den anpassade avbildningen.

## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hur gör jag för att hantera Windows 10 Enterprise multi-session efter distribution?

Du kan använda valfritt konfigurations verktyg som stöds, men vi rekommenderar Configuration Manager version 1906 eftersom det stöder Windows 10 Enterprise multi-session. Vi arbetar för närvarande med Microsoft Intune support.

## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Kan Windows 10 Enterprise multi-session vara Azure Active Directory (AD)-ansluten?

Windows 10 Enterprise multi-session stöds för närvarande som hybrid Azure AD-ansluten. När Windows 10 Enterprise multi-session är domänansluten använder du det befintliga grupprincip-objektet för att aktivera Azure AD-registrering. Mer information finns i [Planera hybrid Azure Active Directory Join-implementeringen](../active-directory/devices/hybrid-azuread-join-plan.md).

## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Var kan jag hitta Windows 10 Enterprise multi-session-avbildningen?

Windows 10 Enterprise multi-session finns i Azure-galleriet. Du hittar det genom att gå till Azure Portal och söka efter en utgåva av Windows 10 Enterprise för virtuella skriv bord. För en avbildning som är integrerad med Microsoft 365 appar för företag går du till Azure Portal och söker efter **Microsoft Windows 10 + Microsoft 365-appar för företag**.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Vilken Windows 10 Enterprise-avbildning av flera sessioner ska jag använda?

Azure-galleriet har flera versioner, inklusive Windows 10 Enterprise multi-session, version 1809 och Windows 10 Enterprise multi-session, version 1903. Vi rekommenderar att du använder den senaste versionen för bättre prestanda och tillförlitlighet.

## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Vilka Windows 10 Enterprise-versioner av flera sessioner stöds?

Windows 10 Enterprise multi-session, version 1809 och senare stöds och är tillgängliga i Azure-galleriet. De här versionerna följer samma support livs cykel princip som Windows 10 Enterprise, vilket innebär att den mars utgåvan stöds i 18 månader och den september som ingår i 30 månader.

## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Vilken profil hanterings lösning ska jag använda för Windows 10 Enterprise multi-session?

Vi rekommenderar att du använder FSLogix profil behållare när du konfigurerar Windows 10 Enterprise i icke-beständiga miljöer eller andra scenarier som behöver en centralt lagrad profil. FSLogix garanterar att användar profilen är tillgänglig och aktuell för varje användarsession. Vi rekommenderar också att du använder en FSLogix profil behållare för att lagra en användar profil i en SMB-resurs med rätt behörigheter, men du kan lagra användar profiler i Azure-sidans blob-lagring om det behövs. Användare av virtuella Windows-datorer kan använda FSLogix utan extra kostnad.  FSLogix är förinstallerat på alla Windows 10 Enterprise multi-session-avbildningar, men IT-administratören ansvarar fortfarande för att konfigurera FSLogix Profile-behållaren.

Mer information om hur du konfigurerar en FSLogix profil behållare finns i [Konfigurera FSLogix profil container](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Vilken licens behöver jag för att få åtkomst till Windows 10 Enterprise multi-session?

En fullständig lista över tillämpliga licenser finns i [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Varför försvinner Mina appar när jag loggar ut?

Detta beror på att du använder Windows 10 Enterprise multi-session med en profil hanterings lösning som FSLogix. Din administratörs-eller profil lösning har konfigurerat systemet att ta bort användar profiler när användare loggar ut. Den här konfigurationen innebär att när ditt system tar bort din användar profil när du har loggat ut, tas även alla appar som du har installerat bort under sessionen. Om du vill behålla de appar som du har installerat måste du be din administratör att etablera dessa appar för alla användare i din Windows-miljö för virtuella datorer.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hur gör jag för att se till att appar inte försvinner när användarna loggar ut?

De flesta virtualiserade miljöer konfigureras som standard för att hindra användare från att installera ytterligare appar till sina profiler. Om du vill vara säker på att en app inte försvinner när användaren loggar ut från det virtuella Windows-skrivbordet måste du etablera appen för alla användar profiler i din miljö. Mer information om hur du konfigurerar appar finns i följande resurser:

- [Publicera inbyggda appar i Windows Virtual Desktop](publish-apps.md)
- [Kommando rads alternativ för DISM-paket underhåll](/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](/powershell/module/dism/add-appxprovisionedpackage)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hur gör jag för att se till att användarna inte laddar ned och installerar appar från Microsoft Store?

Du kan inaktivera Microsoft Store-appen för att se till att användarna inte laddar ned extra appar utöver de appar som du redan har tillhandahållit.

Så här inaktiverar du Store-appen:

1. Skapa en ny grupprincip.
2. Välj **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**.
3. Välj **Arkiv**.
4. Välj **Store-program**.
5. Välj **inaktive rad** och välj sedan **OK**.
6. Välj **Använd**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Windows Virtual Desktop och Windows 10 Enterprise multi-session:

- Läs vår [dokumentation om Windows Virtual Desktop](overview.md)
- Besök vår [TechCommunity för virtuella Windows-datorer](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Konfigurera din Windows-distribution av virtuella datorer med [självstudierna för Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)
