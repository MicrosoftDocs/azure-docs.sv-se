---
title: Använda Purview Studio
description: Den här konceptuella artikeln beskriver hur du använder Azure avdelningens kontroll Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d8e6c4b2addf9745b2ddabe8f6fdad9d82dce59f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503958"
---
# <a name="use-purview-studio"></a>Använda Purview Studio

Den här artikeln ger en översikt över några av de viktigaste funktionerna i Azure dataavdelningens kontrolls.

## <a name="prerequisites"></a>Krav

* Ett aktivt avdelningens kontroll-konto har redan skapats i Azure Portal och användaren har åtkomst behörighet till avdelningens kontroll Studio.

## <a name="launch-purview-account"></a>Starta avdelningens kontroll-konto

* Om du vill starta ditt avdelningens kontroll-konto går du till avdelningens kontroll-konton i Azure Portal väljer du det konto som du vill starta och starta kontot.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Skärm bild av valet för att starta Azure avdelningens kontroll-konto katalogen.":::

* Ett annat sätt att starta avdelningens kontroll-kontot är att gå till `https://web.purview.azure.com` , välja **Azure Active Directory** och ett konto namn för att starta kontot.

## <a name="home-page"></a>Startsida

**Start** sidan är start sidan för Azure avdelningens kontroll-klienten.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Skärm bild av start sidan.":::

I följande lista sammanfattas huvud funktionerna på **Start sidan**. Varje nummer i listan motsvarar ett markerat tal i föregående skärm bild.

1. Katalogens eget namn. Du kan ange katalog namn i **hanterings Center** > **konto information*.

2. Katalog analys visar antalet:
    - Användare, grupper och program
    - Datakällor
    - Tillgångar
    - Ordlistetermer

3. Med sökrutan kan du söka efter data till gångar i data katalogen.

4. Knapparna för snabb åtkomst ger åtkomst till ofta använda funktioner i programmet. De knappar som visas beror på vilken roll som tilldelats ditt användar konto.

    - För *data curator* är knapparna **kunskaps Center**, **Bläddra till gångar**, **Hantera ord lista** och **Visa insikter**.
    - För *data läsare* är de aktuella knapparna **kunskaps Center**, **Bläddra till gångar**, **Visa ord lista** och **Visa insikter**.
    - För *data källans administratörs*  +  *data curator* är de aktuella knapparna **kunskaps Center**, **registrera data källor**, **Bläddra till gångar** och **Hantera ord lista**.
    - För *data källans administratörs*  +  *data läsare* är de aktuella knapparna **kunskaps Center**, **registrera data källor**, **Bläddra till till gångar** och **Visa ord lista**.

5. I det vänstra navigerings fältet kan du hitta programmets huvud sidor. De knappar som visas beror på vilken roll som tilldelats ditt användar konto.

    - För *data curator* är knapparna **Home**, **ordbok**, **insikter** och **Management Center**.
    - För *data läsare* är knapparna **Home**, **ordbok**, **insikter** och **Management Center**.
    - För *data källans administratörs*  +  *data curator/Reader* är knapparna **Home**, **SOURCES**, **ordbok**, **Insights** och **Management Center**.
  
6. Fliken **senast använda** visar en lista över nyligen använda data till gångar. Information om hur du kommer åt till gångar finns i [sök Data Catalog](how-to-search-catalog.md) och [Bläddra efter typ av till gång](how-to-browse-catalog.md#browse-experience).  Fliken **Mina objekt** är en lista över data till gångar som ägs av den inloggade användaren.
7. **Användbara länkar** innehåller länkar till region status, dokumentation, prissättning, översikt och avdelningens kontroll status
8. Det övre navigerings fältet innehåller information om versions anmärkningar/uppdateringar, ändra avdelningens kontroll-konto, meddelanden, hjälp och feedback-avsnitt.

## <a name="knowledge-center"></a>Kunskaps Center

Kunskaps Center är där du hittar alla videor och självstudier som är relaterade till avdelningens kontroll.

## <a name="guided-tours"></a>Guidade visningar

Varje UX i Azure avdelningens kontroll Studio har guidade visningar för att ge en översikt över sidan. Starta guidad visning genom att välja **Hjälp** i det översta fältet och välja **guidade visningar**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Skärm bild av guidad visning.":::

> [!Important]
   > Rollen som administratör för data källa har inte till gång till avdelningens kontroll Studio.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till ett säkerhets objekt](tutorial-scan-data.md)