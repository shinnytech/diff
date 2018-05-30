.. _unregister_indicator_class:

unregister_indicator_class
=======================================
扩展进程 -> 主进程，删除自定义技术指标类

Example
--------------------------------------------------
.. code-block:: javascript

    {
        "aid": "unregister_indicator_class",    //必填, 请求删除自定义技术指标类
        "name": "MACD",                         //必填, 指标名称，与 register_indicator_class 中指定的一致
    }

    
Remarks
--------------------------------------------------
