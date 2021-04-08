---
title: Accelererad labb installations guide för Azure Lab Services
description: Om du är en labb skapare, kan den här guiden hjälpa dig att snabbt skapa ett labb konto i skolan.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021750"
---
# <a name="lab-setup-guide"></a>Installations guide för labb

I den här guiden får du lära dig hur du skapar ett labb för studenter i skolan.

Processen för att publicera ett labb i dina studenter kan ta upp till flera timmar. Hur lång tid det tar beror på hur många virtuella datorer (VM) som du vill skapa i labbet. Tillåt minst en dag för att se till att labbet fungerar som det ska och för att tillåta tillräckligt med tid för att publicera dina studenters virtuella datorer.

## <a name="understand-the-lab-requirements-of-your-class"></a>Förstå labb kraven för klassen

Innan du skapar ett nytt labb bör du tänka på följande frågor.

### <a name="what-software-requirements-does-the-class-have"></a>Vilka program varu krav har klassen?

Se klassens utbildnings mål när du bestämmer vilka operativ system, program och verktyg som du behöver installera på de virtuella datorerna i labbet. Om du vill konfigurera virtuella labb datorer har du tre alternativ:

- **Använd en Azure Marketplace-avbildning**: Azure Marketplace innehåller hundratals avbildningar som du kan använda när du skapar ett labb. För vissa klasser kan en av dessa avbildningar redan innehålla allt som du behöver för klassen.

- **Skapa en ny anpassad avbildning**: du kan skapa en egen anpassad avbildning genom att använda en Azure Marketplace-avbildning som en start punkt. Du kan sedan anpassa den genom att installera ytterligare program vara och göra konfigurations ändringar.

- **Använd en befintlig anpassad avbildning**: du kan återanvända anpassade avbildningar som du redan har skapat eller bilder som har skapats av andra administratörer eller lärare i skolan. För att kunna använda anpassade avbildningar måste dina administratörer konfigurera ett delat avbildnings Galleri.  Ett delat avbildnings galleri är en lagrings plats som används för att spara anpassade avbildningar.

> [!NOTE]
> Dina administratörer ansvarar för att aktivera Azure Marketplace-avbildningar och anpassade avbildningar så att du kan använda dem. Koordinera med IT-avdelningen och se till att de bilder du behöver är aktiverade. Anpassade avbildningar som du skapar aktive ras automatiskt för användning inom labb som du äger.

### <a name="what-hardware-requirements-does-the-class-have"></a>Vilka maskin varu krav har klassen?

Du kan välja mellan olika beräknings storlekar:

- **Kapslade virtualiseringslösningar**: låter dig ge studenter åtkomst till en virtuell dator som kan vara värd för flera, kapslade virtuella datorer. Du kan till exempel använda den här beräknings storleken för nätverk eller etiska hackning-klasser.

- **GPU-storlekar**: låter dina studenter använda dator intensiva typer av program. Det här alternativet används till exempel ofta med artificiell intelligens och Machine Learning.

