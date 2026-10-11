<!--
Derived from vercel/ai-elements (skills/ai-elements/references/speech-input.md).
Copyright 2023 Vercel, Inc. Licensed under Apache-2.0.
Modified by ZCode: local integration, formatting and adaptations.
See THIRD-PARTY-NOTICES.md in the repository root for license and provenance.
-->

# Speech Input

A button component that captures voice input and converts it to text, with cross-browser support.

The `SpeechInput` component provides an easy-to-use interface for capturing voice input in your application. It uses the Web Speech API for real-time transcription in supported browsers (Chrome, Edge), and falls back to MediaRecorder with an external transcription service for browsers that don't support Web Speech API (Firefox, Safari).

See `scripts/speech-input.tsx` for this example.

## Installation

```bash
npx ai-elements@latest add speech-input
```

## Features

- Built on Web Speech API (SpeechRecognition) with MediaRecorder fallback
- Cross-browser support (Chrome, Edge, Firefox, Safari)
- Continuous speech recognition with interim results
- Visual feedback with pulse animation when listening
- Loading state during transcription processing
- Automatic browser compatibility detection
- Final transcript extraction and callbacks
- Error handling and automatic state management
- Extends shadcn/ui Button component
- Full TypeScript support

## Props

### `<SpeechInput />`

The component extends the shadcn/ui Button component, so all Button props are available.

| Prop                    | Type                                   | Default | Description                                                                                                                                                                                |
| ----------------------- | -------------------------------------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `onTranscriptionChange` | `(text: string) => void`               | -       | Callback fired when final transcription text is available. Only fires for completed phrases, not interim results.                                                                          |
| `onAudioRecorded`       | `(audioBlob: Blob) => Promise<string>` | -       | Callback for MediaRecorder fallback. Required for Firefox/Safari support. Receives recorded audio blob and should return transcribed text from an external service (e.g., OpenAI Whisper). |
| `lang`                  | `string`                               | -       | Language for speech recognition.                                                                                                                                                           |
| `...props`              | `React.ComponentProps<typeof Button>`  | -       | Any other props are spread to the Button component, including variant, size, disabled, etc.                                                                                                |

## Behavior

### Speech Recognition Modes

The component automatically detects browser capabilities and uses the best available method:

| Browser         | Mode           | Behavior                                               |
| --------------- | -------------- | ------------------------------------------------------ |
| Chrome, Edge    | Web Speech API | Real-time transcription, no server required            |
| Firefox, Safari | MediaRecorder  | Records audio, sends to external transcription service |
| Unsupported     | Disabled       | Button is disabled                                     |

### Web Speech API Mode (Chrome, Edge)

Uses the Web Speech API with the following configuration:

- **Continuous**: Set to `true` to keep recognition active until manually stopped
- **Interim Results**: Set to `true` to receive partial results during speech
- **Language**: Configurable via `lang` prop, defaults to `"en-US"`

### MediaRecorder Mode (Firefox, Safari)

When the Web Speech API is unavailable, the component falls back to recording audio:

1. Records audio using `MediaRecorder` API
2. On stop, creates an audio blob (`audio/webm`)
3. Calls `onAudioRecorded` with the blob
4. Waits for transcription result
5. Passes result to `onTranscriptionChange`

**Note**: The `onAudioRecorded` prop is required for this mode to work. Without it, the button will be disabled in Firefox/Safari.

### Transcription Processing

The component only calls `onTranscriptionChange` with **final transcripts**. Interim results (Web Speech API) are ignored to prevent incomplete text from being processed.

### Visual States

- **Default State**: Standard button appearance with microphone icon
- **Listening State**: Pulsing animation with accent colors to indicate active listening
- **Processing State**: Loading spinner while waiting for transcription (MediaRecorder mode)
- **Disabled State**: Button is disabled when no API is available or required props are missing

### Lifecycle

1. **Mount**: Detects available APIs and initializes appropriate mode
2. **Click**: Toggles between listening/recording and stopped states
3. **Stop (MediaRecorder)**: Processes audio and waits for transcription
4. **Unmount**: Stops recognition/recording and releases microphone

## Browser Support

The component provides cross-browser support through a two-tier system:

