## In Magento:
* **Code pools**:
  * **core**
    * Holds modules that are distributed with the base Magento and make up the core functionality
  * **community**
    * Holds modules that are developed by third-parties and installed by the users
  * **local**
    * Holds custom modules developed by your company, including Mage code overrides.
  * The priority to load the files from these pools is:
    * Local
    * Comunity
    * Core
* **Module Structure:**
  ```
    Namespace/
      └── Modulename
          ├── Block
          ├── Helper
          ├── Model
          ├── controllers
          ├── etc
          └── sql
  ```
  * **Bloks**
    * Cordinate models with the template files. The files in the folder load the data from the database
      and transfer it to the templates in your theme(.phtml files).
  * **Controllers**
    * Represent all business logic actions for the given request( dispatch(), preDispatch(), postDispatch() methods) and delegate commands to other parts of the system.
  * **etc**
    * It contains all xml files that declare and configure behavior of all modules. Each module must have at least config.xml and it's a right place to declare all models, routes, blocks, helpers etc.
    * Optionally this folder could have adminhtml.xml( grant permisions for your tabs/sections in backend menus) and system.xml( creates this new section or specifies where it should be diplayed in existinh one).
  * **Helper**
    * Contain utility methods, which are commonly used in the whole system. Methods, declared in helpers, can be called from any template file or block, model, controller, class by:
      ```
        Mage::Helper('modulename/helpername')->methodName();
      ```
  * **Model**
    * In clasical MVC, Models are used to connect to the database and process the data from and to it. Magento has a different approach, which can be triky at first look.
      Magento models can be categorized in one of two ways.
        * ActiveRecord-like/one-object-one-table Model
        * Entity Attribute Value(EAV) Model.
    ***
      Each of Model also gets a **Model Collection**, Collections are PHP objects used to hold a number of invidual magento Model
      Instances. The magento team has implemented the PHP Standard Lyrbrary Interfaces of Magento Instances of IteratorAggregate and Countable
      to allow each Model type to have it's own collection type
      Each model uses two ModelResource classes:
      ```
          * one to read
          * one to write
      ```
    ***


* **Magento Templates and layout files location**
  * A theme consists of the following elements
    * **Layout:** Folder includes XML files that define the layout of the theme. Layout files work as a "glue" between the modules
    (wich are found under the app/code directory), and the template files

    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── <b>layout</b>
      │   │   │       └── template
    ```
  Because of magent modularity, all XML data of the default theme are stored in separate files with the name of the module to which they belong to.
A non-default theme, contrariwise, should have a sigle layout file, named local.xml, where all layout updates are placed.

* **Template**
  * This folder cointains .phtml files that have HTML and PHP code for reach Magento Blocks which will be displayed in the frontend

    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── <b>template</b>
    ```
* **Locale**
  * This folder contains all .CSV files organized by language that provide translations in format of languagecode_COUNTRYCODE eg(es_MX, en_US)/translate.csv

    ```
      app/
      ├── design
      │   ├── frontend
      │   │   ├── package_name
      │   │   │   └── theme_name
      │   │   │       ├── etc
      │   │   │       ├── layout
      │   │   │       └── template
      │   │   │       └── <b>locale</b>
      │   │   │           └── es_MX
      │   │   │               └── translate.csv
    ```

* **Skin and javascript files location**
  * Skin folder includes javascript, CSS and image files tha  are used in .phtml files in template folder. e.g All the files in this folder vary from theme to theme.

    ```
      skin/
      ├── frontend
      │   ├── package_name
      │   │   └── theme_name
      │   │   |   ├── <b>js</b>
      │   │   |   ├── <b>css</b>
      │   │   |   ├── <b>images</b>
    ```
  * **JS** folder embodies **js** files, libraries and frameworks that are used via frontend and backend, I f you need to add a new Javascript/AJAX library on your module from local code pool
    requires special scripts, they should be placed here.


## Design Areas:
```
  All the frontend files are stored on these areas:
```

* **adminhtml**
  * Everithing that you will observe while working in the admin panel cabe be found here. In other words
  , If a module performs anything related to admin panel, all templates and layouts will be here.
* **frontend**
  * Everything that can be observed on your webstore by any customer is stored here.
* **install**
  * Files from this area will be displayed during the installation process.
* **sql**
  * Handles any custom database tables wich will be used by the module and process all upgrades to the extension.


* **Class naming conventions and their relationship with the autoloader**
  >Magento was developed based on the Zend Framework, so the rules of class naming in Magento were taken from the Zend Framework

  Magento standardizes class names depending on their location in the file system. Such standadization enables atomatic class loading(**autoloader**)
  instead of using require_once and include_once functions. Rather than the directory separator('/' - invalid character for class names), developers use the underscor
  character('_')

  For example, the Mage_Catalog_Model_Product class is located in the /app/code/core/Mage/Catalog/Model/Product.php category. Magento Autoloader replaces all underscore characters
('_') with the category separator('/') and looks for the file in one of the categories(/app/code/local/ can be disables in the app/etc/local.xml in the disable_local_modules tag).

  - /app/code/core
  - /app/code/community
  - /app/code/local
  - /lib/
> File search categories in Magento are defined in app/Mage.php
```php
  Mage::register('original_include_path', get_include_path());

  if (defined('COMPILER_INCLUDE_PATH')) {
      $appPath = COMPILER_INCLUDE_PATH;
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage_Core_functions.php";
      include_once "Varien_Autoload.php";
   } else {
      /**
       * Set include path
       */
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'local';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'community';
      $paths[] = BP . DS . 'app' . DS . 'code' . DS . 'core';
      $paths[] = BP . DS . 'lib';

      $appPath = implode(PS, $paths);
      set_include_path($appPath . PS . Mage::registry('original_include_path'));
      include_once "Mage/Core/functions.php";
      include_once "Varien/Autoload.php";
   }

  Varien_Autoload::register();
  ```
  After defining all include paths you see, at the very last line of above snippet, that
  Magento's autoloader ins initialized. This line triggers the underneath snippet, which registers
  the autoload() method of the Varient_Autoloader object as it's autoloader weapon of choice.
  So behind the scenes Magento will now run each classname through this method to get to it's related file.

