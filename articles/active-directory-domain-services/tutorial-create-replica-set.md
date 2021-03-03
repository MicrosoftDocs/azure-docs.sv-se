---
title: Självstudie – Skapa en replik uppsättning i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du skapar och använder replik uppsättningar i Azure Portal för tjänste återhämtning med Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: e8887cc39b48f090ff223e5e83c13d65b921dc0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687506"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services"></a>Självstudie: skapa och Använd replik uppsättningar för återhämtning eller geolokalisering i Azure Active Directory Domain Services

Om du vill förbättra återhämtningen hos en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän, eller distribuera till ytterligare geografiska platser nära dina program, kan du använda *replik uppsättningar*. Varje Azure AD DS-hanterad domän namn rymd, till exempel *aaddscontoso.com*, innehåller en inledande replik uppsättning. Möjligheten att skapa ytterligare replik uppsättningar i andra Azure-regioner ger geografisk återhämtning för en hanterad domän.

Du kan lägga till en replik uppsättning till ett peer-kopplat virtuellt nätverk i valfri Azure-region som stöder Azure AD DS.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå kraven för virtuella nätverk
> * Skapa en replik uppsättning
> * Ta bort en replik uppsättning

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän som skapats med Azure Resource Manager distributions modell och som kon figurer ATS i din Azure AD-klient.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][tutorial-create-instance].

    > [!IMPORTANT]
    > Hanterade domäner som skapats med den klassiska distributions modellen kan inte använda replik uppsättningar. Du måste också använda minst *företags* -SKU: er för din hanterade domän. Om det behövs [ändrar du SKU: n för en hanterad domän][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar du och hanterar replik uppsättningar med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="networking-considerations"></a>Nätverksöverväganden

De virtuella nätverk som är värdar för replik uppsättningar måste kunna kommunicera med varandra. Program och tjänster som är beroende av Azure AD DS behöver också nätverks anslutning till de virtuella nätverk som är värdar för replik uppsättningarna. Peering av virtuella Azure-nätverk ska konfigureras mellan alla virtuella nätverk för att skapa ett helt nätverk med ett helt nätverk. Dessa peer-kopplingar möjliggör effektiv replikering inom platser mellan replik uppsättningar.

Innan du kan använda replik uppsättningar i Azure AD DS granskar du följande Azure Virtual Network-krav:

* Undvik överlappande IP-adressutrymme för att tillåta virtuell nätverks-peering och routning.
* Skapa undernät med tillräckligt med IP-adresser för att ge stöd för ditt scenario.
* Kontrol lera att Azure AD DS har sitt eget undernät. Dela inte det här virtuella nätverkets undernät med program virtuella datorer och tjänster.
* Peer-kopplat virtuella nätverk är inte transitiva.

> [!TIP]
> När du skapar en replik uppsättning i Azure Portal skapas nätverks peering mellan virtuella nätverk åt dig.
>
> Om det behövs kan du skapa ett virtuellt nätverk och ett undernät när du lägger till en replik uppsättning i Azure Portal. Du kan också välja befintliga virtuella nätverks resurser i mål regionen för en replik uppsättning och låta peer-kopplingarna skapas automatiskt om de inte redan finns.

## <a name="create-a-replica-set"></a>Skapa en replik uppsättning

När du skapar en hanterad domän, till exempel *aaddscontoso.com*, skapas en inledande replik uppsättning. Ytterligare replik uppsättningar delar samma namnrymd och konfiguration. Ändringar i Azure AD DS, inklusive konfiguration, användar identitet och autentiseringsuppgifter, grupper, grup princip objekt, dator objekt och andra ändringar tillämpas på alla replik uppsättningar i den hanterade domänen med AD DS-replikering.

I den här självstudien skapar du ytterligare en replik uppsättning i en annan Azure-region än den ursprungliga Azure AD DS-replik uppsättningen.

Utför följande steg för att skapa ytterligare en replik uppsättning:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På den vänstra sidan väljer du **replik uppsättningar**. Varje hanterad domän innehåller en inledande replik uppsättning i den valda regionen, som visas i följande exempel skärm bild:

    ![Exempel skärm bild som visar och lägger till en replik uppsättning i Azure Portal](./media/tutorial-create-replica-set/replica-set-list.png)

    Om du vill skapa ytterligare en replik uppsättning väljer du **+ Lägg till**.

1. I fönstret *Lägg till en replik uppsättning* väljer du mål region, t. ex. *USA, östra*.

    Välj ett virtuellt nätverk i mål regionen, till exempel *VNet-öster*, och välj sedan ett undernät som *aadds-Subnet*. Om det behövs väljer du **Skapa ny** för att lägga till ett virtuellt nätverk i mål regionen. sedan **hanterar** du för att skapa ett UNDERNÄT för Azure AD DS.

    Om de inte redan finns skapas automatiskt Azures virtuella nätverks peering mellan den befintliga hanterade domänens virtuella nätverk och det virtuella mål nätverket.

    Skärm bilden i följande exempel visar processen för att skapa en ny replik uppsättning i *USA, östra*:

    ![Exempel skärm bild för att skapa en replik uppsättning i Azure Portal](./media/tutorial-create-replica-set/create-replica-set.png)

1. När du är klar väljer du **Spara**.

Processen för att skapa replik uppsättningen tar en stund eftersom resurserna skapas i mål regionen. Den hanterade domänen replikeras sedan med AD DS-replikering.

Replik uppsättnings *rapporterna som en* distribution fortsätter, som du ser i följande exempel skärm bild. När du är klar visas replik uppsättningen som *körs*.

![Exempel skärm bild av distributions status för replik uppsättning i Azure Portal](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>Ta bort en replik uppsättning

En hanterad domän är för närvarande begränsad till fyra repliker – den första replik uppsättningen och tre ytterligare replik uppsättningar. Om du inte behöver en replik uppsättning längre, eller om du vill skapa en replik uppsättning i en annan region, kan du ta bort onödiga replik uppsättningar.

> [!IMPORTANT]
> Du kan inte ta bort den sista replik uppsättningen i en hanterad domän.

Utför följande steg för att ta bort en replik uppsättning:

1. I Azure Portal söker du efter och väljer **Azure AD Domain Services**.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På den vänstra sidan väljer du **replik uppsättningar**. I listan över replik uppsättningar väljer du snabb menyn **...** bredvid den replik uppsättning som du vill ta bort.
1. Välj **ta bort** på snabb menyn och bekräfta att du vill ta bort replik uppsättningen.

> [!NOTE]
> Borttagning av replik uppsättningar kan vara en tids krävande åtgärd.

Om du inte längre behöver det virtuella nätverket eller peering som används av replik uppsättningen kan du också ta bort dessa resurser. Se till att inga andra program resurser i den andra regionen behöver nätverks anslutningarna innan du tar bort dem.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera peering för virtuellt nätverk
> * Skapa en replik uppsättning i en annan geografisk region
> * Ta bort en replik uppsättning

Mer information finns i så här fungerar replik uppsättningar i Azure AD DS.

> [!div class="nextstepaction"]
> [Repliker anger koncept och funktioner][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
