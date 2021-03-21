---
title: Konfigurera din Azure percept DK
description: Anslut ditt dev-paket till Azure och distribuera din första AI-modell
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 9567ec2458a01825568cb853728f71db10228ee3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608304"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Konfigurera Azure percept DK och distribuera din första AI-modell

Slutför installations miljön för Azure percept DK för att konfigurera ditt dev kit och distribuera din första AI-modell. När du har kontrollerat att ditt Azure-konto är kompatibelt med Azure percept kommer du att:

- Ansluta ditt dev-paket till ett Wi-Fi nätverk
- Konfigurera en SSH-inloggning för fjärråtkomst till ditt dev-paket
- Skapa en ny IoT Hub som ska användas med Azure percept
- Anslut ditt dev-paket till din IoT Hub och ditt Azure-konto

Om du får problem under den här processen kan du läsa mer i [installations fel söknings guiden](./how-to-troubleshoot-setup.md) för möjliga lösningar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure percept DK (dev-paket).
- En Windows-, Linux-eller OS X-baserad värddator med Wi-Fi funktion och en webbläsare.
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure-kontot måste ha rollen **ägare** eller **deltagare** i prenumerationen. Följ stegen nedan för att kontrol lera din roll i Azure-kontot. Mer information om roll definitioner för Azure finns i dokumentationen till [Azure Role-baserad åtkomst kontroll](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

    > [!CAUTION]
    > Om du har flera Azure-konton kan din webbläsare cachelagra autentiseringsuppgifter från ett annat konto. För att undvika förvirring rekommenderar vi att du stänger alla oanvända webb läsar fönster och loggar in på [Azure Portal](https://portal.azure.com/) innan du startar installations miljön. Mer information om hur du kan se till att du är inloggad med rätt konto finns i [fel söknings guiden](./how-to-troubleshoot-setup.md) .

### <a name="check-your-azure-account-role"></a>Kontrol lera din roll för Azure-konto

Följ dessa steg om du vill kontrol lera om ditt Azure-konto är "Owner" eller "bidrag" i prenumerationen:

1. Gå till [Azure Portal](https://portal.azure.com/) och logga in med samma Azure-konto som du tänker använda med Azure percept.

1. Klicka på **prenumerations** ikonen (den ser ut som en gul nyckel).

1. Välj din prenumeration i listan. Om din prenumeration inte visas kontrollerar du att du är inloggad med rätt Azure-konto. Följ [dessa steg](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)om du vill skapa en ny prenumeration.

1. Från menyn prenumeration väljer du **åtkomst kontroll (IAM)**.
1. Klicka på **Visa min åtkomst**.
1. Kontrol lera rollen:
    - Om din roll visas som **läsare** eller om du får ett meddelande om att du inte har behörighet att se roller måste du följa den nödvändiga processen i organisationen för att öka din konto roll.
    - Om din roll visas som **ägare** eller **deltagare**, kommer ditt konto att fungera med Azure-percept och du kan gå vidare med installations miljön.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Starta installations upplevelsen för Azure percept DK

1. Anslut värddatorn direkt till dev-paketets Wi-Fi åtkomst punkt. Som du ansluter till andra Wi-Fi nätverk, öppnar du nätverks-och Internet inställningar på datorn, klickar på följande nätverk och anger nätverks lösen ordet när du uppmanas till det:

    - **Nätverks namn**: beroende på operativ systemets version av dev kit, är namnet på Wi-Fi åtkomst punkten antingen **SCZ-xxxx** eller **APD-xxxx** (där "xxxx" är de sista fyra siffrorna i dev-paketets Mac-adress)
    - **Lösen ord**: hittar du på Välkomst kortet som medföljde dev kit

    > [!WARNING]
    > När du är ansluten till Azure percept DK Wi-Fi åtkomst punkten kommer värddatorn tillfälligt att förlora anslutningen till Internet. Aktiva video konferens anrop, webb direkt uppspelning eller andra nätverksbaserade upplevelser avbryts.

1. När du har anslutit till dev-paketets Wi-Fi åtkomst punkt startar värddatorn automatiskt installations miljön i ett nytt webbläsarfönster med **din. New. Device/** i adress fältet. Starta installations programmet genom att gå till om fliken inte öppnas automatiskt [http://10.1.1.1](http://10.1.1.1) . Kontrol lera att din webbläsare är inloggad med samma autentiseringsuppgifter för Azure-kontot som du tänker använda med Azure percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Välkomst sidan.":::

    > [!CAUTION]
    > Webb tjänsten för installations upplevelse stängs av efter 30 minuters icke-användning. Om detta händer startar du om dev kit för att undvika anslutnings problem med dev-paketets Wi-Fi åtkomst punkt.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Slutför installations upplevelsen för Azure percept DK

1. Klicka på **Nästa** på **välkomst** skärmen.

1. På sidan **nätverks anslutning** klickar du på **Anslut till ett nytt WiFi-nätverk**.

    Om du redan har anslutit ditt dev-paket till Wi-Fi nätverket klickar du på **hoppa över**.

1. Välj ditt Wi-Fi nätverk i listan över tillgängliga nätverk och klicka på **Anslut**. Ange Nätverks lösen ordet när du uppmanas till det.

1. När ditt dev-paket har anslutit till ditt eget nätverk, visar sidan IPv4-adressen som tilldelats ditt dev-paket. **Skriv ned IPv4-adressen som visas på sidan.** Du behöver IP-adressen när du ansluter till ditt dev-paket via SSH för fel sökning och enhets uppdateringar.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Kopiera IP-adress.":::

    > [!NOTE]
    > IP-adressen kan ändras vid varje enhets start.

1. Läs igenom licens avtalet, Välj **Jag har läst och samtycker till licens avtalet** (du måste rulla längst ned i avtalet) och klicka på **Nästa**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Godkänn licens avtal.":::

1. Ange ett namn och lösen ord för SSH-kontot och **Skriv ned inloggnings informationen för senare användning**.

    > [!NOTE]
    > SSH (Secure Shell) är ett nätverks protokoll som gör att du kan ansluta till dev-paketet via en fjärr anslutning via en värddator.

1. På nästa sida klickar du på **Konfigurera som en ny enhet** för att skapa en ny enhet på ditt Azure-konto.

1. Kopiera enhets koden genom att klicka på **Kopiera** . Klicka efteråt och klicka på **Logga in på Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopiera enhets kod.":::

1. En ny flik i webbläsaren öppnas med ett fönster som säger att **koden är inskriven**. Klistra in koden i fönstret och klicka på **Nästa**. Stäng inte fliken **Välkommen** med installations programmet.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Ange enhets kod.":::

1. Logga in på Azure-percept med de autentiseringsuppgifter för Azure-kontot som du kommer att använda med ditt dev-paket. Lämna fliken webbläsare öppen när du är klar.

    > [!CAUTION]
    > Webbläsaren kan automatiskt cachelagra andra autentiseringsuppgifter. Dubbel kontroll att du loggar in med rätt konto.

    När du har loggat in på Azure procent på enheten går du tillbaka till fliken **Välkommen** och fortsätter installationen.

1. När sidan **tilldela din enhet till din Azure-IoT Hub** visas på fliken **Välkommen** gör du något av följande:

    - Om du redan har ett IoT Hub som du vill använda med Azure-percept och det visas på den här sidan markerar du det och går till steg 15.
    - Om du inte har någon IoT Hub eller om du vill skapa en ny Azure-IoT Hub klickar du på **skapa en ny Azure-**.

    > [!IMPORTANT]
    > Om du har ett IoT Hub, men det inte visas i listan, kan du ha loggat in på Azure-percept med fel autentiseringsuppgifter. Mer information finns i [installations guiden för fel sökning](./how-to-troubleshoot-setup.md) .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Välj en IoT Hub.":::

1. Om du vill skapa en ny IoT Hub fyller du i följande fält:

    - Välj den Azure-prenumeration som du vill använda med Azure-percept.
    - Välj en befintlig resursgrupp. Om det inte finns någon sådan, klickar du på **Skapa ny** och följer anvisningarna.
    - Välj den Azure-region som är närmast din fysiska plats.
    - Ge din nya IoT Hub ett namn.
    - Välj pris nivån S1 (standard).

    > [!NOTE]
    > Om du behöver ett större [meddelande genom strömning](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) för dina Edge AI-program kan du när som helst [Uppgradera IoT Hub till en högre standard nivå](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) i Azure Portal. B-och F-nivåer har inte stöd för Azure-percept.

1. Det kan ta några minuter att IoT Hub distributionen. När distributionen är klar klickar du på **Registrera**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub har distribuerats.":::

1. Ange ett enhets namn för ditt dev-paket och klicka på **Nästa**.

1. Vänta tills enhetens moduler har laddats ned – det tar några minuter.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Installationen slutförs.":::

1. När du ser att **enhets konfigurationen är klar!** -Sidan har ditt dev-paket länk ATS till din IoT Hub och hämtat nödvändig program vara. Ditt dev-paket kopplas från automatiskt från Wi-Fi åtkomst punkten, vilket resulterar i följande två meddelanden:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Varning vid från koppling av installations miljö.":::

1. Anslut värddatorn till det Wi-Fi nätverk som devkit är anslutet till i steg 2.

1. Klicka på **Fortsätt till Azure Portal**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Gå till Azure percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Visa video strömmen för dev kit och distribuera en exempel modell

1. [Översikts sidan för Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) är din start punkt för att komma åt många olika arbets flöden för både inledande och avancerad utveckling av AI-lösningar. Kom igång genom att klicka på **enheter** i den vänstra menyn.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Visa din lista över enheter.":::

1. Kontrol lera att ditt dev-paket visas som **anslutet** och klicka på det för att Visa enhets sidan.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Välj din enhet.":::

1. Klicka på **Visa enhets strömmen**. Om det här är första gången du visar video strömmen på enheten, visas ett meddelande om att en ny modell har distribuerats i det övre högra hörnet. Det kan ta några minuter.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Visa video strömmen.":::

    När modellen har distribuerats får du ett annat meddelande med en länk till **visnings strömmar** . Klicka på länken om du vill visa video strömmen från din Azure percept Vision-kamera i ett nytt webbläsarfönster. Dev kit är förinstallerat med en AI-modell som automatiskt utför objekt identifiering av många vanliga objekt.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Se objekt identifiering.":::

1. Azure percept Studio har också ett antal exempel på AI-modeller. Om du vill distribuera en exempel modell till ditt dev-paket går du tillbaka till sidan enheter och klickar på **distribuera en exempel modell**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Utforska färdiga modeller.":::

1. Välj en exempel modell i biblioteket och klicka på **distribuera till enhet**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Se objekt identifiering i åtgärd.":::

1. När modellen har distribuerats visas ett meddelande med en länk till **visnings strömmar** i det övre högra hörnet på skärmen. Om du vill visa modellens inferencing i praktiken klickar du på länken i meddelandet eller går tillbaka till enhets sidan och klickar på **Visa enhets strömmen**. Alla modeller som tidigare kördes i dev-paketet kommer nu att ersättas med den nya modellen.

## <a name="video-walkthrough"></a>Videogenomgång

En visuell genom gång av stegen som beskrivs ovan finns i följande video (installations upplevelsen börjar vid 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en lösning utan kod](./tutorial-nocode-vision.md)