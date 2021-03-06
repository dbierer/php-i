# ZFF-I NOTES

Q: Is there a diagram for ZF workflow?
A: ???
Please add code for module specific listener

## ZF Reference Manual
* https://framework.zend.com/learn

## ERRATA

### Slides
* http://localhost:9090/#/2/5: s/be ´event´
* http://localhost:9090/#/3/22: s/be onlinemarket.work
* http://localhost:9090/#/3/19: there is no folder "Model"
* http://localhost:9090/#/4/10: s/be `<module_name>/src/Module.php` as per convention in composer.json.  Here is an example:
```
"autoload": {
    "psr-4": {
        "Application\\": "module/Application/src/",
        "Guestbook\\": "module/Guestbook/src/",
        "Login\\": "module/Login/src/",
        "Events\\": "module/Events/src/"
    }
},
```
* http://localhost:9090/#/4/27: s/be ´SOME VALUE´
* http://localhost:9090/#/4/44: s/be "escape" not "excape"
* http://localhost:9090/#/5/10: need to add `CreateHttpNotFoundModel` to list of plugins
* http://localhost:9090/#/5/11: delete this slide: AcceptableViewModelSelector is covered in ZFF-II
* http://localhost:9090/#/5/??: RE: flashMessenger plugin: https://docs.zendframework.com/zend-mvc-plugin-flashmessenger/
* http://localhost:9090/#/5/33: already discussed this!
* http://localhost:9090/#/5/41: drop template_map == too confusing
* http://localhost:9090/#/5/22: suggest moving discussion of `url()` plugin before discussion of `redirect()` plugin as concepts are similar, but `url()` plugin is easier to understand
* http://localhost:9090/#/6/16 - 23: initializers, abstract factories and delegators are covered extensively in the ZFF-II
* http://localhost:9090/#/7/16: 'default' does not work for `setTemplate()`
* http://localhost:9090/#/5/??: location of factory should be `Market\src\Controller\Factory\ViewControllerFactory.php` ("\Factory\" path was omitted)
* http://localhost:9090/#/8/26: this view template doesn´t work!
* http://localhost:8080/#/10/26: links to docs should go to ref manual, not github!!!
* http://localhost:8080/#/10/29: need to add `use Zend\Db\Sql\Like;` for this example to work!
* http://localhost:8080/#/10/37: this example has things which are not used!
* http://localhost:8080/#/11/29: class name s/be `Module` not Moduleb
* http://localhost:8080/#/12/11: don´t need leading ":" for hostname route

### VM Source
* `Market\Form\PostFilter` uses deprecated methods including `setAllowEmpty` and `addByName`

## Class Notes
* If you want to deliver JSON from a controller action:
  * Add the ZF component: `composer require zendframework/zend-json` 
  * In the `module.config.php` under `view_manager => 'strategies' => ['ViewJsonStrategy'}`
  * Controller returns `\Zend\View\Model\JsonModel` with the data supplied as constructor argument
  * Don´t forget the ¨use¨ statement as well!!!

## LABS
### Important Note

You need to disable Zend Server OpCache and PageCache ... otherwise changes you make might not be recognized right away as the opcode or output has been cached
* From browser: `http://localhost:10081`
* Administration
* Components
* Checkmark `Zend OPcache` and `Zend Page Cache`
* Click on the `Disable` button at the top
* Restart Zend Server

### First Lab:
* Need to add onlinemarket.work apache vhost link
* Do not do steps 7 and 8
* Need to add a step assigning rights to `www-data`
* Need to add entry for onlinemarket.work to `/etc/hosts`
  
### Module Lab:
* make sure you update security rights for new module 
```
cd /home/vagrant/Zend/workspaces/DefaultWorkspace/onlinemarket.work
sudo chown -R module/Market
```
* path for controller s/be: `onlinemarket.work\module\Market\src\Controller\IndexController.php`
* s/be `Market\config\module.config.php`

