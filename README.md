# Documentacao Go - CTT AP2

Site de documentacao da linguagem Go, desenvolvido como atividade AP2 da disciplina **Collaboration Tools for Teams**.

**Site publicado:** [https://impactasian.github.io/documeta-o-go/](https://impactasian.github.io/documeta-o-go/)

## Integrantes

| Nome | RA |
|------|-----|
| Alan Oliveira Rocha | 2500557 |
| Lucas Keiji Kuada | 2500169 |
| Gustavo Palomo Moura | 2500355 |
| Mario Nascimento Martins | 2500954 |
| Joao Carlos Vieira do Nascimento | 2501305 |

## Sobre o Projeto

Este repositorio contem uma documentacao tecnica sobre a linguagem Go, cobrindo 10 topicos desde fundamentos ate concorrencia e testes. O site e gerado automaticamente com [Zensical](https://zensical.org) (gerador de sites estaticos baseado em Python) e publicado no GitHub Pages via CI/CD com GitHub Actions.

## Estrutura do Repositorio

```
documeta-o-go/
├── .github/
│   └── workflows/
│       └── docs.yml              # Pipeline CI/CD
├── docs/
│   ├── index.md                  # Pagina inicial
│   ├── introducao-instalacao.md  # Introducao e Instalacao
│   ├── sintaxe-variaveis.md      # Sintaxe Basica e Variaveis
│   ├── estruturas-controle.md    # Estruturas de Controle
│   ├── arrays-slices-maps.md     # Arrays, Slices e Maps
│   ├── structs-metodos.md        # Structs e Metodos
│   ├── tratamento-erros.md       # Tratamento de Erros
│   ├── goroutines.md             # Concorrencia I: Goroutines
│   ├── channels.md               # Concorrencia II: Channels
│   ├── go-modules.md             # Gerenciamento de Pacotes
│   └── testes.md                 # Testes Automatizados
├── mkdocs.yml                    # Configuracao do Zensical
├── requirements.txt              # Dependencias Python
└── README.md
```

## Fluxo de Trabalho

### Protecao da Branch

A branch `main` esta protegida com as seguintes regras:

- Commits diretos (push) na `main` sao bloqueados
- Todas as alteracoes devem ser feitas via Pull Request (PR)
- Cada PR precisa da aprovacao de pelo menos 1 membro da equipe antes do merge

### Feature Branches

Cada membro trabalha em uma branch separada seguindo o padrao `feat/doc-nome-da-pagina`. O fluxo para cada pagina e:

1. Criar branch a partir da `main` atualizada
2. Escrever o conteudo no arquivo `.md` correspondente
3. Fazer commit e push da branch
4. Abrir um Pull Request para a `main`
5. Outro membro revisa, comenta e aprova o PR
6. Apos aprovacao, o PR e mergeado na `main`

### Como as Revisoes Foram Feitas

As revisoes de codigo foram realizadas diretamente no GitHub, na aba "Files changed" de cada Pull Request. O revisor verificava se o Markdown estava correto, se os exemplos de codigo faziam sentido, se havia pelo menos um bloco de codigo com syntax highlighting e uma admonition por pagina, e deixava comentarios quando necessario antes de aprovar.

## Arquitetura do Workflow (CI/CD)

O pipeline esta definido em `.github/workflows/docs.yml` e possui a seguinte arquitetura:

### Gatilhos (Triggers)

O workflow e executado em tres situacoes:

- **pull_request** para a `main`: roda o build para validacao antes do merge, permitindo que o revisor saiba se o codigo esta quebrando o site
- **push** na `main`: roda o build e o deploy apos o merge do PR
- **schedule** (cron `0 0 * * 0`): rebuild semanal automatico todo domingo a meia-noite UTC

### Jobs

O workflow possui dois jobs desacoplados:

**`build_site`** - Responsavel pela validacao e geracao do site:

- Utiliza **Matrix Strategy** com duas versoes do Python (3.10 e 3.11) para garantir que a documentacao e gerada sem erros em diferentes ambientes
- Implementa **cache** das dependencias pip com `actions/cache` para acelerar execucoes subsequentes
- Executa `zensical build --clean` para gerar os arquivos HTML
- Faz **upload do artefato** (pasta `site/`) com `actions/upload-pages-artifact` apenas na versao 3.11 da matrix

**`deploy_site`** - Responsavel pela publicacao no GitHub Pages:

- Depende do `build_site` atraves da diretiva `needs`
- Possui **condicional de seguranca** (`if`): nunca executa durante Pull Requests, apenas no push para `main` ou no schedule
- Faz o deploy utilizando `actions/deploy-pages`

### Fluxo Visual

```
pull_request → build_site (3.10 + 3.11) → [sem deploy]
push na main → build_site (3.10 + 3.11) → deploy_site → GitHub Pages
schedule     → build_site (3.10 + 3.11) → deploy_site → GitHub Pages
```

## Tecnologias Utilizadas

- **Zensical** - Gerador de sites estaticos (baseado em Python, compativel com MkDocs)
- **GitHub Actions** - CI/CD para build e deploy automatizado
- **GitHub Pages** - Hospedagem do site
- **Markdown** - Formato do conteudo da documentacao
