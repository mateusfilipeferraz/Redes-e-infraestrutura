![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Vpn-Tailscale/og-image.png)

# VPN com Tailscale e Isolando RDP

O **Tailscale** é uma ferramenta de rede privada virtual (VPN) baseada no protocolo **WireGuard**, que facilita a criação de redes privadas seguras entre dispositivos, sem a necessidade de configuração complexa de firewalls ou roteadores. Ele é amplamente utilizado para conectar servidores, dispositivos pessoais e ambientes corporativos de forma segura e eficiente.

## Principais Características do Tailscale

- **Baseado em WireGuard** – Usa o protocolo WireGuard, conhecido por sua velocidade e segurança.  
- **Fácil Configuração** – Não requer configurações complicadas de firewall ou port forwarding.  
- **Zero Trust e Controle de Acesso** – Possui autenticação baseada em identidade (Google, Microsoft, GitHub, Okta, etc.).  
- **Multi-Plataforma** – Funciona no Windows, macOS, Linux, iOS, Android, Synology, Raspberry Pi, entre outros.  
- **Auto Mesh Networking** – Conecta automaticamente dispositivos da mesma rede sem precisar passar por servidores centrais.  
- **Administração Simples** – Interface web para monitoramento e controle de dispositivos.  
- **Integração com Kubernetes e Docker** – Ideal para DevOps e cloud computing.  

## Casos de Uso do Tailscale

- **Acesso Remoto Seguro** – Conecte-se a servidores privados de qualquer lugar, sem abrir portas na internet.  
- **Substituição de VPNs Tradicionais** – Alternativa mais leve e rápida às VPNs corporativas.  
- **Ambientes de Desenvolvimento e DevOps** – Conecte ambientes locais e em nuvem.  
- **Jogos Online Privados** – Crie redes privadas para jogar com amigos.  
- **Trabalho Remoto** – Conecte funcionários de forma segura sem VPNs tradicionais complexas.  

## Diferença do Tailscale para VPNs Tradicionais

- VPNs tradicionais criam um túnel centralizado para todo o tráfego.  
- O Tailscale funciona como uma **VPN mesh peer-to-peer**, conectando dispositivos diretamente sempre que possível.  
- **Não exige servidores dedicados** – Gerencia conexões diretamente entre dispositivos.  

## Tailscale é gratuito?

- Plano gratuito para até **3 usuários** e **100 dispositivos**.  
- Planos pagos com mais recursos e controle para empresas.  

## Como configurar?

1. Faça um **cadastro na plataforma do Tailscale** para baixar o instalador.

   ![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Vpn-Tailscale/parte-1.gif)  
3. Repita o procedimento em cada computador que deseja adicionar à rede Tailscale.
   ![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Vpn-Tailscale/parte1-1.gif)  

   ![Minha imagem](https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Vpn-Tailscale/image-1-1024x416.webp)  
5. O servidor já poderá ser **pingado**, pois todos estarão na mesma rede VPN.

   ![Minha imagem](https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Vpn-Tailscale/image-2.webp)  
7. Também será possível acessar o **RDP através da VPN**.  

![Minha imagem](https://raw.githubusercontent.com/mateusfilipeferraz/Redes-e-infraestrutura/refs/heads/main/Vpn-Tailscale/image-3.webp) 
## Isolando o RDP

Para isolar o acesso RDP apenas pela VPN:

- Configure o firewall da VPS permitindo conexões apenas do **IP da VPN** do computador autorizado.  
- Com isso, a VPS **não responderá mais ao IP público** para conexões RDP e aceitará somente via VPN.
![Minha imagem](https://github.com/mateusfilipeferraz/Redes-e-infraestrutura/blob/main/Vpn-Tailscale/dfs.gif) 
```

