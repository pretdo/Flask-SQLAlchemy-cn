配置
=============

下面是 Flask-SQLAlchemy 中存在的配置值。Flask-SQLAlchemy 从你的 Flask 主配置中加载这些值。 注意其中的一些在引擎创建后不能修改，所以确保尽早配置且不在运行时修改它们。

配置键
------------------

Flask-SQLAlchemy 扩展能够识别的配置键的清单:

.. tabularcolumns:: |p{6.5cm}|p{8.5cm}|

================================== =========================================
``SQLALCHEMY_DATABASE_URI``        用于连接数据的数据库。例如：

                                   - ``sqlite:////tmp/test.db``
                                   - ``mysql://username:password@server/db``
``SQLALCHEMY_BINDS``               一个映射绑定 (bind) 键到 
                                   SQLAlchemy 连接 URIs                              的字典。更多的信息请参阅 :ref:`binds`。
``SQLALCHEMY_ECHO``                If set to `True` SQLAlchemy will log all
                                   the statements issued to stderr which can
                                   be useful for debugging.
``SQLALCHEMY_RECORD_QUERIES``      Can be used to explicitly disable or
                                   enable query recording.  Query recording
                                   automatically happens in debug or testing
                                   mode.  See :func:`get_debug_queries` for
                                   more information.
``SQLALCHEMY_NATIVE_UNICODE``      Can be used to explicitly disable native
                                   unicode support.  This is required for
                                   some database adapters (like PostgreSQL
                                   on some Ubuntu versions) when used with
                                   improper database defaults that specify
                                   encoding-less databases.
``SQLALCHEMY_POOL_SIZE``           The size of the database pool.  Defaults
                                   to the engine's default (usually 5)
``SQLALCHEMY_POOL_TIMEOUT``        Specifies the connection timeout for the
                                   pool.  Defaults to 10.
``SQLALCHEMY_POOL_RECYCLE``        Number of seconds after which a
                                   connection is automatically recycled.
                                   This is required for MySQL, which removes
                                   connections after 8 hours idle by
                                   default.  Note that Flask-SQLAlchemy
                                   automatically sets this to 2 hours if
                                   MySQL is used.
``SQLALCHEMY_MAX_OVERFLOW``        Controls the number of connections that
                                   can be created after the pool reached
                                   its maximum size.  When those additional
                                   connections are returned to the pool,
                                   they are disconnected and discarded.
``SQLALCHEMY_TRACK_MODIFICATIONS`` If set to `True` (the default)
                                   Flask-SQLAlchemy will track
                                   modifications of objects and emit
                                   signals.  This requires extra memory
                                   and can be disabled if not needed. 
================================== =========================================

.. versionadded:: 0.8
   增加 ``SQLALCHEMY_NATIVE_UNICODE``, ``SQLALCHEMY_POOL_SIZE``,
   ``SQLALCHEMY_POOL_TIMEOUT`` 和 ``SQLALCHEMY_POOL_RECYCLE`` 配置键。

.. versionadded:: 0.12
   增加 ``SQLALCHEMY_BINDS`` 配置键。

.. versionadded:: 0.17
   增加 ``SQLALCHEMY_MAX_OVERFLOW`` 配置键。

.. versionadded:: 2.0
   增加 ``SQLALCHEMY_TRACK_MODIFICATIONS`` 配置键。

连接 URI 格式
-----------------

For a complete list of connection URIs head over to the SQLAlchemy
documentation under (`Supported Databases
<http://www.sqlalchemy.org/docs/core/engines.html>`_).  This here shows
some common connection strings.

SQLAlchemy indicates the source of an Engine as a URI combined with
optional keyword arguments to specify options for the Engine. The form of
the URI is::

    dialect+driver://username:password@host:port/database

Many of the parts in the string are optional.  If no driver is specified
the default one is selected (make sure to *not* include the ``+`` in that
case).

Postgres::

    postgresql://scott:tiger@localhost/mydatabase

MySQL::

    mysql://scott:tiger@localhost/mydatabase

Oracle::

    oracle://scott:tiger@127.0.0.1:1521/sidname

SQLite (note the four leading slashes)::

    sqlite:////absolute/path/to/foo.db
