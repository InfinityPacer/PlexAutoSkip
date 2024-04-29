PlexAutoSkip
==============
 **自动跳过Plex中标记的内容**

这是一个后台Python脚本，它会监控你服务器上的本地播放，并会自动“按下”跳过片头按钮或自动跳过其他类似标记的内容。它维护你服务器的实时播放状态（不依赖API更新），以确保精准的跳过时机。采用多线程处理多个播放器，包含多层状态验证以防止不必要的停顿/缓冲。自定义定义让你可以扩展功能，超出Plex自动检测的范围。兼容所有自动标记的标识，包括片头、片尾和广告。

通知
--------------
Plex正向在客户端级别添加原生片头跳过功能转变，这无疑是更好的解决方案。因此，此项目不会添加任何新的主要功能。将继续维护小的错误修复，以确保对不受支持的播放器的兼容，并乐于审查拉取请求。

https://forums.plex.tv/t/player-experience/857990

需求
--------------
- LAN会话（非远程），因为Plex的API不允许通过API进行远程会话的查找调整
- “作为播放器广告” / 兼容Plex Companion的播放器

请访问https://github.com/mdhiggins/PlexAutoSkip/wiki/Troubleshooting#notice 查看基于Plex Web的播放器的变更

功能
--------------
- 跳过任何Plex识别的标记，可调节偏移量
  - 标记（可以跳过任何标记，但Plex在Plex Pass中包括以下内容）
    - 片头
    - 商业广告
    - 广告
    - 片尾
  - 章节
- 只对已观看的内容跳过
- 忽略跳过系列和季节首播
- 忽略在每个新的观看会话中跳过第一集
- 跳过最后一章（片尾）
- 绕过“接下来播放”的屏幕
- 自定义定义
  - 在自动检测失败时定义自己的标记
  - 过滤客户端/用户
  - 导出和审核Plex标记以进行更正/填补空白
  - 批量编辑标记时间
  - 使用负值偏移量相对于内容结束时跳过
- 静音或降低音量而不是跳过
  - 客户端必须支持Plex setVolume API调用
- Docker

需求
--------------
- Python3
- PIP
- PlexPass（自动标记）
- PlexAPI
- Websocket-client

设置
--------------
1. 在Plex服务器 > 网络设置中启用“启用本地网络发现（GDM）”
2. 在Plex播放器上启用“作为播放器广告”
3. 确保已安装[Python](https://docs.python-guide.org/starting/installation/#installation)和[PIP](https://packaging.python.org/en/latest/tutorials/installing-packages/)
4. 克隆存储库
5. 使用`pip install -r ./setup/requirements.txt`安装需求
6. 运行`main.py`一次以生成配置文件，或从`./setup`目录复制样本并重命名，去掉`.sample`后缀
7. 编辑`./config/config.ini`，填入你的Plex账户或Plex服务器设置
8. 运行`main.py`

_当GDM未启用或功能不全时，脚本有回退方法_

config.ini
--------------
- 请参见https://github.com/mdhiggins/PlexAutoSkip/wiki/Configuration#configuration-options-for-configini

custom.json
--------------
可选的自定义参数，用于指定哪些电影、节目、

季节或集数应该包括或阻止。如果你没有Plex Pass或希望跳过更多内容区域，你也可以为媒体定义自定义跳过段落
- 请参见https://github.com/mdhiggins/PlexAutoSkip/wiki/Configuration#configuration-options-for-customjson
- 请参观https://github.com/mdhiggins/PlexAutoSkipCustomMarkers，这是一个虽小但希望不断增长的社区制作的自定义标记库

Docker
--------------
- https://github.com/mdhiggins/plexautoskip-docker

custom_audit.py
--------------
包含检查和批量修改你的自定义定义文件的附加支持脚本。可以对整个集合的标记进行偏移，从Plex导出数据，转换GUID和ratingKey格式等等

```
# 开始
python custom_audit.py --help
```

特别感谢
--------------
- Plex
- PlexAPI
- Skippex
- https://github.com/Casvt/Plex-scripts/blob/main/stream_control/intro_skipper.py
- https://github.com/liamcottle

--------------
翻译 by ChatGPT4