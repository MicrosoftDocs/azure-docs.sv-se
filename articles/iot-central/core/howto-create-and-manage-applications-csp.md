---
title: Skapa och hantera Azure IoT Central-program från CSP-portalen | Microsoft Docs
description: Som KRYPTOGRAFIPROVIDER skapar du ett Azure IoT Central-program för kundens räkning.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675815"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Skapa och hantera ett Azure IoT Central-program från CSP-portalen

CSP-programmet (Microsoft Cloud Solution Provider) är ett Microsoft-program för åter försäljare. Avsikten är att tillhandahålla våra kanal partner med ett program som gör att du kan sälja alla Microsofts kommersiella online tjänster. Läs mer om [Cloud Solution Provider-programmet](https://partner.microsoft.com/cloud-solution-provider).

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Som KRYPTOGRAFIPROVIDER kan du skapa och hantera Microsoft Azure IoT Central program för dina kunders räkning via [Microsoft Partner Center](https://partnercenter.microsoft.com/partner/home). När Azure IoT Central-program skapas för kunders räkning med kryptografiproviders, precis som med andra CSP-hanterade Azure-tjänster, hanterar CSP fakturering för kunder. En avgift för Azure IoT Central kommer att visas i din totala faktura i Microsoft Partner Center.

Kom igång genom att logga in på ditt konto på Microsoft Partner Portal och välj en kund för vilken du vill skapa ett Azure IoT Central-program. Navigera till Service Management för kunden från det vänstra navigerings fältet.

![Microsoft Partner Center, kund visning](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central listas som en tjänst som är tillgänglig för administration. Välj Azure IoT Central-länken på sidan för att skapa nya program eller hantera befintliga program för den här kunden.

![Azure-IoT Central tillgängligt för hantering](media/howto-create-and-manage-applications-csp/image2.png)

Du land på sidan för Azure IoT Central Application Manager. Azure IoT Central håller kontexten som du kom från Microsoft Partner Center och som du kom att hantera den specifika kunden. Du ser det här bekräftelsen i rubriken på program hanterings sidan. Härifrån kan du antingen navigera till ett befintligt program som du har skapat tidigare för den här kunden för att hantera eller skapa ett nytt program för kunden.

![Skapa hanterare för kryptografiproviders](media/howto-create-and-manage-applications-csp/image3.png)

Om du vill skapa ett Azure IoT Central-program väljer du **skapa** på den vänstra menyn. Välj en av bransch mallarna eller Välj **anpassad app** för att skapa ett program från grunden. Då öppnas sidan Skapa program. Du måste fylla i alla fält på den här sidan och sedan välja **skapa**. Du hittar mer information om vart och ett av fälten nedan.

![Skärm bild som visar sidan "Bygg ditt IoT-program" med knappen "Build" vald.](media/howto-create-and-manage-applications-csp/image4.png)

![Skapa program sida för kryptografiproviders](media/howto-create-and-manage-applications-csp/image4-1.png)

![Information om att skapa program sida för CSP-fakturering](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Pris plan

Du kan bara skapa program som använder en standard pris plan som en CSP. För att kunna presentera Azure-IoT Central till kunden kan du skapa ett program som använder den kostnads fria pris Planen separat. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Du kan bara skapa program som använder en standard pris plan som en CSP. För att kunna presentera Azure-IoT Central till kunden kan du skapa ett program som använder den kostnads fria pris Planen separat. Läs mer om kostnads fria priser och standard priser på [sidan med priser för Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Programnamn

Namnet på ditt program visas på **program hanterings** sidan och i varje Azure IoT Central-program. Du kan välja ett namn för ditt Azure IoT Central-program. Välj ett namn som passar dig och till andra i din organisation.

## <a name="application-url"></a>Program-URL

Programmets URL är länken till ditt program. Du kan spara ett bok märke i webbläsaren eller dela det med andra.

När du anger namnet på programmet genereras din program-URL automatiskt. Om du vill kan du välja en annan URL för ditt program. Varje Azure IoT Central-URL måste vara unik i Azure IoT Central. Ett fel meddelande visas om den URL du väljer redan har gjorts.

## <a name="directory"></a>Katalog

Eftersom Azure IoT Central har sammanhang som du har för att hantera kunden som du valde i Microsoft Partner Portal, ser du bara Azure Active Directory klienten för kunden i fältet katalog. 

En Azure Active Directory klient organisation innehåller användar identiteter, autentiseringsuppgifter och annan organisatorisk information. Flera Azure-prenumerationer kan associeras med en enda Azure Active Directory-klient.

Läs mer i [Azure Active Directory](../../active-directory/index.yml).

## <a name="azure-subscription"></a>Azure-prenumeration

Med en Azure-prenumeration kan du skapa instanser av Azure-tjänster. Azure IoT Central hittar automatiskt alla Azure-prenumerationer för kunden som du har åtkomst till och visar dem i en listruta på sidan **skapa program** . Välj en Azure-prenumeration för att skapa ett nytt Azure IoT Central-program.

Om du inte har någon Azure-prenumeration kan du skapa en i Microsoft Partner Center. När du har skapat Azure-prenumerationen kan du gå tillbaka till sidan **Skapa program**. Din nya prenumeration visas i listrutan **Azure-prenumeration**.

Mer information finns i [Azure-prenumerationer](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Location

**Plats** är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa programmet. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. För närvarande kan du skapa ett IoT Central-program i regionerna **Australien**, **Asien och Stillahavsområdet**, **Europa**, **USA**, **Storbritannien** och **Japan** . När du har valt en plats kan du inte senare flytta programmet till en annan plats.

## <a name="application-template"></a>Programmall

Välj den program mall som du vill använda för ditt program.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett Azure IoT Central-program som en CSP, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)