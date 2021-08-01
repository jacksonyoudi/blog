---
title: supervisor
date: 2021-08-01 22:38:07
top: true
hide: false
cover: true
toc: true
mathjax: false
summary: 通过源码介绍supervisor，学习一些通用的软件架构和技巧
keywords:
-  go
-  supervisor
-  源码
- python

categories: 技术
tags:
-  go
-  supervisor
-  源码
- python
---

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/mTUPK0.jpg)

今天介绍一下supervisor的源码。 supervisor是python写的。如果你不懂python，我也介绍一下golang版的实现。 如果你不会，其他语言的实现，应该也有，github找一下。 自己读一下。实现原理应该都是一样的。

[源码](https://github.com/Supervisor/supervisor)


## supervisor的组件
### 1. supervisord

>服务器主进程名为supervisord。它负责在自己的调用中启动子程序，响应来自客户端的命令，重新启动崩溃或退出的子进程，记录其子进程stdout和stderr 输出，以及生成和处理与子进程生命周期中的点相对应的“事件”。

服务器进程使用配置文件。这通常位于/etc/supervisord.conf中。此配置文件是“Windows-INI”样式配置文件。通过适当的文件系统权限保持此文件的安全非常重要，因为它可能包含未加密的用户名和密码。

### 2. supervisorctl

主进程的命令行客户端部分名为 supervisorctl。它为supervisord提供的功能提供了类似shell的界面。从 supervisorctl，用户可以连接到不同的 supervisord进程（一次一个），获取由子进程控制的状态，停止和启动子进程，并获取supervisord的运行进程列表。

命令行客户机通过UNIX域套接字或internet (TCP)套接字与服务器通信。服务器可以断言客户机的用户应该在执行命令之前提供身份验证凭据。客户机进程通常使用与服务器相同的配置文件，但是其中包含[supervisorctl]部分的任何配置文件都可以工作。

### 3. Web Server

如果您针对internet套接字启动了adminord，那么可以通过浏览器访问具有与supervise orctl类似功能的(稀疏的)web用户界面。在激活配置文件的`[inet_http_server]`部分之后，访问服务器URL(例如`http://localhost:9001/`)，通过web界面查看和控制进程状态。

### 4. XML-RPC Interface
服务于web UI的同一个HTTP服务器提供一个XML-RPC接口，该接口可用于询问和控制管理器及其运行的程序。参见XML-RPC API文档。


### 5. 目录结构

整体看一下目录结构：

```bash
├── __init__.py
├── childutils.py
├── compat.py
├── confecho.py
├── datatypes.py
├── dispatchers.py
├── events.py
├── http.py
├── http_client.py
├── loggers.py
├── medusa
│   ├── CHANGES.txt
│   ├── LICENSE.txt
│   ├── README.txt
│   ├── TODO.txt
│   ├── __init__.py
│   ├── asynchat_25.py
│   ├── asyncore_25.py
│   ├── auth_handler.py
│   ├── counter.py
│   ├── default_handler.py
│   ├── docs
│   │   ├── README.html
│   │   ├── async_blurbs.txt
│   │   ├── composing_producers.gif
│   │   ├── data_flow.gif
│   │   ├── data_flow.html
│   │   ├── producers.gif
│   │   ├── programming.html
│   │   ├── proxy_notes.txt
│   │   ├── threads.txt
│   │   └── tkinter.txt
│   ├── filesys.py
│   ├── http_date.py
│   ├── http_server.py
│   ├── logger.py
│   ├── producers.py
│   ├── util.py
│   └── xmlrpc_handler.py
├── options.py
├── pidproxy.py
├── poller.py
├── process.py
├── rpcinterface.py
├── scripts
│   ├── loop_eventgen.py
│   ├── loop_listener.py
│   ├── sample_commevent.py
│   ├── sample_eventlistener.py
│   └── sample_exiting_eventlistener.py
├── skel
│   └── sample.conf
├── socket_manager.py
├── states.py
├── supervisorctl.py
├── supervisord.py
├── tests
│   ├── __init__.py
│   ├── base.py
│   ├── fixtures
│   │   ├── donothing.conf
│   │   ├── example
│   │   │   └── included.conf
│   │   ├── hello.sh
│   │   ├── include.conf
│   │   ├── issue-1054.conf
│   │   ├── issue-565.conf
│   │   ├── issue-638.conf
│   │   ├── issue-663.conf
│   │   ├── issue-664.conf
│   │   ├── issue-835.conf
│   │   ├── issue-836.conf
│   │   ├── listener.py
│   │   ├── spew.py
│   │   └── unkillable_spew.py
│   ├── test_childutils.py
│   ├── test_confecho.py
│   ├── test_datatypes.py
│   ├── test_dispatchers.py
│   ├── test_end_to_end.py
│   ├── test_events.py
│   ├── test_http.py
│   ├── test_http_client.py
│   ├── test_loggers.py
│   ├── test_options.py
│   ├── test_poller.py
│   ├── test_process.py
│   ├── test_rpcinterfaces.py
│   ├── test_socket_manager.py
│   ├── test_states.py
│   ├── test_supervisorctl.py
│   ├── test_supervisord.py
│   ├── test_web.py
│   └── test_xmlrpc.py
├── ui
│   ├── images
│   │   ├── button_refresh.gif
│   │   ├── button_restart.gif
│   │   ├── button_stop.gif
│   │   ├── icon.png
│   │   ├── rule.gif
│   │   ├── state0.gif
│   │   ├── state1.gif
│   │   ├── state2.gif
│   │   ├── state3.gif
│   │   └── supervisor.gif
│   ├── status.html
│   ├── stylesheets
│   │   └── supervisor.css
│   └── tail.html
├── version.txt
├── web.py
└── xmlrpc.py
```


整体架构


源码学习按上面的分块进行介绍

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/t4Mc7M.jpg)


