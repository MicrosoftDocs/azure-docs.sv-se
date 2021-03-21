---
title: Konfigurera push-meddelanden i Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du konfigurerar Azure-Notification Hubs i Azure Portal genom att använda PNS-inställningar (Platform notification system).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94887271"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Snabb start: Konfigurera push-meddelanden i en Notification Hub

Azure Notification Hubs tillhandahåller en push-motor som är lätt att använda och som skalar ut. Använd Notification Hubs för att skicka meddelanden till valfri plattform (iOS, Android, Windows, Baidu) och från valfri Server del (moln eller lokalt). Mer information finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabb starten använder du PNS-inställningarna (Platform notification system) i Notification Hubs för att konfigurera push-meddelanden på flera plattformar. Snabb starten visar vilka steg du ska vidta i Azure Portal. [Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) innehåller instruktioner för hur du använder Azure CLI.

Om du inte redan har skapat en Notification Hub skapar du ett nu. Mer information finns i [skapa en Azure Notification Hub i Azure Portal](create-notification-hub-portal.md) eller [skapa en Azure Notification Hub med Azure CLI](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Konfigurera Apple Push Notification Service (APN):

1. I Azure Portal på sidan **Notification Hub** väljer du **Apple (APNs)** på den vänstra menyn.

1. För **autentiseringsläge** väljer du antingen **certifikat** eller **token**.

   a. Om du väljer **certifikat**:
   * Välj fil ikonen och välj sedan den *. p12* -fil som du vill ladda upp.
   * Ange ett lösen ord.
   * Välj **Sandbox**-läge. Eller, om du vill skicka push-meddelanden till användare som har köpt din app från Store, väljer du **produktions** läge.

     ![Skärm bild av en konfiguration av APN-certifikat i Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Om du väljer **token**:

   * Ange värdena för **nyckel-ID**, **paket-ID**, **Team-ID** och **token**.
   * Välj **Sandbox**-läge. Eller, om du vill skicka push-meddelanden till användare som har köpt din app från Store, väljer du **produktions** läge.

     ![Skärm bild av en konfiguration av APN-token i Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Mer information finns i [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konfigurera push-meddelanden för Google FCM:

1. I Azure Portal på sidan **Notification Hub** väljer du **Google (GCM/FCM)** på den vänstra menyn.
2. Klistra in **API-nyckeln** för det Google FCM-projekt som du sparade tidigare.
3. Välj **Spara**.

   ![Skärm bild som visar hur du konfigurerar Notification Hubs för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

När du slutför de här stegen anger en varning att Notification Hub har uppdaterats. Knappen **Spara** är inaktiverad.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kommer att behöva **API-nyckeln** för ditt Google Firebase Cloud Messaging-projekt (FCM).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Den här artikeln kräver version 2.0.67 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurera push-meddelanden för Google FCM

1. Använd kommandot [AZ Notification – Hub Credential GCM Update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) för att lägga till din Google API-nyckel i Notification Hub.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android-appen behöver en anslutnings sträng för att ansluta till Notification Hub.  Använd kommandot [AZ Notification-Hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) för att visa en lista över tillgängliga åtkomst principer.  Använd kommandot [AZ Notification-Hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) för att hämta anslutnings strängarna för åtkomst principen.  Ange **primaryConnectionString** eller **secondaryConnectionString** i `--query` parametern för att hämta den primära anslutnings strängen direkt.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Använd kommandot [AZ Notification-Hub test-Send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) för att testa att skicka meddelanden till Android-appen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Hämta Azure CLI-referenser för andra plattformar med kommandot [AZ Notification – Hub Credential](/cli/azure/ext/notification-hub/notification-hub/credential) .

Mer information om hur du skickar meddelanden till ett Android-program finns i [skicka push-meddelanden till Android-enheter med Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Konfigurera Windows Push Notification Service (WNS):

1. I Azure Portal på sidan **Notification Hub** väljer du **Windows (WNS)** på den vänstra menyn.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärm bild som visar rutorna paket-SID och säkerhets nyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Mer information finns i [skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service för Windows Phone

Så här konfigurerar du Microsoft Push Notification Service (MPNS) för Windows Phone:

1. I Azure Portal på sidan **Notification Hub** väljer du **Windows Phone (MPNS)** på den vänstra menyn.
1. Aktivera antingen oautentiserade eller autentiserade push-meddelanden:

   a. Om du vill aktivera oautentiserade push-meddelanden väljer du **Aktivera oautentiserad push**  >  **Spara**.

      ![Skärm bild som visar hur du aktiverar oautentiserade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Så här aktiverar du autentiserade push-meddelanden:
      * I verktygsfältet väljer du **Ladda upp certifikat**.
      * Välj fil ikonen och välj sedan certifikat filen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * På sidan **Windows Phone (MPNS)** väljer du **Spara**.

Mer information finns i [push-meddelanden till Windows Phone appar med hjälp av Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Konfigurera push-meddelanden för Baidu:

1. I Azure Portal på sidan **Notification Hub** väljer du **Baidu (Android China)** på den vänstra menyn.
2. Ange den **API-nyckel** som du fick från Baidu-konsolen i Baidu Cloud push projektet.
3. Ange den **hemliga nyckeln** som du fick från Baidu-konsolen i Baidu Cloud push projektet.
4. Välj **Spara**.

    ![Skärm bild av Notification Hubs som visar Baidu-konfigurationen (Android China) för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

När du slutför de här stegen anger en varning att Notification Hub har uppdaterats. Knappen **Spara** är inaktiverad.

Mer information finns i [Kom igång med Notification Hubs med hjälp av Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du konfigurerar inställningar för plattforms aviserings systemet för en Notification Hub i Azure Portal.

Mer information om hur du skickar meddelanden till olika plattformar finns i följande Självstudier:

* [Skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md)
* [Skicka meddelanden till Android-enheter med hjälp av Notification Hubs och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Skicka meddelanden till en UWP-app som körs på en Windows-enhet](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Skicka meddelanden till en Windows Phone 8-app med hjälp av MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Skicka meddelanden med hjälp av Notification Hubs och Baidu Cloud Push](notification-hubs-baidu-china-android-notifications-get-started.md)