* If you get this message:
```
Fatal error: Uncaught Zend\ModuleManager\Exception\RuntimeException: Module (NAME) could not be initialized
```
* Make sure you do the following:
	* there MUST be a file `/modules/NAME/src/Module.php` with a class `Module` using the module namespace
	* add the module to the array in /config/modules.config.php
	* add an entry in composer.json for autoloading
	* run `composer update` or `composer dump-autoload`

### Plugins Lab
* Directory s/be: `Market\src\Controller\Plugin` not `plugin`
* To use the `flashMessenger()` plugin see: https://docs.zendframework.com/zend-mvc-plugin-flashmessenger/
  * Need to add `zendframework\zend-mvc-plugin-flashmessenger` to the `composer.json` file
  * Need to add `Zend\Mvc\Plugin\FlashMessenger` to `/config/modules.config.php`
  * Refresh the composer autoloader

### Controllers and Factories Lab
* Steps 8 + 17: I would drop the `template_map` in favor of `template_path_stack`
* Steps 9 + 18: too early to discuss child_routes: I would recommend just single level routes at this stage
* Location of the ViewControllerFactory should be `Market\src\Controller\Factory\ViewControllerFactory.php`

### Forms / Filters / Validators Lab
* Form + Filter in one class: https://github.com/dbierer/zf3-examples/blob/master/guestbook/module/Login/src/Form/Login.php

## Q & A
* Q: Do you need git installed to use composer?
* A: If the repository from which Composer draws is based on github.com or bitbucket.org you do not have to install git.  Otherwise, if the packages uses something else (e.g. svn), you need to have that client installed.

* Q: Is there a list of Module::get*Config() methods?
* A: Yes: look here https://docs.zendframework.com/zend-modulemanager/module-manager/ about the middle of the page

* Q: Is there a tool to generate a template map?
* A: /path/to/vendor/bin/templatemap_generator.php /path/to/module/MODULE_NAME

## Wed 16 Aug 2017

### Example of setting alternate template:
```
<?php
/**
 * @link      http://github.com/zendframework/ZendSkeletonApplication for the canonical source repository
 * @copyright Copyright (c) 2005-2016 Zend Technologies USA Inc. (http://www.zend.com)
 * @license   http://framework.zend.com/license/new-bsd New BSD License
 */

namespace Application\Controller;

use Zend\Mvc\Controller\AbstractActionController;
use Zend\View\Model\ViewModel;

class IndexController extends AbstractActionController
{
    public function indexAction()
    {
        return new ViewModel();
    }
    public function testAction()
    {
        $viewModel = new ViewModel(['controller' => __CLASS__]);
        $viewModel->setTemplate('application/index/index');
        return $viewModel;
    }
}
```


### examples of nested views
https://gist.github.com/anonymous/6563adab8e0cc1fb51dd92346a697018

