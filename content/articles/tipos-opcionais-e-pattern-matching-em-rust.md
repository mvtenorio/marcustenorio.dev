---
title: Tipos opcionais e pattern matching em Rust
date: 2021-01-19
---

Recentemente resolvi aprender uma nova linguagem de programação e a escolhida foi Rust. Como desenvolvedor, acredito que aprender novas linguagens ou novos conceitos é uma prática importante para expandir nosso conhecimento e também nos manter empolgados com a profissão. Claro que pessoas são diferentes e talvez você não sinta a necessidade de aprender uma nova linguagem que não utilizará no trabalho, mas te garanto que, mesmo que você não use a linguagem no seu dia a dia, ela pode te apresentar novas ideias que serão úteis, mesmo ao programar na sua linguagem usual.

Um exemplo disso é como o Rust trata tipos opcionais. No rust não existe tipo `null` ou `undefined` como no JavaScript, por exemplo, assim como em várias outras linguagens. Para resolver o caso de um valor ser opcional, existe o tipo [Option](https://doc.rust-lang.org/std/option/), que possui duas variantes: `Some`, que contém um valor, e `None`, que não contém nada. Claro que Rust não é a única a tratar valores opcionais dessa forma. A primeira vez que eu tive contato com esse tipo de tratamento foi no Elm com o tipo [Maybe](https://guide.elm-lang.org/error_handling/maybe.html), que por sua vez foi inspirado no tipo de mesmo nome do [Haskell](https://wiki.haskell.org/Maybe).
Vamos exemplificar como funciona no Rust:

Vamos criar uma funcão que imprime a idade de um usuário:
```rust
fn main() {
    let idade = 30;

    mostra_idade(idade); // A idade do usuário é: 30 anos
}

fn mostra_idade(idade: i32) {
    println!("A idade do usuário é: {} anos", idade);
}
```

A função `mostra_idade` não precisa tratar o caso da variável conter um valor nulo. O compilador garante que a idade seja do valor `i32` (um número inteiro de 32 bits). Agora vamos transpor o exemplo para uma linguagem que tenha null, como JavaScript:

```js
function mostraIdade(idade) {
    console.log(`A idade do usuário é: ${idade} anos`);
}

mostraIdade(30); // A idade do usuário é: 30 anos

mostraIdade(null); // A idade do usuário é: null anos

mostraIdade(undefined); // A idade do usuário é: undefined anos
```

Os exemplos acima, passando `null` ou `undefined` como argumentos, talvez sejam familiares pra você. É muito comum ver esse tipo de erros em aplicações no nosso dia a dia. Você provavelmente não passaria `null` diretamente para a função mas esse valor pode vir de uma variável qualquer. Para garantir que esse código funcione corretamente seria necessário verificar de alguma forma se a variável `idade` contém um valor válido:

```js
function mostraIdade(idade) {
    if (idade === null || idade === undefined) {
        return;
    }
    console.log(`A idade do usuário é: ${idade} anos`);
}
```

Apesar de ser possível fazer esse tratamento, vemos pela quantidade de bugs existentes que isso é um grande problema. Uma fala do criador da referência nula, Tony Hoare, ficou bem famosa, quando ele disse que esse é um ["erro de um bilhão de dólares"](https://en.wikipedia.org/wiki/Null_pointer#History), pois causou inúmeros problemas ao longo do tempo.

Voltando ao Rust, já vimos que não é necessário tratar o caso de valores nulos. Porém, em alguns casos, a variável pode ser opcional. No nosso exemplo, talvez o a idade do usuário não seja um valor obrigatório. Aí entra o tipo `Option` mencionado anteriormente:

```rust
fn main() {
    let idade = Some(30);

    mostra_idade(idade); // A idade do usuário é: 30 anos

    mostra_idade(None); // A idade do usuário é desconhecida
}

fn mostra_idade(idade: Option<i32>) {
    match idade {
        Some(idade) => println!("A idade do usuário é: {} anos", idade),
        None => println!("A idade do usuário é desconhecida"),
    }
}
```

Mudamos o tipo `idade` para `Option<i32>`. Isso informa o compilador que o valor de `idade` pode ser `Some` ou `None`. Dessa forma o compilador nos obriga a tratar as duas possibilidades. Uma forma de fazer isso no Rust é através da expressão [`match`](https://doc.rust-lang.org/std/keyword.match.html). Nela, definimos o que deve ser executado para cada uma das possibilidades. Caso alguma das duas não seja implementada, o compilador nos mostrará um erro.

Existe muito mais para ser comentado sobre a tipagem do Rust, mas não me aprofundarei neste post. Apenas como exemplo, o tipo [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html) possui as variantes `Ok` e `Err`. Ele é utilizado para operações que podem ou não funcionar. Você também pode criar seus próprios tipos com [`enum`](https://doc.rust-lang.org/std/keyword.enum.html). Tanto `Result` quanto enums podem ser tratados em expressões `match`:

```rust
enum Status {
    Pendente,
    EmAndamento,
    Concluido,
}

fn main() {
    mostra_status(Status::Pendente); // Pendente
    mostra_status(Status::EmAndamento); // Em andamento
    mostra_status(Status::Concluido); // Concluído
}

fn mostra_status(status: Status) {
    match status {
        Status::Pendente => println!("Pendente"),
        Status::EmAndamento => println!("Em andamento"),
        Status::Concluido => println!("Concluído"),
    }
}
```

Essa técnica de tratar todas as variantes de um tipo atráves de expressões `match` é conhecida como [pattern matching](https://en.wikipedia.org/wiki/Pattern_matching) e é bastante utilizada em linguagens funcionais. Apesar de não estar disponíveis em outras linguagens que utilizo, como Python * e JavaScript, percebo que aprender como linguagens diferentes (especialmente linguagens de paradigmas diferentes dos que você está mais acostumado) tratam situações comuns em qualquer aplicação, agrega para a minha "caixa de ferramentas" como desenvolvedor.

\* Enquanto pesquisava para escrever este post, descobri que existe uma [proposta](https://www.python.org/dev/peps/pep-0634/) em andamento para adicionar pattern matching no Python. Seria uma excelente adição à linguagem!
