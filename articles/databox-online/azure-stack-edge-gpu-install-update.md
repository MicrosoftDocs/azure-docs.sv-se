---
title: Installera uppdatering på Azure Stack Edge Pro GPU-enhet | Microsoft Docs
description: Beskriver hur du tillämpar uppdateringar med hjälp av Azure Portal och lokalt webb gränssnitt för Azure Stack Edge Pro GPU-enhet och Kubernetes-klustret på enheten
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 4590949f2feb86dc344dce87f3ff447e0e05e8ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438122"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Uppdatera Azure Stack Edge Pro GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

I den här artikeln beskrivs de steg som krävs för att installera uppdateringen på Azure Stack Edge Pro med GPU via det lokala webb gränssnittet och via Azure Portal. Du tillämpar program uppdateringar eller snabb korrigeringar för att hålla din Azure Stack Edge Pro-enhet och tillhör ande Kubernetes-kluster på enheten uppdaterad.

Proceduren som beskrivs i den här artikeln har utförts med en annan version av program varan, men processen är densamma för den aktuella program versionen.

> [!IMPORTANT]
> - Uppdatering **2101** är den aktuella uppdateringen och motsvarar:
>   - Enhetens program varu version – **2.2.1473.2521**
>   - Kubernetes Server version – **v 1.17.3**
>   - IoT Edge version: **0.1.0-beta10**
>    
>    Information om vad som är nytt i den här uppdateringen finns i [viktig information](azure-stack-edge-gpu-2101-release-notes.md).
> - Om du vill tillämpa 2101-uppdateringen måste enheten köra 2010.
> - Kom ihåg enheten startas om när du installerar en uppdatering eller korrigering. Den här uppdateringen innehåller enhets program uppdateringar och Kubernetes-uppdateringar. Med tanke på att Azure Stack Edge Pro är en enda Node-enhet avbryts alla i/O-åtgärder och enheten upplever en stillestånds tid på upp till 1,5 timmar för uppdateringen.

Om du vill installera uppdateringar på enheten måste du först konfigurera platsen för uppdaterings servern. När uppdaterings servern har kon figurer ATS kan du tillämpa uppdateringarna via Azure Portal gränssnittet eller det lokala webb gränssnittet.

Vart och ett av dessa steg beskrivs i följande avsnitt.

## <a name="configure-update-server"></a>Konfigurera uppdaterings Server

