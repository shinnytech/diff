.. _notify:

通知
==================================================
通知通过 `rtn_data` 包中的 notify 字段发送, 如下所示

.. code-block:: javascript

  {
    "aid": "rtn_data",                                        //数据推送
    "data": [                                                 //diff数据数组, 一次推送中可能含有多个数据包
      {
        "notify": {                                           //通知信息
          "2010": {
            "type": "MESSAGE",                                //MESSAGE TEXT
            "code": 1000,
            "content": "abcd",
          }
        },
      }
    ]
  }

