---
layout: post
title: Symfony 2 Object Entity Custom Choice Selected Value Sorunu
description: "Symfony 2 Object Field Entity Custom Choice'un seçilen verisini alma sorunu."
modified: 2015-09-25
tags: [symfony 2, symfony 2 entity object custom choice, symfony custom choice]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Object türü Entity ile birlikte Choice formu kullanırken selected olan veriyi Form otomatik olarak işlemiyor. Bu sorunun çözümü işin aslında bir hayli kolay ancak konu ile ilgili bir kaynak bulmak imkansız.

{% highlight php %}
$choice1 = new Choice();
$choice1->setName('Yok');
$choice1->setValue(0);

$choice2 = new Choice();
$choice2->setName('Var');
$choice2->setValue(1);

$resolver->setDefaults(array(
            'choices' =>  array(
                $choice1,
                $choice2
            ),
            'choices_as_values' => true,
            'choice_label' => function($choice, $key, $index) {
                /** @var Choice $choice */
                return $choice->getName();
            },
            'choice_value' => '__value__',
        ));
{% endhighlight php %} 

Sorunu çözümü choice_value kısımında. Bu ayara '__value__' değerini verdiğinizde Data Class'ınızın "getValue" methodundan dönen değeri alıyor.