---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 02/21/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 79af6512e9ce3d3f897be216ee3626c5d4fbcf1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733872"
---
## <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

Primeiro, você precisa criar uma conta dos Serviços de Mídia. Esta seção mostra o que é necessário para criar a conta usando a CLI do Azure.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando a seguir. Um grupo de recursos do Azure é um contêiner lógico em que recursos, como contas dos Serviços de Mídia do Azure e contas de armazenamento associadas, são implantados e gerenciados.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. Para saber mais sobre como as contas de armazenamento são usadas nos Serviços de Mídia, confira [Contas de armazenamento](../articles/media-services/latest/storage-account-concept.md).

Você deve ter uma conta de armazenamento **Primária**, e pode ter quantas contas de armazenamento **Secundárias** você quiser associadas à sua conta dos Serviços de Mídia. Os Serviços de Mídia oferecem suporte a contas **v2 para fins gerais** (GPv2) ou contas **v1 para fins gerais** (GPv1). As contas somente blob não são permitidas como **Primárias**. Se quiser saber mais sobre as contas de armazenamento, confira [Opções de conta de Armazenamento do Azure](../articles/storage/common/storage-account-options.md). 

Neste exemplo, criamos uma conta de uso geral v2, LRS Padrão. Caso deseje fazer experimentos com contas de armazenamento, use `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção, você deverá considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade dos negócios. Para obter mais informações, confira [Contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
O comando a seguir cria uma conta de armazenamento que será associada à conta dos Serviços de Mídia. No script a seguir, você pode substituir `storageaccountforams` pelo seu valor. O nome da conta deve ter menos de 24 caracteres.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta de Serviços de Mídia

O comando da CLI do Azure a seguir cria uma nova conta dos Serviços de Mídia. Você pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao valor fornecido de conta de armazenamento) e `amsResourceGroup` (deve corresponder ao valor fornecido de grupo de recursos).

```azurecli
az ams account create --name amsaccount \
  -l westus2 \
  -g amsResourceGroup --storage-account storageaccountforams
```
