---
title: Vissa RDP-felmeddelanden för virtuella Azure-datorer | Microsoft Docs
description: Förstå vissa fel meddelanden som du kan få när du försöker använda fjärr skrivbords anslutning till en virtuell Windows-dator i Azure
keywords: Fel i fjärr skrivbord, anslutning till fjärr skrivbord, det går inte att ansluta till den virtuella datorn, fel sökning av fjärr skrivbord
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 808443cb805b2dee2426198f9cd4f7ba502d8999
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197514"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Felsöka specifika RDP-felmeddelanden till en virtuell Windows-dator i Azure
Du kan få ett särskilt fel meddelande när du använder fjärr skrivbords anslutning till en virtuell Windows-dator i Azure. I den här artikeln beskrivs några av de vanligaste fel meddelandena, tillsammans med fel söknings steg för att lösa dem. Om du har problem med att ansluta till din virtuella dator med hjälp av RDP men inte stöter på ett fel meddelande, se [fel söknings guiden för fjärr skrivbord](troubleshoot-rdp-connection.md).

Information om vissa fel meddelanden finns i följande avsnitt:

* [Fjärrsessionen kopplades från eftersom det inte finns några fjärr skrivbords licens servrar tillgängliga för att tillhandahålla en licens](#rdplicense).
* [Fjärr skrivbord kan inte hitta datorn "name"](#rdpname).
* [Ett autentiseringsfel har inträffat. Det går inte att kontakta den lokala säkerhets kontrollen](#rdpauth).
* [Windows-säkerhets fel: autentiseringsuppgifterna fungerade inte](#wincred).
* [Datorn kan inte ansluta till fjärrdatorn](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Fjärrsessionen kopplades från eftersom det inte finns några fjärr skrivbords licens servrar tillgängliga för att tillhandahålla en licens.
Orsak: Grace-perioden på 120 dagar för rollen fjärr skrivbords server har upphört att gälla och du måste installera licenser.

Som en lösning kan du spara en lokal kopia av RDP-filen från portalen och köra det här kommandot i en PowerShell-kommandotolk för att ansluta. Det här steget inaktiverar licensiering för just den anslutningen:

```powershell
mstsc <File name>.RDP /admin
```

Om du inte behöver mer än två samtidiga fjärr skrivbords anslutningar till den virtuella datorn kan du använda Serverhanteraren för att ta bort rollen fjärr skrivbords server.

Mer information finns i blogg inlägget [Azure VM Miss lyckas med "inga fjärr skrivbords licens servrar är tillgängliga"](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Fjärr skrivbord kan inte hitta datorn "name".
Orsak: fjärr skrivbords klienten på datorn kan inte matcha namnet på datorn i inställningarna för RDP-filen.

Möjliga lösningar:

* Om du befinner dig på en organisations intranät kontrollerar du att datorn har åtkomst till proxyservern och att den kan skicka HTTPS-trafik till den.
* Om du använder en lokalt lagrad RDP-fil kan du prova att använda den som genereras av portalen. Det här steget säkerställer att du har rätt DNS-namn för den virtuella datorn eller moln tjänsten och slut punkts porten för den virtuella datorn. Här är ett exempel på en RDP-fil som genereras av portalen:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

Adress delen av den här RDP-filen har:

* Det fullständigt kvalificerade domän namnet för den moln tjänst som innehåller den virtuella datorn ("tailspin-azdatatier.cloudapp.net" i det här exemplet).
* Slut punktens externa TCP-port för fjärr skrivbords trafik (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ett autentiseringsfel har inträffat. Det går inte att kontakta den lokala säkerhets kontrollen.
Orsak: den virtuella mål datorn kan inte hitta säkerhets myndigheten i användar namns delen av dina autentiseringsuppgifter.

När ditt användar namn har formatet *SecurityAuthority* \\ *användar* namn (exempel: CORP\User1.) är *SecurityAuthority* -delen antingen den virtuella datorns dator namn (för den lokala säkerhets kontrollen) eller ett Active Directory domän namn.

Möjliga lösningar:

* Om kontot är lokalt för den virtuella datorn kontrollerar du att namnet på den virtuella datorn är rättstavat.
* Om kontot finns på en Active Directory domän kontrollerar du stavningen för domän namnet.
* Om det är ett Active Directory domän konto och domän namnet är rättstavat, kontrollerar du att en domänkontrollant är tillgänglig i domänen. Det är ett vanligt problem i virtuella Azure-nätverk som innehåller domänkontrollanter som en domänkontrollant inte är tillgänglig på, eftersom den inte har startats. Som en lösning kan du använda ett lokalt administratörs konto i stället för ett domän konto.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-säkerhets fel: autentiseringsuppgifterna fungerade inte.
Orsak: den virtuella mål datorn kan inte verifiera ditt konto namn och lösen ord.

En Windows-baserad dator kan verifiera autentiseringsuppgifterna för antingen ett lokalt konto eller ett domän konto.

* För lokala konton använder du syntaxen *dator* namn \\ *användar namn* (exempel: SQL1\Admin4798).
* För domän konton använder du syntaxen *domän*\ \\ *användar namn* (exempel: CONTOSO\peterodman).

Om du har uppgraderat den virtuella datorn till en domänkontrollant i en ny Active Directory skog, konverteras det lokala administratörs kontot som du loggade in med till ett motsvarande konto med samma lösen ord i den nya skogen och domänen. Det lokala kontot tas sedan bort.

Om du till exempel har loggat in med det lokala kontot DC1\DCAdmin och sedan befordrade den virtuella datorn som en domänkontrollant i en ny skog för corp.contoso.com-domänen tas det lokala DC1\DCAdmin-kontot bort och ett nytt domän konto (CORP\DCAdmin) skapas med samma lösen ord.

Kontrol lera att konto namnet är ett namn som den virtuella datorn kan verifiera som ett giltigt konto och att lösen ordet är korrekt.

Om du behöver ändra lösen ordet för det lokala administratörs kontot läser du [så här återställer du ett lösen ord eller fjärr skrivbords tjänsten för virtuella Windows-datorer](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Datorn kan inte ansluta till fjärrdatorn.
Orsak: kontot som används för att ansluta saknar inloggnings rättigheter för fjärr skrivbord.

Varje Windows-dator har en lokal grupp med fjärr skrivbords användare som innehåller de konton och grupper som kan logga in på den via en fjärr anslutning. Medlemmar i den lokala gruppen Administratörer har även åtkomst, även om dessa konton inte visas i den lokala gruppen fjärr skrivbords användare. För domänanslutna datorer innehåller den lokala gruppen Administratörer även domän administratörer för domänen.

Kontrol lera att det konto som du använder för att ansluta med har inloggnings rättigheter för fjärr skrivbord. Som en lösning kan du använda en domän eller ett lokalt administratörs konto för att ansluta via fjärr skrivbord. Om du vill lägga till det önskade kontot i den lokala gruppen fjärr skrivbords användare använder du snapin-modulen Microsoft Management Console (**system verktyg > lokala användare och grupper > grupper > fjärr skrivbords användare**).

## <a name="next-steps"></a>Nästa steg
Om ingen av dessa fel uppstår och du har problem med att ansluta via RDP kan du läsa [fel söknings guiden för fjärr skrivbord](troubleshoot-rdp-connection.md).

* Fel söknings steg för att komma åt program som körs på en virtuell dator finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Om du har problem med att använda SSH (Secure Shell) för att ansluta till en virtuell Linux-dator i Azure läser du [FELSÖKA SSH-anslutningar till en virtuell Linux-dator i Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
