---
title: Konfigurera ett labb för att lära dig MATLAB med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära dig MATLAB med Azure Lab Services.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445006"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Konfigurera ett labb för att lära sig MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html), som står för mat ris labb, är programmerings plattform från [MathWorks](https://www.mathworks.com/).  Den kombinerar beräknings kraft och visualisering som gör det till ett populärt verktyg i områdena matematik, teknik, fysik och kemi.

Om du använder en [licens för Campus-stor](https://www.mathworks.com/academia/tah-support-program/administrators.html), se anvisningar i [Hämta MATLAB](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) -installationsfiler för att ladda ned de MATLAB-installationsfiler som finns på mallen.  

I den här artikeln visar vi hur du konfigurerar en klass som använder MATLAB-klientprogram vara med en licens Server.

## <a name="license-server"></a>Licens Server

Innan du ändrar mall-datorn för ditt labb måste du konfigurera servern för att köra program varan för [nätverks licens hanteraren](https://www.mathworks.com/help/install/administer-network-licenses.html) .  Dessa anvisningar gäller endast för institutioner som väljer alternativet för nätverks licensiering för MATLAB, vilket innebär att användare kan dela en pool med licens nycklar.  Du måste också spara licens filen och fil installations nyckeln för senare.  Detaljerade anvisningar om hur du hämtar en licens fil finns i det första steget i artikeln [Installera nätverks licens hanterare med Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) .

Detaljerade anvisningar för hur du installerar en licensierings Server finns i [Installera nätverks licens hanterare med Internet anslutning](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Information om hur du aktiverar lånet finns i artikeln om [låne licens](https://www.mathworks.com/help/install/license/borrow-licenses.html) .

Om licens servern är placerad i ett lokalt nätverk eller ett privat nätverk i Azure kan du inte glömma att peer-koppla [det virtuella nätverket](how-to-connect-peer-virtual-network.md) till ditt [labb konto](tutorial-setup-lab-account.md).  Nätverks-peering måste göras innan du skapar labbet så att de virtuella datorerna i labbet kan komma åt licens servern.

## <a name="lab-configuration"></a>Labb konfiguration

Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet.  Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt konto.  Information om hur du skapar ett nytt labb konto finns i [själv studie kursen installera ett labb konto](tutorial-setup-lab-account.md).

Om du vill skapa ett nytt labb, följer du anvisningarna i [klass rummet labb](tutorial-setup-classroom-lab.md).  Använd följande inställningar:

| Storlek för virtuell dator | Bild |
| -------------------- | ----- |
| Medel | Windows 10 |

MATLAB stöds på fler operativ system.  Se [MATLAB-systemets krav](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) för mer information.

> [!WARNING]
> Glöm inte att [peer-koppla det virtuella nätverket](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) för labb kontot till det virtuella nätverket för licens servern innan du skapar labbet.

## <a name="template-machine"></a>Mall dator

När du har skapat mallen startar du datorn och ansluter till den för att slutföra följande viktiga uppgifter.

1. Hämta installationsfilerna för den MATLAB-klient program varan.
2. Installera MATLAB med fil installations nyckeln.

Installation av MATLAB är en process med flera delar.  Den första delen kommer att hämta filerna för MATLAB och andra produkter som du vill installera.  Om du använder en fil installations nyckel måste alla installationsfiler för produkter som ska installeras förladdas.  Den andra delen kommer att installera MATLAB-programvaran på mallen VM och aktivera program varan.  Om den virtuella mallen har kon figurer ATS för att aktivera med hjälp av licens servern gör de virtuella studenterna samma.

### <a name="download-installation-files"></a>Hämta installationsfiler

Du måste vara licens administratör för att kunna hämta installationsfilerna samt hämta licens filen och fil installations nyckeln.  Steg för att hämta installationsfilerna finns nedan.

1. Logga in på ditt konto för [https://www.mathworks.com](https://www.mathworks.com) .
2. Välj **mitt konto**.
3. Under avsnittet **min program vara** på sidan konto klickar du på licensen som är kopplad till installations programmet för nätverks licens hanteraren för labbet.
4. Klicka på **Ladda ned produkter** på sidan licens information.
5. Vänta tills installations programmet har extraherat.
6. Starta installationsprogrammet.  
7. På sidan **Logga in på ditt MathWorks-konto** anger du ditt MathWorks-konto.
8. Godkänn termen på sidan **MathWorks licens avtal** och klicka på knappen **Nästa** .
9. Klicka på list rutan **Avancerade alternativ** och välj **Jag vill hämta utan att installera**.
10. I **Välj målmapp** klickar du på **Nästa**.
11. Välj **Windows** som plattform för den dator som du ska installera MATLAB.
12. På sidan **Välj produkt** ser du till att MATLAB är markerat tillsammans med andra MathWorks-produkter som du vill installera.
13. På sidan **Bekräfta val och hämtning** klickar du på **starta nedladdning**.  
14. Vänta tills de valda produkterna har laddats ned.  Klicka på **Finish**.

Du kan också hämta en ISO-avbildning från MathWorks-webbplatsen.

1. Logga in på ditt konto för [https://www.mathworks.com](https://www.mathworks.com) .
2. Gå till [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads).
3. Välj den version av MATLAB som du vill installera.
4. Klicka på länken "Hämta {version}. ISO-avbildning" under de relaterade länkarna där {version} är något som R2020a.
5. Klicka på den blå **nedladdnings** länken för Windows.

### <a name="run-installer"></a>Kör installations programmet

När filerna har hämtats är det andra steget att köra installations programmet. Du måste vara licens administratör för att slutföra det här steget.  Endast licens administratörer kan installera MATLAB med en fil installations nyckel.

1. Kontrol lera den hämtade licens filen och kontrol lera att SERVER raden visar licens servern korrekt.  Information om hur licens filen ska formateras finns i [Uppdatera nätverks licens](https://www.mathworks.com/help/install/ug/network-license-files.html), [licens lån](https://www.mathworks.com/help/install/license/borrow-licenses.html)och [hitta värd-ID-](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) artiklar.
2. Starta MATLAB-installationsprogrammet.
3. På sidan **Logga in på ditt MathWorks-konto** anger du ditt MathWorks-konto.
4. Godkänn termen på sidan **MathWorks licens avtal** och klicka på knappen **Nästa** .
5. Klicka på list rutan **Avancerade alternativ** och välj **Jag har en fil installations nyckel**.
6. På sidan **Installera Använd fil installations nyckel** anger du fil installations nyckeln för licens servern.   Klicka på **Nästa**.
7. På sidan **Välj licens fil** navigerar du till licens filen som sparades när du hämtade installationsfilerna tidigare.
8. På sidan **Välj målmapp** klickar du på **Nästa**.
9. På sidan **Välj produkter** klickar du på **Nästa**.
10. På sidan **Välj alternativ** klickar du på **Nästa**.
11. På sidan **Bekräfta val och installation** klickar du på **påbörja installation**.
12. Kontrol lera att **Aktivera MATLAB** är markerat på sidan **installationen har slutförts** .  Klicka på **Finish**.

## <a name="cost-estimate"></a>Kostnads uppskattning

Vi ska se en möjlig kostnads uppskattning för den här klassen.  Den här beräkningen omfattar inte kostnaden för att köra licens servern.  Vi använder en klass av 25 studenter.  Det finns 20 timmar med den schemalagda klass tiden.  Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider.  Storleken på den virtuella datorn som vi valde var medel, vilket är 55 lab-enheter.

Här är ett exempel på en möjlig kostnads uppskattning för den här klassen:

25 studenter \* (20 schemalagda timmar + 10 kvot timmar) \* 55 lab-enheter \*  0,01 USD per timme = 412,50 USD

>[!IMPORTANT]
> Kostnads uppskattning är till exempel endast syfte.  Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa, hantera och publicera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
