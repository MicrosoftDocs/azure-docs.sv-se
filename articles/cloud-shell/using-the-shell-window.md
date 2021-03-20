---
title: Använda fönstret Azure Cloud Shell | Microsoft Docs
description: Översikt över hur du använder fönstret Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70860318"
---
# <a name="using-the-azure-cloud-shell-window"></a>Använda fönstret Azure Cloud Shell

Det här dokumentet beskriver hur du använder fönstret Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Växla mellan bash-och PowerShell-miljöer

Använd miljö väljaren i Cloud Shells verktygsfältet för att växla mellan bash-och PowerShell-miljöer.  
![Välj miljö](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Starta om Cloud Shell
Klicka på ikonen Starta om i Cloud Shell-verktygsfältet för att återställa dator status.  
![Starta om Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Om du startar om Cloud Shell återställs dator statusen och alla filer som inte sparas av Azure-filresursen går förlorade.

## <a name="change-the-text-size"></a>Ändra text storleken
Klicka på inställnings ikonen längst upp till vänster i fönstret och hovra sedan över alternativet "text storlek" och välj önskad text storlek. Ditt val sparas i flera sessioner.
![Text storlek](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Ändra teckensnitt
Klicka på ikonen Inställningar längst upp till vänster i fönstret och hovra sedan över alternativet "font" och välj önskat teckensnitt.  Ditt val sparas i flera sessioner.
![Teckensnitt](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Ladda upp och ned filer
Klicka på ikonen Ladda upp/ladda ned filer längst upp till vänster i fönstret och välj sedan ladda upp eller ladda ned.  
![Ladda upp/ladda ned filer](media/using-the-shell-window/uploaddownload.png)
* För att ladda upp filer använder du popup-fönstret för att bläddra till filen på den lokala datorn, väljer önskad fil och klickar på knappen "öppna".  Filen kommer att överföras till `/home/user` katalogen.
* För att hämta filen anger du den fullständigt kvalificerade sökvägen till popup-fönstret (dvs. i princip en sökväg under `/home/user` katalogen som visas som standard) och väljer knappen "Ladda ned".  
> [!NOTE] 
> Filer och fil Sök vägar är Skift läges känsliga i Cloud Shell. Dubbelt kontrol lera ditt Skift läge i din fil Sök väg.

## <a name="open-another-cloud-shell-window"></a>Öppna ett annat Cloud Shell-fönster
Cloud Shell aktiverar flera samtidiga sessioner över flikar i webbläsaren genom att varje session kan finnas som en separat process.
Om du avslutar en session måste du avsluta varje session-fönster eftersom varje process körs separat, även om de körs på samma dator.  
Klicka på ikonen öppna ny session längst upp till vänster i fönstret. En ny flik öppnas med en annan session som är ansluten till den befintliga behållaren.
![Öppna ny session](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell redigerare
* Referera till sidan [med Azure Cloud Shell redigeraren](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Webb förhands granskning
Klicka på ikonen för förhands granskning längst upp till vänster i fönstret, välj "Konfigurera" och ange önskad port att öppna.  Välj antingen "öppna port" om du bara vill öppna porten eller "öppna och bläddra" för att öppna porten och för hands Visa porten på en ny flik.  
![Webb förhands granskning](media/using-the-shell-window/preview.png)  
<br>
![Konfigurera port](media/using-the-shell-window/preview-configure.png)  
Klicka på ikonen för förhands granskning längst upp till vänster i fönstret och välj "Förhandsgranska port..." för att förhandsgranska en öppen port på en ny flik. Klicka på ikonen för förhands granskning längst upp till vänster i fönstret och välj "Stäng port..." för att stänga den öppna porten.  
![För hands version/Stäng port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimera & maximera Cloud Shells fönstret
Klicka på ikonen minimera längst upp till höger i fönstret för att dölja den. Klicka på ikonen Cloud Shell igen för att visa.
Klicka på ikonen maximera för att ange fönster till maximal höjd. Klicka på Återställ för att återställa fönstret till föregående storlek.  
![Minimera eller maximera fönstret](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiera och klistra in
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ändra storlek på Cloud Shell fönstret
Klicka och dra den övre kanten på verktygsfältet uppåt eller nedåt för att ändra storlek på fönstret Cloud Shell.

## <a name="scrolling-text-display"></a>Visning av rullande text
Bläddra med musen eller pekplattan för att flytta terminalfönstret.

## <a name="exit-command"></a>Avsluta kommando
Kör `exit` avslutar den aktiva sessionen. Det här beteendet inträffar som standard efter 20 minuter utan interaktion.

## <a name="next-steps"></a>Nästa steg

[Bash i Cloud Shell snabb start](quickstart.md) <br>
[PowerShell i Cloud Shell snabb start](quickstart-powershell.md)
