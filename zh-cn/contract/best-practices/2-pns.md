# 安全去中心化的Polkadot域名系统

## 简介
PNS（Polkadot Name Service） 是一个建立在 Polkadot 上的域名系统，它的主要功能是域名解析，即将一个例如 “polka.dot” 这样一个可读性和可记忆性都非常好的字符串翻译成 Polkadot 上一长串无实际意义的地址。

这样我们就可以在转账、投票以及一些 dapp 操作中使用像 “polka.dot” 这样简单易懂的『域名』而不是冗长难记的『地址』。就好像现实生活中我们我们访问网站使用的是例如 “google.com” 的『域名』，而不是谷歌机房的 ip 地址。

将 “google.com” 翻译成谷歌主机 ip 的服务就是 DNS（Domain Name Service），而目前全球的 IPV4 根域名服务器只有13台，其中9台在美国，2台在欧洲，1台在亚洲，如此中心化的分布也导致了互联网上有一个说法：攻击整个因特网最有力、最直接，也是最致命的方法恐怕就是攻击根域名服务器了。

而相比于 DNS，PNS 由于直接架构在 Polkadot 上，因此天然的拥有去中心化的特点，所以传统的攻击根域名服务器的方法自然无法奏效。

除了基础的域名解析服务，PNS还提供了安全可靠的域名注册、拍卖、转让以及交易等功能。

## 域名驻车
![image-domain](../../../_media/contract/domain.png '')

### 域名长度

* 短域名（3-6个字符，需要拍卖，示例：chainx.dot）
* 长域名（7-12字符，支付租用费选择租用期限并注册，示例：chainxpool.dot）

### 注册步骤

> 1. 填写想要注册的域名（如 chainx）
  2. 选择域名时效（默认1年有效期，可续期，租用费用和租用市场相关，大于3年可给予一定优惠）
  3. 支付费用提交交易，交易成功后获取域名
  4. 可选：默认绑定交易地址，可更改绑定地址

### 拍卖方式

* 英式拍卖，以一年期租用费用为起拍价，无保留价
* 拍卖系统定期放出一部分短域名进行竞拍，在规定期限内，首次出价最高的用户将会获得域名。

> 如无人竞拍，域名将以起拍价放置于代理交易系统，任何想获得该域名的用户都可以通过代理交易方式获取该域名。

### 代理交易

用户可以注册域名，自然就会有卖出域名的需求。而卖出域名的过程具体如下：

Alice 想要卖掉自己的域名，只需要向『代理交易合约』提交一笔包含交易价格、佣金费率（佣金费率决定了你在 PNS 交易系统中的展示优先级）和时效的交易就可以了，交易成功之后该域名会自动进入『代理交易系统』中，而在时效过期之后该域名则会离开『代理交易系统』并返回到 Alice 的归属权下。在时效期内任何愿意购买该域名的用户只需要购买并支付『代理交易合约』中标明的价格就可以获得该域名。

这里会有一个潜在的问题，那就是如果 Alice 和 Bob 认识，并且他们两个人已经商量好了交易价格，在 Alice 挂出域名之后则有可能出现两个非预想的情况：

1. Bob 没有及时完成购买操作，域名被其他人买了
2. Alice 在挂出之后就及时通知 Bob 进行购买，但是依然会被一些自动脚本或者恶意抢注的人先行一步的购买成功

针对这两种情况 PNS 还提供了指定购买者地址的可选项，所以只要 Alice 在向『代理交易合约』提交请求的时候指定 Bob 提供的购买者地址就可以保证该域名只会被 Bob 购买成功了。

### 出价转让

当你发现你想要注册的域名已经被别人注册了的时候，你一定会非常沮丧。在现实世界中为了得到你心仪的域名，你可以通过域名管理网站联系经纪人，然后经纪人去帮助你询问域名拥有者是否愿意转卖，如果对方愿意转卖，在经历过域名管理机构以及域名注册商的转让操作之后，你就可以得到域名了。但是在区块链世界中，似乎没有人可以当你的经济人，在账户匿名的情况下即使你想要付出高额的溢价，也有可能根本联系不到对方。

所以 PNS 同时也提供了出价转让系统，那具体怎么操作呢？我们设想下面一个场景：

Alice 想要注册一个名为 "polka.dot" 的域名，但是发现该域名已经被一个未知用户注册，且该域名既不在『拍卖系统』中，也不在『代理交易系统』中，那么 Alice 就可以向『出价转让合约』发送一个出价转让的申请交易并携带自己愿意支付的报价和一定比例的保证金。

