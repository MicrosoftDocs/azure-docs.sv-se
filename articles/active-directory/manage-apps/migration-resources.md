---
title: Resurser för att migrera appar till Azure Active Directory | Microsoft Docs
description: Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376655"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resurser för att migrera program till Azure Active Directory

Resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (Azure AD).

| Resurs  | Beskrivning  |
|:-----------|:-------------|
|[Migrera dina appar till Azure AD](https://aka.ms/migrateapps/whitepaper) | Den white paper här artikeln visar fördelarna med migrering och beskriver hur du planerar för migrering i fyra tydligt beskrivande faser: identifiering, klassificering, migrering och pågående hantering. Du vägleds genom hur du tänker på processen och delar upp ditt projekt i lättanvända delar. I hela dokumentet finns länkar till viktiga resurser som hjälper dig på vägen. |
|[Lösningsguide: Migrera appar från Active Directory Federation Services (AD FS) (AD FS) till Azure AD](./migrate-adfs-apps-to-azure.md) | Den här lösningsguiden vägleder dig genom samma fyra faser för planering och körning av ett programmigreringsprojekt som beskrivs på en högre nivå i faktabladet för migrering. I den här guiden får du lära dig hur du tillämpar faserna på det specifika målet att flytta ett program från Azure Directory Federated Services (AD FS) till Azure AD.|
|[Självstudie för utvecklare: AD FS till en spelbok för programmigrering i Azure AD för utvecklare](https://aka.ms/adfsplaybook) | Den här uppsättningen ASP.NET kodexempel och tillhörande självstudier hjälper dig att på ett säkert sätt migrera dina program som är integrerade med Active Directory Federation Services (AD FS) (AD FS) till Azure Active Directory (Azure AD). Den här självstudien fokuserar på utvecklare som inte bara behöver lära sig att konfigurera appar på både AD FS och Azure AD, utan även bli medvetna och säkra på ändringar som deras kodbas kräver i den här processen.|
| [Verktyg: Active Directory Federation Services (AD FS) skript för migreringsberedskap](https://aka.ms/migrateapps/adfstools) | Det här är ett skript som du kan köra på din lokal Active Directory Federation Services-server (AD FS) för att fastställa beredskapen för appar för migrering till Azure AD.|
| [Distributionsplan: Migrera från AD FS till synkronisering av lösenordshashar](https://aka.ms/ADFSTOPHSDPDownload) | Med synkronisering av lösenordshashar synkroniseras hash-värden för användarlösenord från lokal Active Directory till Azure AD. På så sätt kan Azure AD autentisera användare utan att interagera med lokal Active Directory.| 
| [Distributionsplan: Migrera från AD FS till direktautentisering](https://aka.ms/ADFSTOPTADPDownload)|Azure AD-direktautentisering hjälper användare att logga in på både lokala och molnbaserade program med samma lösenord. Den här funktionen ger användarna en bättre upplevelse eftersom de har ett mindre lösenord att komma ihåg. Det minskar också kostnaderna för IT-supporten eftersom det är mindre troligt att användarna glömmer bort att logga in när de bara behöver komma ihåg ett lösenord. När användare loggar in med Azure AD verifierar den här funktionen användarnas lösenord direkt mot din lokala Active Directory.|
| [Distributionsplan: Aktivera enkel inloggning till en SaaS-app med Azure AD](https://aka.ms/SSODPDownload) | Enkel inloggning (SSO) hjälper dig att komma åt alla appar och resurser som du behöver för att göra affärer, samtidigt som du bara loggar in en gång med ett enda användarkonto. När en användare har loggat in kan användaren till exempel gå från Microsoft Office till SalesForce till Box utan att autentisera (till exempel skriva ett lösenord) en andra gång. 
| [Distributionsplan: Utöka appar till Azure AD med Programproxy](https://aka.ms/AppProxyDPDownload)| Att ge åtkomst från anställdas bärbara datorer och andra enheter till lokala program har traditionellt varit virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZs). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera. Azure AD Programproxy gör det enklare att komma åt lokala program. |
| [Distributionsplaner](../fundamentals/active-directory-deployment-plans.md) | Hitta fler distributionsplaner för distribution av funktioner som multifaktorautentisering, villkorsstyrd åtkomst, användareablering, sömlös SSO, lösenordsåterställning via självbetjäning och mycket mer! |