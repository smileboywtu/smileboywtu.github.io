---
layout: post
title:  monkey patch in python
date:   2016-04-05 19:52:00 +0800
categories: articles
---

 It's simply the dynamic replacement of attributes at runtime.

 sometime you use a library from the others installed from pypi, you may not satisfied with function it defines, that's  say you want more than it supplies. so you can create your own package but it's not really good idea, you make the software larger and larger. so good idea is to let the user installed it from online and you just supply the patches to make it specific for your software.

 in python the module will only be imported once.

 1. import a module
 2. add to sys.modules dict
 3. from sys.modules dict to globals

 you can just do a simple replacement by:

 {% highlight python %}

 import json

 def dumps(dict):
    pass

 json.dumps = dumps()

 {% endhighlight %}

 a more good way according to the lib eventlet:

 simple code:

 {% highlight python %}

 from eventlet import patcher  

 # *NOTE: there might be some funny business with the "SOCKS" module  
 # if it even still exists  
 from eventlet.green import socket  

 patcher.inject('ftplib', globals(), ('socket', socket))  

 del patcher  

 {% endhighlight %}

 monkey patch:

 {% highlight python %}

 __exclude = set(('__builtins__', '__file__', '__name__'))  

def inject(module_name, new_globals, *additional_modules):  
    """Base method for "injecting" greened modules into an imported module.  It
    imports the module specified in *module_name*, arranging things so
    that the already-imported modules in *additional_modules* are used when
    *module_name* makes its imports.

    *new_globals* is either None or a globals dictionary that gets populated
    with the contents of the *module_name* module.  This is useful when creating
    a "green" version of some other module.

    *additional_modules* should be a collection of two-element tuples, of the
    form (, ).  If it's not specified, a default selection of
    name/module pairs is used, which should cover all use cases but may be
    slower because there are inevitably redundant or unnecessary imports.
    """  
    if not additional_modules:  
        # supply some defaults  
        additional_modules = (  
            _green_os_modules() +  
            _green_select_modules() +  
            _green_socket_modules() +  
            _green_thread_modules() +  
            _green_time_modules())  

    ## Put the specified modules in sys.modules for the duration of the import  
    saved = {}  
    for name, mod in additional_modules:  
        saved[name] = sys.modules.get(name, None)  
        sys.modules[name] = mod  

    ## Remove the old module from sys.modules and reimport it while  
    ## the specified modules are in place  
    old_module = sys.modules.pop(module_name, None)  
    try:  
        module = __import__(module_name, {}, {}, module_name.split('.')[:-1])  

        if new_globals is not None:  
            ## Update the given globals dictionary with everything from this new module  
            for name in dir(module):  
                if name not in __exclude:  
                    new_globals[name] = getattr(module, name)  

        ## Keep a reference to the new module to prevent it from dying  
        sys.modules['__patched_module_' + module_name] = module  
    finally:  
        ## Put the original module back  
        if old_module is not None:  
            sys.modules[module_name] = old_module  
        elif module_name in sys.modules:  
            del sys.modules[module_name]  

        ## Put all the saved modules back  
        for name, mod in additional_modules:  
            if saved[name] is not None:  
                sys.modules[name] = saved[name]  
            else:  
                del sys.modules[name]  

    return module  

  {% endhighlight %}
