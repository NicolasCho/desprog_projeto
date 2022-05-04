Algoritmo de Rabin-Karp para Busca em Texto
======

O algoritmo de Rabin-Karp foi criado com intuito de encontrar uma substring em uma string.
Normalmente utilizado em:

* encontrar termos em um texto;

* achar palavras em uma página web;

* ferramentas de plágio;

Sua eficiência
---------
Uma das vantagens do algoritmo é a sua eficiência em realizar múltiplas buscas simultaneamente. Além disso, ocupa menos espaço na **memória**, nesse requisito, o Rabin-Karp é melhor que os outro algoritmos do mesmo tipo. 


??? Exercício 

Por que a `md Força Bruta` é considerado uma `md Abordagem Ingênua`, ou seja, não é eficiente ao problema apresentado?
E qual é a sua complexidade?
::: Gabarito
Porque a `md Força Bruta` seria uma ideia bem demorada, ela percorreria a *string* inteira para encontrar a *substring* desejada. Portanto, obsevando o tamanho da *string* e da *substring*, a sua complexidade é *O(nm)*.
:::

???


:brute_force 


e imagens. Lembre que todas as imagens devem estar em uma subpasta *img*.

![](logo.png)

Para tabelas, usa-se a [notação do
MultiMarkdown](https://fletcher.github.io/MultiMarkdown-6/syntax/tables.html),
que é muito flexível. Vale a pena abrir esse link para saber todas as
possibilidades.

| coluna a | coluna b |
|----------|----------|
| 1        | 2        |

Ao longo de um texto, você pode usar *itálico*, **negrito**, {red}(vermelho) e
[[tecla]]. Também pode usar uma equação LaTeX: $f(n) \leq g(n)$. Se for muito
grande, você pode isolá-la em um parágrafo.

$$\lim_{n \rightarrow \infty} \frac{f(n)}{g(n)} \leq 1$$

Para inserir uma animação, use `md :` seguido do nome de uma pasta onde as
imagens estão. Essa pasta também deve estar em *img*.

:bubble



Você também pode inserir código, inclusive especificando a linguagem.

``` py
def f():
    print('hello world')
```

``` c
void f() {
    printf("hello world\n");
}
```

Se não especificar nenhuma, o código fica com colorização de terminal.

```
hello world
```


!!! Aviso
Este é um exemplo de aviso, entre `md !!!`.
!!!