## supervisord
先找到入口程序
```python
# Main program
def main(args=None, test=False):
    assert os.name == "posix", "This code makes Unix-specific assumptions"
    # if we hup, restart by making a new Supervisor()
    first = True
    while 1:
        options = ServerOptions()
        options.realize(args, doc=__doc__)
        options.first = first
        options.test = test
        if options.profile_options:
            sort_order, callers = options.profile_options
            profile('go(options)', globals(), locals(), sort_order, callers)
        else:
            go(options)
        options.close_httpservers()
        options.close_logger()
        first = False
        if test or (options.mood < SupervisorStates.RESTARTING):
            break

if __name__ == "__main__": # pragma: no cover
    main()
```

从上面看出， main()中有个死循环一直只工作。
下面详细介绍，在循环中做了哪些工作？

```python
# Main program
def main(args=None, test=False):
    assert os.name == "posix", "This code makes Unix-specific assumptions"
    # if we hup, restart by making a new Supervisor()
    first = True
    while 1:
        options = ServerOptions() // 配置
        options.realize(args, doc=__doc__)
        options.first = first
        options.test = test
        if options.profile_options:
            sort_order, callers = options.profile_options
            profile('go(options)', globals(), locals(), sort_order, callers)
        else:
            go(options) // 加载配置开始运行
        options.close_httpservers()
        options.close_logger()
        first = False
        if test or (options.mood < SupervisorStates.RESTARTING):
            break

def go(options): # pragma: no cover
    d = Supervisor(options) // 实例化一个Supervisor对象
    try:
    d.main()  // 运行main()函数
    except asyncore.ExitNow:
        pass
```

### `Supervisor`类的代码

```python
class Supervisor:
    stopping = False  # set after we detect that we are handling a stop request
    lastshutdownreport = 0  # throttle for delayed process error reports at stop
    process_groups = None  # map of process group name to process group object
    stop_groups = None  # list used for priority ordered shutdown

    def __init__(self, options): # 初始化
        self.options = options # 配置
        self.process_groups = {}
        self.ticks = {}

    def main(self):
        if not self.options.first:
            # prevent crash on libdispatch-based systems, at least for the
            # first request
            self.options.cleanup_fds()

        self.options.set_uid_or_exit()

        if self.options.first:
            self.options.set_rlimits_or_exit()

        # this sets the options.logger object
        # delay logger instantiation until after setuid
        self.options.make_logger()

        if not self.options.nocleanup:
            # clean up old automatic logs
            self.options.clear_autochildlogdir()

        self.run() # 运行

    def run(self):
        self.process_groups = {}  # clear
        self.stop_groups = None  # clear
        events.clear()
        try:
            # 根据配置进行添加process
            for config in self.options.process_group_configs:
                self.add_process_group(config)
            # 进程环境
            self.options.process_environment()
            # 打开http web
            self.options.openhttpservers(self)
            # 用于捕获信号
            self.options.setsignals()
            # 主进程是否成为守护进程
            if (not self.options.nodaemon) and self.options.first:
                self.options.daemonize()
            # writing pid file needs to come *after* daemonizing or pid
            # will be wrong
            self.options.write_pidfile()
            # 运行异步io服务器
            self.runforever()
        finally:
            # 异常退出，清理工作
            self.options.cleanup()

```

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/gPpFtg.jpg)


