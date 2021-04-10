---
title: Konfigurera lyssnings bara SSL-principer på Azure Application Gateway via portalen
description: Lär dig hur du konfigurerar Listener-/regionsspecifika SSL-principer på Application Gateway via portalen
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231644"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Konfigurera lyssnings bara SSL-principer på Application Gateway via portalen (för hands version)

Den här artikeln beskriver hur du använder Azure Portal för att konfigurera Listener-/regionsspecifika SSL-principer på din Application Gateway. Med lyssnings bara SSL-principer kan du konfigurera vissa lyssnare så att de använder olika SSL-principer från varandra. Du kommer fortfarande att kunna ange en standard-SSL-princip som alla lyssnare kommer att använda om de inte skrivs över av den lyssnare-speciella SSL-principen. 

> [!NOTE]
> Endast Standard_v2 och WAF_v2 SKU: er har stöd för avlyssnings vissa principer som Listener-vissa principer ingår i SSL-profiler, och SSL-profiler stöds bara på v2-gatewayer. 



Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-new-application-gateway"></a>Skapa en ny Application Gateway

Skapa först en ny Application Gateway som du brukar via portalen. det finns inga ytterligare steg att skapa för att konfigurera lyssnings bara SSL-principer. Mer information om hur du skapar en Application Gateway i portalen finns i [självstudien om Portal snabb start](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Konfigurera en lyssnare-Specific SSL-princip

Om du vill konfigurera en avlyssnings-/regionsspecifika SSL-princip måste du först gå till fliken **SSL-inställningar (för hands version)** i portalen och skapa en ny SSL-profil. När du skapar en SSL-profil visas två flikar: **klientautentisering** och **SSL-princip**. Fliken **SSL-princip** är att konfigurera en lyssnare-Specific SSL-princip. På fliken **klientautentisering** kan du ladda upp ett eller flera klient certifikat för ömsesidig autentisering – mer information finns i [Konfigurera en ömsesidig autentisering](./mutual-authentication-portal.md).

> [!NOTE]
> Vi rekommenderar att du använder TLS 1,2 som TLS 1,2 i framtiden. 

1. Sök efter **Application Gateway** i portalen, Välj **programgatewayer** och klicka på din befintliga Application Gateway.

2. Välj **SSL-inställningar (förhands granskning)** på den vänstra menyn.

3. Klicka på plus tecknet bredvid SSL- **profiler** överst för att skapa en ny SSL-profil.

4. Ange ett namn under **SSL**-profilnamn. I det här exemplet kallar vi vår SSL-profil *applicationGatewaySSLProfile*. 

5. Gå till fliken **SSL-princip** och markera kryss rutan **Aktivera lyssnings-Specific SSL-princip** . 

6. Konfigurera din lyssnare-specifik SSL-princip med dina krav. Du kan välja mellan fördefinierade SSL-principer och anpassa din egen SSL-princip. Mer information om SSL-principer finns i [Översikt över SSL-principer](./application-gateway-ssl-policy-overview.md). Vi rekommenderar att du använder TLS 1,2

7. Välj **Lägg till** för att spara.

    > [!NOTE]
    > Du behöver inte konfigurera klientautentisering på en SSL-profil för att koppla den till en lyssnare. Du kan bara konfigurera klientautentisering eller bara lyssnare för en speciell SSL-princip som kon figurer ATS eller båda som kon figurer ATS i din SSL-profil.  

    ![Lägg till Listener Specific SSL-princip i SSL-profil](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Koppla SSL-profilen till en lyssnare

Nu när vi har skapat en SSL-profil med en lyssnare-Specific SSL-princip måste vi koppla SSL-profilen till lyssnaren för att kunna använda den avlyssnings bara principen i praktiken. 

1. Navigera till din befintliga Application Gateway. Om du precis har slutfört stegen ovan behöver du inte göra något här. 

2. Välj **lyssnare** på menyn på den vänstra sidan. 

3. Klicka på **Lägg till lyssnare** om du inte redan har KONFIGURERAT en https-lyssnare. Om du redan har en HTTPS-lyssnare klickar du på den i listan. 

4. Fyll i **lyssnarens namn**, **klient delens IP-adress**, **port**, **protokoll** och andra **https-inställningar** så att de passar dina behov.

5. Markera kryss rutan **Aktivera SSL-profil** så att du kan välja vilken SSL-profil som ska associeras med lyssnaren. 

6. Välj den SSL-profil som du skapade i list rutan. I det här exemplet väljer vi den SSL-profil vi skapade från föregående steg: *applicationGatewaySSLProfile*. 

7. Fortsätt att konfigurera resten av lyssnaren så att den passar dina behov. 

8. Klicka på **Lägg till** för att spara din nya lyssnare med den SSL-profil som är kopplad till den. 

    ![Koppla SSL-profil till ny lyssnare](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)