# PWM Controller

> 带登录鉴权的 8 通道 PWM 串口 Web 上位机

## 项目简介

本项目名为 **PWM Controller**，是一套基于 Node.js + Express 开发的轻量化硬件控制 Web 上位机，内置 Session 登录校验机制；前端采用原生 Web Serial API 直连 USB 串口设备，可可视化调节 8 路 PWM 信号的频率与占空比。

项目提供完整 Dockerfile，基于 `node:18-alpine` 轻量镜像打包，无需本地安装 Node 运行环境，支持 Windows / macOS / Linux 全平台一键部署。

## 核心功能

### 1. 登录权限管控
- 基于 `express-session` 实现会话管理，登录有效期 1 小时
- 未登录用户自动跳转登录页面，保护硬件控制权限
- 默认账号：`admin` / 默认密码：`admin123`
- 支持一键登出，销毁当前会话

### 2. 8 通道 PWM 可视化调节
- 全局统一频率设置，支持 1Hz ~ 150KHz 范围
- 分组占空比调节（CH1-CH3 / CH4-CH6 / CH7-CH8）
- 滑块与数字输入双模式调节，占空比范围 0~100%
- 实时串口收发日志，便于调试

### 3. 浏览器原生串口通信
- 采用标准 Web Serial API 直接操作 USB 串口
- 无需额外安装串口客户端软件
- 本地 `localhost` 访问可直接使用 HTTP
- 局域网 / 公网远程访问必须部署 HTTPS

### 4. 容器化零依赖部署
- 容器内自动安装生产环境依赖
- 镜像体积小、启动速度快
- 一条命令即可拉起完整服务
- 环境隔离，跨设备迁移便捷

### 5. 极简轻量化架构
- 后端仅依赖 Express + 会话中间件
- 前端纯原生 HTML/CSS/JS，无第三方框架
- 资源占用低，适合嵌入式、工控场景

## 端口说明

| 端口类型 | 端口号 | 说明 |
|---------|--------|------|
| 容器内部服务端口 | `8080` | Node 服务在容器内固定监听 8080，`EXPOSE 8080` 仅作文档标识，不会自动暴露端口 |
| 本机映射端口 | 自定义 | 通过 `-p 本机端口:8080` 映射，例如 `-p 8080:8080` 或 `-p 9000:8080` |

> **注意**：8080 是 Web 服务网络端口；COM3、`/dev/ttyUSB0` 等为硬件物理串口，二者完全独立，不存在端口冲突。

## Web Serial API 必须使用 HTTPS 的原因

浏览器出于硬件安全策略，串口、USB、摄像头等硬件 API 仅允许在 **安全上下文（Secure Context）** 中调用：

| 访问场景 | 协议要求 | 说明 |
|---------|---------|------|
| 本地访问 `localhost` / `127.0.0.1` | HTTP 即可 | 浏览器特殊安全豁免，可正常调用串口 |
| 局域网 IP / 公网域名远程访问 | 必须 HTTPS | 否则 `navigator.serial` 对象不存在，串口功能直接失效 |

### 安全底层逻辑

1. **硬件权限高危**：串口可读写单片机、PLC、电机控制器等设备，明文 HTTP 站点易被中间人劫持，恶意脚本可静默操控硬件，造成设备失控
2. **防止页面篡改**：HTTPS 全程加密传输，杜绝页面脚本被篡改、伪造串口控制指令
3. **W3C 统一规范**：Web Serial、WebUSB、摄像头、麦克风等硬件 API 统一限制仅 HTTPS 可用，仅 localhost 单独放行
4. **会话安全隔离**：HTTPS 下 Cookie、Session 可开启 Secure 属性，登录鉴权信息不易被劫持



## 技术栈

- **后端**：Node.js 18 + Express + express-session
- **前端**：原生 HTML / CSS / JavaScript（Web Serial API）
- **容器**：Docker node:18-alpine 轻量镜像

## 部署 & 运行

###Docker Hub 拉取镜像

```bash
# 拉取预构建镜像
docker pull 96mao/pwmcontroller:latest

# 后台运行容器，映射本机 8080 端口
docker run -d -p 8080:8080 --name pwm-web-server 96mao/pwmcontroller:latest
```

**镜像地址**：[https://hub.docker.com/r/96mao/pwmcontroller](https://hub.docker.com/r/96mao/pwmcontroller)

- **访问地址**：`http://localhost:8080`
- **登录账号**：`admin` / `admin123`


# 声明容器内部 Web 服务端口
EXPOSE 8080


## 适用场景

- Arduino / STM32 单片机 PWM 远程上位机
- 直流电机调速、LED 亮度分级调光控制
- 简易工业信号发生器、串口硬件调试平台
- 无本地开发环境，仅依靠 Docker 快速部署硬件控制面板


## 浏览器兼容性

Web Serial API 仅支持以下浏览器：
- Chrome 89+
- Edge 89+
- Opera 75+

> Firefox、Safari 暂不支持 Web Serial API，请使用 Chrome 或 Edge 浏览器访问。

## License

MIT License

可自由二次开发，修改登录鉴权逻辑、PWM 控制交互逻辑；欢迎提交 Issue 反馈问题、PR 贡献优化功能。