上面代码只有 `self.runforever()` 是工作的

```python
    def runforever(self):
       # 事件通知机制
        events.notify(events.SupervisorRunningEvent())
        timeout = 1  # this cannot be fewer than the smallest TickEvent (5)
        # 获取已经注册的句柄
        socket_map = self.options.get_socket_map()
        
        # 这里会一直 运行，相当于守护进程
        while 1:
            # 保存运行信息等
            combined_map = {}
            combined_map.update(socket_map)
            combined_map.update(self.get_process_map())
            
            # 进程信息
            pgroups = list(self.process_groups.values())
            pgroups.sort()
            
            # 根据进程配置开启或关闭进程
            if self.options.mood < SupervisorStates.RUNNING:
                if not self.stopping:
                    # first time, set the stopping flag, do a
                    # notification and set stop_groups
                    self.stopping = True
                    self.stop_groups = pgroups[:]
                    events.notify(events.SupervisorStoppingEvent())

                self.ordered_stop_groups_phase_1()

                if not self.shutdown_report():
                    # if there are no unstopped processes (we're done
                    # killing everything), it's OK to shutdown or reload
                    raise asyncore.ExitNow
                    
            for fd, dispatcher in combined_map.items():
                if dispatcher.readable():
                    self.options.poller.register_readable(fd)
                if dispatcher.writable():
                    self.options.poller.register_writable(fd)
            # poll操作
            r, w = self.options.poller.poll(timeout)

            for fd in r:
                if fd in combined_map:
                    try:
                        dispatcher = combined_map[fd]
                        self.options.logger.blather(
                            'read event caused by %(dispatcher)r',
                            dispatcher=dispatcher)
                        dispatcher.handle_read_event()
                        if not dispatcher.readable():
                            self.options.poller.unregister_readable(fd)
                    except asyncore.ExitNow:
                        raise
                    except:
                        combined_map[fd].handle_error()
            # 依次遍历注册的文件句柄
            for fd in w:
                if fd in combined_map:
                    try:
                        dispatcher = combined_map[fd]
                        self.options.logger.blather(
                            'write event caused by %(dispatcher)r',
                            dispatcher=dispatcher)
                        dispatcher.handle_write_event()
                        if not dispatcher.writable():
                            self.options.poller.unregister_writable(fd)
                    except asyncore.ExitNow:
                        raise
                    except:
                        combined_map[fd].handle_error()

            for group in pgroups:
                group.transition()

            # 获取已经死亡的子进程信息
            self.reap()
            # 处理信号
            self.handle_signal()
            
            # tick时钟
            self.tick()

            if self.options.mood < SupervisorStates.RUNNING:
                self.ordered_stop_groups_phase_2()

            if self.options.test:
                break

```
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/kJHHD0.jpg)


### 管理process

下面详细介绍一下如何管理一个process：
下面图可以指导主要是



```python

    def add_process_group(self, config):
        name = config.name
        if name not in self.process_groups:
            config.after_setuid()
            
            # 根据初始化后的配置文件生成相应的子进程实例
            self.process_groups[name] = config.make_group()
            # 添加事件通知
            events.notify(events.ProcessGroupAddedEvent(name))
            return True
        return False
      
```
在supervisor配置中，我们需要写上执行的文件以及执行环境env. 所以，我们围绕 `执行这个程序的逻辑就可以了`
```python
    def execve(self, filename, argv, env):
        return os.execve(filename, argv, env)
```

作者将所有的process或event的配置 都绑定到 `options`的对象上， 包括执行的程序以及各种状态， 然后一个deamon程序一直在运行，去实时检查配置是否发生变化了。做对应的操作。



