# OpenVPN com pfSense e Redirecionamento de Tráfego

Neste guia, apresentaremos um passo a passo para configurar o roteamento de tráfego da sua VPS para a rede privada, permitindo a utilização de um IP público no ambiente local de forma eficiente e segura.

---

## 📥 Instalando o pfSense na VPS

<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/1.gif"  width="600" />

### Primeiro Login
Após a instalação, acesse o pfSense utilizando as credenciais padrão:

Usuário: admin
<br>
Senha: pfsense


> ⚠️ **Importante:** Troque as credenciais imediatamente após o primeiro login.
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/2.png"  width="600" />

### Alterar Senha
<img src="https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/3.webp"  width="600" />

1. Clique em **"Alterar a senha"** no **Gerenciador de Usuários**.
2. Digite a nova senha.
3. Confirme a nova senha.
4. Clique em **Salvar**.

<img src="https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/21.webp"  width="600" />
---

## 🔒 Criando Regras no Firewall

Para evitar perder o acesso ao fazer alterações nas interfaces, crie uma regra no firewall:

1. Vá em **Firewall**.
2. Clique em **Regras**.
3. Clique em **Adicionar**.
4. Em **Interface**, selecione **WAN address**.
<img src="https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/10.webp"  width="600" />

6. Em **Serviço**, selecione **HTTP (porta 80)**.
<img src="https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/11.webp"  width="600" />

8. Clique em **Salvar**.

---

## 🌐 Trocando o Idioma

Se preferir, você pode alterar o idioma para **Português (Brasil)**:

1. Clique em **System**.
2. Clique em **Configuração Geral**.
3. Em **Idioma**, selecione **Português (Brasil)**.
<img src="https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/15.webp"  width="600" />
   
5. Clique em **Salvar**.

---

## 🔧 Configurando o OpenVPN

### Criar uma Interface VPN
Configure uma nova interface dedicada à VPN.

<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/16.gif"  width="600" />
### Criar um Perfil de Usuário
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/17.gif"  width="600" />
Crie um perfil específico para o usuário que utilizará a VPN.

### Baixar o Arquivo do Cliente OpenVPN
Após configurar a VPN, baixe o arquivo de cliente para utilizar no dispositivo local.
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/18.gif"  width="600" />

---

## 🔁 Redirecionamento de Portas

O redirecionamento de tráfego já estará funcionando após a configuração da VPN, mas é essencial configurar o redirecionamento de **portas específicas** para acessar serviços locais.
<img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/19.gif"  width="600" />

### Exemplo: Redirecionar a Porta do RDP (Remote Desktop Protocol)

1. Acesse o painel do pfSense.
2. Vá em **Firewall > NAT**.
3. Adicione uma nova regra para redirecionar a **porta 3389** (RDP).
4. Direcione essa porta para o **IP local** da máquina desejada.
5. Salve e aplique as configurações.
6. Teste o acesso para garantir que o serviço está funcionando corretamente.

   <img src="https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Open%20Vpn%20com%20PFsense%20e%20redirecionamento%20de%20tr%C3%A1fego/20.gif"  width="600" />

---

✅ **Pronto!** Sua VPN com pfSense está configurada, com tráfego redirecionado e pronto para uso seguro em sua rede local.
