Algoritmo de Rabin-Karp para Busca em Texto
======

Imagine que temos um texto (que vamos considerar uma string [**txt**]) e que nela queremos buscar um determinado termo (que vamos
nos referir a substring, ou padrão [**pattern**]). Isso pode ser necessário para alguns casos que vemos no cotidiano, 
como encontrar palavras em uma página web (famoso ctrl+f) ou determinar plágios.

Ideias inicias
---------
A primeira ideia que pode ter vindo em sua cabeça deve ter sido iterar sobre o **txt** de caractere em caractere para verificar se o **pattern** bate com alguma substring analisada. Vamos analisar um pouco essa solução.

??? Exercício 
Escreva uma função **brute_force()** que recebe uma string **txt** e uma string **pattern** e, iterando sobre o txt, busca o pattern desejado.
A função deve retornar o indice em que se encontra o pattern caso exista. Se não encontrar, retorna -1. Considere que txt sempre será maior que pattern.

*Dica*: Use a função **strlen()** para determinar tanto o tamanho do txt quanto do pattern.
::: Gabarito
``` c
int brute_force(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
 
    for (int i = 0; i <= n - m; i++) {
 
        for (int j = 0; j < m; j++){
            if (txt[i + j] != pat[j]){
                break;
            }
        }

        if (j == m){
            return i;
        }
    }
    return -1;
}
```
:::
???

O método analisado pode ser considerado uma forma de força bruta. Essa abordagem ingênua resolve o problema, mas de fato não é 
a forma mais elegante nem efetiva de resolver a questão.

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

Como dito anteriormente, o algoritimo até que funciona, mas não é muito eficiente. Vamos tentar uma abordagem diferente para identificar *matches* de substrings.
No caso vamos atribuir um valor numérico para cada caractere e vamos considerar um *match* quando os dois valores numéricos concidirem.

Para tanto, um bom modo de fazer isto seria utilizando a tabela ascii.

![](ascii.png)

Então, se fossemos analisar uma palavra como "hello", teríamos:

|     h    |     e    |     l    |     l    |     o    |
|----------|----------|----------|----------|----------|
|    104   |    101   |    108   |    108   |    111   |

Agora que sabemos o valor numérico para cada caractere, devemos encontrar uma forma identificar *matches* entre as substrings e o **pattern**.

Hashes
--------

Uma ideia inicial (e que você provavelmente deve ter pensado ao ver a tabela acima) para identificar os *matches* seria simplesmente somar os valores de cada caractere da palavra.

E, de fato, iremos utilizar esta ideia para agilizar a busca, o **hashing**.

Uma função de hashing funciona transformando os dados de uma determinada entrada em uma saída de tamanho fixo, aplicando operações lógicas ou matemáticas. No caso,
se somarmos todos os caracteres de "hello" obteríamos 532, que seria a saída da função de hash aplicada sobre a palavra:

$$hash_{sum}(hello) = 532$$

??? Exercício
Reescreva o algoritmo de força bruta feito anteriormente, agora identificando matches por meio dos valores numéricos.

*Dica*: Apesar da tabela ascii ter sido mencionada anteriormente, não é preciso fazer nenhuma transformação dos caracteres. Operações aritméticas com caracteres
já serão consideradas utilizando os valores da tabela ascii.

!!! Aviso
O que o exercício busca é a forma mais intuitiva de reescrever a abordagem da força bruta. Mas talvez você já tenha notado uma ideia que iremos abordar na próxima sessão.
!!!
::: Gabarito
``` c
int brute_force_numerico(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
    int pat_hash = 0;

    for (int k = 0; k < m ; k++){
        pat_hash += pat[k];
    }
    
    int txt_hash;
    for (int i = 0; i <= n - m; i++) {
        txt_hash = 0;

        for (int j = 0; j < m; j++){
            txt_hash += txt[i+j]; 
        }

        if (pat_hash == txt_hash){
            return i;
        }
    }
    return -1;
}
```
:::
???

Sim. Parece que ficou pior.

E de fato ficou.


??? Exercício
Qual é o problema de calcular um novo valor para cada trecho do texto?

::: Gabarito
Se nós simplesmente calcularmos um novo número para cada trecho possivel a complexidade do algoritimo permanece **O(nm)**. 

