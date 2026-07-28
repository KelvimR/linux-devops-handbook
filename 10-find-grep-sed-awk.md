# Capítulo 10 — Find, Grep, Sed e Awk

> Os comandos **find**, **grep**, **sed** e **awk** são considerados o "canivete suíço" do Linux. Eles permitem localizar arquivos, pesquisar informações, transformar textos e automatizar análises de dados de forma extremamente eficiente.

---

# Objetivos

Ao final deste capítulo você será capaz de:

- Localizar arquivos e diretórios
- Pesquisar conteúdo em arquivos
- Utilizar expressões regulares
- Editar arquivos automaticamente
- Extrair colunas de dados
- Processar logs
- Criar pipelines eficientes
- Resolver problemas reais utilizando o terminal

---

# O comando find

O `find` é utilizado para localizar arquivos e diretórios.

Sintaxe:

```bash
find [CAMINHO] [OPÇÕES]
```

---

# Localizar todos os arquivos

```bash
find .
```

---

# Procurar por nome

```bash
find . -name "appsettings.json"
```

---

# Ignorar maiúsculas

```bash
find . -iname "readme.md"
```

---

# Procurar diretórios

```bash
find . -type d
```

---

# Procurar arquivos

```bash
find . -type f
```

---

# Arquivos por extensão

```bash
find . -name "*.cs"
```

Outro exemplo

```bash
find . -name "*.log"
```

---

# Procurar por tamanho

Maior que 100 MB

```bash
find . -size +100M
```

Menor que 1 MB

```bash
find . -size -1M
```

---

# Arquivos vazios

```bash
find . -empty
```

---

# Arquivos modificados

Últimas 24 horas

```bash
find . -mtime -1
```

Últimos 7 dias

```bash
find . -mtime -7
```

Mais de 30 dias

```bash
find . -mtime +30
```

---

# Arquivos por usuário

```bash
find . -user kelvim
```

---

# Executar comandos

Excluir logs antigos

```bash
find . -name "*.log" -delete
```

Ou

```bash
find . -name "*.tmp" -exec rm {} \;
```

---

# O comando grep

Pesquisa texto.

```bash
grep "ERROR" app.log
```

---

# Ignorar maiúsculas

```bash
grep -i error app.log
```

---

# Mostrar número da linha

```bash
grep -n ERROR app.log
```

---

# Contar ocorrências

```bash
grep -c ERROR app.log
```

---

# Pesquisa recursiva

```bash
grep -R TODO .
```

---

# Mostrar linhas que NÃO possuem

```bash
grep -v INFO app.log
```

---

# Expressões Regulares

Pesquisar números

```bash
grep "[0-9]" arquivo.txt
```

Pesquisar letras

```bash
grep "[A-Za-z]" arquivo.txt
```

---

Começa com

```bash
grep "^Erro" arquivo.txt
```

Termina com

```bash
grep "final$" arquivo.txt
```

---

Pesquisar IP

```bash
grep -E "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" access.log
```

---

Pesquisar e destacar

```bash
grep --color ERROR app.log
```

---

# O comando sed

Editor de texto em linha de comando.

---

Substituir texto

```bash
sed 's/Linux/Ubuntu/' arquivo.txt
```

---

Todas as ocorrências

```bash
sed 's/Linux/Ubuntu/g' arquivo.txt
```

---

Editar arquivo

```bash
sed -i 's/localhost/producao/' appsettings.json
```

---

Trocar porta

```bash
sed -i 's/5000/8080/' appsettings.json
```

---

Excluir linha

```bash
sed '5d' arquivo.txt
```

---

Mostrar apenas linha

```bash
sed -n '10p' arquivo.txt
```

---

Inserir linha

```bash
sed '3i Novo Texto' arquivo.txt
```

---

Adicionar após linha

```bash
sed '5a Texto Adicionado' arquivo.txt
```

---

# O comando awk

Excelente para trabalhar com colunas.

---

Mostrar primeira coluna

```bash
awk '{print $1}' arquivo.txt
```

---

Primeira e terceira

```bash
awk '{print $1,$3}' arquivo.txt
```

---

Separador personalizado

CSV

```bash
awk -F"," '{print $2}' dados.csv
```

---

Somar valores

```bash
awk '{soma += $2} END {print soma}' vendas.txt
```

---

Contar linhas

```bash
awk 'END {print NR}' arquivo.txt
```

---

Maior valor

```bash
awk 'max<$2{max=$2} END{print max}' dados.txt
```

---

# Sort

Ordenar

```bash
sort nomes.txt
```

Numérico

```bash
sort -n numeros.txt
```

Reverso

```bash
sort -r nomes.txt
```

---

# Uniq

Remover duplicados

```bash
sort nomes.txt | uniq
```

Contar

```bash
sort nomes.txt | uniq -c
```

---

# Cut

Primeira coluna

```bash
cut -d":" -f1 /etc/passwd
```

CSV

```bash
cut -d"," -f2 vendas.csv
```

---

# Xargs

Executar comandos em lote.

