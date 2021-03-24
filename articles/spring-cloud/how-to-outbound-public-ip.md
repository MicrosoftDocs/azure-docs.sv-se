---
title: Så här identifierar du utgående offentliga IP-adresser i Azure våren Cloud
description: Så här visar du de statiska utgående offentliga IP-adresserna för att kommunicera med externa resurser, till exempel databas, lagring, Key Vault osv.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878221"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Identifiera utgående offentliga IP-adresser i Azure våren Cloud

Den här sidan förklarar hur du visar statiska utgående offentliga IP-adresser för Azure våren Cloud-program.  Offentliga IP-adresser används för att kommunicera med externa resurser, till exempel databaser, lagring och nyckel valv.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Hur IP-adresser fungerar i Azure våren Cloud

En moln tjänst för Azure våren har en eller flera utgående offentliga IP-adresser. Antalet utgående offentliga IP-adresser kan variera beroende på nivåerna och andra faktorer. 

De utgående offentliga IP-adresserna är vanligt vis konstanta och förblir desamma, men det finns undantag.

## <a name="when-outbound-ips-change"></a>När utgående IP-ändringar ändras

Varje Azure våren Cloud-instans har ett angivet antal utgående offentliga IP-adresser vid en angiven tidpunkt. Utgående anslutningar från program, till exempel till en backend-databas, använder en av de utgående offentliga IP-adresserna som ursprungs-IP-adress. IP-adressen väljs slumpvis vid körning, så att Server dels tjänsten måste öppna dess brand vägg för alla utgående IP-adresser.

Antalet utgående offentliga IP-adresser ändras när du utför någon av följande åtgärder:

- Uppgradera Azure våren Cloud-instansen mellan nivåer.
- Generera ett support ärende för fler utgående offentliga IP-adresser för affärs behov.

## <a name="find-outbound-ips"></a>Hitta utgående IP-adresser

Om du vill hitta de utgående offentliga IP-adresser som för närvarande används av tjänst instansen i Azure Portal klickar du på **nätverk** i det vänstra navigerings fönstret i din instans. De visas i fältet **utgående IP-adresser** .

Du kan hitta samma information genom att köra följande kommando i Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
* [Lär dig mer om hanterade identiteter för Azure-resurser](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Lär dig mer om Key Vault i Azure våren Cloud](spring-cloud-tutorial-managed-identities-key-vault.md)
