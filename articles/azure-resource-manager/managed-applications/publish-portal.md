---
title: Publicera hanterade appar via portalen
description: Visar hur du använder Azure Portal för att skapa ett Azure-hanterat program som är avsett för medlemmar i din organisation.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651727"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicera ett tjänst katalog program via Azure Portal

Du kan använda Azure Portal för att publicera [hanterade program](overview.md) som är avsedda för medlemmar i din organisation. En IT-avdelning kan exempelvis publicera hanterade program som säkerställer efterlevnaden av organisationens standarder. Dessa hanterade program är tillgängliga via tjänstkatalogen, inte på Azure Marketplace.

## <a name="prerequisites"></a>Förutsättningar

När du publicerar ett hanterat program kan du ange en identitet för att hantera resurserna. Vi rekommenderar att du anger en Azure Active Directory användar grupp. Om du vill skapa en Azure Active Directory användar grupp, se [skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 

Zip-filen som innehåller definitionen för det hanterade programmet måste vara tillgänglig via en URI. Vi rekommenderar att du överför zip-filen till en lagrings-blob. 

## <a name="create-managed-application-with-portal"></a>Skapa hanterat program med portalen

1. I det övre vänstra hörnet väljer du **+ ny**.

   ![Ny tjänst](./media/publish-portal/new.png)

1. Sök efter **tjänst katalog**.

1. Rulla tills du hittar **tjänst katalogens hanterade program definition** i resultatet. Välj den.

   ![Sök efter definitioner för hanterade program](./media/publish-portal/select-managed-apps-definition.png)

1. Välj **skapa** för att starta processen för att skapa den hanterade program definitionen.

   ![Skapa definition av hanterade program](./media/publish-portal/create-definition.png)

1. Ange värden för namn, visnings namn, beskrivning, plats, prenumeration och resurs grupp. Ange sökvägen till zip-filen som du skapade för paket filens URI.

   ![Ange värden](./media/publish-portal/fill-application-values.png)

1. När du kommer till avsnittet autentisering och lås nivå väljer du **Lägg till auktorisering**.

   ![Lägg till auktorisering](./media/publish-portal/add-authorization.png)

1. Välj en Azure Active Directory grupp för att hantera resurserna och välj **OK**.

   ![Lägg till auktoriseringsregel](./media/publish-portal/add-auth-group.png)

1. När du har angett alla värden väljer du **skapa**.

   ![Skapa hanterat program](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempel på hanterade program finns i [exempel projekt för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.