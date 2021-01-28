---
title: Så här konfigurerar du en privat slut punkt – QnA Maker
description: Det finns mer information om hur du skapar privata slut punkter i QnA Maker hanterade.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956592"
---
# <a name="private-endpoints"></a>Privata slutpunkter

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. QnA Maker har nu stöd för att skapa privata slut punkter till tjänsten Azure Search. Den här funktionen är tillgänglig i QnA Maker hanterad. 

Privata slut punkter tillhandahålls av en [privat Azure-länk](https://docs.microsoft.com/azure/private-link/private-link-overview), som en separat tjänst. Mer information om kostnader finns på sidan med [priser.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Krav
> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.
> * En QnA Maker [hanterad resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) som skapats i Azure Portal. Kom ihåg Azure Active Directory ID, prenumeration, QnA-resurs namn som du valde när du skapade resursen.

## <a name="steps-to-enable-private-endpoint"></a>Steg för att aktivera privat slut punkt
1. Tilldela *Contribute* -rollen för att QNA Maker hanterad tjänst i Azure Searchs tjänst instansen. Den här åtgärden kräver *ägar* åtkomst till prenumerationen. Hämta identiteten genom att gå till fliken identitet i tjänst resursen.
![Hanterad tjänst identitet](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Lägg till identiteten ovan ovan som för *Contribute* genom att gå till Azure Search tjänstens IAM-flik. ![ Hanterad tjänst-IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Klicka på *Lägg till roll tilldelningar*, Lägg till identiteten och klicka på *Spara*.
![Hanterad roll tilldelning](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Gå nu till fliken *nätverk* i Azure Search tjänst instansen och Byt slut punkts anslutnings data från *offentlig* till *privat*. Den här åtgärden är en tids krävande process och kan ta upp till 30 minuter att slutföra. 
![Hanterade Azure Search-nätverk](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Gå till fliken *nätverk* i QNA Maker hanterad tjänst och under alternativet *Tillåt åtkomst från* väljer du alternativet *valda nätverk och privata slut punkter* och klickar på *Spara*. 
![Hanterad QnA Maker-newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Detta upprättar en privat slut punkts anslutning mellan QnA Maker-tjänsten och Azure kognitiv search service-instansen. Du kan kontrol lera den privata slut punkts anslutningen på fliken *nätverk* i Azure Search tjänst instans. När hela åtgärden har slutförts är du redo att använda din QnA Maker-tjänst. 
![Hanterad nätverks tjänst](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Information om stöd
 * Vi stöder inte ändrings Azure Search tjänsten när du har aktiverat privat åtkomst till din QnAMaker-tjänst. Om du ändrar Azure Search tjänsten via fliken "konfiguration" när du har aktiverat privat åtkomst kommer QnAMaker-tjänsten att bli oanvändbar.
 * När du har upprättat en privat slut punkts anslutning kan du inte använda QnAMaker-tjänsten om du växlar Azure Search tjänst nätverk till "offentlig". Azure Search tjänst nätverk måste vara privat för att den privata slut punkts anslutningen ska fungera