```
<?php
namespace Market\Controller;

use Zend\Mvc\Controller\AbstractActionController;
use Zend\View\Model\ {ViewModel, JsonModel};

class IndexController extends AbstractActionController
{
    protected $someService;
    protected $categories;
    public function indexAction()
    {
        $this->layout('market/layout');
        return new ViewModel(['someService' => $this->getSomeService(),
                              'categories' => $this->getCategories()
        ]);
    }
    public function requestAction()
    {
        $viewModel = new ViewModel(['request' => $this->getRequest()]);
        $viewModel->setTerminal(TRUE);
        return $viewModel;
    }
    public function paramsAction()
    {
        $status[] = $this->params()->fromQuery('status1', 'Unknown');
        $status[] = $this->getRequest()->getQuery('status2', 'Unknown');
        $viewModel = new ViewModel(['status' => $status]);
        $childView = new ViewModel(['controller' => __CLASS__, 'action' => __FUNCTION__, 'this' => 'that']);
        $childView->setTemplate('alt/child');
        $otherView = clone $childView;
        $viewModel->addChild($childView, 'child');
        $viewModel->addChild($otherView, 'otherChild');
        return $viewModel;
    }
    public function stopAction()
    {
        $response = $this->getResponse();
        $response->setContent('<h1>No Problem ... Be Happy!</h1>');
        return $response;
    }
    public function jsonAction()
    {
        $test = array();
        $test[] = range('A', 'F');
        $test[] = range(1,6);
        $test[] = range('a', 'f');
        return new JsonModel(['data' => $test]);
    }
    /**
     * @return the $someService
     */
    public function getSomeService()
    {
        return $this->someService;
    }

    /**
     * @param field_type $someService
     */
    public function setSomeService($someService)
    {
        $this->someService = $someService;
    }
    /**
     * @return the $categories
     */
    public function getCategories()
    {
        return $this->categories;
    }

    /**
     * @param field_type $categories
     */
    public function setCategories($categories)
    {
        $this->categories = $categories;
    }
}

// Market/view/alt/child.phtml
<h2>Child</h2>
Controller: <?php echo $this->controller; ?>
Action:  <?php echo $this->action; ?>

// Market/view/market/index/params.phtml
<div class="col-lg-4">
	<pre><?= var_export($this->status, TRUE); ?></pre>
</div>
<div class="col-lg-4">
	<?php echo $this->child; ?>
</div>
<div class="col-lg-4">
	<?php echo $this->otherChild; ?>
</div>
```

## Mon 28 Aug

```
<?php
// module/Model/src/Module.php
namespace Model;

use Zend\Db\Adapter\Adapter;
class Module
{
    public function getServiceConfig()
    {
        return [
            'services' => [
                'model-primary-adapter-config' => [
                    'driver' => 'PDO',
                    'dsn' => 'mysql:hostname=localhost;dbname=onlinemarket',
                    'username' => 'zend',
                    'password' => 'password',
                ],
            ],
            'factories' => [
                'model-primary-adapter' => function ($container, $requestedName) {
                    return new Adapter($container->get('model-primary-adapter-config'));
                },
            ],
        ];        
    }
}
```

```
<?php
// /config/modules.config.php
/**
 * @link      http://github.com/zendframework/ZendSkeletonApplication for the canonical source repository
 * @copyright Copyright (c) 2005-2016 Zend Technologies USA Inc. (http://www.zend.com)
 * @license   http://framework.zend.com/license/new-bsd New BSD License
 */

/**
 * List of enabled modules for this application.
 *
 * This should be an array of module namespaces used in the application.
 */
return [
    'Zend\Router',
    'Zend\Validator',
    'Market',
    'Application',
    'Model',
];
```

```
<!-- market/view/index.phtml -->
<div class="col-lg-2">
	<?php echo $this->htmllist($this->categories); ?>
</div>

<div class="col-lg-10">
    <pre>
    <?php var_dump($this->someService); ?>
    <?php echo get_class($this); ?>
    <?php foreach ($this->result as $row) var_dump($row); ?>
    </pre>
</div>
```

```
<?php
namespace Market\Controller;

use Zend\Mvc\Controller\AbstractActionController;
use Zend\View\Model\ {ViewModel, JsonModel};

class IndexController extends AbstractActionController
{
    protected $someService;
    protected $categories;
    protected $adapter;
    public function indexAction()
    {
        $result = $this->adapter->query('SELECT * FROM listings', []);
        return new ViewModel(['someService' => $this->getSomeService(),
                              'categories' => $this->getCategories(),
                              'result' => $result
        ]);
    }
}
```

```
<?php
namespace Market\Controller\Factory;
use Interop\Container\ContainerInterface;
use Zend\ServiceManager\Factory\FactoryInterface;
class IndexControllerFactory implements FactoryInterface 
{
    public function __invoke(ContainerInterface $container, $requestedName, array $options = null) {
        $controller = new $requestedName();
        $controller->setSomeService($container->get('some-service'));
        $controller->setCategories($container->get('categories'));
        $controller->setAdapter($container->get('model-primary-adapter'));
        return $controller;
    }
}
```