### supervisorclt
这个客户端功能：
1. 需要有一个UI,实现是cli
2. 与server通信

```python
def main(args=None, options=None):
    if options is None:
       # 实例化对象
        options = ClientOptions()

    options.realize(args, doc=__doc__)
    # 控制器
    c = Controller(options)

    if options.args:
        c.onecmd(" ".join(options.args))
        sys.exit(c.exitstatus)
    
    # 如果是交互模式
    if options.interactive:
        # 一直loop, 图形编程常见操作模式
        c.exec_cmdloop(args, options)
        sys.exit(0)  # exitstatus always 0 for interactive mode
```

我们看交互式的逻辑，`exec_cmdloop`
```python
    # 控制器的method
    def exec_cmdloop(self, args, options):
        try:
            import readline
            delims = readline.get_completer_delims()
            delims = delims.replace(':', '')  # "group:process" as one word
            delims = delims.replace('*', '')  # "group:*" as one word
            delims = delims.replace('-', '')  # names with "-" as one word
            readline.set_completer_delims(delims)

            if options.history_file:
                try:
                    readline.read_history_file(options.history_file)
                except IOError:
                    pass

                def save():
                    try:
                        readline.write_history_file(options.history_file)
                    except IOError:
                        pass

                import atexit
                atexit.register(save)
        except ImportError:
            pass
        try:
            self.cmdqueue.append('status')
            # cmdloop
            self.cmdloop()
        except KeyboardInterrupt:
            self.output('')
            pass
       
    # cmdloop method定义  
    def cmdloop(self, intro=None):
        """Repeatedly issue a prompt, accept input, parse an initial prefix
        off the received input, and dispatch to action methods, passing them
        the remainder of the line as argument.

        """

        self.preloop()
        # 输入和快捷键
        if self.use_rawinput and self.completekey:
            try:
                import readline
                self.old_completer = readline.get_completer()
                readline.set_completer(self.complete)
                readline.parse_and_bind(self.completekey+": complete")
            except ImportError:
                pass
        try:
            if intro is not None:
                self.intro = intro
            if self.intro:
                self.stdout.write(str(self.intro)+"\n")
            stop = None
            while not stop:
                if self.cmdqueue:
                    line = self.cmdqueue.pop(0)
                else:
                    if self.use_rawinput:
                        try:
                            line = input(self.prompt)
                        except EOFError:
                            line = 'EOF'
                    else:
                        self.stdout.write(self.prompt)
                        self.stdout.flush()
                        line = self.stdin.readline()
                        if not len(line):
                            line = 'EOF'
                        else:
                            line = line.rstrip('\r\n')
                line = self.precmd(line)
                stop = self.onecmd(line)
                stop = self.postcmd(stop, line)
            self.postloop()
        finally:
            if self.use_rawinput and self.completekey:
                try:
                    import readline
                    readline.set_completer(self.old_completer)
                except ImportError:
                    pass
            
```

上面执行又会执行到`onecmd`

