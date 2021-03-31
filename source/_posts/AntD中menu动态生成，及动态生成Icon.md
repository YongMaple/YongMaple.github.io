---
title: AntD中menu动态生成，及动态生成Icon
date: 2021-03-09 14:28:30
categories: 前端
tags:
  - AntD
---

在 routes.ts 里面配置路由，menu 根据 routes 动态生成

<!-- more -->

```js
export default [
  {
    exact: false,
    path: '/',
    component: '@/layouts/index',
    routes: [
      {
        exact: true,
        path: '/overview',
        component: '@/pages/Overview',
        name: '总览',
        icon: 'RadarChartOutlined',
      },
      {
        exact: true,
        path: '/events',
        component: '@/pages/Events',
        name: '事件列表',
        icon: 'BarsOutlined',
      },
      {
        exact: true,
        path: '/feedback',
        component: '@/pages/Feedback',
        name: '情报反馈',
        icon: 'AlertOutlined',
      },
      {
        exact: true,
        path: '/contacts',
        component: '@/pages/Contacts',
        name: '联系人',
        icon: 'DeploymentUnitOutlined',
      },
      {
        exact: true,
        name: '后台管理',
        icon: 'SlidersOutlined',
        routes: [
          {
            exact: true,
            path: '/classifications',
            component: '@/pages/Classifications',
            name: '分类管理',
          },
          {
            exact: true,
            path: '/records',
            component: '@/pages/Records',
            name: '操作日志',
          },
        ],
      },
    ],
  },
]
```

生成 menu

```js
import * as Icon from '@alipay/bigfish/icons';
    ...
// 用React.createElement动态创建元素
const createIcon = (name: string) =>
  React.createElement(Icon && (Icon as any)[name], {
    style: { fontSize: '16px' }
  });
const renderMenu = (data) => {
  return data.map((item) => {
    if (item.routes && item.routes.length > 0) {
      return (
        <SubMenu key={item.name} title={item.name} icon={item.icon ? createIcon(item.icon) : ''}>
          {renderMenu(item.routes)}
        </SubMenu>
      );
    }
    return (
      <Menu.Item key={item.path} icon={item.icon ? createIcon(item.icon) : ''}>
        {item.name}
      </Menu.Item>
    );
  });
};
return (
  ...
    <Menu
      defaultSelectedKeys={[location.pathname]}
      // defaultOpenKeys={['sub1']}
      mode="inline"
      inlineCollapsed={collapsed}
      className={style.menu}
      onClick={handleMenu}
    >
      { renderMenu(routes) }
    </Menu>
  ...
)
```
