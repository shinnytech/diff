.. _trade:

交易及银期转账
==================================================

交易账户结构
--------------------------------------------------

用户(USER)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
一个用户由一个唯一的 USER_ID 标识. 每个用户的账户信息互相独立. 

在任一时刻, 一个用户的交易账户可以由以下信息完整描述

* 1-N个资金账户(ACCOUNT)
* 0-N个持仓记录(POSITION)
* 0-N个委托单(ORDER)

这些信息完整的描述了用户交易账户的[当前状态]. 需要注意的是, 用户过往的交易记录, 转账记录等并不在其中, 那些信息对于用户的交易动作没有任何影响.


资金账户(ACCOUNT)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
每个资金账户由一个 ACCOUNT_ID 标识. 一个USER可以同时拥有多个ACCOUNT. 每个 ACCOUNT 中的各字段都使用同一币种.

下面是一个ACCOUNT的内容示例:

.. code-block:: javascript

  "CNY": {
    //账号及币种
    "user_id": "423423",                      //用户ID
    "currency": "CNY",                        //币种
    
    //本交易日开盘前状态
    "pre_balance": 12345,                     //上一交易日结算时的账户权益
    
    //本交易日内出入金事件的影响
    "deposit": 42344,                         //本交易日内的入金金额
    "withdraw": 42344,                        //本交易日内的出金金额
    "static_balance": 124895,                 //静态权益 = pre_balance + deposit - withdraw

    //本交易日内已完成交易的影响
    "close_profit": 12345,                    //本交易日内的平仓盈亏
    "commission": 123,                        //本交易日内交纳的手续费
    "premium": 123,                           //本交易日内交纳的期权权利金

    //当前持仓盈亏
    "position_profit": 12345,                 //当前持仓盈亏
    "float_profit": 8910.2,                   //当前浮动盈亏
    
    //当前权益
    "balance": 9963216.55,                    //账户权益 = static_balance + close_profit - commission - premium + position_profit
    
    //保证金占用, 冻结及风险度
    "margin": 11232.23,                       //持仓占用保证金
    "frozen_margin": 12345,                   //挂单冻结保证金
    "frozen_commission": 123,                 //挂单冻结手续费
    "frozen_premium": 123,                    //挂单冻结权利金
    "available": 9480176.150000002,           //可用资金 = balance - margin - frozen_margin - frozen_commission - frozen_premium
    "risk_ratio": 0.048482375,                //风险度 = 1 - available / balance
  }


持仓(POSITION)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
每个持仓项描述一个合约的当前持仓情况. 通常以相应的合约代码(SYMBOL)作为KEY

下面是一个 POSITION 的内容示例:

.. code-block:: javascript

  "SHFE.cu1801":{                             //position_key=symbol
    //交易所和合约代码
    "user_id": "423423",                      //用户ID
    "exchange_id": "SHFE",                    //交易所
    "instrument_id": "cu1801",                //合约在交易所内的代码
    
    //持仓手数与冻结手数
    "volume_long_today": 5,                   //多头今仓持仓手数
    "volume_long_his": 5,                     //多头老仓持仓手数
    "volume_long": 10,                        //多头持仓手数
    "volume_long_frozen_today": 1,            //多头今仓冻结手数
    "volume_long_frozen_his": 2,              //多头老仓冻结手数
    "volume_short_today": 5,                  //空头今仓持仓手数
    "volume_short_his": 5,                    //空头老仓持仓手数
    "volume_short": 10,                       //空头持仓手数
    "volume_short_frozen_today": 1,           //空头今仓冻结手数
    "volume_short_frozen_his": 2,             //空头老仓冻结手数

    //成本, 现价与盈亏
    "open_price_long": 3203.5,                //多头开仓均价
    "open_price_short": 3100.5,               //空头开仓均价
    "open_cost_long": 3203.5,                 //多头开仓成本
    "open_cost_short": 3100.5,                //空头开仓成本
    "position_price_long": 32324.4,           //多头持仓均价
    "position_price_short": 32324.4,          //空头持仓均价
    "position_cost_long": 32324.4,            //多头持仓成本
    "position_cost_short": 32324.4,           //空头持仓成本
    "last_price": 12345.6,                    //最新价
    "float_profit_long": 32324.4,             //多头浮动盈亏
    "float_profit_short": 32324.4,            //空头浮动盈亏
    "float_profit": 12345.6,                  //浮动盈亏 = float_profit_long + float_profit_short
    "position_profit_long": 32324.4,          //多头持仓盈亏
    "position_profit_short": 32324.4,         //空头持仓盈亏
    "position_profit": 12345.6,               //持仓盈亏 = position_profit_long + position_profit_short
    
    //保证金占用
    "margin_long": 32324.4,                   //多头持仓占用保证金
    "margin_short": 32324.4,                  //空头持仓占用保证金
    "margin": 32123.5,                        //持仓占用保证金 = margin_long + margin_short
  }


