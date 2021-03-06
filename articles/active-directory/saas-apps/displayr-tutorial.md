---
title: 'Tutorial: Integração do Azure Active Directory ao Displayr | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9694790ea02bc778bf3b9db212e61fabb90a28a8
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65441435"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Tutorial: Integração do Azure Active Directory ao Displayr

Neste tutorial, você aprenderá a integrar o Displayr ao Azure AD (Azure Active Directory).
A integração do Displayr ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Displayr.
* Você pode permitir que os usuários sejam conectados automaticamente ao Displayr (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Displayr, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Displayr

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Displayr dá suporte ao SSO iniciado por **SP**

## <a name="adding-displayr-from-the-gallery"></a>Como adicionar o Displayr por meio da galeria

Para configurar a integração do Displayr ao Azure AD, você precisará adicionar o Displayr por meio da galeria à lista de aplicativos SaaS gerenciados.

**Para adicionar o Displayr por meio da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Displayr**, selecione **Displayr** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Displayr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Displayr, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Displayr.

Para configurar e testar o logon único do Azure AD com o Displayr, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Displayr](#configure-displayr-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Displayr](#create-displayr-test-user)** – para ter um equivalente de Brenda Fernandes no Displayr que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Displayr, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Displayr**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar Logon Único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração Básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, execute a seguinte etapa:

    ![Informações de logon único de Domínio e URLs do Displayr](common/sp-intiated.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.displayr.com/Login`

5. Na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **Certificado (Bruto)** usando as opções fornecidas de acordo com suas necessidades e salve-o no computador.

    ![O link de download do Certificado](common/certificateraw.png)

6. O aplicativo Displayr espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone  **Editar** para abrir a caixa de diálogo Atributos do usuário.

    ![image](common/edit-attribute.png)

7. Além do indicado acima, o aplicativo Displayr espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Atributos e Declarações de Usuário** da caixa de diálogo **Declarações de Grupo (Versão Prévia)** , execute as seguintes etapas:

     a. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selecione **Todos os Grupos** na lista de opções.

    c. Selecione **Atributo de Origem** da **ID do Grupo**.

    d. Marque **Personalizar o nome da declaração de grupo**.

    e. Marque **Emitir grupos como declarações de função**.

    f. Clique em **Save** (Salvar).

8. Na seção **Configurar o Displayr**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-displayr-single-sign-on"></a>Configurar o logon único do Displayr

1. Em outra janela do navegador da Web, entre no Displayr como administrador.

2. Clique em **Configurações** e, em seguida, navegue para **Conta**.

    ![Configuração](./media/displayr-tutorial/config01.png)

3. Alterne para **Configurações** no menu superior e role a página para baixo para clicar em **Configurar Logon Único (SAML)**.

    ![Configuração](./media/displayr-tutorial/config02.png)

4. Na página **Logon Único (SAML)**, execute as seguintes etapas:

    ![Configuração](./media/displayr-tutorial/config03.png)

     a. Marque a caixa **Habilitar Logon Único (SAML)**.

    b. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Abra o Certificado (Bruto) no Bloco de notas, copie o conteúdo e cole-o na caixa de texto **Certificado**.

    f. Os **mapeamentos de grupo** são opcionais.

    g. Clique em **Save** (Salvar).  

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

     a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo a ela o acesso ao Displayr.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Displayr**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Displayr**.

    ![O link do Displayr na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-displayr-test-user"></a>Criar um usuário de teste do Displayr

Para permitir que os usuários do Azure AD entrem no Displayr, eles precisam ser provisionados no Displayr. No Displayr, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no Displayr como administrador.

2. Clique em **Configurações** e, em seguida, navegue para **Conta**.

    ![Configuração do Displayr](./media/displayr-tutorial/config01.png)

3. Alterne para **Configurações** no menu superior e role a página para baixo até a seção **Usuários** e, em seguida, clique em **Novo Usuário**.

    ![Configuração do Displayr](./media/displayr-tutorial/config07.png)

4. Na página **Novo Usuário**, execute as seguintes etapas:

    ![Configuração do Displayr](./media/displayr-tutorial/config06.png)

     a. Na caixa de texto **Nome**, insira o nome do usuário como **Brendafernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como `Brittasimon@contoso.com`.

    c. Selecione a **Associação de grupo** apropriada.

    d. Clique em **Save** (Salvar).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Displayr no Painel de Acesso, você deverá ser conectado automaticamente ao Displayr, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