```
// composer.json file
	"autoload" : {
		"psr-4" : {
			"Application\\" : "module/Application/src/",
			"Market\\" : "module/Market/src/",
			"Model\\" : "module/Model/src/"
		}
	},
```

## 30 Aug 2017

### Zend\\Db
* Example of `Zend\\Db\\Sql` with join: https://github.com/dbierer/zf3-examples/blob/master/guestbook/module/Events/src/TableModule/Model/RegistrationTable.php
* Q: Any examples of using the profiler?
* A: ???

### Zend\\ModuleManager
* Ties `module.config.php` keys to `get*Config()` methods in `Module` class:
  * `Zend\\Mvc\\Service\\ModuleManagerFactory`
* Documentation on keys vs. get*Config() methods: https://docs.zendframework.com/zend-modulemanager/module-manager/

### Creating a Module Specific Listener:
```
<?php
// Module.php
namespace Market;

use Zend\Mvc\MvcEvent;

class Module
{
    public function getConfig()
    {
        return include __DIR__ . '/../config/module.config.php';
    }
    public function getServiceConfig()
    {
        return [
            'services' => [
                'some-service' => [2 => __FILE__],
            ]
        ];
    }
    public function onBootstrap(MvcEvent $e) {
        $em = $e->getApplication()->getEventManager();
        $em->attach(MvcEvent::EVENT_DISPATCH, array($this, 'myCall'));
    }
    
    public function myCall(MvcEvent $e) {
        $module = $e->getRouteMatch()->getParam('module');
        if ($module == __NAMESPACE__) {
            echo '<br>This is the Market Module';
        }
    }
}

// module.config.php
<?php
/**
 * @link      http://github.com/zendframework/ZendSkeletonApplication for the canonical source repository
 * @copyright Copyright (c) 2005-2016 Zend Technologies USA Inc. (http://www.zend.com)
 * @license   http://framework.zend.com/license/new-bsd New BSD License
 */

namespace Application;

use Zend\Router\Http\Literal;
use Zend\Router\Http\Segment;
use Zend\ServiceManager\Factory\InvokableFactory;

return [
    'router' => [
        'routes' => [
            'home' => [
                'type' => Literal::class,
                'options' => [
                    'route'    => '/',
                    'defaults' => [
                        'controller' => Controller\IndexController::class,
                        'action'     => 'index',
                        'module'     => __NAMESPACE__,
                    ],
                ],
            ],
            'application' => [
                'type'    => Segment::class,
                'options' => [
                    'route'    => '/application[/:action]',
                    'defaults' => [
                        'controller' => Controller\IndexController::class,
                        'action'     => 'index',
                        'module'     => __NAMESPACE__,
                    ],
                ],
            ],
        ],
    ],
    'service_manager' => [
        'services' => [
            'some-service' => [3 => __FILE__],
        ]
    ],
    'controllers' => [
        'factories' => [
            Controller\IndexController::class => InvokableFactory::class,
        ],
    ],
    'view_manager' => [
        'display_not_found_reason' => true,
        'display_exceptions'       => true,
        'doctype'                  => 'HTML5',
        'not_found_template'       => 'error/404',
        'exception_template'       => 'error/index',
        'template_map' => [
            'layout/layout'           => __DIR__ . '/../view/layout/layout.phtml',
            'application/index/index' => __DIR__ . '/../view/application/index/index.phtml',
            'error/404'               => __DIR__ . '/../view/error/404.phtml',
            'error/index'             => __DIR__ . '/../view/error/index.phtml',
        ],
        'template_path_stack' => [
            __DIR__ . '/../view',
        ],
    ],
];
```
