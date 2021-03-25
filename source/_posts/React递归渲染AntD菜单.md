---
title: React递归渲染AntD菜单
date: 2021-03-25 10:30:55
tags:
  - React
  - AntD
  - Menu
---
### layouts的写法
```javascript
const renderMenu = (data) => {
  return data.map((item) => {
    if (item.routes && item.routes.length > 0) {
      return (
        <SubMenu
          key={item.name}
          title={item.name}
          icon={item.icon ? createIcon(item.icon) : ''}
        >
          {renderMenu(item.routes)}
        </SubMenu>
      )
    }
    if (item.other) return null
    return (
      <Menu.Item key={item.path} icon={item.icon ? createIcon(item.icon) : ''}>
        {item.name}
      </Menu.Item>
    )
  })
}
const handleMenu = ({ key }) => {
  history.push(key)
}

return (
  ...
  <Menu
    defaultSelectedKeys={[location.pathname]}
    mode="inline"
    inlineCollapsed={collapsed}
    className={style.menu}
    onClick={handleMenu}
  >
    {renderMenu(routes)}
  </Menu>
  ...
)
```

### routes的写法
```javascript

{
  exact: true,
  path: '/foo',
  component: '@/pages/foo',
  name: 'Foo',
  icon: 'foo',
},
{
  exact: true,
  path: '/foobar',
  component: '@/pages/foobar',
  name: 'Foobar',
  icon: 'foobar',
  other: true
},
{
  exact: true,
  name: 'Bar',
  icon: 'bar',
  routes: [
    {
      exact: true,
      path: '/baz',
      component: '@/pages/baz',
      name: 'Baz',
    },
    { exact: true, 
      path: '/qux', 
      component: '@/pages/qux', 
      name: 'Qux' 
    },
  ],
},
```
