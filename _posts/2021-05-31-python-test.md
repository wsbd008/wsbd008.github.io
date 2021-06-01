```
├───server
│       server.py
└───worker
        work.py
```
```python
#server.py
import sys
sys.path.append('..')

import logging
from worker.work import hello

logging.basicConfig(filename='myapp.log', format='%(asctime)s %(levelname)s:%(message)s', level=logging.INFO)

def call_hello():
    logging.info('server start')
    txt = hello()
    print('server say {}'.format(txt))
    logging.info('server finished')

call_hello()
```

```python
#worker.py
import logging
def hello():
    logging.info('worker start')
    print('hello\n')
    logging.info('worker end')
    return 'hello'
```
