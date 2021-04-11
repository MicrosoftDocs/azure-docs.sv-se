---
title: Ansluta till Windows Virtual Desktop Windows 10 eller 7 – Azure
description: Hur du ansluter till virtuella Windows-datorer med hjälp av Windows Skriv bords klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447887"
---
# <a name="connect-with-the-windows-desktop-client"></a>Ansluta med Windows-skrivbordsklienten

Du kan komma åt Windows virtuella Skriv bords resurser på enheter med Windows 10, Windows 10 IoT Enterprise och Windows 7 med Windows-skrivbordet. 

> [!IMPORTANT]
> Detta stöder inte Window 8 eller Windows 8,1.
> 
> Detta stöder endast Azure Resource Manager objekt, för att stödja objekt utan Azure Resource Manager, se [ansluta med Windows Desktop (klassisk)-klient](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Detta stöder inte klienten för RemoteApp-och fjärr skrivbords anslutningar (RADC) eller Anslutning till fjärrskrivbord-klienten (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Installera Windows Desktop-klienten

Ladda ned klienten baserat på din Windows-version:

- [Windows 64-bitars](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bitars](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Under installationen för att fastställa åtkomst väljer du antingen:

- **Installera bara för dig**
- **Installera för alla användare av den här datorn** (kräver administratörs behörighet)

Starta klienten efter installationen genom att använda **Start** -menyn och söka efter **fjärr skrivbord**.

## <a name="subscribe-to-a-workspace"></a>Prenumerera på en arbets yta

Om du vill prenumerera på en arbets yta väljer du antingen:

- Använd ett arbets-eller skol konto och låt klienten identifiera de resurser som är tillgängliga för dig
- Använd den speciella URL: en för resursen

Om du vill starta resursen när du prenumererar går du till **anslutnings Center** och dubbelklickar på resursen.

> [!TIP]
> Om du vill starta en resurs från **Start** -menyn kan du hitta mappen med namnet på arbets ytan eller ange resurs namnet i Sök fältet.

### <a name="use-a-user-account"></a>Använda ett användar konto

1. Välj **Prenumerera** på huvud sidan.
1. Logga in med ditt användar konto när du uppmanas till det.

Resurserna som är grupperade efter arbets yta visas i **anslutnings centret**.

   > [!NOTE]
   > Windows-klienten får automatiskt standard Windows Virtual Desktop (klassisk). 
   > 
   > Men om klienten upptäcker ytterligare Azure Resource Manager resurser lägger den till dem automatiskt eller meddelar användaren att de är tillgängliga.

### <a name="use-a-specific-url"></a>Använd en viss URL

1. Välj **Prenumerera med URL** från huvud sidan.
1. Ange antingen *arbets ytans URL* eller en *e-post adress*:
   - För **arbetsyte-URL** använder du den URL som tillhandahålls av din administratör.

   |Tillgängliga resurser|URL|
   |-|-|
   |Windows Virtual Desktop (klassisk)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Windows Virtual Desktop (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - Använd din e-postadress för **e-post**. 
      
   Klienten hittar den URL som är kopplad till din e-post, förutsatt att din administratör har aktiverat [e-postidentifiering](/windows-server/remote/remote-desktop-services/rds-email-discovery).

1. Välj **Nästa**.
1. Logga in med ditt användar konto när du uppmanas till det.

Resurserna som är grupperade efter arbets yta visas i **anslutnings centret**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder-klienten kan du läsa [komma igång med Windows Skriv bords klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Om du är en administratör som vill lära sig mer om klientens funktioner kan du gå [till Windows Skriv bords klient för administratörer](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).
