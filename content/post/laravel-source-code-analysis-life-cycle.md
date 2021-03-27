---
title: "laravel源码分析-生命周期"
date: 2019-06-04T01:01:42+08:00
typora-root-url: ..\..\static
tags: ["laravel生命周期"]
categories: ["Laravel"]
draft: false
---

前提:使用的laravel版本为5.7,因为涉及到的流程比较复杂,请结合具体代码查看

文件名:public/index.php

```php
$app = require_once __DIR__.'/../bootstrap/app.php';
```

文件名:bootstrap/app.php

```php
$app = new Illuminate\Foundation\Application(
    $_ENV['APP_BASE_PATH'] ?? dirname(__DIR__)
);
```

文件名:vendor/laravel/framework/src/Illuminate/Foundation/Application.php

```php
/**
     * Create a new Illuminate application instance.
     *创建一个新的应用程序实例
     * @param  string|null  $basePath
     * @return void
     */
    public function __construct($basePath = null)
    {
        if ($basePath) {
            //设置应用程序的基本路径和绑定容器中的所有应用程序路径
            $this->setBasePath($basePath);
        }

        //将基本绑定注册到容器中
        $this->registerBaseBindings();

        //注册所有基本服务提供者
        $this->registerBaseServiceProviders();

        //在容器中注册核心类别名
        $this->registerCoreContainerAliases();
    }
```

文件名:bootstrap/app.php

```php
//绑定实例
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);

$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);
```

文件名:public/index.php

```php
//创建一个新的HTTP内核实例
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

//处理传入的HTTP请求
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()//创建一个http请求,这里用到了Symfony实例
);
```

说明:我们关注的重点在handle方法,传入参数为使用到Symfony组件中的http包,下面我们具体看一下handle方法

文件名:vendor/laravel/framework/src/Illuminate/Foundation/Http/Kernel.php

```php
/**
     * Handle an incoming HTTP request.
     *处理传入的HTTP请求
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function handle($request)
    {
        try {
            //启用对_method请求参数的支持，以确定预期的HTTP方法
            $request->enableHttpMethodParameterOverride();

            //通过中间件/路由器发送给定的请求。
            $response = $this->sendRequestThroughRouter($request);
            
        } catch (Exception $e) {
            $this->reportException($e);

            $response = $this->renderException($request, $e);
        } catch (Throwable $e) {
            $this->reportException($e = new FatalThrowableError($e));

            $response = $this->renderException($request, $e);
        }

        $this->app['events']->dispatch(
            new Events\RequestHandled($request, $response)
        );

        return $response;
    }
```

很明显,sendRequestThroughRouter才是主角,我们来看一下

```php
/**
     * Send the given request through the middleware / router.
     *通过中间件/路由器发送给定的请求。
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    protected function sendRequestThroughRouter($request)
    {
        $this->app->instance('request', $request);

        //清除已解析的外观实例
        Facade::clearResolvedInstance('request');

        //为HTTP请求引导应用程序
        $this->bootstrap();
        
        return (new Pipeline($this->app))//这个扩展管道捕获每个片期间发生的任何异常
                    ->send($request)//设置通过管道发送的对象
                    ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)//中间件
                    ->then($this->dispatchToRouter());//使用最终目标回调运行管道,传入参数为路由调度程序回调
    }
```

这里都加了注释,需要注意的是then方法,我们来看一下:

文件名:D:\WWW\Test\laravel_test\vendor\laravel\framework\src\Illuminate\Pipeline\Pipeline.php

```php
/**
     * Run the pipeline with a final destination callback.
     *使用最终目标回调运行管道
     * @param  \Closure  $destination
     * @return mixed
     */
    public function then(Closure $destination)
    {
        //$this->pipes为中间件, carry方法为处理
        $pipeline = array_reduce(
            array_reverse($this->pipes), $this->carry(), $this->prepareDestination($destination)
        );
        return $pipeline($this->passable);
    }
```

