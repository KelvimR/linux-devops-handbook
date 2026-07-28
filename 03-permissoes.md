# Capítulo 3 — Permissões no Linux

> Um dos pilares da segurança no Linux é o controle de permissões. Saber interpretá-las e modificá-las é uma habilidade essencial para administradores de sistemas, DevOps e desenvolvedores.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Entender como funcionam as permissões
- Ler a saída do comando `ls -l`
- Alterar permissões com `chmod`
- Alterar proprietário com `chown`
- Alterar grupos com `chgrp`
- Entender o conceito de `umask`
- Utilizar permissões especiais
- Trabalhar com ACL (Access Control Lists)
- Resolver problemas de "Permission denied"

---

# Como funcionam as permissões?

Cada arquivo possui três conjuntos de permissões.

```
Usuário (Owner)

Grupo (Group)

Outros (Others)
```

Cada conjunto possui três permissões.

```
Leitura (Read)

Escrita (Write)

Execução (Execute)
```

Representadas pelas letras:

| Letra | Significado |
|--------|-------------|
| r | Read |
| w | Write |
| x | Execute |

---

# Visualizando permissões

```bash
ls -l
```

Resultado

```text
-rwxr-xr--
```

Vamos dividir.

```
-

rwx

r-x

r--
```

---

# O primeiro caractere

Indica o tipo do arquivo.

| Símbolo | Tipo |
|----------|------|
| - | Arquivo |
| d | Diretório |
| l | Link simbólico |
| c | Dispositivo |
| b | Dispositivo em bloco |
| s | Socket |
| p | Pipe |

Exemplo

```
drwxr-xr-x
```

É um diretório.

---

# Owner

```
rwx
```

O proprietário pode:

✅ Ler

✅ Escrever

✅ Executar

---

# Group

```
r-x
```

O grupo pode:

✅ Ler

❌ Escrever

✅ Executar

---

# Others

```
r--
```

Todos os demais usuários podem apenas ler.

---

# Permissões em diretórios

Em diretórios as permissões possuem outro significado.

## r

Permite listar arquivos.

## w

Permite criar ou remover arquivos.

## x

Permite entrar no diretório.

Exemplo

```bash
cd pasta
```

Sem permissão de execução (`x`) isso não funciona.

---

# Representação Numérica

Cada permissão possui um valor.

| Permissão | Valor |
|------------|-------|
| r | 4 |
| w | 2 |
| x | 1 |

Somando:

```
rwx

4+2+1

7
```

---

## Exemplos

| Número | Permissão |
|----------|------------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |
| 3 | -wx |
| 2 | -w- |
| 1 | --x |
| 0 | --- |

---

# Exemplos mais utilizados

```
777

rwxrwxrwx
```

Todos possuem acesso total.

⚠️ Evite em produção.

---

```
755

rwxr-xr-x
```

Mais comum para diretórios.

---

```
644

rw-r--r--
```

Mais comum para arquivos.

---

```
700

rwx------
```

Somente o proprietário.

---

```
600

rw-------
```

Ideal para arquivos sensíveis.

---

```
400

r--------
```

Muito usado para chaves SSH.

Exemplo

```bash
chmod 400 chave.pem
```

---

# Alterando permissões

## Sintaxe

```bash
chmod PERMISSAO arquivo
```

---

## Exemplo

```bash
chmod 755 script.sh
```

---

## Outro exemplo

```bash
chmod 644 appsettings.json
```

---

## Recursivamente

```bash
chmod -R 755 projeto
```

---

# Modo Simbólico

Adicionar execução

```bash
chmod +x script.sh
```

Remover escrita

```bash
chmod -w arquivo.txt
```

Adicionar leitura

```bash
chmod +r arquivo.txt
```

Adicionar execução apenas ao dono

```bash
chmod u+x script.sh
```

Grupo

```bash
chmod g+w arquivo.txt
```

Outros

```bash
chmod o-r arquivo.txt
```

Todos

```bash
chmod a+x script.sh
```

Legenda

```
u = owner

g = group

o = others

a = all
```

---

# Proprietário

Ver

```bash
ls -l
```

Resultado

```text
kelvim developers
```

---

Alterar proprietário

```bash
sudo chown joao arquivo.txt
```

---

Alterar proprietário e grupo

```bash
sudo chown joao:developers arquivo.txt
```

---

Recursivamente

```bash
sudo chown -R joao:developers projeto
```

---

# Alterando grupos

```bash
sudo chgrp developers arquivo.txt
```

---

# Criando grupos

```bash
sudo groupadd devops
```

Adicionar usuário

```bash
sudo usermod -aG devops kelvim
```

Ver grupos

```bash
groups
```

---

# Umask

Define as permissões padrão para novos arquivos.

Consultar

```bash
umask
```

Resultado

```
0022
```

---

## Como funciona?

Arquivo padrão

