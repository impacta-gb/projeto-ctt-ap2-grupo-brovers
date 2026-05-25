# Arrays, Slices e Maps em Go

# Introdução

Em Go, Arrays, Slices e Maps são estruturas fundamentais para armazenamento e manipulação de dados.

Cada uma possui características específicas:

- Arrays → tamanho fixo
- Slices → tamanho dinâmico
- Maps → estrutura de chave e valor

---

# Arrays em Go

## O que é um Array?

Um Array é uma coleção de elementos do mesmo tipo com tamanho fixo.

## Sintaxe

```go
var numeros [5]int
```

Isso cria um array de 5 posições do tipo inteiro.

---

## Exemplo de Array

```go
package main

import "fmt"

func main() {
    numeros := [5]int{10, 20, 30, 40, 50}

    fmt.Println(numeros)
}
```

### Saída

```txt
[10 20 30 40 50]
```

---

## Acessando elementos

Os elementos são acessados pelo índice.

```go
fmt.Println(numeros[0])
```

### Saída

```txt
10
```

---

## Alterando valores

```go
numeros[1] = 99
```

---

## Percorrendo Arrays

```go
for i := 0; i < len(numeros); i++ {
    fmt.Println(numeros[i])
}
```

Também é comum usar `range`.

```go
for indice, valor := range numeros {
    fmt.Println(indice, valor)
}
```

---

# Slices em Go

## O que é um Slice?

Slices são estruturas dinâmicas baseadas em arrays.

Diferente dos arrays, o tamanho pode aumentar ou diminuir.

---

## Criando um Slice

```go
nomes := []string{"Ana", "Carlos", "Maria"}
```

---

## Exemplo Completo

```go
package main

import "fmt"

func main() {
    frutas := []string{"Maçã", "Banana", "Uva"}

    fmt.Println(frutas)
}
```

---

## Adicionando elementos

Usamos a função `append`.

```go
frutas = append(frutas, "Laranja")
```

---

## Removendo elementos

```go
frutas = append(frutas[:1], frutas[2:]...)
```

---

## Comprimento e Capacidade

```go
fmt.Println(len(frutas))
fmt.Println(cap(frutas))
```

| Função | Descrição |
|---|---|
| len() | Quantidade de elementos |
| cap() | Capacidade interna |

---

## Slice de um Array

```go
numeros := [5]int{1, 2, 3, 4, 5}

parte := numeros[1:4]

fmt.Println(parte)
```

### Saída

```txt
[2 3 4]
```

---

# Maps em Go

## O que é um Map?

Maps armazenam dados em formato de chave e valor.

Funcionam de maneira semelhante a dicionários em outras linguagens.

---

## Criando um Map

```go
idades := map[string]int{
    "Carlos": 22,
    "Ana": 19,
}
```

---

## Exemplo Completo

```go
package main

import "fmt"

func main() {
    alunos := map[string]int{
        "João": 8,
        "Maria": 10,
    }

    fmt.Println(alunos)
}
```

---

## Acessando valores

```go
fmt.Println(alunos["Maria"])
```

---

## Adicionando elementos

```go
alunos["Pedro"] = 7
```

---

## Removendo elementos

```go
delete(alunos, "João")
```

---

## Verificando existência de chave

```go
valor, existe := alunos["Ana"]

if existe {
    fmt.Println(valor)
}
```

---

## Percorrendo Maps

```go
for chave, valor := range alunos {
    fmt.Println(chave, valor)
}
```

---

# Diferenças entre Arrays, Slices e Maps

| Estrutura | Tamanho | Dinâmico | Índice | Chave e Valor |
|---|---|---|---|---|
| Array | Fixo | Não | Sim | Não |
| Slice | Variável | Sim | Sim | Não |
| Map | Variável | Sim | Não | Sim |

---

# Boas Práticas

## Arrays

Use arrays quando:
- O tamanho for conhecido e fixo
- Precisar de melhor previsibilidade de memória

## Slices

Use slices quando:
- Precisar de flexibilidade
- Trabalhar com listas dinâmicas

## Maps

Use maps quando:
- Precisar buscar informações rapidamente
- Trabalhar com relações de chave e valor

---

# Conclusão

Arrays, Slices e Maps são estruturas essenciais na linguagem Go.

Arrays oferecem tamanho fixo e simplicidade.
Slices fornecem flexibilidade para listas dinâmicas.
Maps permitem acesso rápido através de chaves.

Dominar essas estruturas é fundamental para desenvolver aplicações eficientes em Go.