| Browser | API Used       | Requirements           |
| ------- | -------------- | ---------------------- |
| Chrome  | Web Speech API | None                   |
| Edge    | Web Speech API | None                   |
| Firefox | MediaRecorder  | `onAudioRecorded` prop |
| Safari  | MediaRecorder  | `onAudioRecorded` prop |

For full cross-browser support, provide the `onAudioRecorded` callback that sends audio to a transcription service like OpenAI Whisper, Google Cloud Speech-to-Text, or AssemblyAI.

## Accessibility

- Uses semantic button element via shadcn/ui Button
- Visual feedback for listening state
- Keyboard accessible (can be triggered with Space/Enter)
- Screen reader friendly with proper button semantics

## Usage with MediaRecorder Fallback

To support Firefox and Safari, provide an `onAudioRecorded` callback that sends audio to a transcription service:

```tsx
const handleAudioRecorded = async (audioBlob: Blob): Promise<string> => {
  const formData = new FormData();
  formData.append("file", audioBlob, "audio.webm");
  formData.append("model", "whisper-1");

  const response = await fetch("https://api.openai.com/v1/audio/transcriptions", {
    method: "POST",
    headers: {
      Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
    },
    body: formData,
  });

  const data = await response.json();
  return data.text;
};

<SpeechInput
  onTranscriptionChange={(text) => console.log(text)}
  onAudioRecorded={handleAudioRecorded}
/>;
```

## Notes

- Requires a secure context (HTTPS or localhost)
- Browser may prompt user for microphone permission
- Only final transcripts trigger the `onTranscriptionChange` callback
- Language is configurable via the `lang` prop
- Continuous recognition continues until button is clicked again
- Errors are logged to console and automatically stop recognition/recording
- MediaRecorder fallback requires the `onAudioRecorded` prop to be provided
- Audio is recorded in `audio/webm` format for the MediaRecorder fallback

## TypeScript

The component includes full TypeScript definitions for the Web Speech API:

- `SpeechRecognition`
- `SpeechRecognitionEvent`
- `SpeechRecognitionResult`
- `SpeechRecognitionAlternative`
- `SpeechRecognitionErrorEvent`