委托单(ORDER)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
委托单的单号:

* 每个委托单都必须有一个单号, 单号可以是不超过128个字节长的任意中英文字符和数字组合.
* 单号由发出下单指令的终端负责设定. 它必须保证, 对于同一个USER, 每个单号都是不重复的.

委托单状态: 

* 任何一个委托单的状态只会是这两种之一: FINISHED 或 ALIVE
* FINISHED: 已经可以确定, 这个委托单以后不会再产生任何新的成交
* ALIVE: 除上一种情况外的其它任何情况, 委托单状态都标记为 ALIVE, 即这个委托单还有可能产生新的成交

下面是一个 ORDER 的内容示例:

.. code-block:: javascript

  "123": {                                    //order_id, 用于唯一标识一个委托单. 对于一个USER, order_id 是永远不重复的
  
    //委托单初始属性(由下单者在下单前确定, 不再改变)
    "user_id": "423423",                      //用户ID
    "order_id": "123",                        //委托单ID, 对于一个USER, order_id 是永远不重复的
    "exchange_id": "SHFE",                    //交易所
    "instrument_id": "cu1801",                //在交易所中的合约代码
    "direction": "BUY",                       //下单方向
    "offset": "OPEN",                         //开平标志
    "volume_orign": 6,                        //总报单手数
    "price_type": "LIMIT",                    //指令类型
    "limit_price": 45000,                     //委托价格, 仅当 price_type = LIMIT 时有效
    "time_condition":	"GTD",                  //时间条件
    "volume_condition": "ANY",                //数量条件

    //下单后获得的信息(由期货公司返回, 不会改变)
    "insert_date_time":	1517544321432,        //下单时间, epoch nano
    "exchange_order_id": "434214",            //交易所单号
    
    //委托单当前状态
    "status": "ALIVE",                        //委托单状态, ALIVE=有效, FINISHED=已完
    "volume_left": 3,                         //未成交手数
    "frozen_margin": 343234,                  //冻结保证金
    "last_msg": "",                           //提示信息
    
    //内部序号
    "seqno": 4324,
  }

  
成交记录(TRADE)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
下面是一个 TRADE 的内容示例:

.. code-block:: javascript

  "123": {                                    //trade_key, 用于唯一标识一条成交记录. 对于一个USER, trade_key 是永远不重复的
  
    "user_id": "423423",                      //用户ID
    "order_id": "434214",                     //交易所单号
    "trade_id": "123",                        //委托单ID, 对于一个USER, trade_id 是永远不重复的
    "exchange_id": "SHFE",                    //交易所
    "instrument_id": "cu1801",                //在交易所中的合约代码
    "exchange_trade_id": "434214",            //交易所单号
    "direction": "BUY",                       //下单方向
    "offset": "OPEN",                         //开平标志
    "volume": 6,                              //成交手数
    "price": 45000,                           //成交价格
    "trade_date_time":	15175442131,          //成交时间, epoch nano
    "commission": "434214",                   //成交手续费
    "seqno": 4324,
  }


交易账户信息同步
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
交易账户信息通过 `rtn_data` 包的 trade 字段进行差分发送, 如下所示:

