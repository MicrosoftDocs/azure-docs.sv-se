---
title: Snabbstart – Skapa ett Azure IoT Central-| Microsoft Docs
description: Snabbstart – Skapa ett nytt Azure IoT Central program. Skapa programmet med antingen den kostnadsfria prisplanen eller någon av standardprisplanerna.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 7b33beaad580e64a4760b0557f04f266ecfc1b4d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718817"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Snabbstart – Skapa ett Azure IoT Central program

Den här snabbstarten visar hur du skapar ett Azure IoT Central program.

## <a name="prerequisite"></a>Förutsättning 

 - Ett Azure-konto med en aktiv prenumeration. Skapa ett konto utan [kostnad.](https://aka.ms/createazuresubscription)
 - Din Azure-prenumeration ska ha deltagaråtkomst

## <a name="create-an-application"></a>Skapa ett program

Gå till [Azure IoT Central build-webbplatsen.](https://aka.ms/iotcentral) Logga sedan in med ett personligt konto, arbetskonto eller skolkonto från Microsoft.

Du kan skapa ett nytt program antingen från listan över branschrelev relevanta IoT Central som hjälper dig att komma igång snabbt eller börja från grunden med hjälp av **en anpassad appmall.** I den här snabbstarten använder du **mallen Anpassat** program.

Skapa ett nytt Azure IoT Central från mallen **Anpassat** program:

1. Gå till **sidan Skapa:**

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Skapa din IoT-programsida":::

1. Välj **Anpassad app**

1. På sidan **Nytt program** kontrollerar du att Anpassat **program har** valts under **programmallen**.

1. Azure IoT Central automatiskt ett **programnamn baserat** på den programmall som du har valt. Du kan använda det här namnet eller ange ett eget eget programnamn.

1. Azure IoT Central också ett unikt **URL-prefix** åt dig, baserat på programnamnet. Du använder den här URL:en för att få åtkomst till ditt program. Ändra det här URL-prefixet till något som är mer lätt att komma ihåg om du vill.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central Skapa en programsida":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central faktureringsinformation":::

    > [!Tip]
    > Om du väljer **Anpassad app** på föregående sida visas listrutan **Programmall.** Listrutan kan visa andra mallar som har gjorts tillgängliga för dig av din organisation.

1. Välj att skapa det här programmet med prisplanen för 7 dagars kostnadsfri utvärderingsversion eller någon av standardprisplanerna:

    - Program som du skapar med *den kostnadsfria* planen är kostnadsfria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem så att de använder en standardprisplan när som helst innan de upphör att gälla.
        > [!NOTE]
        > Program som skapas med *den* kostnadsfria planen kräver inte någon Azure-prenumeration och därför visas de inte i din Azure-prenumeration på Azure Portal. Du kan bara se och hantera kostnadsfria appar från IoT Central portalen.          
    - Program som du skapar med en *standardplan* debiteras per enhet. Du kan välja **prisplanen Standard 0,** **Standard 1** eller **Standard 2** där de första två enheterna är kostnadsfria. Läs mer om de kostnadsfria och standardprisplanerna [på Azure IoT Central sidan med prisinformation.](https://azure.microsoft.com/pricing/details/iot-central/) Om du skapar ett program med hjälp av en standardprisplan måste du välja din *katalog,* *Azure-prenumeration* och *plats:*
        - *Katalogen* är den Azure Active Directory där du skapar ditt program. En Azure Active Directory innehåller användaridentiteter, autentiseringsuppgifter och annan organisationsinformation. Om du inte har någon Azure Active Directory skapas en åt dig när du skapar en Azure-prenumeration.
        - Med en *Azure-prenumeration* kan du skapa instanser av Azure-tjänster. IoT Central till resurser i din prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en kostnadsfritt på [sidan för Azure-registrering.](https://aka.ms/createazuresubscription) När du har skapat Azure-prenumerationen går du tillbaka till **sidan Nytt** program. Din nya prenumeration visas nu i **listrutan** Azure-prenumeration.
        - *Plats* är [det geografiska](https://azure.microsoft.com/global-infrastructure/geographies/) område där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. När du har valt en plats kan du inte senare flytta programmet till en annan plats.

1. Granska villkoren och välj **Skapa** längst ned på sidan. Efter några minuter är IoT Central program redo att användas:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central program":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett IoT Central-program. Här är det föreslagna nästa steget för att fortsätta lära sig mer om IoT Central:

> [!div class="nextstepaction"]
> [Lägga till en simulerad enhet i ditt IoT Central program](./quick-create-simulated-device.md)

Om du är enhetsutvecklare och vill fördjupa dig i kod föreslår vi att du gör följande:
> [!div class="nextstepaction"]
> [Skapa och ansluta ett klientprogram till ditt Azure IoT Central program](./tutorial-connect-device.md)
