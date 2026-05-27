# Concorrência I: Goroutines

Go foi projetado com concorrência como cidadã de primeira classe. O mecanismo central para isso são as **goroutines**: funções que executam de forma concorrente com o resto do programa, gerenciadas pelo runtime do Go (não pelo sistema operacional).

---

## O que são Goroutines?

Uma goroutine é como uma thread extremamente leve. Enquanto uma thread do SO pode ocupar megabytes de memória, uma goroutine começa com apenas alguns kilobytes. O runtime do Go multiplexa automaticamente milhares de goroutines em um número menor de threads do SO.

Para iniciar uma goroutine, basta prefixar a chamada de função com `go`:

```go
package main

import (
    "fmt"
    "time"
)

func saudar(nome string) {
    fmt.Println("Olá,", nome)
}

func main() {
    go saudar("Alice") // executa de forma concorrente
    go saudar("Bob")

    // Sem este Sleep, o main termina antes das goroutines executarem
    time.Sleep(100 * time.Millisecond)
    fmt.Println("Fim do main")
}
```

!!! note
    Quando a função `main` termina, todas as goroutines são encerradas imediatamente, mesmo que ainda não tenham concluído. Por isso, o `time.Sleep` acima é apenas didático — na prática, usa-se `sync.WaitGroup` para aguardar goroutines corretamente.

---

## `go func()` — Goroutines anônimas

É comum lançar goroutines com funções anônimas diretamente:

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    for i := 1; i <= 3; i++ {
        i := i // cria uma cópia local de i para cada goroutine
        go func() {
            fmt.Printf("Goroutine %d executando\n", i)
        }()
    }

    time.Sleep(200 * time.Millisecond)
}
```

!!! warning
    Note o `i := i` dentro do loop. Sem essa cópia, todas as goroutines compartilhariam a mesma variável `i` do loop externo — uma race condition clássica. Ao capturar closures em goroutines, sempre crie uma cópia local das variáveis do loop.

---

## `sync.WaitGroup` — Aguardando goroutines

A forma correta de esperar um conjunto de goroutines terminar é com `sync.WaitGroup`:

```go
package main

import (
    "fmt"
    "sync"
)

func processar(id int, wg *sync.WaitGroup) {
    defer wg.Done() // decrementa o contador ao terminar
    fmt.Printf("Processando tarefa %d\n", id)
}

func main() {
    var wg sync.WaitGroup

    for i := 1; i <= 5; i++ {
        wg.Add(1) // incrementa o contador antes de lançar a goroutine
        go processar(i, &wg)
    }

    wg.Wait() // bloqueia até o contador chegar a zero
    fmt.Println("Todas as tarefas concluídas.")
}
```

| Método       | O que faz                                                   |
|--------------|-------------------------------------------------------------|
| `wg.Add(n)`  | Adiciona `n` ao contador interno (chame antes do `go`)      |
| `wg.Done()`  | Decrementa o contador em 1 (chame ao fim de cada goroutine) |
| `wg.Wait()`  | Bloqueia a goroutine atual até o contador chegar a zero     |

---

## Cuidados com Race Conditions

Uma **race condition** acontece quando duas ou mais goroutines acessam e modificam a mesma variável ao mesmo tempo sem sincronização, gerando resultados imprevisíveis:

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    var contador int
    var wg sync.WaitGroup
    var mu sync.Mutex // mutex para proteger o acesso ao contador

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            mu.Lock()   // garante acesso exclusivo
            contador++
            mu.Unlock() // libera o acesso
        }()
    }

    wg.Wait()
    fmt.Println("Contador final:", contador) // sempre 1000
}
```

Sem o `sync.Mutex`, o resultado seria imprevisível — às vezes 998, às vezes 1000, dependendo do agendamento das goroutines.

!!! note
    Go possui uma ferramenta embutida para detectar race conditions em tempo de execução. Execute seu programa com a flag `-race` para ativá-la:

    ```bash
    go run -race main.go
    go test -race ./...
    ```

    Use essa flag durante o desenvolvimento e nos testes — ela não deve ser usada em produção por ter overhead de desempenho.
