
## Estruturas de Controle

### if / else

```go
package main

import "fmt"

func classificarIdade(idade int) string {
    if idade < 12 {
        return "Criança"
    } else if idade < 18 {
        return "Adolescente"
    } else {
        return "Adulto"
    }
}

func main() {
    fmt.Println(classificarIdade(8))  // Criança
    fmt.Println(classificarIdade(15)) // Adolescente
    fmt.Println(classificarIdade(30)) // Adulto
}
```

Go permite declarar uma variável diretamente na condição do `if` (escopo limitado ao bloco):

```go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    // numero só existe dentro deste if/else
    if numero, err := strconv.Atoi("42"); err == nil {
        fmt.Println("Número convertido:", numero)
    } else {
        fmt.Println("Erro na conversão:", err)
    }
}
```

> [!NOTE] Em Go não existe operador ternário (`condição ? a : b`). Use sempre `if/else` explicitamente — isso melhora a legibilidade.

### for — o único loop em Go

Go tem **apenas um** tipo de loop: o `for`. Mas ele é flexível o suficiente para substituir `while` e `do-while`:

```go
package main

import "fmt"

func main() {
    // Loop clássico (equivalente ao for de C/Java)
    for i := 0; i < 5; i++ {
        fmt.Printf("i = %d\n", i)
    }

    // Equivalente ao while
    contador := 0
    for contador < 3 {
        fmt.Println("Contador:", contador)
        contador++
    }

    // Loop infinito (use break para sair)
    tentativas := 0
    for {
        tentativas++
        if tentativas >= 3 {
            fmt.Println("Saindo após", tentativas, "tentativas")
            break
        }
    }
}
```

Iterando sobre slices e maps com `range`:

```go
package main

import "fmt"

func main() {
    frutas := []string{"maçã", "banana", "laranja"}

    for indice, fruta := range frutas {
        fmt.Printf("[%d] %s\n", indice, fruta)
    }

    // Se não precisar do índice, use _ para ignorar
    for _, fruta := range frutas {
        fmt.Println(fruta)
    }
}
```

|Forma do `for`|Equivalente em outras linguagens|
|---|---|
|`for i := 0; i < n; i++`|`for` clássico (C, Java)|
|`for condição { }`|`while`|
|`for { }`|`while(true)` / loop infinito|
|`for i, v := range x`|`foreach` / iterador|

### switch

O `switch` em Go é mais poderoso que em outras linguagens: **não precisa de `break`** (ele não "cai" para o próximo caso automaticamente) e pode comparar qualquer tipo:

```go
package main

import "fmt"

func diaDaSemana(dia int) string {
    switch dia {
    case 1:
        return "Domingo"
    case 2:
        return "Segunda-feira"
    case 3:
        return "Terça-feira"
    case 4:
        return "Quarta-feira"
    case 5:
        return "Quinta-feira"
    case 6:
        return "Sexta-feira"
    case 7:
        return "Sábado"
    default:
        return "Dia inválido"
    }
}

func main() {
    fmt.Println(diaDaSemana(3)) // Terça-feira
    fmt.Println(diaDaSemana(9)) // Dia inválido
}
```

`switch` sem expressão funciona como uma cadeia de `if/else if`:

```go
package main

import "fmt"

func classificarNota(nota float64) string {
    switch {
    case nota >= 9.0:
        return "Excelente"
    case nota >= 7.0:
        return "Aprovado"
    case nota >= 5.0:
        return "Recuperação"
    default:
        return "Reprovado"
    }
}

func main() {
    fmt.Println(classificarNota(9.5)) // Excelente
    fmt.Println(classificarNota(6.0)) // Recuperação
    fmt.Println(classificarNota(4.0)) // Reprovado
}
```

> [!WARNING] Se você **realmente** quiser que um caso "caia" para o próximo (comportamento padrão de C/Java), use a palavra-chave `fallthrough` explicitamente. Isso é incomum em Go e deve ser usado com cautela.

```go
switch x {
case 1:
    fmt.Println("Um")
    fallthrough // Executa o próximo caso também
case 2:
    fmt.Println("Dois")
}
```