```python
    def onecmd(self, line):
        """Interpret the argument as though it had been typed in response
        to the prompt.

        This may be overridden, but should not normally need to be;
        see the precmd() and postcmd() methods for useful execution hooks.
        The return value is a flag indicating whether interpretation of
        commands by the interpreter should stop.

        """
        cmd, arg, line = self.parseline(line)
        if not line:
            return self.emptyline()
        if cmd is None:
            return self.default(line)
        self.lastcmd = line
        if line == 'EOF' :
            self.lastcmd = ''
        if cmd == '':
            return self.default(line)
        else:
            try:
                func = getattr(self, 'do_' + cmd)
            except AttributeError:
                return self.default(line)
            return func(arg)
```
可以看出 会返回一个`func = getattr(self, 'do_' + cmd)`,函数调用，是以`do_开头的函数`
在supervisorctl的源码文件中， 我们找一个示例
```python
    def do_start(self, arg):
        if not self.ctl.upcheck():
            return

        names = arg.split()
        # 连接supervisor服务的代理
        supervisor = self.ctl.get_supervisor()

        if not names:
            self.ctl.output("Error: start requires a process name")
            self.ctl.exitstatus = LSBInitExitStatuses.INVALID_ARGS
            self.help_start()
            return
        
        # 开启所有
        if 'all' in names:
            # 调用startAllProcesses函数
            results = supervisor.startAllProcesses()
            for result in results:
                self.ctl.output(self._startresult(result))
                self.ctl.set_exitstatus_from_xmlrpc_fault(result['status'], xmlrpc.Faults.ALREADY_STARTED)
        else:
            # 逐个关闭
            for name in names:
                group_name, process_name = split_namespec(name)
                if process_name is None:
                    try:
                        results = supervisor.startProcessGroup(group_name)
                        for result in results:
                            self.ctl.output(self._startresult(result))
                            self.ctl.set_exitstatus_from_xmlrpc_fault(result['status'], xmlrpc.Faults.ALREADY_STARTED)
                    except xmlrpclib.Fault as e:
                        if e.faultCode == xmlrpc.Faults.BAD_NAME:
                            error = "%s: ERROR (no such group)" % group_name
                            self.ctl.output(error)
                            self.ctl.exitstatus = LSBInitExitStatuses.INVALID_ARGS
                        else:
                            self.ctl.exitstatus = LSBInitExitStatuses.GENERIC
                            raise
                else:
                    try:
                        result = supervisor.startProcess(name)
                    except xmlrpclib.Fault as e:
                        error = {'status': e.faultCode,
                                  'name': process_name,
                                  'group': group_name,
                                  'description': e.faultString}
                        self.ctl.output(self._startresult(error))
                        self.ctl.set_exitstatus_from_xmlrpc_fault(error['status'], xmlrpc.Faults.ALREADY_STARTED)
                    else:
                        name = make_namespec(group_name, process_name)
                        self.ctl.output('%s: started' % name)



```
上面比较重要的就是远程通过rpc调用
```python
class SupervisorNamespaceRPCInterface:
    ....
    def startProcessGroup(self, name, wait=True):
        """ Start all processes in the group named 'name'

        @param string name     The group name
        @param boolean wait    Wait for each process to be fully started
        @return array result   An array of process status info structs
        """
        self._update('startProcessGroup')

        group = self.supervisord.process_groups.get(name)

        if group is None:
            raise RPCError(Faults.BAD_NAME, name)

        processes = list(group.processes.values())
        processes.sort()
        processes = [ (group, process) for process in processes ]

        startall = make_allfunc(processes, isNotRunning, self.startProcess,
                                wait=wait)

        startall.delay = 0.05
        startall.rpcinterface = self
        return startall # deferred


```


### web UI

```python
def make_http_servers(options, supervisord):
    from supervisor.web import supervisor_ui_handler
    uihandler = supervisor_ui_handler(supervisord)
    
class supervisor_ui_handler:
    IDENT = 'Supervisor Web UI HTTP Request Handler'

    def __init__(self, supervisord):
        self.supervisord = supervisord

    def match(self, request):
        if request.command not in ('POST', 'GET'):
            return False

        path, params, query, fragment = request.split_uri()

        while path.startswith('/'):
            path = path[1:]

        if not path:
            path = 'index.html'

        for viewname in VIEWS.keys():
            if viewname == path:
                return True

    def handle_request(self, request):
        if request.command == 'POST':
            request.collector = collector(self, request)
        else:
            self.continue_request('', request)

    def continue_request (self, data, request):
        form = {}
        cgi_env = request.cgi_environment()
        form.update(cgi_env)
        if 'QUERY_STRING' not in form:
            form['QUERY_STRING'] = ''

        query = form['QUERY_STRING']

        # we only handle x-www-form-urlencoded values from POSTs
        form_urlencoded = parse_qsl(data)
        query_data = parse_qs(query)

        for k, v in query_data.items():
            # ignore dupes
            form[k] = v[0]

        for k, v in form_urlencoded:
            # ignore dupes
            form[k] = v

        form['SERVER_URL'] = request.get_server_url()

        path = form['PATH_INFO']
        # strip off all leading slashes
        while path and path[0] == '/':
            path = path[1:]
        if not path:
            path = 'index.html'

        viewinfo = VIEWS.get(path)
        if viewinfo is None:
            # this should never happen if our match method works
            return

        response = {'headers': {}}

        viewclass = viewinfo['view']
        viewtemplate = viewinfo['template']
        context = ViewContext(template=viewtemplate,
                              request = request,
                              form = form,
                              response = response,
                              supervisord=self.supervisord)
        view = viewclass(context)
        pushproducer = request.channel.push_with_producer
        pushproducer(DeferredWebProducer(request, view))
    

```


