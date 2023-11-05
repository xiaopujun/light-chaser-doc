> 注: 本文主要针对开发者接入自己的组件到设计器这一场景

## 准备

请按照快速开始章节的步骤，将设计器跑起来。

## 接入自定义组件

###  新建文件夹

在src/comps目录下任意位置新建一个文件夹，例如：src/comps/MyComp（这个文件夹用于存放所有与你组件相关的信息）

###  新建文件

在light chaser中接入组件一共设计到4个必要的文件，分别是：

- definition.ts: 自定义组件信息定义文件，用于定义自定义组件的所有基础信息和配置信息
- controller.ts: 自定义组件控制器，用于管理自定义组件的整个生命周期，后续所有对自定义组件的操作都要依赖于这个文件的实例对象，所以理解他十分重要！
- component.tsx: 自定义的React组件，用于渲染自定义组件的UI
- config.tsx: 自定义组件的右侧配置面板，用于配置自定义组件的属性
- preview.png/jpg: 自定义组件的预览图，用于在设计器中展示自定义组件的样式

如下图所示：

![custom-component.png](https://picdl.sunbangyan.cn/2023/11/05/a5cc56bdd239099c5585a4b82cedbc71.png)

###  component.tsx

component.tsx就是一个普通的react组件，其完整结构和说明如下

```js
import React, {Component} from 'react';

/**
 * 这个style定义将作为自定义组件后续样式更新的基础（请留意这个style在后续的使用）
 */
export interface DemoComponentStyle {
    color ? : string;
}

/**
 * 这里使用class组件，你使用hook组件也没问题，只要后续你建立的controller能控制你组件的创建，更新，销毁即可
 * ps: class组件在这个场景下就尤为方便，只需拿到该组件的this指针即可
 * 除此上面提到的，这个组件和普通的react组件没有任何本质上的区别
 */
class DemoComponent extends Component {
    render() {
        return (
            <div style={{fontSize: 20, color: 'blue', textAlign: "center", width: '100%', height: '100%'}}>
                这是一个测试组件
            </div>
        );
    }
}

export default DemoComponent;
```

### controller.ts

controller.ts是自定义组件的声明周期控制器，它将接手你自定义组件的创建、更新、销毁等操作，其完整结构和说明如下

```js
import AbstractDesignerController from "../../framework/core/AbstractDesignerController";
import DemoComponent, {DemoComponentStyle} from "./DemoComponent";
import ComponentUtil from "../../utils/ComponentUtil";
import {DataConfigType, ThemeItemType} from "../../designer/DesignerType";
import ObjectUtil from "../../utils/ObjectUtil";
import {OperateType, UpdateOptions} from "../../framework/core/AbstractController";
import {ComponentInfoType} from "../common-component/common-types";

/**
 * 这是自定义组件所有配置项的完整定义，其中包括了他的基础信息和样式和数据，当然你还可以在这个基础上继续扩展，只不过这3个是必须的
 * ps：
 * 1. 请注意，style的类型使用的就是component.tsx中定义的DemoComponentStyle，因为这部分配置将直接影响组件的样式
 * 2. 关于info和data的具体定义请自行查看其详细内容
 */
export interface DemoComponentProps {
    info: ComponentInfoType;
    style: DemoComponentStyle;
    data: DataConfigType;
}

/**
 * 自定义组件的控制器，这里继承了AbstractDesignerController，这个类中定义了一些组件的基础方法，比如create、destroy、update等，接入时候按照要求复写完所有方法即可
 * AbstractDesignerController中需要传递两个泛型参数，
 * - 第一个是自定义的React组件，也就是我梦上一个文件定义的DemoComponent
 * - 第二个是自定义组件的完整配置项，也就是上面定义的BaseTextComponentProps
 */
export class DemoController extends AbstractDesignerController<DemoComponent, DemoComponentProps> {

    /**
     * 创建自定义组件实例，这个方法会创建你提供的自定义组件，并将他渲染到指定的容器中
     * 方法入参会给你传递要渲染到的目标容器和初始化的完整配置项，这是前提条件，剩下的就看你自己的实现了
     * @param container 容器
     * @param config 组件的完整初始化配置项
     */
    async create(container: HTMLElement, config: any): Promise<this> {
        this.config = config;
        this.container = container;
        //ComponentUtil.createAndRender方法是设计器提供的一个工具方法，它可以创建并渲染一个React组件，
        //并将组件实例返回给设计器（这点尤为重要，设计器后续要操作该组件的属性都依靠这个实例）
        //PS：如果你可以更好的实现这个过程，请联系我优化它或者提交pr，嘿嘿~~
        this.instance = await ComponentUtil.createAndRender(container, DemoComponent, config);
        return this;
    }

    /**
     * 销毁方法。这个方法里面会把所有的组件变量都清空掉。让gc可以及时回收内存
     * 调用实际你可自行决定，一般是在组件销毁前调用
     */
    destroy(): void {
        this.instance = null;
        this.config = null;
    }

    /**
     * 该方法获取当前组件的完整配置项
     */
    getConfig(): DemoComponentProps | null {
        return this.config;
    }

    /**
     * 本方法更新组件的配置项，并判断是否需要重新渲染组件
     * @param config 配置项片段（片段demo参考下面给出的截图）
     * @param upOp  operateType?: 更新的数据类型，普通样式或是组件数据。具体细分取决你自己组件的要求，也可以不划分，反正参数会传递给你;
     *              reRender: boolean;是否需要触发组件的重新渲染，这个点很关键，有些配置的更新是不需要重新渲染组件的，就可以通过这个参数控制
     */
    update(config: DemoComponentProps, upOp: UpdateOptions | undefined): void {
        //ObjectUtil.merge方法是设计器提供的一个工具方法，它可以将配置片段和原有的配置项合并成一个新的完整配置项，当然这个过程你也可以自定义实现
        this.config = ObjectUtil.merge(this.config, config);
        upOp = upOp || {reRender: true, operateType: OperateType.OPTIONS};
        if (upOp.reRender)
            this.instance?.setState(this.config); //如果是class组件这里就很方便了，我们直到class组件中setState就可以触发组件的重新渲染
    }

    /**
     * 更新组件主题，这个方法需要你自己实现，方法入参会给你传递一个系列的主题颜色，将这些颜色用于你自己的组件样式中接口，不实现就没有主题切换的效果
     * @param newTheme
     */
    updateTheme(newTheme: ThemeItemType): void {

    }
}
```

配置片段与旧配置的合并过程示意图：

![配置项合并示意图](https://picdl.sunbangyan.cn/2023/11/05/0ff2af054d52cb2e71fd18a20f793f99.png)

### config.ts

config.ts是自定义组件右侧配置菜单的react组件实现，他和component一样本质也是一个react组件。并且他对组件类型也是没有要求的

其详细结构和说明如下：

```js
import React from 'react';
import {ConfigType} from "../../designer/right/ConfigType";
import {DemoComponentStyle} from "./DemoComponent";

/**
 * 自定义组件的样式配置组件，组件的props是固定的，他会传递给你一个controller。这个controller就是我们之前定义的controller的实例对象
 */
export const DemoStyleConfig: React.FC<ConfigType> = ({controller}) => {

    const updateStyle = (config: DemoComponentStyle) => {
        //controller是之前定义的controller.ts这个类的实例对象，
        //你可以调用他的update方法直接更新对应组件的样式并重新渲染它
        controller.update({style: config});
    }

    return (
        <div>这是组件的配置项</div>
    )
}


```

### definition.ts

definition.ts是用于定义自定义组件的进本信息、舒适化数据和缩略图等数据

其详细结构和说明如下：

```js
import {BaseInfoType} from "../../designer/DesignerType";
import {getDefaultMenuList} from "../../designer/right/util";
import {AbstractComponentDefinition, MenuToConfigMappingType} from "../../framework/core/AbstractComponentDefinition";
import {ClazzTemplate} from "../common-component/common-types";
import {MenuInfo} from "../../designer/right/MenuType";
import BaseInfo from "../common-component/base-info/BaseInfo";
import {DemoComponentProps, DemoController} from "./DemoController";
import demoImg from './demo-comp.png';
import {DemoStyleConfig} from "./DemoConfig";
import {ClassifyEnum} from "../../designer/left/classify-list/ClassifyType";
import {DatabaseFilled, HighlightFilled, InteractionFilled, MediumCircleFilled, SkinFilled} from "@ant-design/icons";

/**
 *  组件的信息定义类，在这个类中，继承AbstractComponentDefinition并实现好所有方法即可。
 *  在这些方法中你需要提供
 *  1. 组件基本信息（包括组件名称、类型、描述等）
 *  2. 组件缩略图引用。
 *  3. 组件的controller类引用
 *  4. 组件的初始化配置
 *  5. 组件的配置菜单列表
 *  6. 组件的配置菜单与配置内容的映射关系
 *
 *  PS: 这个类是设计器扫描的核心类，必须完整实现AbstractComponentDefinition，否则设计器将无法扫描到你的自定义组件
 */
export default class BaseTextDefinition extends AbstractComponentDefinition<DemoController, DemoComponentProps> {

    /**
     * 返回自定义组件的基础信息
     */
    getBaseInfo(): BaseInfoType {
        return {
            compName: "测试组件", //中文名称
            compKey: "testComponent", //组件标识，设计器内不允许重复，否则会出现识别误差
            type: "基础",
            typeKey: ClassifyEnum.BASE, //分类标识，参考: ClassifyEnum
            desc: "测试组件描述",
        };
    }

    /**
     * 返回组件的预览图，将文件夹中的图片引入并返回即可
     */
    getChartImg(): string | null {
        return demoImg;
    }

    /**
     * 返回组件的controller类引用，引入我们定义的controller然后返回即可
     */
    getComponent(): ClazzTemplate<DemoController> | null {
        return DemoController;
    }

    /**
     * 返回组件的初始化配置，这个配置将会在组件第一次被拖拽到设计器中时使用，其中的类型约束来自于我们在controller中定义的DemoComponentProps
     */
    getInitConfig(): DemoComponentProps {
        return {
            info: {
                id: "",
                name: '测试组件',
                type: 'demoComponent', //与getBaseInfo中的compKey保持一致
                desc: '测试组件描述',
            },
            style: {
                color: '#a7a7a7',
            },
            data: {
                dataSource: 'static',
                staticData: {
                    data: "测试组件的静态数据，组件的数据结构需要你自己定义好，这里的data是any类型，可以接收任何类型的数据"
                },
            },
        };
    }

    /**
     * 返回自定义组件在设计器右侧的操作菜单列表，这个看你自己规划的组件设置项有多少，如何分类。根据你自己的想法来就行
     */
    getMenuList(): Array<MenuInfo> | null {
        return [
            {
                icon: MediumCircleFilled,
                name: '信息',
                key: 'info',
            },
            {
                icon: HighlightFilled,
                name: '样式',
                key: 'style',
            }
        ];
    }

    /**
     * 返回操作菜单列表key与对应配置组件的映射关系。这个方法的返回值是一个对象，对象的key是菜单列表的key，value是配置组件的引用
     * 例如style对应的配置组件就是我们之前定义的DemoConfig这个文件中的DemoStyleConfig组件。其余的你可以自行扩展。
     *
     * 注：一般getMenuList和getMenuToConfigContentMap这两个方法是成对出现的，有多少个菜单项就有对应的配置组件，key要对应上
     */
    getMenuToConfigContentMap(): MenuToConfigMappingType | null {
        return {
            info: BaseInfo,
            style: DemoStyleConfig,
        };
    }
}
```

#### 效果展示

![最终效果展示](https://picdl.sunbangyan.cn/2023/11/05/3a2468da5546949fcdb0abb0d80c497d.png)