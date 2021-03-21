---
title: Självstudie för att konfigurera certifikat för Azure Stack Edge Mini R-enhet i Azure Portal | Microsoft Docs
description: Självstudie för att distribuera Azure Stack Edge Mini R instruerar dig att konfigurera certifikat på den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: c3a09242b895234c96c64d9e23449d980e47e387
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546745"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Självstudie: Konfigurera certifikat, VPN, kryptering för Azure Stack Edge Mini R

I den här självstudien beskrivs hur du kan konfigurera certifikat, VPN och kryptering i vila för din Azure Stack Edge-Mini R-enhet med hjälp av det lokala webb gränssnittet.

Den tid det tar för det här steget kan variera beroende på vilket alternativ du väljer och hur certifikat flödet upprättas i din miljö.

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten
> * Konfigurera VPN
> * Konfigurera kryptering i vila

## <a name="prerequisites"></a>Förutsättningar

Innan du konfigurerar och konfigurerar din Azure Stack Edge Mini R-enhet ser du till att:

* Du har installerat den fysiska enheten enligt beskrivningen i [installera Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).

* Om du planerar att ta med dina egna certifikat:
    - Du bör ha dina certifikat klara i rätt format, inklusive signerings kedjans certifikat. Mer information om certifikat finns i [Hantera certifikat](azure-stack-edge-gpu-manage-certificates.md)

    - Om enheten har distribuerats i Azure Government eller Azure Government Secret eller Azure Government Top Secret och inte distribueras i det offentliga Azure-molnet krävs ett certifikat för signerings kedjan innan du kan aktivera enheten. 
    Mer information om certifikat finns i [Hantera certifikat](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Konfigurera certifikat för enheten

1. På sidan **certifikat** kommer du att konfigurera dina certifikat. Beroende på om du har ändrat enhets namnet eller DNS-domänen på sidan **enhet** kan du välja något av följande alternativ för dina certifikat.

    - Om du inte har ändrat standardenhets namnet eller standard-DNS-domänen i det tidigare steget och inte vill ta med dina egna certifikat kan du hoppa över det här steget och fortsätta till nästa steg. Enheten har automatiskt genererat självsignerade certifikat som börjar med. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Om du har ändrat enhets namnet eller DNS-domänen visas statusen för certifikaten visas som **ogiltig**. 

        ![Lokalt webb gränssnitt "certifikat" Sidan 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Välj ett certifikat för att visa information om statusen.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Certifikat statusen är **inte giltig** eftersom certifikaten inte återspeglar det uppdaterade enhets namnet och DNS-domänen (som används i ämnes namn och alternativt ämnes namn). För att kunna aktivera din enhet kan du ta med dina egna signerade slut punkts certifikat och motsvarande signerings kedjor. Du lägger först till signerings kedjan och laddar sedan upp slut punkts certifikaten. Om du vill ha mer information går du till [ta med dina egna certifikat på din Azure Stack Edge Mini R-enhet](#bring-your-own-certificates).


    - Om du har ändrat enhets namnet eller DNS-domänen och inte tar med dina egna certifikat, **blockeras aktiveringen**.


#### <a name="bring-your-own-certificates"></a>Ta med dina egna certifikat

Du har redan lagt till signerings kedjan i ett tidigare steg på den här enheten. Nu kan du ladda upp slut punkts certifikat, nodnamn, lokalt UI-certifikat och VPN-certifikatet. Följ dessa steg om du vill lägga till dina egna certifikat.

1. För att överföra certifikat, på sidan **certifikat** , väljer du **+ Lägg till certifikat**.

    [![Lokalt webb gränssnitt "certifikat" sida 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Du kan ladda upp andra certifikat. Du kan till exempel Ladda upp certifikaten för Azure Resource Manager och Blob Storage-slutpunkten.

    ![Lokalt webb gränssnitt "certifikat" sidan 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Du kan också ladda upp det lokala webb GRÄNSSNITTs certifikatet. När du har laddat upp det här certifikatet måste du starta webbläsaren och rensa cacheminnet. Sedan måste du ansluta till det lokala webb gränssnittet för enheten.  

    ![Lokalt webb gränssnitt "certifikat" sida 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Du kan också ladda upp Node-certifikatet.


    ![Lokalt webb gränssnitt "certifikat" sidan 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Slutligen kan du ladda upp VPN-certifikatet.
        
    ![Sidan certifikat för lokalt webb gränssnitt](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. Du kan när som helst välja ett certifikat och se information för att se till att dessa matchar det certifikat som du har överfört.

    [![Lokalt webb gränssnitt "certifikat" sidan 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    Sidan certifikat bör uppdateras för att avspegla de nyligen tillagda certifikaten.

    [![Lokalt webb gränssnitt "certifikat" sidan 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Förutom för det offentliga Azure-molnet behövs signerings kedjans certifikat innan aktiveringen för alla molnappar (Azure Government eller Azure Stack Hub).


## <a name="configure-vpn"></a>Konfigurera VPN

1. Välj **Konfigurera** för VPN på panelen **säkerhet** . 

    Om du vill konfigurera VPN måste du först se till att du har all nödvändig konfiguration i Azure. Mer information finns i [Konfigurera VPN via PowerShell för din Azure Stack Edge Mini R-enhet](azure-stack-edge-placeholder.md). När detta är klart kan du göra konfigurationen i det lokala användar gränssnittet.
    
    1. På sidan VPN väljer du **Konfigurera**.
        ![Konfigurera lokalt VPN-gränssnitt 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. På bladet **Konfigurera VPN** :

        1. Ange telefon boken som inmatad.
        2. Ange JSON-filen för Azure Data Center IP-intervall som indata. Ladda ned den här filen från: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. Välj **öster** som region.
        4. Välj **Använd**.

        ![Konfigurera lokalt VPN-gränssnitt 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Konfigurera IP-adressintervall som endast ska användas med VPN. 
    
        - Under **IP-adressintervall som ska nås enbart via VPN väljer du** **Konfigurera**.
        - Ange det virtuella nätverkets IPv4-intervall som du har valt för din Azure-Virtual Network.
        - Välj **Använd**.
    
        ![Konfigurera lokalt VPN-gränssnitt 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Enheten är nu redo att krypteras. Konfigurera kryptering i vila.


## <a name="enable-encryption"></a>Aktivera kryptering

1. På **säkerhets** panelen väljer du **Konfigurera** för kryptering vid vila. Detta är en obligatorisk inställning och tills den har kon figurer ATS kan du inte aktivera enheten. 

    ![Lokalt webb gränssnitt "kryptering på Vila" sida 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    När enheterna är avbildade på fabriken är volym nivå BitLocker-kryptering aktive rad. När du har fått enheten måste du konfigurera kryptering vid vila. Lagringspoolen och volymer återskapas och du kan ange BitLocker-nycklar för att aktivera kryptering vid vila och därmed skapa ett andra lager av kryptering för dina data i vila.

1. I fönstret **krypterings-vid-rest** anger du en 32-tecken lång (AES-256-bitars) Base-64-kodad nyckel. Det här är en engångs konfiguration och den här nyckeln används för att skydda den faktiska krypterings nyckeln. 

    ![Lokalt webb gränssnitt "kryptering på Vila" sida 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Du kan även välja att automatiskt generera den här nyckeln.

    ![Lokalt webb gränssnitt "kryptering på Vila" sidan 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Välj **Använd**. Den här åtgärden tar flera minuter och status för åtgärden visas på **säkerhets** panelen.

    ![Lokalt webb gränssnitt "kryptering i vila" sida 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. När statusen visas som **slutförd** går du tillbaka till **Kom igång**.

Enheten är nu redo att aktive ras.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Förutsättningar
> * Konfigurera certifikat för den fysiska enheten
> * Konfigurera VPN
> * Konfigurera kryptering i vila

Information om hur du aktiverar din Azure Stack Edge-Mini R-enhet finns i:

> [!div class="nextstepaction"]
> [Aktivera Azure Stack Edge Mini R-enhet](./azure-stack-edge-mini-r-deploy-activate.md)
