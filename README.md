# reproduce-fiftyone-briefcase-bug

### System information

-   Windows 11 64x 
-   Python 3.11.5
-   FiftyOne v0.22.2, Voxel51, Inc.
-   pip

### Prerequisites

- python environment with briefcase installed
``` pip install briefcase```


### Commands to reproduce

```
git clone https://github.com/EytanDn/reproduce-fiftyone-briefcase-bug.git
cd reproduce-fiftyone-briefcase-bug/helloworld
```
#### verify it works in dev mode:
should work here
```
briefcase dev
```
#### reproduce from build:
1) run:
```
briefcase build
explorer build\helloworld\windows\app\src  # to open the folder in windows
```
2) double click the `Hello World.exe`
#### reproduce from package:
1) run:
```
briefcase package
explorer dist
```
2) double click the .msi installer and install
3) open the installed Hello World app

### Describe the problem
When running the application using briefcase dev, everything operates as expected on Windows. However, after packaging the application with Briefcase, launching the Windows executable triggers a ServiceListenTimeout error. This error occurs specifically when FiftyOne attempts to initialize its MongoDB database service.

The issue seems confined to the packaged Windows application; everything works fine in the development environment and on a packaged Linux build.

Expected Behavior:
FiftyOne should be able to initialize its MongoDB service in the packaged Windows application without encountering a port binding error.

Actual Behavior:
Upon launching the packaged Windows application, it fails to start and throws a ServiceListenTimeout error, preventing FiftyOne's MongoDB service from initializing

### Code to reproduce issue

notes above

### Other info/logs

```
...
    import fiftyone.core.odm as foo
  File "\app_packages\fiftyone\__init__.py", line 25, in <module>
    from fiftyone.__public__ import *
  File "\app_packages\fiftyone\__public__.py", line 15, in <module>
    _foo.establish_db_conn(config)
  File "\app_packages\fiftyone\core\odm\database.py", line 200, in establish_db_conn
    port = _db_service.port
           ^^^^^^^^^^^^^^^^
  File "\app_packages\fiftyone\core\service.py", line 276, in port
    return self._wait_for_child_port()
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "\app_packages\fiftyone\core\service.py", line 170, in _wait_for_child_port
    return find_port()
           ^^^^^^^^^^^
  File "\app_packages\retrying.py", line 56, in wrapped_f
    return Retrying(*dargs, **dkw).call(f, *args, **kw)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "\app_packages\retrying.py", line 266, in call
    raise attempt.get()
          ^^^^^^^^^^^^^
  File "\app_packages\retrying.py", line 301, in get
    six.reraise(self.value[0], self.value[1], self.value[2])
  File "\app_packages\six.py", line 719, in reraise
    raise value
  File "\app_packages\retrying.py", line 251, in call
    attempt = Attempt(fn(*args, **kwargs), attempt_number, False)
                      ^^^^^^^^^^^^^^^^^^^
  File "\app_packages\fiftyone\core\service.py", line 168, in find_port
    raise ServiceListenTimeout(etau.get_class_name(self), port)
fiftyone.core.service.ServiceListenTimeout: fiftyone.core.service.DatabaseService failed to bind to port
```
### What areas of FiftyOne does this bug affect?

-   [ X ] `App`: FiftyOne application issue
-   [ ] `Core`: Core Python library issue
-   [ ] `Server`: FiftyOne server issue

### Willingness to contribute

-   [ ] Yes. I can contribute a fix for this bug independently
-   [ X ] Yes. I would be willing to contribute a fix for this bug with guidance from the FiftyOne community
-   [ ] No. I cannot contribute a bug fix at this time
