# Sintaxe Básica e Variáveis

Go tem uma sintaxe enxuta e poucas palavras-chave. Esta página cobre as formas de declarar variáveis, os tipos primitivos da linguagem, constantes e a conversão explícita entre tipos.

---

## Declaração de variáveis

Go oferece duas formas principais de declarar variáveis:

```go
package main

import "fmt"

func main() {
    // Forma explícita com var
    var nome string = "Gopher"
    var idade int = 10

    // Inferência de tipo com :=
    linguagem := "Go"
    versao := 1.22

    fmt.Println(nome, idade, linguagem, versao)
}
```

!!! note
    O operador `:=` (short variable declaration) só pode ser usado **dentro de funções**. Para variáveis no escopo do pacote, use `var`.

## Tipos primitivos

| Tipo | Exemplo de valor | Descrição |
|---|---|---|
| `int` | `42`, `-7` | Inteiro (tamanho depende da plataforma) |
| `int8` … `int64` | `127`, `-128` | Inteiros com tamanho fixo |
| `uint` | `0`, `255` | Inteiro sem sinal |
| `float32` | `3.14` | Ponto flutuante de 32 bits |
| `float64` | `3.141592653` | Ponto flutuante de 64 bits (padrão) |
| `string` | `"Olá, Go!"` | Sequência de bytes UTF-8 |
| `bool` | `true`, `false` | Valor booleano |
| `byte` | `'A'` | Alias para `uint8` |
| `rune` | `'ã'` | Alias para `int32`, representa um caractere Unicode |

```go
package main

import "fmt"

func main() {
    var inteiro int = 42
    var flutuante float64 = 3.14159
    var texto string = "Go é incrível"
    var booleano bool = true

    fmt.Printf("int: %d\n", inteiro)
    fmt.Printf("float64: %.2f\n", flutuante)
    fmt.Printf("string: %s\n", texto)
    fmt.Printf("bool: %t\n", booleano)
}
```

## Constantes

Constantes são declaradas com `const` e não podem ser alteradas após a definição:

```go
package main

import "fmt"

const Pi = 3.14159
const AppName = "MeuApp"
const MaxRetries = 3

func main() {
    fmt.Println(Pi, AppName, MaxRetries)
}
```

Go também oferece o `iota`, um enumerador automático muito útil:

```go
package main

import "fmt"

type DiaSemana int

const (
    Domingo DiaSemana = iota // 0
    Segunda                  // 1
    Terca                    // 2
    Quarta                   // 3
    Quinta                   // 4
    Sexta                    // 5
    Sabado                   // 6
)

func main() {
    fmt.Println(Segunda) // 1
    fmt.Println(Sexta)   // 5
}
```

## Conversão de tipos

Go é **estritamente tipado** — não há conversão implícita entre tipos. Você deve converter explicitamente:

```go
package main

import "fmt"

func main() {
    var inteiro int = 42
    var flutuante float64 = float64(inteiro) // int → float64
    var inteiro2 int = int(flutuante)        // float64 → int (trunca a parte decimal)

    texto := fmt.Sprintf("%d", inteiro)      // int → string

    fmt.Println(flutuante, inteiro2, texto)
}
```

!!! warning
    Converter `float64` para `int` **trunca** a parte decimal — `int(3.99)` resulta em `3`, não em `4`. Cuidado com perdas de precisão em cálculos financeiros ou científicos.
