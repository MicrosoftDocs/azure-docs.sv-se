---
title: Guide för att styra beteendet för Windows-avstängning i Azure Lab Services | Microsoft Docs
description: Steg för att automatiskt stänga av en virtuell Windows-dator som är inaktiv och ta bort Windows shutdown-kommandot.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647706"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Guide för att styra avstängnings beteendet i Windows

Azure Lab Services tillhandahåller flera kostnads kontroller för att säkerställa att virtuella Windows-datorer (VM) inte körs utan förvarning:
 - [Ange ett schema](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Ange kvoter för användare](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [Aktivera automatisk avstängning vid frånkoppling](./how-to-enable-shutdown-disconnect.md)

Även med dessa kostnads kontroller finns det situationer där en virtuell Windows-dator kan fortsätta att köras. Det innebär att dra av student kvoten:

- **RDP-fönstret lämnas öppet**
  
    När en student ansluter till den virtuella datorn med RDP kan de oavsiktligt lämna RDP-fönstret öppet.  Så länge RDP-fönstret förblir öppet börjar inte inställningen **Automatisk avstängning vid från koppling** att gälla eftersom den endast utlöses efter att RDP-sessionen har kopplats från.

- **Windows shutdown-kommandot används för att stänga av den virtuella datorn**
  
    En student kan använda Windows avstängnings kommando eller andra avstängnings metoder som finns i Windows för att stänga av den virtuella datorn i stället för att använda [Azure Lab Services stopp-knappen](./how-to-use-classroom-lab.md#start-or-stop-the-vm).  När detta inträffar kommer den virtuella datorn fortfarande att användas från Azure Lab Services perspektiv.
    
För att hjälpa dig att förhindra dessa situationer, innehåller den här guiden steg för att automatiskt stänga av en inaktiv virtuell Windows-dator och ta bort Windows shutdown-kommandot från **Start** menyn.  

> [!NOTE]
> En virtuell dator kan också dras av från kvoten när studenten startar sin virtuella dator, men ansluter aldrig till den via RDP.  Den här guiden löser för närvarande *inte* det här scenariot.  I stället bör eleverna bli påmind om att omedelbart ansluta till den virtuella datorn med RDP när de har startat den. eller så bör de stoppa den virtuella datorn.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Ta bort Windows avstängnings kommando från Start menyn

Med inställningarna för lokala Windows- **Grupprincip** kan du också ta bort shutdown-kommandot från **Start** menyn.

Om du vill ta bort kommandot shutdown kan du ansluta till mallen VM och köra PowerShell-skriptet nedan.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Eller så kan du välja att följa dessa manuella steg med mallen VM:

1. Tryck på Windows-tangenten, Skriv **gpedit** och välj sedan **Redigera grup princip (kontroll panelen)**.

1. Gå till **dator konfiguration > Administrativa mallar > Start-menyn och aktivitets fältet**.  

    ![Redigeraren för lokala grupprinciper](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Högerklicka på **ta bort och förhindra åtkomst till kommandona Stäng av, starta om, ström spar läge och vilo läge** och klicka på **Redigera**.

1. Välj den **aktiverade** inställningen och klicka sedan på **OK**:
 
   ![Avslutnings inställning](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Observera att kommandot shutdown inte längre visas under **Start** menyn i Windows; endast kommandot **från koppling** visas.

    ![Shutdown-kommando](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Nästa steg
Se artikeln om hur du förbereder en virtuell Windows-mall: [Guide för att konfigurera en Windows-mall i Azure Lab Services](how-to-prepare-windows-template.md)