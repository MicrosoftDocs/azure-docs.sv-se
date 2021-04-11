---
title: Repliker anger koncept för Azure AD Domain Services | Microsoft Docs
description: Lär dig vad replik uppsättningar är i Azure Active Directory Domain Services och hur de ger redundans till program som kräver identitets tjänster.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: justinha
ms.openlocfilehash: 8bcd3ebef027ec72728be21b0fe1504236f553ba
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058178"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>Repliker anger koncept och funktioner för Azure Active Directory Domain Services

När du skapar en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän definierar du ett unikt namn område. Det här namn området är domän namnet, till exempel *aaddscontoso.com* och två domänkontrollanter (DCS) distribueras sedan till den valda Azure-regionen. Den här distributionen av DCs kallas för en replik uppsättning.

Du kan expandera en hanterad domän så att den har fler än en replik uppsättning per Azure AD-klient. Replik uppsättningar kan läggas till i alla peer-kopplat virtuella nätverk i alla Azure-regioner som har stöd för Azure AD DS. Ytterligare replik uppsättningar i olika Azure-regioner ger geografisk haveri beredskap för äldre program om en Azure-region försätts i offlineläge.

> [!NOTE]
> Med replik uppsättningar kan du inte distribuera flera unika hanterade domäner i en enda Azure-klient. Varje replik uppsättning innehåller samma data.

## <a name="how-replica-sets-work"></a>Så här fungerar replik uppsättningar

När du skapar en hanterad domän, till exempel *aaddscontoso.com*, skapas en inledande replik uppsättning. Ytterligare replik uppsättningar delar samma namnrymd och konfiguration. Ändringar i Azure AD DS, inklusive konfiguration, användar identitet och autentiseringsuppgifter, grupper, grup princip objekt, dator objekt och andra ändringar tillämpas på alla replik uppsättningar i den hanterade domänen med AD DS-replikering.

Du skapar varje replik uppsättning i ett virtuellt nätverk. Varje virtuellt nätverk måste vara peer-kopplat till alla andra virtuella nätverk som är värdar för en hanterad domäns replik uppsättning. Den här konfigurationen skapar en nätverkstopologi för nät verk som stöder Katalogduplicering. Ett virtuellt nätverk kan stödja flera replik uppsättningar, förutsatt att varje replik uppsättning finns i ett annat virtuellt undernät.

Alla replik uppsättningar placeras på samma Active Directory plats. Resultatet blir att alla ändringar sprids med replikering mellan platser för snabb konvergens.

> [!NOTE]
> Det går inte att definiera separata platser och definiera replikeringsinställningar mellan replik uppsättningar.

Följande diagram visar en hanterad domän med två replik uppsättningar. Den första replik uppsättningen skapas med domän namn området. En andra replik uppsättning skapas efter den:

![Diagram över exempel på hanterad domän med två replik uppsättningar](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> Replik uppsättningar garanterar tillgänglighet för autentiseringstjänster i regioner där en replik uppsättning har kon figurer ATS. För att ett program ska ha geografisk redundans om det uppstår ett regionalt avbrott måste den program plattform som förlitar sig på den hanterade domänen också finnas i den andra regionen.
>
> Andra tjänster som krävs för att programmet ska fungera, till exempel virtuella Azure-datorer eller Azure App-tjänster, tillhandahålls inte av replik uppsättningar. Tillgänglighets design för andra program komponenter måste överväga återhämtnings funktioner för tjänster som utgör programmet.

I följande exempel visas en hanterad domän med tre replik uppsättningar för att ytterligare kunna tillhandahålla återhämtning och garantera tillgänglighet för Authentication Services. I båda exemplen finns program arbets belastningar i samma region som den hanterade domän replik uppsättningen:

![Diagram över exempel på hanterad domän med tre replik uppsättningar](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Distributionsöverväganden

Standard-SKU: n för en hanterad domän är *företags* -SKU: n, som stöder flera replik uppsättningar. Om du vill skapa fler replik uppsättningar om du har ändrat till *standard* -SKU: n [uppgraderar du den hanterade domänen](change-sku.md) till *Enterprise* eller *Premium*.

Det högsta antalet replik uppsättningar som stöds är fem, inklusive den första repliken som skapas när du skapade den hanterade domänen.

Faktureringen för varje replik uppsättning baseras på SKU: n för domän konfiguration. Om du till exempel har en hanterad domän som använder *Enterprise* -SKU: n och du har tre replik uppsättningar debiteras din prenumeration per timme för var och en av de tre replik uppsättningarna.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Kan jag skapa en replik uppsättning i en annan prenumeration än min hanterade domän?

Nej. Replik uppsättningar måste vara i samma prenumeration som den hanterade domänen.

### <a name="how-many-replica-sets-can-i-create"></a>Hur många replik uppsättningar kan jag skapa?

Du kan skapa högst fem replik uppsättningar – den inledande replik uppsättningen för den hanterade domänen plus ytterligare fyra replik uppsättningar.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Hur kommer användar-och grupp information att synkroniseras med mina replik uppsättningar?

Alla replik uppsättningar är anslutna till varandra med hjälp av en virtuell nätverks-peering i nätet. En replik uppsättning får användar-och grupp uppdateringar från Azure AD. Dessa ändringar replikeras sedan till de andra replik uppsättningarna som använder AD DS-replikering inom ett peer-nätverk.

Precis som med lokal AD DS kan ett utökat frånkopplat tillstånd orsaka avbrott i replikeringen. Eftersom peer-kopplat virtuella nätverk inte är transitivt, kräver design kraven för replik uppsättningar en helt nättopologi för nätverk.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>Hur gör jag för att göra ändringar i min hanterade domän efter att jag har replik uppsättningar?

Ändringar i den hanterade domänen fungerar precis som de tidigare var. Du [skapar och använder en virtuell hanterings dator med de RSAT-verktyg som är anslutna till den hanterade domänen](tutorial-create-management-vm.md). Du kan ansluta så många hanterings-VM: er till den hanterade domänen som du vill.

## <a name="next-steps"></a>Nästa steg

[Skapa och konfigurera en Azure AD DS-hanterad domän][tutorial-create-advanced]för att komma igång med replik uppsättningar. När du har distribuerat [skapar du och använder ytterligare replik uppsättningar][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
