---
title: Privata erbjudanden på Microsofts kommersiella marknads platser
description: Privata erbjudanden på Microsofts kommersiella marknads plats för app-och tjänst utgivare.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 01/28/2021
ms.openlocfilehash: 786ecbf553ace6a90515347e8138eeb6e022589b
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063104"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Privata erbjudanden på Microsofts kommersiella marknads plats

Privata erbjudanden, även kallade privata planer, som gör det möjligt för utgivare att skapa planer som bara är synliga för riktade kunder. I den här artikeln beskrivs alternativen och fördelarna med privata erbjudanden.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Lås upp företags erbjudanden med privata erbjudanden

Genom att skapa privata erbjudanden kan utgivare privat erbjuda anpassade lösningar till riktade kunder med funktioner som företag kräver:

- Med _förhandlad prissättning_ kan utgivare utöka rabatter och priser utanför listan från offentligt tillgängliga erbjudanden.
- _Privata allmänna_ villkor gör det möjligt för utgivare att skräddarsy villkor för en specifik kund.
- _Specialiserade konfigurationer_ gör att utgivare skräddarsyr sina Virtual Machines, Azure-program och program vara som en tjänst (SaaS) till en enskild kunds behov. Det här alternativet gör det också möjligt för utgivare att tillhandahålla för hands versions åtkomst till nya produkt funktioner innan de startas till alla kunder.

Privata erbjudanden gör att utgivare kan dra nytta av skalbarheten och den globala tillgängligheten för en offentlig marknads plats, med flexibilitet och kontroll som behövs för att förhandla fram och leverera anpassade erbjudanden och konfigurationer. Företag kan nu köpa och sälja på det sätt de förväntar sig 

## <a name="create-private-offers-using-plans"></a>Skapa privata erbjudanden med hjälp av planer

För *nya eller befintliga erbjudanden med planer* kan utgivare enkelt skapa nya, privata variationer genom att skapa nya planer (tidigare kallade SKU: er) och markera dem som privata. Varje erbjudande kan ha upp till 45 privata planer.

<!--- [Private SKUs]() --->

Privata planer är tillgängliga för följande erbjudande typer:

- Virtuell Azure-dator
- Azure Application (implementeras som lösningsfiler eller hanterade program)
- Hanterad tjänst
- SaaS-erbjudanden

Privata planer är komponenter i ett erbjudande och är bara synliga och köpbara av de riktade kunderna. Privata planer är bara synliga och köpbara av de riktade kunderna.  Privata planer kan göras tillgängliga för kunder i både Azure Global och Azure Government.

Privata planer kan återanvända bas avbildningar och/eller erbjudna metadata som redan har publicerats för ett offentligt abonnemang. Med det här alternativet kan utgivare skapa flera privata varianter av ett offentligt erbjudande utan att behöva publicera flera versioner av samma bas avbildning och erbjuda metadata. När en privat plan delar en bas avbildning med ett offentligt abonnemang för Azure Virtual Machine och Azure-program, kommer alla ändringar i bas avbildningen av erbjudandet att spridas över alla offentliga och privata planer med den bas avbildningen.

För *nya erbjudanden som bara omfattar privata planer* kan utgivare skapa sina erbjudanden som ett annat erbjudande och sedan markera planerna som privata. Erbjudanden som bara har privata planer kommer inte att kunna identifieras eller nås i [Azure Portal](https://azure.microsoft.com/features/azure-portal/) av kunder som inte är associerade med erbjudandet.

>[!NOTE]
>Ett erbjudande som bara innehåller privata planer visas inte i den offentliga Azure Marketplace eller AppSource.

## <a name="target-customers-with-private-offers"></a>Rikta kunder mot privata erbjudanden

För både nya och befintliga privata erbjudanden kan utgivare rikta kunder med prenumerations-ID. För virtuella Azure-datorer, Azure Application och hanterade tjänst erbjudanden kan utgivare begränsa tillgängligheten för en privat plan till ett enskilt Azure-prenumerations-ID eller ladda upp en CSV med upp till 10 000 Azure-prenumerations-ID: n. För SaaS-erbjudanden kan utgivare associera ett Azure Active Directory klient-ID för att begränsa tillgängligheten för en privat plan med hjälp av antingen manuell eller CSV-överföring.

När ett erbjudande har certifierats och publicerats kan kunder uppdateras eller tas bort från planen med hjälp av funktionen synkronisera privata prenumerationer. Den här funktionen gör det möjligt för utgivare att snabbt och enkelt uppdatera listan över kunder som den privata planen presenteras i utan att du behöver certifiera eller publicera erbjudandet igen.

## <a name="deploying-private-offers"></a>Distribuera privata erbjudanden

När du har loggat in på Azure Portal kan kunderna följa de här stegen för att välja dina privata erbjudanden.

1. Logga in på [Azure Portal](https://ms.portal.azure.com/).
1. Under **Azure-tjänster** väljer du **skapa en resurs**.
1. På den **nya** sidan bredvid **Azure Marketplace** väljer du **Visa alla**. Sidan Marketplace visas.
1. Välj **privata erbjudanden** i det vänstra navigerings fältet.

> [!NOTE]
> Privata erbjudanden kan bara upptäckas i [Azure Portal](https://azure.microsoft.com/features/azure-portal/). De visas inte i [Microsoft AppSource](https://appsource.microsoft.com/) eller på [Azure Marketplace](https://azuremarketplace.microsoft.com). Mer information om hur du publicerar till de olika onlinebutiker för kommersiella platser finns i [Introduktion till List alternativ](./determine-your-listing-type.md).

Privata erbjudanden visas också i Sök Resultat och kan distribueras via kommando raden och Azure Resource Manager mallar, precis som med andra erbjudanden.

![[Privata erbjudanden]](./media/marketplace-publishers-guide/private-offer.png)

Privata erbjudanden visas också i Sök resultaten. Leta bara efter det **privata** märket.

>[!Note]
>Privata erbjudanden stöds inte med prenumerationer som upprättats via en åter försäljare av CSP-programmet (Cloud Solution Provider).


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
