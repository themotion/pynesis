pynesis [![CircleCI](https://circleci.com/gh/ticketea/pynesis.svg?style=svg&circle-token=7c5486e8508438ca0b70ef3d795c814d71ef91f4)](https://circleci.com/gh/ticketea/pynesis)
=======

High level kinesis client. Support python 2.7 and 3.6, and has
helpers for using it within Django.


Some features:
    
* Supports python 2 & 3
* Django helpers included
* Automatically detects shard count changes
* Checkpoints/sequences persistence can be customized
* Provided Checkpointer implementations for memory, django model and redis
* Provided Dummy kinesis implementation for development/testing

Some limitations:

* Single threaded/sequential. It will read from all shards in a
round-robin fashion


Installation
-----------

Install the latest stable version from Pypi with `pip install pynesis`


Usage
-----

```python
from pynesis.backends import KinesisBackend

stream = KinesisBackend("my-stream", region_name="eu-west-2")

for record in stream.read():
    print(record)

```

Now persisting the sequences:

```python
from pynesis.backends import KinesisBackend
from pynesis.checkpointers import RedisCheckpointer

checkpointer = RedisCheckpointer(redis_host="localhost")
stream = KinesisBackend("my-stream", region_name="eu-west-2", checkpointer=checkpointer)

for record in stream.read():
    print(record)

```



See the examples available [here](pynesis/tests/examples_tests.py) for 
more details


Django support
--------------

If you are using django, you can configure your kinesis streams in the standard
django `settings.py` file, see [here](pynesis/tests/examples_tests.py#L54) for an example.

You can use alsto the provided Django model based Checkpointer if
you want to save streams sequences into the database instead of redis, for
using it, just add `pynesis` to `INSTALLED_APPS` and run the provided
migration with `manage.py migrate`.


Development environment
=======================

Run `make shell` and then use the tests/examples.
 

Running tests
=============

To run all tests in all environments and python versions supported, run:
    
    make test
    
    
To run a single test in a single environment, from within a `make shell` run:

    tox -e py36-dj19-redis -- pynesis/tests/examples_tests.py::pynesis/tests/example_test.py::test_simple_reading_example