.. code-block:: javascript

  {
    "aid": "rtn_data",                                      //数据推送
    "data": [                                               //diff数据数组, 一次推送中可能含有多个数据包
    {
      "trade": {                                            //交易相关数据
        "user1": {                                          //登录用户名
          "user_id": "user1",                               //登录用户名
          "accounts": {                                     //账户资金信息
            "CNY": {                                        //account_key, 通常为币种代码
              //核心字段
              "account_id": "423423",                       //账号
              "currency": "CNY",                            //币种
              "balance": 9963216.550000003,                 //账户权益
              "available": 9480176.150000002,               //可用资金
              //参考字段
              "pre_balance": 12345,                         //上一交易日结算时的账户权益
              "deposit": 42344,                             //本交易日内的入金金额
              "withdraw": 42344,                            //本交易日内的出金金额
              "commission": 123,                            //本交易日内交纳的手续费
              "preminum": 123,                              //本交易日内交纳的权利金
              "static_balance": 124895,                     //静态权益
              "position_profit": 12345,                     //持仓盈亏
              "float_profit": 8910.231,                     //浮动盈亏
              "risk_ratio": 0.048482375,                    //风险度
              "margin": 11232.23,                           //占用资金
              "frozen_margin": 12345,                       //冻结保证金
              "frozen_commission": 123,                     //冻结手续费
              "frozen_premium": 123,                        //冻结权利金
              "close_profit": 12345,                        //本交易日内平仓盈亏
              "position_profit": 12345,                     //当前持仓盈亏
            }
          },
          "positions": {                                    //持仓
            "SHFE.cu1801": {                                //合约代码
              //核心字段
              "exchange_id": "SHFE",                        //交易所
              "instrument_id": "cu1801",                    //合约代码
              //参考字段                                 
              "hedge_flag": "SPEC",                         //套保标记
              "open_price_long": 3203.5,                    //多头开仓均价
              "open_price_short": 3100.5,                   //空头开仓均价
              "open_cost_long": 3203.5,                     //多头开仓成本
              "open_cost_short": 3100.5,                    //空头开仓成本
              "float_profit_long": 32324.4,                 //多头浮动盈亏
              "float_profit_short": 32324.4,                //空头浮动盈亏
              "position_cost_long": 32324.4,                //多头持仓成本
              "position_cost_short": 32324.4,               //空头持仓成本
              "position_profit_long": 32324.4,              //多头浮动盈亏
              "position_profit_long": 32324.4,              //空头浮动盈亏
              "volume_long_today": 5,                       //多头今仓持仓手数
              "volume_long_his": 5,                         //多头老仓持仓手数
              "volume_short_today": 5,                      //空头今仓持仓手数
              "volume_short_his": 5,                        //空头老仓持仓手数
              "margin_long": 32324.4,                       //多头持仓占用保证金
              "margin_short": 32324.4,                      //空头持仓占用保证金
              "order_volume_buy_open": 1,                   //买开仓挂单手数
              "order_volume_buy_close": 1,                  //买平仓挂单手数
              "order_volume_sell_open": 1,                  //卖开仓挂单手数
              "order_volume_sell_close": 1,                 //卖平仓挂单手数
            }
          },
          "orders": {                                       //委托单
            "123": {                                        //order_id, 用于唯一标识一个委托单. 对于一个USER, order_id 是永远不重复的
              //核心字段                              
              "order_id": "123",                            //委托单ID, 对于一个USER, order_id 是永远不重复的
              "order_type": "TRADE",                        //指令类型
              "exchange_id": "SHFE",                        //交易所
              "instrument_id": "cu1801",                    //在交易所中的合约代码
              "direction": "BUY",                           //下单方向, BUY=
              "offset": "OPEN",                             //开平标志
              "volume_orign": 6,                            //总报单手数
              "volume_left": 3,                             //未成交手数
              "trade_type": "TAKEPROFIT",                   //指令类型
              "price_type": "LIMIT",                        //指令类型
              "limit_price": 45000,                         //委托价格, 仅当 price_type = LIMIT 时有效
              "time_condition":	"GTD",                      //时间条件
              "volume_condition": "ANY",                    //数量条件
              "min_volume": 0,                        
              "hedge_flag": "SPECULATION",                  //保值标志
              "status": "ALIVE",                            //委托单状态, ALIVE=有效, FINISHED=已完
              //参考字段
              "last_msg":	"",                               //最后操作信息
              "insert_date_time":	1928374000000000,         //下单时间  
              "exchange_order_id": "434214",                //交易所单号
            }
          },
          "trades": {                                       //成交记录
            "123|1": {                                      //trade_key, 用于唯一标识一个成交项
              "order_id": "123",
              "exchange_id": "SHFE",                        //交易所
              "instrument_id": "cu1801",                    //交易所内的合约代码
              "exchange_trade_id": "1243",                  //交易所成交号
              "direction": "BUY",                           //成交方向
              "offset": "OPEN",                             //开平标志
              "volume": 6,                                  //成交手数
              "price": 1234.5,                              //成交价格
              "trade_date_time": 1928374000000000           //成交时间
            }
          },
        },
      },
      ]
    }
  }


终端登录鉴权
--------------------------------------------------
我们使用 aid = "req_login" 的包作为登录请求包. 此包的结构由具体的实现定义. 以 `Open Trade Gateway <https://github.com/shinnytech/open-trade-gateway>`_ 项目为例, req_login 包结构如下:

.. code-block:: javascript
   
  {
    "aid": "req_login",
    "bid": "aaa",
    "user_name": "43214",
    "password": "abcd123",
  }

