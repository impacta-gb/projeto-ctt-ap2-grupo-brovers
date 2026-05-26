# Tratamento de Erros

Em Go, erros são valores — não exceções. Ao invés de lançar e capturar exceções como em Java ou Python, Go usa retorno múltiplo para que funções indiquem falhas de forma explícita. Essa abordagem torna o fluxo de erros visível e obriga o programador a lidar com eles.

---

## O padrão `error` em Go

O tipo `error` é uma interface nativa do Go. Uma função que pode falhar retorna `error` como último valor:

```go
package main

import (
    "errors"
    "fmt"
)

func dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("divisão por zero não é permitida")
    }
    return a / b, nil
}

func main() {
    resultado, err := dividir(10, 2)
    if err != nil {
        fmt.Println("Erro:", err)
        return
    }
    fmt.Printf("Resultado: %.2f\n", resultado)

    _, err = dividir(10, 0)
    if err != nil {
        fmt.Println("Erro:", err)
    }
}
```

> [!NOTE]
> Quando não há erro, a função retorna `nil` no lugar do `error`. O padrão `if err != nil` é a forma idiomática de checar erros em Go — você vai vê-lo em praticamente todo código Go real.

---

## Criando erros: `errors.New` e `fmt.Errorf`

### `errors.New`

Usado para mensagens de erro simples e estáticas:

```go
package main

import (
    "errors"
    "fmt"
)

var ErrNaoEncontrado = errors.New("registro não encontrado")

func buscarUsuario(id int) (string, error) {
    usuarios := map[int]string{1: "Alice", 2: "Bob"}

    nome, existe := usuarios[id]
    if !existe {
        return "", ErrNaoEncontrado
    }
    return nome, nil
}

func main() {
    nome, err := buscarUsuario(3)
    if err != nil {
        fmt.Println("Erro:", err)
        return
    }
    fmt.Println("Usuário:", nome)
}
```

### `fmt.Errorf`

Usado para criar mensagens de erro dinâmicas com contexto. O verbo `%w` "embrulha" um erro existente, permitindo inspecioná-lo depois com `errors.Is` ou `errors.As`:

```go
package main

import (
    "errors"
    "fmt"
)

var ErrPermissaoNegada = errors.New("permissão negada")

func acessarArquivo(usuario, arquivo string) error {
    if usuario != "admin" {
        return fmt.Errorf("usuário %q tentou acessar %q: %w", usuario, arquivo, ErrPermissaoNegada)
    }
    return nil
}

func main() {
    err := acessarArquivo("mario", "config.yaml")
    if err != nil {
        fmt.Println(err)

        // Verifica se o erro é (ou contém) ErrPermissaoNegada
        if errors.Is(err, ErrPermissaoNegada) {
            fmt.Println("Ação bloqueada por falta de permissão.")
        }
    }
}
```

| Função          | Quando usar                                              |
|-----------------|----------------------------------------------------------|
| `errors.New`    | Erros simples, sem formatação dinâmica                   |
| `fmt.Errorf`    | Erros com contexto variável ou que embrulham outro erro  |
| `errors.Is`     | Verificar se um erro (ou sua cadeia) é um valor específico |
| `errors.As`     | Extrair um erro de um tipo específico da cadeia          |

---

## `panic` e `recover`

### `panic`

`panic` interrompe a execução normal do programa. Deve ser reservado para situações verdadeiramente irrecuperáveis — erros de programação, como índice fora dos limites ou estado impossível:

```go
package main

import "fmt"

func verificarConfiguracao(porta int) {
    if porta <= 0 || porta > 65535 {
        panic(fmt.Sprintf("porta inválida: %d", porta))
    }
    fmt.Println("Porta configurada:", porta)
}

func main() {
    verificarConfiguracao(8080) // ok
    verificarConfiguracao(-1)   // causa panic
}
```

### `recover`

`recover` captura um `panic` e permite que o programa continue. Só funciona dentro de uma função chamada via `defer`:

```go
package main

import "fmt"

func executarComSeguranca(f func()) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recuperado de um panic:", r)
        }
    }()
    f()
}

func main() {
    executarComSeguranca(func() {
        fmt.Println("Iniciando operação...")
        panic("algo deu muito errado")
    })

    fmt.Println("Programa continua após o recover.")
}
```

> [!WARNING]
> Não use `panic` como substituto para tratamento de erros comum. Em Go, a convenção é retornar `error` para situações esperadas (arquivo não encontrado, entrada inválida) e reservar `panic` apenas para falhas que indicam bugs no próprio código. O uso excessivo de `panic`/`recover` é considerado má prática.
