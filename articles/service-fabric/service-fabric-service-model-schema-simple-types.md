---
title: Enkla typer av XML-scheman för Azure Service Fabric service Model
description: Beskriver de enkla typerna i XML-schemat för den Service Fabric tjänst modellen.
ms.topic: reference
ms.date: 12/10/2018
ms.openlocfilehash: 3ed028bc255fa9561316a655da6edfacceff1c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75466213"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-simple-types"></a>Enkla typer av XML-scheman för service modell

## <a name="fabricuri-simpletype"></a>FabricUri simpleType
En URI som används som en stabil identifierare av tjänster genom att Microsoft Azure Service Fabric namngivnings systemet. 

### <a name="xml-source"></a>XML-källa
```xml
<xs:simpleType xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="FabricUri">
    <xs:annotation>
      <xs:documentation>A URI that is used as a stable identifier of services by Microsoft Azure Service Fabric Naming system. </xs:documentation>
    </xs:annotation>
    <xs:restriction base="xs:anyURI"/>
  </xs:simpleType>
  
```
