---
title: Hantera enhets registreringar för Azure IoT Hub Device Provisioning Service i Azure Portal
description: Hantera enhets registreringar för enhets etablerings tjänsten (DPS) i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010970"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Hantera enhets registreringar med Azure Portal

En *enhets registrering* skapar en post för en enskild enhet eller en grupp av enheter som kan komma att registreras med Azure-IoT Hub Device Provisioning service. Registrerings posten innehåller den inledande konfigurationen för enheten eller enheterna som en del av registreringen. Som ingår i konfigurationen är IoT-hubben som en enhet tilldelas till eller en resursallokeringsprincip som konfigurerar hubben från en uppsättning hubbar. Den här artikeln visar hur du hanterar enhets registreringar för etablerings tjänsten.


## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering

Det finns två sätt att registrera enheter med etablerings tjänsten:

* En **registrerings grupp** är en post för en grupp av enheter som delar en gemensam mekanism för attestering. Vi rekommenderar att du använder en registrerings grupp för ett stort antal enheter som delar en inledande konfiguration, eller för enheter som alla kommer till samma klient. Enheter som använder [symmetrisk nyckel](concepts-symmetric-key-attestation.md) eller [X. 509-certifikat](concepts-x509-attestation.md) stöds. 

    Stegvisa instruktioner om hur du skapar och använder registrerings grupper med symmetriska nycklar finns i själv studie kursen [etablera enheter med symmetriska nycklar](how-to-legacy-device-symm-key.md) .

    Du skapar en registrerings grupp i portalen för en grupp av enheter med hjälp av följande steg:

    1. Logga in på Azure Portal och klicka på **alla resurser** på den vänstra menyn.  
    1. Klicka på den enhets etablerings tjänst som du vill registrera enheten på från listan över resurser.  
    1. I etablerings tjänsten klickar du på **Hantera registreringar** och klickar sedan på knappen **Lägg till registrerings grupp** överst.  
     
        ![Registrerings grupp i portalen](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. När panelen "Lägg till registrerings grupp" visas anger du informationen för din registrering och klickar på **Spara**.  
     
        [![Lägg till en registrerings grupp med portalen](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Fält | Beskrivning |
        | :--- | :--- |
        | **Gruppnamn** | Det namn som krävs för din enhets grupp. |
        | **Attesterings typ** | Klicka på **certifikat** eller **symmetrisk nyckel** för attesterings typen beroende på vilken attesterings metod som enheterna ska använda. |
        | **Certifikat typ** | Tillgängligt om du använder certifikat attestering. Välj **ca-certifikat** eller **mellanliggande** baserat på vilket certifikat som signerade enhets certifikaten. |
        | **Primärt certifikat** | Om du signerar dina enhets certifikat med ett certifikat från en rot certifikat UTFÄRDAre, måste rot certifikat utfärdaren ha [bevis på innehavet](how-to-verify-certificates.md) . Sedan kan du välja det som **primärt certifikat** för enhets gruppen.<br><br>Om du signerar dina enhets certifikat med ett mellanliggande certifikat är knappen Ladda upp tillgänglig så att du kan överföra ditt mellanliggande certifikat. Det certifikat som undertecknade mellanliggande måste också ha [bevis på innehav](how-to-verify-certificates.md) som har slutförts. |

        
    

* En **enskild registrering** är en post för en enskild enhet som kan tilldelas till en IoT-hubb. Enheter som använder [symmetrisk nyckel](concepts-symmetric-key-attestation.md), [X. 509-certifikat](concepts-x509-attestation.md)och [TPM-attestering](concepts-tpm-attestation.md) stöds. 

    Du kan skapa en enskild registrering i portalen med hjälp av följande steg:

    1. Logga in på Azure Portal och klicka på **alla resurser** på den vänstra menyn.
    1. Klicka på den enhets etablerings tjänst som du vill registrera enheten på från listan över resurser.
    1. I etablerings tjänsten klickar du på **Hantera registreringar** och klickar sedan på knappen **Lägg till individuell registrering** överst.   

       [![Lägg till en enskild registrering i portalen](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. När panelen "Lägg till registrering" visas anger du informationen för enskilda enhets registreringar och klickar på **Spara**. 
     
        [![Individuell registrering i portalen](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Fält | Beskrivning |
        | :--- | :--- |
        | **Mekanism** | Välj **X. 509**, **TPM** eller **symmetrisk nyckel** för mekanismen som ska användas beroende på vilken attesterings metod som enheterna ska använda. |
        | Inställningar för attestering | Stegvisa instruktioner om hur du skapar och använder enskilda registreringar med symmetriska nycklar eller X. 509-certifikat finns i en av de [tillhandahållna en symmetrisk enhet](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) eller [etablera en X. 509 certifikats enhets](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) snabb starter.<br><br>Stegvisa instruktioner om hur du skapar och använder enskilda registreringar med TPM-attestering finns i en av de [tillhandahållna en simulerad TPM-enhet](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) exempel.|
        | **IoT Hub enhets-ID** |  Detta ID representerar din enhet. Det måste följa reglerna för ett enhets-ID. Mer information finns i [Egenskaper för enhets identitet](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>När du använder X. 509-certifikat måste den här texten vara ämnes namnet för enhets certifikatet som du laddar upp för registreringen. Ämnes namnet måste följa reglerna för ett enhets-ID.|
            


## <a name="update-an-enrollment-entry"></a>Uppdatera en registrerings post
Du kan uppdatera en befintlig registrerings post i portalen med hjälp av följande steg:

1. Öppna enhets etablerings tjänsten i Azure Portal och klicka på **Hantera registreringar**. 
1. Navigera till den registrerings post som du vill ändra. Klicka på posten, som öppnar en sammanfattnings information om din enhets registrering. 
1. På den här sidan kan du ändra andra objekt än säkerhets typ och autentiseringsuppgifter, till exempel den IoT-hubb som enheten ska länkas till och enhets-ID: t. Du kan också ändra den inledande enhetens dubbla tillstånd. 
1. När du är klar klickar du på **Spara** för att uppdatera enhets registreringen. 

    ![Uppdatera registreringen i portalen](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Ta bort en enhets registrering
I de fall där enhet (er) inte behöver tillhandahållas till någon IoT-hubb kan du ta bort posten för den relaterade registreringen i portalen med hjälp av följande steg:

1. Öppna enhets etablerings tjänsten i Azure Portal och klicka på **Hantera registreringar**. 
1. Navigera till och välj den registrerings post som du vill ta bort. 
1. Klicka på knappen **ta bort** högst upp och välj sedan **Ja** när du uppmanas att bekräfta. 
1. När åtgärden har slutförts ser du att posten tas bort från listan över enhets registreringar. 
 
    ![Ta bort registreringen i portalen](./media/how-to-manage-enrollments/remove-enrollment.png)