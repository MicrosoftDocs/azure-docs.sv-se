---
title: Windows 7 Virtual Machines Windows Virtual Desktop (klassisk) – Azure
description: 'Lösa problem för virtuella Windows 7-datorer (VM: ar) i en Windows Virtual Desktop-miljö (klassisk).'
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 99c1c76092714c58fd13d9ce51ef850971d80c86
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444130"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Felsöka virtuella Windows 7-datorer i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Använd den här artikeln för att felsöka problem som du har med när du konfigurerar virtuella Windows-sessioner för fjärrskrivbordssessioner värd för virtuella datorer (VM).

## <a name="known-issues"></a>Kända problem

Windows 7 på virtuella Windows-datorer har inte stöd för följande funktioner:

- Virtualiserade program (RemoteAppar)
- Omdirigering av tidszon
- Automatisk DPI-skalning

Virtuella Windows-datorer kan bara virtualisera fullständiga skriv bord för Windows 7.

Även om automatisk DPI-skalning inte stöds, kan du manuellt ändra lösning på den virtuella datorn genom att högerklicka på ikonen i fjärr skrivbords klienten och välja **lösning**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fel: det går inte att komma åt användar gruppen fjärr skrivbord

Om det inte går att hitta dig eller dina användares autentiseringsuppgifter i det virtuella Windows-skrivbordet kan du se något av följande fel meddelanden:

- "Den här användaren är inte medlem i användar gruppen fjärr skrivbord"
- "Du måste beviljas behörighet att logga in via Fjärrskrivbordstjänster"

Du kan åtgärda det här felet genom att lägga till användaren i användar gruppen fjärr skrivbord:

1. Öppna Azure-portalen.
2. Välj den virtuella dator som du såg fel meddelandet på.
3. Välj **kör ett kommando**.
4. Kör följande kommando och `<username>` Ersätt med namnet på den användare som du vill lägga till:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```