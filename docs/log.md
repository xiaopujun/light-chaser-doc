## [v0.0.10](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.10)

#### Features:

1. 新增大屏预览尺寸自适应
2. 新增组件资源池，图片资源功能，实现图片资源的本地存储与服务器存储
3. 新增项目在本地存储与服务器存储接口
4. 新增图片资源池缓存，封面图片缓存，优化设计器性能
5. 新增项目快照功能，支持项目封面、快照的生成，导出

#### Bug Fixes：

1. 修复由React17升级到18后新的并发特性导致的代码执行顺序发生变化，进而导致拖拽组件、缩放组件的undo、redo操作失效的bug

#### Enhancement：

1. 设计器UI优化调整
2. antd4升级antd5
3. 优化主设计器与蓝图设计器布局框架
4. 优化蓝图链接线段的渲染性能
5. 优化设计器代码结构，尽可能减少any类型
6. 优化设计器事件的加载与卸载时机
7. 优化蓝图设计器拖拽添加过程的事件触发与销毁过程，减少多余事件的触发
8. 细化自定义组件蓝图接入流程

## [v0.0.9](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.9)

发布时间：2023-12-17

#### Features:

1. 新增配置项模板控件
2. 新增基础表格组件

#### Bug Fixes：

1. 修复多层级图层复制后图层出现混乱的bug
2. 修复删除元素后undo、redo操作不连续的bug
3. 修复加载设计器数据后默认主题丢失的bug
4. 修复双击添加组件失效的bug

#### Enhancement：

1. vite4升级到vite5
2. React17升级到React18
3. 优化项目代码结构和命名风格
4. 优化数据操作接口，同时支持操作本地数据和服务器数据
5. 优化预览模式下组件数据加载时机
6. 优化预览模式下组件数据加载异常时，异常提示信息的渲染

## [v0.0.8](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.8)

发布时间：2023-11-24

#### Features

- 新增分组图层设置面板
- 新增图层列表支持右键菜单

#### Enhancement

- 优化蓝图性能,
- 优化图层列表操作体验
- 优化画布组件拖拽体验，支持shift固定方向拖拽
- 优化代码结构与命名 （broken change）

#### Bug Fixes

- 修复撤销与重做相关bug
- 修复快捷键相关bug
- 修复蓝图线条、节点相关bug
- 警告：v0.0.8版本不与之前版本兼容

## [v0.0.7](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.7)

发布时间：2023-11-19

#### Features

- 新增图层分组功能
- 支持拖拽方式添加组件到画布
- 支持monaco编辑器在内网使用

## [v0.0.6](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.6)

发布时间：2023-11-7

#### Features

- 新增蓝图编辑器
- 新增JSON Schema UI解析器

#### Bug Fixes

- 修复标尺刻度在画布反复缩放后出现误差的bug

## [v0.0.5](https://github.com/xiaopujun/light-chaser/releases/tag/v0.0.5)

发布时间：2023-9-12

- 完成主编辑器的基础功能
