---
title: Starta anslutning av virtuell dator – Azure
description: Så här konfigurerar du funktionen Starta virtuell dator vid anslutning.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: af95cf5d3e4112c717d653062f186797d48fb515
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389816"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Starta virtuell dator vid anslutning (förhandsversion)

> [!IMPORTANT]
> Funktionen Starta virtuell dator på Anslut är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med funktionen Starta virtuell dator (VM) på Anslut (förhandsversion) kan du spara kostnader genom att du kan friplacera dina virtuella datorer när du inte använder dem. När du behöver använda den virtuella datorn igen behöver du bara aktivera dina virtuella datorer igen.

>[!NOTE]
>Windows Virtual Desktop (klassisk) stöder inte den här funktionen.

## <a name="requirements-and-limitations"></a>Krav och begränsningar

Du kan bara aktivera funktionen Starta virtuell dator på Anslut för personliga värdpooler. Mer information om personliga värdpooler finns i [Windows Virtual Desktop miljö](environment-setup.md#host-pools).

Följande fjärrskrivbordsklienter stöder funktionen Starta virtuell dator på Anslut:

- [Webbklienten](connect-web.md)
- [Windows-klienten (version 1.2748 eller senare)](connect-windows-7-10.md)

Du kan söka efter meddelanden om uppdateringar och klientstöd på [Tech Community-forumet.](https://aka.ms/wvdtc)

Det Azure Government molnet stöder för närvarande inte Starta virtuell dator på Anslut.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Skapa en anpassad roll för Starta virtuell dator på Anslut

Innan du kan konfigurera funktionen Starta virtuell dator på Anslut måste du tilldela den virtuella datorn en anpassad RBAC-roll (rollbaserad åtkomstkontroll). Med den här rollen kan Windows Virtual Desktop hantera de virtuella datorerna i din prenumeration. Du kan också använda den här rollen för att aktivera virtuella datorer, kontrollera deras status och rapportera diagnostikinformation. Om du vill veta mer om vad varje roll gör kan du ta en titt på anpassade [Azure-roller.](../role-based-access-control/custom-roles.md)

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här använder du Azure Portal tilldela en anpassad roll för Starta virtuell dator på Anslut:

1. Öppna Azure Portal och gå till **Prenumerationer**.

2. Gå till **Åtkomstkontroll (IAM) och** välj Lägg **till en anpassad roll.**

    > [!div class="mx-imgBorder"]
    > ![En skärmbild av en nedrullningslistad meny från knappen Lägg till i Åtkomstkontroll (IAM). "Lägg till en anpassad roll" är markerat i rött.](media/add-custom-role.png)

3. Ge sedan den anpassade rollen ett namn och lägg till en beskrivning. Vi rekommenderar att du ger den namnet "starta virtuell dator vid anslutning".

4. På fliken **Behörigheter** lägger du till följande behörigheter till den prenumeration som du tilldelar rollen till: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. När du är klar väljer du **OK.**

Därefter måste du tilldela rollen för att bevilja åtkomst till Windows Virtual Desktop.

Så här tilldelar du den anpassade rollen:

1. På fliken **Åtkomstkontroll (IAM) väljer** du Lägg **till rolltilldelningar.**

2. Välj den roll som du nyss skapade.

3. I sökfältet anger och väljer du **Windows Virtual Desktop**.

      >[!NOTE]
      >Du kan se två appar om du har distribuerat Windows Virtual Desktop (klassisk). Tilldela rollen till båda apparna som visas.
      >
      > [!div class="mx-imgBorder"]
      > ![En skärmbild av fliken Åtkomstkontroll (IAM). I sökfältet är både Windows Virtual Desktop och Windows Virtual Desktop (klassisk) markerade i rött.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Skapa en anpassad roll med en JSON-filmall

Om du använder en JSON-fil för att skapa den anpassade rollen visar följande exempel en grundläggande mall som du kan använda. Ersätt prenumerations-ID-värdet med det prenumerations-ID som du vill tilldela rollen till.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Konfigurera funktionen Starta virtuell dator på Anslut

Nu när du har tilldelat din prenumeration rollen är det dags att konfigurera funktionen Starta virtuell dator på Anslut!

### <a name="deployment-considerations"></a>Distributionsöverväganden 

Starta virtuell dator på Anslut är en värdpoolinställning. Om du bara vill att en grupp användare ska använda den här funktionen måste du bara tilldela den roll som krävs till de användare som du vill lägga till.

>[!IMPORTANT]
> Du kan bara konfigurera den här funktionen i befintliga värdpooler. Den här funktionen är inte tillgänglig när du skapar en ny värdpool.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här använder du Azure Portal för att konfigurera Starta virtuell dator på Anslut:

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com).

2. I Azure Portal du till **Windows Virtual Desktop**.

3. Välj **Värdpooler** och leta sedan reda på den värdpool som innehåller de personliga skrivbord som du tilldelade rollen till.

   >[!NOTE]
   > Värdpoolen som du konfigurerar den här funktionen i måste ha personliga skrivbord med direkt rolltilldelningar. Om skrivborden i värdpoolen inte är korrekt konfigurerade fungerar inte konfigurationsprocessen.

4. I värdpoolen väljer du **Egenskaper**. Under **Starta virtuell dator på** anslut väljer du **Ja** och sedan **Spara** för att tillämpa inställningen omedelbart.

    > [!div class="mx-imgBorder"]
    > ![En skärmbild av Fönstret Egenskaper. Alternativet Starta virtuell dator vid anslutning är markerat i rött.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Använd PowerShell

Om du vill konfigurera den här inställningen med PowerShell måste du kontrollera att du har namnen på den resursgrupp och de värdpooler som du vill konfigurera. Du måste också installera modulen [Azure PowerShell (version 2.1.0 eller senare).](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)

Så här konfigurerar du Starta virtuell dator på Anslut med PowerShell:

1. Öppna ett PowerShell-kommandofönster.

2. Kör följande cmdlet för att aktivera Starta virtuell dator på Anslut:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Kör följande cmdlet för att inaktivera Starta virtuell dator på Anslut:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Användarupplevelse

I typiska sessioner ökar den tid det tar för en användare att ansluta till en frisallokerad virtuell dator eftersom den virtuella datorn behöver tid att slå på igen, ungefär som att aktivera en fysisk dator. Fjärrskrivbordsklienten har en indikator som talar om för användaren att datorn är påslagen medan den ansluter.

## <a name="troubleshooting"></a>Felsökning

Om funktionen får problem rekommenderar vi att du använder funktionen Windows Virtual Desktop [diagnostik för](diagnostics-log-analytics.md) att söka efter problem. Om du får ett felmeddelande ska du vara noga med meddelandeinnehållet och kopiera felnamnet någonstans som referens.

Du kan också använda [Azure Monitor för Windows Virtual Desktop](azure-monitor.md) få förslag på hur du kan lösa problem.

## <a name="next-steps"></a>Nästa steg

Om du får problem som felsökningsdokumentationen eller diagnostikfunktionen inte kunde lösa kan du läsa Starta virtuell dator på [Vanliga frågor och svar om Connect.](start-virtual-machine-connect-faq.md)