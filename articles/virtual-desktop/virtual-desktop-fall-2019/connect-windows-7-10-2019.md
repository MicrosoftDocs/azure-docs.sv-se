---
title: Ansluta till Windows Virtual Desktop (klassisk) Windows 10 eller 7 – Azure
description: Så här ansluter du till Windows Virtual Desktop (klassisk) med hjälp av Windows Skriv bords klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 62686589b03f7187ec473dab8fba602eaf7176f2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445252"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Ansluta till Windows Desktop-klienten (klassisk)

> Gäller för: Windows 7, Windows 10 och Windows 10 IoT Enterprise

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../connect-windows-7-10.md).

Du kan komma åt Windows-resurser för virtuella skriv bord på enheter med Windows 7, Windows 10 och Windows 10 IoT Enterprise med Windows Skriv bords klienten. Klienten har inte stöd för Windows 8 eller Windows 8,1.

>[!NOTE]
>Windows-klienten får automatiskt standard Windows Virtual Desktop (klassisk). Men om klienten upptäcker att användaren också har Azure Resource Manager resurser lägger den automatiskt till resurserna eller meddelar användaren om att de är tillgängliga.

> [!IMPORTANT]
> Windows Virtual Desktop stöder inte RADC-klienten (RemoteApp-och Desktop Connections) eller Anslutning till fjärrskrivbord-klienten (MSTSC).

> [!IMPORTANT]
> Windows Virtual Desktop stöder för närvarande inte fjärr skrivbords klienten från Windows Store.

## <a name="install-the-windows-desktop-client"></a>Installera Windows Desktop-klienten

Välj den klient som matchar din version av Windows:

- [Windows 64-bitars](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bitars](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Du kan installera klienten för den aktuella användaren, som inte kräver administratörs rättigheter, eller så kan administratören installera och konfigurera klienten så att alla användare på enheten kan komma åt den.

När du har installerat kan klienten startas från Start-menyn genom att söka efter **fjärr skrivbord**.

## <a name="subscribe-to-a-workspace"></a>Prenumerera på en arbets yta

Du kan prenumerera på en arbets yta på två sätt. Klienten kan försöka identifiera de resurser som är tillgängliga från ditt arbets-eller skol konto eller så kan du ange webb adressen direkt där dina resurser finns i de fall där klienten inte kan hitta dem. När du prenumererar på en arbets yta kan du starta resurser med någon av följande metoder:

- Gå till anslutnings Center och dubbelklicka på en resurs för att starta den.
- Du kan också gå till Start-menyn och leta efter en mapp med namnet på arbets ytan eller ange resurs namnet i Sök fältet.

### <a name="subscribe-with-a-user-account"></a>Prenumerera med ett användar konto

1. Välj **Prenumerera** på klientens huvud sida.
2. Logga in med ditt användar konto när du uppmanas till det.
3. Resurserna kommer att visas i anslutnings Center och grupperas efter arbets yta.

### <a name="subscribe-with-a-url"></a>Prenumerera med en URL

1. Från klientens huvud sida väljer du **Prenumerera med URL**.
2. Ange arbets ytans URL eller din e-post adress:
   - Om du använder **URL: en för arbets ytan** använder du den som din administratör fick. Om du får åtkomst till resurser från virtuella Windows-datorer kan du använda någon av följande URL: er:
     - Windows Virtual Desktop (klassisk): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Virtuellt Windows-skrivbord: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Om du använder **e-** postfältet i stället anger du din e-postadress. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om din administratör har konfigurerat [identifiering av e-post](/windows-server/remote/remote-desktop-services/rds-email-discovery).
3. Välj **Nästa**.
4. Logga in med ditt användar konto när du uppmanas till det.
5. Resurserna bör visas i anslutnings centret, grupperade efter arbets yta.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Windows-skrivbordet kan du läsa [komma igång med Windows Skriv bords klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
