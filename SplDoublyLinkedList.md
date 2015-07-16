# PHP SPL - A biblioteca padrão do PHP

Durante uma discussão no PHP Brasil, descobrimos que há uma baixa aceitação da SPL por conta de desconhecimento de sua própria existência. A proposta da série *PHP SPL - A biblioteca padrão do PHP* é justamente procurar resolver essa questão, apresentando cada um dos participantes da SPL, mostrando o que são, para que servem e como utilizá-los.

## Listas ligadas e a SplDoublyLinkedList

Antes de abordar diretamente a SplDoublyLinkedList e mostrar como utilizá-la, precisamos compreender o que é, de fato, uma DoublyLinkedList ou lista duplamente ligada. Para isso, vamos começar descrevendo uma lista ligada simples antes de evoluir para a lista duplamente ligada.

## Lista ligada

Linguagens de programação possuem estruturas de dados. Uma lista ligada ou duplamente ligada nada mais é do que uma estrutura de dados. Utilizamos com bastante frequência no PHP uma estrutura de dados chamada array. Independentemente da forma como é implementado no PHP - em PHP o array associativo tem um comportamento de HashTable -, arrays são, normalmente, estruturas de dados contínuas, ou seja, elas são armazenadas em memória ou no disco item por item de forma sequêncial.

Por ser sequencial, essas estruturas de dados possuem um custo relativamente alto em operações de exclusão ou insersão que não sejam no fim do array. Basicamente, num array como é normalmente implementado, precisamos cortar o array na posição desejada, criando dois arrays, colocar o novo item na posição desejada e, então, mesclar novamente os dois arrays.

A grande vantagem de uma lista ligada, em comparação com o array, está justamente no baixo custo que ela promove para essas operações e para o armazenamento - seja em memória, seja em disco. E esse tipo de estrutura de dados pode ser implementada de forma extremamente simples. Tudo o que precisamos é de duas referências: uma para o dado armazenado, outra para o próximo item da lista. Por exemplo:

```php
<?php
printf("\n-----------[ Criação da lista ]------------\n\n");

$first = new stdClass();
$first->data = 1;

$second = new stdClass();
$second->data = 2;

$third = new stdClass();
$third->data = 3;

$first->next = $second;
$second->next = $third;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));

```

A saída será:

```
-----------[ Criação da lista ]------------

1
2
3
```

### Inserção e remoção de itens

Como pôde ser visto no exemplo anterior, implementar uma lista ligada e iterar por seus elementos é muito simples. Tão simples quanto implementar a lista, é adicionar um novo elemento no meio dela. Tudo o que precisamos é mudar a referência para o próximo item, no local onde queremos adicionar o novo item. Por exemplo:

```php
<?php
printf("\n-----------[ Criação da lista ]------------\n\n");

$first = new stdClass();
$first->data = 1;

$second = new stdClass();
$second->data = 2;

$third = new stdClass();
$third->data = 3;

$first->next = $second;
$second->next = $third;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));

printf("\n-----------[ Inserção no meio da lista ]------------\n\n");

$twonhalf = new stdClass();
$twonhalf->data = 2.5;

$second->next = $twonhalf;
$twonhalf->next = $third;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));
```

A saída será:

```
-----------[ Criação da lista ]------------

1
2
3

-----------[ Inserção no meio da lista ]------------

1
2
2.5
3
```

E para a remoção, basta mudar a referência do próximo item, no item anterior ao queremos remover da lista. Por exemplo:

```php
<?php
printf("\n-----------[ Criação da lista ]------------\n\n");

$first = new stdClass();
$first->data = 1;

$second = new stdClass();
$second->data = 2;

$third = new stdClass();
$third->data = 3;

$first->next = $second;
$second->next = $third;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));

printf("\n-----------[ Inserção no meio da lista ]------------\n\n");

$twonhalf = new stdClass();
$twonhalf->data = 2.5;

$second->next = $twonhalf;
$twonhalf->next = $third;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));

printf("\n-----------[ Remoção no meio da lista ]------------\n\n");

$first->next = $twonhalf;

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));
```

A saída será:

```
-----------[ Criação da lista ]------------

1
2
3

-----------[ Inserção no meio da lista ]------------

1
2
2.5
3

-----------[ Remoção no meio da lista ]------------

1
2.5
3
```

## Lista duplamente ligada

Compreendido o que é uma lista ligada, fica fácil, já que é um tanto óbvio, compreender o que é uma lista duplamente ligada. Ao contrário da lista ligada, que possui referência apenas para o próximo item, a lista duplamente ligada também possui referência para o item anterior.

E por que temos duas referências? Bom, existem diversos motivos, entre eles o menor custo em algumas operações, mas o motivo mais simples é que haverá situações onde precisaremos iterar os itens de forma invertida, indo do último item até o primeiro item. Por exemplo:

```php
<?php
$first = new stdClass();
$first->data = 1;

$second = new stdClass();
$second->previous = $first;
$second->data = 2;

$third = new stdClass();
$third->previous = $second;
$third->data = 3;

$first->next = $second;
$second->next = $third;

printf("\n-----------[ Iterando a lista do primeiro ao último ]------------\n\n");

$item = null;

do {
    $item = $item == null? $first: $item->next;

    printf("%s\n", $item->data);
} while (isset($item->next));

printf("\n-----------[ Iterando a lista do último ao primeiro ]------------\n\n");

$item = null;

do {
    $item = $item == null? $third: $item->previous;

    printf("%s\n", $item->data);
} while (isset($item->previous));
```

