---
title: Ta bort Windows virtuell Skriv bords värd pool – Azure
description: Så här tar du bort en adresspool i det virtuella Windows-skrivbordet.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007598"
---
# <a name="delete-a-host-pool"></a>Ta bort en värdpool

Alla lagringspooler som har skapats i Windows Virtual Desktop är kopplade till sessionsbaserade värdar och app-grupper. Om du vill ta bort en adresspool måste du ta bort dess tillhör ande app-grupper och-värd. Det är ganska enkelt att ta bort en app-grupp, men det är mer komplicerat att ta bort en sessions värd. När du tar bort en sessions värd måste du kontrol lera att den inte har några aktiva användarsessioner. Alla användarsessioner på sessionen måste loggas ut för att hindra användare från att förlora data.

## <a name="delete-a-host-pool-with-powershell"></a>Ta bort en värdbaserad pool med PowerShell

Om du vill ta bort en adresspool med hjälp av PowerShell måste du först ta bort alla app-grupper i poolen. Om du vill ta bort alla app-grupper kör du följande PowerShell-cmdlet:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Kör sedan denna cmdlet för att ta bort värddatorn:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Denna cmdlet tar bort alla befintliga användarsessioner på värddatorn för värddatorn. Den avregistrerar också värd för sessionen från poolen. Alla relaterade virtuella datorer (VM) kommer fortfarande att finnas i din prenumeration.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Ta bort en adresspool med Azure Portal

Så här tar du bort en adresspool i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Sök efter och välj **virtuellt skriv bord i Windows**.

3. Välj **pooler för värdar** på menyn till vänster på sidan och välj sedan namnet på den värddator som du vill ta bort.

4. På menyn på vänster sida av sidan väljer du **program grupper**.

5. Välj alla program grupper i den modempool som du vill ta bort och välj sedan **ta bort**.

6. När du har tagit bort app-grupperna går du till menyn till vänster på sidan och väljer **Översikt**.

7. Välj **Ta bort**.

8. Om det finns värddatorer i den modempool som du tar bort visas ett meddelande som ber om din tillåtelse att fortsätta. Välj **Ja**.

9. Den Azure Portal kommer nu att ta bort alla sessionsbaserade värdar och ta bort den. De virtuella datorerna som är relaterade till sessionen tas inte bort och kommer att finnas kvar i din prenumeration.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en adresspool finns i följande artiklar:

- [Skapa en värdpool med Azure-portalen](create-host-pools-azure-marketplace.md)
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)

Information om hur du konfigurerar inställningar för anslutningspoolen finns i följande artiklar:

- [Anpassa Remote Desktop Protocol egenskaper för en värd pool](customize-rdp-properties.md)
- [Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurera tilldelnings typen egen Skriv bords värd pool](configure-host-pool-personal-desktop-assignment-type.md)