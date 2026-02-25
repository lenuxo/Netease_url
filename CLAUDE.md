# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

网易云音乐无损解析工具，提供歌曲搜索、单曲解析、歌单解析、专辑解析和音乐下载功能。支持多种音质（标准、极高、无损、Hi-Res、杜比全景声等）。

## 核心架构

### 模块结构
```
music_api.py        - 网易云音乐API封装（加密、请求处理、音质枚举）
cookie_manager.py   - Cookie管理和验证
music_downloader.py - 音乐文件下载和标签写入
qr_login.py        - 二维码登录功能
main.py            - Flask API服务主入口
templates/index.html - Web前端界面
```

### 关键设计模式
- **APIConstants**: 统一管理API端点和加密密钥
- **CryptoUtils**: AES加密参数处理（网易云eapi接口必需）
- **QualityLevel枚举**: 定义支持的所有音质类型（standard/exhigh/lossless/hires/sky/jyeffect/jymaster/dolby）
- **Cookie管理**: 重要Cookie字段验证（MUSIC_U, MUSIC_A, __csrf等）

## 开发命令

### 启动服务
```bash
# 直接启动（默认端口5023）
python main.py

# 使用环境变量
LEVEL=lossless MODE=api URL=http://127.0.0.1:5023 python main.py

# Docker方式
docker-compose up -d
```

### 依赖安装
```bash
pip install -r requirements.txt
```

### 二维码登录
```bash
python qr_login.py
```

## API端点

| 端点 | 方法 | 功能 |
|------|------|------|
| `/health` | GET | 健康检查 |
| `/song` | GET/POST | 获取歌曲信息（支持type参数：url/name/lyric/json） |
| `/search` | GET/POST | 搜索音乐 |
| `/playlist` | GET/POST | 获取歌单详情 |
| `/album` | GET/POST | 获取专辑详情 |
| `/download` | GET/POST | 下载音乐（支持format参数：file/json） |

## 重要配置

- Cookie文件: `cookie.txt`（黑胶会员必需）
- 下载目录: `downloads/`
- 日志文件: `music_api.log`
- 环境配置: `.env`

## 音质等级说明

- **标准音质**: standard (128kbps)
- **极高音质**: exhigh (320kbps)
- **无损音质**: lossless (FLAC)
- **Hi-Res**: hires (24bit/96kHz)
- **VIP专属**: sky, jyeffect
- **SVIP专属**: jymaster, dolby

## 加密机制

网易云音乐eapi接口需要AES加密：
- 密钥: `e82ckenh8dichen8`（常量定义在APIConstants中）
- 加密流程: MD5哈希 → AES-ECB加密 → Base64编码
- 详见`CryptoUtils.encrypt_params()`

## 向后兼容

API保留旧端点别名：
- `/Song_V1` → `/song`
- `/Search` → `/search`
- `/Playlist` → `/playlist`
- `/Album` → `/album`
- `/Download` → `/download`
