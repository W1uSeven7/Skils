# 示例：订单退货售后流程

## 用户输入

用户提供 Figma 原型链接，并说明：

- 根据订单售后流程原型图绘制泳道图。
- 使用 Mermaid `swimlane-beta` 语法，并优先优化布局，减少长箭头和交叉线。
- 需要按业务逻辑从上到下绘制。
- 开始节点是“用户点击申请退货按钮”。
- 在输出 Mermaid 代码、节点清单、连线清单前，需要先确认泳道数量和角色。

## 需要先确认的问题

1. 一共有几条泳道？分别是什么角色？
2. 用户提交退货申请后，是否需要后台运营审核？
3. 后台运营审核是否只有通过和拒绝两个结果？
4. 用户在哪些阶段可以取消退货申请？
5. 退款是系统自动完成，还是后台运营处理后完成？

## 用户确认

- 泳道为：用户、系统、后台运营。
- 用户提交退货申请后，后台运营需要审核。
- 审核结果只有通过和拒绝。
- 从用户提交退货申请开始，到退款真正到账前，任意阶段都可以取消退货申请。
- 用户取消退货申请后，商品卡片状态变回 `Delivered`。

## 输出示例：Mermaid swimlane-beta 代码

```mermaid
swimlane-beta TB
  accTitle: 订单退货售后流程
  accDescr: 从用户点击申请退货按钮开始，展示用户、系统、后台运营三方职责。

  subgraph user [用户]
    u_start[点击申请退货按钮]
    u_select_reason[选择退货原因]
    u_fill_desc[填写问题描述]
    u_submit_return[提交退货申请]
    u_cancel_return[确认取消退货申请]
    u_submit_tracking[填写并提交 Tracking Number]
  end

  subgraph system [系统]
    s_show_form[展示退货申请页]
    s_validate[校验退货原因和说明]
    s_create_return[生成退货申请单]
    s_return_requested[商品卡片状态变为 Return requested]
    s_restore_delivered[关闭退货申请单，商品卡片状态变回 Delivered]
    s_return_approved[商品卡片状态变为 Return approved]
    s_return_declined[商品卡片状态变为 Return declined]
    s_refund_processing[商品卡片状态变为 Refund processing]
    s_refunded[商品卡片状态变为 Refunded]
    s_show_refund_completed[展示 Refund completed / Return Details]
  end

  subgraph ops [后台运营]
    o_receive[接收退货申请]
    o_review[审核退货申请]
    o_decision{退货申请是否通过?}
    o_wait_return[等待/确认退货物流或收货情况]
    o_refund[处理退款]
  end

  u_start --> s_show_form
  s_show_form --> u_select_reason
  u_select_reason --> u_fill_desc
  u_fill_desc --> u_submit_return
  u_submit_return --> s_validate
  s_validate --> s_create_return
  s_create_return --> s_return_requested
  s_return_requested --> o_receive
  o_receive --> o_review
  o_review --> o_decision

  s_return_requested -->|退款到账前可取消| u_cancel_return
  u_cancel_return --> s_restore_delivered

  o_decision -->|拒绝| s_return_declined
  o_decision -->|通过| s_return_approved

  s_return_approved -->|退款到账前可取消| u_cancel_return
  s_return_approved --> u_submit_tracking
  u_submit_tracking --> s_refund_processing

  s_refund_processing -->|退款到账前可取消| u_cancel_return
  s_refund_processing --> o_wait_return
  o_wait_return --> o_refund
  o_refund --> s_refunded
  s_refunded --> s_show_refund_completed
```

## 输出示例：节点清单

用户：
- 点击申请退货按钮
- 选择退货原因
- 填写问题描述
- 提交退货申请
- 确认取消退货申请
- 填写并提交 Tracking Number

系统：
- 展示退货申请页
- 校验退货原因和说明
- 生成退货申请单
- 商品卡片状态变为 Return requested
- 关闭退货申请单，商品卡片状态变回 Delivered
- 商品卡片状态变为 Return approved
- 商品卡片状态变为 Return declined
- 商品卡片状态变为 Refund processing
- 商品卡片状态变为 Refunded
- 展示 Refund completed / Return Details

后台运营：
- 接收退货申请
- 审核退货申请
- 退货申请是否通过?
- 等待/确认退货物流或收货情况
- 处理退款

## 输出示例：连线清单

- 点击申请退货按钮 -> 展示退货申请页
- 展示退货申请页 -> 选择退货原因
- 选择退货原因 -> 填写问题描述
- 填写问题描述 -> 提交退货申请
- 提交退货申请 -> 校验退货原因和说明
- 校验退货原因和说明 -> 生成退货申请单
- 生成退货申请单 -> 商品卡片状态变为 Return requested
- 商品卡片状态变为 Return requested -> 接收退货申请
- 接收退货申请 -> 审核退货申请
- 审核退货申请 -> 退货申请是否通过?
- 退货申请是否通过? --拒绝--> 商品卡片状态变为 Return declined
- 退货申请是否通过? --通过--> 商品卡片状态变为 Return approved
- 商品卡片状态变为 Return approved -> 填写并提交 Tracking Number
- 填写并提交 Tracking Number -> 商品卡片状态变为 Refund processing
- 商品卡片状态变为 Refund processing -> 等待/确认退货物流或收货情况
- 等待/确认退货物流或收货情况 -> 处理退款
- 处理退款 -> 商品卡片状态变为 Refunded
- 商品卡片状态变为 Refunded -> 展示 Refund completed / Return Details
- 商品卡片状态变为 Return requested --退款到账前可取消--> 确认取消退货申请
- 商品卡片状态变为 Return approved --退款到账前可取消--> 确认取消退货申请
- 商品卡片状态变为 Refund processing --退款到账前可取消--> 确认取消退货申请
- 确认取消退货申请 -> 关闭退货申请单，商品卡片状态变回 Delivered
