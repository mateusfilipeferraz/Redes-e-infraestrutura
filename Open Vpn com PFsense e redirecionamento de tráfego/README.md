# OpenVPN com pfSense e Redirecionamento de Tráfego

Neste guia, apresentaremos um passo a passo para configurar o roteamento de tráfego da sua VPS para a rede privada, permitindo a utilização de um IP público no ambiente local de forma eficiente e segura.

---

## 📥 Instalando o pfSense na VPS




### Primeiro Login
Após a instalação, acesse o pfSense utilizando as credenciais padrão:

Usuário: admin
Senha: pfsense


> ⚠️ **Importante:** Troque as credenciais imediatamente após o primeiro login.

### Alterar Senha

1. Clique em **"Alterar a senha"** no **Gerenciador de Usuários**.
2. Digite a nova senha.
3. Confirme a nova senha.
4. Clique em **Salvar**.

---

## 🔒 Criando Regras no Firewall

Para evitar perder o acesso ao fazer alterações nas interfaces, crie uma regra no firewall:

1. Vá em **Firewall**.
2. Clique em **Regras**.
3. Clique em **Adicionar**.
4. Em **Interface**, selecione **WAN address**.
5. Em **Serviço**, selecione **HTTP (porta 80)**.
6. Clique em **Salvar**.

---

## 🌐 Trocando o Idioma

Se preferir, você pode alterar o idioma para **Português (Brasil)**:

1. Clique em **System**.
2. Clique em **Configuração Geral**.
3. Em **Idioma**, selecione **Português (Brasil)**.
4. Clique em **Salvar**.

---

## 🔧 Configurando o OpenVPN

### Criar uma Interface VPN
Configure uma nova interface dedicada à VPN.

### Criar um Perfil de Usuário
Crie um perfil específico para o usuário que utilizará a VPN.

### Baixar o Arquivo do Cliente OpenVPN
Após configurar a VPN, baixe o arquivo de cliente para utilizar no dispositivo local.

---

## 🔁 Redirecionamento de Portas

O redirecionamento de tráfego já estará funcionando após a configuração da VPN, mas é essencial configurar o redirecionamento de **portas específicas** para acessar serviços locais.

### Exemplo: Redirecionar a Porta do RDP (Remote Desktop Protocol)

1. Acesse o painel do pfSense.
2. Vá em **Firewall > NAT**.
3. Adicione uma nova regra para redirecionar a **porta 3389** (RDP).
4. Direcione essa porta para o **IP local** da máquina desejada.
5. Salve e aplique as configurações.
6. Teste o acesso para garantir que o serviço está funcionando corretamente.

---

✅ **Pronto!** Sua VPN com pfSense está configurada, com tráfego redirecionado e pronto para uso seguro em sua rede local.
