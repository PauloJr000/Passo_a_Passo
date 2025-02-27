# Configuração do Servidor Samba

## Nome da Dupla
**Francisco de Paulo Mesquita Júnior e Gabriel Sousa Braga**

## Matrícula
**554902 e 517451**

---

## Passo 1 - Instalação do Samba

1. Logue na sua máquina Linux (Debian/Ubuntu) como root.
2. Atualize o Linux:
   ```bash
   apt update && apt upgrade -y
   ```
3. Instale o Samba:
   ```bash
   apt install samba
   ```
4. Verifique o status do `smbd` e do `nmbd`:
   ```bash
   systemctl status smbd
   systemctl status nmbd
   ```
5. Ambos devem estar ativos. Caso contrário, utilize os comandos abaixo:
   ```bash
   systemctl start smbd
   systemctl start nmbd
   ```

---

## Passo 2 - Configuração do Cenário no Windows

1. No Painel de Controle do Windows:
   - Selecione **Exibir por: Ícones Grandes**.
   - Acesse **Ferramentas Administrativas > Gerenciamento do Computador**.
   - Navegue até **Usuários e Grupos locais > Usuários**.
2. Crie um novo usuário **aluno**:
   - Clique com o botão direito > **Novo Usuário**.
   - Nome do usuário: **aluno**.
   - Não crie uma senha para **aluno**.
   - Desmarque **O usuário deve alterar a senha no próximo logon**.
   - Clique em **Criar**.
3. Faça logout e entre no perfil criado.
4. No Prompt de Comando, teste a conexão com a sua máquina servidor.
5. Repita o processo para criar o usuário **professor**:
   - Nome: Escolha um nome para o professor.
   - Descrição: **Professor**.
   - Crie uma senha para o professor.
   - Desmarque **O usuário deve alterar a senha no próximo logon**.
6. Teste a conexão da mesma forma.

---

## Passo 3 - Criando a Estrutura de Pastas no Servidor Samba

1. No Linux, acesse o diretório **home**:
   ```bash
   cd /home
   ```
2. Crie a pasta **pública** e defina permissões:
   ```bash
   mkdir publica
   chmod -R 777 publica
   ```
3. Crie a pasta **atividades** e defina permissões:
   ```bash
   mkdir atividades
   chmod -R 777 atividades
   ```
4. Crie os perfis no Linux para professor e aluno:
   ```bash
   useradd -m professor
   useradd -m aluno
   ```

---

## Passo 4 - Adicionando Usuários ao Samba

1. No terminal do Linux, crie o perfil do usuário **aluno** no Samba:
   ```bash
   smbpasswd -a aluno
   ```
2. Defina uma senha para **aluno**.
3. Agora, faça o mesmo para **professor**:
   ```bash
   smbpasswd -a professor
   ```
4. Insira a mesma senha que foi criada no Windows.

---

## Passo 5 - Configurando o Servidor Samba

1. No terminal do Linux, acesse a pasta do Samba:
   ```bash
   cd /etc/samba
   ```
2. Faça um backup do arquivo de configuração:
   ```bash
   cp smb.conf smb.conf.bkp
   ```
3. Edite o arquivo **smb.conf**:
   ```bash
   nano smb.conf
   ```
4. Ative a numeração das linhas pressionando **Alt + Shift + 3**.
5. Em **Global Settings**, verifique se a linha **29** está como:
   ```
   workgroup = WORKGROUP
   ```
   - Caso não esteja, modifique para esse valor.
6. Em **Share Definitions**, modifique a linha **181**:
   ```
   read only = no
   ```
7. No final do arquivo, adicione as seguintes configurações:
   ```
   [publica]
   path = /home/publica
   read only = no
   ```
8. Agora, adicione a configuração para a pasta **atividades**:
   ```
   [atividades]
   path = /home/atividades
   read only = no
   read list = aluno
   ```
   - A linha `read list = aluno` impede o aluno de modificar arquivos na pasta **atividades**.
9. Reinicie os serviços do Samba:
   ```bash
   systemctl restart smbd
   systemctl restart nmbd
   ```

---

## Passo 6 - Mapeando as Pastas Compartilhadas no Windows

1. No usuário **professor**, pressione **Windows + R** e digite:
   ```
   \\ip_da_sua_máquina_servidor\
   ```
2. Pressione **Enter**.
3. Acesse as pastas com o usuário **professor**:
   - **Pública**: Crie e modifique arquivos.
   - **Atividades**: Crie e modifique arquivos.
4. Agora, acesse as pastas com o usuário **aluno**:
   - **Pública**: Deve ter acesso total.
   - **Atividades**: Deve ter apenas acesso de leitura.

---

## Referência
Este passo a passo foi desenvolvido com base na playlist: **Curso Linux File Server Level 1 - SAMBA Server**, do canal **Professor José de Assis**.

Link do canal: [Professor José de Assis](https://www.youtube.com/@ProfessorJosedeAssis)

