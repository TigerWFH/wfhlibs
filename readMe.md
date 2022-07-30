# wfhlibs

## npm install folder

> 该命令本质是在项目 project 中建立一个对依赖项 lib 的 symlink。
>
> 结论 1、如果依赖项 lib 在项目 project 根目录下，会对 lib 的依赖做提升，安装到项目 project 的 node_modules 中
>
> 结论 2、如果依赖项 lib 不是在项目 project 根目录下，会先在 lib 的 node_modules 下安装 lib 的依赖，然后再项目 project 的 node_modules 目录下建立对 lib 的 symlink，即不会做 lib 依赖的提升
>
> 官方文档：https://docs.npmjs.com/cli/v6/commands/npm-install

- `试验Demo`

```js
/*
目的：
    探索清楚npm install folder中，npm到底有没有做扁平化结构，对间接依赖做了提升

试验准备：
    lib1@2.0.0，依赖lib2@3.0.0，依赖lib3@1.0.0
    lib2@3.0.0
    lib3@1.0.0
试验步骤：
    1、删除lib1的node_modules包
    2、将wfhlibs初始化一个npm项目，其中lib1、lib2、lib3位于wfhlibs项目的根目录（工作目录），目录结构见图1
    3、进入到wfhlibs根目录，安装依赖：npm install /Users/xxx/xxx/wfhlibs/lib1，或者npm install(package.json中已经存在依赖，会记录文件路径)
    4、安装后，wfhlibs目录下出现node_modules目录，且lib1依赖的lib2和lib3都被安装到wfhlibs/node_modules/@wfhdemo/目录下，且都是symlink，详情见图2
    结论：证实了上文中的结论1
    --------------------------------------------------
    5、在wfhlibs目录下创建wfhdemo应用，并依赖lib1，见图3
    6、进入到wfhdemo项目，安装依赖：npm install /Users/xxx/xxx/wfhlibs/lib1，或者npm install(package.json中已经存在依赖，会记录文件路径)
    7、安装后，lib1目录下的node_modules/@wfhdemo安装了lib2、lib3且是symlink，wfhdemo目录下的node_modules/@wfhdemo安装了lib1，且是symlink，结构非扁平的，详情见图4
    结论：证实了上文中的结论2
*/
```

![图1](./%E5%9B%BE1.jpg)
![图2](./%E5%9B%BE2.jpg)
![图3](./%E5%9B%BE3.jpg)
![图4](./%E5%9B%BE4.jpg)

## 关于依赖重复问题

> wfhdemo 依赖了lib1@2.0.0，lib2@3.0.0和lib3@1.0.0
>
> lib1@2.0.0依赖了lib2@2.0.0和lib3@1.0.0
>
> 假设 1：如果 wfhdemo 先安装了lib1@2.0.0，再安装lib2@1.0.0和lib3@1.0.0，那么lib2@1.0.0和lib2@1.0.0安装位置会怎样？
>
> 假设 2：如果 wfhdemo 先安装了lib2@1.0.0，后安装了lib1@2.0.0，lib2@1.0.0和lib2@2.0.0安装位置会怎样？

- `试验Demo`

```js
/*
    目的：探索不同安装顺序，项目wfhdemo的直接依赖lib2@1.0.0和间接依赖lib2@2.0.0的安装位置结构

    试验准备：
        npm包@wfhdemo/lib1@2.0.0，依赖了npm包@wfhdemo/lib2@2.0.0和@wfhdemo/lib3@1.0.0

        项目wfhdemo依赖了npm包@wfhdemo/lib1@2.0.0和@wfhdemo/lib2@3.0.0
    试验步骤：
        1、进入到wfhdemo目录，安装@wfhdemo/lib1@2.0.0，观察node_modeuls的目录结构，见图5
        2、进入到wfhdemo目录，安装@wfhdemo/lib2@3.0.0，观察node_modules的目录结构，见图6
        -------------------------------------------------
        3、进入到wfhdemo目录，安装@wfhdemo/lib2@3.0.0，观察node_modules的目录结构，见图7
        4、进入到wfhdemo目录，安装@wfhdemo/lib1@2.0.0，观察node_modeuls的目录结构，见图8

        结论：项目的直接依赖会被优先安装到项目的node_modules目录下，不受安装顺序影响
*/
```

![图5](./%E5%9B%BE5.jpg)
![图6](./%E5%9B%BE6.jpg)
![图7](./%E5%9B%BE7.jpg)
![图8](./%E5%9B%BE8.jpg)
