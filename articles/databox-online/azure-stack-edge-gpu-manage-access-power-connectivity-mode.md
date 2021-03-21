---
title: Azure Stack Edge Pro GPU-enhetens åtkomst, ström och anslutnings läge | Microsoft Docs
description: Beskriver hur du hanterar åtkomst, energi och anslutnings läge för den Azure Stack Edge Pro GPU-enhet som hjälper till att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443122"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Hantera åtkomst, energi och anslutnings läge för Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du hanterar åtkomst, ström och anslutnings läge för din Azure Stack Edge Pro med GPU-enhet. De här åtgärderna utförs via det lokala webb gränssnittet eller Azure Portal.

Den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera enhets åtkomst
> * Hantera företagsresurser
> * Hantera anslutnings läge
> * Hantera energi


## <a name="manage-device-access"></a>Hantera enhets åtkomst

Åtkomsten till din Azure Stack Edge Pro-enhet styrs av användningen av ett enhets lösen ord. Du kan ändra lösen ordet via det lokala webb gränssnittet. Du kan också återställa enhetens lösen ord i Azure Portal.

Åtkomsten till data på enhets diskarna styrs också av kryptering vid vila-nycklar.

### <a name="change-device-password"></a>Ändra enhetens lösenord

Följ de här stegen i det lokala användar gränssnittet för att ändra enhetens lösen ord.

