---
title: 'Tutorial: integração do Azure Active Directory com o TINFOIL SECURITY | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262373"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: integração do Azure Active Directory com o TINFOIL SECURITY

Neste tutorial, você aprenderá como integrar o TINFOIL SECURITY ao Azure AD (Azure Active Directory).
A integração do TINFOIL SECURITY com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TINFOIL SECURITY.
* Você pode permitir que seus usuários entrem automaticamente no TINFOIL SECURITY (logon único) com a conta do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TINFOIL SECURITY, serão necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do TINFOIL SECURITY

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O TINFOIL SECURITY é compatível com SSO iniciado por **IDP**

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionar o TINFOIL SECURITY da galeria

Para configurar a integração do TINFOIL SECURITY com o Azure AD, é necessário adicionar o TINFOIL SECURITY da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TINFOIL SECURITY da galeria, siga as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TINFOIL SECURITY**, selecione **TINFOIL SECURITY** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

     ![TINFOIL SECURITY na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o TINFOIL SECURITY com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TINFOIL SECURITY.

Para configurar e testar o logon único do Azure AD com o TINFOIL SECURITY, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do TINFOIL SECURITY](#configure-tinfoil-security-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do TINFOIL SECURITY](#create-tinfoil-security-test-user)** – para ter um equivalente de Brenda Fernandes no TINFOIL SECURITY que esteja vinculado à representação de usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TINFOIL SECURITY, siga as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TINFOIL SECURITY**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do TINFOIL SECURITY](common/preintegrated.png)

5. O aplicativo TINFOIL SECURITY espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizado de acordo com a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo **Atributos de Usuário** .

        ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo TINFOIL SECURITY espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | NOME | Atributo de Origem |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

     a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na caixa de texto **Atributo de origem**, cole o valor da ID da conta que você obterá posteriormente no tutorial.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

7. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

8. Na seção **Certificado de Autenticação SAML**, copie a **Impressão Digital** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

9. Na seção **Configurar o TINFOIL SECURITY**, copie as URLs corretas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-tinfoil-security-single-sign-on"></a>Configurar o logon único do TINFOIL SECURITY

1. Em uma janela diferente do navegador da Web, entre no site da empresa TINFOIL SECURITY como administrador.

2. Na barra de ferramentas na parte superior, clique em **Minha Conta**.
   
    ![Painel](./media/tinfoil-security-tutorial/ic798971.png "Painel")

3. Clique em **Segurança**.
   
    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

4. Na página de configuração **Logon Único** , realize as seguintes etapas:
   
    ![Logon Único](./media/tinfoil-security-tutorial/ic798973.png "Logon Único")
   
     a. Selecione **Habilitar SAML**.
   
    b. Clique em **Configuração Manual**.
   
    c. Na caixa de texto **URL Post do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure
   
    d. Na caixa de texto **Impressão Digital do Certificado SAML**, cole o valor de **Impressão Digital** copiado da seção **Certificado de Autenticação SAML**.
  
    e. Copie o valor da **ID da Sua Conta** e cole-o na caixa de texto **Valor do Atributo** na seção **Adicionar Atributo** no Portal do Azure.
   
    f. Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo acesso ao TINFOIL SECURITY.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, selecione **TINFOIL SECURITY**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TINFOIL SECURITY**.

    ![O link do TINFOIL SECURITY na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-tinfoil-security-test-user"></a>Criar um usuário de teste do TINFOIL SECURITY

Para permitir que os usuários do Azure AD entrem no TINFOIL SECURITY, eles devem ser provisionados no TINFOIL SECURITY. No caso do TINFOIL SECURITY, o provisionamento é uma tarefa manual.

**Para provisionar um usuário, siga as seguintes etapas:**

1. Se o usuário fizer parte de uma conta Enterprise, será necessário [contatar a equipe de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para que a conta de usuário seja criada.

1. Se o usuário for um usuário de SaaS do TINFOIL SECURITY regular, ele poderá adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite ao email especificado para criar uma nova conta de usuário do TINFOIL SECURITY.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para provisionar as contas de usuário do Azure AD.
> 

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TINFOIL SECURITY no Painel de Acesso, você deverá ser conectado automaticamente ao TINFOIL SECURITY no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