```bash
find . -name "*.log" | xargs rm
```

---

Outro exemplo

```bash
cat arquivos.txt | xargs ls -lh
```

---

# Tee

Mostrar e salvar saída

```bash
ls -la | tee arquivos.txt
```

---

Adicionar

```bash
date | tee -a log.txt
```

---

# WC

Contar linhas

```bash
wc -l arquivo.txt
```

Palavras

```bash
wc -w arquivo.txt
```

Caracteres

```bash
wc -c arquivo.txt
```

---

# Tr

Converter letras

```bash
echo "linux" | tr a-z A-Z
```

Remover espaços

```bash
echo "abc 123" | tr -d " "
```

---

# Paste

Juntar arquivos

```bash
paste nomes.txt idades.txt
```

---

# Comandos em Pipeline

Exemplo

```bash
cat access.log | grep ERROR | awk '{print $1}'
```

---

Outro

```bash
ps aux | grep nginx
```

---

Contar erros

```bash
grep ERROR app.log | wc -l
```

---

IPs únicos

```bash
cat access.log \
| awk '{print $1}' \
| sort \
| uniq
```

---

Top 10 IPs

```bash
awk '{print $1}' access.log \
| sort \
| uniq -c \
| sort -nr \
| head
```

---

# Cenários Reais

## Encontrar arquivos grandes

```bash
find / -size +1G
```

---

## Descobrir quem acessou mais uma API

```bash
awk '{print $1}' access.log \
| sort \
| uniq -c \
| sort -nr
```

---

## Trocar URL em vários arquivos

```bash
find . -name "*.json" \
-exec sed -i 's/dev/prod/g' {} \;
```

---

## Contar erros

```bash
grep ERROR app.log | wc -l
```

---

## Encontrar TODOs

```bash
grep -Rn "TODO" .
```

---

## Encontrar arquivos antigos

```bash
find /backup -mtime +30
```

---

# Laboratório

Criar arquivo

```bash
cat > vendas.csv <<EOF
Produto,Valor
Notebook,5000
Mouse,120
Teclado,350
EOF
```

Extrair coluna

```bash
awk -F"," '{print $2}' vendas.csv
```

Contar linhas

```bash
wc -l vendas.csv
```

Pesquisar

```bash
grep Mouse vendas.csv
```

Substituir

```bash
sed 's/Mouse/Monitor/' vendas.csv
```

Encontrar CSVs

```bash
find . -name "*.csv"
```

---

# Problemas Comuns

## Arquivo não encontrado

```text
No such file or directory
```

Verifique:

```bash
pwd
ls
```

---

## Permissão negada

```bash
sudo find /
```

---

## grep não retorna resultados

Verifique:

- Maiúsculas e minúsculas
- Expressão regular
- Caminho do arquivo

---

## sed não altera arquivo

Lembre-se do `-i`.

```bash
sed -i 's/a/b/' arquivo.txt
```

---

# Boas Práticas

✅ Utilize `find` em vez de `locate` quando precisar de resultados atualizados.

---

✅ Sempre teste comandos `sed` sem `-i` antes de alterar arquivos.

---

✅ Combine `grep`, `awk` e `sort` para análise de logs.

---

✅ Utilize `head` e `tail` em arquivos muito grandes.

---

✅ Prefira pipelines simples e legíveis.

---

# Cheat Sheet

```bash
# Find
find . -name "*.log"
find . -size +100M
find . -mtime -7

# Grep
grep ERROR app.log
grep -Ri TODO .
grep -n ERROR app.log

# Sed
sed 's/dev/prod/'
sed -i 's/dev/prod/g' arquivo.txt

# Awk
awk '{print $1}'
awk -F"," '{print $2}'

# Sort
sort arquivo.txt
sort -nr numeros.txt

# Uniq
sort arquivo.txt | uniq
sort arquivo.txt | uniq -c

# Cut
cut -d":" -f1 /etc/passwd

# Tee
comando | tee arquivo.txt

# WC
wc -l arquivo.txt

# TR
tr a-z A-Z

# Xargs
find . -name "*.tmp" | xargs rm
```

---

# Resumo

Neste capítulo você aprendeu:

- Como localizar arquivos com `find`
- Como pesquisar conteúdo com `grep`
- Como editar textos com `sed`
- Como extrair e processar dados com `awk`
- Como utilizar `sort`, `uniq`, `cut`, `tee`, `wc`, `tr` e `xargs`
- Como criar pipelines eficientes
- Como analisar logs e arquivos de configuração
- Boas práticas para automação e troubleshooting

---

# Próximo capítulo

No **Capítulo 11 — Cron e Agendamento de Tarefas**, aprenderemos:

- O que é o `cron`
- Estrutura do `crontab`
- Agendamentos por minuto, hora, dia e mês
- Variáveis de ambiente no cron
- Logs de execução
- Diferenças entre `cron`, `anacron` e `systemd timers`
- Casos reais de backup, limpeza e automação de deploy
- Boas práticas para tarefas agendadas em produção