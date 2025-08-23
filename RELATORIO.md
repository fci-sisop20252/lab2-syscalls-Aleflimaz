# 📝 Relatório do Laboratório 2 - Chamadas de Sistema

---

## 1️⃣ Exercício 1a - Observação printf() vs 1b - write()

### 💻 Comandos executados:
```bash
strace -e write ./ex1a_printf
strace -e write ./ex1b_write
```

### 🔍 Análise

**1. Quantas syscalls write() cada programa gerou?**
- ex1a_printf: 9 syscalls
- ex1b_write: 7 syscalls

**2. Por que há diferença entre os dois métodos? Consulte o docs/printf_vs_write.md**

```
pois o buffer do printf foi forçado a esvaziar com os dois \n, aumentando a quantidade de writes.
```

**3. Qual método é mais previsível? Por quê você acha isso?**

```
Write, pois é impresso a variavel, print está sujeito ao tamanho de buffer ou esvaziamento forçado.
```

---

## 2️⃣ Exercício 2 - Leitura de Arquivo

### 📊 Resultados da execução:
- File descriptor: 3
- Bytes lidos: 127 

### 🔧 Comando strace:
```bash
strace -e openat,read,close ./ex2_leitura
```

### 🔍 Análise

**1. Qual file descriptor foi usado? Por que não começou em 0, 1 ou 2?**

```
file descriptor usado = 3 pois é o menor número inteiro disponivel para ser alocado, já que os outros são usados automaticamente para os canais de comunicação padrão ao iniciar um processo.
```

**2. Como você sabe que o arquivo foi lido completamente?**

```
é possivel saber se o read == 0 significando que o chegou ao fim do arquivo e não leu nenhum byte, ou read== -1, sinalizando que ocorreu um erro na leitura. Para saber se foi totalmente lido seria necessario ler e não encontrar mais byte.
```

**3. Por que verificar retorno de cada syscall?**

```
Entender a resposta do sistema e como tratar da melhor forma.
```

---

## 3️⃣ Exercício 3 - Contador com Loop

### 📋 Resultados (BUFFER_SIZE = 64):
- Linhas: 25 (esperado: 25)
- Caracteres: 1300
- Chamadas read(): 21
- Tempo: 0,000089 segundos

### 🧪 Experimentos com buffer:

| Buffer Size | Chamadas read() | Tempo (s) |
|-------------|-----------------|-----------|
| 16          |       87        | 0.000222  |
| 64          |       21        | 0.000089  |
| 256         |        6        | 0.000073  |
| 1024        |        2        | 0.000061  |

### 🔍 Análise

**1. Como o tamanho do buffer afeta o número de syscalls?**

```
Mais buffer requerem menos solicitações para o sistema
```

**2. Todas as chamadas read() retornaram BUFFER_SIZE bytes? Discorra brevemente sobre**

```
Somente quando o que foi lido era demandaria maior buffer_size. Com o buffer_size sendo maior que o arquivo foi retornado somente o tamanho necessario.
```

**3. Qual é a relação entre syscalls e performance?**

```
Uma realação forte que impacta na performance ao ter menos solicitações de syscall.
```

---

## 4️⃣ Exercício 4 - Cópia de Arquivo

### 📈 Resultados:
- Bytes copiados: 1364
- Operações: 1364
- Tempo: 0.009638 segundos
- Throughput: 138.21 KB/s

### ✅ Verificação:
```bash
diff dados/origem.txt dados/destino.txt
```
Resultado: [X] Idênticos [ ] Diferentes

### 🔍 Análise

**1. Por que devemos verificar que bytes_escritos == bytes_lidos?**

```
Verificação se tudo foi copiado realmente escrito.
```

**2. Que flags são essenciais no open() do destino?**

```
Porque elas sinalizam quais operações serão feitas no arquivo, trazendo as funcionalidade que serão usadas, o O_WRONLY como escrita e leitura, como o O_CREAT que cria caso não exista o arquivo, o O_TRUNC que trunca o arquivo.
```

**3. O número de reads e writes é igual? Por quê?**

```
Não pois o código requer alguns prints aumentando os writes, mas durante o laço de repetição while são parecido em quantidade.
```

**4. Como você saberia se o disco ficou cheio?**

```
Se ficar cheio retorna o "Erro na Escrita" ou seja bytes_escritos != bytes_lidos, mostrando que a função write retornou -1.
```

**5. O que acontece se esquecer de fechar os arquivos?**

```
Possiveis dados no buffer farão que o arquivo fique incompleto pois ainda não foram gravados, lentidão pois arquivo ainda está em operação, e até mesmo vazamento/consumo de recursos.
```

---

## 🎯 Análise Geral

### 📖 Conceitos Fundamentais

**1. Como as syscalls demonstram a transição usuário → kernel?**

```
Quando é necessário o sistema é feito a solicitação e troca de dados.
```

**2. Qual é o seu entendimento sobre a importância dos file descriptors?**

```
Muito necessário para realizar leituras e escritas com a melhor eficácia
```

**3. Discorra sobre a relação entre o tamanho do buffer e performance:**

```
Um buffer maior aumenta a performance pois é necessário menos execuções que buscam dados.
```

### ⚡ Comparação de Performance

```bash
# Teste seu programa vs cp do sistema
time ./ex4_copia
time cp dados/origem.txt dados/destino_cp.txt
```

**Qual foi mais rápido?** cp

**Por que você acha que foi mais rápido?**

```
Porque é um comando direto e ignora parte da estrutura do código, executando menos instruções e chamadas de sistema, além de ser feito por função de sistema local.
```

---

## 📤 Entrega
Certifique-se de ter:
- [X] Todos os códigos com TODOs completados
- [X] Traces salvos em `traces/`
- [X] Este relatório preenchido como `RELATORIO.md`

```bash
strace -e write -o traces/ex1a_trace.txt ./ex1a_printf
strace -e write -o traces/ex1b_trace.txt ./ex1b_write
strace -o traces/ex2_trace.txt ./ex2_leitura
strace -c -o traces/ex3_stats.txt ./ex3_contador
strace -o traces/ex4_trace.txt ./ex4_copia
```
# Bom trabalho!
