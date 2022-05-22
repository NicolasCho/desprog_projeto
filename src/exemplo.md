Algoritmo de Rabin-Karp para Busca em Texto
======

Imagine que temos um texto (que vamos considerar uma string [**txt**]) e que nela queremos buscar um determinado termo (que vamos
nos referir a substring, ou padrão [**pattern**]). Isso pode ser necessário para alguns casos que vemos no cotidiano, 
como encontrar palavras em uma página web (famoso ctrl+f) ou determinar plágios. Vejamos um exemplo em pequena escala.

Dentro do **txt**: *"oi como vai"*,  como faríamos para encontrar a palavra (**pattern**): *"vai"* ?

No caso, como consideramos uma string um *array* de chars, estamos querendo o índice de **txt** em que se encontra a palavra
*"vai"*, se ela existir:

![](exemplo_inicial.png)

Visualmente, é fácil enxergar que *"vai"* se encontra no índice 9 de **txt**, mas como faríamos para máquina encontrá-la?    

Ideias inicias
---------
A primeira ideia que pode ter vindo em sua cabeça deve ter sido iterar sobre o **txt** de caractere em caractere para verificar se o **pattern** bate com alguma substring analisada. 

Assim, começando do índice 0 de **txt**, verificamos se o caractere que está neste índice bate com o caractere inicial de nosso
**pattern**. Se coincidir, verificamos se o caractere do índice 1 de **txt** bate com o segundo caractere de **pattern** e assim 
por diante. Se alguma verificação falhar, quer dizer que a palavra não se encontra no índice 0, e assim repetimos esse processo
para o índice seguinte até encontrarmos a palavra (ou não, caso ela não existir no texto).

O código abaixo descreve a ideia mencionada.

``` c
int brute_force(char* txt, char* pat){
    //A função strlen() retorna o tamanho da string
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
Veja a animação a seguir com um exemplo do algoritmo descrito. Nela buscamos uma substring **"BAB"** no texto dado.

:brute_force 

O método analisado pode ser considerado uma forma de **força bruta**. Essa abordagem ingênua resolve o problema, mas de fato não é 
a forma mais elegante nem efetiva de resolver a questão.

??? Exercício 
Estime a complexidade do algoritmo ingênuo de força bruta descrito acima. **Não é necessário cálculo**. É possível estimar
a complexidade de maneira intuitiva ao analisar o algoritmo.

::: Gabarito
Considerando o tamanho de txt como *n* e o tamanho do pattern como *m*, no pior dos casos, teríamos que iterar para cada caractere
do txt *m* vezes para verificar se a substring bate o desejado. Assim é fácil ver que sua complexidade é de **O(nm**).
:::
???

Melhorando
---------

Como dito anteriormente, o algoritimo até que funciona, mas não é muito eficiente. Vamos tentar uma abordagem diferente para identificar *matches* de substrings.

O problema do método a força bruta apresentado é que ele itera pelas substrings de txt toda vez que precisamos compará-las com o 
pattern. Se conseguirmos extrair alguma espécie de valor que serve de identidade ou *impressão digital* para cada substring e 
pattern, talvez o processo de comparação seja mais rápido. 

Um bom modo de fazer isto seria utilizando a tabela ascii.

![](ascii.png)

Então, se fossemos analisar uma palavra como "hello", teríamos:

|     h    |     e    |     l    |     l    |     o    |
|----------|----------|----------|----------|----------|
|    104   |    101   |    108   |    108   |    111   |

Agora que sabemos o valor numérico para cada caractere, uma das formas de definir a suposta *impressão digital* da palavra
"hello" seria, por exemplo, somar todos os valores. Assim, "hello" poderia ser resumida por um valor numérico de 532.

Hashes
--------

O que fizemos na sessão anterior, ao somar os valores de todos os caracteres, descreve uma função de **hash** (apesar de
bem simples).

Uma função de hashing funciona transformando os dados de uma determinada entrada em uma saída de tamanho fixo, aplicando operações lógicas ou matemáticas. No caso,
se somarmos todos os caracteres de "hello" obteríamos 532, que seria a saída da função de hash aplicada sobre a palavra:

$$hash_{sum}(hello) = 532$$

Vamos agora aplicar essa ideia do hash de somatória para o algoritmo de força bruta que havíamos feito e tentar identificar
melhoras. No caso, se compararmos o hash, a *impressão digital*, do pattern com o hash da substring de txt analisada e eles
forem iguais, então vamos assumir que encontramos a palavra.

Primeiro precisamos calcular o hash do **pattern**:

``` c

