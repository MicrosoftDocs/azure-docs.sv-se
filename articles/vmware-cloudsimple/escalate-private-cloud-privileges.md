---
title: Eskalera privata moln privilegier
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur du eskalerar privilegier i ditt privata moln för administrativa funktioner i vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895830"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Eskalera behörigheter för privat moln vCenter från CloudSimple-portalen

För administrativ åtkomst till ditt privata moln vCenter kan du tillfälligt eskalera dina CloudSimple-privilegier.  Med utökade privilegier kan du installera VMware-lösningar, lägga till identitets källor och hantera användare.

Nya användare kan skapas i vCenter SSO-domänen och tilldelas åtkomst till vCenter.  När du skapar nya användare lägger du till dem i CloudSimple-Builtin-grupperna för åtkomst till vCenter.  Mer information finns i [behörighets modellen för CloudSimple-privata moln i VMware vCenter](./learn-private-cloud-permissions.md).

> [!CAUTION]
> Gör inga konfigurations ändringar för hanterings komponenter. Åtgärder som vidtas under det eskalerade privilegierade läget kan påverka systemet negativt eller kan orsaka att systemet blir otillgängligt.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Eskalera privilegier

1. Få åtkomst till [CloudSimple-portalen](access-cloudsimple-portal.md).

2. Öppna sidan **resurser** , Välj det privata moln som du vill eskalera privilegier för.

3. Klicka på **eskalera** längst ned på sidan Sammanfattning under **ändra vSphere-privilegier**.

    ![Ändra vSphere-behörighet](media/escalate-private-cloud-privilege.png)

4. Välj användar typen vSphere.  Endast `CloudOwner@cloudsimple.local` lokal användare kan eskaleras.

5. Välj intervallet för eskalerad tid i list rutan. Välj den kortaste perioden som gör att du kan slutföra uppgiften.

6. Markera kryss rutan för att bekräfta att du förstår riskerna.

    ![Dialog rutan eskalera privilegier](media/escalate-private-cloud-privilege-dialog.png)

7. Klicka på **OK**.

8. Det kan ta några minuter att eskalera processen. När du är klar klickar du på **OK**.

Behörighets eskaleringen börjar och varar tills det valda intervallet har slutförts.  Du kan logga in på ditt privata moln vCenter för att utföra administrativa uppgifter.

> [!IMPORTANT]
> Endast en användare kan ha eskalerade privilegier.  Du måste ta bort användarens behörigheter innan du kan eskalera en annan användares privilegier.

> [!CAUTION]
> Nya användare får endast läggas till i *moln-ägar-grupp*, *Cloud-global-Cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* eller, *Cloud-global-VM-admin-Group*.  Användare som lagts till i gruppen *Administratörer* tas då bort automatiskt.  Endast tjänst konton måste läggas till i *Administratörs* gruppen och tjänst kontona får inte användas för att logga in på vSphere-webbgränssnittet.

## <a name="extend-privilege-escalation"></a>Utöka eskalering av privilegier

Om du behöver ytterligare tid för att slutföra dina uppgifter kan du utöka behörighets eskalerings perioden.  Välj det ytterligare eskalerade tidsintervall som gör att du kan slutföra de administrativa uppgifterna.

1. På **resursens**  >  **privata moln** i CloudSimple-portalen väljer du det privata moln som du vill utöka behörighets eskaleringen för.

2. Nästan längst ned på fliken Sammanfattning klickar du på **utöka eskalering av privilegier**.

    ![Utöka eskalering av privilegier](media/de-escalate-private-cloud-privilege.png)

3. Välj ett intervall för eskalering i list rutan. Granska den nya slut tiden för eskalering.

4. Klicka på **Spara** för att utöka intervallet.

## <a name="de-escalate-privileges"></a>Ta bort behörigheter för att eskalera

När dina administrativa uppgifter har slutförts bör du eskalera dina privilegier.  

1. På **resursens**  >  **privata moln** i CloudSimple-portalen väljer du det privata moln som du vill ta bort behörigheter för.

2. Klicka på **eskalera**.

3. Klicka på **OK**.

> [!IMPORTANT]
> Undvik eventuella fel genom att logga ut från vCenter och logga in igen efter att de eskalerats.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera vCenter-identitets källor som ska använda Active Directory](./set-vcenter-identity.md)
* Installera säkerhets kopierings lösning för [säkerhets kopiering av virtuella datorer i arbets belastning](./backup-workloads-veeam.md)
