---
title: Azure AD Connect omvandling av moln synkronisering
description: Den här artikeln beskriver hur du använder omvandlingar för att ändra standardattributen för mappning.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614063"
---
# <a name="transformations"></a>Transformeringar

Med en omvandling kan du ändra standard beteendet för hur ett attribut synkroniseras med Azure Active Directory (Azure AD) med hjälp av molnbaserad synkronisering.

För att kunna utföra den här uppgiften måste du redigera schemat och sedan skicka det igen via en webbegäran.

Mer information om attribut för moln synkronisering finns i [förstå Azure AD-schemat](concept-attributes.md).


## <a name="retrieve-the-schema"></a>Hämta schemat
Hämta schemat genom att följa stegen i [Visa schemat](concept-attributes.md#view-the-schema). 

## <a name="custom-attribute-mapping"></a>Anpassad attributmappning
Följ dessa steg om du vill lägga till en anpassad attributmappning.

1. Kopiera schemat till en text-eller kod redigerare som [Visual Studio Code](https://code.visualstudio.com/).
1. Leta upp det objekt som du vill uppdatera i schemat.

   ![Objekt i schemat](media/how-to-transformation/transform-1.png)</br>
1. Leta upp koden för `ExtensionAttribute3` objektet användare.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Redigera koden så att attributet Company mappas till `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Kopiera schemat tillbaka till Graph Explorer, ändra typ av **begäran** till **Lägg** och välj **Kör fråga**.

    ![Köra fråga](media/how-to-transformation/transform-2.png)

 1. I Azure Portal går du till Cloud Sync-konfigurationen och väljer **starta om etablering**.

    ![Starta om etablering](media/how-to-transformation/transform-3.png)

 1. Efter en liten stund kontrollerar du att attributen fylls genom att köra följande fråga i Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Du bör nu se värdet.

    ![Värdet visas](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Mappning av anpassade attribut med funktion
För mer avancerade mappningar kan du använda funktioner som gör att du kan manipulera data och skapa värden för attribut som passar organisationens behov.

Följ föregående steg och redigera sedan den funktion som används för att konstruera det slutliga värdet för att utföra den här uppgiften.

Information om syntax och exempel på uttryck finns i [skriva uttryck för mappningar av attribut i Azure Active Directory](reference-expressions.md).


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud Sync?](what-is-cloud-sync.md)
