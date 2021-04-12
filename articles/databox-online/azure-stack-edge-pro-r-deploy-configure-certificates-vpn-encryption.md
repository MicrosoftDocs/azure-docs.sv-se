---
title: Självstudie för att konfigurera certifikat för Azure Stack Edge Pro R-enhet i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Pro R instruerar dig att konfigurera certifikat på den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059980"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Självstudie: Konfigurera certifikat för Azure Stack Edge Pro R

I den här självstudien beskrivs hur du kan konfigurera certifikat för din Azure Stack Edge Pro R-enhet med hjälp av det lokala webb gränssnittet.

Den tid det tar för det här steget kan variera beroende på vilket alternativ du väljer och hur certifikat flödet upprättas i din miljö.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten
> * Konfigurera VPN
> * Konfigurera kryptering vid vila

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Pro R-enhet ser du till att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).
* Om du planerar att ta med dina egna certifikat:
    - Du bör ha dina certifikat klara i rätt format, inklusive signerings kedjans certifikat. Mer information om certifikat finns i [Hantera certifikat](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Konfigurera certifikat för enheten

1. På sidan **certifikat** kommer du att konfigurera dina certifikat. Beroende på om du har ändrat enhets namnet eller DNS-domänen på sidan **enhet** kan du välja något av följande alternativ för dina certifikat.

    - Om du inte har ändrat enhets namnet eller DNS-domänen i det tidigare steget kan du hoppa över det här steget och fortsätta till nästa steg. Enheten har automatiskt genererat självsignerade certifikat som börjar med. 

    - Om du har ändrat enhets namnet eller DNS-domänen visas statusen för certifikaten visas som **ogiltig**. 

        ![Lokalt webb gränssnitt "certifikat" Sidan 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Välj ett certifikat för att visa information om statusen.

        ![Lokalt webb gränssnitt "certifikat" sidan 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Detta beror på att certifikaten inte återspeglar det uppdaterade enhets namnet och DNS-domänen (som används i ämnes namn och alternativt ämnes namn). För att kunna aktivera din enhet kan du ta med dina egna signerade slut punkts certifikat och motsvarande signerings kedjor. Du lägger först till signerings kedjan och laddar sedan upp slut punkts certifikaten. Mer information finns i ta med [dina egna certifikat på din Azure Stack Edge Pro R-enhet](#bring-your-own-certificates).

    - Om du har ändrat enhets namnet eller DNS-domänen och inte tar med dina egna certifikat, **blockeras aktiveringen**.

    
#### <a name="bring-your-own-certificates"></a>Ta med dina egna certifikat

Följ dessa steg om du vill lägga till egna certifikat, inklusive signerings kedjan.

1. För att överföra certifikat, på sidan **certifikat** , väljer du **+ Lägg till certifikat**.

    ![Lokalt webb gränssnitt "certifikat" sida 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Ladda upp signerings kedjan först och välj **verifiera & Lägg till**.

    ![Lokalt webb gränssnitt "certifikat" sidan 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Nu kan du ladda upp andra certifikat. Du kan till exempel Ladda upp certifikaten för Azure Resource Manager och Blob Storage-slutpunkten.

    ![Lokalt webb gränssnitt "certifikat" sidan 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Du kan också ladda upp det lokala webb GRÄNSSNITTs certifikatet. När du har laddat upp det här certifikatet måste du starta webbläsaren och rensa cacheminnet. Sedan måste du ansluta till det lokala webb gränssnittet för enheten.  

    ![Lokalt webb gränssnitt "certifikat" sida 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Du kan också ladda upp Node-certifikatet.

    ![Lokalt webb gränssnitt "certifikat" sidan 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Slutligen kan du ladda upp VPN-certifikatet.

    ![Lokalt webb gränssnitt "certifikat" sidan 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Du kan när som helst välja ett certifikat och se information för att se till att dessa matchar det certifikat som du har överfört.

    Sidan certifikat bör uppdateras för att avspegla de nyligen tillagda certifikaten.

    ![Lokalt webb gränssnitt "certifikat" sidan 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Förutom för det offentliga Azure-molnet behövs signerings kedjans certifikat innan aktiveringen för alla molnappar (Azure Government eller Azure Stack).

1. Välj **< tillbaka för att komma igång**.

## <a name="configure-vpn"></a>Konfigurera VPN

1. Välj **Konfigurera** för VPN på panelen **säkerhet** .

    ![Sidan VPN för lokalt webb gränssnitt](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Om du vill konfigurera VPN måste du först se till att du har all nödvändig konfiguration i Azure. Mer information finns i [Konfigurera förutsättningar](azure-stack-edge-placeholder.md) och [Konfigurera Azure-resurser för VPN](azure-stack-edge-placeholder.md). När detta är klart kan du göra konfigurationen i det lokala användar gränssnittet.
    
    1. På sidan VPN väljer du **Konfigurera**.
    2. På bladet **Konfigurera VPN** :

    - Aktivera **VPN-inställningar**.
    - Ange den **delade VPN-hemligheten**. Det här är den delade nyckel du angav när du skapade objektet Azure VPN-anslutning.
    - Ange IP-adress för **VPN-gateway** . Det här är IP-adressen för den lokala Azure-Nätverksgatewayen.
    - För **PFS-grupp** väljer du **ingen**. 
    - För **DH-grupp** väljer du **group2**.
    - För **integritets metod för IPSec** väljer du **SHA256**.
    - För **IPseccipher Transform-konstanter** väljer du **GCMAES256**.
    - För **omvandlings konstanter för IPSec-autentisering** väljer du **GCMAES256**.
    - För **krypterings metod för IKE** väljer du **AES256**.
    - Välj **Använd**.

        ![Konfigurera lokalt användar gränssnitt 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Om du vill överföra konfigurations filen för VPN-vägen väljer du **överför**. 
    
        ![Konfigurera lokalt användar gränssnitt 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Bläddra till den VPN- *konfigurationsfilen som* du laddade ned i det lokala systemet i föregående steg.
        - Välj region som den Azure-region som är kopplad till enheten, det virtuella nätverket och gatewayer.
        - Välj **Använd**.
    
            ![Konfigurera lokalt användar gränssnitt 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Om du vill lägga till användarspecifika vägar konfigurerar du IP-adressintervall så att de endast används med VPN. 
    
        - Under **IP-adressintervall som ska nås enbart via VPN väljer du** **Konfigurera**.
        - Ange ett giltigt IPv4-intervall och välj **Lägg till**. Upprepa stegen för att lägga till andra intervall.
        - Välj **Använd**.
    
            ![Konfigurera lokalt användar gränssnitt 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Välj **< tillbaka för att komma igång**.

## <a name="configure-encryption-at-rest"></a>Konfigurera kryptering vid vila

1. På **säkerhets** panelen väljer du **Konfigurera** för kryptering vid vila. Detta är en obligatorisk inställning och tills den har kon figurer ATS kan du inte aktivera enheten. 

    När enheterna är avbildade på fabriken är volym nivå BitLocker-kryptering aktive rad. När du har fått enheten måste du konfigurera kryptering vid vila. Lagringspoolen och volymer återskapas och du kan ange BitLocker-nycklar för att aktivera kryptering vid vila och därmed skapa ett andra lager av kryptering för dina data i vila.

1. I fönstret **krypterings-vid-rest** anger du en 32 tecken lång Base-64-kodad nyckel. Det här är en engångs konfiguration och den här nyckeln används för att skydda den faktiska krypterings nyckeln. Du kan välja att generera nyckeln automatiskt eller ange en.

    ![Fönster för lokalt webb gränssnitt "kryptering i vila"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Nyckeln sparas i en nyckel fil på sidan med **moln information** när enheten har Aktiver ATS.

1. Välj **Använd**. Den här åtgärden tar flera minuter och status för åtgärden visas på **säkerhets** panelen.

    ![Sidan kryptering på vilo läge för lokalt webb gränssnitt](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. När statusen visas som **slutförd** väljer **du< tillbaka för att komma igång**.

Enheten är nu redo att aktive ras. 


## <a name="next-steps"></a>Nästa steg

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten
> * Konfigurera VPN
> * Konfigurera kryptering vid vila

Information om hur du aktiverar din Azure Stack Edge Pro R-enhet finns i:

> [!div class="nextstepaction"]
> [Aktivera Azure Stack Edge Pro R-enhet](./azure-stack-edge-pro-r-deploy-activate.md)
