---
author: yevster
ms.author: yebronsh
ms.date: 7/17/2020
ms.openlocfilehash: 1e7957a03cb96254a0318774a1620c7143ae6fc4
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062069"
---
### <a name="migrate-all-certificates-to-keyvault"></a>Перенос всех сертификатов в Key Vault

Azure Spring Cloud не предоставляет доступ к хранилищу ключей JRE, поэтому вам придется перенести сертификаты в Azure Key Vault и изменить код приложения, чтобы он обращался к этим сертификатам в Key Vault. Дополнительные сведения см. в статьях [Начало работы с сертификатами Key Vault](/azure/key-vault/certificates/certificate-scenarios) и [Клиентская библиотека сертификатов Azure Key Vault для Java](/java/api/overview/azure/security-keyvault-certificates-readme).