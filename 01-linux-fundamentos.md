# Capítulo 1 — Fundamentos do Linux

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender o que é Linux
- Conhecer a arquitetura do sistema
- Navegar pelo terminal
- Entender o sistema de arquivos
- Executar comandos básicos
- Localizar ajuda
- Entender como o Kernel funciona

---

# O que é Linux?

Linux é um sistema operacional de código aberto baseado em Unix.

Foi criado por Linus Torvalds em 1991.

Hoje é utilizado em:

- Servidores
- Cloud Computing
- Kubernetes
- Android
- IoT
- Supercomputadores
- Raspberry Pi
- Data Centers

Mais de 90% dos servidores na internet executam Linux.

---

# Distribuições Linux

Uma distribuição (distro) é um sistema operacional construído sobre o Kernel Linux.

Exemplos:

| Distribuição | Uso |
|--------------|-----|
| Ubuntu | Desenvolvimento |
| Debian | Servidores |
| Fedora | Desktop |
| CentOS Stream | Enterprise |
| Rocky Linux | Enterprise |
| AlmaLinux | Enterprise |
| Arch Linux | Avançado |
| Kali Linux | Segurança |

---

# Arquitetura do Linux

```
+-----------------------------+
| Usuário                     |
+-----------------------------+
| Aplicações                  |
+-----------------------------+
| Shell (Bash/Zsh)            |
+-----------------------------+
| Kernel                      |
+-----------------------------+
| Hardware                    |
+-----------------------------+
```

---

# Kernel

O Kernel é o núcleo do sistema operacional.

Ele controla:

- Memória
- CPU
- Disco
- Rede
- Drivers
- Processos

Sem o Kernel nenhum programa consegue conversar com o hardware.

---

# Shell

O Shell interpreta comandos digitados pelo usuário.

Exemplo:

```bash
ls
```

O Shell envia esse comando para o Kernel.

O Kernel solicita ao sistema de arquivos a listagem do diretório.

---

# Terminal

O Terminal é a interface onde digitamos comandos.

Alguns exemplos:

- GNOME Terminal
- Windows Terminal
- iTerm2
- Konsole

---

# Estrutura de Diretórios

```
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin
├── srv
├── sys
├── tmp
├── usr
└── var
```

---

# Diretórios Importantes

## /

Raiz do sistema.

Tudo começa aqui.

---

## /home

Usuários.

```
/home/kelvim
```

---

## /etc

Arquivos de configuração.

Exemplos:

```
/etc/passwd

/etc/hosts

/etc/nginx

/etc/systemd
```

---

## /var

Logs.

```
/var/log
```

---

## /tmp

Arquivos temporários.

---

## /usr

Programas instalados.

---

## /bin

Comandos essenciais.

```
ls

cp

mv

rm
```

---

# Comandos Básicos

Mostrar diretório atual

```bash
pwd
```

Listar arquivos

```bash
ls
```

Listar detalhes

```bash
ls -l
```

Mostrar arquivos ocultos

```bash
ls -la
```

Entrar em diretório

```bash
cd Documentos
```

Voltar

```bash
cd ..
```

Ir para Home

```bash
cd ~
```

---

# Ajuda

Manual

```bash
man ls
```

Ajuda rápida

```bash
ls --help
```

Qual comando executável

```bash
which docker
```

Descobrir localização

```bash
whereis docker
```

Versão

```bash
docker --version
```

---

# Variáveis de Ambiente

Mostrar PATH

```bash
echo $PATH
```

Mostrar usuário

```bash
echo $USER
```

Home

```bash
echo $HOME
```

Shell

```bash
echo $SHELL
```

---

# Exercícios

1. Descubra seu diretório atual.
2. Entre na pasta Home.
3. Liste todos os arquivos ocultos.
4. Descubra onde está instalado o Git.
5. Exiba a variável PATH.
6. Consulte o manual do comando `cp`.

---

# Laboratório

## Criando diretórios

```bash
mkdir linux-lab
```

```bash
cd linux-lab
```

```bash
mkdir estudos
```

```bash
cd estudos
```

Criar arquivos

```bash
touch aula01.txt

touch aula02.txt
```

Listar

```bash
ls -la
```

---

# Erros Comuns

## Permission denied

Causa:

Permissão insuficiente.

Solução:

```bash
sudo comando
```

ou ajustar permissões com `chmod`.

---

## command not found

Causa:

Programa não instalado ou ausente do `PATH`.

Solução:

```bash
which comando
```

---

# Boas Práticas

- Não utilize `sudo` sem necessidade.
- Evite trabalhar como `root`.
- Leia a documentação oficial.
- Utilize nomes descritivos para arquivos e diretórios.
- Aprenda os comandos antes de automatizá-los.

---