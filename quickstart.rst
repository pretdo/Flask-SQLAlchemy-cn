.. _quickstart:

快速入门
==========

.. currentmodule:: flask.ext.sqlalchemy

Flask-SQLAlchemy 使用起来非常有趣，对于基本应用十分容易使用，并且对于大型项目易于扩展。有关完整的指南，请参阅 :class:`SQLAlchemy` 的 API 文档。

一个最小应用
---------------

常见情况下对于只有一个 Flask 应用，所有你需要做的事情就是创建 Flask 应用，选择加载配置接着创建 :class:`SQLAlchemy` 对象时候把 Flask 应用传递给它作为参数。

一旦创建，这个对象就包含 :mod:`sqlalchemy` 和 :mod:`sqlalchemy.orm` 中的所有函数和助手。此外它还提供一个名为 `Model` 的类，用于作为声明 模型时的 delarative 基类::

    from flask import Flask
    from flask.ext.sqlalchemy import SQLAlchemy

    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:////tmp/test.db'
    db = SQLAlchemy(app)


    class User(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        username = db.Column(db.String(80), unique=True)
        email = db.Column(db.String(120), unique=True)

        def __init__(self, username, email):
            self.username = username
            self.email = email

        def __repr__(self):
            return '<User %r>' % self.username

为了创建初始数据库，只需要从交互式 Python shell 中导入 `db` 对象并且调用 :meth:`SQLAlchemy.create_all` 方法来创建表和数据库
To create the initial database, just import the `db` object from an
interactive Python shell and run the
:meth:`SQLAlchemy.create_all` method to create the
tables and database:

>>> from yourapplication import db
>>> db.create_all()

Boom, and there is your database.  Now to create some users:

>>> from yourapplication import User
>>> admin = User('admin', 'admin@example.com')
>>> guest = User('guest', 'guest@example.com')

But they are not yet in the database, so let's make sure they are:

>>> db.session.add(admin)
>>> db.session.add(guest)
>>> db.session.commit()

Accessing the data in database is easy as a pie:

>>> users = User.query.all()
[<User u'admin'>, <User u'guest'>]
>>> admin = User.query.filter_by(username='admin').first()
<User u'admin'>

简单的关系
--------------------

SQLAlchemy connects to relational databases and what relational databases
are really good at are relations.  As such, we shall have an example of an
application that uses two tables that have a relationship to each other::


    from datetime import datetime


    class Post(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        title = db.Column(db.String(80))
        body = db.Column(db.Text)
        pub_date = db.Column(db.DateTime)

        category_id = db.Column(db.Integer, db.ForeignKey('category.id'))
        category = db.relationship('Category',
            backref=db.backref('posts', lazy='dynamic'))

        def __init__(self, title, body, category, pub_date=None):
            self.title = title
            self.body = body
            if pub_date is None:
                pub_date = datetime.utcnow()
            self.pub_date = pub_date
            self.category = category

        def __repr__(self):
            return '<Post %r>' % self.title


    class Category(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        name = db.Column(db.String(50))

        def __init__(self, name):
            self.name = name

        def __repr__(self):
            return '<Category %r>' % self.name

First let's create some objects:

>>> py = Category('Python')
>>> p = Post('Hello Python!', 'Python is pretty cool', py)
>>> db.session.add(py)
>>> db.session.add(p)

Now because we declared `posts` as dynamic relationship in the backref
it shows up as query:

>>> py.posts
<sqlalchemy.orm.dynamic.AppenderBaseQuery object at 0x1027d37d0>

It behaves like a regular query object so we can ask it for all posts that
are associated with our test “Python” category:

>>> py.posts.all()
[<Post 'Hello Python!'>]


启蒙之路
---------------------

The only things you need to know compared to plain SQLAlchemy are:

1.  :class:`SQLAlchemy` gives you access to the following things:

    -   all the functions and classes from :mod:`sqlalchemy` and
        :mod:`sqlalchemy.orm`
    -   a preconfigured scoped session called `session`
    -   the :attr:`~SQLAlchemy.metadata`
    -   the :attr:`~SQLAlchemy.engine`
    -   a :meth:`SQLAlchemy.create_all` and :meth:`SQLAlchemy.drop_all`
        methods to create and drop tables according to the models.
    -   a :class:`Model` baseclass that is a configured declarative base.

2.  The :class:`Model` declarative base class behaves like a regular
    Python class but has a `query` attribute attached that can be used to
    query the model.  (:class:`Model` and :class:`BaseQuery`)

3.  You have to commit the session, but you don't have to remove it at
    the end of the request, Flask-SQLAlchemy does that for you.
