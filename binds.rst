.. _binds:

.. currentmodule:: flask.ext.sqlalchemy

绑定多个数据库
=============================

从 0.12 开始，Flask-SQLAlchemy 可以容易地连接到多个数据库。为了实现这个功能，预配置了 SQLAlchemy 来支持多个 “binds”。

什么是绑定(binds)? 在 SQLAlchemy 中一个绑定(bind)是能执行 SQL 语句并且通常是一个连接或者引擎类的东东。在 Flask-SQLAlchemy 中，绑定(bind)总是背后自动为您创建好的引擎。这些引擎中的每个之后都会关联一个短键（bind key）。这个键会在模型声明时使用来把一个模型关联到一个特定引擎。

如果模型没有关联一个特定的引擎的话，就会使用默认的连接(``SQLALCHEMY_DATABASE_URI`` 配置值)。

示例配置
---------------------

下面的配置声明了三个数据库连接。特殊的默认值和另外两个分别名为 `users`（用于用户）和 `appmeta` 连接到一个提供只读访问应用内部数据的 sqlite 数据库）::

    SQLALCHEMY_DATABASE_URI = 'postgres://localhost/main'
    SQLALCHEMY_BINDS = {
        'users':        'mysqldb://localhost/users',
        'appmeta':      'sqlite:////path/to/appmeta.db'
    }

创建和删除表
----------------------------

The :meth:`~SQLAlchemy.create_all` and :meth:`~SQLAlchemy.drop_all` methods
by default operate on all declared binds, including the default one.  This
behavior can be customized by providing the `bind` parameter.  It takes
either a single bind name, ``'__all__'`` to refer to all binds or a list
of binds.  The default bind (``SQLALCHEMY_DATABASE_URI``) is named `None`:

>>> db.create_all()
>>> db.create_all(bind=['users'])
>>> db.create_all(bind='appmeta')
>>> db.drop_all(bind=None)

Referring to Binds
------------------

If you declare a model you can specify the bind to use with the
:attr:`~Model.__bind_key__` attribute::

    class User(db.Model):
        __bind_key__ = 'users'
        id = db.Column(db.Integer, primary_key=True)
        username = db.Column(db.String(80), unique=True)

Internally the bind key is stored in the table's `info` dictionary as
``'bind_key'``.  This is important to know because when you want to create
a table object directly you will have to put it in there::

    user_favorites = db.Table('user_favorites',
        db.Column('user_id', db.Integer, db.ForeignKey('user.id')),
        db.Column('message_id', db.Integer, db.ForeignKey('message.id')),
        info={'bind_key': 'users'}
    )

If you specified the `__bind_key__` on your models you can use them exactly the
way you are used to.  The model connects to the specified database connection 
itself.
