# share-data —— 进 git 的共享数据目录

小体量样例、配置文件、标定结果等需要全队共享的内容放这里，**进 git**。

- 大件（LeRobot 数据集、录像、模型权重、运行日志、大 STEP / mesh）一律走 `local-data/`，不进 git；需要别人拿到时在 issue 里给出获取方式。
- 目录名以本目录为唯一事实，不要另建 `shared-data/` / `share_data/` 之类的变体。