1. I det lokala webb gränssnittet går du till **konfigurations**  >  **uppdaterings Server**.
   
    ![Konfigurera uppdateringar 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. I **Välj uppdaterings Server typ**, i list rutan, väljer du från Microsoft Update Server (standard) eller Windows Server Update Services.  
   
    Om du uppdaterar från Windows Server Update Services anger du Server-URI. Servern vid denna URI kommer att distribuera uppdateringarna på alla enheter som är anslutna till den här servern.

    ![Konfigurera uppdateringar 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    WSUS-servern används för att hantera och distribuera uppdateringar via en hanterings konsol. En WSUS-server kan också vara uppdaterings källa för andra WSUS-servrar inom organisationen. WSUS-servern som agerar som en uppdateringskälla kallas för en uppströms-server. I en WSUS-implementering måste minst en WSUS-server i nätverket kunna ansluta till Microsoft Update för att hämta tillgänglig uppdaterings information. Som administratör kan du bestämma beroende på nätverks säkerhet och-konfiguration – hur många andra WSUS-servrar som ansluter direkt till Microsoft Update.
    
    Mer information finns i [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Vi rekommenderar att du installerar uppdateringar via Azure Portal. Enheten söker automatiskt efter uppdateringar en gång om dagen. När uppdateringarna är tillgängliga visas ett meddelande i portalen. Du kan då ladda ned och installera uppdateringarna.

> [!NOTE]
> Kontrol lera att enheten är felfri och att statusen visas som **online** innan du installerar uppdateringarna.

1. När uppdateringarna är tillgängliga för din enhet visas ett meddelande. Välj meddelandet eller från det övre kommando fältet, **Uppdatera enhet**. Detta gör att du kan tillämpa enhets program uppdateringar.

    ![Program varu version efter uppdatering](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. På bladet **enhets uppdateringar** kontrollerar du att du har granskat de licens villkor som är associerade med nya funktioner i viktig information.

    Du kan välja att **Ladda ned och installera** uppdateringarna eller bara **Hämta** uppdateringarna. Du kan sedan välja att installera uppdateringarna senare.

    ![Program varu version efter uppdatering 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Om du vill hämta och installera uppdateringarna markerar du alternativet som uppdateras automatiskt när hämtningen är klar.

    ![Program varu version efter uppdatering 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Hämtningen av uppdateringar startar. Du ser ett meddelande om att nedladdningen pågår.

    ![Program varu version efter uppdatering 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    En meddelande banderoll visas också i Azure Portal. Detta anger hämtnings förloppet.

    ![Program varu version efter uppdatering 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Du kan välja det här meddelandet eller välja **Uppdatera enhet** för att visa detaljerad status för uppdateringen.

    ![Program varu version efter uppdatering 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. När hämtningen är klar uppdateras meddelande banderollen för att visa att åtgärden har slutförts. Om du väljer att hämta och installera uppdateringarna startar installationen automatiskt.

    ![Program varu version efter uppdatering 7](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Om du väljer att bara hämta uppdateringar väljer du meddelandet för att öppna bladet med **enhets uppdateringar** . Välj **installera**.
  
    ![Program varu version efter uppdatering 8](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Du ser ett meddelande om att installationen pågår.

    ![Program varu version efter uppdatering 9](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    Portalen visar också en informations avisering som indikerar att installationen pågår. Enheten är offline och är i underhålls läge.
   
    ![Program varu version efter uppdatering 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Eftersom det här är en 1-nods enhet startas enheten om efter att uppdateringarna har installerats. Den kritiska varningen under omstarten innebär att enhetens pulsslag förloras.

    ![Program varu version efter uppdatering 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Välj aviseringen för att se motsvarande enhets händelse.
    
    ![Program varu version efter uppdatering 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Efter omstarten placeras enheten återigen i underhålls läge och en informations avisering visas för att indikera att.

    Om du väljer **Uppdatera enhet** i det övre kommando fältet kan du se förloppet för uppdateringarna.   

8. Enhets statusen uppdateras till **online** efter att uppdateringarna har installerats. 

    ![Program varu version efter uppdatering 13](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    Välj **enhets uppdateringar** från det övre kommando fältet. Kontrol lera att uppdateringen har installerats och att enhetens program varu version återspeglar detta.

    ![Program varu version efter uppdatering 14](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

<!--9. You will again see a notification that updates are available. These are the Kubernetes updates. Select the notification or select **Update device** from the top command bar.

    ![Software version after update 15](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Download the Kubernetes updates. You can see that the package size is different when compared to the previous update package.

    ![Software version after update 16](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    The process of installation is identical to that of device updates. First the updates are downloaded.

    ![Software version after update 17](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Once the updates are downloaded, you can then install the updates. 

    ![Software version after update 18](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    As the updates are installed, the device is put into maintenance mode. The device does not restart for the Kubernetes updates. -->

När enhetens program vara och Kubernetes-uppdateringar har installerats försvinner informations meddelandet. Enheten har nu den senaste versionen av enhets program och Kubernetes.


## <a name="use-the-local-web-ui"></a>Använd det lokala webb gränssnittet

Det finns två steg när du använder det lokala webb gränssnittet:

* Hämta uppdateringen eller snabb korrigeringen
* Installera uppdateringen eller snabb korrigeringen

Vart och ett av dessa steg beskrivs ingående i följande avsnitt.


### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabb korrigeringen

Utför följande steg för att ladda ned uppdateringen. Du kan hämta uppdateringen från den Microsoft-angivna platsen eller från Microsoft Update katalogen.


Utför följande steg för att ladda ned uppdateringen från Microsoft Update katalogen.

1. Starta webbläsaren och gå till [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Sökkatalog](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. I rutan Sök i Microsoft Updates katalogen anger du Knowledge Base-numret för snabb korrigeringen eller villkoren för den uppdatering som du vill ladda ned. Ange till exempel **Azure Stack Edge Pro** och klicka sedan på **Sök**.
   
    Uppdaterings listan visas som **Azure Stack Edge update 2101**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Välj **Hämta**. Det finns två filer att ladda ned med *SoftwareUpdatePackage.exe* och *Kubernetes_Package.exe* suffix som motsvarar enhets program uppdateringar respektive Kubernetes uppdateringar. Ladda ned filerna till en mapp på det lokala systemet. Du kan också kopiera mappen till en nätverks resurs som kan kontaktas från enheten.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabb korrigeringen

Innan du installerar uppdateringen eller hotfixen bör du kontrol lera att:

 - Du har uppdateringen eller snabb korrigeringen som hämtats antingen lokalt på värden eller kan nås via en nätverks resurs.
 - Enhetens status är felfri så som visas på sidan **Översikt** i det lokala webb gränssnittet.

   ![uppdatera enhet](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Den här proceduren tar cirka 20 minuter att slutföra. Utför följande steg för att installera uppdateringen eller snabb korrigeringen.

1. I det lokala webb gränssnittet går du till **Underhåll**  >  **program uppdatering**. Anteckna den program varu version som du kör. 
   
   ![uppdatera enhet 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Ange sökvägen till uppdaterings filen. Du kan också bläddra till installations filen för uppdateringen om den placeras på en nätverks resurs. Välj program uppdaterings filen med *SoftwareUpdatePackage.exe* suffix.

   ![uppdatera enhet 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Välj **Använd**.

   ![uppdatera enhet 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. När du uppmanas att bekräfta, väljer du **Ja** för att fortsätta. Om enheten är en enskild nod enhet startas enheten om när uppdateringen har installerats och det finns avbrott. 
   
   ![uppdatera enhet 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användar gränssnittet är inte tillgängligt under denna varaktighet.
   
6. När omstarten är klar tas du till **inloggnings** sidan. Kontrol lera att enhetens program vara har uppdaterats genom att gå till **Underhåll**  >  **program uppdatering** i det lokala webb gränssnittet. Den program varu version som visas ska vara **Azure Stack Edge 2101** för den aktuella versionen. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Du kommer nu att uppdatera Kubernetes-program versionen. Upprepa stegen ovan. Ange en sökväg till Kubernetes-uppdaterings filen med *Kubernetes_Package.exe* suffixet.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Välj **tillämpa uppdatering**.

   ![uppdatera enhet 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. När du uppmanas att bekräfta, väljer du **Ja** för att fortsätta.

10. När Kubernetes-uppdateringen har installerats sker ingen ändring av den program vara som visas i **underhålls**  >  **program uppdateringen**.


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [du administrerar Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).