```
666
```

Diretório padrão

```
777
```

Umask

```
022
```

Resultado

Arquivo

```
644
```

Diretório

```
755
```

---

Alterar temporariamente

```bash
umask 027
```

---

# Permissões Especiais

Linux possui três permissões especiais.

- SUID
- SGID
- Sticky Bit

---

# SUID

Executa um programa com permissões do proprietário.

Exemplo

```bash
passwd
```

Verificar

```bash
ls -l /usr/bin/passwd
```

Resultado

```text
-rwsr-xr-x
```

O `s` indica SUID.

Adicionar

```bash
chmod u+s arquivo
```

Remover

```bash
chmod u-s arquivo
```

---

# SGID

Em diretórios.

Arquivos criados herdam o grupo.

Adicionar

```bash
chmod g+s compartilhado
```

Resultado

```text
drwxr-sr-x
```

---

# Sticky Bit

Muito usado em

```
/tmp
```

Permite remover apenas arquivos do próprio usuário.

Verificar

```bash
ls -ld /tmp
```

Resultado

```text
drwxrwxrwt
```

Adicionar

```bash
chmod +t pasta
```

---

# ACL (Access Control Lists)

Permite permissões específicas.

Exemplo

Dar acesso apenas para maria.

```bash
setfacl -m u:maria:rwx projeto
```

Consultar

```bash
getfacl projeto
```

Remover

```bash
setfacl -b projeto
```

---

# Cenários Reais

## Script não executa

Erro

```text
Permission denied
```

Solução

```bash
chmod +x script.sh
```

Executar

```bash
./script.sh
```

---

## Docker Volume

Container não consegue gravar.

Resolver

```bash
sudo chown -R 1000:1000 volume
```

---

## Deploy Web

Arquivos pertencem ao usuário errado.

Resolver

```bash
sudo chown -R www-data:www-data /var/www
```

---

## Chave SSH

Erro

```text
UNPROTECTED PRIVATE KEY FILE
```

Resolver

```bash
chmod 400 chave.pem
```

---

# Laboratório

Criar diretório

```bash
mkdir laboratorio
```

Entrar

```bash
cd laboratorio
```

Criar arquivo

```bash
touch script.sh
```

Ver permissões

```bash
ls -l
```

Dar execução

```bash
chmod +x script.sh
```

Ver novamente

```bash
ls -l
```

Remover execução

```bash
chmod -x script.sh
```

Criar diretório compartilhado

```bash
mkdir compartilhado
```

Aplicar

```bash
chmod 775 compartilhado
```

---

# Boas Práticas

✅ Utilize `755` para diretórios.

✅ Utilize `644` para arquivos comuns.

✅ Utilize `600` para arquivos confidenciais.

✅ Utilize `400` para chaves SSH.

✅ Nunca utilize `777` em produção.

✅ Utilize grupos em vez de conceder permissões individuais quando possível.

✅ Revise permissões após implantações.

---

# Erros Comuns

## Permission denied

Verifique

```bash
ls -l
```

---

## Operation not permitted

Execute como administrador.

```bash
sudo comando
```

---

## chmod: cannot access

Arquivo inexistente.

Verifique

```bash
pwd

ls
```

---

## owner mismatch

O usuário não possui propriedade do arquivo.

Resolver

```bash
sudo chown usuario arquivo
```

---

# Cheat Sheet

```bash
# Ver permissões
ls -l

# Alterar permissões
chmod 755 arquivo

# Tornar executável
chmod +x script.sh

# Alterar proprietário
sudo chown usuario arquivo

# Alterar grupo
sudo chgrp grupo arquivo

# Alterar proprietário e grupo
sudo chown usuario:grupo arquivo

# Alterar recursivamente
chmod -R 755 pasta

# Alterar proprietário recursivamente
sudo chown -R usuario:grupo pasta

# Ver umask
umask

# Alterar umask
umask 022

# Ver ACL
getfacl arquivo

# Definir ACL
setfacl -m u:usuario:rwx arquivo
```

---

# Resumo

Neste capítulo você aprendeu:

- Como interpretar permissões no Linux
- Diferença entre Owner, Group e Others
- Uso de `chmod`, `chown` e `chgrp`
- Representação numérica e simbólica
- Conceito de `umask`
- Permissões especiais (SUID, SGID e Sticky Bit)
- ACL (Access Control Lists)
- Boas práticas de segurança
- Cenários reais de administração de servidores

---

# Próximo capítulo

No **Capítulo 4 — Usuários e Grupos**, aprenderemos:

- Gerenciamento de usuários
- Gerenciamento de grupos
- `/etc/passwd`
- `/etc/shadow`
- `/etc/group`
- `useradd`
- `usermod`
- `passwd`
- `su`
- `sudo`
- Controle de acesso e autenticação
- Administração de contas em ambientes corporativos