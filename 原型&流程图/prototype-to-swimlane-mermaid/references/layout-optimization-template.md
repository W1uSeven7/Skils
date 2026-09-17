# Mermaid 布局优化模板

当流程包含多个泳道、判断分支、回退线或取消线时，参考本模板组织 Mermaid `swimlane-beta` 代码。目标是减少长箭头、交叉线和判断节点附近的连线混乱。

## 使用原则

- 使用 `swimlane-beta TB`。
- 每个泳道内部先定义节点，再集中定义连线。
- 主流程尽量沿同一泳道纵向推进。
- 跨泳道职责转移使用相邻高度的节点承接。
- 判断节点前放一个清晰的处理或校验节点。
- 判断节点只表达一个核心问题。
- 远距离回退、取消、异常流程优先通过局部汇聚节点收束。

## 参考模板

```mermaid
swimlane-beta TB
  accTitle: 后台新增商品泳道图

  subgraph OP["运营"]
    OP_START([开始])
    OP_LIST[商品列表页]
    OP_ADD[点击新增商品]
    OP_PAGE[新增商品页面]
    OP_BACK{点击返回?}
    OP_FILL[填写商品基础信息]
    OP_MODE[选择销售模式]
    OP_TAG[选择商品标签]
    OP_PRE[填写预售相关信息]
    OP_MORE[填写价格、库存、说明书等信息]
    OP_SWITCH[设置上架按钮]
    OP_SUBMIT[点击新增]
    OP_FIX[修改表单信息]
    OP_END([结束])
  end

  subgraph SYS["系统"]
    SYS_OPEN[打开新增商品页面]
    SYS_RETURN[返回商品列表页]
    SYS_MODE{是否为预售商品}
    SYS_TAG_ON[允许选择商品标签]
    SYS_TAG_OFF[禁用商品标签选择]
    SYS_VALIDATE[校验必填信息和业务规则]
    SYS_VALID{校验是否通过}
    SYS_ERROR[提示错误并停留当前页面]
    SYS_UP{上架按钮是否开启}
    SYS_CREATE_ON[新增商品为已上架]
    SYS_CREATE_OFF[新增商品为未上架]
    SYS_SUCCESS[返回商品列表页并提示成功添加]
  end

  subgraph USER["用户"]
    USER_SHOW[商城可看到该商品]
    USER_HIDE[商城不可看到该商品]
  end

  OP_START --> OP_LIST --> OP_ADD
  OP_ADD --> SYS_OPEN --> OP_PAGE
  OP_PAGE --> OP_BACK

  OP_BACK -->|是| SYS_RETURN --> OP_LIST
  OP_BACK -->|否| OP_FILL

  OP_FILL --> OP_MODE
  OP_MODE --> SYS_MODE

  SYS_MODE -->|现货| SYS_TAG_ON --> OP_TAG --> OP_MORE
  SYS_MODE -->|预售| SYS_TAG_OFF --> OP_PRE --> OP_MORE

  OP_MORE --> OP_SWITCH --> OP_SUBMIT
  OP_SUBMIT --> SYS_VALIDATE --> SYS_VALID

  SYS_VALID -->|不通过| SYS_ERROR --> OP_FIX --> OP_FILL
  SYS_VALID -->|通过| SYS_UP

  SYS_UP -->|开启| SYS_CREATE_ON
  SYS_UP -->|关闭| SYS_CREATE_OFF

  SYS_CREATE_ON --> SYS_SUCCESS --> OP_END
  SYS_CREATE_OFF --> SYS_SUCCESS

  SYS_CREATE_ON --> USER_SHOW
  SYS_CREATE_OFF --> USER_HIDE
```

## 反模式

避免以下写法：

```mermaid
A -->|可取消| CANCEL
B -->|可取消| CANCEL
C -->|可取消| CANCEL
```

当 `A`、`B`、`C` 位于不同高度时，这会产生多条远距离回流线。优先改为：

```mermaid
A --> CANCEL_CHECK{是否取消?}
B --> CANCEL_CHECK
C --> CANCEL_CHECK
CANCEL_CHECK -->|是| CANCEL
CANCEL_CHECK -->|否| NEXT
```

或者在相关状态节点文案中注明“退款到账前可取消”，减少非必要回流线。
