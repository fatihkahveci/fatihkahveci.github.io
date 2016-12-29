---
layout: post
title: Symfony ile Doctrine'de Random Entity
description: "Symfony 2 Object Field Entity Custom Choice'un seçilen verisini alma sorunu."
modified: 2016-12-29
tags: [symfony 2, symfony 2 doctrine rand, symfony random entity]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Symfony ile proje geliştirirken default ORM olarak Doctrine kullanıyoruz. Ancak Doctrine'de (native query yazmıyorsanız) random olarak entity çağırmanız mümkün olmuyor. Ancak bunun için ufak bi extension ekleyerek sorunu çözebiliyoruz.

{% highlight php %}
use Doctrine\ORM\Query\Lexer;
use Doctrine\ORM\Query\Parser;
use Doctrine\ORM\Query\SqlWalker;
use Doctrine\ORM\Query\AST\Functions\FunctionNode;

class RandFunction extends FunctionNode
{
    public function parse(Parser $parser)
    {
        $parser->match(Lexer::T_IDENTIFIER);
        $parser->match(Lexer::T_OPEN_PARENTHESIS);
        $parser->match(Lexer::T_CLOSE_PARENTHESIS);
    }

    public function getSql(SqlWalker $sqlWalker)
    {
        return 'RAND()';
    }
}
{% endhighlight php %} 

İlk etapta yukarıda ki RandFunction classını oluşturmanız ve bundan sonra config.yml dosyamıza bir ayar çekmemiz gerekiyor.

{% highlight yml %}
orm:
        dql:
                numeric_functions:
                        Rand: Friend\Bundle\CoreBundle\DQL\RandFunction
{% endhighlight yml %}

Bunu yapmamızın ardından artık RAND() functionu Doctrine ile beraber otomatik olarak yüklü gelecek.

{% highlight php %}
public function getRandomUser($limit)
{
    $qb = $this->getEntityManager()->createQueryBuilder();

    $qb->addSelect('user');
    $qb->addSelect('RAND() as HIDDEN rand')->orderBy('rand');


    $qb->from('AcmeUserBundle:User', 'user');

    $qb->setMaxResults($limit);


    return $qb->getQuery()->getResult();
 }
{% endhighlight php %}

Ardından yukarıda ki örnekte olduğu gibi istediğiniz Entity'den random obje çekebilirsiniz.