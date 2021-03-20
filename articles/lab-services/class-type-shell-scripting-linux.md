---
title: Konfigurera ett Linux Shell Scripting Lab med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära sig Shell-skript i Linux.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ba14f2fb5263367014b57741c78d6e509df044b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444972"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Konfigurera ett labb för att lära sig använda Shell-skript i Linux
Den här artikeln visar hur du konfigurerar ett labb för att lära sig att använda Shell-skript i Linux. Skript är en användbar del av system administration som gör det möjligt för administratörer att undvika repetitiva uppgifter. I det här exempel scenariot täcker klassen traditionella bash-skript och förbättrade skript. Förbättrade skript är skript som kombinerar bash-kommandon och Ruby. Med den här metoden kan ruby skicka data runt-och bash-kommandon för att interagera med gränssnittet. 

Studenter som använder dessa skript klasser får en virtuell Linux-dator för att lära sig grunderna i Linux och även bekanta dig med bash shell-skript. Den virtuella Linux-datorn har åtkomst till fjärr skrivbord och med text redigerarna [gedit](https://help.gnome.org/users/gedit/stable/) och [Visual Studio Code](https://code.visualstudio.com/) installerade.

## <a name="lab-configuration"></a>Labb konfiguration
Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt labb konto. Se följande självstudie för att skapa ett nytt labb konto: [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).

När du har skapat labb kontot aktiverar du följande inställningar i labb kontot: 

| Inställning för labb konto | Instruktioner |
| ----------- | ------------ |  
| Marketplace-avbildningar | Aktivera Ubuntu Server 18,04 LTS-avbildningen för användning i ditt labb konto. Mer information finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapande](specify-marketplace-images.md). | 

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och tillämpa följande inställningar:

| Labb inställningar | Värde/anvisningar | 
| ------------ | ------------------ |
| Storlek för virtuell dator (VM) | Liten  |
| Avbildning av virtuell dator | Ubuntu Server 18.04 LTS|
| Aktivera anslutning till fjärr skrivbord | Aktivera. <p>Om du aktiverar den här inställningen kan lärare och studenter ansluta till sina virtuella datorer med hjälp av fjärr skrivbord (RDP). Mer information finns i [Aktivera fjärr skrivbord för virtuella Linux-datorer i ett labb i Azure Lab Services](how-to-enable-remote-desktop-linux.md). </p>|

## <a name="install-desktop-and-rdp"></a>Installera Desktop och RDP
Ubuntu Server 18,04 LTS-avbildningen har inte RDP-fjärrservern installerad som standard. Följ anvisningarna i artikeln [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](../virtual-machines/linux/use-remote-desktop.md) -artikeln för att installera de paket som behövs på mallen för att ansluta via RDP (Remote Desktop Protocol).

## <a name="install-ruby"></a>Installera Ruby
Ruby är ett dynamiskt språk med öppen källkod som kan kombineras med bash-skript. Det här avsnittet visar hur du använder `apt-get` för att installera den senaste versionen av [ruby](https://www.ruby-lang.org/).

1. Installera uppdateringar genom att köra följande kommandon:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Installera [ruby](https://www.ruby-lang.org/).  Ruby är ett dynamiskt språk med öppen källkod som kan kombineras med bash-skript. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Installera utvecklingsverktyg
I det här avsnittet visas hur du installerar ett par text redigerings program. Gedit är standard text redigeraren för gnome desktop-miljön. Den är utformad som en vanlig text redigerare. Visual Studio Code är en text redigerare som innehåller stöd för fel sökning och integrering av käll kontroll.

> [!NOTE]
> Det finns flera olika text redigerare. Visual Studio Code och gedit är bara två exempel.

1. Installera [gedit](https://help.gnome.org/users/gedit/stable/).

    ```bash
    sudo apt-get install gedit
    ```
1. Installera [Visual Studio Code](https://code.visualstudio.com/).  Visual Studio Code kan installeras med hjälp av Snap Store.  Alternativa installations alternativ finns i [Visual Studio Code alternativa hämtningar](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    Mallen har nu uppdaterats och har både programmeringsspråk och utvecklingsverktyg som krävs för att slutföra labbet. Nu kan du publicera mallen i labbet. Klicka på knappen **publicera** på mallsida för att publicera mallen i labbet.  

## <a name="cost"></a>Cost 
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel:
 
För en klass av 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara: 

25 studenter * (20 + 10) timmar * 20 labbs enheter * 0,01 USD per timme = 150 USD

Mer information om priser finns i följande dokument: [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Den här artikeln gick dig genom stegen för att skapa ett labb för skript klasser. Den här artikeln fokuserar på att konfigurera ruby-skript verktyg på Linux-datorn, men samma konfiguration kan användas för andra skript klasser som python på Linux.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users). 





