---
title: Ansluta till en virtuell Windows Server-dator
description: Lär dig hur du ansluter och loggar in på en virtuell Windows-dator med hjälp av Azure Portal och distributions modellen för Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5d14160a47789e10f1881fa0e55afd4af122c990
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550764"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Först ansluter du till den virtuella datorn och sedan loggar du in.

Om du vill ansluta till en virtuell Windows-dator från en Mac måste du installera en RDP-klient för Mac, till exempel [Microsoft fjärrskrivbord](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Gå till [Azure Portal](https://portal.azure.com/) för att ansluta till en virtuell dator. Sök efter och välj **virtuella datorer**.
2. Välj den virtuella datorn i listan.
3. I början av sidan virtuell dator väljer du **Anslut**.
4. På sidan **Anslut till virtuell dator** väljer du **RDP** och väljer sedan lämplig **IP-adress** och **port nummer**. I de flesta fall ska standard-IP-adressen och porten användas. Välj **Hämta RDP-fil**. Om den virtuella datorn har en princip uppsättning för just-in-Time måste du först välja knappen **begär åtkomst** för att begära åtkomst innan du kan ladda ned RDP-filen. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).
5. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas. Du får en varning om att `.rdp` filen kommer från en okänd utgivare. Detta är förväntat. I fönstret **anslutning till fjärrskrivbord** väljer du **Anslut** för att fortsätta.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och välj sedan **OK**.
   
     **Lokalt konto**: det här är vanligt vis det lokala kontots användar namn och lösen ord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    Domänansluten **virtuell dator**: om den virtuella datorn tillhör en domän anger du användar namnet i formatet *domän*&#92;*användar namn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontrollant: om** den virtuella datorn är en domänkontrollant anger du användar namn och lösen ord för ett domän administratörs konto för domänen.
4. Välj **Ja** för att verifiera identiteten för den virtuella datorn och slutför inloggningen.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Om knappen **Anslut** på portalen är nedtonad och du inte är ansluten till Azure via en [Express Route](../../expressroute/expressroute-introduction.md) eller en VPN-anslutning från [plats till plats](../../vpn-gateway/tutorial-site-to-site-portal.md) måste du skapa och tilldela en offentlig IP-adress för din virtuella dator innan du kan använda RDP. Mer information finns i [offentliga IP-adresser i Azure](../../virtual-network/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ansluta till den virtuella datorn med PowerShell

 

Om du använder PowerShell och har installerat Azure PowerShell-modulen kan du också ansluta med hjälp av `Get-AzRemoteDesktopFile` cmdleten, som du ser nedan.

I det här exemplet startas RDP-anslutningen direkt, med hjälp av liknande prompter som ovan.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Du kan också spara RDP-filen för framtida användning.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta kan du läsa [Felsöka anslutningar till fjärr skrivbord](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json).