# PlatON Secure Multi-Party Computation 

## MPC任务智能合约 

### <span id="contract_write">合约详细说明</span>

`Privacy Contract` 固定了计算的相关数据，当合约被发布后，该规则就实实在在的存储在了`PlatON`网络的各个节点。
任何时候如果已定义好的参与者如果需要开启一次计算，则只需要调用合约中对应的算法 [MPC算法介绍](#)，
调用时需要提供质押资产（此次交易质押多少`E`,`VALUE`指定）调用后合约会返回本次计算的`taskId`。
同时，在合约内部会记录当前产生的任务信息，并将调用者提供的`ATP`质押到合约账户中。

### 如何生成

`PlatON`整个工具链中提供了编译工具[plang](_plang_usage)，可根据编写的算法、合约模板及相关信息编译产生一个标准的 `Privacy Contract` 。 

### 合约模板

合约语法规则请参考资料：[Wasm合约开发指南]([Chinese-Simplified]-Wasm合约开发指南)

`Privacy Contract` 范例：[点击查看](privacy-contract/mpcc.cpp)

```c++
namespace mpc {

/// ++++++++++++++++++++++++ 主要用于定义各变量存储在 Storage 中的key +++++++++++++++++++
#define COMMON_SPLIT_CHAR "&"
#define OWNER "__OWNER__"

#define PREFIX "MPC_"
#define PREFIX_BONUS_RULE "BONUS_RULE_"

/// define key for the storage.
#define KEY_IR "IR"
#define KEY_PARTIES PREFIX "PARTIES"
#define KEY_URLS PREFIX "URLS"
#define KEY_INVITORS PREFIX "INVITOR"
#define KEY_TASK_INFO PREFIX "TASK"
#define KEY_METHOD_PRICE PREFIX "METHOD_PRICE"

/// the value of IR
#define IR_VALUE "$IR_VALUE_TEMPLATE$"

/// the value of invitor
#define INVITOR_VALUE "$INVITOR_VALUE_TEMPLATE$"

/// the list of parties, use mark('&') to split. eg：a&b&c
#define PARTIES_VALUE "$PARTIES_VALUE_TEMPLATE$"

/// the url of parties
/// eg.0x60ceca9c1290ee56b98d4e160ef0453f7c40d219$DirectNodeServer:default -h 10.10.8.155 -p 10001,0x3771c08952f96e70af27324de11bb380ec388ec3$DirectNodeServer:default -h 10.10.8.155 -p 10002
#define URLS_VALUE "$URLS_VALUE_TEMPLATE$"

/// profit rule.
/// format：k1:v1,k2:v2,k3:v3
#define PROFIT_RULES_VALUE "$PROFIT_RULES_VALUE$"

/// rule：${METHOD}&${VALUE},${METHOD}&${VALUE}
/// eg: func01$10000000000000000,func02$200000000000000,func03$4000000000000000000000000000
#define METHOD_PRICE_VALUE "$METHOD_PRICE_VALUE$"

#define PREFIX_RULES_MAP PREFIX "MAP_RULE_"
#define PREFIX_RESULT_MAP PREFIX "MAP_RESULT_"
#define PREFIX_ALLOT_MAP PREFIX "MAP_ALLOT_"
#define PREFIX_METHOD_MAP PREFIX "MAP_PRICE_"

/// ++++++++++++++++++++++++++++++++ END +++++++++++++++++++++++++++++++++++++++++++

class MPC : public platon::Contract {
    public:
        /// 定义Event(事件)
        PLATON_EVENT(start_calc_event, uint64_t, const char *)
        PLATON_EVENT(set_result_event, uint64_t, const char *)
        PLATON_EVENT(set_fees_event, uint64_t, const char *)

        /// 初始化函数，在合约被发布的时候调用
        /// 可在此处对相关参数进行初始化
        void init() {
        }

        /// 获取合约的发布者地址
        const char * get_owner() const {
        }

        /// 此为IR算法中的一个计算方法
        /// 由调用方决定调用哪个算法进行计算
        void ListMsg(const char *extra) {
                start_calc("ListMsg", extra);
        }

        /// 此为IR算法中的一个计算方法
        /// 含义同上
        void fuc02(const char *extra) {
                start_calc("fuc02", extra);
        }

        /// 此为IR算法中的一个计算方法
        /// 含义同上
        void fuc03(const char *extra) {
                start_calc("func03", extra);
        }

        /// 启动一次MPC计算
        /// 启动计算的核心逻辑及事前准备工作会在此处完成
        void start_calc(const char *method, const char *extra) {
        }

        /// 设置计算结果
        void set_result(const char *taskId, uint64_t status, const char *data) {
        }

        /// 设置费用分配规则
        /// k1:v1,k2:v2,k3:v3
        void set_fees(const char *fees) {
        }

        /// 获取IR二进制数据
        const char * get_ir_data() const {
        }

        /// 获取参与者信息
        const char * get_participants() const {
        }

        /// 获取所有参与者计算通信地址
        const char * get_urls() const {
        }

        /// 根据用户地址获取计算者通信地址
        const char * get_url_by_id(const char *id) const {
        }

        /// 获取计算结果[结果已二进制存储，返回十六进制，需要调用者进行解码]
        const char * get_result(const char *task_id) const {
        }

        /// 获取计算状态
        uint64_t get_status(const char *task_id) const {
        }

        /// 获取某个计算方法所需要花费的费用
        uint64_t get_fee(const char* method) const {
        }

        /// 获取当次计算的邀请者
        const char * get_invitor() const {
        }
        };
}

/// 以下声明 ABI 需要导出的方法
PLATON_ABI(mpc::MPC, ListMsg);
PLATON_ABI(mpc::MPC, fuc02);
PLATON_ABI(mpc::MPC, fuc03);
PLATON_ABI(mpc::MPC, start_calc);
PLATON_ABI(mpc::MPC, get_owner);
PLATON_ABI(mpc::MPC, set_result);
PLATON_ABI(mpc::MPC, get_ir_data);
PLATON_ABI(mpc::MPC, get_participants);
PLATON_ABI(mpc::MPC, get_urls);
PLATON_ABI(mpc::MPC, get_result);
PLATON_ABI(mpc::MPC, get_status);
PLATON_ABI(mpc::MPC, get_fee);
PLATON_ABI(mpc::MPC, get_invitor);
PLATON_ABI(mpc::MPC, get_url_by_id);

#endif

```

### 全局变量说明

上述合约模板中已经有了部分说明，不过此处将进行更为详细的介绍。在 `Privacy Contract` 中，使用宏定义进行静态变量的替换。

**1. IR_VALUE**

```
#define IR_VALUE "$IR_VALUE_TEMPLATE$"
```

**含义**

用于存储MPC算法数据，序列化后的16进制表现形式。示例中数据过大，省略展示。

**举例**

```
#define IR_VALUE "3b204d6f64756 ... 3732" 
```

**2. INVITOR_VALUE**

```
#define INVITOR_VALUE "$INVITOR_VALUE_TEMPLATE$"
```

**含义**

用于存储计算的邀请方地址信息

**举例**

```
#define INVITOR_VALUE "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219"
```

**3. PARTIES_VALUE**

```
#define PARTIES_VALUE "$PARTIES_VALUE_TEMPLATE$"
```

**含义**

用于存储计算参与者列表，默认模板中使用 `&` 作为分割连接符。

**举例**

```
#define PARTIES_VALUE "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219&0x3771c08952f96e70af27324de11bb380ec388ec3"
```

**4. URLS_VALUE**

```
#define URLS_VALUE "$URLS_VALUE_TEMPLATE$"
```

**含义**

用于存储计算参与者的地址信息，默认使用连接分隔符: `$` 进行ID与地址的连接，使用 `,` （逗号）进行多组数据连接。

**举例**

```
#define URLS_VALUE "0x60ceca9c1290ee56b98d4e160ef0453f7c40d219$DirectNodeServer:default -h 192.168.18.31 -p 10001,0x3771c08952f96e70af27324de11bb380ec388ec3$DirectNodeServer:default -h 192.168.18.31 -p 10002"
```

**5. PROFIT_RULES_VALUE**

```
#define PROFIT_RULES_VALUE "$PROFIT_RULES_VALUE$"
```

**含义**

用于存储计算后对质押资产的分配规则，官方提供的默认按人数比例对参计算者进行分配。可以自定义规则，然后实现相关实现。
如示例中表示：地址 `A` 分配 `30%` ，地址 `B` 分配 `70%` , 具体分配规则用户可以自定义实现。

**举例**

```
#define PROFIT_RULES_VALUE "A:30,B:70"
```

**6. METHOD_PRICE_VALUE**

```
#define METHOD_PRICE_VALUE "$METHOD_PRICE_VALUE$"
```

**含义**

用于存储每一个算法的计价，单位 `ADP`，（`1ATP=10^18 ADP`）。
示例含义：计算方法 `add` 需要 `1000000 ADP`，`sub` 需要 `2000000 ADP` ， `xor` 需要 `40000000000 ADP` 

**举例**

```
#define METHOD_PRICE_VALUE "add$1000000,sub$2000000,xor$40000000000"
```

### 功能函数说明

**1. Event: start_calc_event**

```
PLATON_EVENT(start_calc_event, uint64_t, const char *)
```

**含义**

  定义 `Event`，该定义固定且不可进行更改。在 `start_calc` 中返回的Event一定为该结构。




**2. Event: set_result_event**

```
PLATON_EVENT(set_result_event, uint64_t, const char *)
```

**含义**

  定义 `Event`，该定义仅做参考，可使用也可以自定义。




**3. Func: init**

```
void init(){}
```

**含义**

  初始化函数，在合约第一次发布时会执行一次。默认在该函数中实现对静态数据进行入 `db` 操作，主要就是将宏定义的各项静态数据入库，为计算做初始数据准备。

**伪逻辑**

```c++
	void init() {
		// 1、设置合约所有者 Owner 
		// 2、设置邀请者
		// 3、设置参与者
		// 4、设置参与者URL信息
		// 5、设置算法数据
		// 6、设置算法价格
	}
```



**4. Func: get_owner**

	const char * get_owner() const {}

**含义**

  返回合约的所有者地址。




**5. Func: add/sub/xor**

	void add(const char *extra) {}
	void sub(const char *extra) {}
	void xor(const char *extra) {}

**含义**

  `add`、`sub`、`xor` 算法, 逻辑最终调用到 `start_calc` 完成主要逻辑。合约中列出与算法相同的方法名主要是为了简化调用。




**6. Func: start_calc**

	void start_calc(const char *method, const char *extra) {}

**含义**

  开启计算的主要逻辑都在此函数中完成，调用成功后会返回 `start_calc_event` （固定且不能改变）事件，事件中要返回错误码和当前任务的 `taskId` 。

**伪逻辑**

```c++
void start_calc(const char *method, const char *extra) {
	// 校验调用者是否为预定义的参与者（根据实际情况决定，此判断可省略）	
	// 校验调用者质押的 ADP 是否足够支付计算方法的定价
	// 生成taskId
	// 保存本次任务信息，存入一条新记录
	// 返回 `event`
}
```



**7. Func: set_result**

	void set_result(const char *taskId, uint64_t status, const char *data) {}

**含义**

  设置计算结果，该函数用于接收计算的结果。收到结果后根据结果的状态进行预质押金额的分配，分配按预定义的比例进行。

**伪逻辑**

```c++
void set_result(const char *taskId, uint64_t status, const char *data) {
	// ...
}
```



**8. Func: set_fees**

	void set_fees(const char *fees) {}

**含义**

  设置分配比例，fees 为有规则的字符串，规则可自定义，只是需要在进行解析时保证对应的反解析规则即可，不做强制要求。

**伪逻辑**

```c++
void set_fees(const char *fees) {
	// ...
}
```



**9. Func: get_ir_data**

	const char * get_ir_data() const {}

**含义**

  获取算法源内容，返回16进制的表示。

**伪逻辑**

```c++
const char * get_ir_data() const {
	// ...
}
```



**10. Func: get_participants**

	const char * get_participants() const {}

**含义**

  获取参与者列表，返回数据同宏定义的静态数据一致。

例如：`0x60ceca9c1290ee56b98d4e160ef0453f7c40d219&0x3771c08952f96e70af27324de11bb380ec388ec3`

**伪逻辑**

```c++
const char * get_participants() const {
	// ...
}
```



**11. Func: get_urls**

	const char * get_urls() const {}

**含义**

  获取参与者及其对应的额节点地址，该地址主要为对端的 `Endpoint` 地址。

例如： `0x60ceca9c1290ee56b98d4e160ef0453f7c40d219$DirectNodeServer:default -h 192.168.18.31 -p 10001,0x3771c08952f96e70af27324de11bb380ec388ec3$DirectNodeServer:default -h 192.168.18.31 -p 10002` 

**伪逻辑**

```c++
const char * get_urls() const {
	// ...
}
```



**12. Func: get_url_by_id**

```
const char * get_url_by_id(const char *id) const {}
```

**含义**

  根据ID获取节点地址，该地址主要为对端的 `Endpoint` 地址。

例如： `0x60ceca9c1290ee56b98d4e160ef0453f7c40d219$DirectNodeServer:default -h 192.168.18.31 -p 10001,0x3771c08952f96e70af27324de11bb380ec388ec3$DirectNodeServer:default -h 192.168.18.31 -p 10002` 

**伪逻辑**

```c++
const char * get_url_by_id(const char *id) const {
	// ...
}
```



**13. Func: get_result**

	const char * get_result(const char *task_id) const {}

**含义**

  返回计算结果，结果为16进制，具体类型由获取者自行进行解析。

**伪逻辑**

```c++
const char * get_result(const char *task_id) const {
	// ...
}
```



**14. Func: get_status**

	uint64_t get_status(const char *task_id) const {}

**含义**

  获取计算的状态，0 计算中，1 成功，2 失败 

**伪逻辑**

```c++
uint64_t get_status(const char *task_id) const {
	// ...
}
```



**15. Func: get_fee**

	uint64_t get_fee(const char* method) const {}

**含义**

  获取红利分配规则。

例如：`a:30,b:70`

**伪逻辑**

```c++
uint64_t get_fee(const char* method) const {
	// ...
}
```