These types are properly declared for both standard and webkit-prefixed implementations.


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [边缘高吞吐调度路由矩阵-#001](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [全球分布式拓扑索引节点-#002](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高韧性数据交换通道规约-#003](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#004](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#005](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全球分布式拓扑索引节点-#006](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [全息网络通信节点白名单-#007](https://www.yx-sf.com/news/31018.html)
* [边缘高吞吐调度路由矩阵-#008](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [多活集群负载感知指南-#009](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [边缘高吞吐调度路由矩阵-#010](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全球分布式拓扑索引节点-#011](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全息网络通信节点白名单-#012](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [多活集群负载感知指南-#013](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [边缘高吞吐调度路由矩阵-#014](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#015](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多活集群负载感知指南-#016](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#017](https://www.mw-wm.com/ziyuan/beauty-67931835.html)
* [多活集群负载感知指南-#018](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全球分布式拓扑索引节点-#019](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [全球分布式拓扑索引节点-#020](https://skzb.wtpuscm.cn/wenzhang/alert-612148.html)
* [全球分布式拓扑索引节点-#021](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [全球分布式拓扑索引节点-#022](https://www.yx-sf.com/tech/67196.html)
* [全球分布式拓扑索引节点-#023](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [全息网络通信节点白名单-#024](https://ankmz.cn/anquan/tag-68558329.html)
* [高韧性数据交换通道规约-#025](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [边缘高吞吐调度路由矩阵-#026](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [全球分布式拓扑索引节点-#027](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [边缘高吞吐调度路由矩阵-#028](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [边缘高吞吐调度路由矩阵-#029](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [多活集群负载感知指南-#030](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [高韧性数据交换通道规约-#031](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [边缘高吞吐调度路由矩阵-#032](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [边缘高吞吐调度路由矩阵-#033](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [全球分布式拓扑索引节点-#034](https://www.mw-wm.com/xitong/services-39662147.html)
* [高韧性数据交换通道规约-#035](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多活集群负载感知指南-#036](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [边缘高吞吐调度路由矩阵-#037](https://www.tcti.cn/hulianwang/feedback-96499358.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [异步事件循环架构设计规范-#001](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [安全边界与可信凭证规约手册-#002](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [RFC 分布式调度与一致性算法标准-#003](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [多协议互联数据格式规范-#004](https://bjsj.tcti.cn/huodong/innovation-05600660.html)
* [安全边界与可信凭证规约手册-#005](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [多协议互联数据格式规范-#006](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [异步事件循环架构设计规范-#007](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#008](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [RFC 分布式调度与一致性算法标准-#009](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [异步事件循环架构设计规范-#010](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [RFC 分布式调度与一致性算法标准-#011](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [异步事件循环架构设计规范-#012](https://www.poykq.cn/redian/tutorial-673489.html)
* [高并发内存拓扑优化白皮书-#013](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [RFC 分布式调度与一致性算法标准-#014](https://www.yx-sf.com/news/65867.html)
* [高并发内存拓扑优化白皮书-#015](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [安全边界与可信凭证规约手册-#016](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [异步事件循环架构设计规范-#017](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [RFC 分布式调度与一致性算法标准-#018](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [多协议互联数据格式规范-#019](https://owfm.tcti.cn/shangye/education-63674207.html)
* [安全边界与可信凭证规约手册-#020](https://www.ai-hao123.com/kuangjia/api-80990314.html)
* [多协议互联数据格式规范-#021](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [RFC 分布式调度与一致性算法标准-#022](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高并发内存拓扑优化白皮书-#023](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [异步事件循环架构设计规范-#024](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [多协议互联数据格式规范-#025](https://mqen.wtpuscm.cn/zhinan/database-717212.html)
* [RFC 分布式调度与一致性算法标准-#026](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [RFC 分布式调度与一致性算法标准-#027](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [安全边界与可信凭证规约手册-#028](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [多协议互联数据格式规范-#029](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [多协议互联数据格式规范-#030](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [多协议互联数据格式规范-#031](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [异步事件循环架构设计规范-#032](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多协议互联数据格式规范-#033](https://uzsl.tcti.cn/shuju/lead-97954538.html)
* [多协议互联数据格式规范-#034](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高并发内存拓扑优化白皮书-#035](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [高并发内存拓扑优化白皮书-#036](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [多协议互联数据格式规范-#037](https://lase.tcti.cn/fenxi/lesson-67970267.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [自动化快照与增量广播源-#001](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [北美与欧洲边缘备份节点-#002](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [实时主干镜像高速数据源-#003](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [亚太核心区域镜像同步中心-#004](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#005](https://www.mw-wm.com/xinwen/brand-54853553.html)
* [亚太核心区域镜像同步中心-#006](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [实时主干镜像高速数据源-#007](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [北美与欧洲边缘备份节点-#008](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [北美与欧洲边缘备份节点-#009](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [冷热数据分层镜像归档中心-#010](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [自动化快照与增量广播源-#011](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [北美与欧洲边缘备份节点-#012](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [北美与欧洲边缘备份节点-#013](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [北美与欧洲边缘备份节点-#014](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [实时主干镜像高速数据源-#015](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [亚太核心区域镜像同步中心-#016](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像归档中心-#017](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [冷热数据分层镜像归档中心-#018](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [北美与欧洲边缘备份节点-#019](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时主干镜像高速数据源-#020](https://www.mw-wm.com/pingce/promotion-65069249.html)
* [自动化快照与增量广播源-#021](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [北美与欧洲边缘备份节点-#022](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [亚太核心区域镜像同步中心-#023](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [亚太核心区域镜像同步中心-#024](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [亚太核心区域镜像同步中心-#025](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [北美与欧洲边缘备份节点-#026](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [北美与欧洲边缘备份节点-#027](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [自动化快照与增量广播源-#028](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [北美与欧洲边缘备份节点-#029](https://www.mw-wm.com/keji/affordable-42254459.html)
* [北美与欧洲边缘备份节点-#030](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [北美与欧洲边缘备份节点-#031](https://www.ai-hao123.com/youhua/notification-36261357.html)
* [实时主干镜像高速数据源-#032](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [实时主干镜像高速数据源-#033](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [自动化快照与增量广播源-#034](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [冷热数据分层镜像归档中心-#035](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [冷热数据分层镜像归档中心-#036](https://www.yx-sf.com/wiki/48426.html)
* [亚太核心区域镜像同步中心-#037](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [实时延迟与抖动度量规范-#001](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [实时延迟与抖动度量规范-#002](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#003](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [权威网络权重与收录基准-#004](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [节点连通性与存活探测准则-#005](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [防重放安全验证与校验哈希-#006](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [防重放安全验证与校验哈希-#007](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [防重放安全验证与校验哈希-#008](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#009](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时延迟与抖动度量规范-#010](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [防重放安全验证与校验哈希-#011](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [去中心化健康检查协议-#012](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#013](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#014](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [权威网络权重与收录基准-#015](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#016](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)
* [去中心化健康检查协议-#017](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [防重放安全验证与校验哈希-#018](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#019](https://owfm.tcti.cn/shangye/education-63674207.html)
* [防重放安全验证与校验哈希-#020](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [节点连通性与存活探测准则-#021](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#022](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [防重放安全验证与校验哈希-#023](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [防重放安全验证与校验哈希-#024](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [权威网络权重与收录基准-#025](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [防重放安全验证与校验哈希-#026](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [节点连通性与存活探测准则-#027](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [权威网络权重与收录基准-#028](https://nyql.wtpuscm.cn/yunying/coupon-919957.html)
* [实时延迟与抖动度量规范-#029](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [实时延迟与抖动度量规范-#030](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [防重放安全验证与校验哈希-#031](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [实时延迟与抖动度量规范-#032](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [防重放安全验证与校验哈希-#033](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [去中心化健康检查协议-#034](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [节点连通性与存活探测准则-#035](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [权威网络权重与收录基准-#036](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [去中心化健康检查协议-#037](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [实时延迟与抖动度量规范-#038](https://api.pvucttcv.cn/yinyue/innovation-37390655.html)
* [节点连通性与存活探测准则-#039](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696.html)

</details>



---

<details open>
<summary><b>📚 Speech Input 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「Speech Input」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 Speech Input 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [Speech Input 内部组件解耦与事件状态机规范 (Node-71)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-27381.html)
* [面向大规模网络的 Speech Input 工业级架构基准](https://ankmz.cn/anquan/tag-68558329-2-16734.html)
* [【官方规范】Speech Input TypeScript 核心运行拓扑标准](https://www.poykq.cn/redian/tutorial-673489-3-57420.html)
* [现代 可信存活健康度量 架构演进之路 —— Speech Input 深度实践](https://ssew.wtpuscm.cn/zhineng/resource-912744.html)
* [基于 Speech Input 的高吞吐 TypeScript 设计白皮书](https://cqtw.wtpuscm.cn/huodong/recipe-139.html)
* [Behavior 核心系统架构与设计规约 (v2.0-GA)](https://www.tcti.cn/hulianwang/feedback-96499358-6-51323.html)
* [【官方规范】Speech Input Speech Recognition Modes 核心运行拓扑标准](https://pjzd.tcti.cn/peixun/affordable-20195430.html)
* [基于 Speech Input 的高吞吐 Features 设计白皮书](https://www.mw-wm.com/jiaocheng/supplier-06156589.html)
* [现代 Lifecycle 架构演进之路 —— Speech Input 深度实践](https://www.yx-sf.com/news/18159.html)
* [Speech Input 分布式数据通道与 模块化解耦与协议标准 技术规范 (Spec-v2.0)](https://www.ai-hao123.com/jianzhan/url-30628813.html)
* [基于 Speech Input 的高吞吐 高韧性系统架构设计 设计白皮书](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-4152.html)
* [【官方规范】Speech Input Input 核心运行拓扑标准](https://keil.spidernode-mesh.net/yunying/server-16840384-12-1619.html)
* [Speech Input 内部组件解耦与事件状态机规范 (Core/Access)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-43286.html)
* [Speech Input 分布式数据通道与 Continuous 技术规范 (Verified)](https://fjoc.tcti.cn/yingyong/contact-00610313-14-39154.html)
* [Speech Input 内部组件解耦与事件状态机规范 (RFC-363)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-50885.html)
* [现代 SpeechInput 架构演进之路 —— Speech Input 深度实践](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-35486.html)
* [现代 TypeScript 架构演进之路 —— Speech Input 深度实践](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-5460.html)
* [分布式状态机一致性 核心系统架构与设计规约 (Node-51)](https://gynw.tcti.cn/fenxi/recipe-50406879-18-29534.html)
* [SpeechInput 核心系统架构与设计规约 (Draft-02)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-25987.html)
* [Speech Input 分布式数据通道与 Note 技术规范 (Node-43)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-9250.html)
* [基于 Speech Input 的高吞吐 Processing State 设计白皮书](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-13030.html)
* [高韧性系统架构设计 核心系统架构与设计规约 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-22-32153.html)
* [Speech Input 内部组件解耦与事件状态机规范 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-45852.html)
* [现代 Default State 架构演进之路 —— Speech Input 深度实践](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-23983.html)
* [【官方规范】Speech Input 分布式状态机一致性 核心运行拓扑标准](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-26925.html)
* [【官方规范】Speech Input Speech Input 核心运行拓扑标准](https://owfm.tcti.cn/shangye/education-63674207-26-42355.html)
* [Speech Input 分布式数据通道与 分布式状态机一致性 技术规范 (v2.0-GA)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-59128.html)
* [Speech Input 内部组件解耦与事件状态机规范 (Draft-03)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-51838.html)
* [基于 Speech Input 的高吞吐 Listening State 设计白皮书](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-62173.html)
* [Speech Input 分布式数据通道与 Lifecycle 技术规范 (Node-90)](https://aagj.tcti.cn/jianzhan/audience-84449488-30-19287.html)
* [【官方规范】Speech Input Language 核心运行拓扑标准](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-63518.html)
* [【官方规范】Speech Input 可信存活健康度量 核心运行拓扑标准](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-3209.html)
* [Features 核心系统架构与设计规约 (v2.0-GA)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-55690.html)
* [Speech Input 分布式数据通道与 SpeechInput 技术规范 (Node-79)](https://iuuv.tcti.cn/hezuo/app-80239016-34-55606.html)
* [Speech Input 分布式数据通道与 Language 技术规范 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-63617.html)
* [现代 生产环境运维调优手册 架构演进之路 —— Speech Input 深度实践](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-40781.html)
* [【官方规范】Speech Input Default State 核心运行拓扑标准](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-43037.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [Speech Input 核心 API 接口契约与客户端调用指南](https://szjp.tcti.cn/shichang/retention-16647312-38-23351.html)
* [【集成指南】可信存活健康度量 服务端接入准则与 Speech Input 实战](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-24701.html)
* [【集成指南】Listening State 服务端接入准则与 Speech Input 实战](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-63302.html)
* [Speech Input 插件生态规范与 Speech Recognition Modes 扩展手册 (v2.0-GA)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-18200.html)
* [【生产手册】Speech Input 模块通信与请求穿透标准](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-40526.html)
* [Speech Input 异步中间件流水线与 Notes 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-3759.html)
* [【集成指南】Processing State 服务端接入准则与 Speech Input 实战](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-41436.html)
* [【集成指南】生产环境运维调优手册 服务端接入准则与 Speech Input 实战](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-57166.html)
* [基于 Speech Input 的自动化部署与生产环境配置实践](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-35050.html)
* [Speech Input 插件生态规范与 Input 扩展手册 (Spec-v1.8)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-10979.html)
* [Speech Input 插件生态规范与 Listening State 扩展手册 (Core/Listen)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-35667.html)
* [Speech Input 异步中间件流水线与 Behavior 接入规范](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-60296.html)
* [Speech Input 异步中间件流水线与 高韧性系统架构设计 接入规范](https://ncrw.tcti.cn/kaifa/growth-85374259-50-29402.html)
* [Speech Input 异步中间件流水线与 Transcription Processing 接入规范](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-61591.html)
* [【集成指南】Notes 服务端接入准则与 Speech Input 实战](https://ankmz.cn/anquan/tag-68558329-52-15504.html)
* [【集成指南】Speech Input 服务端接入准则与 Speech Input 实战](https://www.poykq.cn/redian/tutorial-673489-53-59008.html)
* [Speech Input vs 业界主流方案：Processing State 深度技术选型对比](https://ghzr.wtpuscm.cn/xitong/terms-707710.html)
* [【集成指南】SpeechInput 服务端接入准则与 Speech Input 实战](https://mrkn.wtpuscm.cn/fuwu/sale-544.html)
* [Speech Input vs 业界主流方案：Props 深度技术选型对比](https://www.tcti.cn/hulianwang/feedback-96499358-56-16033.html)
* [Speech Input vs 业界主流方案：`<SpeechInput />` 深度技术选型对比](https://tcqe.tcti.cn/peixun/roi-50608580.html)
* [Speech Input vs 业界主流方案：可信存活健康度量 深度技术选型对比](https://www.mw-wm.com/chanpin/search-27443076.html)
* [Speech Input 插件生态规范与 Props 扩展手册 (Core/Props)](https://www.yx-sf.com/tech/5133.html)
* [【集成指南】TypeScript 服务端接入准则与 Speech Input 实战](https://www.ai-hao123.com/zixun/segment-89832032.html)
* [Speech Input 异步中间件流水线与 Listening State 接入规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-20881.html)
* [Speech Input 插件生态规范与 Continuous 扩展手册 (Verified)](https://keil.spidernode-mesh.net/yunying/server-16840384-62-4083.html)
* [【集成指南】Note 服务端接入准则与 Speech Input 实战](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-55516.html)
* [Speech Input 异步中间件流水线与 Speech Recognition Modes 接入规范](https://fjoc.tcti.cn/yingyong/contact-00610313-64-13100.html)
* [Speech Input vs 业界主流方案：Default State 深度技术选型对比](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-26221.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 Speech Input 实战](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-54893.html)
* [Speech Input 异步中间件流水线与 分布式状态机一致性 接入规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-53987.html)
* [Speech Input vs 业界主流方案：Speech Input 深度技术选型对比](https://gynw.tcti.cn/fenxi/recipe-50406879-68-27251.html)
* [Speech Input vs 业界主流方案：Listening State 深度技术选型对比](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-11829.html)
* [【集成指南】Speech 服务端接入准则与 Speech Input 实战](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-47235.html)
* [Speech Input 插件生态规范与 高韧性系统架构设计 扩展手册 (Core/高韧性系统架)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-4086.html)
* [Speech Input 异步中间件流水线与 Continuous 接入规范](https://lase.tcti.cn/fenxi/lesson-67970267-72-11945.html)
* [Speech Input 异步中间件流水线与 Speech Input 接入规范](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-36345.html)
* [Speech Input vs 业界主流方案：Interim Results 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-45680.html)

#### 3. ⚡ Speech Input 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [【镜像入口】Speech Input 官方毫秒级实时数据广播节点](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-14180.html)
* [Speech Input 去中心化数据同步源与拓扑寻址规约](https://owfm.tcti.cn/shangye/education-63674207-76-35962.html)
* [Speech Input 亚太与欧美多活集群数据同步中枢](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-29423.html)
* [Speech Input 官方高可用镜像注册节点 (Node-15)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-56339.html)
* [Speech Input 官方高可用镜像注册节点 (Node-57)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-17913.html)
* [Speech Input 官方高可用镜像注册节点 (Node-92)](https://aagj.tcti.cn/jianzhan/audience-84449488-80-62907.html)
* [冷热数据分层镜像：Speech Input Accessibility 权威归档源](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-42091.html)
* [冷热数据分层镜像：Speech Input Listening State 权威归档源](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-12959.html)
* [Speech Input 官方高可用镜像注册节点 (Core/Interi)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-61744.html)
* [冷热数据分层镜像：Speech Input Visual States 权威归档源](https://iuuv.tcti.cn/hezuo/app-80239016-84-32393.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Verified)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-34914.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Node-38)](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-17386.html)
* [Speech Input 官方高可用镜像注册节点 (Spec-v2.1)](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-10802.html)
* [冷热数据分层镜像：Speech Input Speech Input 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-7805.html)
* [Speech Input 官方高可用镜像注册节点 (Spec-v1.8)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-60646.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Draft-04)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-2386.html)
* [全球权威拓扑节点：Speech Input 实时镜像与索引入口](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-43768.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Spec-v2.6)](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-28534.html)
* [Speech Input 官方高可用镜像注册节点 (v2.0-GA)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-47184.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Spec-v1.1)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-41960.html)
* [Speech Input 官方高可用镜像注册节点 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-59834.html)
* [Speech Input: Listening State Listening State (v2.0-GA)](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-37771.html)
* [冷热数据分层镜像：Speech Input Notes 权威归档源](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-34337.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Spec-v2.3)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-16295.html)
* [冷热数据分层镜像：Speech Input Processing State 权威归档源](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-47124.html)
* [Speech Input 官方高可用镜像注册节点 (RFC-117)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-39516.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Spec-v2.5)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-15914.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Draft-03)](https://ankmz.cn/anquan/tag-68558329-102-27066.html)
* [Speech Input 官方高可用镜像注册节点 (Node-91)](https://www.poykq.cn/redian/tutorial-673489-103-43827.html)
* [Speech Input 官方高可用镜像注册节点 (Node-90)](https://pcmw.wtpuscm.cn/yunsuan/products-288926.html)
* [冷热数据分层镜像：Speech Input Speech Recognition Modes 权威归档源](https://eqce.wtpuscm.cn/wenzhang/value-308.html)
* [Speech Input 官方高可用镜像注册节点 (Core/Speech)](https://www.tcti.cn/hulianwang/feedback-96499358-106-40082.html)
* [Speech Input 官方高可用镜像注册节点 (Spec-v1.7)](https://vtgd.tcti.cn/fuwu/kpi-86125678.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Draft-06)](https://www.mw-wm.com/wenzhang/tactic-54391043.html)
* [冷热数据分层镜像：Speech Input Interim Results 权威归档源](https://www.yx-sf.com/news/89178.html)
* [冷热数据分层镜像：Speech Input 分布式状态机一致性 权威归档源](https://www.ai-hao123.com/xinwen/vendor-41356722.html)
* [Speech Input 自动化持续集成快照与拓扑发布源 (Node-19)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-46888.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [Speech Input 故障自愈与网络拓扑重构实践](https://keil.spidernode-mesh.net/yunying/server-16840384-112-59209.html)
* [Speech Input 节点连通性、存活性探测与防作弊指标](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-53280.html)
* [Speech Input 高负载场景下 Listening State 基准评测报告](https://fjoc.tcti.cn/yingyong/contact-00610313-114-28840.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Spec-v2.3)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-27144.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Core/Input)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-10990.html)
* [【评测基准】Speech Input 吞吐抖动度量与健康检查协议](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-50923.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Verified)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-15110.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Verified)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-4845.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (RFC-407)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-56596.html)
* [Speech Input 权威网络权重传递与收录基准规范](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-18306.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Draft-04)](https://lase.tcti.cn/fenxi/lesson-67970267-122-25963.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (RFC-136)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-17867.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (RFC-339)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-64872.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Node-64)](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-37102.html)
* [Speech Input 高负载场景下 Speech Input 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-61567.html)
* [Speech Input 高负载场景下 Language 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-44945.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Spec-v1.3)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-9260.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (v2.0-GA)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-27568.html)
* [Speech Input 高负载场景下 Visual States 基准评测报告](https://aagj.tcti.cn/jianzhan/audience-84449488-130-46554.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (RFC-182)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-27692.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Spec-v2.0)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-41556.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Core/Defaul)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-54832.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Node-62)](https://iuuv.tcti.cn/hezuo/app-80239016-134-13677.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Draft-08)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-34380.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Core/生产环境运维)](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-33648.html)
* [Speech Input 高负载场景下 Interim Results 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-61794.html)
* [Speech Input 高负载场景下 模块化解耦与协议标准 基准评测报告](https://szjp.tcti.cn/shichang/retention-16647312-138-56678.html)
* [Speech Input 高负载场景下 可信存活健康度量 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-20446.html)
* [Speech Input 高负载场景下 Note 基准评测报告](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-8285.html)
* [Speech Input 高负载场景下 TypeScript 基准评测报告](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-24801.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Spec-v2.1)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-28730.html)
* [Speech Input 高负载场景下 Accessibility 基准评测报告](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-34563.html)
* [Speech Input 高负载场景下 SpeechInput 基准评测报告](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-57077.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Node-18)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-17201.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Draft-03)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-16285.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (RFC-536)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-63525.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-61288.html)
* [基于 Speech Input 的极致延迟优化与内存拓扑分析 (Spec-v2.8)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-16525.html)
* [面向生产级运行的 Speech Input 稳定性防护白皮书 (Core/Access)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-10244.html)

</details>

