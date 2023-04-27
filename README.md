# Trabalho 1: Manipulação de arquivos

<sub>Última atualização: 26/04/2023</sub>

## Sumário

- [Visão geral e objetivos](#visão-geral-e-objetivos)  
- [Tarefas](#tarefas)
- [Requisitos](#requisitos)
- [Orientações gerais](#orientações-gerais)
- [Exemplos de entradas e saídas](#exemplos-de-entradas-e-saídas)
- [Autoria e política de colaboração](#autoria-e-política-de-colaboração)
- [Entrega](#entrega)
- [Avaliação](#avaliação)
- [Dúvidas e informações](#dúvidas-e-informações)

## Visão geral e objetivos

O objetivo deste trabalho é colocar em prática as operações de leitura e escrita em arquivos na linguagem de programação Go. O programa a ser implementado utiliza como fonte de dados um arquivo [CSV (*Comma-Separated Values*)](https://en.wikipedia.org/wiki/Comma-separated_values) contendo o número de nascidos vivos em cada um dos 167 municípios do Estado do Rio Grande do Norte entre os  anos de 1994 e 2020. Os dados são provenientes do Sistema de Informações sobre Nascidos Vivos(SINASC) do Departamento de Informática do Sistema Único de Saúde (DATASUS), vinculado ao Ministério da Saúde do Brasil, e estão disponíveis em http://tabnet.datasus.gov.br/cgi/tabcgi.exe?sinasc/cnv/nvuf.def.

Este trabalho explora os seguintes elementos da programação em Go, cujos conhecimentos são, portanto, ora necessários:

- Entrada e saída formatada via console
- Entrada e saída via arquivos
- Tipos estruturados (*structs*)
- Funções
- *Arrays* e *slices*
- Mapas

- Estruturas de controle de fluxo
- Tipos estruturados (*structs*)
- Funções
- *Slices*
- Manipulação de *strings*
- Interfaces
- Generics

## Tarefas

### Tarefa 1

A primeira tarefa deste trabalho consiste em implementar um programa chamado `nascimentos` que recebe como entrada, via linha de comando, um arquivo de texto no formato CSV contendo os números de nascidos vivos em cada município para cada ano contabilizado. Cada linha do arquivo refere-se a um município e os números de nascimentos em cada ano são separados por ponto-e-vírgulas. Enquanto o arquivo de entrada for sendo lido, os dados de cada município deverão ser armazenados em memória. Para tal armazenamento, pode-se fazer uso de tipos estruturados (*structs*) e/ou de estruturas de dados como *arrays*, *slices* ou mapas.

Ao abrir o arquivo CSV de entrada em um editor de texto ou editor de planilhas, será possível observar que a primeira linha do arquivo diz respeito a um cabeçalho para a tabela de dados e  que a primeira coluna contém o nome do município antecedido por um código numérico que o identifica. Durante a leitura do arquivo, o programa deverá separar o código numérico do nome do município, cada um sendo armazenado no respectivo campo do tipo estruturado, e deverá desprezar a última coluna. A última linha e a última coluna do arquivo CSV de entrada apresentam, respectivamente, o total de nascimentos em cada ano e o total de nascimentos em cada município somados os 27 anos  da série 1994-2020. No entanto, esses totais deverão ser usados **apenas** para fins de verificação  de consistência. Para isso, deverá ser implementada uma função de verificação para verificar se os totais informados no arquivo conferem com a série de dados lida.

Após a leitura do arquivo, o programa deverá computar as seguintes estatísticas que resumem o conjunto de dados em questão na série histórica:

1. o *maior* número de nascimentos em cada ano;
2. o *menor* número de nascimentos em cada ano;
3. a *média* do número de nascimentos em cada ano;
4. o *desvio padrão* do número de nascimentos em cada ano, e;
5. o número *total* de nascimentos em cada ano.

Para calcular o desvio padrão $\sigma$ do número de nascimentos em um determinado ano, pode-se utilizar a seguinte equação:

$$ \sigma = \sqrt{\frac{1}{M} \sum_{i=1}^{M} \(n_i - MD\)^2} $$

em que $M$ é o número de municípios, $n_i$ é o número de nascimentos do $i$-ésimo município no ano em questão e $MD$ é a média do número de nascimentos nesse ano. Um baixo desvio padrão indica que os pontos dos dados tendem a estar próximos da médiado, enquanto que um alto desvio padrão indica que os pontos dos dados estão espalhados por uma ampla gama de valores.

Como saída, o programa deverá gerar automaticamente dois arquivos:

1. um arquivo de texto no formato CSV chamado `estatisticas.csv`, no qual cada linha corresponde a um ano e suas respectivas estatísticas acerca do número de nascimentos, cada valor sendo separado por ponto-e-vírgulas, e;
2. um arquivo de texto chamado `totais.dat` contendo **apenas** o ano e o respectivo número total de nascimentos nesse ano, separados por **espaço**.

Uma vez gerado o arquivo `totais.dat` como saída da execução do programa implementado, deverá ser gerado automaticamente um [histograma](https://en.wikipedia.org/wiki/Histogram) que mostrará a evolução do número de nascimentos entre os anos de 1994 e 2020. Para gerar esse gráfico, pode-se utilizar o programa [`gnuplot`](http://www.gnuplot.info/) ou outro programa ou biblioteca que ofereça a mesma funcionalidade, seja em Go ou em outra linguagem de programação. Contudo, a fim de minimizar uma maior curva de aprendizado, sugere-se o uso do `gnuplot`, com o qual o histograma pode ser gerado de forma simples executando o seguinte comando no terminal do sistema operacional:

```bash
$ gnuplot -e  "filename='totais.dat'" histograma.gnuplot
```

sendo `totais.dat` é o arquivo anteriormente gerado e que contém os dados a serem plotados no histograma e o arquivo [`histograma.gnuplot`](histograma.gnuplot) é um *script* de configuração para instrução do quando da geração do gráfico. Esse *script*, disponível neste repositório, possui o seguinte conteúdo:

```
# Inicialização
clear
reset
set key off

# Configurações de saida: inclui formato de exportação, tamanho do gráfico, 
# fontes utilizadas e nome do arquivo de saída

# Exportação para o formato PNG
set terminal png size 640,480 enhanced font 'Helvetica,12'
set output 'histograma.png'

# Exportação para o formato JPG
# set terminal jpeg size 640,480 enhanced font 'Helvetica' 12
# set output 'histograma.jpg'

# Exportação para o formato SVG
# set terminal svg size 640,480 enhanced background rgb 'white' fname 'Helvetica' fsize 14 butt solid
# set output 'histograma.svg'

# Título do gráfico
set title 'Total de nascidos vivos no RN (1994-2020)'

# Configurações do eixo horizontal
set xrange[1994:2020]           # Faixa de valores
set xtics 1                     # Salto entre valores
set xtic rotate by -45 scale 0  # Rotação dos rótulos

# Configurações do eixo vertical
set yrange[0:80000]             # Faixa de valores

# Seleção do tipo de gráfico a ser gerado (histograma)
set style data histogram
set style histogram clustered gap 1
set style fill solid border -1      # Preenchimento e contorno
set linetype 1 lc rgb 'green'       # Cor
set boxwidth 0.6                    # Largura das barras verticais

# Plotagem do gráfico
# Os dados a serem plotados constam no arquivo totais.dat
plot 'totais.dat' using 1:2 title '' smooth freq with boxes
```

Por padrão, esse *script* gerará um gráfico na forma de um arquivo de imagem no formato PNG, a saber, `histograma.png`. Para gerar em outros formatos de arquivos de imagem, o caractere `#` (o qual indica um comentário) das respectivas linhas responsáveis pela geração no formato em questão, iniciadas pelos comandos `set terminal` e `set output`. É importante destacar que **não é possível** gerar mais de um arquivo de imagem em uma única plotagem (comando `plot`), ou seja, é necessário repetir o comando de plotagem de dados para cada arquivo de imagem a ser gerado, caso deseje-se gerar saída em múltiplos formatos.

Além da [documentação oficial](http://gnuplot.info/documentation.html) do `gnuplot`, bons exemplos de uso e demonstração podem ser encontrados neste [link](http://alvinalexander.com/technology/gnuplot-charts-graphs-examples) e neste outro [link](http://gnuplot.sourceforge.net/demo/).

## Tarefa 2

A segunda tarefa consiste em estender o programa implementado até a realização da Tarefa 1 para que ele seja capaz de fornecer, na saída padrão, quais municípios apresentaram a maior e a menor *taxa de crescimento* no número de nascimentos quando comparados os anos inicial e final da série histórica. A taxa de crescimento relativa $TC$ de um município $M$ pode ser calculada através da seguinte equação:

$$ TC(M) = \frac{N_{2020}(M)}{N_{1994}(M)} $$

em que $N_{2020}$ e $N_{1994}$ são respectivamente os números de nascidos vivos nos anos de 2020 e 1994. Caso o valor de $TC$ para o município $M$ seja inferior a 1, tem-se na verdade uma *taxa de queda* (ou *taxa de crescimento negativa*).

Os menor e maior valores de taxa de crescimento deverão ser apresentados na saída padrão conforme o seguinte exemplo:

```bash
$ ./estatisticas Nascimentos_RN.csv
> Arquivo estatisticas.csv gerado
> Arquivo totais.dat gerado

Município com maior taxa de crescimento na série histórica 1994-2020: X ()
Município com menor taxa de crescimento na série histórica 1994-2020: Y ()
```



1. Qual município apresentou a maior *taxa de queda* no número de nascimentos quando comparados os anos de 2020 e 1994?
2. Qual município apresentou a maior *taxa de crescimento* no número de nascimentos quando comparados os anos de 2020 e 1994?

As seguintes tarefas deverão ser realizadas na implementação do programa `genmap`:

1. Implementar um tipo estruturado (*struct*) genérico chamado `Entry` que represente uma entrada do mapa, composta de uma chave e um valor. A chave deve ser unicamente do tipo inteiro ou *string*, enquanto o valor pode ser de qualquer tipo passível de utilização de operadores relacionais. Por questões de simplicidade, deve-se assumir que os valores são de tipos dentre os tipos primitivos preexistentes em Go.
2. Implementar um tipo estruturado (*struct*) genérico chamado `Map` que representa o mapa propriamente dito, composto por um conjunto de entradas, ou seja, elementos do tipo `Entry`. O número de entradas é indefinido *a priori*.
3. Implementar as seguintes funções genéricas para realizar operações sobre o mapa. A chamada às funções deve ser realizada por meio de uma abreviação (*case insensitive*) a ser informada pelo usuário através da entrada padrão. As abreviações devem ser **exatamente** as listados a seguir.

    1. **_addEntry_ (abreviação _add_):** Adiciona uma nova entrada ao mapa, exibindo na saída padrão o mapa atualizado com a entrada adicionada. A chave e o valor a serem armazenados na entrada deverão ser informados pelo usuário através da entrada padrão. Caso já exista uma entrada para a chave informada, o valor armazenado deve ser sobrescrito pelo novo valor. Esta função deve receber como parâmetro o mapa que será modificado, a chave e o valor a serem adicionados.
    2. **_getEntry_ (abreviação _get_):** Exibe na saída padrão uma entrada armazenada no mapa a partir de uma determinada chave, percorrendo o conjunto de entradas por meio da comparação com a chave informada. Caso a chave não exista, o programa deverá exibir uma mensagem na saída padrão informando que a chave é inexistente.
   3. **_mapSize_ (abreviação _size_):** Exibe na saída padrão o número de entradas atualmente armazenadas no mapa.
   4. **_printMap_ (abreviação _print_):** Exibe na saída padrão todas as entradas atualmente armazenadas no mapa e o número de entradas.

4. O programa deve continuamente aceitar novas abreviações providas pelo usuário através da entrada padrão. O programa deverá ainda exibir uma mensagem de erro caso seja fornecido um número inválido de argumentos a *add* ou a *get*, bem como caso seja fornecida uma abreviação para uma função não implementada. Por fim, caso o usuário informe a abreviação *exit*, o programa deve ser encerrado.

Por questões de simplicidade, na função principal do programa, deve-se admitir que o mapa irá armazenar chaves do tipo inteiro e valores do tipo *string* e, portanto, esses tipos são os que deverão ser utilizados na instanciação do mapa. Contudo, **todos os tipos estruturados e funções devem ser genéricos**.

## Requisitos

A implementação deste trabalho requer os seguintes elementos instalados no ambiente de desenvolvimento:

- [Git](https://git-scm.com), como sistema de controle de versões
- [Go](https://go.dev), incluindo compilador, ambiente de execução e outras ferramentas associadas à linguagem de programação Go

## Orientações gerais

Boas práticas de programação deverão ser constantemente aplicadas no desenvolvimento do programa. Assim, o programa deverá ser codificado de forma legível (com indentação de código fonte, nomes consistentes, etc.) e documentado adequadamente na forma de comentários.

O programa deverá ainda ser desenvolvido com qualidade, garantindo que o ele funcione de forma correta e eficiente. Deve-se também pensar nas possíveis entradas que poderão ser utilizadas para testar apropriadamente o programa, além de serem tratadas adequadamente possíveis entradas consideradas inválidas.

## Exemplos de entradas e saídas

```bash
$ genmap

> print
[]
0

> add 1 "Hello"
[1] "Hello"

> add 2 "World"
[1] "Hello"
[2] "World"

> get 2
[2] "World"

> size
2

> exit
```

Note que a exibição de cada entrada na saída padrão deve obedecer ao formato `[<chave>] valor` (ou seja, com colchetes circundando as chaves). Caso o mapa esteja vazio, devem ser impressos apenas os colchetes seguidos do número zero.

## Autoria e política de colaboração

Este trabalho deverá necessariamente ser realizado em equipe composta de **até dois estudantes**, sendo importante, dentro do possível, dividir as tarefas igualmente entre os integrantes da equipe. Após a implementação das soluções para os problemas propostos, o arquivo [`author.md`](https://github.com/ufrn-golang/lista2/tree/master/author.md) presente no repositório deverá ser editado preenchendo as informações de identificação dos integrantes da equipe, na seção [Informações de Autoria](https://github.com/ufrn-golang/lista2/tree/master/author.md#identificação-de-autoria). 

O trabalho em cooperação entre estudantes da turma é estimulado, sendo admissível a discussão de ideias e estratégias. Contudo, tal interação não deve ser entendida como permissão para utilização de (parte de) código fonte de colegas, o que pode caracterizar situação de plágio. Trabalhos copiados no todo ou em parte de outros colegas ou da Internet serão anulados e receberão nota zero.

## Entrega

O sistema de controle de versões [Git](https://git-scm.com) e o serviço de hospedagem de repositórios [GitHub](https://git-scm.com) serão utilizados para possibilitar a entrega da implementação realizadas. Para possibilitar a associação de repositórios Git para cada equipe e reuni-los sob uma mesma infraestrutura, foi criada uma atividade (*assignment*) no GitHub Classroom. Cada integrante de equipe deverá acessar este [*link*](https://classroom.github.com/a/5aSHjU-U), aceitar o convite para ingressar no GitHub Classroom e finalmente seguir as instruções em tela para acessar a atividade e ingressar em uma equipe existente ou criar outra. Este [vídeo](https://youtu.be/ObaFRGp_Eko) demonstra como ocorre esse processo.

No momento de criação de uma equipe, o GitHub Classroom cria um repositório Git privado acessível unicamente pelos integrantes da equipe e pelo docente, sob a organização [`ufrn-golang`](https://github.com/ufrn-golang). A fim de garantir a boa manutenção do repositório, deve-se ainda configurar corretamente o arquivo `.gitignore` para desconsiderar arquivos que não devam ser versionados, a exemplo do arquivo executável gerado a partir da compilação do código fonte.

A implementação do programa objeto deste trabalho deverá ser realizada **até as 11:00 do dia 17 de abril de 2023** no respectivo repositório Git da equipe. Para fins de registro, o endereço do repositório também deverá ser **obrigatoriamente** enviado através da opção *Tarefas* na Turma Virtual do SIGAA, devendo **um único membro da equipe** realizar esse envio. Além disso, **não serão aceitos envios por outros meios ou repositórios que não sejam os descritos nesta especificação.**

## Avaliação

A avaliação do programa implementado contabilizará nota de até 10,0 pontos. O programa implementado será avaliado de acordo com os seguintes critérios:

- utilização correta dos recursos providos pela linguagem de programação Go;
- corretude da execução do programa implementado, que deve apresentar saída em conformidade com a especificação;
- aplicação de boas práticas de programação, incluindo legibilidade, organização e documentação de código fonte, e;
- correta utilização do repositório Git, no qual deverá ser registrado todo o histórico da implementação por meio de *commits*.

O não cumprimento de algum dos critérios de avaliação especificados poderá resultar nos seguintes decréscimos, aplicados sobre a nota obtida até então na avaliação:

| Falta | Decréscimo |
| :--- | ---: |
| Falta de comentários no código fonte | -10% |
| Uso inadequado de controle de versão com Git | -20% |
| Falta de especificação ou especificação incorreta da autoria do trabalho (arquivo [`author.md`](https://github.com/ufrn-golang/lista2/tree/master/author.md)) | -20% |
| Código fonte com legibilidade prejudicada (por exemplo, com identação ou nomenclatura inadequada) | -30% |
| Implementação realizada em desacordo com as especificações postas para o trabalho | -50% |
| Programa apresenta erros de compilação, não executa ou apresenta saída incorreta | -70% |
| Plagiarismo | -100% |

## Dúvidas e informações

Caso haja qualquer dúvida, questionamento ou necessidade de informação adicional, é possível:

- enviar *e-mail* para o endereço everton.cavalcante@ufrn.br;
- enviar mensagem privada diretamente ao docente, utilizando o servidor Discord;
- enviar mensagem no canal de texto `#duvidas` no servidor Discord, ou;
- agendar encontros síncronos pelo canal de áudio `Fale com Prof. Everton` no servidor Discord.
