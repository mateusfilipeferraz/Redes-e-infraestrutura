#### Estes conteúdos foram elaborados com a ajuda do ChatGPT.

Link donwload: https://drive.google.com/drive/folders/1FKFG6CB6M4C-GjXCxYtHlkTcRezQJmPe?usp=sharing

# Configurando o Proxy no Mikrotik

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Proxy-Web-no-Mikrotik/Lab.png)



O proxy web é um servidor intermediário que atua entre o cliente (navegador) e a internet, transmitindo as solicitações de rede de um para o outro. Ele pode ser usado para várias finalidades, incluindo controle de acesso, filtragem de conteúdo, aumento de desempenho (através de cache) e até mesmo para mascarar o endereço IP do cliente. 


## Para configurar um proxy no Mikrotik, siga os passos abaixo:

1. **Acessar o WinBox ou a Interface Web do Mikrotik:**
   Abra o WinBox ou a interface web do Mikrotik e faça login.

2. **Configurar o Servidor Proxy:**
   - Vá até `IP > Web Proxy`.
   - Ative o "Web Proxy" marcando a opção **Enabled**.
   - Configure a porta do proxy. A porta padrão é 8080, mas você pode mudar se necessário.
   - (Opcional) Defina o `Cache Administrator`, o qual será o e-mail do administrador responsável.

3. **Configurar as Regras de Proxy (opcional):**
   - Em `IP > Web Proxy > Access`, você pode adicionar regras para controlar o acesso, como bloquear determinados sites ou permitir apenas alguns endereços.
   - Clique em **Add New** e configure os parâmetros de acordo com a política desejada.
   
 ##  Trabalhando Proxy Transparente

4. **Redirecionar o Tráfego para o Proxy (opcional):**
   Para forçar os clientes a usarem o proxy, crie uma regra de redirecionamento no `IP > Firewall > NAT`:
   - Clique em **Add New**.
   - Em `Chain`, selecione **dstnat**.
   - Em `Protocol`, escolha **6 (tcp)**.
   - Em `Dst. Port`, defina **80,443** (porta HTTP e HHTTPS).
   - Em `Action`, escolha **Redirect** e defina a porta do proxy (ex.: 8080).


# Configuração do Proxy no Mikrotik via CLI

## 1. Ativar o Servidor Proxy e Configurar a Porta

Para ativar o proxy e definir a porta (ex.: 8080), use o seguinte comando:

```bash
/ip proxy set enabled=yes port=8080
```

Substitua `8080` pela porta desejada, caso prefira uma diferente.

### Exemplo: Bloquear um site específico

Para bloquear um site, use o comando:

```bash
/ip proxy access add dst-host="sitebloqueado.com" action=deny
```

### Exemplo: Permitir apenas um site específico

Para permitir apenas um site, use:

```bash
/ip proxy access add dst-host="sitepermitido.com" action=allow
```

## 2. Redirecionar o Tráfego para o Proxy (Opcional)

Para forçar os clientes a usarem o proxy, você pode adicionar uma regra de redirecionamento no firewall. Esse comando redireciona o tráfego HTTP (porta 80) para o proxy:

```bash
/ip firewall nat add chain=dstnat protocol=tcp dst-port=80 action=redirect to-ports=8080
```

Essa regra captura todo o tráfego na porta 80 e o direciona para o proxy configurado na porta 8080.

---

Esses comandos devem ser suficientes para configurar o proxy no Mikrotik diretamente pela CLI. Ajuste conforme necessário para o seu ambiente e as políticas de segurança de rede.
### Configurando o Proxy no Firefox

1. Abra o Firefox e vá para **Configurações**.
2. Role para baixo até a seção **Configurações de Rede** e clique em **Configurações**.
3. Selecione **Configuração manual de proxy**.
4. Em **HTTP Proxy**, insira o endereço IP e a porta configurados no Mikrotik (ex.: `192.168.1.1` e porta `8080`).
5. Se desejar que o proxy funcione para todos os protocolos, marque **Usar este proxy para todos os protocolos**.
6. Clique em **OK** para salvar as configurações.

Com essas configurações, o Firefox passará a rotear o tráfego HTTP (e possivelmente HTTPS) através do proxy configurado no Mikrotik, permitindo controle centralizado e potencial economia de banda.

### Para configurar o proxy no Windows, siga estes passos:

![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Proxy-Web-no-Mikrotik/proxy%20windows.png)


1. **Abrir as Configurações de Proxy**:
   - Pressione **Win + I** para abrir as **Configurações** do Windows.
   - Vá para **Rede e Internet** > **Proxy**.

2. **Configurar o Proxy Manualmente**:
   - Na seção **Configuração de proxy manual**, ative a opção **Usar um servidor proxy**.
   - Em **Endereço IP do proxy**, insira o endereço IP do servidor proxy configurado no Mikrotik (por exemplo, `192.168.1.1`).
   - Em **Porta**, insira a porta do proxy (por exemplo, `8080`).

3. **Opções Avançadas** (opcional):
   - Se quiser excluir certos endereços do uso do proxy (por exemplo, sites internos ou da intranet), insira-os na caixa de texto em **Use o servidor proxy, exceto para os endereços que comecem com**. Separe cada endereço com ponto e vírgula (`;`).
   - Marque a caixa **Não usar o servidor proxy para endereços locais (intranet)** se você deseja que o proxy seja usado apenas para endereços externos.

4. **Salvar Configurações**:
   - Clique em **Salvar** para aplicar as configurações.

Essas configurações de proxy afetam apenas os aplicativos que utilizam as configurações de proxy do sistema no Windows. Isso inclui navegadores como o Edge e o Chrome, mas pode não afetar outros programas que utilizam configurações de rede próprias.

Agora, o Windows direcionará o tráfego de rede para o proxy configurado no Mikrotik, ajudando no controle e no monitoramento do tráfego de rede.
