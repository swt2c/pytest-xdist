pytest-xdist 1.17.1 (2017-06-10)
================================

Bug Fixes
---------

- Hot fix release reverting the change introduced by #124, unfortunately it
  broke a number of test suites so we are reversing this change while we
  investigate the problem. (#157)


Improved Documentation
----------------------

- Introduced ``towncrier`` for ``CHANGELOG`` management. (#154)

- Added ``HOWTORELEASE`` documentation. (#155)


..
    You should *NOT* be adding new change log entries to this file, this
    file is managed by towncrier. You *may* edit previous change logs to
    fix problems like typo corrections or such.
    To add a new change log entry, please see
    https://pip.pypa.io/en/latest/development/#adding-a-news-entry
    We named the news folder ``changelog``

.. towncrier release notes start


1.17.0
------

- fix #124: xdist would mark test as complete after 'call' step. As a result,
  xdist could identify the wrong test as failing when test crashes at teardown.
  To address this issue, xdist now marks test as complete at teardown.

1.16.0
------

- ``pytest-xdist`` now requires pytest 2.7 or later.

- Add ``worker_id`` attribute in the TestReport

- new hook: ``pytest_xdist_make_scheduler(config, log)``, can return custom tests items
  distribution logic implementation. You can take a look at built-in ``LoadScheduling``
  and ``EachScheduling`` implementations. Note that required scheduler class public
  API may change in next ``pytest-xdist`` versions.

1.15.0
------

- new ``worker_id`` fixture, returns the id of the worker in a test or fixture.
  Thanks Jared Hellman for the PR.

- display progress during collection only when in a terminal, similar to pytest #1397 issue.
  Thanks Bruno Oliveira for the PR.

- fix internal error message when ``--maxfail`` is used (#62, #65).
  Thanks Collin RM Stocks and Bryan A. Jones for reports and Bruno Oliveira for the PR.


1.14
----

- new hook: ``pytest_xdist_node_collection_finished(node, ids)``, called when
  a worker has finished collection. Thanks Omer Katz for the request and
  Bruno Oliveira for the PR.

- fix README display on pypi

- fix #22: xdist now works if the internal tmpdir plugin is disabled.
  Thanks Bruno Oliveira for the PR.

- fix #32: xdist now works if looponfail or boxed are disabled.
  Thanks Bruno Oliveira for the PR.


1.13.1
-------

- fix a regression -n 0 now disables xdist again


1.13
-------------------------

- extended the tox matrix with the supported py.test versions

- split up the plugin into 3 plugin's
  to prepare the departure of boxed and looponfail.

  looponfail will be a part of core
  and forked boxed will be replaced
  with a more reliable primitive based on xdist

- conforming with new pytest-2.8 behavior of returning non-zero when all
  tests were skipped or deselected.

- new "--max-slave-restart" option that can be used to control maximum
  number of times pytest-xdist can restart slaves due to crashes. Thanks to
  Anatoly Bubenkov for the report and Bruno Oliveira for the PR.

- release as wheel

- "-n" option now can be set to "auto" for automatic detection of number
  of cpus in the host system. Thanks Suloev Dmitry for the PR.

1.12
-------------------------

- fix issue594: properly report errors when the test collection
  is random.  Thanks Bruno Oliveira.

- some internal test suite adaptation (to become forward
  compatible with the upcoming pytest-2.8)


1.11
-------------------------

- fix pytest/xdist issue485 (also depends on py-1.4.22):
  attach stdout/stderr on --boxed processes that die.

- fix pytest/xdist issue503: make sure that a node has usually
  two items to execute to avoid scoped fixtures to be torn down
  pre-maturely (fixture teardown/setup is "nextitem" sensitive).
  Thanks to Andreas Pelme for bug analysis and failing test.

- restart crashed nodes by internally refactoring setup handling
  of nodes.  Also includes better code documentation.
  Many thanks to Floris Bruynooghe for the complete PR.


1.10
-------------------------

- add glob support for rsyncignores, add command line option to pass
  additional rsyncignores. Thanks Anatoly Bubenkov.

- fix pytest issue382 - produce "pytest_runtest_logstart" event again
  in master. Thanks Aron Curzon.

- fix pytest issue419 by sending/receiving indices into the test
  collection instead of node ids (which are not neccessarily unique
  for functions parametrized with duplicate values)

- send multiple "to test" indices in one network message to a slave
  and improve heuristics for sending chunks where the chunksize
  depends on the number of remaining tests rather than fixed numbers.
  This reduces the number of master -> node messages (but not the
  reverse direction)


1.9
-------------------------

- changed LICENSE to MIT

- fix duplicate reported test ids with --looponfailing
  (thanks Jeremy Thurgood)

- fix pytest issue41: re-run tests on all file changes, not just
  randomly select ones like .py/.c.

- fix pytest issue347: slaves running on top of Python3.2
  will set PYTHONDONTWRITEYBTECODE to 1 to avoid import concurrency
  bugs.

1.8
-------------------------

- fix pytest-issue93 - use the refined pytest-2.2.1 runtestprotocol
  interface to perform eager teardowns for test items.

1.7
-------------------------

- fix incompatibilities with pytest-2.2.0 (allow multiple
  pytest_runtest_logreport reports for a test item)

1.6
-------------------------

- terser collection reporting

- fix issue34 - distributed testing with -p plugin now works correctly

- fix race condition in looponfail mode where a concurrent file removal
  could cause a crash

1.5
-------------------------

- adapt to and require pytest-2.0 changes, rsyncdirs and rsyncignore can now
  only be specified in [pytest] sections of ini files, see "py.test -h"
  for details.
- major internal refactoring to match the pytest-2.0 event refactoring
  - perform test collection always at slave side instead of at the master
  - make python2/python3 bridging work, remove usage of pickling
- improve initial reporting by using line-rewriting
- remove all trailing whitespace from source

1.4
-------------------------

- perform distributed testing related reporting in the plugin
  rather than having dist-related code in the generic py.test
  distribution

- depend on execnet-1.0.7 which adds "env1:NAME=value" keys to
  gateway specification strings.

- show detailed gateway setup and platform information only when
  "-v" or "--verbose" is specified.

1.3
-------------------------

- fix --looponfailing - it would not actually run against the fully changed
  source tree when initial conftest files load application state.

- adapt for py-1.3.1's new --maxfailure option

1.2
-------------------------

- fix issue79: sessionfinish/teardown hooks are now called systematically
  on the slave side
- introduce a new data input/output mechanism to allow the master side
  to send and receive data from a slave.
- fix race condition in underlying pickling/unpickling handling
- use and require new register hooks facility of py.test>=1.3.0
- require improved execnet>=1.0.6 because of various race conditions
  that can arise in xdist testing modes.
- fix some python3 related pickling related race conditions
- fix PyPI description

1.1
-------------------------

- fix an indefinite hang which would wait for events although no events
  are pending - this happened if items arrive very quickly while
  the "reschedule-event" tried unconditionally avoiding a busy-loop
  and not schedule new work.

1.0
-------------------------

- moved code out of py-1.1.1 into its own plugin
- use a new, faster and more sensible model to do load-balancing
  of tests - now no magic "MAXITEMSPERHOST" is needed and load-testing
  works effectively even with very few tests.
- cleaned up termination handling
- make -x cause hard killing of test nodes to decrease wait time
  until the traceback shows up on first failure