int brute_force_numerico(char* txt, char* pat){
    int n = strlen(txt);
    int m = strlen(pat);
    int pat_hash = 0;

    for (int k = 0; k < m ; k++){
            pat_hash += pat[k];
        }

    ...
```

Em seguida, precisamos calcular o valor de hash para cada substring em **txt** e verificar se o valor corresponde ao 
valor do **hash de pattern**. 

O código final deve ficar da seguinte forma portanto:

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

Sim. Parece que ficou pior.

E de fato ficou.

??? Exercício
Qual é o problema de calcular um novo valor para cada trecho do texto?

::: Gabarito
Se nós simplesmente iterarmos pela substring para calcularmos um novo número em cada trecho possível a complexidade do algoritimo permanece **O(nm)**. 

Aliás, no algoritmo da força bruta original, parávamos de testar a substring caso encontrássemos um caractere em txt que não correspondesse ao pattern. No algoritmo que 
acabamos de implementar, calculamos o valor numérico percorrendo a substring inteira, podendo resultar em performance pior do que o algoritmo original.
:::
???

Então como resolver esse problema? 

Vamos analisar o que o algoritmo acima faz para o exemplo do começo do handout, ou seja, para o **txt** *"oi como vai"* e o 
**pattern** *"vai"*.

![](exemplo_inicial.png)

Vamos supor o momento em que o algoritmo está analisando a substring que começa no índice 8 de **txt**.

Obs: Na figura abaixo, os números embaixo de cada caractere representam seu valor numérico da tabela ascii.

![](antes.png)

O algoritmo então compara **247** com **320** e percebe que não são iguais e parte para o próximo índice, o 9.

![](depois.png)

As operaçõs realizadas pelo algoritmo nos dois momentos da imagem foram:

$$ (1):32 + 118 + 97 = 247$$

$$ (2):118 + 97 + 105 = 320$$

Se fossemos escrever de forma genérica:

![](generico.png)

$$ (1):a + b + c = y$$

$$ (2):b + c + d = x$$

Em que *y* é o valor de hash da substring que se inicia no índice 8 de **txt** e *x* é o hash da substring que se inicia
no índice 9.

??? Exercício
Encontre uma relação de *x* que dependa de *y* a partir das equações acima. O que podemos concluir portanto?

::: Gabarito
A partir de (1) temos:

$$ (1):b + c = y - a$$

Substituindo b + c em (2):

$$ (2):y - a + d = x$$

Logo, podemos concluir que para calcular o valor de hash da substring seguinte **não precisamos iterar sobre ela**, podemos apenas
pegar o valor do hash anterior (*d*) e subtrair o valor do caractere do índice anterior analisado (*a*). Em seguida, basta somar
o valor do último caractere da substring analisada atualmente (*e*).
:::
???

Rolling Hashes
----------

A ideia de utilizar valores de hashes anteriores para calcular novos hashes é abordada no conceito de Rolling Hashes. É mais fácil relacionar esta ideia a um hash que se move
de maneira contínua sobre o **txt**, não perdendo tempo, ou processamento, para avaliar caracteres que já foram analisados.

A animação seguinte facilita entender esta ideia mencionada no exercício anterior: de não precisar iterar sobre a substring. Nela buscamos o pattern "vai" que tem como hash o valor de 320.

:rolling_hash

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

Logo, o algoritmo feito na sessão anterior pode devolver um valor errado caso encontre uma substring com mesmo valor de hash que
o pattern, mas os caracteres da substring não coincidem com o pattern devido a ocorrência de colisão.

??? Reflexão
Sabendo que estamos utilizando a somatória dos caracteres com função de hashing, quais tipos de palavras que resultariam em 
colisões?

::: Gabarito
Palíndromos ou anagramas.

Ex: Hash(roma) = Hash(amor) = 431
:::
???

??? Exercício
Com base no algoritmo implementado anteriormente, adicione uma forma de verificar colisões.
!!! Aviso
A performance do algoritmo vai piorar.
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

Na verdade, o exercício que acabamos de resolver é a versão **Las Vegas** do algoritmo. Nesta versão, ao encontrar uma *match*
entre os **pat_hash** e **txt_hash**, devemos verificar de fato se a substring corresponde ao pattern desejado, corrigindo 
problemas de colisões. A versão que fizemos na sessão anterior (que considera o primeiro *match* já como problema resolvido) é 
considerada a versão **Monte Carlo** do algoritmo. 

A versão Monte Carlo não necessariamente tem um desempenho ruim, no sentido de resolver o problema. Ela *aposta* que está 
resolvido, pois espera que haja uma baixa probabilidade de ocorrer colisões. 

Para que isso seja verdade, precisamos utilizar um rolling hash bom o suficiente que reduza ao máximo a chance de colisão.

Indo além
--------

A função de hash mais popular no algoritmo de Rabin-Karp é o Hashing modular. Ela é um pouco mais elaborada do que a que
utilizamos no handout e é boa suficiente para assumir a versão de Monte Carlo de forma confiável.

Se quiser saber mais sobre essa função veja a sessão "Função de hashing modular" do seguinte link:

[https://www.ime.usp.br/~pf/estruturas-de-dados/aulas/st-hash.html](https://www.ime.usp.br/~pf/estruturas-de-dados/aulas/st-hash.html)