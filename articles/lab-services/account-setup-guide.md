---
title: Installations guide för påskyndat labb konto för Azure Lab Services
description: Den här guiden hjälper administratörer att snabbt konfigurera ett labb konto för användning i sin skola.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669397"
---
# <a name="lab-account-setup-guide"></a>Installations guide för labb konto
Om du är administratör måste du först skapa ett *labb konto* i din Azure-prenumeration innan du konfigurerar din Azure Lab Servicess miljö. Ett labb konto är en behållare för en eller flera labb och det tar bara några minuter att konfigurera.

Den här guiden innehåller tre delar:
-  Förutsättningar
-  Planera inställningarna för ditt labb konto
-  Konfigurera ditt labb konto

## <a name="prerequisites"></a>Förutsättningar
Följande avsnitt beskriver vad du behöver göra innan du kan konfigurera ett labb konto.


### <a name="access-your-azure-subscription"></a>Få åtkomst till din Azure-prenumeration
Om du vill skapa ett labb konto måste du ha åtkomst till en Azure-prenumeration som redan har kon figurer ATS för din skola. Din skola kan ha en eller flera prenumerationer. Du använder en prenumeration för att hantera fakturering och säkerhet för alla dina Azure-resurser och-tjänster, inklusive Lab-konton.  Azure-prenumerationer hanteras vanligt vis av IT-avdelningen.  Mer information finns i avsnittet "prenumeration" i [Azure Lab Services-Administrator guide](./administrator-guide.md#subscription).

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>Beräkna hur många virtuella datorer och VM-storlekar du behöver
Det är viktigt att veta hur många [virtuella datorer (VM) och VM-storlekar som](./administrator-guide.md#vm-sizing) krävs för skol labbet. 

Vägledning om hur du strukturerar dina labb och bilder finns i blogg inlägget [flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

Mer information om hur du strukturerar labb finns i avsnittet "labb" i [Azure Lab Services-Administrator guide](./administrator-guide.md#lab).

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Förstå virtuella prenumerations gränser och regional VM-kapacitet
När du har beräknat antalet virtuella datorer och VM-storlekarna för dina labb måste du:

- Se till att din Azure-prenumerations kapacitets gräns tillåter antalet virtuella datorer och den VM-storlek som du planerar att använda i dina labb.
- Skapa ditt labb konto inom en region som har tillräcklig tillgänglig VM-kapacitet.

Mer information finns i [begränsningar för VM-prenumeration och regional kapacitet](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Bestäm hur många labb konton som ska skapas

Kom igång snabbt genom att skapa ett enda labb konto i en egen resurs grupp.  Senare kan du skapa ytterligare labb konton och resurs grupper efter behov. Du kanske till exempel har ett labb konto och en resurs grupp per avdelning som ett sätt att tydligt separera kostnader. 

Mer information om labb konton, resurs grupper och åtskiljande kostnader finns i:
- Avsnittet "resurs grupp" i [guiden Azure Lab Services-administratör](./administrator-guide.md#resource-group)
- Avsnittet "labb konto" i [Azure Lab Services-administratörs guide](./administrator-guide.md#lab-account) 
- [Kostnads hantering för Azure Lab Services](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>Planera inställningarna för ditt labb konto

Om du vill planera inställningarna för ditt labb konto bör du tänka på följande frågor.

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>Vem ska vara ägare och deltagare i labb kontot?

Din skol IT-administratörer tar vanligt vis rollerna ägare och deltagare för ett labb konto. Dessa roller ansvarar för att hantera de principer som gäller för alla labb i labb kontot. Den person som skapar labb kontot är automatiskt en ägare. Du kan lägga till ytterligare ägare och deltagare från den Azure Active Directory-klient (Azure AD) som är associerad med din prenumeration. 

Mer information om labb kontots ägare och deltagar roller finns i avsnittet "hantera identitet" i [Azure Lab Services-administratörs guide](./administrator-guide.md#manage-identity).

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

Labb användare ser bara en enda lista med de virtuella datorer som de har åtkomst till i Azure AD-klienter i Azure Lab Services.

### <a name="who-will-be-allowed-to-create-labs"></a>Vem ska kunna skapa labb?

Du kan välja att låta IT-teamet eller fakultets medlemmar skapa labb. För att skapa labb, tilldelar du sedan dessa personer till rollen labb skapare i labb kontot. Du tilldelar vanligt vis den här rollen från den Azure AD-klient som är kopplad till din skol prenumeration. Vem som skapar ett labb tilldelas automatiskt som Labbets ägare.  

Mer information om rollen labb skapare finns i avsnittet "hantera identitet" i [Azure Lab Services-administratörs guide](./administrator-guide.md#manage-identity).

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Vem ska kunna äga och hantera labb?

Du kan också välja att låta IT-och fakultets medlemmar own\manage Labs *utan* att ge dem möjlighet att skapa labb.  I det här fallet tilldelas användare från din prenumerations Azure AD-klient antingen ägaren eller deltagaren för befintliga labb.  

Mer information om labb ägare och deltagar roller finns i avsnittet "hantera identitet" i [Azure Lab Services-Administrator guide](./administrator-guide.md#manage-identity).

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>Vill du spara bilder och dela dem i labbet?

Delade avbildnings galleri är en tjänst som du kan använda för att spara och dela bilder. För klasser som behöver använda samma avbildning kan du skapa en avbildning i labbet och sedan exportera den till ett delat avbildnings Galleri.  När en bild har exporter ATS till galleriet för delad avbildning kan du använda den för att skapa nya labb.

Du kanske vill skapa avbildningar i din fysiska miljö och sedan importera dem till ett delat avbildnings Galleri. Mer information finns i blogg inlägget [Importera en anpassad avbildning till ett delat avbildnings Galleri](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353).

Om du bestämmer dig för att använda tjänsten Shared Image Gallery måste du skapa eller koppla ett delat avbildnings galleri till ditt labb konto. Du kan skjuta upp det här beslutet för tillfället, eftersom ett delat avbildnings Galleri kan kopplas till ett labb konto när som helst.  

Mer information finns i:
- Avsnittet "delade avbildnings Galleri" i [Azure Lab Services-administratörs guide](./administrator-guide.md#shared-image-gallery)
- Avsnittet "priser" i [Azure Lab Services-administratörs guide](./administrator-guide.md#pricing)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Vilka bilder i Azure Marketplace använder dina labb?

Azure Marketplace innehåller hundratals avbildningar som du kan aktivera så att Lab-skapare kan använda dem för att skapa sina labb. Vissa bilder kan innehålla allt som ett labb redan behöver. I andra fall kan du använda en avbildning som utgångs punkt och sedan kan du anpassa den genom att installera ytterligare program eller verktyg.

Om du inte vet vilka bilder du behöver kan du komma tillbaka senare för att aktivera dem. Det bästa sättet att se vilka bilder som är tillgängliga är att först skapa ett labb konto. Det ger dig åtkomst så att du kan granska listan med tillgängliga bilder och deras innehåll.  

Mer information finns i [Ange de Azure Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>Behöver de virtuella datorerna i labbet åtkomst till andra Azure-resurser eller lokala resurser?

När du konfigurerar ett labb konto kan du också peer-ditt labb konto med ett virtuellt nätverk.  Tänk på att både ditt virtuella nätverk och labb kontot måste finnas i samma region.  För att avgöra om du behöver peer-koppla med ett virtuellt nätverk, bör du tänka på följande scenarier:

- **Åtkomst till en licens Server**
  
   När du använder Azure Marketplace-avbildningar paketeras kostnaden för operativ system licensen i prissättningen för labb tjänster. Men du behöver inte ange licenser för själva operativ systemet. För ytterligare program vara och program som är installerade måste du ange en licens, efter vad som är tillämpligt.  För att få åtkomst till en licens Server:
   - Du kan välja att ansluta till en lokal licens Server.  För att ansluta till en lokal licens server krävs ytterligare konfiguration.
   - Ett annat alternativ, som är snabbare att konfigurera, är att skapa en licens server som du är värd för på en virtuell Azure-dator.  Den virtuella Azure-datorn finns i ett virtuellt nätverk som du peer-koppla med ditt labb konto.

- **Åtkomst till andra lokala resurser, till exempel en fil resurs eller databas**

   Du skapar vanligt vis ett virtuellt nätverk för att ge åtkomst till lokala resurser med hjälp av en plats-till-plats-virtuell nätverksgateway. Det tar extra tid att konfigurera den här typen av miljö.

- **Åtkomst till andra Azure-resurser som finns utanför ett virtuellt nätverk**

   Om du behöver åtkomst till Azure-resurser som *inte* är skyddade i ett virtuellt nätverk kan du komma åt dem via det offentliga Internet, utan att behöva göra någon peering.

   Mer information om virtuella nätverk finns i:
   - Avsnittet "virtuellt nätverk" i [arkitektur grunderna i Azure Lab Services](./classroom-labs-fundamentals.md#virtual-network)
   - [Anslut ditt labb nätverk till ett peer-virtuellt nätverk i Azure Lab Services](./how-to-connect-peer-virtual-network.md)
   - [Skapa ett labb med en delad resurs i Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>Konfigurera ditt labb konto

När du har avslutat planeringen är du redo att konfigurera ditt labb konto. Du kan använda samma steg för att konfigurera [Azure Lab Services i team](./lab-services-within-teams-overview.md).

1. **Skapa ditt labb konto**. Instruktioner finns i [skapa ett labb konto](./tutorial-setup-lab-account.md#create-a-lab-account).
   
    Information om namngivnings konventioner finns i avsnittet "Naming" i [Azure Lab Services-Administrator guide](./administrator-guide.md#naming).

1. **Lägg till användare i labb skapares rollen**. Instruktioner finns i [lägga till användare i rollen labb skapare](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

1. **Anslut till ett virtuellt peer-nätverk**. Instruktioner finns i [ansluta ditt labb nätverk till ett virtuellt peer-nätverk](./how-to-connect-peer-virtual-network.md).

   Du kan också behöva hänvisa till instruktioner för hur [du konfigurerar adress intervallet för labbets virtuella datorer](./how-to-configure-lab-accounts.md).

1. **Aktivera och granska bilder**. Anvisningar finns i [ange vilka Azure Marketplace-avbildningar som är tillgängliga för labb skapande](./specify-marketplace-images.md).

   Om du vill granska innehållet i varje Azure Marketplace-avbildning väljer du avbildningens namn. Följande skärm bild visar till exempel information om Ubuntu Data Science VM-avbildningen:

   ![Skärm bild av en lista med avbildningar som är tillgängliga för granskning på Azure Marketplace.](./media/setup-guide/review-marketplace-images.png)

   Om ett delat avbildnings galleri är kopplat till ditt labb konto och du vill aktivera anpassade avbildningar som ska delas av labb skapare, slutför du liknande steg som du ser i följande skärm bild:

   ![Skärm bild av en lista över aktiverade anpassade avbildningar i ett delat bild galleri.](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar och hanterar labb finns i:

- [Hantera labbkonton](how-to-manage-lab-accounts.md)  
- [Installations guide för labb](setup-guide.md)
