# Imapsync com Autenticação OAuth2 no Office365

Este guia cobre a migração de e-mails entre servidores utilizando **imapsync**, tendo como origem o Office365 com autenticação OAuth2.

## Configuração no Azure

### 1. Registrar um Aplicativo no Azure AD
O Office 365 exige que as conexões IMAP utilizem OAuth2. Para isso, precisamos registrar um aplicativo no Azure Active Directory.

1. Acesse o [Azure Portal](https://portal.azure.com).
2. Navegue para **Azure Active Directory**.
3. Clique em **Registros de Aplicativos** e selecione **Novo Registro**.
4. Preencha os seguintes campos:
   - **Nome do aplicativo:** `IMAPSync OAuth2`
   - **Tipos de conta:** Escolha **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
5. Clique em **Registrar**.

### 2. Obter Credenciais do Aplicativo
Agora, obtenha as credenciais necessárias:

1. No menu do aplicativo, clique em **Certificados e Segredos**.
2. Em **Segredos do Cliente**, clique em **Novo segredo do cliente**.
3. Defina um nome e escolha uma validade (por exemplo, 1 ano).
4. Copie o valor do segredo (ele não será mostrado novamente).
5. Também copie os seguintes valores:
   - **Application (client) ID**
   - **Directory (tenant) ID**

### 3. Conceder Permissões IMAP para o Aplicativo

1. Acesse **Permissões de API**.
2. Clique em **Adicionar Permissão** → **APIs da Microsoft** → **Microsoft Graph**.
3. Escolha **Permissões Delegadas** e adicione:
   - `IMAP.AccessAsUser.All` (Acesso IMAP)
   - `offline_access` (Manter a sessão ativa)
   - `email` (Acesso ao e-mail)
4. Clique em **Adicionar Permissões**.
5. Em seguida, clique em **Conceder Consentimento do Administrador**.

---

## Instalação do ImapSync

```bash
git clone https://github.com/imapsync/imapsync.git
cd imapsync
```

Veja o arquivo `INSTALL.d/INSTALL.ANY.txt` para instalar os módulos do Perl necessários.

---

## Geração do Token OAuth2 para IMAP

### 1. Instalar e Configurar

1. Baixe o arquivo `oauth2_imap.zip` em:
   [https://imapsync.lamiral.info/oauth2/](https://imapsync.lamiral.info/oauth2/)
2. Extraia o arquivo.

### 2. Gerar o Token

Execute o seguinte comando, substituindo `foo@example.com` pelo seu e-mail:

```bash
./oauth2_imap --provider office365 foo@example.com
```

Os tokens são gerados no subdiretório **tokens**, que deve existir previamente.

---

## Testar Login com o Token

```bash
./imapsync --host1 outlook.office365.com --user1 foo@example.com \
  --oauthaccesstoken1 ./oauth2_imap/tokens/oauth2_tokens_foo@example.com.txt \
  --host2 mail.example.com --user2 foo@example.com --password2 'P@ssw0rd' \
  --automap --justlogin
```

---

## Executar a Migração

```bash
./imapsync --host1 outlook.office365.com --user1 foo@example.com \
  --oauthaccesstoken1 ./oauth2_imap/tokens/oauth2_tokens_foo@example.com.txt \
  --host2 mail.example.com --user2 foo@example.com --password2 'P@ssw0rd' \
  --syncinternaldates --resyncflags --automap --subscribe --useheader 'Message-ID' --skipsize
```

---

## Referências
- **Imapsync:** [https://github.com/imapsync/imapsync](https://github.com/imapsync/imapsync)
- **OAuth2 IMAP:** [https://imapsync.lamiral.info/oauth2/oauth2_imap/README_oauth2.txt](https://imapsync.lamiral.info/oauth2/oauth2_imap/README_oauth2.txt)


