---
title: Distribuera Windows 7 virtuell dator Windows Virtual Desktop – Azure
description: Så här konfigurerar och distribuerar du en virtuell Windows 7-dator på Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 65ef7af1247fdb38a7c85c1a8134257942f5ef5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447037"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Distribuera en virtuell Windows 7-dator på Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Processen för att distribuera en virtuell Windows 7-dator (VM) på virtuella Windows-datorer skiljer sig något från för virtuella datorer som kör senare versioner av Windows. I den här guiden får du lära dig hur du distribuerar Windows 7.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar följer du anvisningarna i [skapa en adresspool med PowerShell](create-host-pools-powershell.md) för att skapa en adresspool. Om du använder portalen följer du anvisningarna i steg 1 till 9 i [skapa en värdbaserad pool med hjälp av Azure Portal](create-host-pools-azure-marketplace.md). Sedan väljer du **Granska + skapa** för att skapa en tom adresspool.

## <a name="configure-a-windows-7-virtual-machine"></a>Konfigurera en virtuell Windows 7-dator

När du har genomfört kraven är du redo att konfigurera din virtuella Windows 7-dator för distribution på Windows Virtual Desktop.

Konfigurera en virtuell Windows 7-dator på Windows Virtual Desktop:

1. Logga in på Azure Portal och Sök antingen efter Windows 7 Enterprise-avbildningen eller ladda upp din egen anpassade Windows 7 Enterprise-avbildning (x64).
2. Distribuera en eller flera virtuella datorer med Windows 7 Enterprise som värd operativ system. Se till att de virtuella datorerna tillåter Remote Desktop Protocol (RDP) (TCP/3389-porten).
3. Anslut till Windows 7 Enterprise-värden med hjälp av RDP och autentisera med de autentiseringsuppgifter som du definierade när du konfigurerade distributionen.
4. Lägg till det konto som du använde när du anslöt till värden med RDP till gruppen "fjärr skrivbords användare". Om du inte lägger till kontot kanske du inte kan ansluta till den virtuella datorn när du har anslutit den till din Active Directory-domän.
5. Gå till Windows Update på den virtuella datorn.
6. Installera alla Windows-uppdateringar i den viktiga kategorin.
7. Installera alla Windows-uppdateringar i den valfria kategorin (exklusive språk paket). Den här processen installerar Remote Desktop Protocol 8,0-uppdateringen ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) som du behöver för att slutföra de här instruktionerna.
8. Öppna redigerare för lokalt grupprincipobjekt och navigera till **dator konfiguration**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **fjärrskrivbordssessionen**  >  för fjärrskrivbordssessioner.
9. Aktivera Remote Desktop Protocol 8,0-principen.
10. Anslut den här virtuella datorn till din Active Directory-domän.
11. Starta om den virtuella datorn genom att köra följande kommando:

     ```cmd
     shutdown /r /t 0
     ```

12. Följ anvisningarna [här](/powershell/module/az.desktopvirtualization/new-azwvdregistrationinfo) för att hämta en registrerings-token.

      - Om du hellre vill använda Azure Portal kan du också gå till sidan Översikt för den värddator som du vill lägga till den virtuella datorn i och skapa en token där.

13. [Hämta Windows Virtual Desktop-agenten för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Ladda ned Windows Virtual Desktop Agent Manager för Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Öppna installations programmet för Windows Virtual Desktop agent och följ instruktionerna. När du uppmanas till det, ger du registrerings nyckeln som du skapade i steg 12.
16. Öppna Windows Virtual Desktop Agent Manager och följ anvisningarna.
17. Du kan också blockera TCP/3389-porten för att ta bort direkt Remote Desktop Protocol åtkomst till den virtuella datorn.
18. Du kan också kontrol lera att .NET Framework är minst version 4.7.2. Uppdatering av ramverket är särskilt viktigt om du skapar en anpassad avbildning.

## <a name="next-steps"></a>Nästa steg

Din Windows-distribution för virtuella datorer är nu redo att användas. [Hämta den senaste versionen av Windows Virtual Desktop-klienten](https://aka.ms/wvd/clients/windows) för att komma igång.

En lista med kända problem och fel söknings anvisningar för Windows 7 på Windows Virtual Desktop finns i vår fel söknings artikel på [Felsöka Windows 7 Virtual Machines i Windows Virtual Desktop](./virtual-desktop-fall-2019/troubleshoot-windows-7-vm.md).
