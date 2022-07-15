# Basic of Ruby and Rails

### Por Fabio Carneiro

​	Estas anotações são conceitos básicos porém fundamentais que obtive pela minha experiência estudando Ruby, lendo isto você não obter um conhecimento aprofundado sobre ruby de modo a ser um desenvolvedor, mas vai te ajudar a entender como tudo funciona e acredito que será tão bom para seu começo como foi para mim construir este documento, de início eu ia guardar só para mim, mas penso que pode ajudar mais pessoas, não tenho planejamento de tornar este documento mais formal ou algo mais do estilo acadêmico, este documento tem o objetivo de servir como o primeiro passo de toda a sua jornada Ruby, eu fui escrevendo isso conforme fui estudando e aprendendo, isso deve ter me custado um total de 5 dias, mas leia com calma, estude com tranquilidade e exercite, espero que meu trabalho seja útil.

​	Eu iniciei meu estudos em uma  versão mais antiga de Ruby e Rails por que é o que eu consegui encontrar, meus estudos iniciaram com ruby 2.5 rails 5.2 em um ubuntu 18.04 virtualizado pelo vagrant, talvez tudo o que coloquei aqui não ajude 100% das pessoas que lerem, porém documentei aqui a solução de todos os problemas que obtive para configurar o ambiente no Windows 11, então acredito que muitas das pessoas que lerem isso serão ajudadas.

​	No início parece que já comecei adiantado, mas como disse, era para ser um documento pessoal, mas continue lendo que mais para frente torna-se tudo mais didático, a seguir, leia como um desabafo ou um comentário sobre como estava sendo minha experiência no meu primeiro contato com ruby, depois de relatar problemas e soluções que consegui aplicar, este documento se mantém no conceito de conhecimento fundamental sobre a linguagem ruby, boa leitura.

​	--*Fabio Carneiro*

​	Ruby funciona melhor em sistemas linux, então é importante usar docker por questão de portabilidade ou vagrant.

​	A ferramenta rbenv é um facilitador para gerenciar ruby no sistema, mas acredito que não há uma real necessidade em usar, é mais complicado de configurar mas é possível instalar o pacote ruby-full pelo gerenciador de pacotes apt.

​	Existem outros gerenciadores de versões do ruby, como chruby, RVM e uru que aparentemente funciona para windows.

​	Uma vez que tenha o ruby instalado, automaticamente temos o gem, que é um gerenciador de dependências para o ruby, podendo usar a sintaxe:

```
gem install dependência -v X.X.X
```

​	Assim estamos declarando que vamos instalar uma determinada dependência em uma versão específica, mas pode não usar: -v X.X.X que o gem irá buscar a opção mais recente possível.

​	Um problema que tive, foi na geração de um projeto, ele acusou um problema na instalação do sqlite3, que é usado nativamente pelo framwork rails, no caso do WSL Ubuntu precisei instalar o pacote: sqlite3-dev, e o postgreSQL parece ser a opção de produção mais desejada, talvez sqlite3 seja mais para estudo.

​	Quando precisa criar um novo projeto, pode usar o comando:

```
rails new nome_projeto
```

​	Podemos também definir qual banco de dados vamos usar no nosso projeto:

```
rails new nome_projeto -d mysql/sqlite3/postgresql
```

​	E quando for iniciar:

```
rails server
```

​	ou se preferir a forma resumida:

```
rails s
```

​	Em meus testes, encontrei muitas dificuldades em montar um ambiente ruby no windows mesmo com wsl e decidi estudar mais sobre o vagrant a ponto de poder usar a ferramenta de forma decente e quando configurado o Vagrantfile para minha box compartilhar a porta com o windows, simplesmente não funcionou, uma solução que encontrei foi quando iniciar o servidor dentro do box, rodar o seguinte comando:

```
rails s -b 0.0.0.0
```

​	e claro, precisei adicionar a linha abaixo no arquivo Vagrantfile do box que estou usando para desenvolvimento:

```yaml
config.vm.network :forwarded_port, guest: 3000, host: 3000 # rails
```

​	Ficando então o arquivo completo do meu box assim:

```yaml
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox"
  config.vm.box = "ubuntu/bionic64"

  config.vm.network :forwarded_port, guest: 3000, host: 3000 # rails
end
```

​	Eu tive problemas de dependências, que foram resolvidas com o comando:

```bash
bundle install
```

​	Uma vez dentro da pasta do projeto criado, podemos mandar o rails criar e configurar o  banco escolhido na criação do projeto, uma vez tudo foi devidamente configurado e dentro de:

```
/raiz_projeto/config/database.yml
```

​	Na linha referente a senha do banco, você coloca a senha de acesso ao mysql por exemplo:

```yaml
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: minha_senha <---- aqui
  socket: /var/run/mysqld/mysqld.sock
```

