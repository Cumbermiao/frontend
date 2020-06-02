# git 规范

## commit 规范
> 参考 [git commit 规范指南](https://www.jianshu.com/p/201bd81e7dc9?utm_source=oschina-app) , [git commit emoji](https://github.com/liuchengxu/git-commit-emoji-cn)

### commit 格式
> icon + type + subject(必填) + body
- icon 图标
- type 本次 commit 类型，主要类型有:
  - feat：新功能（feature）
  - fix：修补bug
  - docs：文档（documentation）
  - style： 格式（不影响代码运行的变动）
  - refactor：重构（即不是新增功能，也不是修改bug的代码变动）
  - test：增加测试
  - chore：构建过程或辅助工具的变动
- subject 本次 commit 目的的简短描述，不超过50个字符。
- body 本次 commit 的详细描述，可以分成多行。

==以上 icon 和 type 二选一 ， subject 是 commit 必填字段.==

#### 例子
`:bug: fix: fix bugs`

#### 常用 icon 
emoji                                   | emoji 代码                   | commit 说明
:--------                               | :--------                    | :--------
:tada: (庆祝)                           | `:tada:`                     | 初次提交
:new: (全新)                            | `:new:`                      | 引入新功能
:bookmark: (书签)                       | `:bookmark:`                 | 发行/版本标签
:bug: (bug)                             | `:bug:`                      | 修复 bug
:ambulance: (急救车)                    | `:ambulance:`                | 重要补丁
:lipstick: (口红)                       | `:lipstick:`                 | 更新 UI 和样式文件
:clapper: (场记板)                      | `:clapper:`                  | 更新演示/示例
:rotating_light: (警车灯)               | `:rotating_light:`           | 移除 linter 警告
:wrench: (扳手)                         | `:wrench:`                   | 修改配置文件
:heavy_plus_sign: (加号)                | `:heavy_plus_sign:`          | 增加一个依赖
:heavy_minus_sign: (减号)               | `:heavy_minus_sign:`         | 减少一个依赖
:arrow_up: (上升箭头)                   | `:arrow_up:`                 | 升级依赖
:arrow_down: (下降箭头)                 | `:arrow_down:`               | 降级依赖
:zap: (闪电)                            | `:zap:`                      | 提升性能
:white_check_mark: (白色复选框)         | `:white_check_mark:`         | 增加测试
:memo: (备忘录)                         | `:memo:`                     | 撰写文档
:hammer: (锤子)                         | `:hammer:`                   | 重大重构
:art: (调色板)                          | `:art:`                      | 改进代码结构/代码格式
:fire: (火焰)                           | `:fire:`                     | 移除代码或文件
:construction: (施工)                   | `:construction:`             | 工作进行中
:lock: (锁)                             | `:lock:`                     | 修复安全问题
:apple: (苹果)                          | `:apple:`                    | 修复 macOS 下的问题
:penguin: (企鹅)                        | `:penguin:`                  | 修复 Linux 下的问题
:checkered_flag: (旗帜)                 | `:checkered_flag:`           | 修复 Windows 下的问题

## 分支管理
参考[文章](https://blog.csdn.net/bocai_xiaodaidai/article/details/103814409)
参考[流程图](https://www.processon.com/diagraming/5ed5a8de7d9c08162f2ad854)

- master : master分支上面每次提交都为一个版本
- dev : 正常开发分支
- hotfix : 紧急修复分支， 基于 master 分支
- feature : 新特性分支， 基于 dev 分支
- release : 发布分支， 基于 dev 分支

### 规范

1. 开发新需求时， 在 dev 分支上开发， 需求开发结束进行测试时， 不需要打 tag。
2. 当测试通过，即将部署时， 新建 release 分支， 部署的项目包基于 release 分支打包， 且每次打包需要打 tag。
3. 当部署之后发现有需要修改优化的内容， 或者验收不通过时， 基于 release 分支开发。 基于 release 分支开发时， dev 和 master 分支上面不应该再提交内容。
4. 当验收通过时， 将 release 分支合并至 dev 和 master 分支上， master 分支合并需要打 tag。
5. 当发现线上有紧急 bug 需要修复时， 新建 hotfix 分支， 基于 hotfix 开发。
6. 当紧急 bug 修复并且测试通过之后， 需要将 hotfix 分支合并至 dev 分支上， 并且合并至 master 分支上， master 分支需要打 tag。
7. 当开发新特性功能时， 应当基于 dev 分支新建 feature 分支， 在 feature 分支上开发。
8. feature 分支上的新特性开发完毕之后， 合并至 dev 分支， dev 分支需求开发结束进行测试。
9. 当新项目需要做定制化需求，且该需求与当前标准产品(master)冲突即无法在当前 dev 分支开发时， 新建 bg-master 分支， 并且在 bg-master 分支上打 tag ，标记基于的 master 版本。
10. bg-master 上的每次提交都是一个版本， 开发时基于 bg-master 新建 bg-dev 分支。
11. bg-dev 上定制化需求开发且测试通过时， 新建 bg-release 分支， 基于该分支进行打包部署测试。
12. 当现场部署验收不通过时， 基于 bg-release 修改， 验收通过后合并至 bg-dev 和 bg-master， 且在 bg-master 上打 tag。