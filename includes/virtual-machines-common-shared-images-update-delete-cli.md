---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b34d37fa79ccb8344fdacd99877403d61ba5f5c2
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138889"
---
## <a name="update-resources"></a>Atualizar recursos

Existem algumas limitações sobre o que pode ser atualizado. Os itens a seguir podem ser atualizados: 

Galeria de imagens compartilhadas:
- DESCRIÇÃO

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- DESCRIÇÃO
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Exclusão da mais recente
- Data de fim da vida útil

Atualizar a descrição de uma galeria usando ([atualização do az sig](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Atualizar a descrição de uma definição de imagem usando [atualização de definição de imagem do az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Atualizar uma versão de imagem para adicionar uma região para replicar usando [atualização de versão da imagem do az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Essa alteração vai demorar um pouco como a imagem é replicada para a nova região.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Excluir recursos

Você precisa excluir os recursos na ordem inversa, excluindo a versão da imagem pela primeira vez. Depois de excluir todas as versões de imagem, você pode excluir a definição da imagem. Depois de excluir todas as definições de imagem, você pode excluir a Galeria. 

Excluir uma versão de imagem usando [az sig-versão da imagem excluir](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Excluir uma definição de imagem usando [excluir definição de imagem az sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Excluir uma imagem da galeria usando [sig az excluir](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```