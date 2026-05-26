# Concorrência II: Channels

Em Go, goroutines se comunicam entre si através de **channels** — canais tipados que permitem enviar e receber valores de forma segura entre goroutines. A filosofia do Go é clara:

> *"Não comunique compartilhando memória; compartilhe memória comunicando."*

Channels são a principal forma de coordenar goroutines sem precisar de locks manuais.

---

## O que é um Channel?

Um channel é um "tubo" por onde valores de um tipo específico fluem entre goroutines. Você cria um channel com `make()` e usa os operadores `<-` para enviar e receber.

```go
package main

import "fmt"

func main() {
    ch := make(chan string) // channel de strings

    go func() {
        ch <- "olá do goroutine!" // envia valor
    }()

    msg := <-ch // recebe valor
    fmt.Println(msg)
}
```

> [!NOTE]
> O operador `<-` indica a direção do dado: `ch <- valor` envia para o channel, e `valor := <-ch` recebe do channel.

---

## Channels Unbuffered vs Buffered

A diferença fundamental está em **quando o envio bloqueia**.

### Unbuffered (sem buffer)

Criado com `make(chan T)`. O envio bloqueia até que alguém receba — e vice-versa. Isso garante sincronização direta entre duas goroutines.

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    ch := make(chan int) // unbuffered
    var wg sync.WaitGroup

    wg.Add(1)
    go func() {
        defer wg.Done()
        ch <- 42 // bloqueia até alguém receber
    }()

    val := <-ch // desbloqueia o envio acima
    fmt.Println("Recebido:", val)
    wg.Wait()
}
```

### Buffered (com buffer)

Criado com `make(chan T, capacidade)`. O envio só bloqueia quando o buffer está cheio. Útil quando produtor e consumidor têm velocidades diferentes.

```go
package main

import "fmt"

func main() {
    ch := make(chan string, 3) // buffer para 3 mensagens

    ch <- "primeira"
    ch <- "segunda"
    ch <- "terceira"
    // não bloqueia porque o buffer comporta tudo

    fmt.Println(<-ch)
    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```

> [!WARNING]
> Enviar para um channel buffered cheio **bloqueia** a goroutine. Se nenhuma outra goroutine consumir, isso causa um **deadlock**.

### Comparação

| Característica         | Unbuffered          | Buffered                     |
|------------------------|---------------------|------------------------------|
| Capacidade             | 0                   | N (definido no `make`)       |
| Envio bloqueia quando? | Sempre (até receber)| Buffer está cheio            |
| Recebimento bloqueia?  | Sempre (até enviar) | Buffer está vazio            |
| Uso principal          | Sincronização       | Desacoplamento de velocidade |

---

## Fechando um Channel

Use `close(ch)` para sinalizar que não haverá mais envios. Receptores podem verificar se o channel foi fechado:

```go
package main

import "fmt"

func gerador(ch chan<- int) {
    for i := 1; i <= 5; i++ {
        ch <- i
    }
    close(ch) // sinaliza fim
}

func main() {
    ch := make(chan int)
    go gerador(ch)

    for v := range ch { // range fecha automaticamente quando o channel fechar
        fmt.Println(v)
    }
}
```

> [!NOTE]
> Só quem **envia** deve fechar o channel. Fechar um channel já fechado causa **panic**. Enviar para um channel fechado também causa **panic**.

---

## Select: Aguardando Múltiplos Channels

O `select` funciona como um `switch`, mas para operações de channel. Ele aguarda o primeiro case que estiver pronto.

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() {
        time.Sleep(1 * time.Second)
        ch1 <- "resultado do ch1"
    }()

    go func() {
        time.Sleep(2 * time.Second)
        ch2 <- "resultado do ch2"
    }()

    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Println("Recebido:", msg1)
        case msg2 := <-ch2:
            fmt.Println("Recebido:", msg2)
        }
    }
}
```

### Select com Default (non-blocking)

Um `select` com `default` nunca bloqueia — se nenhum channel estiver pronto, cai no `default`:

```go
select {
case msg := <-ch:
    fmt.Println("Recebido:", msg)
default:
    fmt.Println("Nenhuma mensagem disponível agora")
}
```

### Select com Timeout

```go
package main

import (
    "fmt"
    "time"
)

func main() {
    ch := make(chan string)

    select {
    case msg := <-ch:
        fmt.Println(msg)
    case <-time.After(2 * time.Second):
        fmt.Println("Timeout! Nenhuma resposta em 2s.")
    }
}
```

> [!NOTE]
> `time.After(d)` retorna um channel que recebe um valor após a duração `d`. É o padrão idiomático para implementar timeouts com `select`.

---

## Deadlock: o Erro Mais Comum

Um deadlock acontece quando todas as goroutines estão bloqueadas esperando umas às outras. O runtime do Go detecta isso e encerra o programa com mensagem de erro.

```go
package main

func main() {
    ch := make(chan int)
    ch <- 1 // DEADLOCK: nenhuma goroutine vai receber
}
```

Saída: `fatal error: all goroutines are asleep - deadlock!`

> [!WARNING]
> Deadlocks em channels geralmente acontecem por: (1) esquecer de lançar o produtor em uma goroutine separada, (2) fechar o channel no lugar errado, ou (3) desbalancear o número de envios e recebimentos em channels buffered.

---

## Padrões Comuns com Channels

### Pipeline

Goroutines conectadas em série, onde a saída de uma vira entrada da próxima:

```go
package main

import "fmt"

func gerar(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func dobrar(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * 2
        }
        close(out)
    }()
    return out
}

func main() {
    nums := gerar(1, 2, 3, 4, 5)
    dobrados := dobrar(nums)

    for v := range dobrados {
        fmt.Println(v) // 2, 4, 6, 8, 10
    }
}
```

### Fan-out / Fan-in

| Padrão   | Descrição                                              |
|----------|--------------------------------------------------------|
| Fan-out  | Distribui trabalho de 1 channel para múltiplas goroutines |
| Fan-in   | Combina múltiplos channels em 1 channel de saída       |
| Pipeline | Goroutines em série, saída de uma é entrada da próxima |
| Done channel | Sinaliza cancelamento para goroutines em execução |

---

## Direção de Channel em Funções

É boa prática restringir a direção do channel nos parâmetros das funções:

```go
func produtor(ch chan<- int) { // só pode enviar
    ch <- 42
}

func consumidor(ch <-chan int) { // só pode receber
    fmt.Println(<-ch)
}
```

> [!NOTE]
> Restringir a direção (`chan<-` ou `<-chan`) torna o código mais seguro e autodocumentado. O compilador vai impedir operações na direção errada.
