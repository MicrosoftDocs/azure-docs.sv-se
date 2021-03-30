---
title: Självstudie – konfigurera molnet för Azure IoT Hub Device Provisioning Service i portalen
description: Den här självstudien visar hur du kan konfigurera moln resurser för enhets etablering i [Azure Portal](https://portal.azure.com) med hjälp av IoT Hub Device PROVISIONING service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316077"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Självstudie: Konfigurera moln resurser för enhets etablering med IoT Hub Device Provisioning Service

Den här självstudien visar hur du konfigurerar molnet för en automatisk enhetsetablering med IoT Hub Device Provisioning-tjänsten. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
> * Skapa en IoT Hub
> * Länka IoT-hubben till Device Provisioning-tjänsten
> * Ange allokeringsprincip för enhetsetableringstjänsten

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Skapa en Device Provisioning-tjänstinstans och hämta ID-omfång

Följ de här stegen för att skapa en ny Device Provisioning-tjänstinstans.

1. Klicka på **skapa en resurs** i det övre vänstra hörnet av Azure Portal.

2. I sökrutan skriver du **enhetsetablering**. 

3. Klicka på **IoT Hub Device Provisioning-tjänst**.

4. Fyll i följande information i formuläret **IoT Hub Device Provisioning-tjänst**:
    
   | Inställning       | Föreslaget värde | Beskrivning | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Namn** | Ett unikt namn | -- | 
   | **Prenumeration** | Din prenumeration  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
   | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |   

   ![Ange grundläggande information om enhetsetableringstjänsten på portalen](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Klicka på **Skapa**. Efter en liten stund skapas instansen för enhetsetableringstjänsten, och sidan **Översikt** visas.

6. På sidan **Översikt** för den nya tjänstinstansen kopierar du värdet för **ID-omfånget** för senare användning. Det värdet används för att identifiera registrerings-ID och erbjuder en garanti för att registrerings-ID:t är unikt.

7. Kopiera även **Tjänstslutpunkten** värde för senare användning. 

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Nu har du skapat din IoT Hub och du har värdnamnet och IoT Hub-anslutningssträngen som du behöver för att slutföra resten av den här kursen.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Länka Device Provisioning-tjänsten till en IoT-hubb

Nästa steg är att länka Device Provisioning-tjänsten och IoT Hub så att IoT Hub Device Provisioning-tjänsten kan registrera enheter till den hubben. Tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till Device Provisioning-tjänsten. Gör så här:

1. På sidan **Alla resurser** klickar du på Device Provisioning-tjänsteinstansen du skapade tidigare.

2. Välj **Linked IoT hubs** (Länkade IoT-hubbar) på Device Provisioning-tjänstens sida.

3. Klicka på **Lägg till**.

4. På sidan **Lägg till länk i IoT Hub** anger du följande information och klickar på **Spara**:

    * **Prenumeration:** Se till att den prenumeration som innehåller IoT-hubben är markerad. Du kan länka till en IoT-hubb som finns i en annan prenumeration.

    * **IoT-hubb:** Välj namnet på den IoT-hubb som du vill länka med den här instansen för enhetsetableringstjänsten.

    * **Åtkomstprincip:** Välj **iothubowner** som autentiseringsuppgifter som ska användas för etablering av länken till IoT-hubben.

   ![Länka hubbnamnet för att länka till enhetsetableringstjänsten på portalen](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Ange allokeringsprincip för enhetsetableringstjänsten

Allokeringsregeln är en IoT Hub Device Provisioning Service inställning som avgör hur enheter tilldelas till en IoT-hubb. Det finns tre allokeringsprinciper som stöds: 

1. **Kortast svarstid**: Enheter etableras till en IoT-hubb baserat på hubben med kortast svarstid till enheten.

2. **Jämnt viktad distribution** (standard): Det är lika sannolikt att länkade IoT-hubbar får enheter etablerade till sig. Den här inställningen är standardinställningen. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen. 

3. **Statisk konfiguration via registreringslistan**: Specificering av den önskade IoT-hubben på registreringslistan har högre prioritet än allokeringsprincipen på Device Provisioning-tjänstnivå.

Om du vill ställa in allokeringsprincipen går du till Device Provisioning-tjänstsidan och klickar på **Hantera allokeringsprincip**. Kontrollera att allokeringsprincipen är inställd på **Jämnt viktad distribution** (standardinställningen). Om du gör några ändringar ska du klicka på **Spara** när du är klar.

![Hantera allokeringsprincip](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Rensa resurser

Andra självstudier i den här samlingen bygger på den här självstudien. Om du tänker fortsätta med efterföljande snabbstarter eller självstudier ska du inte rensa resurserna du har skapat i den här självstudien. Om du inte planerar att fortsätta följer du stegen nedan för att ta bort alla resurser som du har skapat i den här självstudien på Azure-portalen.

1. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT Hub Device Provisioning-tjänstinstans. Klicka på **Ta bort** överst på sidan **Alla resurser**.  

2. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Klicka på **Ta bort** överst på sidan **Alla resurser**.
 
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
> * Skapa en IoT Hub
> * Länka IoT-hubben till Device Provisioning-tjänsten
> * Ange allokeringsprincip för enhetsetableringstjänsten

Gå vidare till nästa självstudie för att lära dig att konfigurera din enhet för etablering.

> [!div class="nextstepaction"]
> [Konfigurera enhet för etablering](tutorial-set-up-device.md)
