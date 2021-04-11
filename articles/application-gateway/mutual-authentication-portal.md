---
title: Konfigurera ömsesidig autentisering på Azure Application Gateway via portalen
description: Lär dig hur du konfigurerar en Application Gateway att ha ömsesidig autentisering via portalen
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231517"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Konfigurera ömsesidig autentisering med Application Gateway via portalen (för hands version)

Den här artikeln beskriver hur du använder Azure Portal för att konfigurera ömsesidig autentisering på din Application Gateway. Ömsesidig autentisering innebär att Application Gateway autentiserar klienten som skickar begäran med hjälp av det klient certifikat som du överför till Application Gateway. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera ömsesidig autentisering med en Application Gateway behöver du ett klient certifikat att ladda upp till gatewayen. Klient certifikatet kommer att användas för att verifiera det certifikat som klienten kommer att presentera för att Application Gateway. I test syfte kan du använda ett självsignerat certifikat. Detta rekommenderas dock inte för produktions arbets belastningar eftersom de är svårare att hantera och inte är helt säkra. 

Mer information, särskilt om vilken typ av klient certifikat som du kan ladda upp, finns i [Översikt över ömsesidig autentisering med Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-new-application-gateway"></a>Skapa en ny Application Gateway

Skapa först en ny Application Gateway som du brukar via portalen. det finns inga ytterligare steg att skapa för att aktivera ömsesidig autentisering. Mer information om hur du skapar en Application Gateway i portalen finns i [självstudien om Portal snabb start](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Konfigurera ömsesidig autentisering 

Om du vill konfigurera en befintlig Application Gateway med ömsesidig autentisering måste du först gå till fliken **SSL-inställningar (för hands version)** i portalen och skapa en ny SSL-profil. När du skapar en SSL-profil visas två flikar: **klientautentisering** och **SSL-princip**. På fliken **klientautentisering** kan du ladda upp dina klient certifikat. Fliken **SSL-princip** är att konfigurera en lyssnare-speciell SSL-princip – mer information finns i [Konfigurera en lyssnare-speciell SSL-princip](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Kontrol lera att du laddar upp hela klient certifikat utfärdarens certifikat kedja i en fil och bara en kedja per fil.

1. Sök efter **Application Gateway** i portalen, Välj **programgatewayer** och klicka på din befintliga Application Gateway.

2. Välj **SSL-inställningar (förhands granskning)** på den vänstra menyn.

3. Klicka på plus tecknet bredvid SSL- **profiler** överst för att skapa en ny SSL-profil.

4. Ange ett namn under **SSL**-profilnamn. I det här exemplet kallar vi vår SSL-profil *applicationGatewaySSLProfile*. 

5. Stanna kvar på fliken **klientautentisering** . Ladda upp PEM-certifikatet som du tänker använda för ömsesidig autentisering mellan klienten och Application Gateway med hjälp av knappen **Ladda upp ett nytt certifikat** . 

    Mer information om hur du extraherar certifikat kedjor för betrodda klient certifikat kedjor som ska överföras finns i [så här extraherar du certifikat kedjor för betrodda klient certifikat utfärdare](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Om detta inte är din första SSL-profil och du har laddat upp andra klient certifikat till din Application Gateway, kan du välja att återanvända ett befintligt certifikat på din gateway via List menyn. 

6. Markera kryss rutan **Verifiera bara certifikat utfärdarens DN** om du vill att Application Gateway ska verifiera klient certifikatets unika namn för omedelbara utfärdare. 

7. Överväg att lägga till en avlyssnings princip. Se anvisningar för att [Konfigurera Listener Specific SSL-principer](./application-gateway-configure-listener-specific-ssl-policy.md).

8. Välj **Lägg till** för att spara.
    > [!div class="mx-imgBorder"]
    > ![Lägg till klientautentisering i SSL-profil](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Koppla SSL-profilen till en lyssnare

Nu när vi har skapat en SSL-profil med ömsesidig autentisering konfigurerad måste vi koppla SSL-profilen till lyssnaren för att slutföra konfigurationen av ömsesidig autentisering. 

1. Navigera till din befintliga Application Gateway. Om du precis har slutfört stegen ovan behöver du inte göra något här. 

2. Välj **lyssnare** på menyn på den vänstra sidan. 

3. Klicka på **Lägg till lyssnare** om du inte redan har KONFIGURERAT en https-lyssnare. Om du redan har en HTTPS-lyssnare klickar du på den i listan. 

4. Fyll i **lyssnarens namn**, **klient delens IP-adress**, **port**, **protokoll** och andra **https-inställningar** så att de passar dina behov.

5. Markera kryss rutan **Aktivera SSL-profil** så att du kan välja vilken SSL-profil som ska associeras med lyssnaren. 

6. Välj den SSL-profil som du nyss skapade i list rutan. I det här exemplet väljer vi den SSL-profil vi skapade från föregående steg: *applicationGatewaySSLProfile*. 

7. Fortsätt att konfigurera resten av lyssnaren så att den passar dina behov. 

8. Klicka på **Lägg till** för att spara din nya lyssnare med den SSL-profil som är kopplad till den. 

    > [!div class="mx-imgBorder"]
    > ![Koppla SSL-profil till ny lyssnare](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Förnya utgångna klient certifikat för certifikat utfärdare

Om klient certifikat utfärdarens certifikat har upphört att gälla kan du uppdatera certifikatet på din gateway genom att följa stegen nedan: 

1. Navigera till Application Gateway och gå till fliken **SSL-inställningar (förhands granskning)** på den vänstra menyn. 
 
1. Välj de befintliga SSL-profilerna med klient certifikatet som har förfallit. 
 
1. Välj **överför ett nytt certifikat** på fliken **klientautentisering** och överför ditt nya klient certifikat. 
 
1. Välj pappers korgs ikonen bredvid det utgångna certifikatet. Detta tar bort associationen för certifikatet från SSL-profilen. 

1. Upprepa steg 2-4 ovan med en annan SSL-profil som har använt samma utgångna klient certifikat. Du kommer att kunna välja det nya certifikatet du laddade upp i steg 3 från List menyn i andra SSL-profiler.

## <a name="next-steps"></a>Nästa steg

- [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)