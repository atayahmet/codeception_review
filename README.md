Codeception
===================

Diğer bir adıyla Behavior Driven Development (BDD).

CodeCeption çatısı altında üç çeşit test yöntemi bulunmaktadır. Bunlar Acceptance Tests, Functional Tests ve Unit Tests yöntemleri.

Bi çok yönden ortak yönleri olduğu gibi her birini özel kılan bazı özellikleri mevcuttur.

Aşağıdaki başlıklarda bu farklılıkları inceleyebilirsiniz.


RUN TESTS
-------------

[Acceptance Tests](#acceptance-tests)

[- PhpBrowser (command line)](#phpbrowser)

[- WebDriver (Selenium)](#webdriver)

[Functional Tests](#functional-tests)

[Unit Tests](#unit-tests)

##Acceptance Tests
- Acceptance Test PhpBrowser ve WebDriver modülleriyle çalışır. 
- Diğer bir artısı projelerinizin hangi platform da geliştirildiğinin hiç bir öneminin olmamasıdır. 
- Performans olarak Functional testlerden biraz daha yavaş çalışır.

**Örnek senaryo:**
```php
<?php
$I = new AcceptanceTester($scenario);
$I->wantTo('sign in');
$I->amOnPage('/login');
$I->fillField('username', 'davert');
$I->fillField('password', 'qwerty');
$I->click('LOGIN');
$I->see('Welcome, Davert!');
?>
```

**Çalıştırılması:**
```sh
$ php codecept.phar run --steps
```

> **Örnek çıktı:**
> 
> I WANT TO SIGN IN

> I am on page **'/login'**

> I fill field **'username'**, **'davert'**

> I fill field **'password'**, **'qwerty'**

> I click **'LOGIN'**

> I see **'Welcome, Davert!'**



Yukarıdaki senaryoyu inceleyelim:

- İlk olarak **wantTo()** methodu ile senaryo adını belirledik.

- **amOnPage()** methodu ile test edilecek url'ye gittik

- Sayfadaki giriş formunu **fillField()** metodu ile doldurduk.

- Değeri **LOGIN** olan butona tıkladık.

- Sonuç olarak tanımladığımız değeri **see()** metodu ile kontrol ettik

Bütün bu işlemler arka tarafta Codeception tarafından yapıldı bize sadece test sonuçları döndü.

Acceptance testlerinde karşılaştırma metodları **see** önceki ile başlar. Bu ek senaryonun sonuna geldiğini ve ekrana sonucu döndüreceği anlamına gelmektedir.

**Buna örnek metotlar:**

```php
$I->see('Logout');
```
Sonuç çıktısında **Logout** kelimesini arar, bulursa test geçer aksi takdirde başarısız olur.

```php
$I->seeCheckboxIsChecked('#agree');
```
ID'si **#agree** olan checkbox seçili ise test başarılı olacaktır.

```php
$I->seeCookie('PHPSESSID');
```
**PHPSESSID** adında bir cookie var test başarılı olacaktır.

##PhpBrowser

> **Özellikler:**

> - Codeception Modülüdür.
> - InnerBrowser Kütüphanesine extend olur.
> - GuzzleHttp\Client paketini kullanır.
> - Symfony BrowserKit paketini kullanır.
> - Php Curl Extension kütüphanesi aktif olmalıdır.
> - Javascript içeren testleri çalıştırmaz.
> - Başarısız olan testleri _output klasörü altında saklar.

Aşağıda PhpBrowser modülü için yapılandırma ayarlarını görüyorsunuz.

> **acceptance.suite.yml:**
> 
![enter image description here](https://lh6.googleusercontent.com/-qI-r8SAaico/VL5BI4qcqzI/AAAAAAAAAKE/-aCl3K9dOWs/s0/Screenshot+from+2015-01-20+10:44:06.png "Screenshot from 2015-01-20 10:44:06.png")

##WebDriver (Selenium)

PhpBrowser ile yazılan testler direkt WebDriver modülü altında Selenium ile çalıştırılabilir. Yalnız test suite ayarlarını WebDriver için yapılandırmamız gerekiyor.

![enter image description here](https://lh3.googleusercontent.com/-4cvKmAemO_M/VL5BxJewIkI/AAAAAAAAAKQ/hW44s83Qz1o/s0/Screenshot+from+2015-01-20+11:57:34.png "Screenshot from 2015-01-20 11:57:34.png")

####Özel Metotlar:
- Wait

WebDriver modülüne özgü metotlar yukarıda sıralanmıştır.

####**Wait**
Uygulamaları test ederken sayfa içinde çalışacak olan javascript işlemlerini beklememiz gerekebilir. Özellikle asenkron çalışan javascript işlemleri için bu metot oldukça işe yarar görünüyor.

**Örnek:**
```php
<?php
	$I->waitForElement('#agree_button', 30); // secs
	$I->click('#agree_button');
?>
```

Yukarıda id'si **#agree_button** olan link tıklandığın da 30 saniye bekleyecektir. 

##Functional Tests

Functional testler framework'lere bağlanarak uygulamaları test olanağ sağlar.

Varsayılan olarak desteklenen frameworkler:

- Symfony2

- Laravel 4

- Yii2

- Zend Framework

- Phalcon1

Functional testler Acceptance tastlerle hemen hemen kullanımı aynıdır, aralarındaki tek fark functional testlerin bir web server'a ihtiyacı olmamasıdır. 

Bütün framework modülleri ve PhpBrowser modülü aynı methodları ve aynı engine kullanmaktadır.

> **Önemli:**
> Functional testler performans bakımından Acceptance testlerden daha üstün fakat daha az stabil çalışmaktadır.

####Headers, Cookies, Sessions

Functional testler da **headers** protokolleri, **sessions** ve **cookie** işlemlerini kullanabiliyoruz. Bunlar bize bir çok alanda kolaylık sağlayacaktır. 

Örneğin **header** protokollerinde belirlediğimiz sınırları aşan işlemlerde hata tetikleyebiliriz.

####Paylaşımlı Hafıza (Shared Memory)
PHP de tüm request'ler tek bir memory üzerinde çalıştırıldığından tüm testlerin aynı anda çalışması beraberinde memory sorunları getirebilir. Bunu aşmak için testleri mümkün olduğunca tek tek çalıştırabiliriz. 

CodeCeption bu tür sorunlar yaşamamak için memory'i her zaman temiz tutmayı öneriyor bunu da **global** ve **static**  değişkenler kullanmamaya dayandırıyor.

####Framework Modüllerini kullanmak
Functional testler **tests/functional** dizini altında bulunmaktadır.
Bir framework modülünü kullanmak için **tests/functional.suite.yml** dosyasında gerekli yapılandırmayı yapmak gerekiyor.

Örnek yapılandırma:

**Symfony2:**

![enter image description here](https://lh3.googleusercontent.com/-bB_kT2YdIDA/VL9uRLq3PDI/AAAAAAAAALA/rukWrLxcbYQ/s0/Screenshot+from+2015-01-21+11:14:19.png "Screenshot from 2015-01-21 11:14:19.png")

Modüller içerisinden framework'lerin **global** değişkenlerine yada bağımlılık yönetimi sınıfına **(Dependency Injection Container)** erişilebilir. Bunu kendi oluşturacağınız bir helper modülü ile yapabilirsiniz.

**Örnek:**
```php
class FunctionalHelper extends \Codeception\Module
{
    function doSomethingWithMyService()
    {
        $service = $this->getModule('Symfony2') // lookup for Symfony 2 module
            ->container // get current DI container
            ->get('my_service'); // access a service

        $service->doSomething();
    }
}
```

##Unit Tests

CodeCeption [PhpUnit](https://phpunit.de/) testlerini kendi içinde çalıştırmaktadır. Daha önce yazılmış unit testlerinizi CodeCeption içine eklenerek çalıştırılabilir. 

PhpUnit testlerinizi CodeCeption içinde yazdığınız da bir takım işlerinizi kolaylaştıracak özelliklerde sunmaktadır.

Ayrıca PhpUnit kurulumuna ihtiyaç olmadığını hatırlatalım.

Unit test ayar yapılandırması aşağıdaki gibidir:

![enter image description here](https://lh3.googleusercontent.com/-24ZlE477-QU/VL-hpPHT9-I/AAAAAAAAALU/O9okL_0OXmo/s0/Screenshot+from+2015-01-21+14:53:52.png "Screenshot from 2015-01-21 14:53:52.png")

Unit test kullanımının iki farklı yöntemi bulunuyor.

- Birincisi klasik diyebileceğimiz PhpUnit test sınıfları.

**Örnek:**

```sh
$ php codecept.phar generate:phpunit unit Example
```

**Çıktı:**

```php
class ExampleTest extends \PHPUnit_Framework_TestCase
{
    protected function setUp()
    {
    }

    protected function tearDown()
    {
    }
}
```

- ikincisi CodeCeption sınıflarına extend olarak oluşturulan unit testler.


**Örnek:**

```sh
$ php codecept.phar generate:test unit Example
```

**Çıktı:**

```php
class ExampleTest extends \Codeception\TestCase\Test
{
    /**
     * @var \UnitTester
     */
    protected $tester;

    protected function _before()
    {
    }

    protected function _after()
    {
    }
}
```

Oluşturulan unit testler **tests/unit** dizini altında bulunmaktadır. Yukarıdaki her iki örnektede oluşturulan test dosyalarının adı **ExampleTest** olacaktır.