当未知用户登录 PNS 或者任意支持 PNS 的应用时（我们会给所有支持 PNS 的应用提供『出价通知』的插件或工具包），他就会接收到出价转让通知，如果该未知用户同意 Alice 的出价转让请求，则可以通过 PNS 提供的方法将自己的域名转移到『代理交易系统』中，Alice 只需要在『代理交易系统』补足尾款即可获得 "polka.dot" 这个域名。
如果域名拥有者不同意 Alice 的请求，那么无需任何操作，两周之后该请求会自动作废，并返还 Alice 的保证金。

如果 Alice 违约，在两周之内没有补足尾款，那么 "polka.dot" 会在『代理交易系统』中被释放，并把之前支付的保证金分配给『代理交易合约』和域名持有者。

> 在几乎所有的区块链应用中都会强调例如去中心化、匿名、安全等关键字，但是对于真正需要交互的区块链应用来说，匿名或许并不是一个值得称道的点。比如在域名的转让过程中，不可能第一次出价就能够让双方都满意，那么彼此的讨价还价就显得很有必要了。在智能合约里讨价还价技术上确实是可行的，但是实际上是一种为了区块链而区块链的浪费资源且耽误时间的行为。因此如果我们可以将用户的联系方式（Email）作为域名的一个属性（如果能够切实的对用户提供便利，那么用户可能并不介意填写自己的电子邮箱），那么毫无关联的两个用户完全可以通过更高效的方式完成域名价格的确定，然后再通过 PNS 提供的『代理交易合约』来安全的完成域名交易，这样既兼顾用户体验又确保交易安全性的交互方式或许更加符合大部分用户的真实需求。

### 域名管理

在注册或者购买域名成功之后，还需要设置一些基本信息才能更好的使用

1. 更改映射地址
2. 添加子域名
3. 更改owner
4. renew

## 合约实现

接下来我们会使用 ink 实现一个简单的 PNS ，这里我们主要实现域名注册、设置地址、域名转移以及域名查询这几个功能。


### 创建智能合约

运行 cargo contract new simple-pns，新建一个合约项目。


### 导入模块

```rust
    use ink_core::{
        env::DefaultXrmlTypes,
        storage,
    };
    use scale::{
        Encode
    };
    use ink_prelude::vec::Vec;

    pub type Text = Vec<u8>;

```

### 定义合约结构

```rust

    #[ink(storage)]
    struct SimplePns {
        /// A hashmap to store all name to addresses mapping
        name_to_address: storage::HashMap<Hash, AccountId>,
        /// A hashmap to store all name to owners mapping
        name_to_owner: storage::HashMap<Hash, AccountId>,
        name_to_abi: storage::HashMap<Hash, Text>,
        code_hash_to_abi: storage::HashMap<Hash, Text>,
        account_to_code_hash_list: storage::HashMap<AccountId, Vec<Hash>>,
        default_address: storage::Value<AccountId>,
    }
   
```

其中 name_to_address 是一个存储域名到映射地址的 hashmap，name_to_owner 是一个存储域名到域名所有者的 hashmap，default_address 是一个类型为 AccountId 的空地址。


### 定义Event

```rust

/// Emitted whenever a new name is being registered.
    #[ink(event)]
    struct Register {
        #[ink(topic)]
        name: Hash,
        #[ink(topic)]
        from: AccountId,
    }

    /// Emitted whenever a new name is being registered.
    #[ink(event)]
    struct RegisterAbi {
        #[ink(topic)]
        name: Hash,
        #[ink(topic)]
        from: AccountId,
        #[ink(topic)]
        code_hash: Hash,
        abi: Text
    }

    #[ink(event)]
    struct SetAddress {
        name: Hash,
        from: AccountId,
        old_address: Option<AccountId>,
        new_address: AccountId,
    }

    #[ink(event)]
    struct Transfer {
        name: Hash,
        #[ink(topic)]
        from: AccountId,
        #[ink(topic)]
        old_owner: Option<AccountId>,
        #[ink(topic)]
        new_owner: AccountId,
    } 


```

### 初始化合约

```rust

 #[ink(constructor)]
 fn new(&mut self) {
    self.default_address.set(AccountId::from([0x0; 32]));
 }

```

### 实现域名操作方法

