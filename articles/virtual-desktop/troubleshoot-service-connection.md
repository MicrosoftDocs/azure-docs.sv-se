---
title: Felsöka tjänst anslutning Windows Virtual Desktop – Azure
description: Lösa problem när du konfigurerar tjänst anslutningar i en Windows-klient för virtuella skriv bord.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539074"
---
# <a name="windows-virtual-desktop-service-connections"></a>Anslutningar till virtuella Windows-datorer

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Använd den här artikeln för att lösa problem med klient anslutningar för virtuella Windows-datorer.

## <a name="provide-feedback"></a>Ge feedback

Du kan ge oss feedback och diskutera Windows Virtual Desktop-tjänsten med produkt teamet och andra aktiva community-medlemmar i [Tech-communityn för Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men inget visas (ingen feed)

En användare kan starta fjärr skrivbords klienter och kan autentisera, men användaren ser inga ikoner i webb identifierings flödet.

1. Bekräfta att användaren som rapporterar problemen har tilldelats program grupper genom att använda den här kommando raden:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Bekräfta att användaren har loggat in med rätt autentiseringsuppgifter.

3. Om webb klienten används kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

4. Om användaren är en del av en Azure Active Directory (AD)-användar grupp kontrollerar du att användar gruppen är en säkerhets grupp i stället för en distributions grupp. Virtuella Windows-datorer stöder inte Azure AD-distributions grupper.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>Användaren förlorar en befintlig feed och ingen fjär resurs visas (ingen feed)

Det här felet visas vanligt vis när en användare har flyttat prenumerationen från en Azure AD-klient till en annan. Därför förlorar tjänsten spårning av användar tilldelningar eftersom de fortfarande är knutna till den gamla Azure AD-klienten.

För att lösa detta behöver du bara tilldela om användarna till sina app-grupper.

Detta kan också inträffa om en CSP-Provider skapade prenumerationen och sedan överförts till kunden. För att lösa detta omregistrerar du resurs leverantören.

1. Logga in på Azure-portalen.
2. Gå till **prenumeration** och välj din prenumeration.
3. I menyn på vänster sida av sidan väljer du **resurs leverantör**.
4. Leta upp och markera **Microsoft. DesktopVirtualization** och välj sedan **registrera igen**.

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Om du vill felsöka problem när du skapar en Windows Virtual Desktop-miljö och en adresspool i en Windows Virtual Desktop-miljö kan du läsa mer i [miljö-och värd bassäng](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem som rör agenten för virtuella Windows-datorer eller sessioner finns i [Felsöka vanliga problem med Windows Virtual Desktop agent](troubleshoot-agent.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
