---
title: Skala en moln tjänst automatiskt (klassisk) i portalen | Microsoft Docs
description: Lär dig hur du använder portalen för att konfigurera regler för automatisk skalning för en moln tjänst roll (klassisk) i Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba7b2279969acab93ac96c42e0033e76e9d0542d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980838"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-classic-in-the-portal"></a>Så här konfigurerar du automatisk skalning för en moln tjänst (klassisk) i portalen

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Villkor kan anges för en arbets roll för en moln tjänst som utlöser en skalning i eller ut-åtgärd. Villkoren för rollen kan baseras på CPU, disk eller nätverks belastning för rollen. Du kan också ange ett villkor baserat på en meddelandekö eller mått för en annan Azure-resurs som är associerad med din prenumeration.

> [!NOTE]
> Den här artikeln fokuserar på moln tjänst (klassisk). När du skapar en virtuell dator (klassisk) direkt är den värdbaserad i en moln tjänst. Du kan skala en virtuell standard dator genom att associera den med en [tillgänglighets uppsättning](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic) och aktivera eller inaktivera dem manuellt.

## <a name="considerations"></a>Överväganden
Du bör fundera över följande information innan du konfigurerar skalning för programmet:

* Skalning påverkas av kärn användning.

    Större roll instanser använder fler kärnor. Du kan bara skala ett program inom gränsen för kärnor för din prenumeration. Anta till exempel att din prenumeration har en gräns på 20 kärnor. Om du kör ett program med två medel stora moln tjänster (totalt 4 kärnor) kan du bara skala upp andra moln tjänst distributioner i din prenumeration med de återstående 16 kärnorna. Mer information om storlekar finns i [moln tjänst storlekar](cloud-services-sizes-specs.md).

* Du kan skala baserat på ett tröskel för Queue meddelande. Mer information om hur du använder köer finns i [så här använder du tjänsten Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md).

* Du kan också skala andra resurser som är kopplade till din prenumeration.

* Om du vill aktivera hög tillgänglighet för ditt program bör du kontrol lera att det har distribuerats med två eller fler roll instanser. Mer information finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/).

* Automatisk skalning sker bara när alla roller har statusen **klar** .  


## <a name="where-scale-is-located"></a>Där skalan finns
När du har valt moln tjänsten bör du se till att moln tjänst bladet är synligt.

1. På bladet moln tjänst väljer du namnet på moln tjänsten på panelen **roller och instanser** .   
   **Viktigt**: se till att klicka på moln tjänst rollen, inte roll instansen som är under rollen.

    ![Skärm bild av panelen roller och instanser med arbets rollen med alternativet S B Queue 1 som beskrivs i rött.](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Välj **skalnings** panelen.

    ![Skärm bild av sidan åtgärder med försäljnings panelen som beskrivs i rött.](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>Automatisk skalning
Du kan konfigurera skalnings inställningar för en roll med antingen två lägen **manuellt** eller **automatiskt**. Manuellt är så som du förväntar dig, ställer du in det absoluta antalet instanser. Med automatisk kan du ange regler som styr hur mycket du ska skala.

Ställ in alternativet **skala enligt** för **schema-och prestanda regler**.

![bild moln tjänster skalnings inställningar med profil och regel](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. En befintlig profil.
2. Lägg till en regel för den överordnade profilen.
3. Lägg till en annan profil.

Välj **Lägg till profil**. Profilen avgör vilket läge du vill använda för skalan: **Always**, **upprepnings** **datum och fast datum**.

När du har konfigurerat profilen och reglerna väljer du ikonen **Spara** längst upp.

#### <a name="profile"></a>Profil
Profilen anger minsta och högsta antal instanser för skalan och även när det här skalnings intervallet är aktivt.

* **Alltid**

    Behåll alltid det här instans intervallet tillgängligt.  

    ![Moln tjänst som alltid skalar](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Upprepning**

    Välj en uppsättning dagar i veckan som ska skalas.

    ![Moln tjänst skala med ett upprepnings schema](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Fast datum**

    Ett fast datum intervall för att skala rollen.

    ![Moln tjänst skala med ett fast datum](./media/cloud-services-how-to-scale-portal/select-fixed.png)

När du har konfigurerat profilen väljer du knappen **OK** längst ned på bladet profil.

#### <a name="rule"></a>Regel
Regler läggs till i en profil och representerar ett villkor som utlöser skalan.

Regel utlösaren baseras på ett mått för moln tjänsten (processor användning, disk aktivitet eller nätverks aktivitet) som du kan lägga till ett villkorligt värde i. Du kan också ha utlösaren baserat på en meddelandekö eller en annan Azure-resurs som är associerad med din prenumeration.

![Skärm bild av dialog rutan regel med alternativet för mått namn som anges i rött.](./media/cloud-services-how-to-scale-portal/rule-settings.png)

När du har konfigurerat regeln väljer du knappen **OK** längst ned på bladet regel.

## <a name="back-to-manual-scale"></a>Tillbaka till manuell skalning
Navigera till [skalnings inställningarna](#where-scale-is-located) och ange alternativet **skala efter** till **ett instans antal som jag anger manuellt**.

![Skalnings inställningar för moln tjänster med profil och regel](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Den här inställningen tar bort automatisk skalning från rollen och du kan sedan ange antalet instanser direkt.

1. Alternativet skala (manuellt eller automatiskt).
2. Ett skjutreglage för roll instans som anger vilka instanser som ska skalas till.
3. Instanser av rollen som ska skalas till.

När du har konfigurerat skalnings inställningarna väljer du ikonen **Spara** längst upp.
