---
title: Windows Virtual Desktop (klassisk)-miljö – Azure
description: De grundläggande elementen i en Windows Virtual Desktop-miljö (klassisk).
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4fa281f6435013e9feb6808aab04ffa69c5f96c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008618"
---
# <a name="windows-virtual-desktop-classic-environment"></a>Windows Virtual Desktop-miljö (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../environment-setup.md).

Windows Virtual Desktop är en tjänst som ger användare enkel och säker åtkomst till sina virtualiserade skriv bord och RemoteApp-datorer. I det här avsnittet får du veta mer om den allmänna strukturen i Windows Virtual Desktop-miljön.

## <a name="tenants"></a>Klientorganisationer

Windows Virtual Desktop-klienten är det primära gränssnittet för att hantera din Windows-miljö för virtuella datorer. Varje Windows-klient för virtuella skriv bord måste vara kopplad till Azure Active Directory som innehåller de användare som ska logga in på miljön. Från Windows-klienten för virtuella skriv bord kan du börja skapa värdar för att köra användarnas arbets belastningar.

## <a name="host-pools"></a>Värdar för pooler

En adresspool är en samling virtuella Azure-datorer som registreras på Windows Virtual Desktop som värdar för Windows Virtual Desktop-agenten. Alla virtuella datorer i en värdstat i en adresspool måste ha en konsekvent användar upplevelse från samma avbildning.

En värdbaserad pool kan vara en av två typer:

- Personligt, där varje sessions värd tilldelas enskilda användare.
- I pooler, där sessions-värdar kan acceptera anslutningar från alla användare som har behörighet till en app-grupp i poolen värd.

Du kan ange ytterligare egenskaper för poolen för att ändra dess belastnings Utjämnings beteende, hur många sessioner varje sessions värd kan ta och vad användaren kan göra för att agera värdar i värddatorn när de loggat in på sina Windows-sessioner med virtuella skriv bord. Du styr vilka resurser som publiceras till användare via app-grupper.

## <a name="app-groups"></a>Appgrupper

En app-grupp är en logisk gruppering av program som är installerade på värdarna i värd gruppen. En app-grupp kan vara en av två typer:

- RemoteApp, där användarna får åtkomst till de RemoteAppar du individuellt väljer och publicerar i app-gruppen
- Skriv bord, där användare kommer åt hela Skriv bordet

Som standard skapas en Skriv bords grupp (med namnet "Skriv bords program grupp") automatiskt när du skapar en adresspool. Du kan när som helst ta bort den här program gruppen. Du kan dock inte skapa en annan grupp för Skriv bords appar i poolen värd när en grupp app-grupp finns. Om du vill publicera RemoteApp-program måste du skapa en RemoteApp-gruppgrupp. Du kan skapa flera RemoteApp-appar för att hantera olika arbets scenarier. Olika RemoteApp-app-grupper kan också innehålla överlappande RemoteApp-program.

Om du vill publicera resurser till användare måste du tilldela dem till app-grupper. Tänk på följande när du tilldelar användare till app-grupper:

- En användare kan inte tilldelas både en Skriv bords grupp och en RemoteApp-app i samma adresspool.
- En användare kan tilldelas flera app-grupper inom samma adresspool och deras flöde är en ackumulering av båda app-grupperna.

## <a name="tenant-groups"></a>Klient grupper

I Windows Virtual Desktop är den virtuella Windows-klienten där det mesta av konfigurationen och konfigurationen sker. Windows-klienten för virtuella skriv bord innehåller värd grupper, app-grupper och användar tilldelningar för app Group. Det kan dock finnas vissa situationer där du behöver hantera flera virtuella Windows-klienter samtidigt, särskilt om du är en moln tjänst leverantör (CSP) eller en värd partner. I dessa fall kan du använda en anpassad Windows-grupp för virtuella skriv bord för att placera var och en av de kundernas Windows virtuella Skriv bords klienter och hantera åtkomsten centralt. Men om du bara hanterar en enda Windows-klient för virtuella datorer, gäller inte klient grupps konceptet och du kan fortsätta att använda och hantera din klient som finns i standard klient gruppen.

## <a name="end-users"></a>Slutanvändare

När du har tilldelat användare till sina app-grupper kan de ansluta till en Windows-distribution med virtuella skriv bord med någon av de virtuella Windows-klienterna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om delegerad åtkomst och hur du tilldelar roller till användare i [delegerad åtkomst i Windows Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Information om hur du konfigurerar en Windows Virtual Desktop-klient finns i [skapa en klient i Windows Virtual Desktop](tenant-setup-azure-active-directory.md).

Information om hur du ansluter till virtuella Windows-datorer finns i någon av följande artiklar:

- [Ansluta från Windows 10 eller Windows 7](connect-windows-7-10-2019.md)
- [Ansluta från en webbläsare](connect-web-2019.md)