* **Describe methods for resolving module conflicts.**
  * There are **3 levels** of module compatibility conflicts
    * Conflicts with configuration files
      * When thow modules use the same class dependence, future conflicts may appear in the program parts of modules(improver use of the class constructor). This group of problems
        is closel connected to our second group 'conflicts with software part'.
        eg:

        Namespace_OtherModulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_OtherModulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_OtherModulename>
              </modules>
          </config>
        ```
        Namespace_Modulename.xml

        ```xml
          <config>
              <modules>
                  <Namespace_Modulename>
                      <active>true</active>
                      <codePool>community</codePool>
              <depends><Mage_Something/></depends>
                  </Namespace_Modulename>
              </modules>
          </config>
        ```

        We can fix them by replacing with the next code:

        Namespace_OtherModulename.xml

          ```xml
            <config>
                <modules>
                    <Namespace_OtherModulename>
                        <active>true</active>
                        <codePool>community</codePool>
                <depends><Mage_Something/></depends>
                    </Namespace_OtherModulename>
                </modules>
            </config>
          ```
          Namespace_Modulename.xml

          ```xml
              <config>
                  <modules>
                      <Namespace_Modulename>
                          <active>true</active>
                          <codePool>community</codePool>
                  <depends><Namespace_OtherModulename/></depends>
                      </Namespace_Modulename>
                  </modules>
              </config>
          ```

    * Conflicts with the software part
      * The main reason for module conflicts could be in the use of dependencies and class rewriting. You should regard your module
        configuration file(/Namespace_Modulename/etc/config.xml), in which class rewriting with `<rewrite></rewrite>` tags can be used. Eg

        ```xml
            <customer>
              <rewrite>
                   <form_edit>Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
               </rewrite>
            </customer>
        ```
        Incorrect use of class methods is the main reason of conflicts origin. There are several ways to solve this problem, but I would like to highlight the very best
        and most correct way in my opinion
        - The elimination of the classes overriding. The idea of this method is similar to the previous method of module conflict resilving and offers setting
          Dependence of one class from another.

          `Namespace_Modulename/etc/config.xml`

          ```xml
            <customer>
              <rewrite>
                <form_edit>Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
              </rewrite>
            </customer>
          ```
          `Namespace_OtherModulename/etc/config.xml`

          ```xml
            <customer>
              <rewrite>
                <form_edit>Namespace_OtherModulename_Block_Rewrite_BlockClass</form_edit>
              </rewrite>
            </customer>
          ```
        - Solution:
        First remove the rewriting for the first module

        ```xml
          <customer>
            <form_edit> Namespace_Modulename_Block_Rewrite_BlockClass</form_edit>
          </customer>
        ```
        Second Use the first class inheritance from the second one:

        ```
          Class Namespace_Modulename_Block_Rewrite_BlockClass extends Namespace_OtherModulename_Block_Rewrite_BlockClass
        ```

    * Conflicts in a module display
      * The most frquent conflict appears while using several modules, problems with displaying module on the frontend.
      * Some reasons:
        * Blocks overrding in layout settings
        * Use different template for the same blocks
        * Removing blocks for another module to be embedded in.

* **Explain how Magento loads and manipulates configuration information**
  At very basic level, when you load up your store, the following happens:

  1. The basic configuration is initialized
  2. Module configuration is initialized

  * Each module on Magento must include a `config.xml` file located on
  `app/code/[codePool]/Namespace/Modulename/etc/config.xml`
  This file contains all basic module setting and `app/etc/modules/Namespace_Modulename.xml` contains
  information about code pool and extension activation flag.

  * Global settings for Magento instalation, inlcuding database connection data and admin panel addrress, are
  sorted in `app/etc/config.xml` and `app/etc/local.xml`.

  > Everything starts on the **index.php** file with the next lines:
  ```php
    /* Store or website code */
    $mageRunCode = isset($_SERVER['MAGE_RUN_CODE']) ? $_SERVER['MAGE_RUN_CODE'] : '';

    /* Run store or run website */
    $mageRunType = isset($_SERVER['MAGE_RUN_TYPE']) ? $_SERVER['MAGE_RUN_TYPE'] : 'store';

    Mage::run($mageRunCode, $mageRunType);
  ```
  If we trace the code starting from the inex.php we get the following outcome:

  ```php
  Index.php
    Mage::run()
      self::$_app = new Mage_Core_Model_App();
      self::$_app = run(...);
  ```
  The `Mage::run()` all wrappers for precessing configuration loading are located in `Mage_Core_Model_App` and refer
  to the `Mage_Core_Model_Config` methods. Calling `the Mage::app()`, we immediately call `Mage_Core_Model_Config::init()`, which contains
  the process of configuration loadig.

  Finally we come to `Magento_Core_Model_Config`, inherited from `Mage_Core_Model_Config_Base` and `Varien_Simplexml_Config`.

  So let's refer to:  `Mage_Core_Model_Config::init()` method:
  ```php
    public function init($options=array())
      {
          $this->setCacheChecksum(null);
          $this->_cacheLoadedSections = array();
          $this->setOptions($options);
          $this->loadBase();

          $cacheLoad = $this->loadModulesCache();
          if ($cacheLoad) {
              return $this;
           }
          $this->loadModules();
          $this->loadDb();
          $this->saveCache();
          return $this;
       }
  ```
  ``$this->loadBase();``

    From the beginning we define the absolute path to `app/etc` directory. Then we get a list of all
  .xml fules from this catalog, read their content and merge into a **Single simpleXmlElement Object**

  If local.xml file has been loaded( which generally means that Magento as already been installed). set flag
  `$this->_isLocalConfigLoaded = true;` It will be used later to initialize store and load module setup scripts.

  The next spe is loading of the most extensive configuration part - `Modules configuration.`

  **$this->loadModules();**

  ```php
    /**
         * Load modules configuration
         *
         * @return Mage_Core_Model_Config
         */
        public function loadModules()
        {
            Varien_Profiler::start('config/load-modules');
            $this->_loadDeclaredModules();

            $resourceConfig = sprintf('config.%s.xml', $this->_getResourceConnectionModel('core'));
            $this->loadModulesConfiguration(array('config.xml',$resourceConfig), $this);

            /**
             * Prevent local.xml directives overwriting
             */
            $mergeConfig = clone $this->_prototype;
            $this->_isLocalConfigLoaded = $mergeConfig->loadFile($this->getOptions()->getEtcDir().DS.'local.xml');
            if ($this->_isLocalConfigLoaded) {
                $this->extend($mergeConfig);
             }

            $this->applyExtends();
            Varien_Profiler::stop('config/load-modules');
            return $this;
         }
    ```
  **$this->_loadDeclaredModules();**
    Scan the `app/etc/modules` directory and collect the list of paths to all .xml files, Indicating all modules in the system.
    We form an associative array with "base" and "custom" keys. Only `Mage_All.xml` path goes to the "base" section. Modules from the
    Mageto basic pack located in `app/core/Mag` code pool "core" go to the “mage“ section “Custom“ section collects the rest of modules.
    At the end we merge everything into a single array. The data is stored in the following sequence: `Mage_All.xml`, modules with Mage namespace
    ,All other modules.

    > Mage_All.xml contains all information required for loading modules that are crucial for the proper system operation.

   All the nformation collected from the .xml files is loaded into `Mage_Core_Model_Config_Base $unsortedConfig` then we get the `$moduleDepends` array,
   based on < depends >, < active > flags and module name.
   At the end of `_loadDeclaredModules()` we create a **simpleXmlElement Object** again and merge it with the one that has been created earlier from `app/etc/*.xml`

   In the method **loadModules()**:
   ```php
    $resourceConfig = sprintf('config.%s.xml', $this->_getResourceConnectionModel('core'));
    $this->loadModulesConfiguration(array('config.xml',$resourceConfig), $this);
   ```

   `$resourceConfig` contains config.mysql4.xml line, our next step will be loading configuration from config.mysql4.xml and config.mysql4.xml files.


* **Describe class group configuration and use in factory methods**
  * Magento uses factory methods to instantiate Models, Blocks and Helpers classes, applying  a necessary method (eg: getModel, helper, etc.). You should pass and abstract name of a
  class group, followed by an entity name.
  > Class groups are described in configuratin XML files in `/etc/config.xml` files of appropiate modules.


  eg: let's try to instatiate the product Object, This can be done by using `getModel()` method.

  ```php
    $product = Mage::getModel('catalog/product');
  ```
  This method retrieves an instance of `Magento_Catalog_Product` this way:

  `app/Mage.php`

  ```php
    /**
     * Retrieve model object
     *
     * @link    Mage_Core_Model_Config::getModelInstance
     * @param   string $modelClass
     * @param   array|object $arguments
     * @return  Mage_Core_Model_Abstract|false
     */
    public static function getModel($modelClass = '', $arguments = array())
    {
        return self::getConfig()->getModelInstance($modelClass, $arguments);
     }
  ```
  **getModel()** calls **getModelInstance()** method, which gets class instance with the help of
  **getModelCalssName**

  `app/code/core/Mage/Core/Model/Config.php`
  ```php
      /**
     * Get model class instance.
     *
     * Example:
     * $config->getModelInstance('catalog/product')
     *
     * Will instantiate Mage_Catalog_Model_Mysql4_Product
     *
     * @param string $modelClass
     * @param array|object $constructArguments
     * @return Mage_Core_Model_Abstract|false
     */
    public function getModelInstance($modelClass='', $constructArguments=array())
    {
        $className = $this->getModelClassName($modelClass);
        if (class_exists($className)) {
            Varien_Profiler::start('CORE::create_object_of::'.$className);
            $obj = new $className($constructArguments);
            Varien_Profiler::stop('CORE::create_object_of::'.$className);
            return $obj;
         } else {
            return false;
         }
     }


    /**
     * Retrieve module class name
     *
     * @param   sting $modelClass
     * @return  string
     */
    public function getModelClassName($modelClass)
    {
        $modelClass = trim($modelClass);
        if (strpos($modelClass, '/')===false) {
            return $modelClass;
         }
        return $this->getGroupedClassName('model', $modelClass);
     }
  ```

    **getGroupedClassName()** method does all the work. As you can see from **getModelClassName**, we pass the group type(model, block or helper) and the class identifier
  (in our case catalog/product) to this method. It explodes our string by using '/' as needle, removes whitespaces and forms array of strings.
  Then we load `Varien_Simplexml_Element` and pass our group name (catalog) to find the class prefix name from `config.xml` of our module. In our example it wold be `Mage_Catalog_Model`;
  in case this extension was overwritten, it could be `Namespace_Catalog_Model`.

  We also add entity class name(product) and after using Magento `uc_words` based on PHP ucwords that return a string with the first character of each word capitalized, if that character
  is alphanumeric. And finally receive `Mage_Catalog_Model_Product` that will be returned in `getModelInstance`

  `app/code/core/Mage/Core/Model/Config.php`

  ```php
     /**
       * Retrieve class name by class group
       *
       * @param   string $groupType currently supported model, block, helper
       * @param   string $classId slash separated class identifier, ex. group/class
       * @param   string $groupRootNode optional config path for group config
       * @return  string
       */
      public function getGroupedClassName($groupType, $classId, $groupRootNode=null)
      {
          if (empty($groupRootNode)) {
              $groupRootNode = 'global/'.$groupType.'s';
           }

          $classArr = explode('/', trim($classId));
          $group = $classArr[0];
          $class = !empty($classArr[1]) ? $classArr[1] : null;

          if (isset($this->_classNameCache[$groupRootNode][$group][$class])) {
              return $this->_classNameCache[$groupRootNode][$group][$class];
           }

          $config = $this->_xml->global->{ $groupType.'s' }->{ $group };

          // First - check maybe the entity class was rewritten
          $className = null;
          if (isset($config->rewrite->$class)) {
              $className = (string)$config->rewrite->$class;
           } else {
              /**
               * Backwards compatibility for pre-MMDB extensions.
               * In MMDB release resource nodes <..._mysql4> were renamed to <..._resource>. So <deprecatedNode> is left
               * to keep name of previously used nodes, that still may be used by non-updated extensions.
               */
              if (isset($config->deprecatedNode)) {
                  $deprecatedNode = $config->deprecatedNode;
                  $configOld = $this->_xml->global->{ $groupType.'s' }->$deprecatedNode;
                  if (isset($configOld->rewrite->$class)) {
                      $className = (string) $configOld->rewrite->$class;
                   }
               }
           }

          // Second - if entity is not rewritten then use class prefix to form class name
          if (empty($className)) {
              if (!empty($config)) {
                  $className = $config->getClassName();
               }
              if (empty($className)) {
                  $className = 'mage_'.$group.'_'.$groupType;
               }
              if (!empty($class)) {
                  $className .= '_'.$class;
               }
              $className = uc_words($className);
           }

          $this->_classNameCache[$groupRootNode][$group][$class] = $className;
          return $className;
       }
  ```


  * **Helpers**
    * Istantiate a helper class the same way  you do models.
    We call `Mage::helper('group/entity') and it calls `getHelperClassName()` this method has a default
    value 'data' in entity name. It means that if you pass only a group name, for eg: `Mage::helper('catalog'),
    it will create an object of `Mage_Catalog_Helper_Data` class.

      `app/Mage.php`
      ```php
       /**
       * Retrieve helper object
       *
       * @param string $name the helper name
       * @return Mage_Core_Helper_Abstract
       */
      public static function helper($name)
      {
          $registryKey = '_helper/' . $name;
          if (!self::registry($registryKey)) {
              $helperClass = self::getConfig()->getHelperClassName($name);
              self::register($registryKey, new $helperClass);
           }
          return self::registry($registryKey);
       }
      ```
      `app/code/core/Mage/Core/Model/Config.php`
      ```php
       /**
       * Retrieve helper class name
       *
       * @param   string $name
       * @return  string
       */
      public function getHelperClassName($helperName)
      {
          if (strpos($helperName, '/') === false) {
              $helperName .= '/data';
           }
          return $this->getGroupedClassName('helper', $helperName);
       }
      ```
  * **Blocks**
    * The process of instantianting blocks is similar to the one of creating models and helpers. **Blocks** can be instantiated via layout xml

      `Namespace_Modulename_Block`

      ```php
         Mage_Core_Model_Layout::createBlock(...);
      ```

*  **Describe the process and configuration of class overrides in Magento**
  I recomend to rewrite a method using the Inheritance

   **How to do it?**
  Creating a module. In config.xml inside globals-> models, blocks and helpers we can specify our rewrites.
  eg: to override the standard class of the `Mage_Catalog_Model_Product` product, you need to use:

    ```xml
      <global>
        ...
        <models>
          ...
          <catalog>
            <rewrite>
              <product> Namespace_Modulename_Model_MyProduct </product>
            </rewrite>
          </catalog>
        </models>
      </global>
    ```

    Principle is the same as in Magento path.

    `<catalog>`  Specify module namespace

    `<rewrite>`  mark that next list of modules rewrites will be described;

    `<product>` Part of the classname following the group classname(`Mage_Catalog_Model`). A new class name is specified inside.
    And the class will be used isntead of the standard one.

    To override the `Mage_Adminhtml_Sales_Order_Create` class, we will add the following lines in configuration (models section).
    ```xml
      <adminhtml>
          <rewrite>
            <sales_order_create>Namespace_Modulename_Adminhtml_Order_Create</sales_order_create>
          </rewrite>
      </adminhtml>
    ```
  * **Controller**
    * To override a controller, we add a new router to config.xml of the module eg:
    ```xml
      <frontend>
        <routers>
          <modulename>
            <use>standard</use>
            <args>
              <module>Namespace_Modulename</module>
              <frontName>routername</frontName>
            </args>
          </modulename>
        </routes>
      </frontend>
    ```
    Normally we set the connection between our router and a module. In this case we override the connection of existing router with our mudule.

    ```xml
      <frontend>
        <routers>
          <prevmodulename>
            <args>
              <modules>
                <namespace_modulename before="Prevnamespace_Prevmodulename">
                  Namespace_Modulename
                  </namespace_modulename>
              </modules>
            </args>
          </prevmodulename>
        </routers>
      </frontend>
    ```
    Next we create a controller with the same name in our module like this:
    ```php
      <?php
        require_once 'Prevnamespace/Prevmodulename/controllers/FilenameController.php';
        class Namespace_Modulename_FilenameController extends Prevnamespace_Prevmodulename_FilenameController
        {
          ...
         }
    ```

  * **Register an Observer**
    * **Observer Pattern**, implemented in Magento, allows inserting additional actions at given moments of code execution
    > Events will be declared in config.xml via layout. In our case:

    ```xml
      <adminhtml>
        <events>
          <catalog_product_save_after>
            <observers>
              <my_individual_name>
                <type> sigleton </type>
                <class>modulename/observer</class>
                <method>methodName</method>
              </my_individual_name>
            </observers>
          </catalog_product_save_after>
        <events>
      </adminhtml>
    ```
    > In this case I announce the event for area adminhtml, but events may also be described both by **global** and of course **frontend**

    ```php
      class Namespace_Module_Module_Observer
      {
        public function methodName(Varien_Event_Observer $observer)
        {
          ...
         }
       }
    ```

    We can get the data of the array, transfered in `dispatchEvent` method, via `$observer` object
    We are free to use the ones, formed automatically and invidually for your module for models and controllers

    * Models

      * *_load_before
      * *_load_after
      ---
      * *_save_before
      * *_save_after
      * *_save_commit_after
      ---
      * *_delete_before
      * *_delete_after
      * *_delete_commit_after
      ---
      * - protected $_eventPrefix='' `Specified individually in a created model`
    * Controllers
      * controller_action_predispatch_*
      * controller_action_predispatch_**
      ---
      * controller_action_postdispatch_*
      * controller_action_postdispatch_**
      ---
      * controller_action_layout_render_before_** `described in a controller, it mostly concerns blocks`
      ---
      * - RouteName
      * - RouteName_ControllerName_ActionName

  * **Set up a cron job**
    * Here is how config of our custom extension will look like.
      ```xml
        <config>
          <crontab>
            <jobs>
              <cronjob_code>
                <scheudule><cron_expr>*/15 * * * *</cron_expr></schedule>
                <run><model>modulename/modelname:methodName</model></run>
              </cronjob_code>
            </jobs>
          </crontab>
        </config>
      ```

      `<cronjob_tab>` Should be unique identifier, will be used as job_code in cron_schedule DB table.

      `<schedule><cron_expr>* * * * *</cron_expr></schedule>` Each asterisk stands for time period corresponding to: minutes, hours, days, months, years.
      If we leave it this way(* * * * *), cron job will executed every minute. in our previous example the cron job will be executed every 15 minutes.

      `<schedule><cron_expr>*/15 * * * *</cron_expr></schedule>` or we can use alternative syntax to do that.`<schedule><cron_expr>0,15,30,45****</cron_expr></schedule>`.

      `<run><model>modulename/modelname:methodName</model></run>` your modulename and modelname should be written in lowercase and stored according to certain(`Magento Factory Method`) rules.
      `methodName` should be written as is.

      >We need to write the code of our method in `app/code/local/Namespace/Modulename/Model/Modelname.php`

      ```php
        <?php
          public function methodName(){
          // your logic here.
          }
      ```
  * Magento XML DOM configuration-based
    * **How does the framework discover active modules and their configuration?**
      `Mage_Core_Model_Config::_loadDeclaredModules()`

    * **What are the common methods with which the framework accesses its configuration values and areas?**
    ```php
      Mage_Core_Model_Config::getDistroServerVars (Get default server variables values ),
      Magento_Core_Model_Config::getStoresConfigByPath(Retrieve store Ids for $path with checking ),
      Magento_Core_Model_Config::$_eventAreas( Configurationfor events by area)
    ```

  * **By what process do the factory methods and autoloader enable class instantiation?**
    ```php
      Varient_Autoload
      spl_autoload_register
    ```
  * **Which class types have configured prefixes, and how does this relate to
  class overrides?**
    Prefixes are used for **BLOCKS, MODELS and HELPERS**. Using prefixes and not referring to classes directly by name allows rewriting the classes in the configuration and without the need to modify the code that uses them.

* **REQUEST FLOW**
  * the request object is initialized un `Mage_Core_Model_App` and the `_initRequest()` method. `getRequest()` and `getResponse` are relevant.

  * **Describe the steps for application initialization**
    *  Check compiler configuration
    *  Include Mage.php
        - Setup core functions and autoloader
        - Register the autoloader
    *  `Mage::run()`
      - Instantiate Mage_Core_Model_App
      - Instantiate the Config model
      - `$app->run()`
        - `baseInit()` - load the base config and initialize cache.
        - `initModules() - load module configuration.
        - Run all the required SQL install and upgrade scripts
        - Setup the locale.
        - `initRequest()` - load the request information into the model.
        - Run all the required data install and upgrade scripts.
        - Dispatch the request.

    * **Loading Magento Module and Database Configuration**
      - The base configuration of Magento is loaded in `Mage_Core_Model_Config::loadBase`. It grabs the glob of `app/etc/*.xml` which contains
        key config informatio. It grabs the glob of `app/etc/*.xml` which contains
          key config information, e.g. database credentials, the core module, the results of installation.

          Database config is stored in app/etc/local.xml and app/etc/config.xml

          `Mage_Core_Model_Config::loadModules` handles looping over each of the modules that exist in app/etc/modules and merging their own config.xml files
          from their respective module directories.

          The order that modules are loaded is:
            1. Mage_All.xml
            2. Mage_*.xml
            3. Everything else.
          If a module depends on another, Magento makes sure it exists and loads its configuration first.
    * **FRONT CONTROLLER**
      * Role:
        The front controller performs the routing of the request to the appropiate controller. It loops over all of the registered routers, passing the request to each one of the,, to be matched
        against a controller capable of handling it. After the request has been dispatched, the front controller send the response to the client.

      * Events:
        * `controller_front_init_before`
          Fired before adding the routers. It is useful for adding a router that takes precedence over any others.
        * `controller_front_init_routers`
          Fired after adding the routers, but before the default router is added. It is useful for adding general routers or modifying existing ones.
        * `controller_front_send_response_before`
          Fired after the response is sent out. It is useful for modifying the response data after the dispatch.
        * `controller_front_send_response_after`
          Fired after the response is sent out. It is useful for performing any tear down operations after the request has been dealt with.
    * **ADDING ROUTER CLASSES**
      > There are two ways to add routes.

      * Using configuration
        ```xml
          <config>
            <default>
              <web>
                <routers>
                  <{ name }>
                    <areas></areas>
                    <class></class>
                  </{ name }>
                </routers>
              </web>
            </default>
          </config>
        ```
      * By observer the `controller_front_init_before` or `controller_front_init_routers` events and injecting the router into the front controller

    * **URL REWRITES**

      * **URL Structure**
        The URL structure in Magento generally uses the format { base_url }/{ front_name }/{ controller }/{ action }.
        `Mage_Core_Controller_Varien_Router_Standard` parses the URLs in this format and maps them to a module used and the controller action to be
        executed.

      * **URL Rewrite Process**
        URL rewrites happen in the Front controller before the routing. The database rewrites are checked and applied first, followd by the configuration(`global->rewrite`) rewrites.
        Rewrites can either redirect the request using HTTP methods, update the request path(keeping the old one for reference) or completely replace the request path.

      * **Database URL Rewrites**
        The most important fields in the `core_url_rewrite` table are `request_path` and `target_path` using the catalog indexer.

      * **Matching requests**
        The requests are applied by the Mage_Core_Model_Url_Rewrite_Request model. The request path is parsed to include any variation(with or without the trailling slash) and then
        looks up the `request_path` column of `the core_url_rewrite` table using the `Mage_Core_Model_Url_Rewrite::LoadByRequestPath()` method.

      * **Request Routing**
        * Built-in Magento routers(in the order that they are matched).
          - Admin
            - Collects all routers for the administration area.
            - Looks within config.xml for routes within admin.
          - Standard
            - Superclass of Admin
            - Collects routes from within frontend routes defined in config.xml
          - CMS
           - Routes CMS pages from identifiers.
          - Default
            - This will always match
            - Routers error or 404 pages.

      The  standard router maps a path to an action by splitting it into `base_url/frontname/controller/action`. The `frontname` is then mapped to a module by way of the configuration files. The controller file
      is then mapped to a module by way of the configuration files. The controller file is then located at `path/to/module/base/controllers/{ Name }/Controller.php`
      Unmapped requests read the **Default** router where they are rewritten to a 404 page and get mapped by the Standard router on the next iteration.

      Before dispatch, requested module, controller, action and parameters are set. Then it is passed to the controller(`all with the Standard router`)

  * **DESIGN AND LAYOUT INITIALISATION**
    * The store design `core_design_packages` is initialized in the controller `preDispatch()` method. The package and theme configurations is then determined by the Mage_Core_Model_Design.
    Layout files get read `$layout->getUpdate()->load()` when the controller calls `$this->loadLayout()`. The same method also compiles the layout `$layout->generateXml()` which processes the
    layout directives.

    The output is rendered when the controller calls `$this->renderLayout(), which calls each of the blocks defined to output data e.g. output="toHtml" in definition, and merged their output into the response body.

    To add a layout handle to be precessed call

    ```php
       <?php Mage::getLayout()->getUpdate()->addHandle('new_handle'); ?>
    ```

    Behind the scenes `Mage_Core_Model_Layout_Update` loads the layout files and their XML while `Mage_Core_Layout` precesses it.
    Layout XML gets merged, first from modules, then `local.xml` and then the  database. The next step is to remove or references as directed by
    the `<remove>` element.

    To add a layout file to be merged, add this to an extensions `config.xml` file:

    ```xml
      <config>
        <{ area }>
          <layout>
            <updates>
              <{ name }>
                <file>{ filename.xml }</file>
              </{ name }>
            </updates>
          </layout>
        </{ area }>
      </config>
    ```
    This file will then be searched for in `app/design/{ area }/{ package }/{ theme }/layout/{ filename.xml }

