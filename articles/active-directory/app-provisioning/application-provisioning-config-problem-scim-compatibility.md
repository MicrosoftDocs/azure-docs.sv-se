---
title: Kända problem med system för SCIM (Cross-Domain Identity Management) 2,0 Protocol-kompatibilitet – Azure AD
description: Så här löser du vanliga kompatibilitetsproblem med att lägga till ett program som inte är en galleri som stöder SCIM 2,0 till Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256906"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Kända problem och lösningar med SCIM 2,0 protokoll kompatibilitet för Azure AD-tjänsten för användar etablering

Azure Active Directory (Azure AD) kan automatiskt etablera användare och grupper för program eller system som är uppkopplade av en webb tjänst med det gränssnitt som definierats i [systemet för scim (Cross-Domain Identity Management) 2,0 protokoll specifikation](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Azure AD: s stöd för SCIM 2,0-protokollet beskrivs i [använda system för scim (Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](use-scim-to-provision-users-and-groups.md), som listar de delar av protokollet som det implementerar för att automatiskt etablera användare och grupper från Azure AD till program som stöder scim 2,0.

I den här artikeln beskrivs aktuella och tidigare problem med Azure AD-tjänsten för användar etablering i SCIM 2,0-protokollet och hur du kan lösa problemen.

## <a name="understanding-the-provisioning-job"></a>Förstå etablerings jobbet
Etablerings tjänsten använder begreppet jobb för att arbeta med ett program. Du hittar jobID i [förlopps indikatorn](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Alla nya etablerings program skapas med en jobID som börjar med "scim". Scim-jobbet representerar tjänstens aktuella tillstånd. Äldre jobb har ID "customappsso". Det här jobbet representerar tjänstens status i 2018. 

Om du använder ett program i galleriet innehåller jobbet vanligt vis namnet på appen (t. ex. zoomning av snö, dataBricks osv.). Du kan hoppa över den här dokumentationen när du använder ett galleri program. Detta gäller främst för program som inte är gallerier med jobID SCIM eller customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2,0-kompatibilitetsproblem och status
I tabellen nedan innebär ett objekt som marker ATS som fast att du kan hitta rätt beteende i SCIM-jobbet. Vi har arbetat med att garantera bakåtkompatibilitet för de ändringar vi har gjort. Vi rekommenderar dock inte att implementera det gamla beteendet. Vi rekommenderar att du använder det nya beteendet för nya implementeringar och uppdaterar befintliga implementeringar.

> [!NOTE]
> För de ändringar som gjorts i 2018 kan du återgå till customappsso-beteendet. För de ändringar som gjorts sedan 2018 kan du använda URL: erna för att återgå till det äldre beteendet. Vi har arbetat för att säkerställa bakåtkompatibiliteten med de ändringar som vi har gjort genom att låta dig återställa tillbaka till den gamla jobID eller genom att använda en flagga. Som tidigare nämnts rekommenderar vi dock inte att implementera det gamla beteendet. Vi rekommenderar att du använder det nya beteendet för nya implementeringar och uppdaterar befintliga implementeringar.

| **SCIM 2,0-kompatibilitetsproblem** |  **Fastsatt?** | **Åtgärds datum**  |  **Bakåtkompatibilitet** |
|---|---|---|
| Azure AD kräver att "/scim" finns i roten för programmets SCIM-slutpunkts-URL  | Ja  |  18 december 2018 | nedgradera till customappSSO |
| Attributen för tillägg använder punkt "."-notation före attributnamn i stället för kolon ":"-notation |  Ja  | 18 december 2018  | nedgradera till customappSSO |
| Uppdaterings begär Anden för flervärdesattribut innehåller ogiltig syntax för Sök vägs filter | Ja  |  18 december 2018  | nedgradera till customappSSO |
| Begär Anden om att skapa grupper innehåller en ogiltig schema-URI | Ja  |  18 december 2018  |  nedgradera till customappSSO |
| Uppdatera KORRIGERINGs beteende för att säkerställa efterlevnad (t. ex. aktiv som boolesk och korrekt borttagning av grupp medlemskap) | Inga | TBD| Använd förhands gransknings flagga |

## <a name="flags-to-alter-the-scim-behavior"></a>Flaggor för att ändra SCIM-beteendet
Använd flaggorna nedan i klient-URL: en för ditt program för att ändra standard klient beteendet för SCIM.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM flaggor till ett senare beteende.":::

* Använd följande URL för att uppdatera KORRIGERINGs beteendet och se till att SCIM kompatibilitet (t. ex. aktiv som boolesk och korrekt borttagning av grupp medlemskap). Det här beteendet är för närvarande endast tillgängligt när du använder-flaggan, men kommer att bli standard beteendet under de kommande månaderna. OBS! den här förhands gransknings flaggan fungerar för närvarande inte med etablering på begäran. 
  * **URL (scim-kompatibel):** AzureAdScimPatch062020
  * **SCIM RFC-referenser:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Ovänta**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Nedgraderings-URL:** När det nya SCIM-kompatibla beteendet blir standardvärdet i det program som inte är galleriet kan du använda följande URL för att återställa till det gamla, icke SCIM-kompatibla beteendet: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Uppgradera från det äldre customappsso-jobbet till SCIM-jobbet
Följ stegen nedan för att ta bort ditt befintliga customappsso-jobb och skapa ett nytt scim-jobb. 
 
1. Logga in på Azure Portal på https://portal.azure.com .
2. I avsnittet **Azure Active Directory > företags program** i Azure Portal letar du reda på och väljer ditt befintliga scim-program.
3. I avsnittet **Egenskaper** i din befintliga scim-app kopierar du **objekt-ID: t**.
4. I ett nytt webbläsarfönster går du till https://developer.microsoft.com/graph/graph-explorer och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att hitta ID: t för ditt etablerings jobb. Ersätt "[Object-ID]" med tjänstens huvud namns-ID (objekt-ID) som har kopierats från det tredje steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Hämta jobb](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Hämta jobb") 


6. I resultatet kopierar du den fullständiga "ID"-strängen som börjar med antingen "customappsso" eller "scim".
7. Kör kommandot nedan för att hämta konfigurationen för attributet-mappning så att du kan göra en säkerhets kopia. Använd samma [objekt-ID] som tidigare och Ersätt [Job-ID] med etablerings jobbets ID som har kopierats från det sista steget.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Hämta schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Hämta schema") 

8. Kopiera JSON-utdata från det sista steget och spara den i en textfil. JSON innehåller eventuella anpassade attribut mappningar som du har lagt till i din gamla app och bör vara cirka några tusen rader med JSON.
9. Kör kommandot nedan för att ta bort etablerings jobbet:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Kör kommandot nedan för att skapa ett nytt etablerings jobb som har de senaste tjänst korrigeringarna.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. I resultatet för det sista steget kopierar du den fullständiga "ID"-strängen som börjar med "scim". Du kan också tillämpa dina gamla attribut-mappningar igen genom att köra kommandot nedan, ersätta [New-Job-ID] med det nya jobb-ID som du kopierade och ange JSON-utdata från steg #7 som begär ande texten.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Gå tillbaka till det första webbläsarfönstret och välj fliken **etablering** för ditt program.
13. Verifiera konfigurationen och starta sedan etablerings jobbet. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Nedgradering från SCIM-jobbet till customappsso-jobbet (rekommenderas inte)
 Vi låter dig nedgradera tillbaka till det gamla beteendet, men det rekommenderas inte eftersom customappsso inte drar nytta av några av de uppdateringar vi gör, och kanske inte stöds för alltid. 

1. Logga in på Azure Portal på https://portal.azure.com .
2. i avsnittet **Azure Active Directory > företags program > Skapa program** i Azure Portal skapar du ett nytt program **utanför galleriet** .
3. I avsnittet **Egenskaper** i din nya anpassade app kopierar du **objekt-ID: t**.
4. I ett nytt webbläsarfönster går du till https://developer.microsoft.com/graph/graph-explorer och loggar in som administratör för Azure AD-klienten där appen läggs till.
5. I Graph Explorer kör du kommandot nedan för att initiera etablerings konfigurationen för din app.
   Ersätt "[Object-ID]" med tjänstens huvud namns-ID (objekt-ID) som har kopierats från det tredje steget.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Gå tillbaka till det första webbläsarfönstret och välj fliken **etablering** för ditt program.
7. Slutför konfigurationen för användar etablering precis som vanligt.


## <a name="next-steps"></a>Nästa steg
[Lär dig mer om etablering och avetablering för SaaS-program](user-provisioning.md)
