---
ms.openlocfilehash: 5d005f5a4906e5e2a55189d073383ab77f6c1860
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013689"
---
| **Создание виртуальных машин** || 
|---|---|
| [Управление виртуальными машинами][1] | Создание, изменение, запуск, остановка и удаление виртуальных машин. |
| [Создание виртуальной машины из настраиваемого образа][2] | Создание настраиваемого образа виртуальной машины и его использование для создания виртуальных машин. | 
| [Getting Started with Managed - Create Virtual Machine Using Specialized Disk From Snapshot - in .Net][3] (Начало работы с управляемыми виртуальными машинами: создание виртуальной машины с использованием специализированного VHD на основе моментального снимка — .Net) | Создание моментального снимка операционной системы виртуальной машины и дисков данных, создание управляемых дисков из моментальных снимков, а также создание виртуальной машины путем подключения управляемых дисков. |  
| [Getting Started with Compute - Manage Virtual Machines In Parallel With Network - in .Net][4] (Начало работы с вычислительными ресурсами: управление виртуальными машинами при помощи сети в параллельном режиме — .Net) | Создание виртуальных машин в одном регионе и в одной виртуальной сети с двумя подсетями в параллельном режиме. |
| [Создание виртуальных машин в разных регионах в параллельном режиме][5] | Создание и распределение нагрузки набора виртуальных машин в нескольких регионах Azure. |
| **Сети виртуальных машин** || 
| [Управление виртуальными сетями][6] | Настройка виртуальной сети с двумя подсетями и ограничение доступа к Интернету в этих подсетях. |
| **Создание масштабируемых наборов** ||
| [Getting Started with Compute - Manage Virtual Machine Scale Set - in .Net][7] (Начало работы с вычислительными ресурсами: управление набором масштабирования виртуальной машины — .Net) | Создание масштабируемого набора виртуальных машин, настройка подсистемы балансировки нагрузки и получение строк SSH-подключения к виртуальным машинам в масштабируемом наборе. |

[1]: ../java-sdk-manage-virtual-machines.md
[2]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-custom-image/
[3]: https://github.com/Azure-Samples/managed-disk-java-create-virtual-machine-using-specialized-disk-from-vhd/
[4]: https://github.com/Azure-Samples/compute-java-manage-virtual-machines-in-parallel/
[5]: ../java-sdk-virtual-machines-in-parallel.md
[6]: ../java-sdk-manage-virtual-networks.md
[7]: ../java-sdk-manage-vm-scalesets.md