* **Flushing Data(output)**
  * Response content gets set by the $layout->renderLayout() method. After the controller dispatch mehotd returns,
    The Front Controllerr send the response.
    The `controller_front_send_response_before` event can be used to modify the response before sending. Subsequently, observing
    for the `controller_front_send_response_after` event allow for cleaning up if necessary.
    If the output is not sent in a response object but printed out, it can prevent headers from geing sent as it is unbuffered.

    * **Redirects**
      There are two types of redirects than can be used in a controller action.
      - `_redirect()` - This performs a HTTP redirect
      - `_forward()` - This redirects internally within the application to another controller and/or action.
* **RENDERING**
   * **Customising Core Functionality with Themes**
    Themes have layouts files which, amongst other things, can be used to change which blocks appear on the page. The block template can also be changed and different methods called.
      * **Store-level designs:**
        Magento's hierarchical structured themes means that a base theme can be extended and assigned on a store level.
      * **Registering Custom Themes:**
        Themes can be configured in three ways:

          1. Per storage under `System>Configuration>Design`
          2. Design change with time limits `System>Design`
          3. Theme exceptions can also be set on a category and product level.

      * **Package versus Theme:**
        A package has multiple themes. Each of the themes in a package inherits from the default theme whitin a package.
      * **Design Fallback**
        The theme fallback procedure for locating templates is:

          1. { package }/{ theme }
          2. { package }/default
          3. base/default

        To add further directories to the theme fallback mechanism `Mage_Core_Model_Design_Package::getFilename` need to be rewritten

        For the admin area the fallback is `default/default`

      * **Template and Layout Paths**
        Theme file paths are rendered by `Mage_Core_Model_Design_Package`. `Mage_Core_Model_Layout_Update` requests absolute paths for layout
        files. `Mage_Core_Block_Templates` requests templates with relative paths.

      * **Blocks**
        Blocks are used for output. The `root` block is the parent of all blocks and is of type `Mage_Page_Block_Html`.
        `Mage_Core_Block_Template` blocks use template files to render content. The template filename are set within `setTemplate()` or `addData('template')` with relative paths.
        Templates are just pieces of PHP included in `Mage_Core_Block_Template`. Therefore `$this` is a template refers to the block.

        `Mage_Core_Template` uses a buffer before including a template to prevent premature output.

        The `Mage_Core_Model_Layout::createBlock` method creates instances of blocks.

        The `Mage_Core_Model_Layout_Update class considers which blocks need to be created for each page by looking at the layout handles.

        All output blocks are rendered, e.g. by calling `toHtml()`, which in turn can choose to render their children.

        `Text` and `Text_List` blocks automatically render their content.

        There are two events that are fired around block rendering that can be used to modify the block before and after renderig the HTML:
          1. `core_block_abstract_to_html_before`
          2. `core_block_abstract_to_html_after`

        A child block will only be rendered automatically if it is of class `Mage_Core_Block_Textlist` otherwise the `getChildHtml()` method need to be called.

        Blocks instances can be accessed through the layout, e.g. `Mage::app()->getLayout()` and `$controller->getLayout()`.Block output is controlled by the `_toHtml()` funtion.

        Templates are rendered by the `renderView()/fetchView()` methods inside a template block. Output buffering can be disabled with `$layout->setDirectOutput()`
        It is possible to add a block to the current layout but it need to be done before the `renderLayout()` method is called.

      * **Layout XML**
        * `<reference>` edit a block
        * `<block>` define a block
        * `<action>` call method on a block
        * `<update>` include nodes from another handle.

        Layout files can be registered in config.xml
        ```xml
          <config>
            <{ area }>
              <layout>
                <updates>
                  <{ name }>
                    <file>{ filepath } </file>
                  </{ name }>
                </updates>
              </layout>
            </{ area }>
          </config>
        ```
        >**Page output can be customized in the following ways:**

          - Template changes
          - Layout changes
          - Overriding blocks
          - Observers.

        >**Variables on blocks can be set in the following ways:**

          - **layout** `Through actions or attributes.`
          - **Controller** `$this->getLayout()->getBlock()`
          - **Child blocks** `$this->getChild()`
          - **Other** `Mage::app()->getLayout()`

      * **Head Blocks Assets**
        Javascripts and CSS assets are handled in the `Mage_Page_Block_Html_head`. This block handles the merging of assets into a single file to minimize HTTP requests.
        The merged file is based on the edit time of the source files.
        When mergin CSS, a callback function on `Mage_Core_Model_Design_Package` is called to update any @import or url() directives with the correct URLs.

* **Databases**
  * **Models, Resource Models and Collections**
    * **Basic Concepts**

      - A **Model** is used to store and manipulate data about a particular object. Models typically contain the business logic of the application.
      - A **Resource Model** is used to interact with the database on behalf of the Model. The Resource Model actually performs the CRUD operations.
      - A **Collection Model** handles working with groups of models and performing(CRUD) operations on groups of models.

    * **Types of Magento Model**

      1. **Simple:**
        Correspond to a single table
      2. **EAV**
        Correspond to multiple tables using the EAV schema design pattern.
    * **Database Connection**
      Database connections are configured in config.xml
      ```xml
        <config>
          <global>
            <resources>
              <{ identifier }>
                <connection>
                  <host>{ host }</host>
                  <username>{ username }</username>
                  <password>{ password }</password>
                  <dbname>{ dbname }</dbname>
                  <model>{ model }</model>
                  <initStatements>{ init commands }</initStatements>
                  <type>{  adapter type  }</type>
                  <active>{ 0|1 }</active>
                </connection>
              </{ identifier }>
            </resources>
          </global>
        </config>
      ```
      The core Magento connections(`default_setup, default_read, default_write,core_setup, core_read, core_write`)
      are configured in `app/etc/config.xml` with the database credentials stored in `app/etc/local.xml`.

    * **Working with Database Tables**
      Magento used the **Resource Models** to interact with the database tables. When a Model is loaded or saved, it calls
      its resource model to perform the operation(executing the database queries). Database table names are configured in config.xml
      and resource modesl retrieve them using look-up methods, which allows for the table names to be customised, e.g. adding a prefix to all
      table names.

      Tables are called entities and are configured like so:
        ```xml
          <config>
            <glogal>
              <models>
                <muslen>
                  <class>Muslen_Module_Model</class>
                  <resourceModel>muslen_resource</resourceModel>
                </muslen>
                <muslen_resource>
                  <entitiess>
                    <table_one>
                      <tableo>muslen_module_tableOne</tableo>
                    </table_one>
                  </entitiess>
                </muslen_resource>
              </models>
            </glogal>
          </config>
        ```

        This allows us to load the table via `getTable('muslen/table_one');`.
* **Performing Joins**
  The following methods exist to create joins between tables on collections and on select instances.

  * Zend_Db_Select::join()
  * Zend_Db_Select::joinInner()
  * Zend_Db_Select::joinLeft()
  * Zend_Db_Select::joinRight()
  * Zend_Db_Select::joinFull()
  * Zend_Db_Select::joinCross()
  * Zend_Db_Select::joinNatural()
  * Mage_Core_Model_Resource_Db_Collection_Abstract::join()

* **Table Name Lookups**
  Use `Mage::getModel('core/resource')->getTablename($modelEntity)` to retrieve the defined table for any model.
  Table names in Magento are configurable to allow customizing and overriding the database schema e.g. using custom table.

  Accessing resource models can be done using the class `Mage_Core_Model_Resource_Db_Abstract` and the methods `getMainTable() and getTable($entityName)`.

* **Loading Data**
  The loading of a model from the database is done using the `load($id, $field = null)` method. The field argument allows the developer to load the records from a different key,
  if no field is specified, then the resource model  identifies the primary key based on the parameters provided to the `_init($table, $key)` method call when the resource model
  was constructed.

  Magento uses Zend database abstraction classes like `Zend_Db_Select` to perform database operations. These classes allow building and executing database queries without having
  to use the syntax of the specific database engine being used.

  When a record is fetched with `Zend_Db_Select` it is added to the `Varien_Object` using `setData($data)`. Some fields may be serialized in the database, so they are un-serialized
  before adding to the `Varien_Object`.

* **Saving Data**
  Saving is not as trivial as loading. The first step is to check to see if the model has any changes. Both `setData($data)` or `unsetData($key, $value)` set the `_hasDataChanges` flag
  on the model. This flag can then be used to determine whether it needs to be written out to the database.

  A **transaction** is then begun so that any changes can be rolled back in the event that something goes wrong during the saving process.

  Firstly a check for uniqueness is performed. This queries the database to check whether each of the fields marked as unique are actually unique. If a duplicate key is found, then
  it is bubbled up using an exception.

  The data then needs to be prepared for insertion or update. This is performed in the `prepareDataForSave()` method. This calls DESCRIBE on the table to identify the column types and
  sanitise the input accordingly. This description is, of course, cached.**This is the reason that cache needs to be cleared if scheme changes are made.**

  The next problem is identifying whether an `INSERT` or and `UPDATE` statement is required. This is usually performed by checking for the existence of the primary key by calling
  `$model->getID() == null`, and here is no exception. If there is no ID set, then it is auto-incremented in the database and needs to be fetched from there. After performing the
  `INSERT, getLastInsertId()` is called on the write adapter to add it to the model.

  If there was an ID specified on the model when save was called, it's either because an update to a model is being saved or because the ID of the model is not controlled by the database
  with an auto-increment. If the flag `_isPkAutoIncrement` is set then an UPDATE can be used. Otherwise the database needs to be checked for an existing record with this key. An UPDATE
  occurs if there is a key, otherwise an INSERT is used.

  The `_isPkAutoIncrement` flag is set to true as a default and can be overwritten by a subclass.

* **Collection Interface**
  Collection Models provide a consistent interface for performing filtering and sorting models, e.g. `addFieldToFilter(), addOrder()` and `setOrder()`.

* **Group Save Operations**
  When several save operations have to be performed for an entity, Magento uses database transactions to ensure that the data stays in a cosnsitent stat in the database.

* **Filtering Flat Table Collections**
  Through the use of:
    * `$collection->addFilter()`
    * `$collection->getSelect()->where()`

* **Ordering Flat Table Collections**
  Through the use of:
    * $collection->setOrder()
    * $collection->getSelect()->order()

  The first method goes through the collection interface, which could perform additional logic, while the second method operates directly on the underlying database
  select statement.
* **Events fired by CRUD operations**
  * `model_load_before`
  * `model_load_after`
  * `model_save_commit_after`
  * `model_save_before`
  * `model_save_after`
  * `model_delete_before`
  * `model_delete_after`
  * `model_delete_commit_after`
  * {collection_event_prefix}_load_before
  * {collection_event_prefix}_load_after

* **Setup, Read and Write Database Resources**
  Resource models request the specific types of database connections they require.
  They different types are defined to allow for different permissions over the
  database. For example, read for read-only connection, write for changing data,
  and setup for resource intensive setup process. However, in practice, all of
  Magento's connection reesources inherit from the `default_setup` resource, so they
  all use the same connection.

* **Install and Upgrade Scripts**
  Magento uses **Setup Resource Models** to perform install and upgrade operations for modules.
  These are executed during the application initialisation where each Setup Resource is allowed
  to apply any updates it requires. This is usually done by inspecting the installed version of
  the module from the core_resource table and executing any setup scripts defined.

  **Setup resources are defined in config.xml**
    ```xml
      <config>
        <global>
          <resources>
            <{ resource_name }>
              <setup>
                <module> { module_name } </module>
                <class> { setup_resource_class } </class>
              </setup>
            </{ resource_name }>
          </resources>
        </global>
      </config>
    ```
  Then the install and upgrade scripts, which are simple PHP scripts executed by
  including them within the setup resource, are placed in `{ module_root }/sql/{ resource_name}/
  ` for **system install/upgrade** scripts or `{ module_root }/data/{ resource_name}/` for **data
  install/upgrade** scripts

  The scripts use the following naming scheme:
    * `install-{ version }.php`
    * `update-{ from_version }-{ to_version }.php`
    * `data-install-{ version }.php`
    * `data-upgrade-{ from_version }-{ to_version }.php`

    > If the module is not present in the database, the Setup Model will install
    the module by first running the latest install script and then the upgrade
    scripts since the install script version.

    > If the module verson in `config.xml` is higher than the version in the
    database, the Setup Model performs an upgrade by running all upgrade
    scripts  which have a `from-version` higher or equal to the database version
    and `to-version` lower or equal to the new config version.

  *  **Different Setup Scripts**
    Different Setup classes have additional methodsto aid the install or upgrade
    procedures of their paticulaar entities, e.g. the **EAV** setup resource
    has methods for creating entity attributes.
    > The base setup classses for flat dables and EAV entities are
    `Mage_Core_Model_Resource_Setup` and `Mage_Eav_Model_Entity_Setup` respectively.
  * **Available Setup Methods**
    * `startSetup()`
    * `endSetup()`
    * `getTable()`
    * `setTable()`
    * `updateTable()`
    * `run($sql)`

  * **EAV attributes Setup MEthods**
    * `addAttribute()` - Handles attribute creation, including creating attribute data,
    adding it to groups and sets and setting attribute options.
    * `updateAttribute() - Simply updates the attribute data. It is also called by
      `addAttribute()` if the attribute being added already exists.

  * **Database Rollback**
    Magento defines a module rollback procedure when the `config.xml` module
    version is lower than the database version. Howeverm the rollback script
    execution is not actually implemented.

 * **Supporting multiple RDBMSs**
  Magento abstracts database engine logic by using the Varien_Db_Adapter_Interface.
  Database engine classes implement this interface, which makes it easy to replace
  one egine class with another without having to rewrite all models that use the
  database. The actual RDBMS used is defined in the connection configuration
  using the `<type>` field, e.g. `<type>pdo_mysql</type>`
