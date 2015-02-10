信号支持
==================

.. versionadded:: 0.10

从 Flask-SQLALchemy 0.10 开始，您可以订阅信号来获取通知当发生某种特定的事件。

存在以下两个信号:

.. data:: models_committed

   这个信号在修改的模型提交到数据库时发出。发送者是发送修改的应用，模型和操作描述符以 ``(model, operation)`` 形式作为元组，这样的元组列表传递给接受者的 `changes` 参数。

   该模型是发送到数据库的模型实例，当一个模型已经插入，操作是 ``'insert'`` ，而已删除是 ``'delete'`` ，如果更新了任何列，会是 ``'update'`` 。

.. data:: before_models_committed

   工作机制和 :data:`models_committed` 完全一样，但是在提交之前发送。
