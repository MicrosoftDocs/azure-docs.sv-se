---
title: Programmet Azure Certified Device – självstudie – Skapa ett projekt
description: Guide för att skapa ett projekt på Azure Certified Device-portalen
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: e5602e133ac8ab13779c9dfebca21d97265d9d76
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969808"
---
# <a name="tutorial-create-your-project"></a>Självstudie: skapa ett projekt

Grattis på valet att certifiera din enhet via programmet Azure Certified Device! Du har nu valt lämpligt certifierings program för enheten och är redo att komma igång på portalen.

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Logga in på [Azure-certifierad enhets Portal](https://certify.azure.com/)
> * Skapa ett nytt certifierings projekt för din enhet
> * Ange grundläggande enhets information för ditt projekt

## <a name="prerequisites"></a>Förutsättningar

- Du behöver ett giltigt arbets-/skol [Azure Active Directory konto](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).
- Du behöver ett verifierat Microsoft Partner Network-konto (MPN). Om du inte har ett MPN-konto kan du [ansluta till partner nätverket](https://partner.microsoft.com/) innan du börjar.

## <a name="signing-into-the-azure-certified-device-portal"></a>Logga in på Azure-certifierad enhets Portal

För att komma igång måste du logga in på portalen där du kommer att ange din enhets information, slutföra certifierings testningen och hantera dina enhets publikationer till Azure Certified Device-katalogen.

1. Gå till [Azure-certifierad enhets Portal](https://certify.azure.com).
1. Välj till `Company profile` vänster och uppdatera information om tillverkaren.
   ![Avsnittet företags profil](./media/images/company-profile.png)
1. Godkänn program avtalet för att påbörja projektet.

## <a name="creating-your-project-on-the-portal"></a>Skapa ditt projekt på portalen

Nu när du har konfigurerat på portalen kan du starta certifierings processen. Först måste du skapa ett projekt för enheten.

1. På Start sidan väljer du `Create new project` . Då öppnas ett fönster där du kan lägga till grundläggande enhets information i nästa avsnitt.

 ![Bild av knappen Skapa nytt projekt](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Identifiera grundläggande enhets information

Sedan måste du ange grundläggande enhets information. Du kan redigera den här informationen senare.

1. Fyll i de obligatoriska fälten i `Basics` avsnittet. Se tabellen nedan för klargöranden avseende de **obligatoriska** fälten:

    | Fält                  | Beskrivning                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Projektnamn           | Internt namn som inte visas i Azure-certifierad enhets katalog                                                        |
    | Enhetsnamn            | Offentligt namn på din enhet                                                                                                |
    | Enhetstyp            | Specifikation av färdig produkt eller Solution-Ready Developer Kit.     Mer information om terminologin finns i [certifierings ord lista](./resources-glossary.md).                                                                     |
    | Enhets klass           | Gateway, sensor eller annan.  Mer information om terminologin finns i [certifierings ord lista](./resources-glossary.md).                                                                    |
    | Källkods-URL för enhet | Krävs om du certifierar en Solution-Ready dev-sats, annars. URL: en måste vara en GitHub-plats för enhets koden. |
1. Klicka på `Next` knappen för att fortsätta till `Certifications` fliken.

    ![Bild av fliken Skapa nytt projekt, certifieringar](./media/images/create-new-project-certificationswindow.png)

1. Ange vilka certifikat som du vill uppnå för din enhet.
1. Välj `Create` och det nya projektet kommer att sparas och visas på Start sidan i portalen.

    ![Bild av projekt tabell](./media/images/project-table.png)

1. Välj på projekt namnet i tabellen. Sidan projekt Sammanfattning öppnas där du kan lägga till och visa annan information om enheten.

    ![Bild av sidan projekt information](./media/images/device-details-section.png)

## <a name="next-steps"></a>Nästa steg

Du är nu redo att lägga till enhets information och testa enheten med vår certifierings tjänst. Gå vidare till nästa artikel om du vill lära dig hur du redigerar din enhets information.
> [!div class="nextstepaction"]
> [Självstudie: lägga till enhets information](tutorial-02-adding-device-details.md)
