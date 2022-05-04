Algoritmo de Rabin-Karp para Busca em Texto
======

Imagine que temos um texto (que vamos considerar uma string [**txt**]) e que nela queremos buscar um determinado termo (que vamos
nos referir a substring, ou padrão [**pattern**]). Isso pode ser necessário para alguns casos que vemos no cotidiano, 
como encontrar palavras em uma página web (famoso ctrl+f) ou determinar plágios.

Ideias inicias
---------
A primeira ideia que pode ter vindo em sua cabeça deve ter sido iterar sobre o **txt** de caractere em caractere para verificar se o **pattern** bate com alguma substring analisada. Vamos analisar um pouco essa solução.

??? Exercício 
Escreva uma função **brute_force()** que recebe um **txt** e um **pattern** e, iterando sobre o txt, busca o pattern desejado.
A função deve retornar o indice em que se encontra o pattern caso haja. Se não encontrar, retorna 0.

*Dica*: Use a função **strlen()** para determinar tanto o tamanho do txt quanto do pattern.
::: Gabarito
``` c
void brute_force(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
 
    for (int i = 0; i <= n - m; i++) {
        int j;
 
        for (j = 0; j < m; j++){
            if (txt[i + j] != pat[j]){
                break;
            }
        }

        if (j == m)
            return i;
    }
    return 0;
}
```
:::
???

O método analisado pode ser considerado uma forma de força bruta. Essa abordagem ingênua resolve o problema, mas de fato não é 
a forma mais elegante nem efetiva de abordar a questão.

??? Exercício 
Estime a complexidade do algoritmo ingênuo de busca em texto descrito acima. Não é necessário cálculo, é possível identificar
a complexidade de maneira intuitiva ao analisar o algoritmo.

::: Gabarito
Considerando o tamanho de txt como *n* e o tamanho do pattern como *m*, no pior dos casos, teríamos que iterar para cada caractere
do txt *m* vezes para verificar se a substring bate o desejado. Assim é fácil ver que sua complexidade é de **O(nm**).
:::
???

Veja a animação a seguir com um exemplo do algoritmo descrito acima. Nela buscamos uma substring **"BAB"** no texto dado.

:brute_force 

Melhorando
---------

O algoritimo até que está funcionando, mas ainda temos como melhorar ele. Primeiro podemos ao inves de analasir caracter por carcter, é melhor transformar a sub-string em um número e então ir calculando o valor de um treho do texto e comparar os valores.

Ex: 'hallo' = 5

??? Exercício
Qual é o problema de calcular um novo valor paracada trecho do texto?

::: Gabarito
Se nós simplesmente calcularmas um novo número paracada trecho possivel a complexidade do algoritimo permanece **O(nm)**.
:::
???

Então como resolver esse problema? Basta usar os valores que já foram cálculados para realizar o achar o valor do prõximo trecho o que faz o algoritimo ficar com a complexidade de **O(n)** na maioria dos casos.

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