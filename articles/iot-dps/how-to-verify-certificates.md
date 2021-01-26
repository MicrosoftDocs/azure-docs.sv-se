---
title: Verifiera X. 509 CA-certifikat med Azure IoT Hub Device Provisioning Service
description: Så här gör du ett certifikat för X. 509 CA-certifikat med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2020205357c68e007a1a375c4853e8c04afd2019
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789068"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Så här gör du ett certifikat för X. 509 CA-certifikat med enhets etablerings tjänsten

Ett verifierat X. 509 certifikat från certifikat utfärdare (CA) är ett CA-certifikat som har laddats upp och registrerats för etablerings tjänsten och som har genomgått tjänsten. 

Du måste utföra följande steg:
1. Hämta en unik verifierings kod som genereras av etablerings tjänsten för ditt X. 509 CA-certifikat. Du kan göra detta från Azure Portal.
2. Skapa ett verifierings certifikat för X. 509 med verifierings koden som ämne och signera certifikatet med den privata nyckel som är kopplad till ditt X. 509 CA-certifikat.
3. Ladda upp det signerade verifierings certifikatet till tjänsten. Tjänsten verifierar verifierings certifikatet med hjälp av den offentliga delen av CA-certifikatet som ska verifieras, vilket styrker att du har till gång till certifikat utfärdarens privata nyckel.

Verifierade certifikat spelar en viktig roll när du använder registrerings grupper. Verifierar att certifikat ägarskapet ger ett extra säkerhets lager genom att se till att omladdningen av certifikatet är i besittning av certifikatets privata nyckel. Verifiering hindrar en skadlig aktör att identifiera din trafik från att extrahera ett mellanliggande certifikat och använda certifikatet för att skapa en registrerings grupp i sin egen etablerings tjänst, vilket skapar en effektiv kapning av dina enheter. Genom att bevisa ägande rätt till roten eller ett mellanliggande certifikat i en certifikat kedja, bevisa du att du har behörighet att generera löv certifikat för de enheter som ska registreras som en del av den registrerings gruppen. Därför måste rot-eller mellanliggande certifikat som kon figurer ATS i en registrerings grupp antingen vara ett verifierat certifikat eller måste sammanställas till ett verifierat certifikat i certifikat kedjan som en enhet presenterar när den autentiserar med tjänsten. Om du vill veta mer om X. 509 certifikat attestering, se [X. 509-certifikat](concepts-x509-attestation.md) och [kontrol lera enhets åtkomst till etablerings tjänsten med X. 509-certifikat](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Registrera den offentliga delen av ett X. 509-certifikat och få en verifierings kod

Följ dessa steg om du vill registrera ett CA-certifikat med din etablerings tjänst och få en verifierings kod som du kan använda. 

1. I Azure Portal navigerar du till etablerings tjänsten och öppnar **certifikat** från den vänstra menyn. 
2. Klicka på **Lägg** till för att lägga till ett nytt certifikat.
3. Ange ett eget visnings namn för ditt certifikat. Bläddra till CER-eller PEM-filen som representerar den offentliga delen av ditt X. 509-certifikat. Klicka på **Överför**.
4. När du får ett meddelande om att certifikatet har laddats upp klickar du på **Spara**.

    ![Överför certifikat](./media/how-to-verify-certificates/add-new-cert.png)  

   Ditt certifikat visas i listan **certifikat Utforskaren** . Observera att **status** för det här certifikatet inte är *verifierad*.

5. Klicka på det certifikat som du lade till i föregående steg.

6. I **certifikat information** klickar du på **generera verifierings kod**.

7. Etablerings tjänsten skapar en **verifierings kod** som du kan använda för att validera certifikatets ägarskap. Kopiera koden till Urklipp. 

   ![Verifiera certifikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Signera verifierings koden digitalt för att skapa ett verifierings certifikat

Nu måste du signera *verifierings koden* med den privata nyckeln som är kopplad till ditt X. 509-CA-certifikat, vilket genererar en signatur. Detta är känt som [bevis på innehavet](https://tools.ietf.org/html/rfc5280#section-3.1) och resulterar i ett signerat verifierings certifikat.

Microsoft tillhandahåller verktyg och exempel som kan hjälpa dig att skapa ett signerat verifierings certifikat: 

- **Azure IoT Hub C SDK** innehåller PowerShell-skript (Windows) och bash (Linux) som hjälper dig att skapa ca-och löv certifikat för utveckling och för att utföra sitt eget skydd med hjälp av en verifierings kod. Du kan ladda ned de [filer](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) som är relevanta för ditt system till en arbetsmapp och följa instruktionerna i Readme-filen för certifikat [utfärdare](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) för att utföra ett ca-certifikats äkthets bevis. 
- **Azure IoT Hub C# SDK** innehåller exempel på [verifiering av grupp certifikat](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), som du kan använda för att göra en egen användning.
 
> [!IMPORTANT]
> Förutom att du behöver göra det kan du använda PowerShell-och bash-skripten tidigare, så att du även kan skapa rot certifikat, mellanliggande certifikat och löv certifikat som kan användas för att autentisera och etablera enheter. Dessa certifikat bör endast användas för utveckling. De bör aldrig användas i en produktions miljö. 

PowerShell-och bash-skripten som anges i dokumentationen och SDK: er förlitar sig på [openssl](https://www.openssl.org/). Du kan också använda OpenSSL eller andra verktyg från tredje part för att hjälpa dig att göra ditt eget bruk. Ett exempel på hur du använder verktyg som medföljer SDK: er finns i [skapa en X. 509-certifikat kedja](tutorial-custom-hsm-enrollment-group-x509.md#create-an-x509-certificate-chain). 


## <a name="upload-the-signed-verification-certificate"></a>Ladda upp det signerade verifierings certifikatet

1. Överför den resulterande signaturen som ett verifierings certifikat till etablerings tjänsten i portalen. I **certifikat information** på Azure Portal använder du ikonen för  _Utforskaren_ bredvid fältet **verifierings certifikat. pem eller CER-fil** för att överföra det signerade verifierings certifikatet från systemet.

2. När certifikatet har laddats upp klickar du på **Verifiera**. **Status** för ditt certifikat ändras till **_verifierat_** i listan **certifikat Utforskaren** . Klicka på **Uppdatera** om den inte uppdateras automatiskt.

   ![Ladda upp certifikat verifiering](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder portalen för att skapa en registrerings grupp finns i [Hantera enhets registreringar med Azure Portal](how-to-manage-enrollments.md).
- Information om hur du använder tjänst-SDK: er för att skapa en registrerings grupp finns i [Hantera enhets registreringar med service SDK](how-to-manage-enrollments-sdks.md): er.










