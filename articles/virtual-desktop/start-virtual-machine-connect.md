---
title: Starta Virtual Machine Connect – Azure
description: Konfigurera funktionen starta virtuell dator vid anslutning.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d9ce9811cd660c9a9b1fcb6f9e24cadd65d3c2bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445609"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Starta virtuell dator vid anslutning (förhands granskning)

> [!IMPORTANT]
> Funktionen starta virtuell dator vid anslutning är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med funktionen starta virtuell dator (VM) på Connect (för hands version) kan du spara kostnader genom att låta dig frigöra dina virtuella datorer när du inte använder dem. När du behöver använda den virtuella datorn igen måste du aktivera dina virtuella datorer igen.

>[!NOTE]
>Windows Virtual Desktop (klassisk) stöder inte den här funktionen.

## <a name="requirements-and-limitations"></a>Krav och begränsningar

Du kan bara aktivera funktionen starta virtuell dator i Connect för personliga värdar. Mer information om personliga värdbaserade pooler finns i [Windows Virtual Desktop-miljö](environment-setup.md#host-pools).

Följande fjärr skrivbords klienter stöder funktionen starta virtuell dator vid anslutning:

- [Webb klienten](connect-web.md)
- [Windows-klienten (version 1,2748 eller senare)](connect-windows-7-10.md)

Du kan söka efter meddelanden om uppdateringar och klient support i [Tech community-forumet](https://aka.ms/wvdtc).

>[!IMPORTANT]
>Funktionen starta virtuell dator vid anslutning stöder för närvarande endast PowerShell och REST API, inte Azure Portal. Mer information finns i [skapa eller uppdatera en adresspool](/rest/api/desktopvirtualization/hostpools/createorupdate).

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Skapa en anpassad roll för starta virtuell dator vid anslutning

Innan du kan konfigurera funktionen starta virtuell dator vid anslutning måste du tilldela en anpassad RBAC-roll (rollbaserad åtkomst kontroll) för den virtuella datorn. Med den här rollen kan virtuella datorer i Windows hantera de virtuella datorerna i din prenumeration. Du kan också använda den här rollen för att aktivera virtuella datorer, kontrol lera deras status och rapportera diagnostikinformation. Om du vill veta mer om vad varje roll gör kan du ta en titt på de [anpassade Azure-rollerna](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Använd Azure Portal för att tilldela en anpassad roll för starta virtuell dator vid anslutning:

1. Öppna Azure Portal och gå till **prenumerationer**.

2. Gå till **åtkomst kontroll (IAM)** och välj **Lägg till en anpassad roll**.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av en nedrullningsbar meny från knappen Lägg till i åtkomst kontroll (IAM). "Lägg till en anpassad roll" är markerat i rött.](media/add-custom-role.png)

3. Ange sedan den anpassade rollen som namn och Lägg till en beskrivning. Vi rekommenderar att du namnger "starta virtuell dator vid anslutning".

4. På fliken **behörigheter** lägger du till följande behörigheter för den prenumeration som du tilldelar rollen till: 
 
   - Microsoft. Compute/virtualMachines/start/Action
   - Microsoft. Compute/virtualMachines/Read

5. När du är klar väljer du **OK**.

Därefter måste du tilldela rollen till att bevilja åtkomst till det virtuella Windows-skrivbordet.

Så här tilldelar du den anpassade rollen:

1. På **fliken åtkomst kontroll (IAM)** väljer du **Lägg till roll tilldelningar**.

2. Välj den roll som du nyss skapade.

3. I Sök fältet anger och väljer du **virtuellt Windows-skrivbord**.

      >[!NOTE]
      >Du kan se två appar om du har distribuerat Windows Virtual Desktop (klassisk). Tilldela rollen till båda apparna du ser.
      >
      > [!div class="mx-imgBorder"]
      > ![En skärm bild av fliken åtkomst kontroll (IAM). I Sök fältet är både Windows Virtual Desktop och Windows Virtual Desktop (klassisk) markerade i rött.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Skapa en anpassad roll med en JSON-filmall

Om du använder en JSON-fil för att skapa den anpassade rollen visas en grundläggande mall som du kan använda i följande exempel. Se till att du ersätter värdet för prenumerations-ID med det prenumerations-ID som du vill tilldela rollen till.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>Konfigurera funktionen starta virtuell dator vid anslutning

Nu när du har tilldelat din prenumeration rollen, är det dags att konfigurera den virtuella datorn för att starta den virtuella datorn i Connect!

### <a name="deployment-considerations"></a>Distributionsöverväganden 

Starta virtuell dator vid anslutning är en inställning för anslutningspoolen. Om du bara vill att en Välj grupp användare ska använda den här funktionen, se till att du bara tilldelar den nödvändiga rollen till de användare som du vill lägga till.

>[!IMPORTANT]
> Du kan bara konfigurera den här funktionen i befintliga värdbaserade pooler. Den här funktionen är inte tillgänglig när du skapar en ny adresspool.

### <a name="use-powershell"></a>Använd PowerShell

Om du vill konfigurera den här inställningen med PowerShell måste du kontrol lera att du har namnen på de resurs grupper och lagringspooler som du vill konfigurera. Du måste också installera [Azure PowerShell-modulen (version 2.1.0 eller senare)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Konfigurera starta virtuell dator vid anslutning med PowerShell:

1. Öppna ett PowerShell-kommando fönster.

2. Kör följande cmdlet för att aktivera starta virtuell dator vid anslutning:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Kör följande cmdlet för att inaktivera starta virtuell dator vid anslutning:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Användarupplevelse

I vanliga sessioner ökar den tid det tar för en användare att ansluta till en fribelagd virtuell dator, eftersom den virtuella datorn behöver tid att aktivera igen, ungefär som att aktivera en fysisk dator. Fjärr skrivbords klienten har en indikator som låter användaren veta att datorn är påslagen medan de ansluter.

## <a name="troubleshooting"></a>Felsökning

Om funktionen körs i alla problem rekommenderar vi att du använder Windows Virtual Desktop Diagnostics- [funktionen](diagnostics-log-analytics.md) för att söka efter problem. Om du får ett fel meddelande, se till att betala nära meddelande innehållet och kopiera fel namnet någonstans för referensen.

Du kan också använda [Azure Monitor för virtuella Windows-datorer](azure-monitor.md) för att få förslag på hur du kan lösa problem.

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem som fel söknings dokumentationen eller diagnostikprogrammet inte kunde lösa, kan du titta på [vanliga frågor om att starta den virtuella datorn på Connect](start-virtual-machine-connect-faq.md).