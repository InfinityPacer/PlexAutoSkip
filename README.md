# PlexAutoSkip

自动跳过 Plex 中带标记的内容。

本项目 fork 自 [mdhiggins/PlexAutoSkip](https://github.com/mdhiggins/PlexAutoSkip)，在原项目基础上提供中文说明，用法与配置和原项目一致。英文说明见 [README.en.md](README.en.md)。

PlexAutoSkip 是一个在后台运行的 Python 脚本，监控 Plex 服务器上的本地播放，在播放到片头、片尾、广告等标记时自动替播放器“按下”跳过，或者改为静音、调低音量。脚本自行维护各播放器的实时播放状态，不依赖 Plex API 的轮询更新，以保证跳过的时机准确。多个播放器分别由独立线程处理，并经过多层状态校验，避免不必要的停顿和缓冲。

## 让 Plex 客户端直接跳过

如果你希望由 Plex 客户端直接跳过，而不依赖局域网和「作为播放器广告」，可以了解 [Cirvel](https://cirvel.tidewren.com)。Cirvel 为 Plex 中的本地文件与网盘 STRM 生成片头片尾标记，Plex 客户端用自带的跳过按钮就能跳过。片尾默认只跳片尾曲，之后的彩蛋与预告照常播放，识别不准的个别剧、季或集也可以单独选择不跳过。具体用法见 [片头片尾指南](https://cirvel.tidewren.com/guide/#markers)。

[![Cirvel 管理面板的片头片尾页，按媒体库显示片头片尾标记的生成进度](https://cirvel.tidewren.com/shots/markers.webp)](https://cirvel.tidewren.com)

## 项目状态

Plex 正在把片头跳过等能力做进客户端本身，这是更好的长期方案，详见 [Plex 论坛的讨论](https://forums.plex.tv/t/player-experience/857990)。因此原项目不再添加主要功能，只继续修复小问题，保证对尚不支持原生跳过的播放器的兼容。

## 使用前提

- 只支持局域网内的播放会话，Plex API 不允许对远程会话调整播放进度。
- 播放器需要支持「作为播放器广告」，也就是兼容 Plex Companion。
- 自动检测的片头片尾标记需要 Plex Pass，没有 Plex Pass 时可以用 `custom.json` 自定义标记。

Plex 已在新版 Plex Web 以及 Windows、Mac、Linux 桌面端移除了「作为播放器广告」，这些播放器从下列版本起无法被本脚本控制，详情见 [Troubleshooting](https://github.com/InfinityPacer/PlexAutoSkip/wiki/Troubleshooting#notice)。

| 播放器 | 不再支持的起始版本 |
| --- | --- |
| Plex Web | 4.83.2 |
| Plex for Windows | 1.46.1 |
| Plex for Mac | 1.46.1 |
| Plex for Linux | 1.46.1 |

## 功能

- 跳过 Plex 识别出的任意标记，并可调整跳过的偏移量
  - Plex Pass 自动检测的标记包括片头、片尾、商业广告和广告
  - 也可以按章节跳过
- 只在已观看过的内容上跳过
- 剧集首播、季首播不跳过
- 每次新的观看会话中，第一集不跳过
- 跳过最后一章，通常是片尾
- 绕过「接下来播放」界面
- 自定义规则
  - 在自动检测失败时自行定义标记
  - 按客户端或用户过滤
  - 导出并审核 Plex 的标记，修正错误或补全缺失
  - 批量调整标记时间
  - 用负数偏移量表示相对于内容结尾的位置
- 静音或调低音量代替跳过，需要客户端支持 Plex 的 `setVolume` 调用
- 支持 Docker 部署

## 安装与运行

运行环境需要 Python 3 与 pip，依赖 PlexAPI 和 websocket-client，见 `setup/requirements.txt`。

1. 在 Plex 服务器的「设置 → 网络」中开启「启用本地网络发现（GDM）」。
2. 在 Plex 播放器中开启「作为播放器广告」。
3. 安装 [Python](https://docs.python-guide.org/starting/installation/#installation) 与 [pip](https://packaging.python.org/en/latest/tutorials/installing-packages/)。
4. 克隆本仓库并安装依赖。

   ```bash
   git clone https://github.com/InfinityPacer/PlexAutoSkip.git
   cd PlexAutoSkip
   pip install -r ./setup/requirements.txt
   ```

5. 运行一次 `main.py` 生成配置文件，或者把 `./setup` 下的示例文件复制到 `./config`，去掉文件名中的 `.sample`。
6. 编辑 `./config/config.ini`，填入 Plex 账号或 Plex 服务器信息。
7. 再次运行 `main.py`。

   ```bash
   python main.py
   # 配置文件不在默认位置时
   python main.py -c /path/to/config.ini
   ```

GDM 未开启或不可用时，脚本会改用其他方式发现播放器。

## 配置

### config.ini

主配置文件，包含 Plex 账号或服务器地址、跳过的标记类型、跳过模式、偏移量、连续观看策略和音量设置等。各项说明见 [Wiki 中的 config.ini 配置](https://github.com/InfinityPacer/PlexAutoSkip/wiki/Configuration#configuration-options-for-configini)。

### custom.json

可选的自定义规则，指定哪些电影、剧集、季或单集参与或不参与跳过，也可以按用户和客户端设置允许或屏蔽名单。没有 Plex Pass，或者想跳过 Plex 未识别的片段时，可以在这里为媒体自定义跳过区间。

- 各项说明见 [Wiki 中的 custom.json 配置](https://github.com/InfinityPacer/PlexAutoSkip/wiki/Configuration#configuration-options-for-customjson)
- 社区维护的自定义标记库见 [PlexAutoSkipCustomMarkers](https://github.com/mdhiggins/PlexAutoSkipCustomMarkers)

### custom_audit.py

检查和批量修改自定义规则的辅助脚本，可以整体平移标记时间、从 Plex 导出标记、在 GUID 与 ratingKey 两种写法之间转换等。

```bash
python custom_audit.py --help
```

## Docker

Docker 镜像与部署方式见 [plexautoskip-docker](https://github.com/mdhiggins/plexautoskip-docker)。

## 致谢

- 感谢 [mdhiggins](https://github.com/mdhiggins) 创建并维护 [PlexAutoSkip](https://github.com/mdhiggins/PlexAutoSkip)。
- 原项目致谢 Plex、[PlexAPI](https://github.com/pkkid/python-plexapi)、Skippex、[intro_skipper.py](https://github.com/Casvt/Plex-scripts/blob/main/stream_control/intro_skipper.py) 等项目。

## 许可证

本项目沿用原项目的 MIT 许可证，见 [LICENSE](LICENSE)。
