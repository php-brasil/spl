# PHP SPL - A biblioteca padrão do PHP

Durante uma discussão no PHP Brasil, descobrimos que há uma baixa aceitação da SPL por conta de desconhecimento de sua própria existência. A proposta da série *PHP SPL - A biblioteca padrão do PHP* é justamente procurar resolver essa questão, apresentando cada um dos participantes da SPL, mostrando o que são, para que servem e como utilizá-los.

## Interfaces SplObserver e SplSubject

Além de clases, a SPL oferece algumas interfaces pra você mesmo implementar o comportamento.
Duas delas são a SplObserver e SplSubject que andam justas pra implentação do **Design Pattern Observer**.

## O Design Pattern Observer

O padrão consiste em manter uma lista de assinantes/observadores (Observer) e notifica-los sobre algum assunto/mudança de estado (Subject).
Um exemplo é o Twitter, os followers são os assinantes que observam por novos Tweets que são os assuntos.

## Exemplo

```php
<?php

class Twitter implements \SplObserver
{
    public $name;
    public $timeline;
    public $followers;

    public function __construct($name)
    {
        $this->name = $name;
        $this->timeline = [];
        $this->followers = [];
    }

    public function update(\SplSubject $subject)
    {
        $this->timeline[] = $subject;
    }

    public function tweet(Tweet $tweet)
    {
        foreach ($this->followers as $twitter) {
            $tweet->attach($twitter);
        }

        $tweet->author = $this;
        $tweet->notify();
    }

    public function follow(Twitter $twitter)
    {
        $twitter->followers[] = $this;
    }
}

class Tweet implements \SplSubject
{
    public $author;
    public $content;

    private $observers = [];

    public function __construct($content = null)
    {
        $this->content = $content;
    }

    public function attach(\SplObserver $observer)
    {
        $this->observers[] = $observer;
    }

    public function detach(\SplObserver $observer)
    {
        if ($key = array_search($observer, $this->observers, true)) {
            unset($this->observers[$key]);
        }
    }

    public function notify()
    {
        foreach ($this->observers as $twitter) {
            $twitter->update($this);
        }
    }
}

$php  = new Twitter('@php_net');
$zend = new Twitter('@zend');
$me   = new Twitter('@leocavalcante');

$me->follow($php);
$me->follow($zend);

$php->tweet(new Tweet('PHP 7.0.0 Beta 1 released'));
$zend->tweet(new Tweet('Turbocharging the Web with PHP 7'));

foreach ($me->timeline as $tweet) {
    echo $tweet->author->name, PHP_EOL, $tweet->content, PHP_EOL;
}
```

Saída

```
@php_net
PHP 7.0.0 Beta 1 released.
@zend
Turbocharging the Web with PHP 7
```