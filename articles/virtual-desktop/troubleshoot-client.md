---
title: Felsöka fjärr skrivbords klienten Windows Virtual Desktop – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows-klient för virtuella skriv bord.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 097c97d16cf62793d03ac42662267e0553383bc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98539627"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Felsöka fjärr skrivbords klienten

I den här artikeln beskrivs vanliga problem med fjärr skrivbords klienten och hur du kan åtgärda dem.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Fjärr skrivbords klienten för Windows 7 eller Windows 10 slutar svara eller kan inte öppnas

Från och med version 1.2.790 kan du återställa användar data från om-sidan eller använda ett kommando.

Använd följande kommando för att ta bort användar data, återställa standardinställningar och avbryta prenumerationen på alla arbets ytor.

```cmd
msrdcw.exe /reset [/f]
```

Om du använder en tidigare version av fjärr skrivbords klienten rekommenderar vi att du avinstallerar och installerar om klienten.

## <a name="web-client-wont-open"></a>Webb klienten öppnas inte

Testa först din Internet anslutning genom att öppna en annan webbplats i webbläsaren. till exempel [www.Bing.com](https://www.bing.com).

Använd **nslookup** för att bekräfta att DNS kan matcha FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Försök att ansluta till en annan klient, t. ex. fjärr skrivbords klienten för Windows 7 eller Windows 10, och kontrol lera om du kan öppna webb klienten.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Det går inte att öppna andra webbplatser när du är ansluten till webb klienten

Om du inte kan öppna andra webbplatser medan du är ansluten till webb klienten, kan det uppstå problem med nätverks anslutningen eller ett nätverks avbrott. Vi rekommenderar att du kontaktar nätverks supporten.

### <a name="nslookup-cant-resolve-the-name"></a>Det går inte att matcha namnet i nslookup

Om det inte går att matcha namnet i nslookup kan det bero på problem med nätverks anslutningen eller ett nätverks avbrott. Vi rekommenderar att du kontaktar nätverks supporten.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klienten kan inte ansluta men andra klienter i nätverket kan ansluta

Om webbläsaren startar eller slutar fungera när du använder webb klienten följer du dessa anvisningar för att felsöka den:

1. Starta om webbläsaren.
2. Rensa webbläsarens cookies. Se [hur du tar bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Rensa webbläsarens cache. Se [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
4. Öppna webbläsare i privat läge.

## <a name="client-doesnt-show-my-resources"></a>Klienten visar inte mina resurser

Börja med att kontrol lera Azure Active Directory kontot som du använder. Om du redan har loggat in med ett annat Azure Active Directory-konto än det som du vill använda för Windows Virtual Desktop, bör du antingen logga ut eller använda ett privat webbläsarfönster.

Om du använder Windows Virtual Desktop (klassisk) använder du länken webb klient i [den här artikeln](./virtual-desktop-fall-2019/connect-web-2019.md) för att ansluta till dina resurser.

Om det inte fungerar kontrollerar du att din app-grupp är kopplad till en arbets yta.

## <a name="web-client-stops-responding-or-disconnects"></a>Webb klienten slutar svara eller kopplas från

Försök att ansluta med en annan webbläsare eller klient.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andra webbläsare och klienter fungerar inte heller eller kan inte öppnas

Om problemen fortsätter även efter att du har bytt webbläsare, kanske problemet inte är med din webbläsare, men med nätverket. Vi rekommenderar att du kontaktar nätverks supporten.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webb klienten kommer att uppmanas att ange autentiseringsuppgifter

Följ dessa instruktioner om webb klienten ska uppmanas att ange autentiseringsuppgifter:

1. Bekräfta att webb adressen till webb klienten är korrekt.
2. Bekräfta att autentiseringsuppgifterna som du använder är för den virtuella Windows-miljön som är kopplad till URL: en.
3. Rensa webbläsarens cookies. Mer information finns i [så här tar du bort cookie-filer i Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Rensa webbläsarens cache. Mer information finns i [Rensa webbläsarens cacheminne för webbläsaren](https://binged.it/2RKyfdU).
5. Öppna webbläsaren i privat läge.

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows-klienten blockerar Windows Virtual Desktop-feed (klassisk)

Om Windows-klientcachen inte visar Windows Virtual Desktop (klassiska) appar, följer du dessa anvisningar:

1. Kontrol lera om principen för villkorlig åtkomst innehåller de app-ID: n som är associerade med Windows Virtual Desktop (klassisk).
2. Kontrol lera om principen för villkorlig åtkomst blockerar all åtkomst förutom program-ID: n för Windows Virtual Desktop (klassisk). I så fall måste du lägga till appens ID- **9cdead84-a844-4324-93f2-b2e6bb768d07** till principen så att klienten kan identifiera feeds.

Om du inte hittar app-ID-9cdead84-a844-4324-93f2-b2e6bb768d07 i listan, måste du registrera Windows Resource Provider för virtuella skriv bord. Så här registrerar du resurs leverantören:

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