​	Este comando parece fazer uma varredura no projeto buscando as dependências que ele vai usar, avisa o gem, e o gem em seguida instala as dependências necessárias para poder iniciar o projeto, por mais que tentei mudar a versão do rails 5.2.0 usado no curso, ele sempre insiste em puxar a versão 7, pode ser que seja algo a ver com o gerenciador rbenv, ou por conta da própria versão do ruby estar amarrada a uma versão de rails. Porém usando o Vagrant configurado para uma box do ubuntu 18.04 com o ambiente configurado devidamente usando o RVM tudo funcionou como desejado, inclusive o link da minha box está [aqui](https://app.vagrantup.com/fabioaacarneiro/boxes/ubuntu1804-rubyonrails-dev)

## Um Pouco Sobre RVM

 Ruby Version Manager é um gerenciador de versões e ambientes ruby, e alguns comandos importantes para saber:

| comandos          | o que faz                                        |
| ----------------- | ------------------------------------------------ |
| rvm use X.X.X     | rvm vai deixar como principal a versão escolhida |
| rvm list known    | lista versões do repositório                     |
| rvm get head      | atualiza a lista                                 |
| rvm list          | lista versões instaladas                         |
| rvm install X.X.X | instalada uma versão específica                  |

*podemos por exemplo usar: rvm install ruby-2.4.4 para a versão 2.4.4*

## Um Pouco Sobre Vagrant

​	Vagrant é um gerenciador de ambientes virtuais portáteis que usa o virtualbox e vários outros como base, então é importante possuir ambos instalados.

| comandos                     | o que faz                                                    |
| ---------------------------- | ------------------------------------------------------------ |
| vagrant global-status        | verifica se as vms estão desligadas                          |
| vagrant halt                 | desliga as máquinas virtuais                                 |
| vagrant init ubuntu/bionic64 | gera um Vagrantfile para instalar box do ubuntu bionic       |
| vagrant up                   | usa o Vagrantfile no diretório atual para inciar a intalação da box |
| vagrant ssh                  | conecta via ssh a box baixada e configurada                  |

*obs* eu tive problemas com a combinação Virtualbox + Vagrant, então pesquisei e fiz alguns testes te descobri que o Virtualbox não iniciava e o Vagrant não conseguia usar ele, e por isso simplesmente não funcionava, os comandos que eu encontrei e que aparentemente corrigiram o problema do Virtualbox não funcionar no windows 11 foram:

```
bcdedit /set hypervisorlaunchtype off
```

e depois:

```
DISM /Online /Disable-Feature:Microsoft-Hyper-V
```

## Configurando o box ubuntu (18.04) para Ruby

Uma vez carregado o sistema com o comando *vagrant ssh* vamos rodar os comandos:

*atualizar a lista de repositórios*

```bash
sudo apt update
```

*atualizar os programas no box*

```bash
sudo apt upgrade -y
```

*instalar dependências e pacotes importantes para o sistema*

```bash
sudo apt install -y build-essential autoconf bison libssl-dev libyaml-dev libreadline-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm-dev libpq-dev curl ruby-full git gnupg2 nodejs mysql-client mysql-server libmysqlclient-dev postgresql postgresql-contrib libpq-dev
```

## Instalando RVM no box Ubuntu 18.04

​	Os comandos a seguir vão fazer: adicionar o repositório ppa do rvm, atualizar a lista de pacotes nos repositórios e instalar o rvm

```bash
sudo apt-add-repository -y ppa:rael-gc/rvm
sudo apt-get update
sudo apt-get install rvm
```

 Já o comando a baixo, vai inserir seu usuário ao grupo *rvm* do sistema:

```
sudo usermod -a -G rvm $USER
```

Aqui, só ficou visível o rvm depois que eu saí do box e refiz a conexão.

## Configuração inicial do mysql

​	O mysql vem cru e permite login apenas com o comando *sudo* e o rails não usa o sudo, então precisamos fazer o login no usuário root do mysql:

```
sudo mysql -u root -p
```

​	E assim estamos logado, precisamos fazer uma alteração apenas:

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'sua senha';
```

​	E em seguida rodar:

```
FLUSH PRIVILEGES;
```

​	E já poderá fazer o login usando senha sem usar comando sudo para abrir o cmd do mysql.

## Básico de PostgreSQL

​	Precisamos criar um usuário e um banco em um primeiro momento para usar o postgreSQL:

```
sudo -u postgress createuser -rds vagrant
createdb vagrant
```

​	E por fim acessar o PostgreSQL

```
psql
```

​	Uma observação importante é que após gerar o projeto novo com *rails new meu_projeto -d postgresql* é importante acessar o diretório do projeto com:

```
cd meu_projeto
```

​	E configurar o arquivo *database.yml* com o *username* e *password* do banco de dados que configuramos. Sendo assim pode abrir o projeto pelo vscode, sublime text ou outro e procurar o arquivo *database.yml* que fica dentro do diretório *config* dentro da pasta do projeto, encontrar as alinhas:

```
#username: testeapp-postgres
```

​	descomentar, e trocar *testeapp-postgres* pelo nome de usuário do postgres que escolhemos na criação do usuário junto da senha. E na linha:

```
#password:
```

​	Precisamos descomentar e colocar a senha que escolhemos, com base na configuração feita no curso, usamos o *username* como *vagrant* e o *password* também como *vagrant* então no arquivo ficará assim:

```
username: vagrant
password: vagrant
```

## Conceito Básicos sobre Ruby lang

* Para criar arquivos da linguagem ruby, é preciso criar arquivos do tipo rb:

  ```
  meu_arquivo.rb
  ```

* Para executar o arquivo, precisamos chamar o interpretador *ruby* e passar o arquivo como argumento pra ele:

  ```
  ruby meu_arquivo.rb
  ```



## Básico da Linguagem

**STDOUT** : Saída padrão de dados.

​	A saída padrão de dados em ruby, usamos o *puts* e ele fará a saída de dados no console:

```
puts "olá mundo"
```

**STDIN** : Entrada padrão de dados.

​	A entrada padrão de dados pelo console através do console é feita pelo *gets*:

```
nome_da_variavel = gets
```

## Limpando dados no STDIN

​	Quando usamos o *gets* para enviar dados ao *STDIN* do Ruby ele vem "sujo", caso você crie um programa para armazenar em uma variável o seu nome e no console você digitar por exemplo: *Jonas*. no *STDIN* será enviado *"Jonas\n"* e isso não é legal em muitos casos, para isso usamos o *chomp*:

```ruby
puts "Digite o seu nome:"
nome = gets.chomp
puts nome.inspect

***console***
    
STDOUT: Digite o seu nome:
STDIN: Jonas
STDOUT: "Jonas"
```

​	Quando mandamos o *puts* jogar no *STDOUT* alguma váriavel, ele naturalmente não informa o *\n* que representa a quebra de linha, algo que só é possível ver quando mandamos o *puts* enviar para o *STDOUT* a variável *"inspecionada"* definimos isso acrescentando *.inspect* ao lado da variável que o *puts* recebe como argumento para enviar ao *STDOUT*:

```ruby
***usando inspect***

puts "Digite o seu nome:"
nome = gets
puts nome.inspect

***console***
    
STDOUT: Digite o seu nome:
STDIN: Jonas
STDOUT: "Jonas\n"

***não usando inspect***

puts "Digite o seu nome:"
nome = gets
puts nome

***console***
    
STDOUT: Digite o seu nome:
STDIN: Jonas
STDOUT: Jonas
```

​	Mesmo o *puts* não mostrando corretamente o valor real da variável, conseguimos ver que há uma *"sujeirinha"* nele usando o *.inspect*.

## Consultando o tipo de dados

​	Podemos consultar em qual *classe de dados* uma determinada variável pertence, de modo simples, quando usamos o *puts* para enviar ao *STDOUT* o valor de uma variável, podemos estender a variável com *.class* assim vamos ter no *STDOUT* o tipo de dados referente a variável que usamos no *puts*:

```ruby
puts "olá mundo".class

output: string
```



## Tipos de Dados em Ruby

​	Ruby não possuí tipos primitivos como a Linguagem C, e sim classes que implementando o conceitos de tipos assim como na Linguagem Python, atualmente descobri 5 tipos básicos em Ruby, sendo eles *String*, *Integer*, *Float*, *TrueClass* e *FalseClass*.

**String**

​	São tipos de dados que refere-se à uma cadeia de caracteres, alfanuméricos, contendo letras, números e símbolos envolvidos por *aspas duplas* .

**Integer**

​	São tipos de dados numéricos inteiros usados para números pequenos e números grandes e assumiu como padrão a partir da versão 2.4 do Ruby, ainda não sei a quantidade total de dados capaz de serem armazenado sem variáveis deste tipo, mas tomando como referencia o Python e por ambos terem classes que implementem tipos, o tipo *Integer* provavelmente tem um tamanho dinâmico aumentando conforme o tamanho do dado armazenado na variável.

**Float**

​	São dados numéricos reais de ponto flutuantes.

**TrueClass**

​	É o tipo de dado referente ao tipo *Boolean* mas este tipo, *TrueClass* em Ruby representa uma variável que seu valor é *True*.

**FalseClass**

​	O mesmo que *TrueClass* mas neste caso a variável tem este tipo quando seu valor é *False*.

**Conversão de dados**

​	Podemos converter tipos de dados em Ruby informando para qual tipo de dados o valor da determinada variável será convertido.

​	Por exemplo, se quisermos converter uma variável do tipo *Float* para *String* que é muito comum, caso quisermos concatenar um valor de uma variável contendo um dado do tipo *Float* com uma *String*, saída padrão do *STOUT* precisamos primeiro converter para *String* para a concatenação poder acontecer sem erros:

```ruby
salario = 1212.98
funcionario = "Jonas"
puts "o funcionário #{funcionario} tem o salário de " + salario.to_i

STOUT: o funcionário Jonas tem o salário de 1212.98
```

​	As opções que encontrei através do *pry* foram eles:

**to_r**  - Rational

**to_f** - Float

**to_i** - Integer

**to_s** - String

**to_c** - Complex

## Condicionais

​	Podemos fazer uma verificação *Booleana* ou seja, uma verificação que nos retorna um resultado entre duas opções ***Verdadeiro*** ou ***Falso*** e caso verdadeiro, nosso programa faz algo, se não ele não faz, ou faz outra coisa:

**if**

```ruby
if 1 < 10
	puts "1 é menor do que 10"
end

STDOUT: 1 é menor do que 10
```

​	Podemos ver no trecho de código acima que a instrução com *puts* foi executada por que sim! 1 é de fato menor do que 10, e no caso de:

```ruby
if 1 > 10
	puts "1 é menor do que 10"
end

STDOUT:
```

​	Não teríamos nenhuma saída por que como 1 não é maior do que 10, o que esta entre *if* e *end* é ignorado.

​	Podemos também ao invés de não mostrar nada, fazer um alternativa que nos retorna algo caso seja falso, usando a clausula senão ou *else*:

```ruby
if 1 > 10
	puts "1 é maior do que 10"
else
	puts "1 é menor do que 10"
end

STDOUT: 1 é menor do que 10
```

​	como neste exemplo, ele ignora o *if* por que a essa análise resulta em *false* mas executa a instrução do *else* que é o que acontece caso o *if* falhe na análise  resultando em *false*.

**unless**

podemos fazer o exemplo anterior de outro modo, mais aconselhável, quando vamos usar o if, desejamos que a análise dê verdadeiro e caso dê falso, ele faça algo sobre isso, no caso do *unless* fazemos o contrário, verificamos se é falso primeiro e caso não, o resultado é verdadeiro:

```ruby
unless 1 > 10
	puts "1 é maior do que 10"
else
	puts "1 é menor do que 10"
end

STDOUT: 1 é maior do que 10
```

​	Neste estranho caso, ele jogou para o *STDOUT*  a confirmação de que 1 é maior do que 10 por que na verificação que acontece no *unless* ele vai executar o que esta entre *unless* e *else* caso a verificação seja falsa e só executará o que está entre *else* e *end* somente se a verificação for verdadeira em consequência da primeira verificação falhar.

**case**

​	No caso do case, usamos para analisar o valor de uma variável por exemplo, e com base no valor que esta variável armazenar, ele pode ter uma ação diferente, por exemplo um programa que pede um numero de 1 à 10 para te mostrar a tabuada do número insetido no *STDIN* do Ruby, podemos fazer isso usando 1 único case, neste caso não vou por o exemplo aqui por que ficaria grande mas podemos fazer isso de forma mais diminuda:

```ruby
numero = gets.chomp
case numero.to_i
when 1
	puts "tabuada do 1"
when 2
	puts "tabuada do 2"
when 3
	puts "tabuada do 3"
when 4
	puts "tabuada do 4"
else
	puts "valor informado é inválido"
	
STDIN: 2
STDOUT: tabuada do 2
    ------------
STDIN: 132
STDOUT: valor informado é inválido
```

​	Neste caso, o exemplo vai até 4 e imprime qual a tabuada escolhida, e quando informamos para o *INPUT* um valor diferente dos que estão sendo *filtrado* ele cai na clausula *else*, podemos usar isso para possivelmente tratar um erro e alertar o usuário que o erro está no dado enviado para o *STDIN* e não uma limitação do nosso programa.

​	Podemos também fazer um tipo de filtro aceitando de um valor a outro, definindo um valor mínimo e um máximo de forma bem simples:

```ruby
entrada = gets.chomp
case entrada
when 1 .. 17
	puts "você é menor de idade, não pode dirigir"
else
	puts "Você é maior de idade, pode dirigir"
end

STDIN: 16
STDOUT: você é menor de idade, não pode dirigir
```

**Ternário**

​	Também temos o operador ternário que podemos passar para uma variável o retorno de uma condição independente se for verdadeiro ou falso, para caso precisarmos de uma variável que por exemplo possa simular um interruptor de luz:

```ruby
botao_da_lampada = "aperdado"
lampada = botao_da_lampada == "apertado" ? "acesa" : "apagada"

STDOUT: acesa
```

​	Neste exemplo, a variável *lampada* vai receber um valor, "acesa" ou "apagada", entre o sinal de " = " e o sinal de " ? " ocorre uma análise igual a instrução *if*, se for verdadeiro, ele retorna o que está a direita do sinal de " ? " que no caso é *"acesa"* caso fosse falso, ele retornaria o que está a direita do sinal de " : " que neste caso é *"apagada"* ou seja, a direita do sinal de interrogação é o retorno de *verdadeiro* e ao lado do sinal de "dois pontos" é o retorno de *falso*.

​	Podemos também fazer a execução de uma função ou um puts por exemplo:

```ruby
sexo = "M"
sexo == "M" ? (puts "Masculino") : (puts "Feminino")

STDOUT: Masculino
```

**Operadores relacionais e aritméticos

**Relacionais**

**>** - maior que

**<** - menor que

**>=** - maior ou igual a

**<=** - menor ou igual a

**==** - igual a

**!=** - diferente de



**Aritméticos**

**soma** - +

**subtração** - -

**multiplicar** - *

**dividir** - /

**potência** - **

**módulo** - %



## Estruturas de Repetição - while

​	Executa loops *enquanto* uma condição for verdadeira:

```ruby
num = 0
while num < 10 do
puts "valor: " + num.to_s
num = num + 1
end

STDOUT: 0
STDOUT: 1
STDOUT: 2
STDOUT: 3
STDOUT: 4
STDOUT: 5
STDOUT: 6
STDOUT: 7
STDOUT: 8
STDOUT: 9
```

​	Não foi mostrado no *STDOUT* o digito 10 por que 10 não é menor do que 10, então ele para até o 9,  quando ele imprime o 9 em seguida o valor da variável *num* é acrescentado em mais 1 ficando com o valor de 10 que na iteração seguinte, ele analisa basicamente se 10 é menor do que 10.

## Estruturas de repetição - each

​	Com o *each* podemos percorrer uma lista de valores, onde cada item por vez é passado com valor para uma variável e podemos a cada iteração usar essa variável para alguma finalidade, no exemplo abaixo apenas fiz um exemplo de sequência simples para percorrer tudo e podemos ver como a variável tem seu valor alterado em cada iteração:

exemplo com array:

```ruby
[1,2,3,4,5].each do |item|
	puts "número sorteado foi (" + item.to_s + ")"
end

STDOUT: número sorteado foi (1)
STDOUT: número sorteado foi (2)
STDOUT: número sorteado foi (3)
STDOUT: número sorteado foi (4)
STDOUT: número sorteado foi (5)
```

exemplo com range de números:

```ruby
(1..5).each do |item|
	puts "número sorteado foi (" + item.to_s + ")"
end

STDOUT: número sorteado foi (1)
STDOUT: número sorteado foi (2)
STDOUT: número sorteado foi (3)
STDOUT: número sorteado foi (4)
STDOUT: número sorteado foi (5)
```

​	

​	Como podemos ver, o que está dentro de "|   |" representa uma variável que terá seu valor atualizado a cada iteração, e sem definir o nome da variável dentro de *pipes* não funcionará.

## Arrays

​	Para criar um *array* podemos faze de dois modos, com valores definidos na declaração:

```ruby
vetor = [123, 321, 42312, 54, 4325]
# ou caso queria declarar um array vazio usando está sintaxe
vetor = []
```

​	Ou podemos criar uma instância de *array* e acrescentar posteriormente os valores usando a cláusula *push*:

```ruby
vetor = Array.new
vetor.push(12)
vetor.push(342)
```

​	Os *arrays* em ruby são heterogêneos dando a possibilidade de em um mesmo array conter dados do tipo *integer* e *string*:

```ruby
vetor = []
vetor.push(10)
vetor.push("vezes")
vetor.each do |item|
	puts item
end

STDOUT: [10, "vezes"]
```

## Array aninhados

​	Podemos fazer um array de arrays, onde cada item do arrays, pode conter um array:

```ruby
array_aninhado = [[1,2],[3,4],[5,6]]
```

​	E podemos fazer uma iteração com eles usando o *each*:

```ruby
array_aninhado.each do |item|
	item.each do |item_interno|
		puts item_interno
	end
end
```

​	E a saída seria:

```ruby
STDOUT: 1
STDOUT: 2
STDOUT: 3
STDOUT: 4
STDOUT: 5
STDOUT: 6
STDOUT: [[1, 2], [3, 4], [5, 6]]
```



## Hashes

​	Uma estrutura de dados que podemos usar, e vamos, é o *hash* que segue o modelo de chave => valor, e é o que será usado para tratar com estruturas de dados, onde seria "nome" => "fabio" ou também "nome": "fabio", essa segunda forma foi implementado na versão 1.9 para se assemelhar com formatação *json*:

formatação modelo padrão:

```ruby
aluno = {"nome" => "fabio", "media_final" => 10}
```



formatação modelo json pós versão 1.8:

```ruby
aluno = {"nome": "fabio", "media_final": 10}
```



​	Para percorrer com *each* podemos fazer praticamente do mesmo modo como no array, a diferença é que agora, ai invés de percorrer um valor por vez, percorremos dois sendo eles o valor de chave e o valor do valor desta chave:

```ruby
aluno.each do |chave,valor|
	puts "#{chave} ->  #{valor.to_s}"
end
```

​	E teremos a seguinte saída:

```ruby
STDOUT: nome ->  fabio
STDOUT: media_final ->  10
```

## Concatenação

​	Em Ruby temos duas formas de criar o efeito de concatenação ( juntar duas ou mais strings) e podemos usar dois o peradores, o de *soma* e o de *pá* (não sei se tem um nome melhor para eles).

​	A diferença entre eles é o seguinte, o modo a baixo, cria uma instancia de cópia da variável, contendo o valor da variável *original* concatenada ao valor passado para a *concatenação*:

```ruby
nome = "fabio"
ultimo = "carneiro"
nome = nome + " " + ultimo
puts nome
STDOUT: fabio carneiro
```

​	Neste exemplo, a primeira váriavel *nome* é diferente da segunda váriavel *nome* e não é pelo fato dos valores terem sido atualizados, por exemplo, quando temos uma váriavel na linguagem C ela representa um valor presente em um determinado endereço de memória, e essa variável sempre será a mesma variável, tendo um valor alterado ou não, por que no caso de uma variável do tipo *int* ela terá alocado 4bytes na memória, e esse endereço é fixo até que desalocamos essa variável. No caso do Ruby, é alocado uma cópia dessa variável *nome* uma vez que concatenamos com o sinal de **+** :

```ruby
nome = "fabio"
ultimo = "carnerio"
puts nome.object_id
puts nome
STDOUT: 47055091736100
STDOUT: "fabio"

----------------------
nome = nome + " " + ultimo
puts nome.object_id
puts nome
STDOUT: 47055093143660
STDOUT: "fabio carneiro"
```



​	Com o uso do *.object_id* podemos ver o id que foi atribuído para este objeto, lembre-se: "não existe tipos primitivos em Ruby, tudo são pseudo-tipos implementados por classe, logo tudo é objeto em Ruby assim como em Python". Assim, podemos ver que ambas variáveis tem o identificador de *"nome"* mas valores *diferentes* e *ID* diferente depois da concatenação com o sinal **+**.

​	No caso do operador *Pá* ocorre algo diferente, ele pega a variável que já possuí o valor que queremos, e introduz dentro dela, o que queremos concatenar com o valor ali existente:

```ruby
nome = "fabio"
puts nome
puts nome.object_id
STDOUT: "fabio carneiro"
STDOUT: 47055093215500

------------------------
ultimo = "carneiro"
nome << " " << ultimo
puts nome
puts nome.object_id
STDOUT: "fabio carneiro"
STDOUT: 47055093215500
```

​	Neste caso, além de não termos que repetir o nome da variável como no caso do operador **+** e nem usar o sinal de atribuição **= ** :

```ruby
 nome = nome + " " + ultimo
```

​	precisando apenas acrescentar o que falta para a variável:

```
 nome << " " << ultimo
```

​	Vemos também que o *ID* da variável se manteve o mesmo!

## Formatação no gets

​	Para formatar a saída do *gets* para o *STDOUT* podemos aplicar o efeito de *interpolação*, podendo fazer diretamente no *gets* ou fora suando **#{}** por, mas isso só funcionará **com strings envolvidas por aspas duplas** exemplo:

```ruby
nome = "fabio"
data_atual = 2022
idade = 32
frase_formatada = "#{nome} tem #{idade} anos e o ano atual é #{data_atual} portando #{nome} nasceu no ano de #{data_atual - idade}"
puts frase_formatada
STDOUT: "fabio tem 32 anos e o ano atual é 2022 portanto fabio nasceu no ano de 1990"
```

​	Podemos formatar diretamente no *puts*:

```ruby
nome = "fabio"
data_atual = 2022
idade = 32
puts "#{nome} tem #{idade} anos e o ano atual é #{data_atual} portando #{nome} nasceu no ano de #{data_atual - idade}"
STDOUT: "fabio tem 32 anos e o ano atual é 2022 portanto fabio nasceu no ano de 1990"
```

## Symbols

​	*Symbols* são utilizados para definir um tipo de *constante* onde seu número de identificador será constante, impedindo que para cada uso seja gerado uma nova alocação na memória.

​	Usamos isso para definir chaves definitivas com id constantes, no caso de *hash*, se fossemos usar o modo clássico de hash usariámos:

```ruby
meu_hash = {:nome => "Jonas"}
```

​	assim *:nome* terá um *id* fixo, porém se usarmos o modo moderno de declarar um hash:

```ruby
meu_hash = {nome: "Jonas"}
```

​	O *Symbol* é aplicado de forma implícita, neste caso, seria mais indicado usar a anotação moderna em projetos futuros que terão uso com *json*.

## Variáveis X Constantes

​	Variáveis sabemos que é um objeto com valor mutável, variável, e a constante é um objeto de valor constante sem redefinição!

​	Variáveis em ruby é mais comum escrevermos o padrão snake_case:

```ruby
meu_nome_esta_nesta_variavel = "fabio carneiro"
```

​	Ou se preferir podemos usar camelCase

```ruby
meuNomeEstaNestaVariavel = "fabio Carneiro"
```

​	No caso de constantes no Ruby, por boas práticas, usamos a inicial em maiúscula ou ela toda em maiúscula:

```ruby
PI = 3.141597
```

```ruby
Pi = 3.141597
```

​	Mas caso tente dar um novo valor para esta variável, ela irá receber o novo valor, você receberá um aviso de que ela foi inicializada como constante, e avisará que seu valor foi alterado, mas ainda assim mudará, talvez não seja uma constante tão constante assim.

```ruby
[1] pry(main)> Nome = "fabio"
=> "fabio"
[2] pry(main)> puts Nome
fabio
=> nil
[3] pry(main)> Nome = "Fabio Carneiro"
(pry):3: warning: already initialized constant Nome
(pry):1: warning: previous definition of Nome was here
=> "Fabio Carneiro"
[4] pry(main)> puts Nome
Fabio Carneiro
=> nil
```



## Classe VS Objeto

​	Em Ruby tudo é objeto, quando precisamos criar uma classe, criamos um tipo de protótipo ou uma "forma" que usamos para criar *objetos* a partir desta *forma*.

​	Quando vamos criar uma nova classe, precisamos seguir algumas regras de boas práticas como por exemplo usar nomenclatura seguindo o padrão *PascalCase* onde todas palavras presentes no nome devem iniciar por maiúscula, ou se preferir, caixa-alta. A sintaxe mais básica seria:

```ruby
class ClassePessoa
end	
```

​	E caso desejarmos criar uma instância desta classe usando o método *.new* 

*Obs. mesmo não tendo os parênteses, como por exemplo .new() ainda assim é um método por que na linguagem Ruby não é obrigatório o uso de parênteses*

Instância sem parênteses:

```ruby
p1 = ClassePessoa.new
```

Instância com parênteses:

```ruby
p1 = ClassePessoa.new()
```

​	Em casos como na Linguagem Java, somos obrigados a definir uma classe no arquivo com o mesmo nome do arquivo ex: 

```java
ClasseJava.java
```

 	No arquivo:

```java
public class ClasseJava {

}
```

## Métodos de classes

​	Em ruby, não precisamos declarar qual será o retorno do método, precisamos apenas estar cientes de que a ultima expressão em ruby terá seu retorno implícito, por exemplo como no trecho de código abaixo.

```ruby
def falar
    "olá, como vai você?"
end
```

​	Neste exemplo, está claro que o retorno será a frase da String declarada dentro do método *falar*, então não há a necessidade de colocar:

```ruby
puts "olá, como vai você?"
```

​	Porque como disse antes, a ultima instrução dentro do *escopo* do método é implícito. Se fossemos criar a classe com este método então ficaria assim:

```ruby
class ClassePessoa
    def falar
        "olá, como vai você?"
    end
end
```

​	E como ficaria para criar um objeto desta classe *ClassePessoa* e fazer uso deste método? Assim:

```ruby
p1 = ClassePessoa.new
puts p1.falar

STDOUT: olá, como vai você?
```

​	Como nosso método *falar* retorna uma *String* para que possamos mostrar esse dado no STDOUT precisamos usar o *puts*

## Parâmetros para os métodos

​	Podemos passar parâmetros para os métodos da linguagem Ruby, de forma convencional à outras linguagens:

```ruby
class Pessoa
    def cumprimentar (convidado)
        "olá, como vai você, #{convidado} ?"
    end
end

p1 = Pessoa.new
puts p1.cumprimentar("Jonas")

STDOUT: olá, como vai você, Jonas ?
```

​	Neste caso, quando vamos usar o método *cumprimentar* somos obrigados a passar argumentos conforme é pedido pelo método, caso seja do desejo do desenvolvedor, podemos criar um argumento *default* ou seja, que já tem um valor definido na declaração do método, caso não seja passado nenhum valor, ele irá usar o *default*:

```ruby
class Pessoa
    def cumprimentar (convidado = "meu amigo")
        "olá, como vai você, #{convidado} ?"
    end
end

p1 = Pessoa.new
puts p1.cumprimentar("Jonas")
puts p1.cumprimentar

STDOUT: olá, como vai você, Jonas ?
STDOUT: olá, como vai você, meu amigo ?
```

## Método *initialize*

​	Temos um método que é usado como um meio de algo acontecer quando é criada uma instância de uma classe, similar ao método construtor:

```ruby
class Pessoa
    def initialize
        puts "por favor entre!"
    end
    def cumprimentar (convidado = "meu amigo")
        "olá, como vai você, #{convidado} ?"
    end
end

p1 = Pessoa.new
puts p1.cumprimentar("Jonas")

STDOUT: por favor entre!
STDOUT: olá, como vai você, Jonas ?
```

## self

​	Quando precisado fazer uma referência ao objeto, como quando precisamos acessar um atributo, ou algo específico do objeto:

```ruby
class Objeto
    def initialize
        puts self.object_id
    end    
end

nvObjeto = Objeto.new

STDOUT: 47176307614260
```

​	Neste exemplo, podemos pegar o id do objeto *nvObjeto* dentro do método *initialize* com o uso do atributo de classe *self* 

​	Podemos também fazer algo curioso, perigo e interessante, que é *reabrir uma classe*. imagine o seguinte, você deseja acessar uma classe já criada e mudar algo nela ? Um exemplo que posso pensar agora é quando estamos por exemplo usando uma linguagem como a Linguagem C++, nela apesar do seu poder absurdo, ainda pode acontecer de precisarmos implementar na mão alguma coisa que precisamos se não houver pronto, em uma situação onde precisamos de um método específico, teríamos que criar isso em algum lugar, em um arquivo externo ou algo assim, talvez até recriar uma classe inteira só para implementar aquilo que queríamos, e as vezes o trabalho seria muito maior. Em Ruby podemos simplesmente reabrir uma classe e mudar o que precisamos, simples e fácil mas muito perigoso quando não sabe o que está fazendo:

```ruby
class String
    def inverter
        self.reverse
    end
end

puts "Fabio Carneiro".inverter

STDOUT: orienraC oibaF
```

​	O curioso disso é que eu inicialmente parecia estar criando uma nova classe chamada *String* mas esta já existe e ela é a responsável por implementar o tipo String na Linguagem Ruby, e podemos observar que mesmo não instanciando esta classe, apenas passamos para o *puts* uma string usando o método que criamos e na saída do console a nossa string foi invertida, é o mesmo efeito do que fazer:

```ruby
puts "Fabio Carneiro".reverse

STDOUT: orienraC oibaF
```

​	Agora, apesar de isso parecer algo muito legal, na mão de pessoas más, seria perigoso né ? Grandes poderes, nos trazem grandes reponsábilidades.

## Variáveis de Instância

​	Variáveis de instância são variáveis comuns que só existem uma vez que a classe foi instanciada e gerou um novo objeto a partir da instância, e uma vez que o objeto foi instanciado com valores de variáveis de instância definidos no momento da instância, estes valores só existem *dentro* dessa mesma instância, caso declare um novo objeto, a partir da mesma classe, os valores de cada objeto são *diferentes*:

```ruby
class Pessoa
    def initialize(nv_nome = "Paulo", nv_profissao = "Professor")
        @nome = nv_nome
        @profissao = nv_profissao
    end

    def resumo_pessoa
        "Nome: #{@nome}\nProfissão: #{@profissao}"
    end
end

p1 = Pessoa.new("Fabio", "Engenheiro de Software")
p2 = Pessoa.new("Jonas", "Engenheiro Mecânico")
p3 = Pessoa.new

puts p1.resumo_pessoa
puts p2.resumo_pessoa
puts p3.resumo_pessoa

OUTPUT: Nome: Fabio
 	    Profissão: Engenheiro de Software
OUTPUT: Nome: Jonas
        Profissão: Engenheiro Mecânico
OUTPUT: Nome: Paulo
        Profissão: Professor
```

​	Neste exemplo, para que possamos criar um objeto instanciando a classe sem passar parâmetro algum, precisamos usar valores default no método *initilialize* e os objetos p1 e p2 foram criados com valores passados na declaração de novas instâncias, e no caso do objeto p3 ele pegou os valores default, mesmo se não declarar os valores default, o p3 não teria acesso aos valores dos outros objetos mesmo pertencendo a mesma classe que originou eles, uma vez que a instância faz uma cópia usando como referência a classe, essa cópia é alocada na me memória e cada nova instância, uma nova parte da memória é alocada, criando assim um novo *objeto*, entende-se objeto como algo criado a partir de uma predefinição de algo, e predefinição de um objeto, é a classe propriamente dita:

```ruby
class Pessoa
    def initialize(nv_nome = "Paulo", nv_profissao = "Professor")
        @nome = nv_nome
        @profissao = nv_profissao
    end

    def resumo_pessoa
        "Nome: #{@nome}\nProfissão: #{@profissao}"
    end
end

p1 = Pessoa.new("Fabio", "Engenheiro de Software")
p2 = Pessoa.new("Jonas", "Engenheiro Mecânico")
p3 = Pessoa.new

puts "ID do p1: " << p1.object_id.to_s
puts "ID do p2: " << p2.object_id.to_s
puts "ID do p3: " << p3.object_id.to_s

STDOUT: ID do p1: 47375271786260
STDOUT: ID do p2: 47375271786200
STDOUT: ID do p3: 47375271786180
```

​	E a prova real que temos, é no exemplo acima mostrando que cada objeto tem um *ID* diferente.

## Acessors

​	Os acessors servem como um atalho para declaração de atributos de uma classe, podemos fazer uma declaração simples e teremos a possibilidade de ter um *Método Getter e Setter* de forma bem simples.

​	O *Método Setter* é um método que nos permite acessar um atributo de um objeto, e *setar* ou *alterar* o valor deste atributo, ele estando vazio ou não.

​	O *Método Getter* é um método que nos permite acessar o valor que existe em um atributo de um objeto, neste, usamos em caso de consulta dos valores já definidos nos atributos do objeto.

```ruby
class Pessoa
    attr_accessor :nome,:profissao
end

p1 = Pessoa.new
p1.nome = "Fabio Carneiro"
p1.profissao = "Engenheiro de Software"

p2 = Pessoa.new
p2.nome = "Jonas"
p2.profissao = "Engenheiro Mecânico"

puts p1.nome << " " << p1.profissao
puts p2.nome << " " << p2.profissao

STDOUT: Fabio Carneiro Engenheiro de Software
STDOUT: Jonas Engenheiro Mecânico
```

## Heranças

​	O conceito de herança é simples de entender  e explicar, mas acontece muitas confusões quando a forma de explicar não é bem didática, mas uma explicação simples seria dizer que uma classe, é uma base para criar objetos, a classe que diz como o objeto será, se ele terá métodos, atributos, e quando falamos em objetos na programação, podemos literalmente comparar com objeto, se você pegar um cubo dado de madeira, você pode descrever ele de forma simples, os atributos podemos usar as características:

cor: branco

lados: 6

valores: [1, 2, 3, 4, 5, 6]

peso: 5.8 (gramas)

material: madeira

fabricante: brinkadados

​	E caso fossemos representar um método que ele tenha poderíamos usar como exemplo:

método: rolar_dado()

​	E este método seria o que usássemos para sortear um valor dentre os disponíveis em seus atributos, isso é um objeto no mundo real e podemos representar ele na programação, uma vez que criamos uma classe, criamos uma base que podemos usar para criar objetos a partir dessa base, podendo estar sem definição os atributos que citamos, mas já definidos os métodos que ele tem, que seria ligado as funcionalidades que este objeto possuí.

​	Quando falamos de *Herança* falamos do conceito de extensão que é o efeito de estender algo, então assim podemos criar uma classe com atributos e métodos, e uma segunda classe com outros atributos e outros métodos, e podemos fazer que essa segunda classe herde da primeira classe citada, o efeito disso, é que na segunda classe, declaramos o que é específico dela, e o que é da primeira classe será herdada para a segunda, ou se preferir, a segunda classe vai estender a primeira classe, por exemplo:

​	Uma pessoa,  tem atributos, por exemplo um nome  e uma idade, podemos definir uma classe Pessoa que tenha esses atributos, podemos também ter uma classe mulher, que herda a classe pessoa. Na classe mulher teremos o atributo, nome ela por herdar a classe pessoa, ela terá os atributos nome, e idade, podemos criar uma nova classe chamada professor, que herda de pessoa e esta classe pode ter os atributos referentes a qual área de ensino este professor atua, e por herdar pessoa, terá um nome e uma idade, ou seja, uma pessoa, não necessariamente é uma mulher, essa pessoa pode ser um homem, uma mulher não necessariamente é uma professora, mas com certeza é uma pessoa, assim como um professor/educador pode não ser uma mulher, mas com certeza é uma pessoa também, então este é o conceito mais básico de heranças entre classes.

Exemplo:

```ruby
class Pessoa
    attr_accessor :nome, :idade, :sexo

    def falar
        puts "oi"
    end
end

class Professor < Pessoa
    attr_accessor :materia, :nivel_ensino

    def ensinar
        puts "ensinando"
    end
end

class Aluno < Pessoa
    attr_accessor :escola, :media

    def aprender
        puts "aprendendo"
    end
end

aluna1 = Aluno.new
aluna1.nome = "Mariana"
aluna1.idade = 16
aluna1.sexo = "F"
aluna1.escola = "Escola Maria Teresa Fonseca"
aluna1.media = 8.7

puts aluna1.nome << ", " << aluna1.idade.to_s << " anos do sexo " << aluna1.sexo << " tem média " << aluna1.media.to_s << " na escola " << aluna1.escola
aluna1.aprender

prof1 = Professor.new
prof1.nome = "Paulo"
prof1.idade = 53
prof1.sexo = "M"
prof1.materia = "História"
prof1.nivel_ensino = "Médio"

puts prof1.nome << ", " << prof1.idade.to_s << " ano do sexo " << prof1.sexo << " atua no ensino " << prof1.nivel_ensino << " lecionando " << prof1.materia
prof1.ensinar

pessoa1 = Pessoa.new
pessoa1.nome = "Pedro"
pessoa1.idade = 38
pessoa1.sexo = "M"

puts pessoa1.nome << " é do sexo " << pessoa1.sexo << " e tem " << pessoa1.idade.to_s << " anos de idade."
```

Neste código teremos a seguinte saída:

```ruby
STDOUT: Mariana, 16 anos do sexo F tem média 8.7 na escola Escola Maria Teresa Fonseca
aprendendo
       
STDOUT: Paulo, 53 ano do sexo M atua no ensino Médio lecionando História
ensinando
    
STDOUT: Pedro é do sexo M e tem 38 anos de idade.
```

​	Como podemos ver, Mariana, é aluna e tem uma escola em que está matriculada e tem uma média, isso por que ela é uma *Aluna* mas ainda assim, possuí um nome, uma idade e um sexo por que ela é uma *Pessoa* e também é uma *Aluna* e por ser uma aluna ela herda atributos de uma *Pessoa*.

​	Paulo é um Professor do Ensino Médio, ele leciona História e tem 53 anos, por que ele é uma *Pessoa* e também um *Professor* logo ele herda os atributos de uma *Pessoa* como *nome, sexo e idade* e estende os atributos de *Pessoa* com os atributos *nível de ensino que atua e qual matéria leciona* por que ele estende os atributos de *Pessoa* por que ele é também é um *Professor*.

​	E no caso do Pedro, mesmo não sendo um *Aluno* de algum curso ou período letivo, nem ser um *Professor* ele ainda assim é uma *Pessoa* e tem direito de ter *nome, sexo e idade* e essa é a explicação mais simples e objetiva que eu pude pensar.

## Métodos de Classe

​	Uma vez que instanciamos uma classe e assim criamos um objeto, podemos acessar os métodos deste objeto, nos referimos à estes métodos como sendo *métodos de instância* por que para usarmos estes métodos, precisamos primeiro instanciar a classe e assim podemos fazer uso dos métodos. Em Java por exemplo chamaríamos *Métodos de Classe* como sendo *Métodos Estáticos* por que eles são vistos mesmo não instanciando a classe ao qual o método que queremos pertence, por exemplo, o *puts* é um *Método de Classe* que não precisamos instanciar nada, apenas chamamos ele, e inclusive podemos fazer assim:

```ruby
puts("olá mundo!")
```

​	Deste modo fica mais nítido que é um método recebendo um parâmetro que ele vai usar para mostrar no STDOUT do console o que escrevemos, podemos recriar este método usando o conceito de *Método de Classe*:

```ruby
class Terminal
    def self.Write(msg = "Mensagem Vazia")
        puts msg
    end
end

Terminal.Write("Olá Mundo!")

OUTPUT: Olá Mundo!
```

​	Por mais que nossa classe ainda usa o puts, perceba que não precisamos instanciar a classe *Terminal* para usar o método *Write* apenas usamos o conceito de *denotação ponto* ou seja, declaramos que queremos usar o método *Write* da classe *Terminal* e como isso funciona? Lembra da palavra reservada *self* que usamos para mencionar diretamente a classe a qual ele pertence ? Então, quando declaramos o médodo *Write* como sendo *"sel.Write"* estamos declarando que a classe em si, será responsável por fazer uso do método e não o objeto instanciado da classe, desse modo não precisa instanciar o objeto, por que a partir do momento que colocamos essa declaração:

```ruby
Terminal.Write("Olá Mundo!")
```

​	Estamos fazendo algo como: *"Hey classe Terminal use o método Write, para escrever isso: 'Olá Mundo!' "*

## Módulos e Mixins

​	Módulos Ruby são parecidos com as classes, eles tem métodos, constantes e definições de módulos e classes. Porém, módulos não são instanciados e assim não é possível criar um objeto a partir de um módulo, dentro de um módulo não podemos herdar um outro módulo nem herdar uma classe, quando precisamos criar um módulo que faça uso de algo pertencente a outro módulo ou classe, precisamos especificar qual funcionalidade de qual módulo queremos usar incluindo ele no módulo, e não fazemos herança, e sim inclusão de módulos.

​	Quando precisamos de *constantes*, é em módulos que vamos declarar elas deixando tudo devidamente organizado. Podemos também comparar os módulos como os *namespaces* de outras linguagens como por exemplo C++ ou C#, deste moto podemos ter dois métodos puts, mas cada qual pertencentes a um *namespace* assim evitaremos conflitos de nomes.

​	Quando falamos que podemos criar um módulo, incluir nele outros módulos e classes, estamos nos referindo ao conceito de **mixins** que vem de **mix** e **in** algo como *misture isso ou misture dentro* se referindo claro ao módulo.

​	Os módulos vão ficar em arquivos externos facilitando a organização do nosso projeto, e nos permitindo acessar esses arquivos externos (*módulos*) e fazer uso dos recursos que ele tem a nos oferecer, e podemos fazer de duas formas, que se assemelham ao *namespace* do c++ por exemplo.

​	No C++ quando vamos criar uma variável do tipo String, precisamos dizer qual é o *módulo* que implementa este tipo, e precisamos verbosamente declarar:

```c++
std::string nome = "fabio carneiro";
```

​	Caso não queira ter que escrever std::string toda vez, podemos fazer uma *"inclusão de namespace"* é como se estivéssemos incluindo na memória o namespace de um modo que não precisamos repetir ele, isso em alguns casos por ocorrer problemas, afinal se você não diz de onde o método que está usando vem, como que o compilador irá saber ? Mas de todo modo faríamos isso assim:

```c++
using namespace std;
```

​	E na hora de declarar uma variável do tipo string ficaria assim:

```c++
string nome = "fabio carneiro";
```

​	Ok, mas estamos falando de Ruby, por que citar C++ ? por que muito provavelmente, é mais fácil você conhecer C++ do que ruby, afinal está lendo isso justamente para aprender ruby, e referencias para facilitar o aprendizado é a alma do négocio.

​	Em Ruby vou exemplificar usando dois arquivos um para o módulo e outro para buscar e usar este módulo, vamos criar o arquivo *constante.rb* que vai armazenar a constante de PI  e outro arquivo chamado de *app.rb* que fará uso desta constante.

​	constante.rb

```ruby
module Constante
    PI = 3.141597
end
```

​	Perceba que declaramos um modulo chamado *Constante* e todos módulos seguirão o padrão *CamelCase* e dentro dele definimos a constante seguindo o mesmo padrão.

app.rb

```ruby
require_relative "constante"
puts Constante::PI
```

​	Na primeira linha o método *require_relative* vai fazer o *requerimento relativo* do arquivo, neste caso usamos o nome do arquivo como parâmetro sem extensão, e obviamente devem estar no mesmo diretório. Caso o arquivo do módulo desejado estivesse dentro de uma pasta chamada *modulo_interno* e dentro desta pasta tivéssemos um arquivo chamado *meuNome.rb* com uma constante *Nome* onde eu armazenaria meu nome dentro de um módulo chamado *MeuNome*, isso ficaria assim:

app.rb

```ruby
require_relative "./modulo_interno/meuNome"
puts MeuNome::Nome
```

​	E caso eu não queria ter que escrever o nome do módulo, que podemos nos referir a ele como sendo um *namespace* como no c++ podemos usar a clausula *include*:

```ruby
require_relative "constante"
include Constante
puts PI
```

​	E também pode ocorrer os problemas que citei qual ao uso disso na linguagem C++ que mencionei.

​	Em caso de precisar declarar métodos dentro dos módulos, a regra é a mesma quando vamos criar métodos de classe, precisamos criar o método de modo que a clausula *self* antecede o nome do método:

pagamento.rb

```ruby
module Pagamento
    def self.pagar(valor_do_boleto, quantia_paga)
        if quantia_paga > valor_do_boleto
           puts "Você pagou o boleto na quantia de #{valor_do_boleto} reais\ncom a quantia paga de #{quantia_paga} reais.\nSeu troco é de #{quantia_paga - valor_do_boleto} reais."
        else
           puts "A quantia paga é insuficiente para efetuar o pagamento, ainda falta #{valor_do_boleto - quantia_paga} reais."
        end
    end
end
```

​	

app.rb

```ruby
require_relative 'pagamento'
 
Pagamento::pagar(10, 14)

OUTPUT: Você pagou o boleto na quantia de 10 reais
        com a quantia paga de 14 reais.
        Seu troco é de 4 reais.
```

## Classe dentro de Módulo

​	Podemos declarar classes dentro do módulo da mesma forma que criamos fora, e a forma de acessar essa classe é quase a mesma que acessar uma classe fora de módulo, a diferença é que o nome do módulo antecede o nome da classe.

marte.rb

```ruby
module Marte
    class Marciano
        def criar()
            puts "Marciano Criado"
        end
    end
end
```

app.rb

```ruby
require_relative 'marte'
 
marciano1 = Marte::Marciano.new
marciano1.criar

STDOUT: Marciano Criado
```

podemos também fazer assim:

marte.rb

```ruby
module Marte
    class Marciano
        def self.criar()
            puts "Marciano Criado"
        end
    end
end
```

app.rb

```ruby
require_relative 'marte'
 
Marte::Marciano.criar

STDOUT: Marciano Criado
```

Neste segundo caso, não precisamos nem instanciar a classe, ficando apenas necessário usar o padrão: *modulo::classe::metodo*

## Módulo em Módulo

​	Podemos fazer a mesma coisa que fizemos com classe mas usando módulos e não há muita explicação para acrescentar neste caso, se fossemos replicar o ultimo exemplo usando um módulo ao invés de uma classe ficaria assim:

marte.rb

```ruby
module Marte
    module Marciano
        def self.criar()
            puts "Marciano Criado"
        end
    end
end
```

app.rb

```ruby
require_relative 'marte'
 
Marte::Marciano.criar

STDOUT: Marciano Criado
```

​	Neste caso só precisamos mudar a clausula *class* para *module*.

## Básico sobre GEMS

​	Gems é quase um conceito, podemos dizer que gem é o gerenciador de dependências do ruby assim como o npm é do note, pip é do python e assim por diante, as coisas fundamentais que você precisa saber é que, para desenvolver algo em ruby, e acrescentar poder no seu desenvolvimento, precisamos instalar *"gemas"* e quem vai nos dar o poder de fazer isso é o gem que vem naturalmente quando o ruby é instalado, pois bem, as opções fundamentais são:

| Comandos                             | O que faz                                                    |
| ------------------------------------ | ------------------------------------------------------------ |
| gem list                             | lista gemas instaladas localmente                            |
| gem list nome_da_gema                | pesquisa mais precisa com base no nome_da_gema nas gemas instaladas localmente |
| gem list nome_da_gema --remote       | o mesmo que o anterior mas faz a pesquisa remotamente        |
| gem list nome_da_gema --remote --all | o mesmo que o anterior mas faz para todas as versões de gemas ( velhas e atual ) |
| gem uninstall nome_da_gema           | remove a gema específica                                     |
| gem install nome_da_gema             | instala a gema específica                                    |
| gem install nome_da_gema -v X.X.X    | instala uma gema específica em uma versão específica         |
| gem cleanup                          | remove versões antigas das gemas                             |
| gem cleanup nome_da_gema             | remove versões antigas da gema específica                    |
| gem cleanup -d                       | verifica versões que serão apagadas                          |

## Importando a gema

​	Uma vez que fizemos a instalação da gema desejada, para podermos incluir ela no nosso projeto, podemos simplesmente usar a clausula *require*:

```ruby
require "minhaGema"
```

## Bundler

​	As gemas muito comumente tem dependências, muitas vezes uma gema depende de outra, e as vezes em uma versão específica, e muitas vezes isso causa um problema que foi apelidado de *dependecy hell* e o bundler feio para salvar nossa pele:

```
gem install bundler
```

​	Um vez com ele instalado, podemos criar arquivos chamados de *Gemfile* que fará o trabalho de organizar essas dependências nos livrando do *dependecy hell*.

exemplo de Gemfile usando a gema cpf_utils

```ruby
source "https://rubygems.org"   <--- onde está a gema
gem "cpf_utils"   <--- nome da gema
```

​	Dentro do diretório onde está o arquivo Gemfile criado só precisamos rodar:

```
bundle install
```

​	E assim tudo o que estiver no nosso Gemfile será instalado, podendo então evitar problemas de ter que ficar instalando tudo na mão. Como no nosso exemplo não foi passado nenhuma versão específica, será geral um aquivo chamado Gemfile.lock especificando versão mais atual da dependência, link da gema entre outras coisas e ele irá se basear na versão mais recente uma vez que não informamos qual versão queremos. E por fim, uma vez tendo o arquivo Gemfile no diretório do nosso projeto, rodado o comando *bundle* e instalado tudo, basta no nosso projeto usar a gema:

```ruby
require "cpf_utils"
```

## Versões

​	Uma coisa que é pouco procurado é um sentido par as versões nos programas e itens correlacionados, nas gemas é comum termos o nome da gema seguida de 3 números separados por pontos, eles separam as vesões *major, minor e patch*.

## Major

​	O primeiro número representa uma atualização grande que pode sofrer grandes impactos nas versões anteriores deste mesmo pacotes.

## Minor

​	O segundo número da versão são referente a mudanças menores sem grande impactos ou nenhum.

## Patch

​	O último número é referente a correções, não tendo tanto impacto e se refere quando alguma correção foi feita, para saber exatamente quais foram, só com a leitura do log pela equipe mantenedora.

​	No caso do Gemfile se eu fosse especificar qual versão eu desejo que o Bundle instale para usar no projeto eu faria assim:

```ruby
source "https://rubygems.org"   <--- onde está a gema
gem "cpf_utils", "1.3.5"  <--- nome da gema e versão
```

​	Se eu quiser que ele instale uma versão igual ou maior que determinada versão, faria assim:

```ruby
source "https://rubygems.org"   <--- onde está a gema
gem "cpf_utils", ">=1.3.5"  <--- nome da gema e versão
```

​	Se eu quiser manter uma margem de versões, de uma determinada versão sem que mude a *Major* e fique mudando entre as versões de *Minor*  eu vou usar o seguinte:

```ruby
source "https://rubygems.org"   <--- onde está a gema
gem "cpf_utils", "~>1.3"  <--- nome da gema e versão
```

​	Neste ultimo exemplo, o bundle vai instalar qualquer versão começando pela *1.3* mas nunca acima da *1.X* podendo nas verões de *Minor* mudarem tranquilamente mas a versão *Major* jamais irá mudar.

​	No caso de permitir que somente sofra atualizações nas versões de *Patch* podemos fazer assim:

```ruby
source "https://rubygems.org"   <--- onde está a gema
gem "cpf_utils", "~>1.3.5"  <--- nome da gema e versão
```

​	Neste ultimo exemplo, o bundle vai instalar qualquer versão começando pela *1.3.5* mas nunca acima da *1.3.X* podendo nas verões de *Patch* mudarem tranquilamente mas a versão *Major* e na *Minor* jamais irão mudar.

## Conclusão

​	Este documento não vai te preparar para o mercado de trabalho mas com certeza com tudo compartilhado aqui você vai ter o que precisa para caminhar com suas próprias pernas nessa jornada de estudos da Linguagem Ruby!!!

