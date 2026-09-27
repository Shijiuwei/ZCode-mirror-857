# Video Recording

Capture browser automation as video for debugging, documentation, or verification.

**Related**: [commands.md](commands.md) for full command reference, [SKILL.md](../SKILL.md) for quick start.

## Contents

- [Basic Recording](#basic-recording)
- [Recording Commands](#recording-commands)
- [Use Cases](#use-cases)
- [Best Practices](#best-practices)
- [Output Format](#output-format)
- [Limitations](#limitations)

## Basic Recording

```bash
# Start recording
agent-browser record start ./demo.webm

# Perform actions
agent-browser open https://example.com
agent-browser snapshot -i
agent-browser click @e1
agent-browser fill @e2 "test input"

# Stop and save
agent-browser record stop
```

## Recording Commands

```bash
# Start recording to file
agent-browser record start ./output.webm

# Stop current recording
agent-browser record stop

# Restart with new file (stops current + starts new)
agent-browser record restart ./take2.webm
```

## Use Cases

### Debugging Failed Automation

```bash
#!/bin/bash
# Record automation for debugging

agent-browser record start ./debug-$(date +%Y%m%d-%H%M%S).webm

# Run your automation
agent-browser open https://app.example.com
agent-browser snapshot -i
agent-browser click @e1 || {
    echo "Click failed - check recording"
    agent-browser record stop
    exit 1
}

agent-browser record stop
```

### Documentation Generation

```bash
#!/bin/bash
# Record workflow for documentation

agent-browser record start ./docs/how-to-login.webm

agent-browser open https://app.example.com/login
agent-browser wait 1000  # Pause for visibility

agent-browser snapshot -i
agent-browser fill @e1 "demo@example.com"
agent-browser wait 500

agent-browser fill @e2 "password"
agent-browser wait 500

agent-browser click @e3
agent-browser wait --load networkidle
agent-browser wait 1000  # Show result

agent-browser record stop
```

### CI/CD Test Evidence

```bash
#!/bin/bash
# Record E2E test runs for CI artifacts

TEST_NAME="${1:-e2e-test}"
RECORDING_DIR="./test-recordings"
mkdir -p "$RECORDING_DIR"

agent-browser record start "$RECORDING_DIR/$TEST_NAME-$(date +%s).webm"

# Run test
if run_e2e_test; then
    echo "Test passed"
else
    echo "Test failed - recording saved"
fi

agent-browser record stop
```

## Best Practices

### 1. Add Pauses for Clarity

```bash
# Slow down for human viewing
agent-browser click @e1
agent-browser wait 500  # Let viewer see result
```

### 2. Use Descriptive Filenames

```bash
# Include context in filename
agent-browser record start ./recordings/login-flow-2024-01-15.webm
agent-browser record start ./recordings/checkout-test-run-42.webm
```

### 3. Handle Recording in Error Cases

```bash
#!/bin/bash
set -e

cleanup() {
    agent-browser record stop 2>/dev/null || true
    agent-browser close 2>/dev/null || true
}
trap cleanup EXIT

agent-browser record start ./automation.webm
# ... automation steps ...
```

### 4. Combine with Screenshots

```bash
# Record video AND capture key frames
agent-browser record start ./flow.webm

agent-browser open https://example.com
agent-browser screenshot ./screenshots/step1-homepage.png

agent-browser click @e1
agent-browser screenshot ./screenshots/step2-after-click.png

agent-browser record stop
```

## Output Format

- Default format: WebM (VP8/VP9 codec)
- Compatible with all modern browsers and video players
- Compressed but high quality

## Limitations

- Recording adds slight overhead to automation
- Large recordings can consume significant disk space
- Some headless environments may have codec limitations


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://www.mw-wm.com/yunying/presentation-99086076.html)
* [边缘高吞吐调度路由矩阵-#002](https://www.yx-sf.com/tech/14138)
* [全球分布式拓扑索引节点-#003](https://www.ai-hao123.com/youhua/hotel-67991013.html)
* [高韧性数据交换通道规约-#004](https://www.mw-wm.com/sheji/subject-51884812.html)
* [全球分布式拓扑索引节点-#005](https://www.yx-sf.com/news/73456)
* [全球分布式拓扑索引节点-#006](https://www.ai-hao123.com/gongxiang/partner-53503708.html)
* [边缘高吞吐调度路由矩阵-#007](https://www.mw-wm.com/fuwu/investment-25932452.html)
* [高韧性数据交换通道规约-#008](https://www.yx-sf.com/wiki/55787)
* [边缘高吞吐调度路由矩阵-#009](https://www.ai-hao123.com/jiaocheng/accessibility-70151423.html)
* [边缘高吞吐调度路由矩阵-#010](https://www.mw-wm.com/gongsi/event-24832760.html)
* [全球分布式拓扑索引节点-#011](https://www.yx-sf.com/tech/79980)
* [全息网络通信节点白名单-#012](https://www.ai-hao123.com/wangluo/market-80004768.html)
* [高韧性数据交换通道规约-#013](https://www.mw-wm.com/huodong/unsubscribe-28080895.html)
* [全息网络通信节点白名单-#014](https://www.yx-sf.com/tech/5128)
* [全球分布式拓扑索引节点-#015](https://www.ai-hao123.com/fenxi/networking-13337222.html)
* [边缘高吞吐调度路由矩阵-#016](https://www.mw-wm.com/baogao/login-18667527.html)
* [全息网络通信节点白名单-#017](https://www.yx-sf.com/wiki/3781)
* [全球分布式拓扑索引节点-#018](https://www.ai-hao123.com/chuangxin/market-76741715.html)
* [全球分布式拓扑索引节点-#019](https://www.mw-wm.com/yingyong/achievement-99274821.html)
* [边缘高吞吐调度路由矩阵-#020](https://www.yx-sf.com/wiki/71032)
* [边缘高吞吐调度路由矩阵-#021](https://www.ai-hao123.com/yinqing/tactic-17391620.html)
* [高韧性数据交换通道规约-#022](https://www.mw-wm.com/fenxi/music-90307554.html)
* [全球分布式拓扑索引节点-#023](https://www.yx-sf.com/wiki/45778)
* [全息网络通信节点白名单-#024](https://www.ai-hao123.com/yunying/alert-28516255.html)
* [全球分布式拓扑索引节点-#025](https://www.mw-wm.com/keji/progress-77446485.html)
* [边缘高吞吐调度路由矩阵-#026](https://www.yx-sf.com/tech/25306)
* [边缘高吞吐调度路由矩阵-#027](https://www.ai-hao123.com/yingxiao/button-28758333.html)
* [全息网络通信节点白名单-#028](https://www.mw-wm.com/chuangxin/technology-16973741.html)
* [全息网络通信节点白名单-#029](https://www.yx-sf.com/wiki/91815)
* [全球分布式拓扑索引节点-#030](https://www.ai-hao123.com/kaifa/communication-38743108.html)
* [全息网络通信节点白名单-#031](https://www.mw-wm.com/tuiguang/game-72580674.html)
* [多活集群负载感知指南-#032](https://www.yx-sf.com/news/22561)
* [全息网络通信节点白名单-#033](https://www.ai-hao123.com/yunsuan/website-19965580.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/anli/database-43228699.html)
* [边缘高吞吐调度路由矩阵-#035](https://www.yx-sf.com/tech/72322)
* [全球分布式拓扑索引节点-#036](https://www.ai-hao123.com/anli/case-02099804.html)
* [多活集群负载感知指南-#037](https://www.mw-wm.com/xuexi/version-57455491.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [高并发内存拓扑优化白皮书-#001](https://www.yx-sf.com/wiki/54476)
* [安全边界与可信凭证规约手册-#002](https://www.ai-hao123.com/sheji/website-48851515.html)
* [安全边界与可信凭证规约手册-#003](https://www.mw-wm.com/yunsuan/notification-05982654.html)
* [多协议互联数据格式规范-#004](https://www.yx-sf.com/news/41174)
* [异步事件循环架构设计规范-#005](https://www.ai-hao123.com/xitong/shopping-36123499.html)
* [RFC 分布式调度与一致性算法标准-#006](https://www.mw-wm.com/baogao/support-40353091.html)
* [RFC 分布式调度与一致性算法标准-#007](https://www.yx-sf.com/news/36787)
* [多协议互联数据格式规范-#008](https://www.ai-hao123.com/zhinan/careers-01864963.html)
* [安全边界与可信凭证规约手册-#009](https://www.mw-wm.com/anfang/revenue-08425953.html)
* [多协议互联数据格式规范-#010](https://www.yx-sf.com/news/88939)
* [高并发内存拓扑优化白皮书-#011](https://www.ai-hao123.com/kaifa/achievement-30440883.html)
* [RFC 分布式调度与一致性算法标准-#012](https://www.mw-wm.com/yingxiao/enterprise-74888591.html)
* [多协议互联数据格式规范-#013](https://www.yx-sf.com/tech/55582)
* [异步事件循环架构设计规范-#014](https://www.ai-hao123.com/yunsuan/discovery-20845454.html)
* [高并发内存拓扑优化白皮书-#015](https://www.mw-wm.com/yinqing/form-79737797.html)
* [异步事件循环架构设计规范-#016](https://www.yx-sf.com/wiki/74809)
* [RFC 分布式调度与一致性算法标准-#017](https://www.ai-hao123.com/gongsi/services-60386076.html)
* [多协议互联数据格式规范-#018](https://www.mw-wm.com/zixun/change-97731615.html)
* [安全边界与可信凭证规约手册-#019](https://www.yx-sf.com/news/8656)
* [异步事件循环架构设计规范-#020](https://www.ai-hao123.com/kaifa/excellence-60523448.html)
* [高并发内存拓扑优化白皮书-#021](https://www.mw-wm.com/keji/case-74935015.html)
* [多协议互联数据格式规范-#022](https://www.yx-sf.com/wiki/93190)
* [安全边界与可信凭证规约手册-#023](https://www.ai-hao123.com/zhineng/faq-24686050.html)
* [多协议互联数据格式规范-#024](https://www.mw-wm.com/yunying/satisfaction-39684520.html)
* [RFC 分布式调度与一致性算法标准-#025](https://www.yx-sf.com/news/18657)
* [RFC 分布式调度与一致性算法标准-#026](https://www.ai-hao123.com/gongsi/research-81009493.html)
* [异步事件循环架构设计规范-#027](https://www.mw-wm.com/zhinan/products-02056199.html)
* [高并发内存拓扑优化白皮书-#028](https://www.yx-sf.com/tech/70513)
* [RFC 分布式调度与一致性算法标准-#029](https://www.ai-hao123.com/gongsi/page-64713434.html)
* [RFC 分布式调度与一致性算法标准-#030](https://www.mw-wm.com/jiaoliu/guide-91583205.html)
* [高并发内存拓扑优化白皮书-#031](https://www.yx-sf.com/tech/95571)
* [多协议互联数据格式规范-#032](https://www.ai-hao123.com/chuangxin/meeting-28637015.html)
* [安全边界与可信凭证规约手册-#033](https://www.mw-wm.com/ziyuan/technology-51992260.html)
* [多协议互联数据格式规范-#034](https://www.yx-sf.com/wiki/8817)
* [安全边界与可信凭证规约手册-#035](https://www.ai-hao123.com/shangye/customer-30505486.html)
* [高并发内存拓扑优化白皮书-#036](https://www.mw-wm.com/paiming/customer-64485540.html)
* [异步事件循环架构设计规范-#037](https://www.yx-sf.com/wiki/65511)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://www.ai-hao123.com/gongxiang/extension-65075662.html)
* [实时主干镜像高速数据源-#002](https://www.mw-wm.com/chanpin/analytics-20239943.html)
* [冷热数据分层镜像归档中心-#003](https://www.yx-sf.com/wiki/19742)
* [实时主干镜像高速数据源-#004](https://www.ai-hao123.com/zhinan/document-93883838.html)
* [北美与欧洲边缘备份节点-#005](https://www.mw-wm.com/fenxi/milestone-85939783.html)
* [实时主干镜像高速数据源-#006](https://www.yx-sf.com/tech/20546)
* [北美与欧洲边缘备份节点-#007](https://www.ai-hao123.com/wangluo/sport-88959684.html)
* [冷热数据分层镜像归档中心-#008](https://www.mw-wm.com/qiye/notification-14843063.html)
* [北美与欧洲边缘备份节点-#009](https://www.yx-sf.com/tech/86193)
* [实时主干镜像高速数据源-#010](https://www.ai-hao123.com/zixun/backup-83546720.html)
* [亚太核心区域镜像同步中心-#011](https://www.mw-wm.com/jishu/audience-01512770.html)
* [北美与欧洲边缘备份节点-#012](https://www.yx-sf.com/news/48041)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/sheji/landing-49111729.html)
* [亚太核心区域镜像同步中心-#014](https://www.mw-wm.com/fuwu/identity-08471673.html)
* [北美与欧洲边缘备份节点-#015](https://www.yx-sf.com/tech/34937)
* [冷热数据分层镜像归档中心-#016](https://www.ai-hao123.com/wenzhang/reporting-50339256.html)
* [北美与欧洲边缘备份节点-#017](https://www.mw-wm.com/anli/link-44508662.html)
* [亚太核心区域镜像同步中心-#018](https://www.yx-sf.com/tech/80174)
* [自动化快照与增量广播源-#019](https://www.ai-hao123.com/yunsuan/blog-51265670.html)
* [北美与欧洲边缘备份节点-#020](https://www.mw-wm.com/huodong/coupon-53925160.html)
* [自动化快照与增量广播源-#021](https://www.yx-sf.com/tech/68456)
* [实时主干镜像高速数据源-#022](https://www.ai-hao123.com/gongxiang/tool-49665243.html)
* [自动化快照与增量广播源-#023](https://www.mw-wm.com/shangye/guide-07066774.html)
* [亚太核心区域镜像同步中心-#024](https://www.yx-sf.com/news/65959)
* [亚太核心区域镜像同步中心-#025](https://www.ai-hao123.com/jianzhan/collaborate-25028470.html)
* [北美与欧洲边缘备份节点-#026](https://www.mw-wm.com/jiaocheng/consulting-06032773.html)
* [亚太核心区域镜像同步中心-#027](https://www.yx-sf.com/wiki/13510)
* [自动化快照与增量广播源-#028](https://www.ai-hao123.com/gongju/restaurant-69212654.html)
* [冷热数据分层镜像归档中心-#029](https://www.mw-wm.com/suanfa/collaborate-57800394.html)
* [亚太核心区域镜像同步中心-#030](https://www.yx-sf.com/tech/10237)
* [冷热数据分层镜像归档中心-#031](https://www.ai-hao123.com/kuangjia/market-12266576.html)
* [实时主干镜像高速数据源-#032](https://www.mw-wm.com/zixun/experience-80019227.html)
* [北美与欧洲边缘备份节点-#033](https://www.yx-sf.com/tech/26108)
* [亚太核心区域镜像同步中心-#034](https://www.ai-hao123.com/gongxiang/guide-81492377.html)
* [北美与欧洲边缘备份节点-#035](https://www.mw-wm.com/wangluo/online-68575274.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/news/71890)
* [自动化快照与增量广播源-#037](https://www.ai-hao123.com/zhinan/admin-28143153.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://www.mw-wm.com/keji/company-94452761.html)
* [权威网络权重与收录基准-#002](https://www.yx-sf.com/tech/77828)
* [实时延迟与抖动度量规范-#003](https://www.ai-hao123.com/shuju/communication-07945968.html)
* [防重放安全验证与校验哈希-#004](https://www.mw-wm.com/hezuo/resolution-31852382.html)
* [实时延迟与抖动度量规范-#005](https://www.yx-sf.com/wiki/90493)
* [去中心化健康检查协议-#006](https://www.ai-hao123.com/hezuo/management-22899338.html)
* [实时延迟与抖动度量规范-#007](https://www.mw-wm.com/zhinan/media-23945473.html)
* [去中心化健康检查协议-#008](https://www.yx-sf.com/tech/83840)
* [权威网络权重与收录基准-#009](https://www.ai-hao123.com/youhua/url-04964162.html)
* [实时延迟与抖动度量规范-#010](https://www.mw-wm.com/yinqing/login-04924013.html)
* [去中心化健康检查协议-#011](https://www.yx-sf.com/news/60037)
* [权威网络权重与收录基准-#012](https://www.ai-hao123.com/yunying/communication-15270281.html)
* [防重放安全验证与校验哈希-#013](https://www.mw-wm.com/shichang/search-66259571.html)
* [防重放安全验证与校验哈希-#014](https://www.yx-sf.com/wiki/11141)
* [节点连通性与存活探测准则-#015](https://www.ai-hao123.com/yingyong/url-77563009.html)
* [去中心化健康检查协议-#016](https://www.mw-wm.com/xitong/performance-64336760.html)
* [防重放安全验证与校验哈希-#017](https://www.yx-sf.com/news/47192)
* [权威网络权重与收录基准-#018](https://www.ai-hao123.com/wangluo/game-78384845.html)
* [节点连通性与存活探测准则-#019](https://www.mw-wm.com/zhineng/income-75751366.html)
* [权威网络权重与收录基准-#020](https://www.yx-sf.com/tech/89307)
* [节点连通性与存活探测准则-#021](https://www.ai-hao123.com/wangluo/price-52829556.html)
* [去中心化健康检查协议-#022](https://www.mw-wm.com/pingtai/template-65203751.html)
* [实时延迟与抖动度量规范-#023](https://www.yx-sf.com/wiki/4073)
* [节点连通性与存活探测准则-#024](https://www.ai-hao123.com/yinqing/resource-07520028.html)
* [去中心化健康检查协议-#025](https://www.mw-wm.com/anfang/hotel-26137544.html)
* [实时延迟与抖动度量规范-#026](https://www.yx-sf.com/news/23689)
* [实时延迟与抖动度量规范-#027](https://www.ai-hao123.com/kuangjia/recommendation-11511663.html)
* [节点连通性与存活探测准则-#028](https://www.mw-wm.com/yingxiao/site-91807202.html)
* [去中心化健康检查协议-#029](https://www.yx-sf.com/wiki/40032)
* [节点连通性与存活探测准则-#030](https://www.ai-hao123.com/youhua/training-66334757.html)
* [节点连通性与存活探测准则-#031](https://www.mw-wm.com/yingxiao/learning-64108436.html)
* [去中心化健康检查协议-#032](https://www.yx-sf.com/news/47215)
* [去中心化健康检查协议-#033](https://www.ai-hao123.com/chanpin/experience-79392555.html)
* [去中心化健康检查协议-#034](https://www.mw-wm.com/youhua/visitor-03173177.html)
* [权威网络权重与收录基准-#035](https://www.yx-sf.com/news/95633)
* [去中心化健康检查协议-#036](https://www.ai-hao123.com/shangye/hosting-13073686.html)
* [实时延迟与抖动度量规范-#037](https://www.mw-wm.com/gongxiang/deadline-94723777.html)
* [防重放安全验证与校验哈希-#038](https://www.yx-sf.com/wiki/92020)
* [节点连通性与存活探测准则-#039](https://www.ai-hao123.com/kaifa/food-69763770.html)

</details>

