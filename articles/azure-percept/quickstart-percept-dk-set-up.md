---
title: Konfigurera din Azure percept DK
description: Anslut ditt dev-paket till Azure och distribuera din första AI-modell
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 77ccf09903b6cff83b52d60d43d0c52533c9c1ad
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041696"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Konfigurera Azure percept DK och distribuera din första AI-modell

Kom igång med Azure percept DK och Azure percept Studio genom att använda Azure percept DK-installationsprogrammet för att ansluta din enhet till Azure och distribuera din första AI-modell. När du har verifierat att ditt Azure-konto är kompatibelt med Azure percept Studio slutför du installations miljön för att se till att din Azure percept DK är konfigurerad för att skapa Edge AI-bevis för koncept.

Om det uppstår problem under Snabbstart kan du läsa [fel söknings](./troubleshoot-dev-kit.md) guiden för möjliga lösningar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure percept DK.
- En Windows-, Linux-eller OS X-baserad värd dator med Wi-Fi-funktion och en webbläsare.
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure-kontot måste ha rollen ägare eller deltagar för prenumerationen. Läs mer om roll definitioner för Azure

### <a name="prerequisite-check"></a>Krav kontroll

Följ dessa steg för att kontrol lera om ditt Azure-konto är "ägare" eller "deltagare" i prenumerationen.

1. Gå till Azure Portal och logga in med samma Azure-konto som du tänker använda med Azure percept Studio. 

    > [!NOTE]
    > Om du har flera Azure-konton kan din webbläsare cachelagra autentiseringsuppgifter från ett annat konto. I fel söknings guiden finns mer information om hur du ser till att du är inloggad med rätt konto.

1. Expandera huvud menyn från det övre vänstra hörnet på skärmen och klicka på prenumerationer eller Välj prenumerationer på Meny ikonen på Start sidan. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. Välj din prenumeration i listan. Om din prenumeration inte visas i listan kontrollerar du att du är inloggad med rätt Azure-konto. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Följ [dessa steg](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)om du vill skapa en ny prenumeration.

1. Välj "åtkomst kontroll (IAM)" på menyn prenumeration
1. Klicka på knappen "Visa min åtkomst"
1. Kontrol lera rollen
    - Om du ser rollen som "läsare" eller om du får ett meddelande om att du inte har behörighet att se roller måste du följa den nödvändiga processen i organisationen för att få din konto roll utökad.
    - Om den visar rollen som "ägare" eller "deltagare", kommer ditt konto att fungera med Azure percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Starta installations upplevelsen för Azure percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Anslut värddatorn direkt till dev-paketets Wi-Fi-åtkomst punkt. Detta görs precis som att ansluta till ett annat Wi-Fi-nätverk
    - **nätverks namn**: SCZ-xxxx (där "xxxx" är de sista fyra siffrorna i dev-paketets Mac-nätverks adress)
    - **lösen ord**: hittar du på Välkomst kortet som medföljde dev kit

    > [!WARNING]
    > När du är ansluten till Azure percept DK Wi-Fi-åtkomst punkten kommer värddatorn tillfälligt att förlora anslutningen till Internet. Aktiva video konferens anrop, webdirektuppspelning eller andra nätverksbaserade upplevelser avbryts tills steg 3 i installations upplevelsen för Azure percept DK är klar.

