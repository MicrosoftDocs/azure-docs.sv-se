---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95559512"
---
## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här självstudien använder du tillägget Azure IoT-verktyg för att skapa en modul och skapa en **behållar avbildning** från filerna. Sedan pushar du avbildningen till ett **register** som lagrar och hanterar dina avbildningar. Slutligen, distribuerar du din avbildning från ditt register så det kör på din IoT Edge-enhet.

Du kan använda valfritt Docker-kompatibelt register för att lagra dina containeravbildningar. Två populära Docker-registertjänster är [Azure Container Registry](../articles/container-registry/index.yml) och [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). I den här kursen använder vi Azure Container Registry.

Om du inte redan har ett containerregister följer du dessa steg för att skapa ett nytt i Azure:

1. I [Azure Portal](https://portal.azure.com) väljer du **Skapa en resurs** > **Container** > **Containerregister**.

2. Skapa containerregistret genom att ange följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Prenumeration | Välj en prenumeration i listrutan. |
   | Resursgrupp | Vi rekommenderar att du använder samma resursgrupp för alla testresurser som du skapar i snabbstarterna och självstudierna om IoT Edge. Till exempel **IoTEdgeResources**. |
   | Registernamn | Ange ett unikt namn. |
   | Location | Välj en plats i närheten av dig. |
   | SKU | Välj **Grundläggande**. |

3. Välj **Skapa**.

4. När du har skapat behållar registret, bläddrar du till det och väljer **åtkomst nycklar** från menyn som finns under **Inställningar** i den vänstra rutan. 

5. Klicka här om du vill att administratörs användare ska kunna se **användar namn** och **lösen ord** för behållar registret.

6. Kopiera värdena för **inloggnings Server**, **användar namn** och **lösen ord** och spara dem på ett praktiskt ställe. Du använder dessa värden i den här självstudien för att ge åtkomst till behållar registret.

   ![Kopiera inloggnings Server, användar namn och lösen ord för container Registry](./media/iot-edge-create-container-registry/registry-access-key.png)