A saída será:

```
-----------[ Iterando a lista do primeiro ao último ]------------

1
2
3

-----------[ Iterando a lista do último ao primeiro ]------------

3
2
1
```

## SplDoublyLinkedList

O objetivo da `SplDoublyLinkedList` é, justamente, facilitar a criação, manutenção e operações relacionadas com listas duplamente ligadas. Ela oferece uma interface que permite que adicionemos itens no início da lista, no fim da lista ou num lugar específico da lista. Ainda permite que a iteração ocorra para frente ou para trás, que possamos remover itens do início

### FIFO - Criando uma fila com a SplDoublyLinkedList

Filas possuem um comportamento FIFO - First In First Out -, ou seja, o primeiro item a entrar na fila deve ser o primeiro item a sair da fila. Muito comum em operações coordenadas, filas de impressão, filas de atendimento em sistemas de help desk, etc.

Para esse tipo de implementação usamos o método `SplDoublyLinkedList::push`, que adicionará o novo elemento no final da fila e o método `SplDoublyLinkedList::shift`, que removerá e retornará o primeiro item da lista:

```php
<?php
$fifo = new SplDoublyLinkedList();
$fifo->push(1);
$fifo->push(2);
$fifo->push(3);

while ($fifo->count()) {
    printf("%s\n", $fifo->shift());
}
```

A saída será:

```
1
2
3
```

### FILO - Criando uma pilha com SplDoublyLinkedList

Pilhas possuem um comportamento FILO - First In Last Out -, ou seja, se estivermos empilhando pratos para lavar, o primeiro prato a ser lavado será o do topo da pilha, ou o último a ser colocado nela. Muito comum em linguagens de programação para organizar a pilha de chamadas de funções.

Para esse tipo de implementação usamos o método `SplDoublyLinkedList::push`, para adicionar um novo item ao final da pilha e o método `SplDoublyLinkedList::pop`, que removerá e retornará o último item da lista:

```php
<?php
$filo = new SplDoublyLinkedList();
$filo->push(1);
$filo->push(2);
$filo->push(3);

while ($filo->count()) {
    printf("%s\n", $fifo->pop());
}
```

A saída será:

```
3
2
1
```

### DEQUEUE - Criando uma fila com terminação dupla, duplamente terminada, etc.

Filas duplamente terminadas são aquelas onde podemos adicionar itens no fim da fila e no começo da fila. Normalmente utilizamos esse tipo de fila em alguma lista onde alguns itens possuem prioridades em relação aos outros. Por exemplo, numa fila de mercado, o primeiro a entrar na fila do caixa é o primeiro a ser atendido. Mas se um senhor de mais idade for ao caixa, então esse senhor deve ter prioridade em relação aos mais novos e pode entrar no começo da fila.

Para esse tipo de implementação usamos os métodos: `SplDoublyLinkedList::push`, para adicionar um novo item no fim da fila, o método `SplDoublyLinkedList::unshift`, para adicionar um novo item no começo da fila e o método `SplDoublyLinkedList::shift`, que removerá e retornará o primeiro item da lista:

```php
<?php
$deque = new SplDoublyLinkedList();
$deque->push('Garotinho');
$deque->push('Jovem de 20');
$deque->push('Jovem de 30');

// O senhor de 80 tem prioridade no atendimento
$deque->unshift('Senhor de 80');

while ($deque->count()) {
    printf("%s\n", $deque->shift());
}
```

A saída será:

```
Senhor de 80
Garotinho
Jovem de 20
Jovem de 30
```

### Outros métodos

Além dos métodos acima, que descrevem alguns comportamentos comuns, também temos outros métodos que permitem alguns comportamentos específicos, por exemplo:

#### Adicionando elementos em posições específicas da lista

```php
<?php
$list = new SplDoublyLinkedList();
$list->push(1);
$list->push(2);
$list->push(3);

// Adicionando o item 2.5 na posição 2 da lista
$list->add(2, 2.5);

foreach ($list as $item) {
    printf("%s\n", $item);
}
```

A saída será:

```
1
2
2.5
3
```

#### Variando a ordem do Iterator

```php
<?php
$list = new SplDoublyLinkedList();
$list->push(1);
$list->push(2);
$list->push(3);

printf("\nIterator como FIFO\n");

$list->setIteratorMode(SplDoublyLinkedList::IT_MODE_FIFO); //First in First Out

foreach ($list as $item) {
    printf("%s\n", $item);
}

printf("\nIterator como FILO\n");

$list->setIteratorMode(SplDoublyLinkedList::IT_MODE_LIFO); //Last in First Out

foreach ($list as $item) {
    printf("%s\n", $item);
}
```

A saída será:

```
Iterator como FIFO
1
2
3

Iterator como FILO
3
2
1
```

## Conclusão

Muitas vezes não usamos alguma coisa simplesmente porque não sabemos para que tal coisa serve. Não adiantaria se tivéssemos uma documentação detalhada da API da classe `SplDoublyLinkedList`, com a descrição de cada um de seus métodos, parâmetros e comportamentos, se não tivermos o conhecimento sobre para que, exatamente, aquela coisa serve. Essa é, justamente, a proposta da série *PHP SPL - A biblioteca padrão do PHP*: promover o conhecimento sobre a SPL através da descrição dos problemas que seus participantes pretendem resolver.