1. I det lokala webb gränssnittet går du till **underhåll > lösen ord**.
2. Ange det aktuella lösen ordet och sedan det nya lösen ordet. Det angivna lösen ordet måste innehålla mellan 8 och 16 tecken. Lösen ordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Bekräfta det nya lösen ordet.

    ![Ändra lösenord](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Välj **ändra lösen ord**.
 
### <a name="reset-device-password"></a>Återställ enhets lösen ord

Återställnings arbets flödet kräver inte att användaren återkallar det gamla lösen ordet och är användbart när lösen ordet går förlorat. Det här arbets flödet utförs i Azure Portal.

1. I Azure Portal går du till **översikt > Återställ administratörs lösen ord**.

    ![Skärm bild som visar enheten med återställt enhets lösen ord valt.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Ange det nya lösen ordet och bekräfta det sedan. Det angivna lösen ordet måste innehålla mellan 8 och 16 tecken. Lösen ordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Välj **Återställ**.

    ![Återställ lösen ord 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Hantera åtkomst till enhets data

För Azure Stack Edge Pro R-och Azure Stack Edge Mini R-enheter styrs åtkomsten till enhets data med hjälp av kryptering – vid-rest-nycklar för enhets enheterna. När du har konfigurerat enheten för kryptering vid vila blir alternativet för att rotera kryptering vid vila tillgängligt i enhetens lokala användar gränssnitt. 

Med den här åtgärden kan du ändra nycklarna för BitLocker `HcsData` -volymer och `HcsInternal` alla Självkrypterande enheter på enheten.

Följ dessa steg om du vill rotera nycklarna för kryptering vid vila.

1. I enhetens lokala användar gränssnitt går du till sidan **Kom igång** . På panelen **säkerhet** väljer du **kryptering-at-rest: alternativet Rotera nycklar** . Det här alternativet är bara tillgängligt när du har konfigurerat nycklar för kryptering vid vila.

    ![Välj Rotera nycklar för kryptering – vila på sidan kom igång](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Du kan använda dina egna BitLocker-nycklar eller använda systemgenererade nycklar.  

    Om du vill ange en egen nyckel anger du en 32 tecken lång Base-64-kodad sträng. Inmatarna liknar det du angav när du konfigurerade kryptering vid vila för första gången.

    ![Ta med din egen kryptering – i vila-nyckel](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    Du kan också välja att använda en systemgenererad nyckel.

    ![Använd systemgenererad kryptering vid vila-nyckel](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Välj **Använd**. Nyckel skydd roteras.

    ![Använd den nya krypteringen – vid-rest-nyckeln](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. När du uppmanas att ladda ned och spara nyckel filen väljer du **Hämta och fortsätt**. 

    ![Hämta och fortsätt nyckel filen](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Spara `.json` nyckel filen på en säker plats. Den här filen används för att under lätta en eventuell framtida återställning av enheten.

    ![Skärm bild som visar dialog rutan Återställ enhets lösen ord.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Hantera företagsresurser

Om du vill skapa Azure Stack Edge/Data Box Gateway, IoT Hub och Azure Storage resurs måste du ha behörighet som deltagare eller högre på resurs grupps nivå. Du måste också ha motsvarande resurs leverantörer för att kunna registreras. För alla åtgärder som inbegriper aktiverings nyckel och autentiseringsuppgifter, krävs även behörighet till Microsoft Graph-API. Dessa beskrivs i följande avsnitt. 

### <a name="manage-microsoft-graph-api-permissions"></a>Hantera Microsoft Graph API-behörigheter

När du genererar aktiverings nyckeln för Azure Stack Edge Pro-enheten, eller utföra åtgärder som kräver autentiseringsuppgifter, måste du ha behörighet att Azure Active Directory Graph API. De åtgärder som behöver autentiseringsuppgifter kan vara:

-  Skapa en resurs med ett associerat lagrings konto.
-  Skapa en användare som har åtkomst till resurserna på enheten.

Du bör ha `User` åtkomst till Active Directory klient organisation som du behöver kunna `Read all directory objects` . Du kan inte vara gäst användare eftersom de inte har behörighet till `Read all directory objects` . Om du är gäst, kommer åtgärder som att generera en aktiverings nyckel att skapa en resurs på din Azure Stack Edge Pro-enhet, skapa en användare, konfiguration av Edge Compute-rollen och återställa enhetens lösen ord.

Mer information om hur du ger åtkomst till användare till Microsoft Graph API finns i [referens för Microsoft Graph behörigheter](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrera resurs leverantörer

Om du vill etablera en resurs i Azure (i Azure Resource Manager-modellen) behöver du en resurs leverantör som stöder skapandet av resursen. Om du till exempel vill etablera en virtuell dator bör du ha en Resource-Provider för Microsoft. Compute som är tillgänglig i prenumerationen.
 
Resursprovidrar registreras på prenumerationsnivån. Alla nya Azure-prenumerationer är som standard förregistrerade med en lista över vanliga resursprovidrar. Resurs leverantören för Microsoft. DataBoxEdge ingår inte i den här listan.

Du behöver inte bevilja åtkomst behörigheter till prenumerations nivån för att användarna ska kunna skapa resurser som "Microsoft. DataBoxEdge" i deras resurs grupper som de har ägar rättigheter på, så länge resurs leverantörerna för dessa resurser redan har registrerats.

Innan du försöker skapa en resurs måste du kontrol lera att resurs leverantören är registrerad i prenumerationen. Om resurs leverantören inte är registrerad måste du kontrol lera att användaren som skapar den nya resursen har tillräckligt med behörighet för att registrera den nödvändiga resurs leverantören på prenumerations nivån. Om du inte har gjort det kan du se följande fel:

*Prenumerationen \<Subscription name> har inte behörighet att registrera resurs leverantör (er): Microsoft. DataBoxEdge.*


Om du vill hämta en lista över registrerade resurs leverantörer i den aktuella prenumerationen kör du följande kommando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

För Azure Stack Edge Pro-enhet `Microsoft.DataBoxEdge` ska registreras. För att registrera dig `Microsoft.DataBoxEdge` bör prenumerations administratören köra följande kommando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Mer information om hur du registrerar en resurs leverantör finns i [lösa fel för registrering av resurs leverantör](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Hantera anslutnings läge

Förutom det fullständigt anslutna standard läget kan enheten också köras i delvis ansluten eller helt frånkopplat läge. Vart och ett av dessa lägen beskrivs nedan:

- **Fullständigt ansluten** – detta är det normala standard läget där enheten fungerar. Både moln överföring och hämtning av data är aktiverat i det här läget. Du kan använda Azure Portal eller det lokala webb gränssnittet för att hantera enheten.

- **Delvis frånkopplad** – i det här läget kan enheten inte ladda upp eller ladda ned några delnings data, men de kan hanteras via Azure Portal.

    Det här läget används vanligt vis i ett avgiftsbelagdt satellit nätverk och målet är att minimera användningen av nätverks bandbredd. Minimal nätverks förbrukning kan fortfarande uppstå för enhets övervaknings åtgärder.

- **Frånkopplad** – i det här läget är enheten helt frånkopplad från molnet och både moln överföringar och hämtningar är inaktiverade. Enheten kan bara hanteras via det lokala webb gränssnittet.

    Det här läget används vanligt vis när du vill ta enheten offline.

Följ dessa steg om du vill ändra enhets läge:

1. I enhetens lokala webb gränssnitt går du till **konfiguration > molnet**.
2. I list rutan väljer du det läge som du vill att enheten ska köras i. Du kan välja från **fullständigt anslutna**, **delvis anslutna** och **helt frånkopplade**. Aktivera **Azure Portal hantering** för att köra enheten i delvis frånkopplat läge.

 
## <a name="manage-power"></a>Hantera energi

Du kan stänga av eller starta om den fysiska enheten med hjälp av det lokala webbgränssnittet. Innan du startar om rekommenderar vi att du tar ned resurserna offline på dataservern och sedan enheten. Den här åtgärden minskar risken för skadade data.

1. I det lokala webb gränssnittet går du till **underhåll >s kraft**.
2. Välj **Stäng** av eller **starta om** beroende på vad du vill göra.

    ![Energiinställningar](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta, väljer du **Ja** för att fortsätta.

> [!NOTE]
> Om du stänger av den fysiska enheten måste du aktivera ström knappen på enheten för att aktivera den.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar resurser](azure-stack-edge-manage-shares.md).