1. När den är ansluten till dev-paketets Wi-Fi-åtkomstkontroll startar värd enheten automatiskt Azure percept DK-installationen i ett nytt webbläsarfönster. Om den inte öppnas automatiskt kan du starta den manuellt genom att öppna ett webbläsarfönster och navigera till [http://10.1.1.1](http://10.1.1.1) . 

1. Nu när du har lanserat installations programmet för Azure-percept är du redo att gå vidare med installations miljön. 

    > [!NOTE]
    > Webb tjänsten Azure percept DK setup Experience stängs av efter 30 minuters icke-användning och slutförs vid installationen. När detta inträffar rekommenderar vi att du startar om dev-paketet för att undvika anslutnings problem med den trådlösa åtkomst punkten för dev-paketet.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Slutför installations upplevelsen för Azure percept DK

1. Kom igång – Klicka på **Nästa** på Välkomst skärmen.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Välkomst sidan.":::

1. Anslut till Wi-Fi – på sidan Nätverks anslutning klickar du på **Anslut till ett nytt WiFi-nätverk** för att ansluta dina devkit till ditt Wi-Fi-nätverk.

    Om du tidigare har anslutit till det här dev-paketet till ditt Wi-Fi-nätverk eller om du redan är ansluten till installations upplevelsen för Azure percept DK via ditt Wi-Fi-nätverk klickar du på länken **hoppa över** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Anslut till Wi-Fi.":::

1. Välj ditt Wi-Fi-nätverk från de tillgängliga anslutningarna och Anslut. (Kräver ditt lokala Wi-Fi-lösenord)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Välj Wi-Fi-nätverk."::: 

1. Kopiera din IP-adress – när din devkit har lyckats ansluta till ditt eget nätverk skriver du ned **IPv4-adressen** som visas på sidan. **Du kommer att behöva den här IP-adressen senare i den här snabb starts guiden**. 

    > [!NOTE]
    > IP-adressen kan ändras vid varje enhets start.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Kopiera IP-adress.":::

1. Granska och godkänn licens avtalet – Läs igenom licens avtalet, Välj **Jag har läst och samtycker till licens avtalet** (måste rulla längst ned i avtalet) och klicka på **Nästa**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Godkänn licens avtal.":::

1. Skapa ditt SSH-inloggningsnamn – Konfigurera SSH för fjärråtkomst till din devkit. Skapa ett SSH-användarnamn och-lösen ord. 

    > [!NOTE]
    > SSH (Secure Shell) är ett nätverks protokoll som används för säker kommunikation mellan värd enheten och dev-paketet. Mer information om SSH finns i [den här artikeln](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Skapa SSH-konto."::: 

1. Påbörja processen för installation av dev kit – på nästa skärm klickar du på **Anslut med en ny enhet** för att påbörja processen med att ansluta ditt dev-paket till Azure IoT Hub. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Anslut till Azure."::: 

1. Kopiera enhets koden – Klicka på **Kopiera** länken för att kopiera enhets koden. Klicka sedan på **Logga in på Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Kopiera enhets kod."::: 

1. Klistra in enhets koden – en ny webbläsare-flik öppnas med ett fönster som frågar efter den kopierade enhets koden. Klistra in koden i fönstret och klicka på **Nästa**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Ange enhets kod."::: 

1. Logga in på Azure – nästa fönster kräver att du loggar in med det Azure-konto som du verifierade i början av Snabbstart. Ange inloggnings uppgifterna och klicka på **Nästa**. 

    > [!NOTE]
    > Webbläsaren kan automatiskt cachelagra andra autentiseringsuppgifter. Dubbel kontroll att du loggar in med rätt konto.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Logga in på Azure.":::
 
1. Stäng inte fliken webbläsare för installations miljö i det här steget – när du har loggat in visas en skärm som visar att du har loggat in. Även om det betyder att du har många Stäng fönstret, **rekommenderar vi att du inte stänger några fönster**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Inloggningen lyckades."::: 

1. Gå tillbaka till webbläsare-fliken som är värd för installations upplevelsen.
1. Välj IoT Hub alternativ
    - Om du redan har en IoT Hub och den visas på den här sidan kan du välja den och **gå till steg 17**.
    - Om du inte har någon IoT Hub eller om du vill skapa en ny, går du till slutet av listan och klickar på **skapa en ny Azure-IoT Hub**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Välj en IoT Hub."::: 

1. Skapa din nya IoT Hub – Fyll i alla fält på den här sidan. 
    - Välj den Azure-prenumeration som du vill använda med Azure percept Studio
    - Välj en befintlig resursgrupp. Om det inte finns någon sådan klickar du på "Skapa ny" och följer anvisningarna. 
    - Välj Azure-region. 
    - Ge din nya IoT Hub ett namn
    - Välj pris nivå (den matchar vanligt vis prenumerationen)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Skapa en ny IoT Hub."::: 

1. Vänta tills IoT Hub har distribuerats – det kan ta några minuter

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Distribuera IoT Hub."::: 

1. Registrera ditt dev-paket – när distributionen är klar klickar du på knappen **Registrera**

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub har distribuerats."::: 

1. Namnge ditt dev-paket – ange ett enhets namn för ditt dev-paket och klicka på **Nästa**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Namnge enheten."::: 

1. Vänta tills standard-AI-modellerna laddas ned – det tar några minuter

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Installationen slutförs."::: 

1. Se vision AI i praktiken – din devkit har kopplats till din Azure-IoT Hub och den har tagit emot en standard identifierings modell för AI-objekt. Azure percept Vision-kameran kan nu göra objekt identifiering inferencing utan anslutning till molnet. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Klicka på Förhandsgranska video utdata."::: 
       
    > [!NOTE]
    > Onboarding-processen och anslutningen till enhetens wifi-åtkomst till värddatorn stängs av nu i det här läget, men ditt dev-paket förblir anslutet till Internet.   Du kan starta om onboarding-upplevelsen med en omstart av dev kit, vilket gör att du kan gå tillbaka genom onboarding och återansluta enheten till en annan IOT-hubb som är kopplad till samma eller en annan Azure-prenumeration..

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Varning vid från koppling av installations miljö."::: 

1. Fortsätt till Azure Portal – gå tillbaka till installations upplevelse fönstret och klicka på knappen **Fortsätt till Azure Portal** för att börja skapa dina anpassade AI-modeller i Azure percept Studio.

    > [!NOTE]
    > Kontrol lera att värddatorn inte längre är ansluten till dev kit-åtkomst punkten i dina WiFi-inställningar och återanslut nu till din lokala WiFi.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Gå till Azure percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Visa din enhet i Azure percept Studio och distribuera vanliga färdiga exempel appar

1. Visa din lista över enheter från översikts sidan för Azure-percept. Översikts sidan för Azure percept är din start punkt för att komma åt många olika arbets flöden för både start och avancerad AI Edge-modell och utveckling av lösningar

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Visa din lista över enheter.":::
    
1. Kontrol lera att enheten du nyss skapade är ansluten och klicka på den.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Välj din enhet.":::

1. Visa enhets strömmen för att se vad din dev kit-kamera ser. En modell för standard objekt identifiering har distribuerats ut ur rutan och identifierar ett antal gemensamma objekt.

    > [!NOTE]
    > första gången du gör detta på en ny enhet får du ett meddelande om att en ny modul distribueras i det övre högra hörnet.  När detta har konkurrerat kommer du att kunna starta fönstret med kamera video strömmen. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Visa video strömmen.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Se objekt identifiering.":::

1. Utforska fördefinierade exempel på AI-modeller.   Azure Precept Studio har ett antal vanliga exempel som kan distribueras med ett enda klick.   Välj "distribuera en exempel modell" för att visa och distribuera dessa.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Utforska färdiga modeller.":::
    
1. Distribuera ett nytt fördefinierat exempel till din anslutna enhet. Välj ett exempel från biblioteket och klicka på "distribuera till enhet"

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Se objekt identifiering i åtgärd.":::

## <a name="next-steps"></a>Nästa steg

Du kan följa ett liknande flöde för att testa [fördefinierade tal modeller](./tutorial-no-code-speech.md).