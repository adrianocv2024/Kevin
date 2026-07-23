🛡️ Relatório do Laboratório: SSH Hardening e Segurança de Acesso
Data: 23 de Julho de 2026

Sistema Operativo: Ubuntu 24.04 LTS (AWS EC2)

Objetivo: Configurar e fortalecer a segurança do servidor SSH (hardening), desativando métodos de autenticação vulneráveis e alterando os pontos de acesso padrão.

1. Descrição e Objetivos do Laboratório
O objetivo deste laboratório consistiu na aplicação de boas práticas de segurança num servidor SSH em ambiente Linux:

Criar um utilizador sem privilégios dedicados para acesso remoto (teste).

Gerar e configurar autenticação segura por par de chaves assimétricas (ed25519).

Desativar a autenticação por password.

Impedir o acesso direto com o utilizador root.

Alterar a porta de escuta padrão (22) para uma porta não padrão (2222).

2. Comandos e Procedimentos Executados
A. Criação de Utilizador e Chaves SSH
Criação do utilizador dedicado:

Bash
sudo adduser teste
Geração do par de chaves SSH (ed25519):

Bash
ssh-keygen -t ed25519 -C "teste@laboratorio"
Instalação da chave pública no utilizador teste:

Bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub teste@127.0.0.1
B. Fortalecimento da Configuração (sshd_config)
Foram aplicadas as seguintes diretivas de segurança através de um ficheiro de override na pasta /etc/ssh/sshd_config.d/port2222.conf e no /etc/ssh/sshd_config:

Ini, TOML
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
C. Ajuste do Systemd Socket Activation
Como o Ubuntu 24.04 utiliza systemd socket activation para gerir a porta do SSH por omissão, foi necessário desativar o socket do systemd para que o daemon do SSH assumisse o ficheiro de configuração da porta 2222:

Bash
sudo systemctl stop ssh.socket
sudo systemctl disable ssh.socket
sudo systemctl restart ssh
3. Evidências e Testes de Validação
1️⃣ Verificação da Escuta na Nova Porta (2222)
Bash
root@ip-10-130-177-181:~# ss -tulpn | grep ssh
tcp   LISTEN 0      128          0.0.0.0:2222       0.0.0.0:*    users:(("sshd",pid=13403,fd=3))
tcp   LISTEN 0      128             [::]:2222          [::]:*    users:(("sshd",pid=13403,fd=4))
Status:  Aprovado. O serviço está a escutar exclusivamente na porta 2222.

2️⃣ Teste de Autenticação com Chave Privada
Bash
root@ip-10-130-177-181:~# ssh -i ~/.ssh/id_ed25519 -p 2222 teste@127.0.0.1
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.17.0-1012-aws x86_64)
...
teste@ip-10-130-177-181:~$ 
Status:  Aprovado. Autenticação realizada com sucesso sem solicitação de password.

3️⃣ Teste de Rejeição de Password / Root Direct Access
Bash
root@ip-10-130-177-181:~# ssh -p 2222 root@127.0.0.1
root@127.0.0.1: Permission denied (publickey).
Status:  Aprovado. Acesso root bloqueado e tentativa sem chave rejeitada pelo servidor.

4. Conclusão
O servidor SSH foi devidamente fortalecido. A alteração para a porta 2222 reduz o ruído de ataques de força bruta automatizados, enquanto a obrigatoriedade de chaves ed25519 e o bloqueio do root garantem que apenas entidades autorizadas consigam estabelecer sessão remota.

📤 Para guardar diretamente no Git:
Podes criar o ficheiro README.md dentro da tua pasta de trabalho com o comando:

Bash
cat << 'EOF' > README.md
# Relatório do Laboratório: SSH Hardening
(cola aqui o texto acima se preferires editar via vi/nano)
EOF
E fazer o push para o repositório:

Bash
git add README.md
git commit -m "docs: adiciona relatorio final de ssh hardening"
git push