登录成功或失败的信息, 通过 `notify` 发送


交易指令
--------------------------------------------------

下单
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
终端通过发送 insert_order 包实现下单
  
.. code-block:: javascript

  {
    "aid": "insert_order",                    //必填, 下单请求
    "user_id": "user1",                       //必填, 需要与登录用户名一致, 或为登录用户的子账户(例如登录用户为user1, 则报单 user_id 应当为 user1 或 user1.some_unit)
    "order_id": "SomeStrategy.Instance1.001", //必填, 委托单号, 需确保在一个账号中不重复, 限长512字节
    "exchange_id": "SHFE",                    //必填, 下单到哪个交易所
    "instrument_id": "cu1803",                //必填, 下单合约代码
    "direction": "BUY",                       //必填, 下单买卖方向
    "offset": "OPEN",                         //必填, 下单开平方向, 仅当指令相关对象不支持开平机制(例如股票)时可不填写此字段
    "volume": 1,                              //必填, 下单手数
    "price_type": "LIMIT",                    //必填, 报单价格类型
    "limit_price": 30502,                     //当 price_type == LIMIT 时需要填写此字段, 报单价格
    "volume_condition": "ANY",
    "time_condition": "GFD",
  }


撤单
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
终端通过发送 cancel_order 包实现撤单

.. code-block:: javascript

  {
    "aid": "cancel_order",                    //必填, 撤单请求
    "user_id": "abcd"                         //必填, 下单时的 user_id
    "order_id": "0001",                       //必填, 委托单的 order_id
  }


银期转账
--------------------------------------------------
签约银行和转账记录
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
签约银行和转账记录信息由 rtn_data 包中 trade 部分的 banks 和 transfers 发送, 如下所示

.. code-block:: javascript

  {
    "aid": "rtn_data",                                        //数据推送
    "data": [                                                 //diff数据数组, 一次推送中可能含有多个数据包
      {
        "trade": {                                            //交易相关数据
          "user1": {                                          //登录用户名
            "banks": {                                        //用户相关银行
              "4324": {
                "id": "4324",
                "name": "工行",
              }
            },
            "transfers": {                                    //账户转账记录
              "0001": {
                "datetime": 433241234123                      //转账时间, epoch nano
                "currency": "CNY",                            //币种
                "amount": 3243,                               //涉及金额
                "error_id": 0,                                //转账结果代码
                "error_msg": "成功",                          //转账结果代码
              }
            },
          },
        },
      ]
    }
  }


请求银期转账
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. code-block:: javascript

  {
    "aid": "req_transfer",                                    //必填, 转账请求
    "future_account": "0001",                                 //必填, 期货账户
    "future_password": "0001",                                //必填, 期货账户密码
    "bank_id": "0001",                                        //必填, 银行ID
    "bank_password": "0001",                                  //必填, 银行账户密码
    "currency": "CNY",                                        //必填, 币种代码
    "amount": 135.4                                           //必填, 转账金额, >0 表示转入期货账户, <0 表示转出期货账户
  }

转账操作的结果, 将由转账记录同步的方式提供给终端
  

字段常量表
------------------------------------------------

order_type
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
TRADE                    交易指令
SWAP                     互换交易指令
EXECUTE                  期权行权指令
QUOTE                    期权询价指令
======================== =================================================================================

trade_type
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
STOPLOSS                 止损
TAKEPROFIT               止盈
======================== =================================================================================

price_type
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
ANY                      任意价
LIMIT                    限价
BEST                     最优价
FIVELEVEL                五档价
======================== =================================================================================

volume_condition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
ANY                      任何数量
MIN                      最小数量
ALL                      全部数量
======================== =================================================================================

time_condition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
IOC                      立即完成，否则撤销
GFS                      本节有效
GFD                      当日有效
GTD                      指定日期前有效
GTC                      撤销前有效
GFA                      集合竞价有效
======================== =================================================================================

force_close
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
======================== =================================================================================
Name	                   Value/Description
======================== =================================================================================
NOT                      非强平
LACK_DEPOSIT             资金不足
CLIENT_POSITION_LIMIT    客户超仓
MEMBER_POSITION_LIMIT    会员超仓
POSITION_MULTIPLE        持仓非整数倍
VIOLATION                违规
OTHER                    其他
PERSONAL_DELIV           自然人临近交割
HEDGE_POSITION_LIMIT     客户套保超仓
======================== =================================================================================


协议实现
-----------------------------------
`DIFF Collection <https://www.shinnytech.com/diff>`_ 中列出了一些本协议的开源实现