Aliás, no algoritmo da força bruta original, parávamos de testar a substring caso encontrássemos um caractere em txt que não correspondesse ao pattern. No algoritmo que 
acabamos de implementar, calculamos o valor numérico percorrendo a substring inteira, podendo resultar em performance pior do que o algoritmo original.
:::
???

Então como resolver esse problema? 

Basta usar os valores de hash que já foram calculados para determinar o valor de hash do próximo trecho. Isto faz com que o algoritimo
fique com a complexidade de **O(n)** na maioria dos casos.

Rolling Hashes
----------

A ideia de utilizar valores de hashes anteriores para calcular novos hashes é abordada no conceito de Rolling Hashes. É mais fácil relacionar esta ideia a um hash que se move
de maneira contínua sobre o **txt**, não perdendo tempo, ou processamento, para avaliar caracteres que já foram analisados.

Como mencionado anteriormente, talvez você já tenha percebido como fazer isso no exercício de antes. 

Se estamos utilizando a soma dos caracteres como uma função de hash, então para calcular o hash da substring seguinte, que apenas anda um espaço para direita (remove o 
caractere mais a esquerda e adiciona um caractere mais a direita), basta subtrair do hash anterior o valor numérico do caractere mais a esquerda da substring anterior e somar o
valor do novo caractere mais a direita.

A animação seguinte facilita entender esta ideia.

**Animação legal**

A busca em texto utilizando-se de rolling hashes para identificar matches é conhecida como **Algoritmo de Rabin-Karp**.

??? Exercício
Implemente o algoritmo de Rabin-Karp utilizando a soma dos caracteres como rolling hash.

::: Gabarito
``` c
int rabin_karp_sum(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
    int pat_hash = 0;
    int txt_hash = 0;

    for (int k = 0; k < m ; k++){
        pat_hash += pat[k];
        txt_hash += txt[k];
    }
    
    for (int i = 0; i <= n - m; i++) {
        if(pat_hash == txt_hash){
            return i;
        }
        txt_hash = txt_hash - txt[i] + txt[i+m];
    }
    return -1;
}
```
:::
???

Uma das vantagens do algoritmo é a sua eficiência em realizar múltiplas buscas simultaneamente. Além disso, ocupa menos espaço de **memória** uma vez que não precisa de 
estruturas auxiliares para aramazenar dados, se comparado a outros algoritmos do tipo.

Colisões
---------
Anteriormente havíamos mencionado que o uso do rolling hash "faz com que o algoritimo fique com a complexidade de **O(n)** na maioria dos casos". Pense um pouco no porquê
mencionarmos "na maioria dos casos".

O título da sessão entrega a resposta. Em funções de hash, colisões ocorrem quando duas entradas possuem a mesma saída. Sendo assim, é possível que em um **txt** haja duas ou 
mais substrings que possuem o mesmo valor de saída e que esse valor de saída seja um *match* com o valor de **pattern**.

Logo, o algoritmo feito na sessão anterior pode devolver um valor errado caso encontre uma substring com mesmo valor de hash que o pattern, mas os caracteres da substring não
coincidem com o pattern devido a ocorrência de colisão.

O exemplo abaixo mostra uma colisão utilizando o hash de somatória que estamos implementando.

**EXEMPLO SHOW**

??? Exercício
Com base no algoritmo implementado anteriormente, adicione uma forma de verificar colisões.
!!! Aviso
Sim, a performance do algoritmo vai piorar.
!!!

::: Gabarito
A única forma de termos certeza de que encontramos a substring desejada é verificando se os caracteres das substrings que deram *match* coincidem com o pattern. 

``` c
int rabin_karp_sum(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
    int pat_hash = 0;
    int txt_hash = 0;

    for (int k = 0; k < m ; k++){
        pat_hash += pat[k];
        txt_hash += txt[k];
    }
    
    for (int i = 0; i <= n - m; i++) {
        if(pat_hash == txt_hash){
            for(int j = 0; j < m; j++){
                if(pat[j] != txt[i+j]){
                    break;
                }
            }
            if(j == m){
                return i;
            }
        }
        txt_hash = txt_hash - txt[i] + txt[i+m];
    }
    return -1;
}
```
:::
???

**FALAR SOBRE A IMPORTÂNCIA DE UM BOM HASH E MÉTODO MONTE CARLO E LAS VEGAS**

**DESAFIO É IMPLEMENTAR COM HASH MODULAR**

APAGAR
--------

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