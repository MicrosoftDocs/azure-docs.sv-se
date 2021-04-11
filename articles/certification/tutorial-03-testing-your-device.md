---
title: Azure-certifierad enhets program – självstudier – testa din enhet
description: En steg-för-steg-guide för att testa enheten med AICS-tjänsten på Azure-certifierad enhets Portal
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 4cc9e37e95c6402bc535d818e994327e7d526047
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969756"
---
# <a name="tutorial-test-and-submit-your-device"></a>Självstudie: testa och skicka din enhet

Nästa viktiga fas i certifierings processen (även om den kan slutföras innan du lägger till din enhets information) måste du testa enheten. Via portalen använder du Azure IoT-AICS (Azure IoT Certificate service) för att demonstrera enhetens prestanda enligt våra certifierings krav. När du har klarat test fasen skickar du sedan din enhet för slutlig granskning och godkännande av Azure-certifierings teamet!

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Anslut enheten till IoT Hub med hjälp av enhets etablerings tjänsten (DPS)
> * Testa din enhet enligt dina valda certifierings program
> * Skicka in din enhet för granskning av Azures certifierings team

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara inloggad och ha ett projekt för enheten som skapats på [Azure-certifierad enhets Portal](https://certify.azure.com). Mer information finns i [självstudien](tutorial-01-creating-your-project.md).
- Valfritt Vi rekommenderar att du förbereder din enhet och kontrollerar deras prestanda manuellt enligt certifierings kraven. Detta beror på att om du vill testa igen med en annan enhets kod eller ett certifierings program måste du skapa ett nytt projekt.

## <a name="connecting-your-device-using-dps"></a>Ansluta din enhet med hjälp av DPS

Alla certifierade enheter krävs för att demonstrera möjligheten att ansluta till IoT Hub med hjälp av DPS. Följande steg beskriver hur du ansluter enheten för testning på portalen.

1. Starta test fasen genom att välja `Connect & test` länken på sidan projekt Sammanfattning:  

    ![Anslut och testa länk](./media/images/connect-and-test-link.png)

1. Beroende på vilka certifikat som har marker ATS ser du de nödvändiga testerna på sidan Anslut & test. Granska dessa för att se till att du är tillämplig för rätt certifierings program.  

    ![Anslut och testa sida](./media/images/connect-and-test.png)

1. Anslut enheten till IoT Hub med enhets etablerings tjänsten (DPS). DPS stöder anslutnings alternativ för symmetriska nycklar, X. 509-certifiering och en Trusted Platform Module (TPM). Detta krävs för alla certifieringar.

    - *Mer information om hur du ansluter din enhet till Azure IoT Hub med DPS finns i [Översikt över etablerings enheter](../iot-dps/about-iot-dps.md "Översikt över enhets etablerings tjänsten").*
    
1. Om du använder symmetriska nycklar uppmanas du att konfigurera DPS med det angivna DPS-ID-omfånget, enhets-ID, autentiseringsnyckel och DPS-slutpunkten. Annars uppmanas du att ange ett X. 509-certifikat eller en bekräftelse nyckel.

1. När du har konfigurerat din enhet med DPS bekräftar du anslutningen genom att klicka på `Connect` knappen längst ned på sidan. När anslutningen är klar kan du fortsätta till test fasen genom att klicka på `Next` knappen.  

    ![Anslut och testa ansluten](./media/images/connected.png)

## <a name="testing-your-device"></a>Testa din enhet

När du har anslutit enheten till AICS är du nu redo att köra de certifierings test som är speciella för det certifierings program som du använder för.

1. **För Azure-certifierad enhets certifiering**: i fliken Välj enhets kapacitet granskar du och väljer vilka tester som du vill köra på enheten.
1. **För IoT plug and Play-certifiering**: noggrant granska de parametrar som ska kontrol leras under testet som du har deklarerat i din enhets modell.
1. **För Edge-hanterad certifiering**: inga ytterligare steg krävs för att demonstrera anslutningen.
1. När du har slutfört de nödvändiga förberedelserna för det angivna certifierings programmet väljer `Next` du att fortsätta till test-fasen.
1. Välj `Run tests` på sidan för att börja köra AICS med din enhet.
1. När du har fått ett meddelande om att du har godkänt testerna väljer du `Finish` att återgå till sidan Sammanfattning.

![Testet lyckades](./media/images/test-pass.png)

7. Om du har ytterligare frågor eller behöver fel söknings hjälp med AICS kan du gå till vår fel söknings guide.

> [!NOTE]
> Även om du kommer att kunna slutföra certifierings processen online för IoT Plug and Play och Edge som hanteras utan att behöva skicka in enheten för manuell granskning, kan du kontaktas av en Azure-certifierad enhets grupp medlem för ytterligare enhets verifiering utöver vad som testas via vår Automation-tjänst.

## <a name="submitting-your-device-for-review"></a>Skicka enheten för granskning

När du har slutfört alla obligatoriska fält i avsnittet enhets information och klarat den automatiserade testningen i processen "Anslut & test" kan du nu meddela det Azure Certified Device-team som du är redo för certifierings granskning.

1. Välj `Submit for review` på sidan projekt Sammanfattning:  

    ![Granska och certifiera länk](./media/images/review-and-certify.png)

1. Bekräfta ditt bidrag i popup-fönstret. När en enhet har skickats blir all enhets information skrivskyddad tills redigering begärs. (Mer information finns i [så här redigerar du enhets informationen efter publiceringen](./how-to-edit-published-device.md).)  

    ![Starta dialog rutan för certifierings granskning](./media/images/start-certification-review.png)

1. När projektet har skickats visar sidan projekt Sammanfattning att projektet är `Under Certification Review` av Azure-certifierings teamet:  

    ![Under granska](./media/images/review-and-certify-under-review.png)

1. Inom 5-7 arbets dagar förväntar sig ett e-postsvar från Azure-certifierings gruppen till adressen som anges i företags profilen angående statusen för din enhets överföring.

    - Godkänd överföring  
        När projektet har granskats och godkänts får du ett e-postmeddelande. E-postmeddelandet innehåller en uppsättning filer, till exempel det Azure Certified Device-märket, användnings rikt linjerna för Badge och annan information om hur du kan använda meddelandet om att enheten är certifierad. Grattis!

    - Väntar på överföring  
        Om ditt projekt inte godkänns, kan du göra ändringar i projekt informationen och sedan skicka in enheten för certifiering när den är klar. Ett e-postmeddelande skickas med information om varför projektet inte har godkänts och vilka steg som krävs för att skicka om certifieringen.

## <a name="next-steps"></a>Nästa steg

Grattis! Enheten har nu klarat alla tester och har godkänts via programmet Azure Certified Device. Nu kan du publicera din enhet till vår Azure-certifierad enhets katalog, där kunder kan handla för dina produkter och lita på sina prestanda med Azure.
> [!div class="nextstepaction"]
> [Självstudie: publicera din enhet](tutorial-04-publishing-your-device.md)

