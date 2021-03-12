---
title: Självstudie om webbapp – testa att ansluta till Azure API för FHIR
description: De här självstudierna beskriver ett exempel på hur du distribuerar ett enkelt webb program. I det här avsnittet av själv studie kursen får du hjälp med att testa att ansluta till FHIR-servern med Postman
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019942"
---
# <a name="testing-the-fhir-api"></a>Testa FHIR-API: et
I de föregående två stegen har du distribuerat Azure API för FHIR och registrerat ditt klient program. Nu är du redo att testa att ditt Azure API för FHIR har kon figurer ATS med klient programmet. 

## <a name="retrieve-capability-statement"></a>Hämta kapacitets instruktion
Först får vi kapacitets satsen för ditt Azure API för FHIR. 
1. Öppna Postman
1. Hämta kapacitets instruktionen genom att göra GET https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/metadata. I bilden nedan är namnet på FHIR-servern **fhirserver**.

![Kapacitets instruktion](media/tutorial-web-app/postman-capability-statement.png)

Nästa försök att hämta en patient görs. Hämta en patient genom att ange GET https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/patient. Du får ett 401-otillåtet fel. Det här felet beror på att du inte har beprövat att du ska ha åtkomst till patient data.

## <a name="get-patient-from-fhir-server"></a>Hämta patient från FHIR Server
![Misslyckad patient](media/tutorial-web-app/postman-patient-authorization-failed.png)

För att få åtkomst måste du ha en åtkomsttoken.
1. I Postman väljer du **auktorisering** och anger typen till **OAuth 2.0**
1. Välj **Hämta ny åtkomsttoken**
1. Fyll i fälten och välj **token för begäran**. Nedan kan du se värdena för varje fält för den här självstudien.

|Fält                |Värde                                                               |
|---------------------|--------------------------------------------------------------------|
|Token Name (Tokennamn)           |Ett namn för din token                                               |
|Grant Type (Typ av beviljande)           |Auktoriseringskod                                                  |
|Callback URL (Webbadress för återanrop)         |https://www.getpostman.com/oauth2/callback                          |
|Auth URL (Auktoriseringswebbadress)             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/OAuth2/? Resource = https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com|
|Access Token URL (Webbadress för åtkomsttoken)     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/token|
|Klient-ID            |Det klient-ID som du kopierade i föregående steg             |
|Client Secret (Klienthemlighet)        |\<BLANK>                                                            |
|Omfång                |\<BLANK>                                                            |
|Stat                |1234                                                                |
|Klientautentisering|Skicka klientautentiseringsuppgifter i brödtext                                     |

4. Logga in med dina autentiseringsuppgifter och välj **Godkänn**
1. Rulla ned i resultatet och välj **Använd token**
1. Välj **Skicka** igen överst och den här gången bör du få ett ![ lyckat resultat för lyckad patient](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Publicera patient i FHIR-servern
Nu när du har åtkomst kan du skapa en ny patient. Här är ett exempel på en enkel patient som du kan lägga till i din FHIR-Server. Ange koden nedan i avsnittet **brödtext** i Postman.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
I det här inlägget skapas en ny patient i FHIR-servern med namnet Jonas Tiberious Kirks.
![Publicera patient](media/tutorial-web-app/postman-post-patient.png)

Om du gör steget Hämta ovan för att hämta en patient igen, kommer du att se Jonas Tiberious-Kirks som visas i utdata.

## <a name="troubleshooting-access-issues"></a>Felsöka åtkomstproblem
Om du stöter på problem under något av de här stegen kan du granska dokumenten som vi har samlat in på Azure Active Directory och Azure API för FHIR. 

* [Azure AD och Azure API för FHIR](azure-ad-hcapi.md) – det här dokumentet beskriver några av de grundläggande principerna för Azure Active Directory och hur det interagerar med Azure-API: t för FHIR.
* [Verifiering av åtkomst-token](azure-ad-hcapi-token-validation.md) – den här instruktions guiden ger mer detaljerad information om verifiering av åtkomst-token och steg som krävs för att lösa åtkomst problem.

## <a name="next-steps"></a>Nästa steg
Nu när du har lyckats ansluta till klient programmet är du redo att skriva ditt webb program.

>[!div class="nextstepaction"]
>[Skriv ett webb program](tutorial-web-app-write-web-app.md)



