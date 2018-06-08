.. _quote:

行情报价数据
==================================================

请求订阅行情报价
--------------------------------------------------
终端通过发送 subscribe_quote 包实现订阅行情报价
  
.. code-block:: javascript

  {
    "aid": "subscribe_quote",       //必填, 请求订阅实时报价数据
    "ins_list": "SHFE.cu1612,CFFEX.IF1701",    //必填, 需要订阅的合约列表，以逗号分隔
  }

  
需要注意几点:

* 合约代码必须带交易所代码, 例如cu1801应该写作 SHFE.cu1801. 目前支持的交易所为 CFFEX, SHFE, DCE, CZCE, INE
* 用户自定义的组合, 交易所代码都为 USER
* 合约代码及交易所代码都是大小写敏感的
* 每次发送 subscribe_quote 时，应在 ins_list 中列出所有需要订阅的合约代码。多次发送 subscribe_quote，后一次的订阅列表会覆盖前一次的


行情报价数据同步
--------------------------------------------------
行情报价数据通过 `rtn_data` 包的 quotes 字段进行差分发送, 如下所示:

.. code-block:: javascript

  {
    "aid": "rtn_data",                                        //数据推送
    "data": [                                                 //diff数据数组, 一次推送中可能含有多个数据包
      {
        "quotes": {                                           //实时行情数据
          "SHFE.cu1612": {
            "instrument_id": "SHFE.cu1612",                   //合约代码
            "volume_multiple": 300,                           //合约乘数
            "price_tick": 0.2,                                //合约价格单位
            "price_decs": 1,                                  //合约价格小数位数
            "max_market_order_volume": 1000,                  //市价单最大下单手数
            "min_market_order_volume": 1,                     //市价单最小下单手数
            "max_limit_order_volume": 1000,                   //限价单最大下单手数
            "min_limit_order_volume": 1,                      //限价单最小下单手数
            "margin": 4480.0,                                 //每手保证金
            "commission": 2.5,                                //每手手续费
            "datetime": "2016-12-30 13:21:32.500000",         //时间
            "ask_price1": 36590.0,                            //卖价
            "ask_volume1": 121,                               //卖量
            "bid_price1": 36580.0,                            //买价
            "bid_volume1": 3,                                 //买量
            "last_price": 36580.0,                            //最新价
            "highest": 36580.0,                               //最高价
            "lowest": 36580.0,                                //最低价
            "amount": 213445312.5,                            //成交额
            "volume": 23344,                                  //成交量
            "open_interest": 23344,                           //持仓量
            "pre_open_interest": 23344,                       //昨持
            "pre_close": 36170.0,                             //昨收
            "open": 36270.0,                                  //今开
            "close": 36270.0,                                 //收盘
            "lower_limit": 34160.0,                           //跌停
            "upper_limit": 38530.0,                           //涨停
            "average": 36270.1,                               //均价
            "pre_settlement": 36270.0,                        //昨结
            "settlement": 36270.0,                            //结算价
          },
        },
      ]
    }
  }
