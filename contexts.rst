.. _contexts:

.. currentmodule:: flask.ext.sqlalchemy

引入上下文
==========================

如果您计划只使用一个应用程序，您大可跳过这一章节。只需要把您的应用程序传给 :class:`SQLAlchemy` 构造函数，一般情况下您就设置好了。然而您想要使用不止一个应用或者在一个函数中动态地创建应用的话，您需要仔细阅读。

如果您在一个函数中定义您的应用，但是 :class:`SQLAlchemy` 对象是全局的，后者如何知道前者了？答案就是 :meth:`~SQLAlchemy.init_app` 函数::

    from flask import Flask
    from flask.ext.sqlalchemy import SQLAlchemy

    db = SQLAlchemy()

    def create_app():
        app = Flask(__name__)
        db.init_app(app)
        return app


What it does is prepare the application to work with
:class:`SQLAlchemy`.  However that does not now bind the
:class:`SQLAlchemy` object to your application.  Why doesn't it do that?
Because there might be more than one application created.

So how does :class:`SQLAlchemy` now really know about your application?
You will have to setup an application context.  If you are working inside
a Flask view function, that automatically happens.  However if you are
working inside the interactive shell, you will have to do that yourself
(see `Creating an Application Context
<http://flask.pocoo.org/docs/appcontext/#creating-an-application-context>`_).

In a nutshell, do something like this:

>>> from yourapp import create_app
>>> app = create_app()
>>> app.app_context().push()

Inside scripts it makes also sense to use the with-statement::

    def my_function():
        with app.app_context():
            user = db.User(...)
            db.session.add(user)
            db.session.commit()

Some functions inside Flask-SQLAlchemy also accept optionally the
application to operate on:

>>> from yourapp import db, create_app
>>> db.create_all(app=create_app())
