信号支持
==================

.. versionadded:: 0.10

从 Flask-SQLALchemy 0.10 开始，你可以订阅信号来获取通知当发生某种特定的事件。

存在以下两个信号:

.. data:: models_committed

   This signal is sent when changed models were committed to the
   database.  The sender is the application that emitted the changes
   and the models and an operation identifier are passed as list of tuples
   in the form ``(model, operation)`` to the receiver in the `changes`
   parameter.

   The model is the instance of the model that was sent to the database
   and the operation is ``'insert'`` when a model was inserted,
   ``'delete'`` when the model was deleted and ``'update'`` if any
   of the columns where updated.

.. data:: before_models_committed

   Works exactly the same as :data:`models_committed` but is emitted
   right before the committing takes place.
