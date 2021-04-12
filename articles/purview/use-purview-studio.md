---
title: Använda Azure avdelningens kontroll Studio
description: Den här artikeln beskriver hur du använder Azure avdelningens kontroll Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283877"
---
# <a name="use-purview-studio"></a>Använda Purview Studio

Den här artikeln ger en översikt över några av de viktigaste funktionerna i Azure dataavdelningens kontrolls.

## <a name="prerequisites"></a>Förutsättningar

* Ett aktivt avdelningens kontroll-konto har redan skapats i Azure Portal och användaren har åtkomst behörighet till avdelningens kontroll Studio.

## <a name="launch-purview-account"></a>Starta avdelningens kontroll-konto

* Om du vill starta ditt avdelningens kontroll-konto går du till avdelningens kontroll-konton i Azure Portal väljer du det konto som du vill starta och starta kontot.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Skärm bild av valet för att starta Azure avdelningens kontroll-konto katalogen.":::

* Ett annat sätt att starta avdelningens kontroll-kontot är att gå till `https://web.purview.azure.com` , välja **Azure Active Directory** och ett konto namn för att starta kontot.

## <a name="home-page"></a>Startsida

**Start** sidan är start sidan för Azure avdelningens kontroll-klienten.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Skärm bild av start sidan.":::

I följande lista sammanfattas huvud funktionerna på **Start sidan**. Varje nummer i listan motsvarar ett markerat tal i föregående skärm bild.

1. Katalogens eget namn. Du kan ange katalog namn i konto information för **hanterings Center**  >  .

2. Katalog analys visar antalet:

   * Användare, grupper och program
   * Datakällor
   * Tillgångar
   * Ordlistetermer

3. Med sökrutan kan du söka efter data till gångar i data katalogen.

4. Knapparna för snabb åtkomst ger åtkomst till ofta använda funktioner i programmet. De knappar som visas beror på vilken roll som tilldelats ditt användar konto.

   * För *data curator* är knapparna **kunskaps Center**, **Bläddra till gångar**, **Hantera ord lista** och **Visa insikter**.
   * För *data läsare* är de aktuella knapparna **kunskaps Center**, **Bläddra till gångar**, **Visa ord lista** och **Visa insikter**.
   * För *data källans administratörs*  +  *data curator* är de aktuella knapparna **kunskaps Center**, **registrera data källor**, **Bläddra till gångar** och **Hantera ord lista**.
   * För *data källans administratörs*  +  *data läsare* är de aktuella knapparna **kunskaps Center**, **registrera data källor**, **Bläddra till till gångar** och **Visa ord lista**.

5. I det vänstra navigerings fältet kan du hitta programmets huvud sidor. De knappar som visas beror på vilken roll som tilldelats ditt användar konto.

   * För *data curator* är knapparna **Home**, **ordbok**, **insikter** och **Management Center**.
   * För *data läsare* är knapparna **Home**, **ordbok**, **insikter** och **Management Center**.
   * För *data källans administratör* eller *data curator/Reader* är knapparna **Start**, **källor**, **ord lista**, **insikter** och **hanterings Center**.
  
6. Fliken **senast använda** visar en lista över nyligen använda data till gångar. Information om hur du kommer åt till gångar finns i [sök Data Catalog](how-to-search-catalog.md) och [Bläddra efter typ av till gång](how-to-browse-catalog.md#browse-experience).  Fliken **Mina objekt** är en lista över data till gångar som ägs av den inloggade användaren.
7. **Användbara länkar** innehåller länkar till region status, dokumentation, prissättning, översikt och avdelningens kontroll status
8. Det övre navigerings fältet innehåller information om versions anmärkningar/uppdateringar, ändra avdelningens kontroll-konto, meddelanden, hjälp och feedback-avsnitt.

## <a name="knowledge-center"></a>Kunskaps Center

Kunskaps Center är där du hittar alla videor och självstudier som är relaterade till avdelningens kontroll.

## <a name="guided-tours"></a>Guidade visningar

Varje UX i Azure avdelningens kontroll Studio har guidade visningar för att ge en översikt över sidan. Starta guidad visning genom att välja **Hjälp** i det översta fältet och välja **guidade visningar**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Skärm bild av guidad visning.":::

> [!Important]
> Rollen som *administratör för data källa* saknar till gång till avdelningens kontroll Studio.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till ett säkerhets objekt](tutorial-scan-data.md)
