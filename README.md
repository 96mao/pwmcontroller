项目简介
本项目名为 PWM Web Controller，基于 Node.js + Express 开发轻量化硬件控制网页，内置 Session 登录校验；前端采用原生 Web Serial API 直连 USB 串口设备，可视化调节 8 路 PWM 频率与占空比。
项目提供完整 Dockerfile，基于 node:18-alpine 轻量镜像打包，无需本地安装 Node 环境，Windows /macOS/ Linux 全平台一键部署。
核心功能
登录权限管控
基于 express-session 会话机制，登录有效期 1 小时；未登录自动跳转登录页，默认账号 admin / 密码 admin123，支持登出销毁会话，防止非法操控硬件。
8 通道 PWM 可视化调节
支持全局统一频率设置、分组滑块 / 数字输入调整占空比，实时展示串口收发日志，适配电机调速、LED 调光、单片机远程控制、简易信号发生器等场景。
浏览器原生串口通信
使用标准 Web Serial API 操作 USB 串口，无需额外串口客户端；本地 localhost 可直接用 HTTP 访问，局域网 / 公网远程访问必须部署 HTTPS。
轻量化容器部署
容器内仅安装生产依赖，镜像体积小、启动快，一条命令即可运行，环境隔离，跨设备迁移便捷。
无前端框架
纯原生 HTML/CSS/JS，无第三方前端依赖，加载速度快。
端口说明
容器内部服务端口：8080
EXPOSE 8080 仅作文档标识，不会自动暴露端口。
端口映射格式
bash
运行
docker run -d -p 本机端口:8080
示例 -p 8080:8080：本机访问 http://localhost:8080
本机 8080 被占用可改为 -p 9000:8080，访问 http://localhost:9000
区分说明
8080 是 Web 服务网络端口；COM、ttyUSB 等为硬件串口，两者互不冲突。
Web Serial API 必须 HTTPS 的原因
浏览器安全规范规定，串口、USB、摄像头等硬件 API 仅能在安全上下文 Secure Context 下运行：
本地访问 localhost / 127.0.0.1：浏览器豁免，HTTP 可正常使用串口；
局域网 IP、公网域名远程访问：必须配置 HTTPS，否则串口功能直接失效。
安全底层逻辑：
串口属于高危硬件权限，明文 HTTP 易被中间人劫持篡改页面脚本，恶意程序可静默操控电机、单片机等设备；
HTTPS 加密传输，防止页面篡改、流量窃听；
HTTPS 会话 Cookie 可开启 Secure 标识，登录鉴权信息不易被劫持。
