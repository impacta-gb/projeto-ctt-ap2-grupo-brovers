# Arrays, Slices e Maps

Arrays, slices e maps são as estruturas de dados fundamentais de Go. Cada uma resolve um problema diferente:

- **Arrays** — coleção de tamanho **fixo** de elementos do mesmo tipo.
- **Slices** — visão **dinâmica** sobre um array, que pode crescer e encolher.
- **Maps** — coleção de pares **chave-valor**, semelhante a dicionários em outras linguagens.

---

## Arrays

Um array é uma coleção de elementos do mesmo tipo com tamanho fixo, definido no momento da declaração. O tamanho faz parte do tipo: `[5]int` e `[3]int` são tipos diferentes.

### Declarando e inicializando

```go
package main

import "fmt"

func main() {
    var numeros [5]int                    // array zerado: [0 0 0 0 0]
    primos := [5]int{2, 3, 5, 7, 11}      // com valores iniciais

    fmt.Println(numeros)
    fmt.Println(primos)
}
```

```text
[0 0 0 0 0]
[2 3 5 7 11]
```

### Acessando e alterando elementos

Os elementos são acessados pelo índice, que começa em `0`:

```go
primos[0] = 99           // altera o primeiro elemento
fmt.Println(primos[0])   // 99
```

### Percorrendo arrays

Você pode usar o `for` clássico ou o `range`, que devolve índice e valor:

```go
package main

import "fmt"

func main() {
    numeros := [3]int{10, 20, 30}

    for i := 0; i < len(numeros); i++ {
        fmt.Println(numeros[i])
    }

    // Forma idiomática com range
    for indice, valor := range numeros {
        fmt.Println(indice, valor)
    }
}
```

---

## Slices

Slices são estruturas dinâmicas construídas sobre arrays. Diferentemente dos arrays, o tamanho pode aumentar ou diminuir, o que os torna a forma mais comum de trabalhar com listas em Go.

### Criando um slice

```go
nomes := []string{"Ana", "Carlos", "Maria"} // a partir de valores
numeros := make([]int, 5)                    // com make: 5 zeros
```

### Adicionando elementos

A função embutida `append` adiciona elementos e retorna o slice atualizado:

```go
frutas := []string{"Maçã", "Banana"}
frutas = append(frutas, "Laranja")
fmt.Println(frutas) // [Maçã Banana Laranja]
```

### Removendo elementos

Não existe função de remoção: combina-se `append` com fatiamento para descartar o índice desejado:

```go
// remove o elemento de índice 1
frutas = append(frutas[:1], frutas[2:]...)
```

### Comprimento e capacidade

`len` retorna quantos elementos existem; `cap` retorna quantos cabem antes de uma nova realocação:

```go
fmt.Println(len(frutas)) // quantidade de elementos
fmt.Println(cap(frutas)) // capacidade interna
```

| Função | Descrição |
|---|---|
| `len()` | Quantidade de elementos |
| `cap()` | Capacidade interna alocada |

### Slice a partir de um array

Um slice pode referenciar parte de um array com a sintaxe `arr[início:fim]` (o `fim` é exclusivo):

```go
package main

import "fmt"

func main() {
    numeros := [5]int{1, 2, 3, 4, 5}
    parte := numeros[1:4]

    fmt.Println(parte)
}
```

```text
[2 3 4]
```

!!! warning
    Um slice **compartilha** a memória do array original. Alterar um elemento do slice altera o array por baixo — e vice-versa.

---

## Maps

Maps armazenam dados em pares de chave e valor e funcionam de maneira semelhante a dicionários em outras linguagens. As chaves são únicas e a ordem de iteração **não** é garantida.

### Criando um map

```go
idades := map[string]int{
    "Carlos": 22,
    "Ana":    19,
}
```

### Acessando, adicionando e removendo

```go
package main

import "fmt"

func main() {
    alunos := map[string]int{
        "João":  8,
        "Maria": 10,
    }

    fmt.Println(alunos["Maria"]) // acessa
    alunos["Pedro"] = 7          // adiciona
    delete(alunos, "João")       // remove

    fmt.Println(alunos)
}
```

### Verificando existência de chave

Acessar um map devolve um segundo valor booleano que indica se a chave existe. Isso evita confundir "chave ausente" com "valor zero":

```go
valor, existe := alunos["Ana"]
if existe {
    fmt.Println(valor)
}
```

### Percorrendo maps

```go
for chave, valor := range alunos {
    fmt.Println(chave, valor)
}
```

---

## Diferenças entre arrays, slices e maps

| Estrutura | Tamanho | Dinâmico | Acesso por índice | Chave e valor |
|---|---|---|---|---|
| Array | Fixo | Não | Sim | Não |
| Slice | Variável | Sim | Sim | Não |
| Map | Variável | Sim | Não | Sim |

## Quando usar cada um

- **Arrays** — quando o tamanho é conhecido e fixo, e você quer previsibilidade de memória.
- **Slices** — na maioria dos casos: listas dinâmicas, coleções que crescem ou encolhem.
- **Maps** — quando precisa buscar valores rapidamente por uma chave.

!!! tip
    Na dúvida entre array e slice, prefira **slice**. Ele é mais flexível e é a estrutura mais usada no dia a dia em Go.
