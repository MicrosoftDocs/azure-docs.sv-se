---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803211"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifiera IoT Edge CI/CD med pipelinen build och release

Om du vill utlösa ett build-jobb kan du antingen skicka ett genomförande till käll kods databasen eller aktivera det manuellt. I det här avsnittet utlöser du CI/CD-pipeline manuellt för att testa att den fungerar. Kontrol lera sedan att distributionen lyckades.

1. I menyn till vänster klickar du på **pipelines** och öppnar build-pipeline som du skapade i början av den här artikeln.

2. Du kan utlösa ett build-jobb i din build-pipeline genom att välja knappen **Kör pipeline** överst till höger.

    ![Utlösa din versions pipeline manuellt med knappen Kör pipeline](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Granska inställningarna för att **köra pipelinen** . Välj sedan **Kör**.

    ![Ange alternativ för att köra pipeliner och välj Kör](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Välj **Agent jobb 1** om du vill se körnings förloppet. Du kan granska loggarna för jobbets utdata genom att välja jobbet. 

    ![Granska jobbets logg utdata](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Om build-pipeline har slutförts utlöses en version till **dev** -fasen. Den framgångs rik **dev** -versionen skapar IoT Edge distribution till mål IoT Edge enheter.

    ![Släpp till dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Klicka på **dev** Stage för att se versions loggarna.

    ![Lanseringsloggar](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. Om din pipeline inte fungerar börjar du med att titta på loggarna. Du kan visa loggar genom att gå till pipeline-körningen och välja jobb och aktivitet. Om en viss aktivitet inte kan utföras, kontrol lera loggarna för aktiviteten. Detaljerade anvisningar för att konfigurera och använda loggar finns i [Granska loggar för att diagnostisera problem med pipelinen](/azure/devops/pipelines/troubleshooting/review-logs).
