---
title: Distribuera Windows 7 Virtual Machine Windows Virtual Desktop (klassisk) – Azure
description: Så här konfigurerar och distribuerar du en virtuell Windows 7-dator på Windows Virtual Desktop (klassisk).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005060"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Distribuera en virtuell Windows 7-dator på Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows-skrivbordet som introduceras i den aktuella versionen av Windows Virtual Desktop, kan du läsa [den här artikeln](../deploy-windows-7-virtual-machine.md).

Processen för att distribuera en virtuell Windows 7-dator (VM) på virtuella Windows-datorer skiljer sig något från för virtuella datorer som kör senare versioner av Windows. I den här guiden får du lära dig hur du distribuerar Windows 7.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar följer du anvisningarna i [skapa en adresspool med PowerShell](create-host-pools-powershell-2019.md) för att skapa en adresspool. Efter det följer du instruktionerna i [skapa lagringspooler på Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) för att tilldela en eller flera användare till program gruppen Skriv bord.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurera en virtuell Windows 7-dator

När du har genomfört kraven är du redo att konfigurera din virtuella Windows 7-dator för distribution på Windows Virtual Desktop.

Konfigurera en virtuell Windows 7-dator på Windows Virtual Desktop:

1. Logga in på Azure Portal och Sök antingen efter Windows 7 Enterprise-avbildningen eller ladda upp din egen anpassade Windows 7 Enterprise-avbildning (x64).
2. Distribuera en eller flera virtuella datorer med Windows 7 Enterprise som värd operativ system. Se till att de virtuella datorerna tillåter Remote Desktop Protocol (RDP) (TCP/3389-porten).
3. Anslut till Windows 7 Enterprise-värden med hjälp av RDP och autentisera med de autentiseringsuppgifter som du definierade när du konfigurerade distributionen.
4. Lägg till det konto som du använde när du anslöt till värden med RDP till gruppen "fjärr skrivbords användare". Om du inte gör det kanske du inte kan ansluta till den virtuella datorn när du har anslutit den till din Active Directory-domän.
5. Gå till Windows Update på den virtuella datorn.
6. Installera alla Windows-uppdateringar i den viktiga kategorin.
7. Installera alla Windows-uppdateringar i den valfria kategorin (exklusive språk paket). Detta installerar Remote Desktop Protocol 8,0-uppdateringen ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) som du behöver för att slutföra de här instruktionerna.
8. Öppna redigerare för lokalt grupprincipobjekt och navigera till **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **fjärrskrivbordssessionen**  >  för fjärrskrivbordssessioner.
9. Aktivera Remote Desktop Protocol 8,0-principen.
10. Anslut den här virtuella datorn till din Active Directory-domän.
11. Starta om den virtuella datorn genom att köra följande kommando:

     ```cmd
     shutdown /r /t 0
     ```

12. Följ anvisningarna [här](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) för att hämta en registrerings-token.
13. [Hämta Windows Virtual Desktop-agenten för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Ladda ned Windows Virtual Desktop Agent Manager för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Öppna installations programmet för Windows Virtual Desktop agent och följ instruktionerna. När du uppmanas till det, ger du registrerings nyckeln som du skapade i steg 12.
16. Öppna Windows Virtual Desktop Agent Manager och följ anvisningarna.
17. Du kan också blockera TCP/3389-porten för att ta bort direkt Remote Desktop Protocol åtkomst till den virtuella datorn.
18. Du kan också kontrol lera att .NET Framework är minst version 4.7.2. Detta är särskilt viktigt om du skapar en anpassad avbildning.

## <a name="next-steps"></a>Nästa steg

Din Windows-distribution för virtuella datorer är nu redo att användas. [Hämta den senaste versionen av Windows Virtual Desktop-klienten](https://aka.ms/wvd/clients/windows) för att komma igång.

En lista med kända problem och fel söknings anvisningar för Windows 7 på Windows Virtual Desktop finns i vår fel söknings artikel på [Felsöka Windows 7 Virtual Machines i Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