```rust
 impl SimplePns {
        /// Creates a new domain name service contract.
        #[ink(constructor)]
        fn new(&mut self) {
            self.default_address.set(AccountId::from([0x0; 32]));
        }

        /// Register specific name with caller as owner
        #[ink(message)]
        fn register(&mut self, name: Hash, address: AccountId) -> bool {
            let caller = self.env().caller();
            if self.is_name_exist_impl(name) {
                return false
            }
            // env.println(&format!("register name: {:?}, owner: {:?}", name, caller));
            self.name_to_owner.insert(name, caller);
            self.name_to_address.insert(name, address);
            self.env().emit_event(Register {
                name: name,
                from: caller,
            });
            true
        }

        #[ink(message)]
        fn register_abi(&mut self, name: Hash, code_hash: Hash, abi: Text) -> bool {
            let caller = self.env().caller();
            if self.is_name_exist_impl(name) {
                return false
            }
            // env.println(&format!("register_abi name: {:?}, owner: {:?}", name, caller));
            self.name_to_abi.insert(name, abi.clone());
            self.code_hash_to_abi.insert(code_hash, abi.clone());
            match self.account_to_code_hash_list.get_mut(&caller) {
                None => {
                    let mut new_vec = Vec::new();
                    new_vec.push(code_hash);
                    self.account_to_code_hash_list.insert(env.caller(), new_vec);
                },
                Some(a) => {
                    a.push(code_hash);
                }
            }
            self.env().emit_event(RegisterAbi {
                from: caller,
                name: name,
                code_hash: code_hash,
                abi: abi
            });
            true
        }

         /// Query abi by name
        #[ink(message)]
        fn get_abi_by_name(&self, name: Hash) -> Text {
            self.name_to_abi.get(&name).unwrap_or(&Vec::new()).to_vec()
        }

        /// Query abi by code_hash
        #[ink(message)]
        fn get_abi_by_code_hash(&self, code_hash: Hash) -> Text {
            self.code_hash_to_abi.get(&code_hash).unwrap_or(&Vec::new()).to_vec()
        }

        /// Query code_hash list by account
        #[ink(message)]
        fn get_code_hash_list_by_account(&self, account: AccountId) -> Vec<Hash> {
            self.account_to_code_hash_list.get(&account).unwrap_or(&Vec::new()).to_vec()
        }

        /// Set address for specific name
        #[ink(message)]
        fn set_address(&mut self, name: Hash, address: AccountId) -> bool {
            let caller: AccountId = self.env().caller();
            let owner: AccountId = self.get_owner_or_none(name);
            // env.println(&format!("set_address caller: {:?}, owner: {:?}", caller, owner));
            if caller != owner {
                return false
            }
            let old_address = self.name_to_address.insert(name, address);
            self.env().emit_event(SetAddress {
                name: name,
                from: caller,
                old_address: old_address,
                new_address: address,
            });
            return true
        }

        /// Transfer owner to another address
        #[ink(message)]
        fn transfer(&mut self, name: Hash, to: AccountId) -> bool {
            let caller: AccountId = self.env().caller();
            let owner: AccountId = self.get_owner_or_none(name);
            // env.println(&format!("transfer caller: {:?}, owner: {:?}", caller, owner));
            if caller != owner {
                return false
            }
            let old_owner = self.name_to_owner.insert(name, to);
            self.env().emit_event(Transfer {
                name: name,
                from: caller,
                old_owner: old_owner,
                new_owner: to,
            });
            return true
        }

        /// Get address for the specific name 
        #[ink(message)]
        fn get_address(&self, name: Hash) -> AccountId {
            let address: AccountId = self.get_address_or_none(name);
            // env.println(&format!("get_address name is {:?}, address is {:?}", name, address));
            address
        }

        /// Check whether name is exist
        #[ink(message)]
        fn is_name_exist(&self, name: Hash) -> bool {
            self.is_name_exist_impl(name)
        }

         fn get_address_or_none(&self, name: Hash) -> AccountId {
            let address = self.name_to_address.get(&name).unwrap_or(&self.default_address);
            *address
        }

        /// Returns an AccountId or default 0x00*32 if it is not set.
        fn get_owner_or_none(&self, name: Hash) -> AccountId {
            let owner = self.name_to_owner.get(&name).unwrap_or(&self.default_address);
            *owner
        }

        /// check whether name is exist
        fn is_name_exist_impl(&self, name: Hash) -> bool {
            let address = self.name_to_owner.get(&name);
            if let None = address {
                return false;
            }
            true
        }
    }

```

### 编译合约和 ABI

编译 WASM 合约文件：

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract build
```

生成合约 metadata，也就是合约 ABI:

```bash
$ docker run --rm -v "$PWD":/build -w /build chainxorg/contract-builder:v0.6.0 cargo contract generate-metadata
```

运行成功之后 target 目录下会出现相应的 wasm 和 json 文件。

## 项目地址

https://github.com/chainpool/simple_pns