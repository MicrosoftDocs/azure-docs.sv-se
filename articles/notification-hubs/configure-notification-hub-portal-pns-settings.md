---
title: Konfigurera push-meddelanden i Azure Notification Hubs | Microsoft Docs
description: Lär dig att konfigurera Azure Notification Hubs i Azure Portal med hjälp av inställningar för plattformsmeddelandesystem (PNS).
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
ms.openlocfilehash: 31d915cd44bcf60f3515eb1a84309980f45d40b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868311"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Snabbstart: Konfigurera push-meddelanden i en meddelandehubb

Azure Notification Hubs tillhandahåller en push-motor som är enkel att använda och som skalar ut. Använd Notification Hubs att skicka meddelanden till valfri plattform (iOS, Android, Windows, Baidu) och från valfri backend-plats (i molnet eller lokalt). Mer information finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten använder du inställningarna för plattformsmeddelandesystem (PNS) i Notification Hubs för att konfigurera push-meddelanden på flera plattformar. Snabbstarten visar hur du gör i Azure Portal. [Google Firebase Cloud Messaging innehåller](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) instruktioner för att använda Azure CLI.

Om du inte redan har skapat en meddelandehubb skapar du en nu. Mer information finns i Skapa [en Azure-meddelandehubb i Azure Portal](create-notification-hub-portal.md) eller Skapa en [Azure-meddelandehubb med Hjälp av Azure CLI.](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Konfigurera en Apple Push Notification Service (APNS):

1. På Azure Portal väljer **du** **Apple (APNS)** på den vänstra menyn på sidan Notification Hub.

1. För **Autentiseringsläge** väljer du antingen **Certifikat** eller **Token**.

   a. Om du väljer **Certifikat:**
   * Välj filikonen och välj sedan den *.p12-fil* som du vill ladda upp.
   * Ange ett lösenord.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som har köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-certifikatkonfiguration i Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Om du väljer **Token**:

   * Ange värdena för **Nyckel-ID,** **Paket-ID,** **Team-ID** och **Token**.
   * Välj **Sandbox**-läge. Om du vill skicka push-meddelanden till användare som har köpt appen från butiken väljer du **Produktionsläge.**

     ![Skärmbild av en APNS-tokenkonfiguration i Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Mer information finns i Skicka [push-meddelanden till iOS-appar med Azure Notification Hubs](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konfigurera push-meddelanden för Google FCM:

1. På Azure Portal väljer du Google  **(GCM/FCM) på** den vänstra menyn på sidan Notification Hub.
2. Klistra in **API-nyckeln** för Google FCM-projektet som du sparade tidigare.
3. Välj **Spara**.

   ![Skärmbild som visar hur du konfigurerar Notification Hubs för Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

När du har slutfört de här stegen indikerar en avisering att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du behöver **API-nyckeln för** ditt Google Firebase Cloud Messaging-projekt (FCM).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Den här artikeln kräver version 2.0.67 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

### <a name="set-up-push-notifications-for-google-fcm"></a>Konfigurera push-meddelanden för Google FCM

1. Använd kommandot [az notification-hub credential gcm update](/cli/azure/notification-hub/credential/gcm#az_notification_hub_credential_gcm_update) för att lägga till din Google API-nyckel i meddelandehubben.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android-appen behöver en anslutningssträng för att ansluta till meddelandehubben.  Använd kommandot [az notification-hub authorization-rule list för](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list) att visa en lista över tillgängliga åtkomstprinciper.  Använd kommandot [az notification-hub authorization-rule list-keys för](/cli/azure/notification-hub/authorization-rule#az_notification_hub_authorization_rule_list_keys) att hämta anslutningssträngarna för åtkomstprincipen.  Ange **primaryConnectionString eller** **secondaryConnectionString i** `--query` parametern för att hämta den primära anslutningssträngen direkt.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Använd kommandot [az notification-hub test-send för](/cli/azure/notification-hub#az_notification_hub_test_send) att testa att skicka meddelanden till Android-appen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Hämta Azure CLI-referenser för andra plattformar med [kommandot az notification-hub credential.](/cli/azure/notification-hub/credential)

Mer information om hur du skickar meddelanden till en Android-app finns i [Skicka push-meddelanden till Android-enheter med Firebase.](notification-hubs-android-push-notification-google-fcm-get-started.md)

---

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

Konfigurera en Windows Push Notification Service (WNS):

1. På Azure Portal väljer **du** **Windows (WNS)** på den vänstra menyn på sidan Notification Hub.
2. Ange värden för **Package SID** (Paket-SID) och **Security Key** (Säkerhetsnyckel).
3. Välj **Spara**.

   ![Skärmbild som visar rutorna Paket-SID och Säkerhetsnyckel](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Mer information finns i [Skicka meddelanden till UWP-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service för Windows Phone

Så här ställer Microsoft Push Notification Service (MPNS) för Windows Phone:

1. I Azure Portal väljer du **MPNS (Windows Phone Notification** **Hub)** på den vänstra menyn.
1. Aktivera antingen ej autentiserade eller autentiserade push-meddelanden:

   a. Om du vill aktivera oauthenticerade push-meddelanden **väljer du Aktivera ejauticerade push-meddelanden**  >  **Spara**.

      ![Skärmbild som visar hur du aktiverar oauticerade push-meddelanden](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Så här aktiverar du autentiserade push-meddelanden:
      * I verktygsfältet väljer du Ladda **upp certifikat.**
      * Välj filikonen och välj sedan certifikatfilen.
      * Ange lösenordet för certifikatet.
      * Välj **OK**.
      * På Windows Phone **(MPNS)** väljer du **Spara**.

Mer information finns i Skicka [meddelanden till Windows Phone med hjälp av Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Konfigurera push-meddelanden för Baidu:

1. I Azure Portal väljer du **Baidu** **(Android China)** på den vänstra menyn på sidan Notification Hub.
2. Ange **den API-nyckel** som du fick från Baidu-konsolen i Baidu Cloud Push projekt.
3. Ange den **hemliga nyckel** som du fick från Baidu-konsolen i Baidu Cloud Push projekt.
4. Välj **Spara**.

    ![Skärmbild av Notification Hubs som visar Baidu-konfigurationen (Android China) för push-meddelanden](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

När du har slutfört de här stegen anger en avisering att meddelandehubben har uppdaterats. Knappen **Spara** är inaktiverad.

Mer information finns i [Kom igång med Notification Hubs hjälp av Baidu.](notification-hubs-baidu-china-android-notifications-get-started.md)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du konfigurerar systeminställningar för plattformsmeddelanden för en meddelandehubb i Azure Portal.

Mer information om hur du push-meddelanden till olika plattformar finns i dessa självstudier:

* [Skicka push-meddelanden till iOS-appar med Azure Notification Hubs](ios-sdk-get-started.md)
* [Skicka meddelanden till Android-enheter med hjälp Notification Hubs och Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Skicka meddelanden till en UWP-app som körs på en Windows-enhet](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Skicka meddelanden till en Windows Phone 8-app med hjälp av MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Skicka meddelanden med hjälp av Notification Hubs och Baidu Cloud Push](notification-hubs-baidu-china-android-notifications-get-started.md)
