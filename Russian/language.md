# Базовый синтаксис

В этой главе мы обсудим организацию файлов и пространств имен, объявления переменных, разные синтаксические соглашения и несколько других концепций.

<a name='organizing-code-in-files-and-namespaces'></a>

## Организация кода в файлах и пространствах имён

В PHP вы можете поместить код в любой файл без определенной структуры. В Zephir каждый файл должен содержать класс (и только один класс). Каждый класс должен иметь пространство имён, а структура каталогов должна соответствовать именам используемых классов и имен. (Это похоже на соглашение автозагрузки классов PSR-4, за исключением того, что оно обеспечивается за счёт самого языка).

Например, для следующей структуры классы в каждом файле должны быть:

    mylibrary/
        router/
            exception.zep # MyLibrary\Router\Exception
        router.zep # MyLibrary\Router
    

Класс в `mylibrary/router.zep`:

    namespace MyLibrary;
    
    class Router
    {
    
    }
    

Класс в `mylibrary/router/exception.zep`:

    namespace MyLibrary\Router;
    
    class Exception extends \Exception
    {
    
    }
    

Zephir выбрасывает ошибку компиляции, если файл или класс не находится в ожидаемом файле или наоборот.

<a name='instruction-separation'></a>

## Разделение инструкций

Возможно, вы уже заметили, что в примерах кода в предыдущей главе было очень мало точек с запятой. Вы можете использовать точки с запятой для разделения операторов и выражений, как в Java, C / C ++, PHP и подобных языках:

    myObject->myMethod(1, 2, 3); echo "world";
    

<a name='comments'></a>

## Комментарии

Zephir поддерживает комментарии в стиле C и C++. Это однострочные комментарии вида `// ...`, и многострочные комментариями вида `/* ... */`:

    // Это однострочный
    
    /**
     * Многострочный комментарий
     */
    

В большинстве языков комментарии — это просто текст, игнорируемый компилятором/интерпретатором. В Zephir многострочные комментарии также используются в качестве блоков документации (docblock), и они экспортируются в сгенерированный код, поэтому они являются частью языка!

Если блок документации не находится там, где ожидается, компилятор выдаст исключение.

<a name='variable-declarations'></a>

## Объявления переменных

В Zephir все переменные, используемые в заданной области видимости, должны быть объявлены. Этот процесс предоставляет компилятору важную информацию для выполнения оптимизаций и проверок. Переменные должны быть уникальными идентификаторами, и они не могут быть зарезервированными словами.

    // Объявление переменных для одного и того же типа в одной инструкции
    var a, b, c;
    
    // Объявление каждой переменной с новой строки
    var a;
    var b;
    var c;
    

Переменные могут дополнительно иметь начальное совместимое значение по умолчанию:

    // Объявление переменных со значениями по умолчанию
    var a = "hello", b = 0, c = 1.0;
    int d = 50; bool some = true;
    

Имена переменных чувствительны к регистру, следующие переменные различаются:

    // Различные переменные
    var somevalue, someValue, SomeValue;
    

<a name='variable-scope'></a>

## Область переменной

Все объявленные переменные локально охвачены методом, в котором они были объявлены:

    class MyClass
    {
    
        public function someMethod1()
        {
            int a = 1, b = 2;
            return a + b;
        }
    
        public function someMethod2()
        {
            int a = 3, b = 4;
            return a + b;
        }
    
    }
    

<a name='super-global'></a>

## Супер-глобальные переменные

Zephir не поддерживает глобальные переменные. Доступ к глобальным переменным из пользовательского пространства PHP недопустим. Тем не менее, вы можете получить доступ к супер-глобальным переменным PHP следующим образом:

    // Получение значения от _POST
    let price = _POST["price"];
    
    // Чтение значения из _SERVER
    let requestMethod = _SERVER["REQUEST_METHOD"];
    

<a name='local-symbol-table'></a>

## Локальная таблица символов

Каждый метод или контекст в PHP имеет таблицу символов, которая позволяет писать переменные очень динамичным способом:

    <?php
    
    $b = 100;
    $a = "b";
    echo $$a; // выведет 100
    

Zephir не реализует этот функционал, так как все переменные скомпилированы до низкоуровневых переменных и не существует способа узнать, какие переменные существуют в определенном контексте. Если вы хотите создать переменную в текущей таблице символов PHP, вы можете использовать следующий синтаксис:

    // Установить переменную $name в PHP
    let {"name"} = "hello";
    
    // Установить переменную $price в PHP
    let name = "price";
    let {name} = 10.2;