这里用到了array_reduce函数,具体的使用方法看文档:[https://www.php.net/manual/zh/function.array-reduce.php](https://www.php.net/manual/zh/function.array-reduce.php),我们需要关注的是第三个参数,这个参数为前面传入的路由调度程序回调,这里面还是用到了carry方法:

```php
/**
     * Get a Closure that represents a slice of the application onion.
     *获取一个表示应用程序洋葱片的闭包
     * @return \Closure
     */
    protected function carry()
    {
        return function ($stack, $pipe) {
            return function ($passable) use ($stack, $pipe) {
                if (is_callable($pipe)) {
                    // If the pipe is an instance of a Closure, we will just call it directly but
                    // otherwise we'll resolve the pipes out of the container and call it with
                    // the appropriate method and arguments, returning the results back out.
                    return $pipe($passable, $stack);
                } elseif (! is_object($pipe)) {

                    [$name, $parameters] = $this->parsePipeString($pipe);
                    // If the pipe is a string we will parse the string and resolve the class out
                    // of the dependency injection container. We can then build a callable and
                    // execute the pipe function giving in the parameters that are required.
                    $pipe = $this->getContainer()->make($name);

                    $parameters = array_merge([$passable, $stack], $parameters);
                } else {
                    // If the pipe is already an object we'll just make a callable and pass it to
                    // the pipe as-is. There is no need to do any extra parsing and formatting
                    // since the object we're given was already a fully instantiated object.
                    $parameters = [$passable, $stack];
                }
                //这里走到了中间件的handle方法,里面有个next方法
                $response = method_exists($pipe, $this->method)
                                ? $pipe->{$this->method}(...$parameters)
                                : $pipe(...$parameters);

                return $response instanceof Responsable
                            ? $response->toResponse($this->container->make(Request::class))
                            : $response;
            };
        };
    }
```

这里将app/Http/Kernel.php中的$middleware拿来进行处理,每一个里面都有handle方法来进行 处理传入请求 ,我们来看一个中间件:

文件名:vendor/laravel/framework/src/Illuminate/Foundation/Http/Middleware/ValidatePostSize.php

```php
public function handle($request, Closure $next)
    {
        $max = $this->getPostMaxSize();

        if ($max > 0 && $request->server('CONTENT_LENGTH') > $max) {
            throw new PostTooLargeException;
        }

        return $next($request);
    }
```

我们需要注意的是最后的 return $next($request);正是因为有了这个,才可以使用array_reduce函数进行迭代

好,我们再回到这个文件 :vendor/laravel/framework/src/Illuminate/Foundation/Http/Kernel.php 中的dispatchToRouter这个方法,下面我们就按照给出代码一步一步找到路由分配控制器的地方

```php
/**
     * Get the route dispatcher callback.
     *获取路由调度程序回调
     * @return \Closure
     */
    protected function dispatchToRouter()
    {
        return function ($request) {
            $this->app->instance('request', $request);

            return $this->router->dispatch($request);
        };
    }
/**
     * Run the given route within a Stack "onion" instance.
     *在堆栈“onion”实例中运行给定的路由
     * @param  \Illuminate\Routing\Route  $route
     * @param  \Illuminate\Http\Request  $request
     * @return mixed
     */
    protected function runRouteWithinStack(Route $route, Request $request)
    {
        $shouldSkipMiddleware = $this->container->bound('middleware.disable') &&
                                $this->container->make('middleware.disable') === true;

        $middleware = $shouldSkipMiddleware ? [] : $this->gatherRouteMiddleware($route);

        return (new Pipeline($this->container))
                        ->send($request)
                        ->through($middleware)
                        ->then(function ($request) use ($route) {
                            return $this->prepareResponse(
                                $request, $route->run()
                            );
                        });
    }
```

文件名:vendor/laravel/framework/src/Illuminate/Routing/Route.php

```php
/**
     * Run the route action and return the response.
     *运行route操作并返回响应
     * @return mixed
     */
    public function run()
    {
        $this->container = $this->container ?: new Container;

        try {
            if ($this->isControllerAction()) {//是string 则运行controller
                return $this->runController();
            }

            return $this->runCallable();
        } catch (HttpResponseException $e) {
            return $e->getResponse();
        }
    }
/**
     * Run the route action and return the response.
     *运行route操作并返回响应
     * @return mixed
     *
     * @throws \Symfony\Component\HttpKernel\Exception\NotFoundHttpException
     */
    protected function runController()
    {
        return $this->controllerDispatcher()->dispatch(
            $this, $this->getController(), $this->getControllerMethod()
        );
    }
/**
     * Get the controller instance for the route.
     *获取路由的控制器实例
     * @return mixed
     */
    public function getController()
    {
        if (! $this->controller) {
            $class = $this->parseControllerCallback()[0];
            //这里处理依赖注入, 容器
            $this->controller = $this->container->make(ltrim($class, '\\'));
        }

        return $this->controller;
    }
```

我们需要关注的是:$this->controllerDispatcher()->dispatch()这段代码,其中的 dispatch 方法实现了分配到路由,我们来看一下具体代码:

文件名:vendor/laravel/framework/src/Illuminate/Routing/ControllerDispatcher.php

```php
/**
     * Dispatch a request to a given controller and method.
     *将请求分派给给定的控制器和方法。
     * @param  \Illuminate\Routing\Route  $route
     * @param  mixed  $controller
     * @param  string  $method
     * @return mixed
     */
    public function dispatch(Route $route, $controller, $method)
    {
        //解析对象方法的类型暗示依赖项  传入参数:1,为没有空值的参数的键/值列表,2:控制器实例,3:方法
        $parameters = $this->resolveClassMethodDependencies(
            $route->parametersWithoutNulls(), $controller, $method
        );
        //有callAction,则执行, 继承框架中的controller,就会有callAction
        if (method_exists($controller, 'callAction')) {
            return $controller->callAction($method, $parameters);
        }
        //执行
        return $controller->{$method}(...array_values($parameters));
    }
```

如果我们继承了Illuminate\Routing\Controller,拿着里面就会有一个callAction方法:我们来看一下:

文件名:vendor/laravel/framework/src/Illuminate/Routing/Controller.php

```php
/**
     * Execute an action on the controller.
     *在控制器上执行一个操作,大部分操作会走到这里
     * @param  string  $method
     * @param  array   $parameters
     * @return \Symfony\Component\HttpFoundation\Response
     */
    public function callAction($method, $parameters)
    {
        return call_user_func_array([$this, $method], $parameters);
    }
```

最后,我们来到$response->send();这一句,来看一下:

文件名:vendor/symfony/http-foundation/Response.php

```php
/**
     * Sends HTTP headers and content.
     *
     * @return $this
     */
    public function send()
    {
        //发送HTTP header
        $this->sendHeaders();
        //发送conetent
        $this->sendContent(); //echo $this->content;

        if (\function_exists('fastcgi_finish_request')) {
            fastcgi_finish_request();
        } elseif (!\in_array(\PHP_SAPI, array('cli', 'phpdbg'), true)) {
            static::closeOutputBuffers(0, true);
        }
        
        return $this;
    }
```

至此,laravel的生命周期完成