---
title: Aver vFXT-krav – Azure
description: Lär dig mer om aktiviteter som ska utföras innan du skapar ett kluster i AVERT vFXT för Azure, inklusive hantering av prenumerationer, kvoter och lagrings tjänst slut punkter.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: be5c640ef069690c408460b7374aee82c35b3a67
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272424"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Förbereda för att skapa Avere vFXT

I den här artikeln beskrivs nödvändiga åtgärder för att skapa ett AVERT vFXT-kluster.

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

Börja med att skapa en ny Azure-prenumeration. Använd en separat prenumeration för varje AVERT vFXT-projekt för att förenkla utgifts spårning och rensning, och se till att andra projekt inte påverkas av kvoter eller resurs begränsning vid etablering av kluster arbets flödet.

För att skapa en ny Azure-prenumeration i Azure Portal:

1. Gå till [prenumerations bladet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klicka på knappen **+ Lägg till** överst
1. Logga in om du uppmanas att göra det
1. Välj ett erbjudande och gå igenom stegen för att skapa en ny prenumeration

## <a name="configure-subscription-owner-permissions"></a>Konfigurera ägar behörigheter för prenumeration

En användare med ägar behörigheter för prenumerationen bör skapa vFXT-klustret. För att skapa kluster krävs en ägare för att godkänna program varu villkoren och för att tillåta ändringar i nätverks-och lagrings resurser.

Det finns vissa lösningar för att tillåta att en icke-ägare skapar ett AVERT vFXT för Azure-kluster. I dessa scenarier ingår att begränsa resurser och tilldela ytterligare Azure-roller till Skaparen. I alla dessa fall måste en prenumerations ägare också [godkänna vFXT-programtermerna](#accept-software-terms) i förväg.

| Scenario | Begränsningar | Åtkomst roller som krävs för att skapa ett AVERT vFXT-kluster |
|----------|--------|-------|
| Resurs grupps administratören skapar vFXT | Virtuella nätverk, kluster styrenheter och klusternoder måste skapas i resurs gruppen. | [Användarens åtkomst administratör](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagar](../role-based-access-control/built-in-roles.md#contributor) roller, båda är begränsade till mål resurs gruppen. |
| Använd ett befintligt, externt virtuellt nätverk | Kluster styrenheten och klusternoderna skapas i resurs gruppen vFXT, men använder ett befintligt virtuellt nätverk i en annan resurs grupp. | (1) [administratör för användar åtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagar](../role-based-access-control/built-in-roles.md#contributor) roller som är begränsade till resurs gruppen vFXT och (2) [virtuell dator deltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [användar åtkomst administratör](../role-based-access-control/built-in-roles.md#user-access-administrator)och [AVERT deltagar](../role-based-access-control/built-in-roles.md#avere-contributor) roller som är begränsade till det virtuella nätverkets resurs grupp. |
| Anpassad roll för kluster skapare | Inga begränsningar för resurs placering. Den här metoden ger icke-ägare betydande privilegier. | Prenumerationens ägare skapar en anpassad Azure-roll enligt beskrivningen i [den här artikeln](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Kvot för vFXT-klustret

Kontrol lera att du har tillräcklig kvot för följande Azure-komponenter. Om det behövs kan du [begära en kvot ökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> De virtuella datorer och SSD-komponenter som anges här är för själva vFXT-klustret. Kom ihåg att du även behöver kvot för de virtuella datorer och SSD som du ska använda för beräknings gruppen.
>
> Kontrol lera att kvoten är aktive rad för den region där du vill köra arbets flödet.

|Azure-komponent|Kvot|
|----------|-----------|
|Virtuella datorer|3 eller flera E32s_v3 (ett per klusternod) |
|Premium SSD-lagring|200 GB OS-utrymme plus 1 TB till 4 TB cacheutrymme per nod |
|Lagringskonto (valfritt) |v2|
|Data backend-lagring (valfritt) |En ny LRS BLOB-behållare |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Godkänn program varu villkoren

> [!TIP]
> Hoppa över det här steget om en prenumerations ägare kommer att skapa ett AVERT vFXT-kluster.

När klustret skapas måste du godkänna villkoren för tjänsten för den Avera vFXT-programvaran. Om du inte är prenumerations ägare måste du ha en prenumerations ägare som accepterar villkoren i förväg.

Det här steget behöver bara utföras en gång per prenumeration.

Så här godkänner du program varu villkoren i förväg:

1. Öppna ett Cloud Shell i Azure Portal eller genom att bläddra till <https://shell.azure.com> . Logga in med ditt prenumerations-ID.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Utfärda det här kommandot för att acceptera tjänst villkoren och aktivera programmerings åtkomst för den Avera vFXT för Azures program avbildning:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Skapa en slut punkt för lagrings tjänsten i ditt virtuella nätverk (om det behövs)

En [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) håller Azure Blob-trafik lokalt i stället för att dirigera den utanför det virtuella nätverket. Det rekommenderas för alla Avera vFXT för Azure-kluster som använder Azure Blob för Server dels data lagring.

Om du skapar ett nytt virtuellt nätverk när du skapar klustret skapas en slut punkt automatiskt. Om du anger ett befintligt virtuellt nätverk måste det ha en Microsoft Storage Service-slutpunkt om du vill skapa en ny Blob Storage-behållare när klustret skapas.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Hoppa över det här steget om du skapar ett nytt virtuellt nätverk som en del av skapandet av klustret.
>* En slut punkt är valfri om du inte skapar Blob Storage när klustret skapas. I så fall kan du skapa tjänst slut punkten senare om du bestämmer dig för att använda Azure blob.

Skapa lagrings tjänstens slut punkt från Azure Portal.

1. Öppna listan med virtuella nätverk i portalen.
1. Välj det virtuella nätverket för klustret.
1. Klicka på **tjänst slut punkter** på den vänstra menyn.
1. Klicka på **Lägg till** längst upp.
1. Välj tjänsten ``Microsoft.Storage`` .
1. Välj klustrets undernät.
1. Klicka på **Lägg till**längst ned på sidan.

   ![Azure Portal skärm bild med anteckningar för stegen för att skapa tjänst slut punkten](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Nästa steg

När du har slutfört de här kraven kan du skapa klustret. Läs [distribuera vFXT-klustret](avere-vfxt-deploy.md) för instruktioner.