### RPC
```python
class supervisor_xmlrpc_handler(xmlrpc_handler):
    path = '/RPC2'
    IDENT = 'Supervisor XML-RPC Handler'

    unmarshallers = {
        "int": lambda x: int(x.text),
        "i4": lambda x: int(x.text),
        "boolean": lambda x: x.text == "1",
        "string": lambda x: x.text or "",
        "double": lambda x: float(x.text),
        "dateTime.iso8601": lambda x: make_datetime(x.text),
        "array": lambda x: x[0].text,
        "data": lambda x: [v.text for v in x],
        "struct": lambda x: dict([(k.text or "", v.text) for k, v in x]),
        "base64": lambda x: as_string(decodestring(as_bytes(x.text or ""))),
        "param": lambda x: x[0].text,
    }

    def __init__(self, supervisord, subinterfaces):
        self.rpcinterface = RootRPCInterface(subinterfaces)
        self.supervisord = supervisord

    def loads(self, data):
        params = method = None
        for action, elem in iterparse(StringIO(data)):
            unmarshall = self.unmarshallers.get(elem.tag)
            if unmarshall:
                data = unmarshall(elem)
                elem.clear()
                elem.text = data
            elif elem.tag == "value":
                try:
                    data = elem[0].text
                except IndexError:
                    data = elem.text or ""
                elem.clear()
                elem.text = data
            elif elem.tag == "methodName":
                method = elem.text
            elif elem.tag == "params":
                params = tuple([v.text for v in elem])
        return params, method

    def match(self, request):
        return request.uri.startswith(self.path)

    def continue_request(self, data, request):
        logger = self.supervisord.options.logger

        try:
            try:
                # on 2.x, the Expat parser doesn't like Unicode which actually
                # contains non-ASCII characters. It's a bit of a kludge to
                # do it conditionally here, but it's down to how underlying
                # libs behave
                if PY2:
                    data = data.encode('ascii', 'xmlcharrefreplace')
                params, method = self.loads(data)
            except:
                logger.error(
                    'XML-RPC request data %r is invalid: unmarshallable' %
                    (data,)
                )
                request.error(400)
                return

            # no <methodName> in the request or name is an empty string
            if not method:
                logger.error(
                    'XML-RPC request data %r is invalid: no method name' %
                    (data,)
                )
                request.error(400)
                return

            # we allow xml-rpc clients that do not send empty <params>
            # when there are no parameters for the method call
            if params is None:
                params = ()

            try:
                logger.trace('XML-RPC method called: %s()' % method)
                value = self.call(method, params)
                logger.trace('XML-RPC method %s() returned successfully' %
                             method)
            except RPCError as err:
                # turn RPCError reported by method into a Fault instance
                value = xmlrpclib.Fault(err.code, err.text)
                logger.trace('XML-RPC method %s() returned fault: [%d] %s' % (
                    method,
                    err.code, err.text))

            if isinstance(value, types.FunctionType):
                # returning a function from an RPC method implies that
                # this needs to be a deferred response (it needs to block).
                pushproducer = request.channel.push_with_producer
                pushproducer(DeferredXMLRPCResponse(request, value))

            else:
                # if we get anything but a function, it implies that this
                # response doesn't need to be deferred, we can service it
                # right away.
                body = as_bytes(xmlrpc_marshal(value))
                request['Content-Type'] = 'text/xml'
                request['Content-Length'] = len(body)
                request.push(body)
                request.done()

        except:
            tb = traceback.format_exc()
            logger.critical(
                "Handling XML-RPC request with data %r raised an unexpected "
                "exception: %s" % (data, tb)
            )
            # internal error, report as HTTP server error
            request.error(500)

    def call(self, method, params):
        return traverse(self.rpcinterface, method, params)
```
![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Hwfzfb.jpg)


## 总结
python是解释型语言 弱类型，看源码没有编译型语言清晰明了。这个源码不太容易看，我参考supervisor的设计写公司项目，一开始是看这个源码，发现很不容易理清楚，我是先看了go语言版的，然后在回头看python版的才看明白。可以直接看go版本的，那个更加清晰。
