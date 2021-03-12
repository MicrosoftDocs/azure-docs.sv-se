---
title: Självstudie för webb program – Skriv webb program
description: Den här självstudien vägleder dig genom ett exempel på distribution av ett enkelt webb program. Det här avsnittet i guiden beskriver hur du skriver webb programmet.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020771"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Skriv Azure-webbprogram för att läsa FHIR-data
Nu när du har möjlighet att ansluta till din FHIR-Server och publicera data är du redo att skriva ett webb program som läser FHIR-data. I det sista steget i själv studie kursen kommer vi att gå igenom Skriv-och åtkomst till webb programmet.

## <a name="create-web-application"></a>Skapa webb program
I Azure väljer du **skapa en resurs** och väljer sedan **webbapp**. Se till att namnge ditt webb program oavsett om du har angett i omdirigerings-URI för ditt klient program eller gå tillbaka och uppdatera omdirigerings-URI: n med det nya namnet. 

![Skapa webb program](media/tutorial-web-app/create-web-app.png)

När webb programmet är tillgängligt **går du till resurs**. Välj **App Service Editor (för hands version)** under utvecklingsverktyg till höger och välj sedan **gå**. Om du väljer go öppnas App Service Editor. Högerklicka på det grå utrymmet under *utforska* och skapa en ny fil med namnet **index.html**.

Nedan visas den kod som du kan mata in i **index.html**. Du måste uppdatera följande objekt:
* **clientId** – uppdatera med ditt klient program-ID. Detta ID är samma ID som du hämtade när du hämtade din token
* **auktoritet** – uppdatera med ditt Azure AD-klient-ID
* **FHIRendpoint** – uppdatera FHIRENDPOINT till FHIR-tjänstens namn
* **omfattningar** – uppdatera för att återspegla den fullständiga URL: en för din mål grupp

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Härifrån kan du gå tillbaka till din webb program resurs och öppna webb adressen som finns på översikts sidan. Logga in om du vill se patient Jonas Tiberious-Kirks som du skapade tidigare.

## <a name="next-steps"></a>Nästa steg
Du har distribuerat Azure API för FHIR, registrerat ett offentligt klient program, testad åtkomst och skapat ett litet webb program. Kolla in Azure API för FHIR-funktioner som stöds som nästa steg.

>[!div class="nextstepaction"]
>[Funktioner som stöds](fhir-features-supported.md)