Vägledning om hur du väljer lämplig VM-storlek finns i:
- [Storlek på virtuell dator](./administrator-guide.md#vm-sizing)
- [Flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> Eftersom tillgänglighet för beräknings storlek varierar beroende på region, kan färre storlekar vara tillgängliga för ditt labb. I allmänhet bör du välja den minsta beräknings storlek som passar dina behov. Med Azure Lab Services kan du konfigurera ett nytt labb med bättre beräknings kapacitet senare, om du behöver.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Vilka beroenden har klassen på externa Azure-eller nätverks resurser?
Dina virtuella labb datorer kan behöva åtkomst till externa resurser, till exempel en databas, en fil resurs eller en licensierings Server.  Om du vill att dina virtuella labb datorer ska använda externa resurser koordinerar du med IT-administratörerna.

> [!NOTE]
> Du bör fundera över om du kan minska ditt labb beroende av externa resurser genom att tillhandahålla nätverks resurser direkt på den virtuella datorn. För att till exempel eliminera behovet av att läsa data från en extern databas kan du installera databasen direkt på den virtuella datorn.  

### <a name="how-will-you-control-costs"></a>Hur ska du kontrol lera kostnaderna?
Labb tjänster använder en pris modell enligt principen betala per användning, vilket innebär att du bara betalar för den tid som en virtuell labb dator körs. Om du vill kontrol lera kostnaderna använder du ett eller flera av följande alternativ:

- **Schema**: Använd scheman för att automatiskt styra när dina virtuella labb datorer startas och stängs av.
- **Kvot**: Använd kvoter för att kontrol lera antalet timmar som eleverna har åtkomst till en virtuell dator utanför de schemalagda timmarna.  När en student använder en virtuell dator och når en kvot stängs den virtuella datorn automatiskt.  Studenten kan inte starta om den virtuella datorn om du inte ökar kvoten.
- **Automatisk avstängning**: när du aktiverar inställningen för automatisk avstängning stängs virtuella Windows-datorer automatiskt när en student har kopplats från en Remote Desktop Protocol-session (RDP). Som standard är denna inställning inaktiverad.

Mer information om att kontrol lera kostnader finns i:
- [Uppskatta kostnader](./cost-management-guide.md#estimate-the-lab-costs)
- [Hantera kostnader](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Hur kommer eleverna att spara sitt arbete?
Varje enskild student tilldelas en virtuell dator för labbets livs längd. Eleverna kan spara sitt arbete:

- Till den virtuella datorn.
- Till en extern plats, till exempel OneDrive eller GitHub. Det är möjligt att konfigurera OneDrive automatiskt för studenter på sina virtuella labb datorer.

> [!NOTE]
> För att säkerställa att dina studenter har fortsatt åtkomst till sitt sparade arbete utanför labbet och när klassen slutar, rekommenderar vi att de sparar sitt arbete på en extern lagrings plats.

### <a name="how-will-students-connect-to-their-vms"></a>Hur ansluter eleverna till sina virtuella datorer?
För RDP-anslutningar till virtuella Windows-datorer rekommenderar vi att eleverna använder [Microsoft fjärrskrivbord-klienten](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Fjärr skrivbords klienten stöder Mac-, Chromebook-och Windows-enheter.

Studenter kan använda antingen SSH (Secure Shell) eller RDP-protokollet för virtuella Linux-datorer. Om du vill att studenter ska ansluta via RDP måste du installera och konfigurera de nödvändiga RDP-och grafiskt användar gränssnitts paketen.

### <a name="will-students-also-use-microsoft-teams"></a>Kommer eleverna också använda Microsoft Teams?
Azure Lab Services integreras med Microsoft Teams så att lärares medlemmar kan skapa och hantera sina labb i team.  På samma sätt kan studenterna komma åt sina labb i team.

Mer information finns i [Azure Lab Services i Microsoft Teams](./lab-services-within-teams-overview.md).

## <a name="set-up-your-lab"></a>Skapa ett labb

När du har förstått kraven för klassens labb är du redo att konfigurera den. Följ länkarna i det här avsnittet om du vill veta mer. Det finns också instruktioner för att konfigurera labb i team.

1. **Skapa ett labb**. Se följande självstudiekurser:
    - [Skapa ett klassrumslabb](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [Skapa ett labb i Teams](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Om din klass kräver kapslad virtualisering, se [Aktivera kapslad virtualisering](./how-to-enable-nested-virtualization-template-vm.md).

1. **Anpassa avbildningar och publicera virtuella labb datorer**. Information om hur du ansluter till en särskild virtuell dator som heter mallen VM finns i:
    - [Skapa och hantera en mall för virtuella datorer](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Använda ett delat bildgalleri](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Om du använder Windows kan du också se [Konfigurera en virtuell Windows-mall](./how-to-prepare-windows-template.md). De här anvisningarna innehåller steg för att konfigurera OneDrive och Microsoft Office för dina studenter.

1. **Hantera VM-pool och kapacitet**. Du kan enkelt skala upp eller ned VM-kapaciteten efter behov av klassen. Tänk på att det kan ta flera timmar att öka VM-kapaciteten eftersom nya virtuella datorer konfigureras. Se följande artiklar:
    - [Konfigurera och hantera en VM-pool](./how-to-set-virtual-machine-passwords.md)
    - [Hantera en VM-pool i labb tjänster i Teams](./how-to-manage-vm-pool-within-teams.md)

1. **Lägg till och hantera labb användare**. Information om hur du lägger till användare i labbet finns i:
   - [Lägga till användare i labbet](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Skicka inbjudningar till användare](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [Hantera användar listor i labb tjänster i team](./how-to-manage-user-lists-within-teams.md)

    Information om vilka typer av konton som eleverna kan använda finns i [elev konton](./how-to-configure-student-usage.md#student-accounts).
  
1. **Ange kostnads kontroller**. Om du vill ange ett schema, upprätta kvoter och aktivera automatisk avstängning, se följande Självstudier:

   - [Ange ett schema](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > Det kan ta flera minuter att starta en virtuell dator beroende på vilket operativ system du har installerat. För att säkerställa att en virtuell labb dator är redo att användas under dina schemalagda timmar rekommenderar vi att du startar den 30 minuter i förväg.

   - [Ange kvoter för användare](./how-to-configure-student-usage.md#set-quotas-for-users) och [Ange ytterligare kvoter för vissa användare](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Aktivera automatisk avstängning vid frånkoppling](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Scheman och kvoter gäller inte för mallen VM, men inställningarna för automatisk avstängning gäller. 
        > 
        > När du skapar ett labb skapas mallen VM men startas inte. Du kan starta mallen VM, ansluta till den, installera all nödvändig program vara för labbet och sedan publicera den. När du publicerar mallen VM stängs den automatiskt ned åt dig om du inte har gjort det manuellt. 
        > 
        > Mall VM: ar *kostnad* när de körs, så se till att den virtuella datorns mall stängs av när du inte behöver den för att köra den.

    - [Skapa och hantera labb tjänst scheman i Teams](./how-to-create-schedules-within-teams.md) 

1. **Använd instrument panelen**. Instruktioner finns i [använda klass rummets labb instrument panel](./use-dashboard.md).

    > [!NOTE]
    > Den uppskattade kostnaden som visas på instrument panelen är den maximala kostnad som du kan förväntar dig för student labb användning. Till exempel debiteras du *inte* för oanvända kvot timmar av dina studenter. De uppskattade kostnaderna återspeglar *inte* några avgifter för att använda mallen VM, delade avbildnings galleriet eller när labb skaparen startar en användar dator.

## <a name="next-steps"></a>Nästa steg

Som en del av att hantera labben kan du läsa följande artiklar:
- [Spåra användning av klass rums labb](tutorial-track-usage.md)  
- [Åtkomst till ett klassrumslabb](tutorial-connect-virtual-machine-classroom-lab.md)
